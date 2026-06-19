# VPN en el Game Stick (WireGuard)

GStickOS está basado en EmuELEC/CoreELEC, que incluye **WireGuard en el kernel**.
No se necesita instalar nada, solo copiar archivos de configuración.

## Objetivo

Tener 2 perfiles VPN listos para alternar:
- **Suecia** → necesario para que funcionen los streams de Magis TV / Xuper TV
- **Colombia** → para acceder a contenido geo-restringido de Colombia

---

## Paso 1: Generar las configs WireGuard (desde PC o Mac)

### Opción A: Cloudflare WARP (RECOMENDADA — gratis, sin límite, rápida)

WARP no te deja elegir país directamente, se conecta al datacenter más cercano.
Para forzar país, se usa un endpoint específico de Cloudflare en ese país.

1. Ir a https://warp-conf-gen.vercel.app/ → genera un `.conf` base
2. O usar la CLI `wgcf` (https://github.com/ViRb3/wgcf):
   ```bash
   # Instalar wgcf
   curl -Lo wgcf https://github.com/ViRb3/wgcf/releases/latest/download/wgcf_linux_amd64
   chmod +x wgcf

   # Registrar cuenta WARP
   ./wgcf register
   
   # Generar config WireGuard
   ./wgcf generate
   # → Produce wgcf-profile.conf
   ```
3. Editar el `Endpoint` del `.conf` para apuntar al país deseado:
   - Suecia: `engage.cloudflareclient.com:2408` (desde un resolver sueco) 
   - Colombia: `engage.cloudflareclient.com:2408`
   
   **Nota:** WARP usa el datacenter más cercano al endpoint. Para forzar país,
   se necesita que el tráfico salga desde ese país. Para IPTV puede que NO sea
   suficiente — usar Opción B si se necesita IP estrictamente de ese país.

### Opción B: VPNBook (gratis, configs descargables, expiran cada 7 días)

1. Ir a https://www.vpnbook.com/freevpn/wireguard-vpn
2. Seleccionar servidor → Sweden / Colombia (si disponible)
3. Generar → descargar el `.conf`
4. Renovar cada 7 días (las configs expiran)

### Opción C: Windscribe "Build-a-Plan" ($1/mes por país)

Si necesitas algo estable y confiable:
1. Crear cuenta en https://windscribe.com
2. Upgrade → Build-a-Plan → seleccionar Sweden ($1) + Colombia ($1) = **$2/mes**
3. Ir a https://windscribe.com/getconfig/wireguard
4. Seleccionar Sweden → descargar `sweden.conf`
5. Seleccionar Colombia → descargar `colombia.conf`

---

## Paso 2: Copiar las configs al Game Stick por SSH

### Credenciales SSH de EmuELEC/GStickOS:
```
Host: (IP del stick en tu red Wi-Fi)
User: root
Pass: emuelec    (o coreelec, o linux)
```

### Encontrar la IP del stick:
- Desde Kodi: Settings → System Information → Network
- O escanear la red: `nmap -sn 192.168.1.0/24`

### Copiar los archivos:
```bash
# Desde tu Mac/PC:
scp sweden.conf root@<IP_STICK>:/storage/.config/wireguard/
scp colombia.conf root@<IP_STICK>:/storage/.config/wireguard/
```

Si el directorio no existe:
```bash
ssh root@<IP_STICK> "mkdir -p /storage/.config/wireguard"
```

---

## Paso 3: Activar/desactivar VPN desde SSH

### Conectar a Suecia:
```bash
ssh root@<IP_STICK>
wg-quick up /storage/.config/wireguard/sweden.conf
```

### Conectar a Colombia:
```bash
wg-quick up /storage/.config/wireguard/colombia.conf
```

### Desconectar:
```bash
wg-quick down /storage/.config/wireguard/sweden.conf
```

### Verificar que funciona:
```bash
curl -s ifconfig.me
# Debería mostrar una IP del país seleccionado
```

---

## Paso 4: Automatizar al boot (opcional)

Crear un script que active la VPN automáticamente:

```bash
cat > /storage/.config/autostart.sh << 'EOF'
#!/bin/bash
sleep 15  # esperar a que Wi-Fi conecte
wg-quick up /storage/.config/wireguard/sweden.conf
EOF
chmod +x /storage/.config/autostart.sh
```

Para cambiar de país: editar el script y cambiar `sweden.conf` por `colombia.conf`.

---

## Resumen de opciones

| Servicio | Suecia | Colombia | Costo | Estabilidad | Nota |
|---|---|---|---|---|---|
| Cloudflare WARP | ⚠️ Indirecto | ⚠️ Indirecto | Gratis | Alta | No garantiza IP del país exacto |
| VPNBook | ✅ | Verificar | Gratis | Media | Config expira cada 7 días |
| Windscribe Build | ✅ | ✅ | $2/mes | Alta | Genera configs WireGuard oficiales |
| Urban VPN (Android) | ✅ | ✅ | Gratis | Baja | Solo para hotspot desde celular |

---

## Notas

- El stick tiene solo 256MB RAM. WireGuard usa ~2MB, no impacta.
- Si `wg-quick` no existe en GStickOS, usar `ip link add wg0 type wireguard` + `wg setconf wg0 /path/to/conf` manualmente.
- Solo puede haber UNA VPN activa a la vez. Hacer `down` antes de `up` de otra.
