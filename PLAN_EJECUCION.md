# Plan de Ejecución — Multi-SD Game Stick

---

## Tamaño de microSD recomendado

| SD | Uso | Tamaño mínimo | Recomendado | Razón |
|---|---|---|---|---|
| SD-2 (Kodi/IPTV/Media) | Sistema + IPTV + películas + música local | 64GB | **128GB–256GB** | Sistema ~3GB, películas 1080p ~5GB c/u, discografía MP3 ~30–40GB, FLAC ~150GB+ |
| SD-3 (Moonlight) | Sistema + Moonlight binario | 2GB | **4GB** | Solo necesita Linux mínimo + un binario. No almacena juegos, todo se transmite |

**El stick soporta microSD hasta 256GB confirmado** (interfaz SDXC, teórico hasta 2TB). En archive.org hay backups de 128GB funcionando sin problema.

- SD-2: clase 10 / U1 mínimo. U3 o A1 recomendado si vas a reproducir muchos archivos simultáneamente.
- SD-3: la más barata clase 10 que encuentres.

---

## Lista de compras

| # | Componente | Modelo sugerido | Precio aprox. MXN |
|---|---|---|---|
| 1 | Hub USB-A con alimentación | AuviPal 3-Port o similar con puerto de poder | $150–200 |
| 2 | Dongle Wi-Fi USB | MediaTek MT7601U | $50–80 |
| 3 | MicroSD 128GB (para Kodi + media) | SanDisk Ultra / Kingston clase 10 U1 | $180–250 |
| 4 | MicroSD 4GB (para Moonlight) | Cualquier clase 10 | $40–60 |
| 5 | Cargador USB 5V extra (para el hub) | Cualquier cargador de celular | $0 (ya tienes uno) |
| | **Total** | | **~$420–590 MXN** |

---

## SD-2: Kodi + IPTV + Música

### Paso 1 — Descargar imagen base

Opción A (preferida): GStickOS
```
https://github.com/lucamot/GStickOS/releases
→ Descargar GStickOS-1.0.1.zip
```

Opción B (alternativa): SpectralElec 3.0
```
Buscar en archive.org: "SpectralElec RK3032"
→ Imagen .img para Game Stick 4K Lite
```

### Paso 2 — Flashear la microSD

```bash
# Windows: usar Balena Etcher
# 1. Descomprimir el .zip → obtener .img
# 2. Abrir Etcher → seleccionar .img → seleccionar microSD 8GB → Flash
```

### Paso 3 — Primer arranque

1. Insertar microSD en el stick
2. Conectar stick al TV por HDMI
3. Alimentar stick por USB
4. Debería arrancar en EmulationStation / RetroArch

### Paso 4 — Verificar que Kodi existe

Navegar el menú → buscar "Kodi" o "Media Center" en las opciones.

**Si Kodi está**: ir al Paso 5.

**Si Kodi NO está (Plan B)**:
```bash
# Conectar hub + dongle Wi-Fi primero (Paso 4b)
# Luego por SSH:
ssh root@<IP_DEL_STICK>
# Password por defecto en GStickOS: revisar documentación del release

# Instalar Kodi si hay paquete disponible:
opkg update && opkg install kodi
# o si es Buildroot:
# Habrá que recompilar la imagen con Kodi incluido (ver Bloque 6 de INVESTIGACION.md)
```

**Si Kodi definitivamente no se puede instalar (Plan C)**:
```bash
# Usar mpv directamente con listas IPTV:
ssh root@<IP>
mpv --playlist=iptv_mexico.m3u
# Control remoto via celular con app "mpv remote" o scripts con evdev
```

### Paso 5 — Conectar Wi-Fi

1. Conectar hub USB-A al stick
2. En el hub: receptor de mandos + dongle MT7601U
3. Alimentar el hub con cargador extra si es necesario
4. Configurar Wi-Fi:

```bash
# Si tiene interfaz gráfica de Wi-Fi: usarla directamente

# Si no, por SSH:
ssh root@<IP>
wpa_passphrase "TU_RED_WIFI" "TU_PASSWORD" >> /etc/wpa_supplicant.conf
wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant.conf
udhcpc -i wlan0
```

### Paso 6 — Configurar IPTV en Kodi

1. Abrir Kodi
2. Ir a **Add-ons → Install from repository → PVR clients → PVR IPTV Simple Client → Install**
3. Configurar el addon:
   - **M3U Play List URL**: `https://iptv-org.github.io/iptv/countries/mx.m3u` (México)
   - O todas: `https://iptv-org.github.io/iptv/index.m3u`
