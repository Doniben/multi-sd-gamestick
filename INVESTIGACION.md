# Plan de Investigación — Multi-SD Game Stick

---

## Bloque 1: Hardware (prioritario)

- [x] 1.1 Identificar el chip exacto → **Rockchip RK3032** (confirmado con fotos de placa: repo `PEARLPALMS/game-stick-4k-lite-segam-m8`)
- [x] 1.2 Verificar la RAM real → **256MB** (2 chips de 128MB, confirmado con fotos)
- [x] 1.3 Versión del kernel → **No bloqueante**. GStickOS y SpectralElec traen su propio kernel precompilado para RK3032. No es necesario conocer la versión original.
- [x] 1.4 Tipo de puerto USB → **USB-A Host** (confirmado: el receptor inalámbrico de mandos ya lo usa). Para agregar dongle Wi-Fi se necesita un **hub USB-A con alimentación externa propia** (el puerto del stick no da corriente suficiente para dos dispositivos). Buscar: "USB 2.0 Hub powered USB-A 4 port" con puerto de alimentación micro USB/USB-C independiente. ~$100–150 MXN en AliExpress.
  ```
  [Stick USB-A] → [Hub con alimentación propia] → Receptor mandos 2.4GHz
                                                 → Dongle Wi-Fi MT7601U
  [Cargador 5V] → [Hub puerto de alimentación]
  ```
- [x] 1.5 Capacidad de decodificación de video → **H264 por hardware vía Rockchip VPU** (moonlight-embedded tiene `src/video/rk.c` y `find_package(Rockchip)` específicamente para este chip)
- [x] 1.6 Ranura microSD → **microSD intercambiable**, todo el sistema vive ahí. El chip solo tiene un bootloader mínimo soldado.

> **Fuentes**: `PEARLPALMS/game-stick-4k-lite-segam-m8`, `lucamot/GStickOS`, `moonlight-stream/moonlight-embedded` CMakeLists.txt

---

## Bloque 2: Sistema base

- [x] 2.1 Versión exacta preinstalada → **SEGAM OS** (Android mínimo propietario, modelo M8-V7.0). Backup disponible en archive.org: `bkp-segam-m-8-v-7.0-v-2.0.0-2024-0210`
- [ ] 2.2 ¿Permite agregar Kodi desde el menú sin reemplazar la imagen?
- [x] 2.3 ¿Existe imagen alternativa confirmada? → **Sí: GStickOS** (`lucamot/GStickOS`) — firmware custom basado en RetroArch, soporta Wi-Fi USB, SSH habilitado, flasheo con Etcher. También existe SpectralElec (XDA) para RK3032.
- [x] 2.4 Proceso de flasheo → **imagen raw con Win32DiskImager / Balena Etcher**. No requiere RKDevTool.

---

## Bloque 3: Moonlight / Streaming desde PC

- [x] 3.1 ¿Moonlight Embedded corre en RK3032? → **Sí**. Tiene soporte explícito para Rockchip en su build system (`ROCKCHIP_FOUND`, `moonlight-rk` lib).
- [x] 3.2 ¿H264 por hardware? → **Sí**, via Rockchip VPU, ya implementado en `moonlight-embedded`.
- [ ] 3.3 Alternativa Parsec: ¿existe cliente Linux ARM funcional?
- [ ] 3.4 GPU de la PC: ¿compatible con Sunshine (NVENC/AMF/QSV)?
- [ ] 3.5 Latencia de red esperada según setup (Wi-Fi vs Ethernet)

---

## Bloque 4: Conectividad

- [ ] 4.1 ¿OTG Hub alimenta correctamente receptor + dongle Wi-Fi simultáneamente?
- [x] 4.2 Driver Wi-Fi → **GStickOS menciona explícitamente soporte para dongle Wi-Fi USB externo**. MT7601U tiene driver nativo en kernel Linux ≥4.x.
- [ ] 4.3 Tipo físico del puerto USB del stick (para seleccionar el hub correcto)

---

## Bloque 5: SDs adicionales

- [ ] 5.1 (SD-4 Música) ¿Existe interfaz MPD navegable con gamepad para ARM/TV?
- [ ] 5.2 (SD-5 Karaoke) ¿UltraStar Deluxe tiene binarios ARM precompilados?
- [ ] 5.3 (SD-6 Escritorio) ¿Chromium corre en RK3032 con 256MB RAM? — con solo 256MB real es muy improbable. Alternativas: NetSurf, Midori, o descartar esta SD.

---

## Bloque 6: Proceso de desarrollo

- [ ] 6.1 Definir toolchain de compilación cruzada (Docker + Buildroot o QEMU + Debian ARM)
- [ ] 6.2 Proceso para empaquetar cada SD como imagen reproducible
- [ ] 6.3 Sistema de testing sin flashear SD física (emulación QEMU ARM)
