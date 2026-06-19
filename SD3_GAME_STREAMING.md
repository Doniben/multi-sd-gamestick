# SD-3: Game Streaming Headless desde WSL2

## Concepto

Correr un motor de juegos en WSL2 (segundo plano) que:
1. Renderice el juego usando la RTX A6000
2. Encode con NVENC
3. Haga streaming via protocolo Moonlight al Game Stick
4. Sin usar la pantalla de Windows — todo headless en background

**Resultado:** puedes trabajar en Windows mientras el Game Stick recibe un juego desde WSL2.

---

## Solución: Wolf (Games on Whales)

**Wolf** es un servidor de streaming open source compatible con Moonlight, diseñado para correr headless en Docker/Linux.

- Repo: https://github.com/games-on-whales/wolf
- Docs: https://games-on-whales.github.io/wolf/stable/
- Licencia: MIT

### ¿Por qué Wolf y no Sunshine?

| | Sunshine | Wolf |
|---|---|---|
| Necesita display real | Sí (o dummy plug) | No — crea virtual display |
| Diseñado para headless | No | Sí |
| Multi-usuario | No | Sí |
| Docker-first | No | Sí |
| Compatible con Moonlight | Sí | Sí |
| Soporte WSL2 | No documentado | Sí (tab dedicada en docs) |

### Arquitectura

```
┌─────────────────────────────────────────────────┐
│ Windows (tu trabajo normal)                     │
│                                                 │
│  ┌───────────────────────────────────────────┐  │
│  │ WSL2 (Ubuntu)                             │  │
│  │                                           │  │
│  │  ┌─────────────────────────────────────┐  │  │
│  │  │ Docker                              │  │  │
│  │  │                                     │  │  │
│  │  │  Wolf ──┬── Virtual Wayland Display │  │  │
│  │  │         ├── NVENC Encoder (A6000)   │  │  │
│  │  │         └── Moonlight Protocol      │  │  │
│  │  │              ↓ stream por red       │  │  │
│  │  └─────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────┘  │
└─────────────────────────────────────────────────┘
         │ UDP/TCP (red local)
         ▼
┌─────────────────────┐
│ Game Stick (SD-3)   │
│ Moonlight client    │
│ Mando inalámbrico   │
└─────────────────────┘
```

---

## Requisitos verificados

| Requisito | Estado |
|---|---|
| GPU NVIDIA en host | ✅ RTX A6000 |
| CUDA en WSL2 | ✅ Soportado oficialmente por NVIDIA |
| NVENC en WSL2 | ✅ Funciona (mismo driver Windows expone hardware) |
| Docker en WSL2 con GPU | ✅ Docker Desktop con WSL2 backend soporta `--gpus all` |
| Wolf en WSL2 | ⚠️ "hasn't been properly tested" pero tiene instrucciones |
| /dev/uinput en WSL2 | ❌ No disponible → sin joypad directo (solo mouse+keyboard) |
| Network host desde WSL2 | ⚠️ WSL2 tiene su propia IP, requiere port forwarding o bridge |

---

## Problemas conocidos y soluciones

### 1. Sin /dev/uinput en WSL2 → sin gamepad virtual

**Problema:** WSL2 no expone `/dev/uinput`, necesario para que Wolf cree joypads virtuales.

**Solución:** Usar **compilación custom del kernel WSL2** con `CONFIG_INPUT_UINPUT=y`:
```bash
# Compilar kernel WSL2 custom con uinput habilitado
git clone --depth=1 https://github.com/microsoft/WSL2-Linux-Kernel
cd WSL2-Linux-Kernel
# Editar .config: CONFIG_INPUT_UINPUT=y
make -j$(nproc)
# Copiar vmlinux a Windows y configurar .wslconfig
```

**Alternativa simple:** El Game Stick envía input del mando por red (protocolo Moonlight). Wolf recibe ese input y lo inyecta en el juego. Si uinput no funciona, el mando del stick se mapea como mouse — no ideal para juegos pero funciona para navegar.

### 2. Red WSL2 ≠ red host

**Problema:** WSL2 tiene su propia subred (ej: 172.x.x.x), no es visible directamente desde el Game Stick.

