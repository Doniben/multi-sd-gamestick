# Plan de Investigación — Multi-SD Game Stick

---

## Bloque 1: Hardware (prioritario)

- [ ] 1.1 Identificar el chip exacto (¿RK3032, S905X, RK3328, RK3229?)
- [ ] 1.2 Verificar la RAM real del dispositivo
- [ ] 1.3 Identificar la versión del kernel Linux en uso
- [ ] 1.4 Tipo de puerto USB: ¿USB 2.0 Host o Micro USB OTG?
- [ ] 1.5 Capacidad de decodificación de video por hardware (H264/H265)
- [ ] 1.6 Ranura microSD: ¿es SD intercambiable o eMMC soldado?

---

## Bloque 2: Sistema base (EmuELEC)

- [ ] 2.1 Versión exacta de EmuELEC preinstalada
- [ ] 2.2 ¿Permite agregar Kodi desde el menú sin reemplazar la imagen?
- [ ] 2.3 ¿Existe imagen SpectralElec o EmuELEC confirmada para este modelo?
- [ ] 2.4 Proceso de flasheo: ¿imagen raw con Etcher o requiere RKDevTool?

---

## Bloque 3: Moonlight / Streaming desde PC

- [ ] 3.1 ¿Moonlight Embedded compila/corre en RK3032 ARMv7?
- [ ] 3.2 ¿El chip soporta decodificación H264 por hardware en Linux?
- [ ] 3.3 Alternativa Parsec: ¿existe cliente Linux ARM funcional?
- [ ] 3.4 GPU de la PC del usuario: ¿compatible con Sunshine (NVENC/AMF/QSV)?
- [ ] 3.5 Latencia de red esperada según setup (Wi-Fi vs Ethernet)

---

## Bloque 4: Conectividad

- [ ] 4.1 ¿OTG Hub alimenta correctamente receptor + dongle Wi-Fi simultáneamente?
- [ ] 4.2 ¿Driver MT7601U incluido en el kernel del stick?
- [ ] 4.3 Tipo físico del puerto USB del stick (para seleccionar el hub correcto)

---

## Bloque 5: SDs adicionales

- [ ] 5.1 (SD-4 Música) ¿Existe interfaz MPD navegable con gamepad para ARM/TV?
- [ ] 5.2 (SD-5 Karaoke) ¿UltraStar Deluxe tiene binarios ARM precompilados?
- [ ] 5.3 (SD-6 Escritorio) ¿Chromium corre en RK3032 con 512MB RAM? Alternativas: Firefox ESR, Midori, NetSurf

---

## Bloque 6: Proceso de desarrollo

- [ ] 6.1 Definir toolchain de compilación cruzada (Docker + Buildroot o QEMU + Debian ARM)
- [ ] 6.2 Proceso para empaquetar cada SD como imagen reproducible
- [ ] 6.3 Sistema de testing sin flashear SD física (emulación QEMU ARM)
