# Servicios propios: subrr, duberr y querr

[← Volver al README](../README.md)

Hasta ahora todo lo que corría en el homelab lo había hecho otra gente: yo lo instalaba y lo configuraba. **subrr y duberr son los primeros servicios del homelab que desarrollo yo**, o más bien que dirijo yo: están *vibecodeados* con Claude Code. Yo pongo la idea, pruebo con mis series y mis fallos reales y decido qué se queda; Claude escribe el código a mi lado, en el propio servidor.

> **Por ahora son herramientas personales.** Hacen lo que yo necesito, con mi biblioteca y mis ajustes, y no están pensadas todavía para que las use otra persona. subrr y duberr tendrán su **propio repositorio** más adelante; cuando existan, lo enlazaré desde aquí.

Son imágenes propias, construidas en el servidor, y están en el panel de Homarr como cualquier otra app.

## subrr: subtítulos traducidos

**Qué problema resuelve:** Bazarr busca subtítulos ya hechos, pero a veces no hay ninguno en castellano para esa versión del vídeo. Y el vídeo suele traer subtítulos en inglés dentro.

**Qué hace:** mira cada episodio o película que elijo, o que Bazarr echa de menos, saca la mejor pista de subtítulos de texto, la **traduce** y la guarda junto al vídeo (o como pista nueva dentro del mkv). Jellyfin y Bazarr la ven como cualquier otro subtítulo.

- **Traducción en cadena:** DeepL, Claude, cualquier modelo compatible con OpenAI, Google, Microsoft y LibreTranslate (este último corre en el propio servidor, gratis). Se usa el primero que valga; si uno se queda sin cupo a mitad, sigue el siguiente, y lo hecho por un motor de reserva se rehace después con uno principal.
- **OCR:** si el vídeo solo trae subtítulos de imagen (PGS, VobSub) los lee con Tesseract o con un motor con visión, y también puede leer el texto grabado en la imagen de las versiones dobladas (rótulos, epílogos).
- **Prudente:** nunca modifica un archivo existente (escribe un temporal y lo renombra) y antes de meter una pista en un mkv guarda el original en una papelera con enlace duro.
- **Ficha por episodio** con las pistas que tiene, la que se usó y el original y la traducción lado a lado.

**Un arreglo reciente (11-nov):** el ajuste «saltar si el audio ya está en el idioma» marcaba los episodios como terminados para siempre. Al desactivarlo no se reprocesaba nada, y sacar la serie y volver a ponerla tampoco, porque el estado va por archivo de vídeo. Ahora, al desactivarlo, esos episodios vuelven a la cola solos.

## duberr: el audio en castellano

**Qué problema resuelve:** muchas series y películas en buena calidad solo traen el audio original, y las versiones con castellano suelen ser de peor calidad. Quiero **la mejor imagen con el audio en castellano**.

**Qué hace:** para las series o películas que elijo desde su web (conectada a Sonarr y Radarr):

- Busca versiones con castellano, guarda **sin recodificar** la pista de audio en castellano y la **mezcla** con el vídeo de mejor calidad.
- **Mide antes de sustituir:** compara el castellano con el original para detectar si va a la misma velocidad (23,976 frente a 25 fps) o si hay deriva, y lo corrige. Las partes que no existen en castellano se rellenan con el original, se recortan o quedan en silencio, según elija.
- **Por episodio:** mientras ninguno tiene castellano usa un perfil de calidad pensado para encontrarlo; en cuanto lo tiene, el episodio pasa a un perfil de calidad.
- **Deshacer:** cada mezcla deja una copia del archivo anterior durante 14 días y puede deshacerse desde la ficha del episodio. Tiene también vista previa de audio, para oír el original y el castellano a la vez.
- **Seguro con los torrents:** nunca modifica un archivo existente (sería modificar un torrent enlazado) y no toca los que siguen compartiéndose.

## querr: «¿qué te apetece comer?» (WIP)

> **Trabajo en curso.** querr es un proyecto que estoy haciendo en pareja. La idea es sencilla: escribir lo que te apetece y ver restaurantes con reparto, con sus ofertas, los platos a los que se aplican y la carta. Pero **por ahora está chungo**, por cómo las cadenas llevan sus APIs.

- **Funciona** con Just Eat: listado, ofertas con los platos a los que se aplican y carta completa, con buscador.
- **No funciona** con el resto. Uber Eats pide captcha, y Glovo, Too Good To Go y TheFork están protegidos con antibots. Esas plataformas se quedan como simples enlaces, y no voy a intentar saltarme sus protecciones. Deliveroo y foodpanda no operan en España.
- Así que, de momento, es más una prueba de concepto que otra cosa. Seguirá evolucionando si encontramos la forma de hacerlo bien.

## Cómo se publican

Ninguno está en internet: como el resto, solo por Tailscale. Las claves y los ajustes de cada uno viven en su propio volumen y no entran en ningún repositorio.