4. Reiniciar Kodi → aparecerán canales en **TV → Channels**

### Paso 7 — Copiar tu biblioteca de películas y música

La microSD tiene dos particiones. La segunda (datos) es donde va tu contenido:

```bash
# Desde tu PC, montar la SD (ya flasheada y funcionando) y copiar:

# Opción A — Desde Windows/Linux directamente a la SD:
# La partición de datos se monta como ext4. En Windows usar Ext2Fsd o WSL para acceder.
# Crear carpetas:
mkdir -p /media/sdcard/storage/peliculas
mkdir -p /media/sdcard/storage/musica

# Copiar tus archivos:
cp -r /ruta/a/tus/peliculas/* /media/sdcard/storage/peliculas/
cp -r /ruta/a/tu/musica/* /media/sdcard/storage/musica/

# Opción B — Por red (stick encendido con Wi-Fi):
ssh root@<IP>
mkdir -p /storage/peliculas /storage/musica

# Desde tu PC:
scp -r ./peliculas/* root@<IP>:/storage/peliculas/
scp -r ./musica/* root@<IP>:/storage/musica/
# (Para archivos grandes, rsync es mejor):
rsync -avP ./peliculas/ root@<IP>:/storage/peliculas/
rsync -avP ./musica/ root@<IP>:/storage/musica/
```

### Paso 8 — Configurar biblioteca en Kodi

1. Abrir Kodi → **Settings → Media → Library**
2. **Videos → Files → Add videos** → Browse → seleccionar `/storage/peliculas/`
   - "This directory contains": **Movies**
   - Scraper: **The Movie Database** (descarga carátulas y sinopsis automáticamente)
3. **Music → Files → Add music** → Browse → seleccionar `/storage/musica/`
   - Kodi escaneará automáticamente tags ID3/metadata
4. **Update Library** → esperar a que indexe todo

Resultado: biblioteca con carátulas, sinopsis, búsqueda por género/año/artista, todo navegable con gamepad.

### Paso 9 — Verificar navegación con gamepad

El gamepad del stick se expone como joystick estándar Linux. Kodi lo reconoce automáticamente:
- D-pad: navegar menús
- A/B: seleccionar/atrás
- Start: menú contextual

**Si el gamepad no responde en Kodi**: mapear manualmente en `~/.kodi/userdata/keymaps/joystick.xml`

---

### Paso 10 — VPN WireGuard para canales colombianos (Caracol, RCN completo)

Algunos canales colombianos (Caracol TV, Canal 1, Telepacífico) están geo-bloqueados y solo transmiten a IPs de Colombia. La solución: un túnel VPN WireGuard entre tu stick en México y un servidor tuyo en Colombia.

**Arquitectura:**

```
[Stick en México] ──WireGuard tunnel──→ [Tu servidor en Colombia]
       │                                        │
       └── IP colombiana (10.0.0.2) ←───────────┘
                    │
                    ↓
        Canales geo-bloqueados ven IP colombiana ✅
```

**WireGuard** porque: consume ~2MB RAM, zero-config después de la primera vez, reconecta automáticamente si pierde señal, latencia mínima (~1ms overhead).

#### 10.1 — Configurar el SERVIDOR (tu VPS/instancia en Colombia)

```bash
# En el servidor (Ubuntu/Debian):
apt install wireguard

# Generar llaves del servidor:
wg genkey | tee /etc/wireguard/server_private.key | wg pubkey > /etc/wireguard/server_public.key
chmod 600 /etc/wireguard/server_private.key

# Crear config del servidor:
cat > /etc/wireguard/wg0.conf << 'EOF'
[Interface]
Address = 10.0.0.1/24
ListenPort = 51820
PrivateKey = <CONTENIDO_DE_server_private.key>

# Habilitar NAT para que el stick salga con la IP del servidor:
PostUp = iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE; sysctl -w net.ipv4.ip_forward=1
PostDown = iptables -t nat -D POSTROUTING -o eth0 -j MASQUERADE

[Peer]
# El game stick
PublicKey = <PUBLIC_KEY_DEL_STICK>
AllowedIPs = 10.0.0.2/32
EOF

# Levantar:
systemctl enable --now wg-quick@wg0
```

#### 10.2 — Configurar el CLIENTE (el game stick)

