# Mi Homelab

![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white)
![Ubuntu Server](https://img.shields.io/badge/Ubuntu_Server-26.04.1_LTS-E95420?logo=ubuntu&logoColor=white)
![Jellyfin](https://img.shields.io/badge/Jellyfin-12.2-00A4DC?logo=jellyfin&logoColor=white)
![Homarr](https://img.shields.io/badge/Homarr-v2-FA5252)
![Beszel](https://img.shields.io/badge/Beszel-monitorizaci%C3%B3n-2F9E44)
![Caddy](https://img.shields.io/badge/Caddy-2-1F88C0?logo=caddy&logoColor=white)
![CrowdSec](https://img.shields.io/badge/CrowdSec-bloqueo_de_IPs-6E3FD3)
![Tailscale](https://img.shields.io/badge/Tailscale-VPN-242424?logo=tailscale&logoColor=white)
![AdGuard Home](https://img.shields.io/badge/AdGuard_Home-DNS-68BC71?logo=adguard&logoColor=white)
![Home Assistant](https://img.shields.io/badge/Home_Assistant-dom%C3%B3tica-18BCF2?logo=homeassistant&logoColor=white)
![Immich](https://img.shields.io/badge/Immich-fotos-4250AF?logo=immich&logoColor=white)
![Nextcloud](https://img.shields.io/badge/Nextcloud-archivos-0082C9?logo=nextcloud&logoColor=white)
![restic](https://img.shields.io/badge/restic-copias_cifradas-1F2937)
![Python](https://img.shields.io/badge/Python-stdlib-3776AB?logo=python&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-scripts-5391FE?logo=powershell&logoColor=white)

Documentación de mi homelab: un servidor en casa, montado en buena parte con hardware reciclado.

<p align="center"><a href="docs/05-panel-homarr.md"><img src="images/homarr-escritorio.png" alt="Tablero de Homarr: el panel central del homelab"></a></p>

<p align="center"><b>El panel central del homelab, en Homarr.</b> Las dos máquinas, los bloqueos de CrowdSec, las notificaciones, las gráficas del servidor y todas las apps con su punto de estado, en una sola pantalla.<br><sub>Las alertas de seguridad son de mis propias pruebas con escáneres web. Algunos datos están tapados.</sub></p>

## Por qué lo monté

Estaba harto de que Gmail y Google Drive me avisaran cada dos por tres de que me quedaba sin almacenamiento, y de que HBO Max me metiera anuncios en mitad de las series. Además, tenía varios discos duros de ordenadores antiguos cogiendo polvo y quería darles una segunda vida.

Así que monté mi propio servidor: mis fotos y archivos en casa, mi biblioteca multimedia sin anuncios y, de paso, un sitio donde aprender Linux, Docker, redes y seguridad. Hoy tiene **Jellyfin** para ver mi biblioteca en las teles de casa (y, de forma segura, también fuera), un panel central, monitorización, acceso privado por VPN, DNS con bloqueo de anuncios, domótica con la alarma de casa integrada, fotos y archivos propios y copias de seguridad dentro y fuera de casa.

Empezó como una prueba en Docker Desktop sobre mi PC con Windows y desde el 31 de octubre de 2026 corre en un servidor dedicado: un Dell OptiPlex 7060 SFF de segunda mano con Ubuntu Server y los discos que tenía por casa. La idea es ir mejorándolo poco a poco.

## Estado

| Fase | Qué | Estado |
|---|---|---|
| 0 | Servicios en Docker Desktop (Windows) | ✅ Cumplió su papel; retirada el 30-oct |
| 1 | Servidor dedicado: OptiPlex + Ubuntu Server + Docker | ✅ En marcha desde el 31-oct: discos en su sitio, RAM ampliada a 16 GB y 29 contenedores |
| 2 | NAS, archivos personales y más servicios | Idea |

## Menos hardware, más rendimiento

Sobre el papel, la mudanza fue un *downgrade*: de un Ryzen 5 5600 con 16 GB a un i5-8500 de 2018 con 8 GB. En la práctica, **el homelab rinde mucho mejor y aprovecha cada recurso**, porque el límite nunca fue el procesador, sino la capa de Docker Desktop, un disco SMR y compartir máquina con mi uso diario.

| | PC con Windows | OptiPlex con Ubuntu |
|---|---|---|
| Velocidad de descarga | ~10 MB/s | **84,9 MB/s**: más de 8 veces más rápido, cerca del máximo de la fibra |
| Transcodificar vídeo | Con el procesador | Con la gráfica integrada (Quick Sync): el procesador, libre |
| Archivos nuevos en Jellyfin | Al siguiente escaneo | Al momento |
| Disponibilidad | Mientras el PC estuviera encendido | 24/7, en una máquina dedicada |
| Recursos | 21 contenedores con el PC al 83 % de RAM | 21 contenedores cabían en 8 GB; hoy **29 contenedores usan solo 4,6 GiB** de los 16 GB a los que amplié la RAM |

> **Y lo mejor no sale en la tabla:** después de una semana con mi ordenador personal encendido día y noche, sufriendo con todos los contenedores a cuestas, por fin lo puedo apagar.

Los porqués, la prueba previa y cómo fue la mudanza: más en [Servidor](docs/08-servidor-nuevo.md#por-qué-no-es-un-paso-atrás).

### Novedades (2 y 3 de noviembre de 2026)

- **[CrowdSec](docs/03-acceso-remoto-y-seguridad.md#crowdsec-bloqueo-automático-2-nov):** Caddy bloquea automáticamente las IPs que atacan. Lo probé lanzando un escáner contra mi propio dominio: el filtro por país lo paró en la puerta y CrowdSec lo marcó como malicioso.
- **[El socket de Docker, en solo lectura](docs/03-acceso-remoto-y-seguridad.md#el-socket-de-docker-en-solo-lectura-2-nov):** Beszel ve los contenedores a través de un proxy, pero ya no podría tocarlos.
- **[Homarr reorganizado](docs/05-panel-homarr.md#cómo-está-ahora-3-nov)** con un panel de CrowdSec: las alertas de la captura las provoqué yo con escáneres web contra mi propio dominio.
- **[La primera noche completa de copias](docs/12-copias-de-seguridad.md#la-primera-noche-completa-2-nov):** configuraciones, fotos, archivos y la copia cifrada, todas correctas.

Del 31 de octubre y el 1 de noviembre: el [servidor asentado](docs/08-servidor-nuevo.md#después-de-la-mudanza-asentar-el-servidor-31-oct) (RAM a 16 GB y 46,4 GiB recuperados al volver a unir los hardlinks), [copias de seguridad](docs/12-copias-de-seguridad.md) dentro y fuera de casa, [fotos y archivos](docs/13-fotos-y-archivos.md) con Immich y Nextcloud, [Claude Code en el servidor](docs/08-servidor-nuevo.md#claude-code-en-el-servidor) y [la tele LG](docs/02-reproduccion-multimedia.md#la-tele-lg-que-dejó-de-cargar-1-nov), que había dejado de cargar Jellyfin, arreglada.

Del 30–31 de octubre: la [mudanza al OptiPlex](docs/08-servidor-nuevo.md#la-mudanza-3031-de-octubre), Quick Sync en Jellyfin, [AdGuard Home](docs/11-dns-adguard-home.md) y Homepage retirado.

El historial completo, con cómo era cada parte antes, está en el [registro de cambios](CHANGELOG.md).

_Última actualización: 3 de noviembre de 2026._

## Índice

- [Arquitectura](#arquitectura)
  - [Red](#red)
  - [Acceso desde fuera de casa: dos puertas](#acceso-desde-fuera-de-casa-dos-puertas)
- [Hardware](#hardware)
- [Servicios](#servicios)
- [Jellyfin](#jellyfin-reproducción-multimedia)
- [Panel: Homarr](#panel-homarr)
- [Monitorización: Beszel](#monitorización-beszel)
- [Avisos al móvil](#avisos-al-móvil)
- [Acceso privado: Tailscale](#acceso-privado-tailscale)
- [DNS: AdGuard Home](#dns-adguard-home)
- [Fotos y archivos](#fotos-y-archivos)
- [Domótica: Home Assistant](#domótica-home-assistant)
- [Seguridad](#seguridad)
- [Copias de seguridad](#copias-de-seguridad)
- [Problemas resueltos](#problemas-resueltos)
- [Próximos pasos](#próximos-pasos)
- [Documentación detallada](#documentación-detallada)

---

## Arquitectura

### Red

```mermaid
flowchart LR
    ISP["Fibra 1 Gb"] --> LB["Router del operador"]
    LB -->|"cable"| SW["Switch gigabit"]
    subgraph CABLE["Por cable, en el switch"]
        OPT["Servidor<br/>Dell OptiPlex 7060 SFF<br/>Ubuntu Server + Docker"]
        PC["PC con Windows<br/>(uso personal)"]
        LAP["Portátil"]
    end
    SW --> OPT
    SW --> PC
    SW --> LAP
    LB -.-> CLI["Resto de aparatos<br/>(Wi-Fi o cable al router)"]
    CLI --- TV1["Tele LG (webOS)"]
    CLI --- TV2["Android TV /<br/>Chromecast con Google TV"]
    CLI --- MOV["Móvil y tablet"]
```

| Elemento | Detalle |
|---|---|
| Router | Router del operador, fibra de 1 Gb |
| Red de casa | `192.168.1.0/24`. El servidor, el PC y el portátil van por cable al mismo switch gigabit, así que entre ellos van a velocidad completa aunque internet esté ocupado. El servidor tiene la misma IP fija que antes tenía el PC, y el PC otra IP fija |
| Red interna de Docker | `172.30.0.0/24`, con Caddy en una IP fija para que Jellyfin lo reconozca como proxy |
| Puertos abiertos | El **443** (Jellyfin a través de Caddy) y uno más para descargas. Tailscale no necesita ninguno |
| VPN | Tailscale (WireGuard) con MagicDNS: el servidor es `homelab.<mi-red>.ts.net` |
| DNS | AdGuard Home en el servidor, configurado a mano en cada aparato (el router del operador no deja repartirlo) |
| DNS dinámico | DuckDNS, porque el operador puede cambiar la IP pública |

### Acceso desde fuera de casa: dos puertas

- **Pública:** solo Jellyfin, publicado con Caddy y con filtros, para que la familia lo vea sin instalar nada.
- **Privada:** todo lo demás (Homarr, Beszel, AdGuard, Home Assistant, fotos y archivos…), solo por **Tailscale**, sin abrir ningún puerto más.

```mermaid
flowchart LR
    FAM["Familia"] -->|"HTTPS 443"| CADDY["Caddy<br/>HTTPS automático"]
    CADDY -->|"España, uso normal"| JF["Jellyfin"]
    CADDY -->|"otros países, admin, bots,<br/>IPs bloqueadas por CrowdSec"| X1["403 / 404"]
    YO["Mis aparatos<br/>con Tailscale"] -->|"WireGuard"| TS["Tailscale<br/>«homelab»"]
    TS --> APPS["Homarr · Beszel · AdGuard<br/>Home Assistant · fotos y archivos"]
    NET["Resto de internet"] -.->|"sin puertos"| APPS
```

Más detalle en [Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md) y [Acceso privado: Tailscale](docs/09-acceso-privado-tailscale.md).

---

## Hardware

### Servidor (desde el 31-oct)

<p align="center"><img src="images/optiplex-interior.jpg" alt="El OptiPlex por dentro" width="420"></p>

| Componente | Especificación |
|---|---|
| Equipo | Dell OptiPlex 7060 SFF, de segunda mano |
| CPU | Intel Core i5-8500 (6 núcleos) con gráfica UHD 630 → **Quick Sync** para transcodificar en Jellyfin |
| RAM | 16 GB DDR4-2400 en doble canal (ampliada el 31-oct desde 8 GB; 4 ranuras, hasta 64 GB) |
| Sistema | SSD SATA 500 GB (Crucial MX500): sistema, configuraciones y carpeta temporal de descargas |
| Datos | HDD 1 TB CMR (Toshiba DT01ACA100) por SATA: biblioteca, descargas, fotos y archivos (396 GB usados de 916) |
| Copias | HDD 1 TB (WD WD10EZEX) por USB: copia diaria de configuraciones, fotos y archivos |
| Red | Gigabit por cable al switch |
| Sistema operativo | Ubuntu Server 26.04.1 LTS + Docker |

Antes de usarlos, revisé la salud de los tres discos con SMART y repartí los papeles según el resultado: el disco con el historial más dudoso es el de copias, para que si falla solo se pierda la copia y no la biblioteca. Detalle en [Servidor](docs/08-servidor-nuevo.md).

### Servidor anterior: mi PC (hasta el 30-oct)

| Componente | Especificación |
|---|---|
| Equipo | PC de sobremesa con Windows, también de uso diario |
| CPU | AMD Ryzen 5 5600 (6 núcleos / 12 hilos) |
| RAM | 16 GB |
| Datos | HDD de 2 TB (SMR) con la biblioteca y las descargas |
| Contenedores | Docker Desktop sobre WSL2 |

### Aparatos que lo usan

| Aparato | Uso |
|---|---|
| Tele LG (webOS, modelo antiguo) | App de Jellyfin de LG Content Store |
| Android TV / Chromecast con Google TV | App nativa de Jellyfin |
| PC, iPhone e iPad | Jellyfin en navegador o app; Homarr como página de inicio; AdGuard como DNS |
| Móvil | Tailscale siempre activo; Homarr en la pantalla de inicio |

---

## Servicios

Todo corre en contenedores con Docker Compose, salvo Tailscale, que va instalado en el sistema. Lo que pone "Casa" se usa también desde fuera **por Tailscale**.

| Área | Servicio | Para qué | Acceso |
|---|---|---|---|
| Reproducción multimedia | **Jellyfin** | Streaming a teles y móviles, con transcodificación por hardware | Casa + internet (vía Caddy) |
| | **Bazarr** | Subtítulos automáticos y sincronizados con el vídeo | Casa |
| Descarga de ISOs | Gestores de biblioteca, indexadores y clientes de descarga | Descarga automatizada y biblioteca ordenada. Detalle en [su página](docs/01-descarga-de-isos.md) | Casa |
| Panel y monitorización | **Homarr** | Panel principal (Homepage, el anterior, retirado el 31-oct) | Casa |
| | **Beszel** | Monitorización de recursos y alertas, del servidor y del PC | Casa |
| | **docker-socket-proxy** | Acceso de solo lectura a Docker, para que Beszel no necesite el socket | Solo el propio servidor |
| | **avisos** (propio) | Notificaciones por ntfy y paneles propios para Homarr | Casa |
| Red | **AdGuard Home** | DNS con bloqueo de anuncios y rastreadores, DoH hacia fuera | Casa |
| Personal | **Immich** | Fotos, con subida automática desde el móvil | Solo Tailscale |
| | **Nextcloud** | Archivos | Solo Tailscale |
| Copias | **restic + rclone** | Copia cifrada fuera de casa | — |
| Domótica | **Home Assistant** | Alarma de casa (estado, armado, fotos, historial) | Casa, nunca publicado |
| Acceso remoto | **Caddy** | Proxy inverso con HTTPS automático y el módulo de CrowdSec; publica solo Jellyfin | Internet (443) |
| | **CrowdSec** | Detecta ataques en los registros de Caddy y Jellyfin y bloquea esas IPs en Caddy | Solo interno |
| | **DuckDNS** | Mantiene el dominio apuntando a la IP de casa | Solo interno |
| | **Tailscale** | VPN para todo lo demás; instalado en el sistema, no en Docker | Mis aparatos |

---

## Jellyfin: reproducción multimedia

<p align="center"><img src="images/jellyfin-login.png" alt="Inicio de sesión de Jellyfin, con los usuarios ocultos" width="640"></p>

Jellyfin es la parte del homelab que usa toda la casa: la biblioteca multimedia en las teles, los móviles y el navegador, sin anuncios. Y es **el único servicio publicado en internet**, para que la familia pueda verlo desde su casa sin instalar nada. Por eso toda la seguridad de la puerta pública (Caddy, el filtro por país y CrowdSec) existe para protegerlo a él.

```mermaid
flowchart LR
    BIB[("Biblioteca<br/>disco de datos")] --> JF["Jellyfin<br/>Quick Sync"]
    BAZ["Bazarr<br/>subtítulos"] --> BIB
    JF --> CASA["Teles y móviles<br/>en casa"]
    JF --> CADDY["Caddy<br/>+ CrowdSec"]
    CADDY --> FAM["Familia,<br/>desde fuera"]
```

**Cómo se ve y dónde funciona**

- **Aspecto de plataforma de streaming:** carrusel de portada (**Media Bar**), etiquetas de calidad e idioma en los pósters (**Jellyfin Enhanced**), botón para **saltar intros y créditos** (**Intro Skipper**) y el tema **Jellyfish**.
- **Probado aparato por aparato** antes de activarlo en todas partes: una tele LG antigua (a la que hubo que quitarle un plugin el 1-nov), Android TV, Chromecast, móviles y navegador.
- **Transcodificación por hardware con Quick Sync** y tone mapping desde la mudanza: el procesador queda libre.
- **TV en directo** con guía de programación.
- **Subtítulos con Bazarr**, que los descarga y los **sincroniza solos** con ffsubsync. Más en [Subtítulos con Bazarr](docs/04-subtitulos-bazarr.md).

**Cómo está protegido**

- **Usuarios:** el administrador no puede entrar desde internet, todos los usuarios están ocultos en la pantalla de inicio de sesión y el usuario externo tiene permisos mínimos y bloqueo tras 5 intentos fallidos.
- **Caddy delante:** solo conexiones desde España, rutas de administración cerradas desde fuera y rutas típicas de bots cortadas.
- **CrowdSec** lee los accesos de Caddy y los inicios de sesión de Jellyfin, y bloquea las IPs que escanean, buscan fallos o prueban contraseñas. Lo probé con escáneres web contra mi propio dominio: son las alertas que se ven en el panel de arriba.
- **Avisos al móvil** de intentos fallidos, usuarios bloqueados y dispositivos nuevos.

La biblioteca se alimenta de la [descarga de ISOs](docs/01-descarga-de-isos.md), que tiene su propia página. Más sobre Jellyfin en [Reproducción multimedia](docs/02-reproduccion-multimedia.md), y sobre su seguridad en [Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md).

---

## Panel: Homarr

Es la página de inicio de mi navegador y la [captura de arriba del todo](#mi-homelab). De un vistazo:

| Zona | Qué enseña |
|---|---|
| Columna izquierda | Todas las apps por categorías, cada una con su punto de estado |
| Máquinas | PC y servidor con Beszel: CPU, memoria, disco, temperatura y tiempo encendido |
| Seguridad | Panel de CrowdSec: alertas de 24 h y 7 días, IPs bloqueadas ahora y últimas alertas en castellano |
| Notificaciones | Lo mismo que llega al móvil por ntfy |
| Gráficas | CPU, memoria, disco, red y uso por contenedor del servidor |
| Jellyfin | Quién está viendo algo y en qué aparato |

Desde el 26 de octubre, **Homarr v2** es la página de inicio del homelab, y desde el 31 el único panel: **Homepage**, que se configuraba con archivos YAML, quedó retirado tras la mudanza. Homarr se configura desde la propia web, guarda las credenciales cifradas y permite un diseño distinto para PC y para móvil.

- **Integraciones por nombre de contenedor** (`http://jellyfin:8096`…): sobrevivieron a la mudanza sin tocar nada. Las pocas que usan la IP del servidor necesitaron abrir el cortafuegos a las redes de Docker.
- **Tablero** con tema "aurora / glass" y CSS propio para que todo, incluidos widgets de la comunidad, tenga el mismo estilo de cristal translúcido.
- **Paneles hechos a medida** (Python) y metidos como iFrame, con fondo transparente, porque algunos widgets de serie se quedaban cortos.
- **Estado de todo de un vistazo:** máquinas (Beszel), **alertas y bloqueos de CrowdSec**, notificaciones, reproducciones en curso y, en una columna a la izquierda, todas las apps con su punto de estado.

```mermaid
flowchart LR
    NAV["/ "] --> H["Homarr :7575"]
    H -->|API| APPS["Jellyfin · Bazarr · AdGuard<br/>Immich · Nextcloud"]
    H -->|"solo lectura"| BZ["Beszel"]
    H --> NT["ntfy"]
    H -->|"iFrame ?embed=1"| AV["avisos :3001<br/>paneles propios"]
```

Despliegue, integraciones, diseño del tablero, CSS, paneles propios y lo que aprendí por el camino: más en [Panel principal: Homarr](docs/05-panel-homarr.md).

## Monitorización: Beszel

**Beszel** mide CPU, RAM, red, temperatura, discos (con **SMART**) y **cada contenedor por separado**, guarda historial y avisa al móvil por ntfy. Se mudó al servidor con su historial: ahora vigila el **servidor** con su propio agente y el **PC** con un agente nativo de Windows, que solo acepta conexiones del servidor. Más en [Monitorización: Beszel](docs/07-monitorizacion-beszel.md).

## Avisos al móvil

Un servicio **en Python sin dependencias** envía notificaciones al móvil por **ntfy**, sobre todo **alertas de seguridad de Jellyfin** (intentos fallidos, usuarios bloqueados, dispositivos nuevos), y genera los paneles propios de Homarr. Más en [Avisos y paneles propios](docs/06-avisos-y-paneles.md).

## Acceso privado: Tailscale

Desde el 29 de octubre, todo menos Jellyfin se usa desde fuera de casa por **Tailscale**:

- **Instalado en el sistema**, no en Docker ni con un nodo por app: una sola máquina, `homelab`, da acceso a todos los puertos. Sin HTTPS de Tailscale, porque WireGuard ya cifra el tráfico.
- **Mudanza transparente:** el PC pasó a llamarse `pc` y el servidor tomó el nombre `homelab`, así que **ningún enlace cambió**. En el servidor, además, **Tailscale SSH**.
- **Cortafuegos** que solo admite el rango de Tailscale para el acceso remoto.

Más en [Acceso privado: Tailscale](docs/09-acceso-privado-tailscale.md).

## DNS: AdGuard Home

Desde el 31 de octubre, el servidor es también el **DNS** de mis aparatos: bloquea anuncios, rastreadores y dominios maliciosos con la lista **HaGeZi Pro** y resuelve hacia fuera **cifrado** (DoH con Quad9 y Cloudflare). Escucha solo en la IP de casa del servidor, para convivir con el DNS del propio Ubuntu y con Tailscale. El router del operador no permite repartirlo por DHCP, así que de momento se configura aparato por aparato; para toda la casa hará falta un router propio. Más en [DNS: AdGuard Home](docs/11-dns-adguard-home.md).

## Domótica: Home Assistant

**Home Assistant** integra la **alarma de casa** (Verisure / Securitas Direct) con la integración comunitaria **Verisure OWA** (HACS), porque la oficial habla con otra plataforma que no sirve en España: armar y desarmar, fotos de las cámaras bajo demanda, temperatura y humedad, e historial de quién hizo qué. En el servidor va en la red del host, para poder descubrir los aparatos de casa.

Como desde ahí se puede desarmar la alarma, lo trato como la propia alarma: **usuario de Verisure propio** para Home Assistant, contraseña única y **verificación en dos pasos**, y **nunca publicado en internet**, solo por Tailscale. Más en [Domótica: Home Assistant](docs/10-domotica-home-assistant.md).

---

## Seguridad

- **Solo Jellyfin está expuesto.** El resto de servicios solo es accesible desde casa o por **Tailscale**, sin abrir más puertos.
- **Caddy** como única puerta de entrada: HTTPS automático, solo conexiones desde **España** (4.326 rangos de IP, actualizados cada mes), rutas de administración de Jellyfin bloqueadas desde fuera, rutas típicas de bots (`.php`, `/wp-admin`, `/.env`…) cortadas con 404 y cabeceras de seguridad (HSTS, `nosniff`). En el servidor, Caddy ya ve la IP real de cada visita.
- **Usuarios de Jellyfin:** el administrador no puede entrar desde internet, todos los usuarios están ocultos en la pantalla de inicio de sesión y el usuario externo tiene permisos mínimos y bloqueo tras 5 intentos fallidos.
- **Alertas al móvil:** intentos de acceso fallidos, usuarios bloqueados, **dispositivos nuevos** y cambios de usuarios o contraseñas.
- **Contraseñas únicas y aleatorias en KeePassXC** para todos los servicios, renovadas otra vez tras la mudanza.
- **Cortafuegos `ufw`** en el servidor: solo la red de casa, Tailscale y las redes internas de Docker.
- **Fotos y archivos solo por Tailscale**, y **copia cifrada fuera de casa** con acceso mínimo a la nube.
- **Claude Code en el servidor** sin permiso para leer los archivos de secretos, y sus comandos revisados antes de aprobarlos.
- **Home Assistant** con 2FA y nunca publicado, porque desde él se puede desarmar la alarma.
- **CrowdSec** analiza los accesos de Caddy y los registros de Jellyfin (escaneos, vulnerabilidades web conocidas, fuerza bruta en el inicio de sesión, enumeración de usuarios) y bloquea en Caddy las IPs que atacan. Si CrowdSec se cae, Jellyfin sigue funcionando. Probado con un escáner externo contra mi propio dominio. Más en [CrowdSec](docs/03-acceso-remoto-y-seguridad.md#crowdsec-bloqueo-automático-2-nov).
- **El socket de Docker, en solo lectura:** Beszel ya no lo monta; lo consulta a través de un docker-socket-proxy que solo deja leer y que no responde ni desde la red de casa ni desde Tailscale.
- **Plan de seguridad por fases:** la fase 1 (con el servidor) está completa; lo siguiente es una red separada para IoT, cámaras e invitados. Más en [Plan](docs/03-acceso-remoto-y-seguridad.md#plan-de-seguridad-por-fases).
- **Secretos fuera del compose**, en archivos `.env`. Homarr guarda las credenciales de sus integraciones **cifradas**, y tanto Homarr como Beszel usan **usuarios de solo lectura** donde es posible.

> **Lo que descubrí:** con Caddy dentro de Docker en Windows, todo lo que llegaba de internet parecía venir de la red de casa, y Jellyfin permite algunas acciones (como reiniciar o apagar el servidor) a cualquiera que esté "en casa". Lo corregí bloqueando esas rutas en Caddy, definiendo bien las redes de Jellyfin y, después, sacando Caddy de Docker para ver la IP real. En el servidor nuevo, Linux conserva la IP real y Caddy ha vuelto a su contenedor. Más en [Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md).

---

## Fotos y archivos

Desde el 1 de noviembre, **Immich** (fotos, con subida automática desde el móvil) y **Nextcloud** (archivos), los dos **solo por Tailscale**. Los datos van al disco grande y las bases de datos al SSD, fuera de los volúmenes de Docker para no inflar la copia diaria. Más en [Fotos y archivos](docs/13-fotos-y-archivos.md).

---

## Copias de seguridad

| Qué | En casa (disco de copias) | Fuera de casa (cifrada) |
|---|---|---|
| Configuraciones | ✅ Diaria, 14 días (~310 MB) | ✅ restic, 7 diarias + 4 semanales, verificación semanal |
| Fotos y archivos | ✅ Diaria, con volcado de bases de datos | Con el NAS futuro |
| Biblioteca multimedia | ❌ Se puede volver a descargar | ❌ |

- **Un RAID no es una copia:** no protege de un borrado, de un error de un programa o de un ransomware. Por eso, copias con historial.
- **Mínimo privilegio:** la herramienta de copia solo ve en la nube los archivos que crea ella.
- **La primera noche completa (2-nov):** todas las copias automáticas salieron bien, también la cifrada.
- **La mudanza fue la mejor prueba:** las 17 configuraciones exportadas en Windows se restauraron en el servidor sin un solo fallo.
- **Un disco de copias inestable:** se desconecta por USB de vez en cuando. Ahora un temporizador lo vigila cada minuto y avisa al móvil, y las copias no se ejecutan si no está montado, para no escribir en el disco equivocado.

Más en [Copias de seguridad](docs/12-copias-de-seguridad.md).

---

## Problemas resueltos

Una selección; la lista completa, con síntoma, causa y solución, está en [Problemas resueltos](docs/14-problemas-resueltos.md).

| Problema | Causa | Solución |
|---|---|---|
| Disco C: casi lleno | El disco virtual de Docker (WSL2) había crecido a 61 GB | Limpieza, `fstrim` y compactado con `diskpart`: ~54 GB recuperados |
| Jellyfin trataba internet como red local | NAT de Docker en Windows | Filtros en Caddy, redes de Jellyfin y Caddy fuera de Docker |
| Notificaciones de Homarr con `ETIMEDOUT` | Node da 250 ms por intento de conexión y WSL2 añade latencia | `--network-family-autoselection-attempt-timeout=2000` |
| *Connect Timeout* entre contenedores en el servidor | El cortafuegos no dejaba pasar las redes de Docker | `ufw allow from 172.16.0.0/12`, también en el script de instalación |
| Ubuntu solo usaba ~100 GB del SSD | LVM marcado por defecto en el instalador | Desmarcarlo al instalar |
| ~50 GB duplicados tras copiar la biblioteca | `robocopy` no conserva los hardlinks | `jdupes`, probado antes en una carpeta temporal: 46,4 GiB recuperados |
| Caddy no arrancaba tras un reinicio | Su IP fija estaba dentro del rango automático de Docker | Separar el rango automático de las IPs fijas |
| La tele LG no cargaba Jellyfin | Un plugin de la pantalla de inicio | Retirarlo, cambiando una sola cosa cada vez |
| La copia en la nube no podía autorizarse | OAuth necesita navegador y el servidor no tiene | Completar el flujo a mano con `curl` |
| "Parado", pero seguía en marcha | Script lanzado sin administrador que informaba mal | Pararlo a mano y comprobar cada paso, no el mensaje |
| La integración oficial de Verisure no inicia sesión | Habla con MyPages; las cuentas de España están en OWA | Integración comunitaria Verisure OWA |
| SMART idéntico en dos discos distintos | El adaptador USB mezclaba los datos con dos discos a la vez | Revisarlos de uno en uno |
| `:ro` en el socket de Docker no protegía nada | A través del socket se habla con toda la API de Docker | docker-socket-proxy de solo lectura |

---

## Próximos pasos

- [x] **Mudanza al OptiPlex**, Quick Sync, AdGuard Home y Homepage retirado (31-oct).
- [x] **RAM a 16 GB**, discos en su sitio y hardlinks recuperados (31-oct).
- [x] **Copia cifrada fuera de casa** de las configuraciones (1-nov).
- [x] **Fotos y archivos** con Immich y Nextcloud (1-nov).
- [x] **Seguridad, fase 1:** CrowdSec en Caddy y docker-socket-proxy (2-nov).
- [x] **Primeras copias automáticas**, todas correctas (2-nov).
- [ ] **Resolver los cortes USB** del disco de copias.
- [ ] **Restauración de prueba** de la copia cifrada.
- [ ] **Limpiar un bloqueador sobrante de CrowdSec**, y otros flecos menores del 2-nov.
- [ ] **Avisos de seguridad de Jellyfin:** su clave de API da error 401.
- [ ] **Refresco de los canales de TV en directo**, que está fallando.
- [ ] **Ampliar el almacenamiento** en el Black Friday, y la copia fuera de casa de fotos y archivos.
- [ ] **Desinstalar Docker Desktop del PC** tras una semana estable.
- [ ] **Versionar la configuración en git** (compose, Caddyfile, scripts), sin claves.
- [ ] **Homarr:** diseño para móvil e integración con Home Assistant.
- [ ] **Home Assistant:** app en el móvil y automatizaciones.
- [ ] **Router propio** para repartir AdGuard a toda la casa y separar la red.

---

## Documentación detallada

| Página | Contenido |
|---|---|
| [01 · Descarga de ISOs](docs/01-descarga-de-isos.md) | La parte automatizada que llena la biblioteca |
| [02 · Reproducción multimedia](docs/02-reproduccion-multimedia.md) | Jellyfin: plugins, tema, compatibilidad con cada aparato, TV en directo y Quick Sync |
| [03 · Acceso remoto y seguridad](docs/03-acceso-remoto-y-seguridad.md) | Caddy, DuckDNS, filtro por país, CrowdSec, socket de Docker en solo lectura, alertas, contraseñas y plan de seguridad por fases |
| [04 · Subtítulos con Bazarr](docs/04-subtitulos-bazarr.md) | Sincronización automática y script en lote |
| [05 · Panel principal: Homarr](docs/05-panel-homarr.md) | Despliegue, integraciones, tablero, tema, paneles propios y cómo era antes con Homepage |
| [06 · Avisos y paneles propios](docs/06-avisos-y-paneles.md) | Servicio en Python: ntfy y paneles propios |
| [07 · Monitorización: Beszel](docs/07-monitorizacion-beszel.md) | Hub, agentes, alertas, integraciones y su mudanza al servidor |
| [08 · Servidor](docs/08-servidor-nuevo.md) | Por qué no es un paso atrás (con resultados medidos), la mudanza paso a paso, RAM, hardlinks, Claude Code, discos y kit de mudanza |
| [09 · Acceso privado: Tailscale](docs/09-acceso-privado-tailscale.md) | Todo menos Jellyfin por VPN, scripts y cómo pasó el nombre al servidor |
| [10 · Domótica: Home Assistant](docs/10-domotica-home-assistant.md) | Alarma de casa con Verisure OWA, limitaciones y seguridad |
| [11 · DNS: AdGuard Home](docs/11-dns-adguard-home.md) | Despliegue junto al DNS de Ubuntu, DoH, listas y el problema del router del operador |
| [12 · Copias de seguridad](docs/12-copias-de-seguridad.md) | Copia local, copia cifrada fuera de casa, la primera noche completa y el disco de copias inestable |
| [13 · Fotos y archivos](docs/13-fotos-y-archivos.md) | Immich y Nextcloud: diseño del almacenamiento y copias |
| [14 · Problemas resueltos](docs/14-problemas-resueltos.md) | Registro de fallos: síntoma, causa y solución |
| [Registro de cambios](CHANGELOG.md) | Evolución del homelab y cómo era cada parte antes |

## Estructura del repositorio

```
.
├── README.md
├── CHANGELOG.md   ← evolución y versiones anteriores
├── docs/          ← documentación detallada por tema
└── images/        ← capturas de pantalla (ver images/README.md)
```