**Soluciones:**
- **mirrored networking** (Windows 11): en `.wslconfig` poner `networkingMode=mirrored` → WSL2 comparte las IPs del host
- **Port forwarding**: `netsh interface portproxy add v4tov4 listenport=47989 connectaddress=<WSL_IP>`
- **bridge mode**: más complejo pero posible

### 3. nvidia-drm modeset

**Problema:** Wolf necesita `nvidia-drm.modeset=1` que no aplica en WSL2.

**Solución:** Wolf puede funcionar sin eso en modo WSL2 ya que el rendering se hace vía `/dev/dri/renderD128` que sí está disponible.

---

## Plan de implementación

### Fase 1: Verificar infraestructura (hacer desde esta PC)
```bash
# En WSL2:
nvidia-smi                          # verificar GPU visible
ls /dev/dri/                        # verificar render node
docker run --gpus all nvidia/cuda:12.0-base nvidia-smi  # GPU en Docker
```

### Fase 2: Levantar Wolf en Docker
```bash
# docker-compose.yml para WSL2 + NVIDIA
docker run \
    --name wolf \
    --network=host \
    -v /etc/wolf:/etc/wolf:rw \
    -v /var/run/docker.sock:/var/run/docker.sock:rw \
    --device /dev/dri/ \
    --gpus all \
    -e NVIDIA_DRIVER_CAPABILITIES=all \
    -e NVIDIA_VISIBLE_DEVICES=all \
    ghcr.io/games-on-whales/wolf:stable
```

### Fase 3: Configurar red
```
# En .wslconfig (Windows side):
[wsl2]
networkingMode=mirrored
```

### Fase 4: Parear Moonlight desde Game Stick
- Apuntar Moonlight al IP del host Windows
- Ingresar PIN en Wolf UI (http://HOST_IP:47989)

### Fase 5: Agregar juegos
- Steam (container con Proton)
- RetroArch (container)
- Emuladores standalone

---

## En el Game Stick (SD-3)

La SD-3 solo necesita **Moonlight** como cliente. Opciones:
- Si GStickOS tiene Flatpak/AppImage: instalar Moonlight
- Si no: usar una imagen dedicada (ej: Lakka con Moonlight, o minimal Linux + Moonlight)
- Alternativa: correr Moonlight como addon de Kodi

---

## Siguiente paso inmediato

### Hallazgos (verificados 2026-06-19):
```
✅ nvidia-smi funciona en WSL2
   GPU: NVIDIA RTX A1000 6GB Laptop (no A6000 como se pensaba)
   Driver: 595.71 / CUDA 13.2
✅ NVENC disponible (la A1000 lo soporta)
❌ /dev/dri/ NO existe aún en esta instancia WSL2
❌ Docker NO instalado en WSL2
```

### Pendientes para habilitar Wolf:
1. **Instalar Docker en WSL2** (no Docker Desktop, sino docker-ce nativo):
   ```bash
   curl -fsSL https://get.docker.com | sh
   sudo usermod -aG docker $USER
   ```
2. **Habilitar /dev/dri** — puede requerir actualizar WSL:
   ```bash
   # Desde PowerShell (Windows):
   wsl --update
   # Reiniciar WSL
   wsl --shutdown
   ```
3. **Instalar nvidia-container-toolkit:**
   ```bash
   distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
   curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
   curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
     sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
     sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
   sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
   sudo nvidia-ctk runtime configure --runtime=docker
   sudo systemctl restart docker
   ```
4. **Configurar mirrored networking** — archivo `%USERPROFILE%\.wslconfig`:
   ```ini
   [wsl2]
   networkingMode=mirrored
   ```
5. **Probar GPU en Docker:**
   ```bash
   docker run --rm --gpus all nvidia/cuda:12.0-base nvidia-smi
   ```
6. **Si todo OK → levantar Wolf**

### Nota sobre la GPU
La GPU es una RTX A1000 6GB Laptop, no una A6000. Esto es suficiente para:
- NVENC encoding (H.264/HEVC) a 1080p60 sin problema
- Juegos ligeros/emuladores renderizados localmente
- Para juegos AAA pesados será limitante el rendimiento de la GPU, no el streaming
