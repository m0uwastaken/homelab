# Capturas

Carpeta para las capturas de pantalla del homelab. En la documentación hay comentarios invisibles (`<!-- 📸 ... -->`) en el sitio donde va cada una: al añadirla, sustituye el comentario por la imagen.

```markdown
![Tablero de Homarr](images/homarr-escritorio.png)
```

Desde una página de `docs/`, la ruta lleva `../` delante, por ejemplo `../images/beszel.png`.

| Archivo | Qué es | Dónde se usa |
|---|---|---|
| `homarr-escritorio.png` | Tablero de Homarr en el PC | README y `docs/05-panel-homarr.md` |
| `homarr-torrents.png` · `homarr-nzb.png` | Los dos paneles de descargas propios | `docs/05-panel-homarr.md` |
| `homarr-alertas.png` | Alertas de Beszel en Homarr | `docs/07-monitorizacion-beszel.md` |
| `beszel.png` | Beszel: el servidor en la última hora | `docs/07-monitorizacion-beszel.md` |
| `adguard.png` | Panel de AdGuard Home | `docs/11-dns-adguard-home.md` |
| `ntfy-movil.png` | Avisos en el móvil | `docs/12-copias-de-seguridad.md` |
| `nzbget-velocidad.png` | NZBGet a 771 Mb/s en el servidor | `docs/08-servidor-nuevo.md` |
| `optiplex-interior.jpg` | El OptiPlex por dentro | README y `docs/08-servidor-nuevo.md` |
| `immich.png` | Álbumes de Immich | `docs/13-fotos-y-archivos.md` |
| `jellyfin-login.png` | Inicio de sesión de Jellyfin con los usuarios ocultos | `docs/02-reproduccion-multimedia.md` |
| `crowdsec-escaneo.png` | Escáner de URLs externo contra mi dominio (dominio tapado) | `docs/03-acceso-remoto-y-seguridad.md` |
| `crowdsec-alertas.png` | Las alertas de ese escaneo en la consola de CrowdSec | `docs/03-acceso-remoto-y-seguridad.md` |

Pendiente: `actividad.png`.

**No subir nunca:** capturas de Home Assistant con la alarma, fotos de las cámaras, el historial de la alarma ni fotos o archivos personales que no quieras enseñar. Lo de la alarma describe la seguridad física de la casa.

**Antes de subir una captura, tapa o difumina:**

- El dominio de DuckDNS, el nombre de la red de Tailscale (`…ts.net`), las IP `100.x`, la IP pública, claves de API, nombres de usuario y contraseñas.
- **Los nombres de lo que se está descargando o hay en la biblioteca** (paneles de descargas, página Actividad, pósters de Jellyfin, widget de reproducciones).
- **En AdGuard:** la lista de consultas y los nombres de los aparatos.
- **Los nombres de proveedores e indexadores** (widget de indexadores, grupos del panel de BitTorrent).

Para el tablero de Homarr queda mejor una captura **con los clientes de descarga en reposo**: se ve el diseño sin tener que difuminar nada.
