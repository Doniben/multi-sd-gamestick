# SD-X: Browser Thin Client — Aplicaciones procesadas en servidor

## Concepto

Una SD con Linux minimal + browser que actúa como **thin client visual**.
Las aplicaciones se procesan enteramente en esta PC (WSL2/Docker) y el Game Stick
solo recibe el output renderizado por red. Sin caché pesado, sin archivos estáticos locales.

```
┌─────────────────────────────────────────────────────┐
│ PC (WSL2 / Docker)                                  │
│                                                     │
│  App Three.js ──► GPU Render ──► Encode ──► Stream  │
│  App IA        ──► CPU/GPU    ──► Encode ──► Stream │
│  App Dashboard ──► Process    ──► Encode ──► Stream │
│                                                     │
│  Todo el compute se queda aquí                      │
└────────────────────────┬────────────────────────────┘
                         │ WebRTC / VNC / HTTP (red local)
                         ▼
┌─────────────────────────────────────────────────────┐
│ Game Stick (SD-X)                                   │
│                                                     │
│  Linux minimal + Chromium/Firefox                   │
│  Solo decodifica video y renderiza UI ligera        │
│  Input (mando/teclado) → envía por red al server   │
└─────────────────────────────────────────────────────┘
```

---

## Hay 2 arquitecturas posibles

### Arquitectura A: Pixel Streaming (GPU renderiza, streama video)

El servidor renderiza la escena 3D (Three.js, Unreal, etc.) y envía un **stream de video**
vía WebRTC al browser del stick. El browser solo decodifica video + envía input.

**Ventaja:** el stick no necesita nada de WebGL — recibe pixels puros.
**Desventaja:** latencia de encoding/decoding (~20-50ms extra).

Tecnologías:
- **Wolf/Sunshine + Moonlight** (lo de SD-3, pero con apps en vez de juegos)
- **Neko** — browser virtual remoto en Docker, streamed via WebRTC
- **Kasm Workspaces** — containerized apps + VNC/WebRTC al browser
- **NVIDIA CloudXR.js** — para apps XR/3D específicamente

### Arquitectura B: Server-side compute + Client-side render ligero

El servidor procesa la lógica/datos y envía solo **geometría/instrucciones**
al browser, que hace un render ligero (2D o WebGL simple).

**Ventaja:** menor latencia, interacción más fluida.
**Desventaja:** el stick necesita algo de GPU/CPU para render básico (pero el RK3032 tiene Mali GPU).

Tecnologías:
- **WebSockets + Canvas/SVG** para dashboards y apps 2D
- **Three.js con server-side physics** — server calcula, client solo dibuja
- **Streaming SSR** — server renderiza HTML que el browser solo pinta

### Arquitectura C (RECOMENDADA): Hybrid — Neko/Kasm como base

Un **browser virtual** corre en Docker en tu PC. El Game Stick abre un browser
que se conecta a ese browser virtual. Es como un TeamViewer/VNC pero optimizado
para browser, via WebRTC con aceleración GPU.

**Ventaja:** CUALQUIER aplicación web funciona sin modificar. Zero config por app.
**Desventaja:** depende del ancho de banda de red local (1080p60 ≈ 5-15 Mbps).

---

## Solución recomendada: Neko

**n.eko** (https://neko.m1k1o.net/) — browser virtual en Docker, accesible desde cualquier browser.

- Open source, self-hosted
- Soporta Chrome, Firefox, etc. como containers
- Streaming via WebRTC con GPU encoding (NVENC)
- Multi-usuario (puedes ver lo mismo desde varios dispositivos)
- Input completo: mouse, teclado, gamepads
- Baja latencia en red local

```bash
# Ejemplo: levantar un browser virtual con Firefox
docker run -d --name neko \
  -p 8080:8080 -p 52000-52100:52000-52100/udp \
  -e NEKO_SCREEN=1920x1080@60 \
  -e NEKO_PASSWORD=neko \
  -e NEKO_PASSWORD_ADMIN=admin \
  --gpus all \
  ghcr.io/m1k1o/neko/nvidia-firefox:latest
```

Después solo abres `http://<IP_PC>:8080` en el browser del Game Stick.

---

## Aplicaciones posibles por campo

### 🎮 Entretenimiento
- Juegos web (Three.js, Babylon.js, itch.io)
- Emuladores en browser (RetroArch web)
- YouTube/Netflix/Streaming (sin caché local)

### 🎨 Creatividad / 3D
- Three.js mundos interactivos
- Blender web viewer
- A-Frame / experiencias VR-lite
- Photopea (Photoshop en browser)
- Figma

### 🤖 IA / Productividad
- Chat con LLMs locales (Ollama + Open WebUI)
- Stable Diffusion WebUI (generación de imágenes)
- ComfyUI (workflows de IA)
- Jupyter Notebooks con visualizaciones
- VS Code Server (code-server)

### 📊 Dashboards / Monitoreo
- Grafana
- Home Assistant
- Dashboards custom con D3.js/Chart.js
- Feeds de cámaras de seguridad

### 📚 Educación
- Simulaciones de física (Matter.js, cannon-es)
- Mapas interactivos (Mapbox GL, Cesium)
- Anatomía 3D, química molecular
- Presentaciones interactivas

### 🎵 Música / Audio
- DAWs web (Soundtrap, BandLab)
- Visualizadores de audio
- Spotify web

---

## En el Game Stick (SD-X)

### Opción 1: GStickOS + browser (si Kodi tiene web browser addon)
- Addon "Web Browser" para Kodi existe pero es limitado

### Opción 2: Linux minimal dedicada
- Imagen con: Linux + Chromium en kiosk mode + Wi-Fi
- Boot directo a browser fullscreen apuntando a `http://<IP_PC>:8080`
- Opciones: Alpine + Chromium, DietPi + Chromium, Armbian minimal

### Opción 3: Usar la misma SD-3 (Wolf)
- Wolf puede stremar un escritorio Linux completo (con browser dentro)
- El browser del escritorio virtual accede a las apps locales
- No necesitas browser en el stick — Moonlight ya es el viewer

---

## Comparación de enfoques

| Enfoque | Latencia | Complejidad | Flexibilidad | RAM stick |
|---|---|---|---|---|
| Neko (browser remoto) | Baja (WebRTC) | Media | Muy alta | ~50MB (solo browser) |
| Wolf/Moonlight (desktop virtual) | Muy baja | Ya configurado (SD-3) | Alta | ~20MB (Moonlight) |
| Kasm Workspaces | Baja | Alta (enterprise) | Muy alta | ~50MB |
| Server API + client render | Mínima | Alta (custom dev) | Por app | ~100MB+ |

---

## Plan

1. **Corto plazo:** Usar Wolf (SD-3) para todo — ya incluye desktop virtual con browser
2. **Medio plazo:** Montar Neko para apps específicas que quieras acceder sin Moonlight
3. **Largo plazo:** Si desarrollas apps custom Three.js, usar Pixel Streaming vía WebRTC directo

---

## Nota importante

La **SD-3 (Wolf/Moonlight) ya cubre este caso de uso**. Wolf stremea un escritorio
Linux virtual completo con browser. Podrías tener:
- Wolf app 1: Steam (juegos)
- Wolf app 2: Desktop con Chromium (todas las apps web)
- Wolf app 3: RetroArch (emuladores)

Cada una es un container Docker diferente, seleccionable desde Moonlight.
No necesitas una SD separada a menos que quieras un setup dedicado sin Moonlight.
