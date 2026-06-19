# Flasheo SD-2 (Kodi + IPTV) — Instrucciones para macOS

## Imagen a usar

**GStickOS 1.0.1** — firmware custom para Game Stick 4K Lite (RK3032)

- Descarga: https://github.com/lucamot/GStickOS/releases/download/v1.0.1/GStickOS-1.0.1.zip
- Tamaño: 393 MB (zip)
- SHA256: `6f779c746df78694f9df69b57159c6dbefe1774f67cd1242ae29629e3c16a7f6`
- Nota: el repo fue archivado el 7 abril 2026 (read-only), pero los releases siguen disponibles.

## Requisitos

- microSD de 128GB (clase 10 / U1 mínimo)
- Lector de microSD
- Balena Etcher (o `dd` desde terminal)

## Opción A: Balena Etcher (recomendada, GUI)

1. Descargar Balena Etcher: https://etcher.balena.io/
2. Descomprimir `GStickOS-1.0.1.zip` → obtienes un `.img`
3. Abrir Etcher → **Flash from file** → seleccionar el `.img`
4. Seleccionar la microSD de 128GB
5. Click **Flash!**
6. Esperar a que termine (~5–10 min)
7. Expulsar la SD de forma segura

## Opción B: Terminal con `dd` (avanzado)

```bash
# 1. Descomprimir
unzip GStickOS-1.0.1.zip

# 2. Identificar la microSD
diskutil list
# Buscar el disco que corresponda a la SD (ej: /dev/disk4)

# 3. Desmontar la SD (NO expulsar)
diskutil unmountDisk /dev/disk4

# 4. Flashear (usar rdiskN para velocidad, NO diskN)
sudo dd if=GStickOS-1.0.1.img of=/dev/rdisk4 bs=4m status=progress

# 5. Expulsar
diskutil eject /dev/disk4
```

⚠️ **CUIDADO**: verifica bien el número de disco. `dd` sobrescribe sin preguntar.

## Después del flasheo

1. Insertar la microSD en el Game Stick
2. Conectar el hub USB (con receptor de mandos + dongle Wi-Fi ya conectados)
3. Conectar HDMI al TV
4. Alimentar el stick por USB
5. Debería arrancar en EmulationStation / RetroArch

## Siguiente paso después del boot

Ver [PLAN_EJECUCION.md](./PLAN_EJECUCION.md) → Paso 4 en adelante (verificar Kodi, configurar Wi-Fi, IPTV).
