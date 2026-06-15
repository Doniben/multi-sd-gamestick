# Plan de Desarrollo — Multi-SD Game Stick

## SD-1: 🎮 Juegos Retro (EXISTENTE)

**Base**: EmuELEC / SpectralElec 3.0  
**Estado**: ✅ Ya existe en el stick original  
**Proyecto**: [SpectralElec XDA](https://xdaforums.com/t/spectralelec-3-0-port-emuelec-retroarch-for-gamestick-4k-m8-rk3032.4680877/)

Mejora opcional: flashear SpectralElec 3.0 para más emuladores y mejor rendimiento.

---

## SD-2: 📺 TV & Películas (Kodi + IPTV)

**Base**: EmuELEC + Kodi  
**Dificultad**: Fácil  
**Proyectos**:
- [EmuELEC](https://github.com/EmuELEC/EmuELEC)
- [iptv-org/iptv](https://github.com/iptv-org/iptv) — +8,000 canales públicos gratuitos M3U

**Funcionalidades**:
- Canales de TV en vivo de todo el mundo (canales públicos, sin pago)
- Películas y series via addons de Kodi
- Navegación 100% con gamepad del stick

**Pasos generales**:
1. Imagen EmuELEC base para RK3032
2. Instalar Kodi desde el menú de EmuELEC
3. Agregar addon IPTV Simple Client
4. Cargar lista M3U de iptv-org/iptv
5. Configurar navegación con gamepad (ya incluida en Kodi)

---

## SD-3: 🖥️ Game Streaming desde PC (Moonlight)

**Base**: Linux ARM minimal + Moonlight Embedded  
**Dificultad**: Media  
**Proyectos**:
- [Sunshine (servidor)](https://github.com/LizardByte/Sunshine) — servidor open source para la PC
- [Moonlight Embedded (cliente)](https://github.com/moonlight-stream/moonlight-embedded) — cliente Linux ARM para el stick

**Cómo funciona**:
```
[Tu PC] --renderiza juego--> [Sunshine] --H264/H265 por red local--> [Moonlight en stick] --> [TV]
              ^                                                              |
              └──────────────── inputs del gamepad ←────────────────────────┘
```

- Tu PC hace todo el trabajo gráfico
- El stick solo decodifica video H264/H265 y envía inputs del control
- Latencia esperada: 1–15ms en red local (imperceptible)
- Requiere: PC conectada a la misma red, dongle Wi-Fi o adaptador Ethernet en el stick

**Requisitos PC**:
- Windows/Linux con GPU Nvidia, AMD o Intel
- Sunshine instalado y configurado
- Misma red local que el stick

**Pasos generales**:
1. Instalar Sunshine en la PC
2. Buildroot o Debian minimal para RK3032 en la SD
3. Compilar/instalar Moonlight Embedded para RK3032
4. Configurar autostart de Moonlight al arrancar
5. Mapear gamepad al protocolo de Moonlight

---

## SD-4: 🎵 Reproductor de Música

**Base**: EmuELEC minimal + MPD + interfaz simple  
**Dificultad**: Fácil  
**Proyectos**:
- [MPD (Music Player Daemon)](https://www.musicpd.org/)
- Interfaz navegable con gamepad

**Funcionalidades**:
- Reproduce desde USB o red local (NAS, Samba)
- Interfaz TV-friendly navegable con el control

---

## SD-5: 🎤 Karaoke

**Base**: Linux ARM + UltraStar Deluxe  
**Dificultad**: Media  
**Proyectos**:
- [UltraStar Deluxe](https://github.com/UltraStar-Deluxe/USDX)

**Funcionalidades**:
- Canciones desde USB o microSD adicional
- Puntuación y multijugador

---

## SD-6: 🌐 Escritorio / Navegador

**Base**: Buildroot + Chromium en modo kiosk  
**Dificultad**: Difícil  
**Casos de uso**:
- YouTube en TV
- Redes sociales
- Cualquier web app

---

## Tabla resumen

| SD | Función | Dificultad | Proyecto base | Custom |
|---|---|---|---|---|
| 1 | Retro Gaming | Ya existe | SpectralElec | No |
| 2 | Kodi/IPTV | Fácil | EmuELEC + Kodi | No |
| 3 | Moonlight PC Streaming | Media | Sunshine + Moonlight Embedded | Algo |
| 4 | Música | Fácil | MPD | Mínimo |
| 5 | Karaoke | Media | UltraStar ARM | Algo |
| 6 | Escritorio/Browser | Difícil | Buildroot | Sí |

---

## Roadmap sugerido

1. **Fase 1** — SD-2 (Kodi + IPTV): la más fácil, valor inmediato
2. **Fase 2** — SD-3 (Moonlight): la más ambiciosa, requiere setup de red
3. **Fase 3** — SD-4/SD-5: complementos de entretenimiento
4. **Fase 4** — SD-6: escritorio completo si el hardware lo permite
