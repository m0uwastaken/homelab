# Registro de cambios

[← Volver al README](README.md)

Cómo ha ido evolucionando el homelab, de lo más reciente a lo más antiguo. Las versiones anteriores de cada parte siguen descritas aquí aunque ya se hayan sustituido.

## 3 de noviembre de 2026: CrowdSec, a prueba

- **Consola web de CrowdSec** (plan gratuito): el servidor inscrito; hizo falta reiniciar CrowdSec para que empezara a sincronizar. Una alerta de prueba con una IP de documentación llegó a la consola.
- **Tráfico normal:** las visitas desde el móvil con datos se analizaron sin saltar ninguna alerta.
- **Escáner externo** contra mi propio dominio: 51 rutas en 53 segundos, todas con 403 por el filtro por país, y aun así CrowdSec marcó la IP como maliciosa (5 alertas, 4 decisiones). Más en [Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md#consola-web-y-prueba-con-un-escáner-3-nov).

## 2 de noviembre de 2026: CrowdSec, socket de Docker y una reacción en cadena

- **Primera noche completa de copias, todas correctas:** configuraciones (311 MB, 0 fallos), fotos (8,3 GB sin miniaturas, con el volcado de su base de datos), archivos (867 MB, con el suyo) y la copia cifrada, ya con 2 copias. El disco de copias, sin desconexiones ese día. Más en [Copias de seguridad](docs/12-copias-de-seguridad.md#la-primera-noche-completa-2-nov).
- **CrowdSec:** Caddy escribe sus accesos en JSON (rotando a 20 MB × 5); CrowdSec 1.8.2 los analiza junto con los registros de Jellyfin, y Caddy se recompila con el módulo de CrowdSec, con versiones fijadas (2.11.7 y v0.14.1). Si CrowdSec se cae, el tráfico sigue pasando. Probado con una IP bloqueada a propósito (403) y desbloqueada (200). Añadido a Homarr. Más en [Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md#crowdsec-bloqueo-automático-2-nov).
- **docker-socket-proxy:** Beszel deja de montar el socket de Docker y pasa a leer a través de un proxy local que solo permite consultas; crear, reiniciar, borrar o ejecutar devuelve 403.
- **Una reacción en cadena en las descargas:** al activar las mejoras automáticas, *Completion* (tolerancia 0 %) rechazaba cada descarga y el gestor de series bajaba de calidad en cada intento: 17 rechazos en ~15 minutos. Perfil reordenado para no bajar nunca de 1080p a 720p y papelera de 30 días activada. No se perdió nada. Más en [Descarga de ISOs](docs/01-descarga-de-isos.md#mejoras-automáticas-una-reacción-en-cadena-2-nov).
- **Importación manual con hardlinks** de 12 episodios que el gestor de series no veía, y tres episodios sin audio en castellano sustituidos (los antiguos, 30 días en la papelera).
- **8 torrents (125 GB) protegidos:** sacados de la biblioteca con un script que deja un hardlink en su sitio y comprueba inodo y tamaño antes de enlazar. 0 bytes extra.

## 1 de noviembre de 2026: Copias fuera de casa, fotos y archivos

- **Copia cifrada fuera de casa** de las configuraciones con restic y rclone: cada noche, 7 diarias y 4 semanales, verificación de una muestra del 5 % los domingos y acceso a la nube limitado a sus propios archivos. Autorizada a mano con `curl` (el servidor no tiene navegador). Más en [Copias de seguridad](docs/12-copias-de-seguridad.md).
- **Immich** (fotos) y **Nextcloud** (archivos), solo por Tailscale; datos en el disco grande, bases de datos en el SSD fuera de los volúmenes; copias diarias al disco de copias con volcados de base de datos. Más en [Fotos y archivos](docs/13-fotos-y-archivos.md).
- **Disco de copias inestable:** dos desconexiones USB. Vigilancia cada minuto con aviso al móvil, Beszel por identificador y copias que no se ejecutan sin el disco montado. Causa aún sin resolver.
- **Jellyfin en la tele LG:** no cargaba; se retiró el plugin Home Screen Sections. Más en [Reproducción multimedia](docs/02-reproduccion-multimedia.md#la-tele-lg-que-dejó-de-cargar-1-nov).
- **Comprobado:** la primera regeneración automática de la lista de IPs de España (4.326 rangos) recargó Caddy sin cortes.

## 31 de octubre de 2026: Asentar el servidor

- **RAM ampliada a 16 GB** (2 × 8 GB DDR4-2400 en doble canal), probada con el diagnóstico de Dell y `memtester`.
- **Discos en su sitio definitivo:** SSD en la bahía óptica, datos por SATA y copias por USB, con la prueba SMART larga del disco de copias superada.
- **Conflicto de IP en Docker** que tumbaba Caddy tras un reinicio: resuelto separando el rango automático de las IPs fijas.
- **46,4 GiB recuperados** volviendo a unir con `jdupes` los hardlinks que perdió la copia, tras medir en solo lectura y probar las opciones en una carpeta temporal.
- **Copia diaria** movida al disco de copias, comprobada con `diff`.
- **Claude Code en el servidor** con Remote Control como servicio de systemd, `CLAUDE.md`, lista de tareas y sin acceso a los archivos de secretos. Más en [Servidor](docs/08-servidor-nuevo.md#claude-code-en-el-servidor).
- **Panel de descargas:** las X rojas falsas, arregladas. **Jellyfin:** tone mapping activado.

## 30–31 de octubre de 2026: Mudanza al OptiPlex

El homelab deja el PC con Windows y pasa a un servidor dedicado. **Sobre el papel es un paso atrás en hardware, pero rinde mucho mejor:** las descargas por Usenet pasan de ~10 a **84,9 MB/s**, el vídeo se transcodifica con la gráfica integrada y los mismos servicios caben con holgura en 8 GB. Y, después de una semana con el PC personal encendido día y noche sufriendo con todos los contenedores, por fin lo puedo apagar. Más en [Servidor](docs/08-servidor-nuevo.md).

- **Preparación (30-oct):** USB de Ubuntu Server 26.04.1 con la huella de la ISO comprobada, llegada del OptiPlex e instalación en el SSD sin LVM.
- **Mudanza (30–31 oct):** `instalar-servidor.sh`; 391 GB copiados sin errores (125 MB/s, 56 min); exportación final en Windows; el servidor hereda la IP fija del PC; `restaurar.sh` con 17 configuraciones y **21 contenedores** arriba; el servidor pasa a ser `homelab` en Tailscale y el PC, `pc`.
- **Comprobaciones:** Homarr en verde, Jellyfin desde fuera, avisos al móvil y **Quick Sync** funcionando (gráfica al ~34 % con una transcodificación forzada).
- **Ajustes de la mudanza:** zona horaria, servicio de Caddy de Windows parado a mano, cortafuegos abierto a las redes de Docker (también en el kit), huella del agente de Beszel, versión de qBittorrent fijada y caché de NZBGet ajustada a 8 GB. Más en [Lo aprendido](docs/08-servidor-nuevo.md#lo-que-aprendí-en-la-mudanza).
- **Seguridad:** contraseñas de todos los servicios aleatorias otra vez y en KeePassXC; administrador de Jellyfin sin acceso remoto; el PC con IP fija y su agente de Beszel abierto solo al servidor.
- **AdGuard Home** como DNS, con HaGeZi Pro y DoH hacia fuera, publicado solo en la IP de casa del servidor para convivir con el DNS de Ubuntu y con Tailscale. El router del operador no deja repartirlo, así que se configura en cada aparato. Más en [AdGuard Home](docs/11-dns-adguard-home.md).
- **Homepage retirado:** Homarr queda como único panel.
- **Provisional:** el disco de datos va por USB hasta que llegue el adaptador para la bahía óptica; el disco de copias aún no está montado.

## 29 de octubre de 2026: Acceso privado con Tailscale

- **Todo el homelab menos Jellyfin por Tailscale**, instalado en el sistema (no en Docker), con el nombre fijo `homelab` y una regla de firewall que solo admite el rango de Tailscale. Hasta ahora, todo lo que no fuera Jellyfin solo se podía usar desde casa. Más en [Tailscale](docs/09-acceso-privado-tailscale.md).
- Los enlaces del panel, de los avisos y de las alertas de Beszel pasan de la IP de casa al nombre de Tailscale.
- **Kit de mudanza:** el servidor instala Tailscale con SSH y toma el nombre `homelab` al restaurar; el PC con Windows pasa a llamarse `pc`.

## 28 de octubre de 2026: TV en directo y la prueba del disco

- **TV en directo** en Jellyfin con un sintonizador nativo y guía, sin publicar las listas (llevan credenciales). Más en [Reproducción multimedia](docs/02-reproduccion-multimedia.md#tv-en-directo).
- **Prueba con NZBGet nativo en Windows:** fuera de Docker Desktop y en el mismo disco, la velocidad pasa de ~10 a 15–25 MB/s. Confirma que el freno es la capa de Docker y el disco SMR, no el PC. Más en [Servidor nuevo](docs/08-servidor-nuevo.md#la-prueba-28-oct).
- **Homarr:** diagnosticado el `ETIMEDOUT` de las notificaciones (timeout de 250 ms de Node con la latencia de WSL2) y su arreglo con `NODE_OPTIONS`.

## 27 de octubre de 2026: Monitorización, contraseñas y alarma

- **Añadido Beszel** para monitorizar recursos: hub en Docker, agente para los contenedores y agente nativo en Windows, con alertas por ntfy y widgets en Homarr. Más en [Monitorización](docs/07-monitorizacion-beszel.md).
- **Tablero de Homarr terminado:** tema "aurora / glass", diseño base de 1200 px y 14 columnas, paneles de descargas arriba, estado de las máquinas, notificaciones, indexadores, reproducciones y lista de apps con puntos de estado. Más en [Homarr](docs/05-panel-homarr.md).
- **Indexadores reorganizados:** fuera los duplicados y los que fallaban en cada búsqueda, detectados con el widget de Homarr.
- **Kit de mudanza:** Beszel incluido; el script de restauración detecta los discos del servidor para la salud SMART.
- **Contraseñas:** todas las de los servicios cambiadas por otras únicas en **KeePassXC**, con un mapa de dónde vive cada una; borradas las copias `.bak` que aún tenían contraseñas. Más en [Contraseñas](docs/03-acceso-remoto-y-seguridad.md#contraseñas-keepassxc).
- **Plan de seguridad por fases** (CrowdSec, docker-socket-proxy, copia fuera de casa, red separada). Más en [Plan](docs/03-acceso-remoto-y-seguridad.md#plan-de-seguridad-por-fases).
- **Añadido Home Assistant** con HACS y la integración **Verisure OWA** para la alarma de casa: panel con alarma, cámaras e historial; usuario propio, 2FA y nunca publicado. Más en [Domótica](docs/10-domotica-home-assistant.md).
- **Documentación:** explicado por qué pasar del PC al OptiPlex no es un paso atrás aunque tenga menos CPU y RAM: el acceso a disco a través de Docker Desktop y que el PC es mi ordenador personal. Más en [Servidor nuevo](docs/08-servidor-nuevo.md#por-qué-no-es-un-paso-atrás).

## 26 de octubre de 2026: Homarr sustituye a Homepage

- **Añadido Homarr v2** como panel principal (puerto 7575). Homepage, el panel usado hasta entonces, sigue funcionando en paralelo.
- **Paneles propios de descargas** (`/nzb` y `/torrents`) en el servicio de avisos, pensados para meterlos en Homarr como iFrame, con fondo transparente y estilo cristal.
- **Arreglados los avisos** de qBittorrent y NZBGet, que llevaban días sin llegar: cambio en el inicio de sesión de qBittorrent 5.2 y dos líneas estropeadas en el script. Más en [Avisos](docs/06-avisos-y-paneles.md#arreglos-del-26-de-octubre).
- Las notificaciones del móvil abren Homarr en lugar de Homepage.
- **Kit de mudanza:** exporta también la configuración de Homarr.
- **Incidente:** borrado accidental de los archivos sueltos de la carpeta del homelab; recuperados los compose, perdidos algunos scripts antiguos.

## 25 de octubre de 2026: Primera versión de esta documentación

Estado del homelab en ese momento:

- **Pila completa en Docker Desktop (Windows):** gestores de biblioteca, Prowlarr y auxiliares, clientes de descarga (Usenet y BitTorrent), Decluttarr, Jellyfin, Seerr, Bazarr y Questarr.
- **Panel:** Homepage, con un widget por servicio y la página "Actividad" incrustada.
- **Avisos al móvil** por ntfy y página "Actividad", en un servicio propio en Python.
- **Acceso remoto** a Jellyfin con Caddy y DuckDNS: solo España, rutas de administración y de bots bloqueadas, alertas de seguridad. Caddy pasó de Docker a servicio de Windows para ver la IP real.
- **Subtítulos** sincronizados automáticamente con Bazarr y ffsubsync, con un script para resincronizar en lote.
- **Servidor nuevo** (Dell OptiPlex 7060 SFF) comprado, discos revisados con SMART y kit de mudanza probado.

## Antes del 25 de octubre de 2026: Puesta en marcha

- **Mediados de octubre:** primera versión de la pila en Docker Desktop. Problemas de rutas (`/downloads` frente a `/data`), del puerto del cliente de BitTorrent y de configuración de Decluttarr.
- **Jellyfin publicado en internet**, primero con Caddy dentro de Docker. Al revisarlo se vio que internet parecía la red de casa y se cerró con reglas en Caddy y las redes de Jellyfin.
- **22 de octubre:** disco C: casi lleno por el disco virtual de Docker (54 GB recuperados), límite de RAM para WSL2 y primera regla de Decluttarr desactivada.
- **23 de octubre:** kit de mudanza probado en el PC real (14 volúmenes, 207 MB).
- **24 de octubre:** Jellyfin con aspecto de plataforma de streaming (plugins y tema), con la compatibilidad revisada aparato por aparato; revisión SMART de los discos del servidor nuevo.
- **Parche** en la extensión *Completion* de NZBGet, que revisaba el archivo equivocado y estropeaba descargas buenas.
