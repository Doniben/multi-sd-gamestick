# Multi-SD Game Stick Project 🎮📺🖥️

Un Game Stick retro chino (RK3032) convertido en un dispositivo multifuncional mediante múltiples tarjetas microSD intercambiables, cada una con un sistema y propósito diferente.

## Concepto

El mismo hardware, múltiples personalidades. Cambias la SD y cambias completamente la función del dispositivo.

```
[Game Stick RK3032]
       |
       ├── SD-1: 🎮 Juegos Retro (EmuELEC / SpectralElec)  ← ya existe
       ├── SD-2: 📺 TV & Películas (Kodi + IPTV)
       ├── SD-3: 🖥️ Game Streaming desde PC (Moonlight)
       ├── SD-4: 🎵 Reproductor de música (MPD)
       ├── SD-5: 🎤 Karaoke (UltraStar ARM)
       └── SD-6: 🌐 Escritorio minimalista (Chromium kiosk)
```

## Hardware Base

- **Chip**: Rockchip RK3032 (ARM Cortex-A53)
- **RAM**: 256MB–512MB
- **Almacenamiento**: MicroSD (todo el sistema vive aquí)
- **Video**: HDMI
- **Alimentación**: USB
- **Controles**: Receptor 2.4GHz inalámbrico vía USB

## Accesorios necesarios para conectividad

Para no perder los controles inalámbricos al agregar red:

| Componente | Modelo recomendado | Precio aprox. MXN |
|---|---|---|
| OTG Hub con alimentación | AuviPal 3-Port Micro USB OTG | $150–200 |
| Dongle Wi-Fi USB | MediaTek MT7601U | $50–80 |
| **Total** | | **~$200–280** |

Conexión:
```
[Game Stick] → [OTG Hub] → [Receptor mandos]
                         → [Dongle Wi-Fi MT7601U]
                         → [Puerto libre]
                         → [Alimentación extra micro USB]
```

---

## Las SDs

Ver [PLAN_DESARROLLO.md](./PLAN_DESARROLLO.md) para el plan detallado de cada imagen.

## Estado del proyecto

- [x] Concepto definido
- [x] Hardware validado: hub USB + dongle Wi-Fi + receptor mandos funcionan juntos
- [ ] SD-2: Kodi + IPTV → **EN PROGRESO** (imagen descargada, pendiente flasheo)
- [ ] SD-3: Moonlight (game streaming)
- [ ] SD-4: Música (fusionada con SD-2 via Kodi)
- [ ] SD-5: Karaoke (pospuesto)
- [ ] SD-6: Escritorio (descartado — 256MB RAM insuficiente)

## Archivos del proyecto

| Archivo | Contenido |
|---|---|
| [INVESTIGACION.md](./INVESTIGACION.md) | Research completo del hardware y software |
| [PLAN_DESARROLLO.md](./PLAN_DESARROLLO.md) | Plan por cada SD |
| [PLAN_EJECUCION.md](./PLAN_EJECUCION.md) | Pasos concretos con comandos |
| [FLASHEO_SD2_MAC.md](./FLASHEO_SD2_MAC.md) | Guía de flasheo para macOS |
