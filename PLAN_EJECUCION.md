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
