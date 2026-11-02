# 🧰 Problemas resueltos

[← Volver al README](../README.md)

Registro de fallos que han ido apareciendo, con su causa real y cómo se resolvieron. Es la parte del homelab de la que más he aprendido.

## Windows y Docker Desktop

### Disco C: casi lleno
- **Síntoma:** solo 27 GB libres en C:.
- **Causa:** el disco virtual de Docker (WSL2) había crecido hasta 61 GB y no se encoge solo al borrar cosas.
- **Solución:** `docker system prune`, `fstrim` dentro de WSL y `diskpart` → `compact vdisk`. Unos **54 GB recuperados**.
- **Aprendido:** el mensaje *"993 GiB trimmed"* asusta, pero solo marca espacio libre; no borra datos.
- **27-oct:** Beszel vuelve a marcar C: al 88 %. Pendiente de revisar.

### PC lento con los contenedores
- **Síntoma:** el PC iba a tirones con todo arrancado.
- **Causa:** la máquina virtual de WSL2 (`Vmmem`) se queda con RAM como caché de disco, más allá de lo que usan los contenedores.
- **Solución:** límite de memoria y recuperación gradual en `.wslconfig` y revisión de consumo por contenedor con `docker stats` (ahora también con Beszel).
- **Aprendido:** crear `.wslconfig` desde el Bloc de notas suele dejarlo como `.wslconfig.txt`, y entonces WSL lo ignora.

### Un archivo de configuración que se convertía en carpeta
- **Síntoma:** Decluttarr no arrancaba: *"not a directory"*.
- **Causa:** al arrancar el contenedor sin que existiera `config.yaml`, Docker creó una **carpeta** con ese nombre. El contenedor seguía montándola así aunque después se pusiera el archivo.
- **Solución:** borrar la carpeta, poner el archivo, recrear el contenedor (`docker compose rm -sf` + `up -d`) y, si hace falta, reiniciar Docker Desktop.

### `docker compose`: *no configuration file provided* (26-oct)
- **Síntoma:** desde la carpeta correcta, `docker compose up` decía que no encontraba la configuración.
- **Causa:** al hacer limpieza, había borrado por error todos los archivos sueltos de la carpeta del homelab, los dos `docker-compose` incluidos. Los contenedores seguían funcionando, porque Docker solo necesita esos archivos para arrancar o cambiar algo.
- **Solución:** recuperar los compose de la papelera. Algunos scripts antiguos de diagnóstico se perdieron.
- **Aprendido:** la configuración (compose, Caddyfile, scripts) debería estar **versionada en git**, sin las claves. Está en la lista de próximos pasos.

### Comandos de diagnóstico que no funcionaban
- **Causa:** en PowerShell, `curl` es un alias de `Invoke-WebRequest` y no existen `head` ni `tail`. En PowerShell 5, además, envolver `Invoke-RestMethod` en `@()` convierte la lista entera en un solo elemento.
- **Solución:** usar `curl.exe` y comandos propios de PowerShell.

## Descarga de ISOs

### *Permission denied* al descargar
- **Causa:** las imágenes de LinuxServer de qBittorrent y NZBGet usan `/downloads` por defecto, que no existe en un montaje basado en `/data`.
- **Solución:** cambiar la ruta de guardado de qBittorrent y `MainDir` de NZBGet (con el resto de rutas basadas en `${MainDir}`).

### Prowlarr no podía enviar descargas a NZBGet
- **Causa:** faltaba la categoría "Prowlarr" en NZBGet.
- **Solución:** crear la categoría.

### Descargas que fallaban al reparar
- **Síntoma:** *"Repair failed… not enough par-blocks"*.
- **Causa:** el archivo estaba incompleto en el servidor de origen, no un fallo de configuración.
- **Solución:** extensión *Completion*, que comprueba si el archivo está completo antes de descargarlo, y búsqueda automática de otra opción.

