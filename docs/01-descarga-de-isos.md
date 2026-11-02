# Descarga de ISOs y biblioteca

[← Volver al README](../README.md)

La parte del homelab que recibe una petición, la busca en los proveedores, la descarga y la deja ordenada en la biblioteca, sin intervención manual.

## Cómo funciona

```mermaid
sequenceDiagram
    participant M as Móvil (Seerr)
    participant A as Gestores de biblioteca
    participant P as Prowlarr (indexadores)
    participant D as Clientes de descarga
    participant J as Jellyfin
    M->>A: Petición
    A->>P: Búsqueda
    P-->>A: Resultados de los proveedores
    A->>A: Elige según el perfil de calidad e idioma
    A->>D: Envía la descarga
    D-->>A: Descarga terminada
    A->>A: Renombra e importa (hardlink)
    A->>J: Avisa de que hay algo nuevo
```

| Pieza | Papel |
|---|---|
| **Seerr** | Interfaz para hacer peticiones desde el móvil, con el usuario de Jellyfin |
| **Sonarr / Radarr** | Gestores de biblioteca: deciden qué descargar según el perfil, lo mandan al cliente y, al terminar, lo renombran y lo importan |
| **Prowlarr** | Gestor central de proveedores / indexadores; los reparte a los gestores de biblioteca |
| **Jackett, FlareSolverr** | Auxiliares de Prowlarr para algunos indexadores |
| **NZBGet** | Cliente de descarga por Usenet |
| **qBittorrent** | Cliente de descarga por BitTorrent |
| **Decluttarr** | Limpia descargas atascadas, lentas o fallidas |

## Estructura de carpetas

Las descargas y la biblioteca están en **un mismo disco y una misma carpeta**, montada como `/data` en todos los contenedores:

```
/data
├── media/      ← biblioteca de Jellyfin
├── torrents/   ← descargas de qBittorrent
└── usenet/     ← descargas de NZBGet
```

Así, al importar, los gestores de biblioteca crean un **hardlink** en lugar de copiar: es instantáneo y el archivo no ocupa el doble.

| | Windows (hasta el 30-oct) | Ubuntu (desde el 31-oct) |
|---|---|---|
| Carpeta real | `J:\homelab\data` | `/srv/data` (disco de datos) |
| Dentro de los contenedores | `/data` | `/data` |

Como dentro de los contenedores la ruta no cambia, en la mudanza **no hay que reconfigurar ninguna aplicación**.

## Docker Compose

- Un `docker-compose.yml` con la pila principal y un `docker-compose.override.yml` con los extras (panel, avisos, monitorización, acceso remoto). Docker los une solo.
- Variables comunes definidas una sola vez con un **ancla YAML** (`&entorno`) y reutilizadas en cada servicio.
- La carpeta de datos se monta como `${DATA_DIR:-/srv/data}:/data`, así el mismo archivo sirve en Windows y en Linux.
- La configuración de cada aplicación va en **volúmenes con nombre**, fáciles de exportar y restaurar.
- Las contraseñas y claves van en archivos `.env`, fuera del compose.

## Perfiles

Los gestores de biblioteca usan **perfiles de calidad e idioma** con puntuaciones, para priorizar el audio en castellano y la calidad que quiero sin tener que elegir a mano cada vez.

