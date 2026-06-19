# Listas IPTV para Kodi — Fuentes

## Repositorios GitHub (recomendados — se actualizan automáticamente)

### 1. ⭐ CharlieII/IPTV_mexico (MEJOR OPCIÓN para México)
- **URL M3U directa:** `https://raw.githubusercontent.com/CharlieII/IPTV_mexico/refs/heads/main/lista.m3u`
- **Repo:** https://github.com/CharlieII/IPTV_mexico
- Canales mexicanos y latinos: Las Estrellas, Azteca 7, Canal 5, noticias, deportes
- Curada manualmente, solo TV abierta legal
- 109 commits, activo
- **Sin canales para adultos**

### 2. Free-TV/IPTV (mundial, 17.6k ⭐)
- **URL M3U directa:** `https://raw.githubusercontent.com/Free-TV/IPTV/master/playlist.m3u8`
- **Repo:** https://github.com/free-TV/IPTV
- Canales gratuitos de todo el mundo (Pluto TV, Samsung TV Plus, YouTube Live, etc.)
- Solo HD, solo canales legalmente gratuitos
- 1,552 commits, muy activo
- **Sin canales para adultos ni religiosos**

### 3. mametchikitty/Listas-IPTV (películas/series latino)
- **Repo:** https://github.com/mametchikitty/Listas-IPTV
- Listas temáticas: películas, Studio Ghibli latino, dibujos animados
- Útil para contenido VOD más que TV en vivo

### 4. myfriendqaz/IPTV2025 (fork de Free-TV)
- **Repo:** https://github.com/myfriendqaz/IPTV2025
- Fork del anterior con actualizaciones 2025

### 5. ⚡ Sunstar16/MagisTV-AS-A-m3u-PLAYLIST (canales de Xuper TV / Magis TV)
- **URL M3U directa:** `https://raw.githubusercontent.com/Sunstar16/MagisTV-AS-A-m3u-PLAYLIST/refs/heads/main/MagisTV%20-%20PLAYLIST.m3u`
- **URL M3U+ (extendida):** `https://raw.githubusercontent.com/Sunstar16/MagisTV-AS-A-m3u-PLAYLIST/refs/heads/main/MagisTV%2B.m3u`
- **EPG (guía de programación):** `https://raw.githubusercontent.com/Sunstar16/MagisTV-AS-A-m3u-PLAYLIST/refs/heads/main/MagisTV%20-%20EPG.xml`
- **Repo:** https://github.com/Sunstar16/MagisTV-AS-A-m3u-PLAYLIST
- Son los mismos canales que tiene la app **Xuper TV** (antes Magis TV)
- Incluye canales de pago latinos: deportes, películas, series, etc.
- **⚠️ REQUIERE VPN A SUECIA** para que funcionen los streams
- **⚠️ Legalmente gris** — retransmite canales de pago sin autorización (bloqueado en Argentina)
- Los links se actualizan periódicamente (86 commits)

---

## VPN gratuita para Suecia (necesaria para lista Magis TV)

### Opción 1: Proton VPN Free (RECOMENDADA)
- **Descarga:** https://protonvpn.com/free-vpn/
- Sin límite de datos, sin anuncios
- Sin tarjeta de crédito, sin registro de actividad
- Plan gratuito: servidores en ~10 países (USA, Japón, Países Bajos, Polonia, Rumanía...)
- **⚠️ Suecia NO está en el plan gratuito** — está en el plan Plus ($2.99/mes)
- Alternativa: probar con servidor de Países Bajos o Polonia (a veces funciona)

### Opción 2: Urban VPN (la que recomienda el repo)
- Chrome: https://chromewebstore.google.com/detail/urban-vpn-proxy/eppiocemhmnlbhjplcgkofciiegomcon
- Android: https://play.google.com/store/apps/details?id=com.vpn.free.hotspot.secure.vpnify
- **Tiene servidor en Suecia gratis**
- Menos confiable en privacidad que Proton, pero funciona para IPTV

### Opción 3: Windscribe Free
- **Descarga:** https://windscribe.com/download
- 10GB/mes gratis, pero tiene servidor en Suecia en plan gratuito si confirmas email (+10GB)
- App para Android disponible

### En el Game Stick (GStickOS/EmuELEC — WireGuard nativo):

GStickOS está basado en EmuELEC/CoreELEC que **ya trae WireGuard en el kernel**.
No necesitas instalar nada — solo copiar un archivo `.conf` vía SSH.

Ver instrucciones completas en [VPN_GAMESTICK.md](./VPN_GAMESTICK.md)

---

## Cómo buscar en Telegram

Buscar en Telegram con estos términos:
- `IPTV m3u gratis`
- `free m3u playlist`
- `IPTV México listas`
- `listas IPTV actualizadas`

**Tips:**
- Las listas de Telegram caducan rápido (días/semanas)
- Prefiere los repos de GitHub que se auto-actualizan
- Nunca des datos personales ni pagues en canales random

---

## Configuración en Kodi (PVR IPTV Simple Client)

1. Abrir Kodi → Add-ons → Mis add-ons → Clientes PVR
2. Buscar **PVR IPTV Simple Client** → Instalar
3. Configurar → General → Ubicación: **URL remota**
4. Pegar la URL M3U (recomendada: la de CharlieII para México)
5. OK → Reiniciar Kodi
6. Los canales aparecen en la sección "TV" del menú principal

### URLs para copiar-pegar en Kodi:

```
# México + Latino (CharlieII):
https://raw.githubusercontent.com/CharlieII/IPTV_mexico/refs/heads/main/lista.m3u

# Mundial (Free-TV):
https://raw.githubusercontent.com/Free-TV/IPTV/master/playlist.m3u8
```

---

## Notas legales

- Las listas de CharlieII y Free-TV solo contienen canales de TV abierta/gratuita
- No se incluyen canales de pago ni contenido pirata
- Estas fuentes compilan enlaces públicos que las propias televisoras emiten en internet