```bash
# En el stick por SSH:
ssh root@<IP_STICK>

# Generar llaves del cliente:
wg genkey | tee /etc/wireguard/client_private.key | wg pubkey > /etc/wireguard/client_public.key

# Crear config del cliente:
cat > /etc/wireguard/wg0.conf << 'EOF'
[Interface]
Address = 10.0.0.2/24
PrivateKey = <CONTENIDO_DE_client_private.key>
# Solo rutear tráfico de streaming por la VPN (no todo):
# Si quieres TODO por VPN: usar 0.0.0.0/0 en AllowedIPs del Peer
DNS = 8.8.8.8

[Peer]
PublicKey = <PUBLIC_KEY_DEL_SERVIDOR>
Endpoint = <IP_PUBLICA_SERVIDOR_COLOMBIA>:51820
AllowedIPs = 0.0.0.0/0
PersistentKeepalive = 25
EOF

# Levantar:
wg-quick up wg0

# Verificar que tienes IP colombiana:
curl ifconfig.me
# Debe mostrar la IP de tu servidor en Colombia
```

#### 10.3 — Autostart de WireGuard en el stick

```bash
# Para que se conecte automáticamente al encender:
cat > /etc/init.d/S50wireguard << 'EOF'
#!/bin/sh
case "$1" in
  start) wg-quick up wg0 ;;
  stop) wg-quick down wg0 ;;
esac
EOF
chmod +x /etc/init.d/S50wireguard
```

Con esto: enciendes el stick → se conecta al Wi-Fi → levanta WireGuard automáticamente → todo el tráfico sale por Colombia → canales geo-bloqueados funcionan.

**El servidor solo necesita estar encendido.** No requiere acción manual de tu parte. El stick se conecta como cliente automáticamente.

#### 10.4 — Listas IPTV con canales colombianos completos

Con la VPN activa, puedes usar listas M3U de terceros que incluyen Caracol, RCN completo, etc. Fuentes para buscar:

```bash
# En Kodi, agregar una segunda lista M3U:
# Settings → PVR IPTV Simple Client → M3U URL:
# Opción: alojar tu propia lista en el servidor de Colombia:
# https://TU_SERVIDOR_CO/iptv/colombia.m3u

# O combinar la lista pública + tu lista privada:
# En el servidor, crear un script que merge ambas:
cat > /var/www/html/iptv/colombia.m3u << 'EOF'
#EXTM3U
#EXTINF:-1 group-title="Colombia",Caracol TV
http://STREAM_URL_AQUI
#EXTINF:-1 group-title="Colombia",RCN Television
http://STREAM_URL_AQUI
#EXTINF:-1 group-title="Colombia",Canal 1
http://STREAM_URL_AQUI
EOF
# Las URLs de streams se obtienen de foros/comunidades IPTV
# Se actualizan periódicamente porque cambian
```

**Nota**: las listas de terceros se caen/cambian frecuentemente. Puedes automatizar la actualización con un cron en el servidor que busque y valide los streams.

#### 10.5 — Requisitos del servidor en Colombia

| Recurso | Mínimo |
|---|---|
| RAM | 512MB (WireGuard usa ~2MB) |
| CPU | 1 vCPU |
| Ancho de banda | ~5 Mbps por stream 720p activo |
| SO | Ubuntu 22.04+ / Debian 12+ |
| Puerto abierto | UDP 51820 |

Un VPS básico en Colombia (DigitalOcean, Vultr, o AWS Lightsail región no disponible — buscar proveedores locales colombianos como Ufinet, ColombiaHosting, o usar Oracle Cloud free tier si tiene región cercana).

---

## SD-3: Moonlight (Game Streaming desde PC)

### Requisitos previos en tu PC

```bash
# Windows: descargar Sunshine de https://github.com/LizardByte/Sunshine/releases
# Instalar → abrir → configurar en https://localhost:47990
# Tu RTX A6000 será detectada automáticamente (NVENC)
```

### Paso 1 — Preparar la SD base

Usar GStickOS como base (ya tiene kernel Linux funcional + drivers + SSH):
```bash
# Misma imagen que SD-2, flashear en la microSD de 4GB con Etcher
```

### Paso 2 — Conectar el stick a la red

Mismo proceso que SD-2 Paso 5 (hub + dongle + Wi-Fi config).

### Paso 3 — Instalar Moonlight Embedded