### Descargas casi completas que acababan borradas
- **Síntoma:** a una descarga le faltaba un 0,2 % (reparable de sobra) y aun así falló y se borró.
- **Causa:** un fallo en la extensión *Completion*: al dispararse por un aviso, revisaba el NZB que había provocado el aviso en vez del de cada elemento de la cola, y le quitaba los par2 al que no era.
- **Solución:** parche de ~6 líneas en su `main.py` para que cada NZB use su propio archivo. Detalle en [Descarga de ISOs](01-descarga-de-isos.md#el-fallo-de-la-extensión-completion).

### Descargas lanzadas a mano que desaparecían
- **Causa:** dos reglas de Decluttarr. `remove_orphans` borraba lo lanzado desde Prowlarr (el gestor de biblioteca no lo reconocía como suyo) y `remove_unmonitored` borraba lo que no estaba marcado como seguido.
- **Solución:** localizarlo en los logs de NZBGet y Decluttarr y desactivar las dos reglas, dejando copia de la configuración.

### Varios indexadores caídos a la vez
- **Causa:** un servicio auxiliar de Prowlarr del que dependían varios indexadores.
- **Solución:** ante una caída simultánea, lo primero es revisar ese contenedor y sus logs (y actualizarlo), antes de pensar en problemas de red.

### Indexadores duplicados y búsquedas lentas
- **Síntoma:** el widget de indexadores de Homarr mostraba algunos repetidos y varios en rojo.
- **Causa:** los mismos indexadores dados de alta en Prowlarr y en Jackett; algunos fallaban en cada búsqueda y la ralentizaban.
- **Solución:** cada indexador en un solo sitio (directamente en Prowlarr cuando lo soporta) y fuera los que no funcionaban.

### qBittorrent no conectable
- **Causa:** el puerto de escucha no llegaba al contenedor.
- **Solución:** redirección del puerto en el router y regla en el firewall de Windows.

### Usenet a solo ~10 MB/s ✅
- **Causa:** la escritura en el HDD SMR a través de Docker Desktop, no la conexión ni el proveedor.
- **Solución:** en el servidor nuevo, la carpeta temporal va al SSD.
- **Comprobación (28-oct):** con NZBGet nativo en Windows, fuera de Docker y en el mismo disco, subió a 15–25 MB/s. La capa de Docker Desktop costaba la mitad de la velocidad; el resto lo limita el disco SMR. Ver [Servidor nuevo](08-servidor-nuevo.md#la-prueba-28-oct).
- **Resuelto (31-oct):** en el servidor, **84,9 MB/s**. Ver [Servidor](08-servidor-nuevo.md#los-resultados).

## Mudanza al servidor (30–31 oct)

### Ubuntu solo usaba ~100 GB del SSD
- **Causa:** el instalador de Ubuntu Server marca por defecto "Set up this disk as an LVM group" y solo asigna una parte del disco al sistema.
- **Solución:** desmarcar LVM en el instalador; la raíz quedó con todo el SSD.

### Archivos duplicados tras copiar la biblioteca
- **Causa:** `robocopy` no conserva los hardlinks: lo que estaba enlazado entre descargas y biblioteca llegó como dos archivos (hasta ~60 GB).
- **Solución (pendiente):** volver a unirlos con `jdupes` o `rdfind` en el disco de datos.

### El script decía "parado", pero Caddy seguía en marcha en Windows
- **Causa:** la exportación final se lanzó sin permisos de administrador. El servicio de Caddy dio "Acceso denegado", pero el script informaba de que estaba parado.
- **Solución:** `Stop-Service` y `Set-Service -StartupType Disabled` en una PowerShell de administrador. Si no, al reiniciar el PC habría vuelto a pelear por el puerto 443.
- **Aprendido:** comprobar el resultado de cada paso en lugar de fiarse del mensaje.

### Hora equivocada en el servidor
- **Causa:** Ubuntu quedó en hora UTC; la copia diaria se habría hecho una hora más tarde.
- **Solución:** `timedatectl set-timezone Europe/Madrid`.

### *Connect Timeout* entre contenedores
- **Síntoma:** algunas integraciones de Homarr y la lista de apps con puntos de estado no conectaban.
- **Causa:** los contenedores que llaman a la IP de casa del servidor salen desde las redes de Docker, y el cortafuegos `ufw` no las dejaba pasar.
- **Solución:** `ufw allow from 172.16.0.0/12`, añadido también al script de instalación del kit.

### Beszel: "fingerprint mismatch"
- **Causa:** el sistema guardaba la huella del agente de Docker Desktop; el agente del servidor es otro.
- **Solución:** borrar la huella guardada en el hub para que el agente nuevo se registre.

### La web de qBittorrent se colgaba
- **Causa:** un fallo de la versión 5.2.4.
- **Solución:** fijar la imagen a la versión anterior, en Windows y en el servidor.

## Asentar el servidor (31-oct y 1-nov)

### Caddy no arrancaba tras un reinicio: *Address already in use*
- **Causa:** la IP fija de Caddy en la red de Docker estaba dentro del rango que Docker reparte automáticamente; otro contenedor podía quedársela al arrancar antes.
- **Solución:** reservar un subrango para las IPs automáticas y dejar las fijas fuera.

### Un filtro de `jdupes` que hacía lo contrario de lo esperado
- **Causa:** la opción de tamaño `size-` *incluye* los archivos pequeños en vez de excluirlos.
- **Solución:** probarlo antes en una carpeta temporal. Con la opción corregida se recuperaron 46,4 GiB.

### La prueba SMART larga del disco de copias se cortaba
- **Causa probable:** la caja USB apaga el disco por inactividad, porque la prueba interna no genera tráfico por USB.
- **Solución:** leer 4 KiB con `O_DIRECT` cada minuto durante la prueba (con Python, porque `dd iflag=direct` no funcionaba). Terminó sin errores.

### El disco de copias se desconecta (sin resolver)
- **Síntoma:** cortes USB con reconexión inmediata; el disco vuelve con otro nombre y queda desmontado, y las copias podrían caer en el disco de datos sin avisar.
- **Medidas:** vigilancia cada minuto con aviso al móvil, Beszel por identificador, scripts de copia que no se ejecutan sin el disco montado. Pendiente probar otro cable o alimentador. Ver [Copias](12-copias-de-seguridad.md#el-disco-de-copias-inestable-y-vigilado).

### La copia cifrada no podía autorizarse: el servidor no tiene navegador
- **Solución:** completar el flujo OAuth a mano con `curl` dentro del propio servidor, y decodificar el token, que esta versión de rclone da en base64 y no en JSON.

### X rojas falsas en el panel de descargas
- **Causa:** contaba como fallo los borrados manuales, y las descargas buenas desaparecían del historial del cliente a los 18 s de importarse.
- **Solución:** leer también el historial de los gestores de biblioteca y ocultar los borrados manuales.

### La app de Jellyfin de la tele LG no cargaba
- **Causa:** el plugin Home Screen Sections (que además daba error en el servidor).
- **Solución:** retirarlo. Ver [Reproducción multimedia](02-reproduccion-multimedia.md#la-tele-lg-que-dejó-de-cargar-1-nov).

## DNS

### El router del operador no deja repartir AdGuard
- **Causa:** ni el DNS que reparte por DHCP ni el suyo propio se pueden cambiar.
- **Solución provisional:** DNS a mano en cada aparato (con el router de reserva en el PC; sin reserva en iOS, que consulta todos a la vez). La definitiva, con un router propio. Ver [AdGuard Home](11-dns-adguard-home.md).

## Avisos

### Los avisos de qBittorrent y NZBGet dejaron de llegar
- **Causa 1:** qBittorrent 5.2 responde al inicio de sesión con un **204 sin cuerpo** (antes, `Ok.`) y la cookie de sesión no se reenviaba sola.
- **Causa 2:** dos líneas del script tenían el usuario y la contraseña escritos a mano en lugar de los nombres de las variables.
- **Solución:** aceptar el 204, mandar la cookie a mano, esperar 10 min tras un inicio de sesión fallido (qBittorrent bloquea la IP a los 5 fallos) y corregir las dos líneas. Detalle en [Avisos y paneles propios](06-avisos-y-paneles.md#arreglos-del-26-de-octubre).

## Homarr

### Franjas vacías a los lados del tablero
- **Causa:** había un segundo diseño de 1440 px con barras laterales fijas. Homarr usa el diseño con el breakpoint más alto que quepa en la pantalla, así que en un monitor ancho se veía ese y no el que estaba ordenando.
- **Solución:** borrar ese diseño y quedarme con el base (1200 px, 14 columnas).

### Paneles embebidos con fondo opaco y esquinas cuadradas
- **Causa:** se estaba cargando la versión de página completa (con título y fondo propio), y el navegador pinta un fondo opaco en el iFrame cuando la página y el tablero no coinciden en modo oscuro.
- **Solución:** usar siempre `?embed=1` y añadir `color-scheme: dark`, fondo transparente y esquinas redondeadas al `iframe` desde el CSS del tablero.

### Punto de estado de ntfy en rojo
- **Causa:** la comprobación del widget apuntaba a una dirección que no responde a una petición simple, aunque las notificaciones funcionaban.
- **Solución:** apuntarla a `https://ntfy.sh/v1/health`.

### Widgets que desaparecían del diseño de PC
- **Causa:** los widgets son los mismos en todos los diseños; borrar uno en el diseño de móvil lo borra en todos.
- **Solución:** en el móvil, mover al final lo que no se quiera ver en vez de borrarlo.

### Notificaciones de ntfy con `ETIMEDOUT` (28-oct)
- **Síntoma:** el widget de notificaciones fallaba y el log de Homarr se llenaba de `ETIMEDOUT` hacia ntfy.sh (`durationMs="261"`), aunque los avisos sí llegaban al móvil.
- **Causa:** Node da solo 250 ms a cada intento de conexión (IPv6 e IPv4); ntfy.sh está en Nueva York y la capa WSL2 añade latencia. La IPv6 tampoco es alcanzable desde el contenedor.
- **Solución:** `NODE_OPTIONS=--network-family-autoselection-attempt-timeout=2000` en el `.env` de Homarr y `docker compose up -d` (con `restart` no se relee el `.env`). Detalle en [Homarr](05-panel-homarr.md#lo-que-aprendí-montándolo).

### Un widget que se quedaba colgado
- **Causa:** pedía datos a Homarr usando la IP de casa. Esa petición la hace el propio contenedor, y con Docker Desktop salir hacia la IP del PC para volver a entrar en el mismo contenedor se cuelga.
- **Solución:** dentro del contenedor, usar `http://localhost:7575`.

## Jellyfin

### Internet parecía la red de casa
- **Causa:** el NAT de Docker Desktop hacía que toda conexión externa llegara desde la IP interna de Docker, y Jellyfin permite algunas acciones (reiniciar, apagar) a cualquiera de la red local.
- **Solución:** bloquear esas rutas en Caddy, definir bien las redes de Jellyfin y sacar Caddy de Docker para ver la IP real. Detalle en [Acceso remoto y seguridad](03-acceso-remoto-y-seguridad.md).

### Un hueco enorme en la pantalla de inicio de las teles antiguas
- **Causa:** el CSS del carrusel bajaba las filas de inicio un 65 %, aunque el carrusel no llegara a cargarse por usar JavaScript demasiado moderno.
- **Solución:** quitar ese margen en el CSS personalizado y devolverlo solo a los navegadores modernos con `(width >= 0px)`.

### Plugin de TV en directo que no aparecía en el catálogo (28-oct)
- **Causa:** su última versión estaba hecha para Jellyfin 10.11 y el servidor tiene la 12.2; como otros plugins al cambiar de versión mayor, sale como no compatible.
- **Solución:** comprobar en el manifiesto del repositorio la versión de Jellyfin para la que está hecho cada plugin (`targetAbi`) y elegir una alternativa mantenida para la 12.

## Contraseñas

### La contraseña de NZBGet volvía a la antigua
- **Causa:** la imagen de LinuxServer aplica el usuario y la contraseña de las variables de entorno en cada arranque, así que un cambio hecho desde la web se pierde al reiniciar.
- **Solución:** cambiarla en el `.env` / compose y aplicar con `docker compose up -d nzbget`.

### Contraseñas que rompían la configuración
- **Causa:** los símbolos tienen significado en los archivos del homelab: `$` es una variable en el compose, `#` empieza un comentario en un `.env` y YAML exige comillas para muchos caracteres.
- **Solución:** para los servicios del homelab, contraseñas largas (32 caracteres) solo con letras y números. Ver [Contraseñas](03-acceso-remoto-y-seguridad.md#contraseñas-keepassxc).

## Home Assistant

### La integración oficial de Verisure no dejaba ni iniciar sesión
- **Causa:** se conecta a MyPages, la plataforma de Verisure de los países nórdicos; las cuentas de España están en OWA.
- **Solución:** la integración comunitaria **Verisure OWA**, instalada con HACS. Ver [Domótica](10-domotica-home-assistant.md).

### La tarjeta de alarma de la integración no aparecía
- **Causa:** la versión actual la ha retirado (*deprecated*) en favor de las tarjetas de serie.
- **Solución:** usar el **Panel de alarma** de Home Assistant; las tarjetas de cámaras e historial de la integración siguen valiendo.

## Discos

### Datos SMART idénticos en dos discos distintos
- **Causa:** el adaptador USB mezclaba los datos con los dos discos conectados a la vez.
- **Solución:** revisar cada disco por separado.

### Un sector pendiente en un disco viejo
- **Solución:** `clean all` con `diskpart` para forzar la escritura de todo el disco. El sector se recuperó (pendientes 1 → 0) sin necesidad de reasignarlo. El disco queda solo para copias.
