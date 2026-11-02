# 💬 Subtítulos con Bazarr

[← Volver al README](../README.md)

Bazarr descarga los subtítulos que faltan y los **sincroniza solos** con el vídeo usando **ffsubsync**: compara el subtítulo con una referencia y lo desplaza (y, si hace falta, cambia su velocidad por una diferencia de fps).

## Cómo se sincroniza

- **Referencia:** el subtítulo incrustado más largo del vídeo. Si no hay ninguno de texto, el audio.
- **Qué se toca:** solo los subtítulos externos (`.srt` / `.ass`). Los incrustados en el `.mkv` ya van a tiempo, y los forzados no se sincronizan.
- **Lo lento:** los vídeos grandes (unos 4 GB) cuyos subtítulos incrustados son imágenes (PGS): no sirven de referencia, así que hay que leer el archivo entero y analizar el audio. En Windows, Docker lee el disco despacio, lo que lo agrava.

## Ajustes

*Settings → Subtitle Processing → Audio Synchronization*

| Ajuste | Valor | Por qué |
|---|---|---|
| Enable Automatic Subtitles Audio Synchronization | Sí | Cada subtítulo que baje se sincroniza solo |
| Series Score Threshold | 96 | Valor recomendado por TRaSH Guides: por encima, el subtítulo ya es de esa versión exacta |
| Movies Score Threshold | 86 | Lo mismo, para películas |
| Synchronization Reference | Subtítulo incrustado (sin forzar audio) | Más rápido y preciso cuando lo hay |
| Do Not Fix Framerate Mismatch | Desactivado | Corrige subtítulos de 25 fps en vídeos de 23,976 y activa los controles de calidad |
| Quality Checks | Por defecto | Si el resultado no es fiable, deja el subtítulo como estaba |
| Golden-Section Search | Sí | Busca mejor la relación de fps |
| Max Offset Seconds | 60 | |

Con la sincronización activa, una puntuación mínima de 80 en series es aceptable: lo que baje algo peor se pone a tiempo solo.

Para resincronizar muchos a la vez con un solo disco, conviene bajar los trabajos simultáneos (*Settings → General → Jobs Manager → Concurrent Jobs*) de 4 a 1.

## Aviso a Jellyfin

Bazarr está conectado a Jellyfin (`http://jellyfin:8096`) con refresco inmediato de las bibliotecas. En Windows, Docker no avisa a Jellyfin de los archivos nuevos, así que sin esto los subtítulos nuevos o corregidos no aparecerían hasta el siguiente escaneo.

## Script `sincronizar-subtitulos.ps1`

Script en PowerShell para dejarlo todo configurado y arreglar lo que ya estaba descargado:

```powershell
powershell -ExecutionPolicy Bypass -File .\sincronizar-subtitulos.ps1
```

1. Aplica los ajustes de la tabla por la API de Bazarr.
2. Conecta Jellyfin si no lo estaba.
3. Busca los subtítulos descargados antes, con puntuación por debajo del umbral y sin sincronizar. Los enseña, pide confirmación, **copia los originales** a una carpeta con fecha (con un `lista.csv`) y los manda sincronizar.

| Opción | Qué hace |
|---|---|
| `-SoloAjustes` | Solo aplica la configuración |
| `-Todos` | Incluye también los que venían con el vídeo |
| `-Si` | No pregunta |
| `-Estado` | Muestra la cola, el % de cada trabajo en marcha y el desfase corregido |
| `-Restaurar` | Devuelve la última copia de los originales (`-Copia <carpeta>` para otra) |

La clave de la API la lee en cada ejecución desde la configuración del contenedor (con `docker exec`) y **no se guarda en ningún archivo**.

**Primera ejecución:** 20 subtítulos con puntuación de 86–87 % a la cola; uno de ellos ya estaba sincronizado, con +2,1 s de desfase corregido.

## Arreglar uno a mano

*Bazarr → serie → episodio → pulsar en el subtítulo → Sync*. Ahí se puede elegir la referencia: una pista de audio concreta o un subtítulo incrustado.

## En el servidor nuevo

Los ajustes viven en el volumen de configuración de Bazarr, así que viajan con la mudanza. El script es para Windows; en Ubuntu, el estado y la restauración se pueden hacer desde la web de Bazarr.