**Opción A — Binario precompilado (si existe para RK3032):**
```bash
ssh root@<IP_DEL_STICK>
# Buscar en repos de la distro:
opkg update && opkg install moonlight-embedded
# o
apt-get install moonlight-embedded
```

**Opción B — Cross-compilar desde tu PC (si no hay binario):**
```bash
# En tu PC (Linux o WSL):
sudo apt install gcc-arm-linux-gnueabihf cmake

git clone --recursive https://github.com/moonlight-stream/moonlight-embedded.git
cd moonlight-embedded

# Necesitas las libs del stick (extraer del rootfs de GStickOS):
# - librockchip_mpp.so + rk_mpi.h (Rockchip VPU)
# - libdrm.so + drm.h
# - libevdev, libudev, libopus

mkdir build && cd build
cmake .. \
  -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc \
  -DCMAKE_FIND_ROOT_PATH=/path/to/stick-rootfs \
  -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY \
  -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY
make -j$(nproc)

# Copiar al stick:
scp moonlight root@<IP>:/usr/local/bin/
```

**Opción C — Compilar directamente en el stick (lento pero simple):**
```bash
ssh root@<IP>
# Si la distro tiene compilador:
apt-get install build-essential cmake libevdev-dev libudev-dev libopus-dev
git clone --recursive https://github.com/moonlight-stream/moonlight-embedded.git
cd moonlight-embedded && mkdir build && cd build
cmake .. && make -j2
make install
```

### Paso 4 — Emparejar con tu PC

```bash
# En el stick:
moonlight pair <IP_DE_TU_PC>
# Aparecerá un PIN en pantalla → ingresarlo en Sunshine (https://localhost:47990)
```

### Paso 5 — Probar streaming

```bash
moonlight stream <IP_DE_TU_PC> -app Desktop -width 1920 -height 1080 -fps 60 -codec h264
# Si funciona: verás tu escritorio en el TV
# Si falla: probar con resolución menor:
moonlight stream <IP_DE_TU_PC> -app Desktop -width 1280 -height 720 -fps 30 -codec h264
```

### Paso 6 — Configurar autostart

```bash
# Que Moonlight arranque automáticamente al encender el stick:
cat > /etc/init.d/S99moonlight << 'EOF'
#!/bin/sh
case "$1" in
  start)
    sleep 5
    moonlight stream <IP_PC> -app Desktop -width 1920 -height 1080 -fps 60 -codec h264 &
    ;;
  stop)
    killall moonlight
    ;;
esac
EOF
chmod +x /etc/init.d/S99moonlight
```

### Paso 7 — Mapear gamepad

Moonlight usa `libevdev` para leer el gamepad. Debería funcionar automáticamente si el receptor 2.4GHz se expone como joystick estándar.

```bash
# Verificar que el gamepad se detecta:
cat /proc/bus/input/devices | grep -A5 "Game"
# o
evtest /dev/input/event0
```

**Si no se detecta**: puede requerir un mapping manual en `~/.config/moonlight/moonlight.conf`

---

## Orden de ejecución recomendado

```
1. Comprar hardware (hub + dongle + microSDs)
         ↓
2. Mientras llega: instalar Sunshine en tu PC y verificar que funciona
         ↓
3. Al llegar: SD-2 primero (Kodi) — es la prueba más fácil de que el hub + Wi-Fi funcionan
         ↓
4. Si SD-2 funciona: SD-3 (Moonlight) — usa la misma base de red ya probada
```

---

## Troubleshooting

| Problema | Causa probable | Solución |
|---|---|---|
| Stick no arranca con la SD | Imagen incorrecta para el modelo | Probar otra imagen (GStickOS vs SpectralElec) |
| Wi-Fi no detectado | Driver MT7601U no incluido | Verificar `lsusb` y `dmesg` por SSH |
| Hub no alimenta todo | Corriente insuficiente | Conectar cargador al puerto de alimentación del hub |
| Kodi lagea mucho | 256MB RAM al límite | Reducir lista IPTV, deshabilitar thumbnails, usar skin Estuary Lite |
| Moonlight: solo pantalla negra | VPU no activado | Verificar que `librockchip_mpp.so` existe en `/usr/lib/` |
| Moonlight: lag inaceptable | Wi-Fi 2.4GHz saturado | Cambiar a canal Wi-Fi menos congestionado, o usar adaptador Ethernet USB |
| Gamepad no responde en Kodi/Moonlight | evdev no mapea correctamente | `evtest` para verificar → mapear manualmente |