Desde el 2-nov, el perfil de series está ordenado para que una "mejora" **nunca baje de 1080p a 720p** (antes, 720p estaba por encima de 1080p HDTV), y el gestor de series tiene la **papelera activada durante 30 días**: si sustituye un archivo, el anterior no se borra al momento. Por qué hizo falta, en [la reacción en cadena](#mejoras-automáticas-una-reacción-en-cadena-2-nov).

## NZBGet

- **Rutas:** la imagen de LinuxServer apunta por defecto a `/downloads`, que no existe en un montaje basado en `/data`. Hay que cambiar `MainDir` a `/data/usenet` y dejar el resto de rutas con `${MainDir}`; si no, salen errores de *Permission denied*.
- **Reparación:** búfer de reparación (`ParBuffer`) a 500 MB.
- **Extensión *Completion*:** antes de descargar, comprueba que el archivo esté completo en el servidor. Si faltan demasiadas partes, lo descarta y el gestor de biblioteca busca otra opción en segundos. Encontré y corregí un fallo en ella (ver abajo). Con tolerancia 0 %, eso sí, puede provocar [una reacción en cadena](#mejoras-automáticas-una-reacción-en-cadena-2-nov).
- **Velocidad:** en Windows iba a unos 10 MB/s, limitada por la escritura en el HDD SMR a través de Docker Desktop. En el servidor, con la carpeta temporal en el SSD, la primera descarga fue a **84,9 MB/s** (≈680 Mbps). Ver [Servidor](08-servidor-nuevo.md#los-resultados).
- **Caché de artículos** a 500 MB, ajustada a los 8 GB de RAM del servidor.

### El fallo de la extensión *Completion*

**Síntoma:** una descarga casi completa (le faltaba un 0,2 %, reparable de sobra) acabó marcada como fallida y borrada.

**Causa:** la extensión se dispara con distintos avisos de NZBGet ("se ha añadido un NZB", "se ha borrado un NZB"…). Al procesar la cola, en vez de revisar el archivo NZB de cada elemento, revisaba siempre el del NZB que había provocado el aviso. Así, al borrarse otra descarga que sí estaba rota, comprobó ese archivo (38 % de partes perdidas) y castigó al siguiente de la cola quitándole sus archivos de reparación (par2). Sin ellos, NZBGet no pudo reconstruir los pocos trozos que faltaban.

**Solución:** un cambio de unas 6 líneas en `main.py` de la extensión: para cada NZB de la cola usa el nombre de su propio archivo, que la extensión ya guarda al añadirlo, y solo si no lo encuentra recurre al del aviso.

## qBittorrent

- **Ruta de guardado:** la imagen de LinuxServer también usa `/downloads` por defecto; hay que cambiarla a `/data/torrents`.
- **Conectividad:** puerto de escucha redirigido en el router y permitido en el firewall de Windows.
- **Versión fijada:** con qBittorrent 5.2.4 la web se colgaba, así que la imagen está fijada a la versión anterior en el compose.
- **qBittorrent 5.2** cambió la respuesta de su API de inicio de sesión, y eso rompió los avisos del homelab. Ver [Avisos y paneles propios](06-avisos-y-paneles.md#arreglos-del-26-de-octubre).

## Decluttarr

- Lo arranqué primero en **modo prueba** (`test_run`), que solo anota lo que haría, y revisé el log antes de activarlo.
- Desactivé dos reglas después de diagnosticar con los logs que borraban descargas legítimas:
  - `remove_orphans`: borraba las descargas lanzadas a mano desde Prowlarr, porque el gestor de biblioteca no las reconocía como suyas.
  - `remove_unmonitored`: borraba descargas de elementos que no estaban marcados como seguidos.
- Cada cambio en `config.yaml` deja una copia `.bak` con fecha.

## Indexadores

El 27 de octubre los reorganicé gracias al widget de estado de Homarr, que dejó a la vista indexadores **dados de alta dos veces** (en Prowlarr y en Jackett) y otros que **fallaban en cada búsqueda** y solo la ralentizaban. Ahora cada indexador está en un solo sitio, directamente en Prowlarr siempre que lo soporta.

## Mejoras automáticas: una reacción en cadena (2-nov)

Revisé con `ffprobe` la calidad real de una serie de la biblioteca (códec, resolución, bitrate, audio e idiomas). Salieron temporadas de entre 2,4 y 9,2 Mb/s, un episodio etiquetado como 1080p que en realidad era 720p, un duplicado de peor calidad y tres episodios sin audio en castellano.

Para corregirlo activé las mejoras automáticas del gestor de series, y empezó una reacción en cadena:

```mermaid
flowchart LR
    G["Gestor de series<br/>busca una mejora"] -->|"envía"| N["NZBGet"]
    N -->|"Completion: le falta algo<br/>y la tolerancia es 0 %"| R["Rechazada"]
    R -->|"prueba la siguiente opción,<br/>de peor calidad"| G
```

La extensión *Completion*, con tolerancia 0 % a artículos que faltan, rechazaba cada descarga, y el gestor pasaba a la siguiente opción, cada vez peor, hasta bajar a 720p. **17 versiones rechazadas en unos 15 minutos.** Lo saqué cruzando tres historiales: el log de NZBGet, el del gestor de series y el de Prowlarr.

De paso salieron tres riesgos que no tenía vistos:

- **El perfil colocaba 720p por encima de 1080p HDTV**, así que una "mejora" podía bajar la resolución.
- **El formato de idioma daba falsos positivos** con los lanzamientos multiidioma: un archivo marcado como "multi" solo tenía francés e inglés.
- **La papelera del gestor de series estaba desactivada:** cualquier sustitución borraba el archivo anterior sin vuelta atrás.

**Solución:** papelera activada (30 días) y perfil reordenado para que una mejora nunca baje de 1080p a 720p. No se perdió ningún archivo. Queda pendiente desactivar el reintento automático de descargas fallidas, para cortar la cadena de raíz.

**Idea para más adelante:** pasar el audio en castellano de los archivos que ya tengo al vídeo de mejor calidad. Con 23,976 fps y una pista de referencia es viable; con 25 fps habría que reajustar el audio un 4 %, y eso ya es otra historia.

## Archivos que el gestor de series no veía (2-nov)

Dos temporadas completas, descargadas con qBittorrent en una categoría que el gestor de series no sigue, no aparecían en la biblioteca. Las importé a mano **con hardlinks**: 12 episodios añadidos sin ocupar espacio extra, y los torrents siguen compartiéndose.

Aprovechando, tres episodios que solo tenían audio en francés se sustituyeron por versiones en castellano. Las antiguas (14,4 GB) quedaron en la papelera, por si acaso, durante 30 días.

## Proteger lo que se está compartiendo (2-nov)

**El problema:** 8 torrents (103 archivos, 125 GB) se compartían desde dentro de la propia biblioteca, sin ninguna otra copia. Una mejora automática del gestor de series habría borrado esos archivos y dejado los torrents en error.

**La solución:** sacar los torrents de la biblioteca sin mover un solo byte en el disco. Un script, para cada torrent:

1. Lo traslada fuera de la biblioteca **desde el propio qBittorrent**, para que el cliente sepa en todo momento dónde están sus archivos.
2. Deja un **hardlink** en la ruta original de la biblioteca.
3. Antes de enlazar, comprueba **inodo y tamaño**, y se detiene ante cualquier diferencia.

```mermaid
flowchart LR
    QB["qBittorrent"] --> T["/data/torrents/…"]
    GS["Gestor de series<br/>y Jellyfin"] --> M["/data/media/…"]
    T --- D[("Los mismos datos en disco<br/>(mismo inodo)")]
    M --- D
```

Lo probé primero con un torrent y, cuando vi que todo cuadraba, con los otros 7. Ningún error.

- Los 8 torrents siguen compartiéndose con normalidad.
- La biblioteca, el gestor de series y Jellyfin ven exactamente los mismos archivos: comprobado en su base de datos, temporada a temporada.
- **El gestor ya no puede borrar lo que se comparte:** si sustituye un archivo, solo elimina su propio enlace.
- **Espacio extra: 0 bytes.**
