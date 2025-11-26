# Integración Multimedia — Tema 7-8

Este README detalla las decisiones tomadas para la integración multimedia del proyecto, con foco en:
- selección de formatos de audio y vídeo y compatibilidad entre navegadores,
- justificación de las configuraciones de reproducción y su impacto en la experiencia del usuario,
- desafíos técnicos encontrados y soluciones aplicadas.

**Selección de formatos**
- **Vídeo — MP4 (H.264 / AAC):** formato principal por su amplia compatibilidad en Chrome, Edge, Safari (incluido iOS) y navegadores móviles; es la opción recomendada para máxima cobertura.
- **Vídeo — WebM (VP8/VP9 + Opus):** formato alternativo con mejor compresión en navegadores modernos (Chrome, Firefox y versiones recientes de Edge); reduce tamaño y mejora la carga en conexiones limitadas.
- **Vídeo — OGG/Theora (opcional):** alternativa libre que puede cubrir casos puntuales en navegadores más antiguos o entornos específicos; su soporte es más limitado en Safari.
- **Audio — MP3 / AAC:** MP3 es casi universal; AAC ofrece buena calidad y compatibilidad nativa en Safari y muchos dispositivos. Incluir ambos maximiza la reproducción sin necesidad de transcodificar en el cliente.
- **Audio — Ogg Vorbis / Opus:** buena opción libre para navegadores compatibles (Firefox, Chrome); considerar como fallback cuando sea necesario.

**Compatibilidad entre navegadores (resumen práctico)**
- **Chromium (Chrome, Edge, Opera):** soporta MP4, WebM y MP3/Opus.
- **Firefox:** soporta WebM y Ogg de forma nativa; MP4/H.264 puede necesitar codecs del sistema, pero actualmente está ampliamente soportado en escritorio.
- **Safari (macOS / iOS):** históricamente favorece MP4/H.264 y AAC; soporte de WebM es reciente en versiones modernas (ver versión del navegador). Ogg suele no estar disponible.
- **Recomendación:** ofrecer al menos `MP4` + `WebM` para vídeo y `MP3` + `OGG/Opus` para audio cuando sea posible; siempre incluir un mensaje/fallback si el navegador no soporta los formatos ofrecidos.

**Justificación de las configuraciones de reproducción y mejoras UX**
- **`preload="metadata"`:** carga únicamente metadatos (duración, dimensiones) reduciendo uso de datos y evitando descargas innecesarias hasta que el usuario inicie la reproducción.
- **No activar `autoplay` por defecto:** evita reproducción inesperada y problemas con políticas de navegadores (bloqueos por sonido). Mejora la experiencia especialmente en dispositivos móviles y entornos con muchas pestañas.
- **`poster` (imagen de portada):** muestra una miniatura informativa antes de reproducir; mejora accesibilidad y evita sorpresas visuales.
- **Controles nativos + controles complementarios personalizados:** mantener controles nativos garantiza accesibilidad y consistencia; añadir botones claros (play/pause, loop toggle) mejora la claridad de interacción sin romper compatibilidad.
- **Uso de `track` para subtítulos/transcripciones:** recomendando agregar `<track kind="subtitles">` o transcripciones para accesibilidad y SEO.
- **Opciones de loop/muted:** ofrecer toggles permite al usuario adaptar reproducción (por ejemplo, activar loop o silenciar) sin imponer comportamiento.

Estos ajustes buscan equilibrio entre rendimiento, accesibilidad y control del usuario.

**Desafíos técnicos encontrados y cómo se resolvieron**
- **Compatibilidad de formatos:** Algunos navegadores no reproducen todos los codecs. Solución: proporcionar múltiples `<source>` (MP4, WebM, OGG) para el mismo elemento `<video>` y múltiples archivos de audio para `<audio>`; así el navegador elige el que soporta.
- **Autoplay bloqueado en móviles/desktop:** Autoplay con sonido suele estar bloqueado. Solución: no usar `autoplay` —en su lugar mostrar un `modal` con instrucciones y permitir que el usuario inicie la reproducción manualmente; considerar `muted`+`autoplay` sólo si el caso lo requiere y se acepta UX.
- **Tamaños de archivo y tiempo de carga:** Vídeos pesados afectan la experiencia. Solución: ofrecer WebM para mejor compresión, usar `preload="metadata"`, y optimizar bitrate y resolución para la web (p. ej. 720p para presentaciones).
- **Responsive y tamaño del reproductor:** El vídeo debe adaptarse a diferentes pantallas. Solución: estilos en `css/styles.css` para vídeo responsive (ancho 100% y contenedor con aspect-ratio o padding-top hack), y uso de Bootstrap grid para posicionamiento.
- **Control personalizado vs. controles nativos (sincronización):** Es complejo replicar todas las funciones nativas. Solución: mantener controles nativos para funcionalidad completa y añadir botones externos que escuchen eventos (`play`, `pause`, `ended`, `timeupdate`) para sincronizar la UI personalizada con la API HTMLMediaElement.
- **Rutas y permisos de archivo al probar localmente:** Al abrir archivos directamente desde el sistema algunos navegadores limitan recursos (especialmente para `fetch` o CORS). Solución: probar con un servidor local simple (por ejemplo `python -m http.server` o un servidor ligero) para emular entorno HTTP.

**Archivos modificados**
- **`formulario.html`**: bloque de vídeo con múltiples fuentes, controles personalizados y elementos `collapse` para ayuda contextual.
- **`index.html`**: `modal` informativo que explica activación de audio (audio omitido en esta entrega).
- **`css/styles.css`**: estilos para vídeo responsive, controles y transiciones.

**Instrucciones rápidas para probar localmente**
- **Preparar archivos:** coloca los ficheros en `multimedia/`:
   - `multimedia/presentacion.mp4`
   - `multimedia/presentacion.webm`
   - `multimedia/presentacion.ogv` (opcional)
- **Poster:** `imagenes/video-poster.jpg` o usa `imagenes/fotomia.png` si ya existe.
- **Servir la carpeta (recomendado):** en PowerShell, desde la carpeta del proyecto:

```powershell
# desde c:\Users\pedro\Desktop\portfolio
python -m http.server 8000; # o usar cualquier servidor estático
```

- Abrir `http://localhost:8000/formulario.html` en el navegador.
