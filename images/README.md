# Capturas

Carpeta para las capturas de pantalla del homelab. En la documentación hay comentarios invisibles (`<!-- 📸 ... -->`) en el sitio donde va cada una: al añadirla, sustituye el comentario por la imagen.

```markdown
![Tablero de Homarr](images/homarr-escritorio.png)
```

Desde una página de `docs/`, la ruta lleva `../` delante, por ejemplo `../images/beszel.png`.

| Archivo | Qué captura | Dónde va |
|---|---|---|
| `homarr-escritorio.png` | Tablero de Homarr completo en el PC | README y `docs/05-panel-homarr.md` |
| `homarr-paneles.png` | Los dos paneles de descargas dentro de Homarr | `docs/05-panel-homarr.md` |
| `beszel.png` | Beszel con los sistemas y sus gráficas | `docs/07-monitorizacion-beszel.md` |
| `jellyfin-inicio.png` | Pantalla de inicio de Jellyfin con el carrusel y el tema | README y `docs/02-reproduccion-multimedia.md` |
| `actividad.png` | Página "Actividad" con lo añadido y lo fallido | `docs/06-avisos-y-paneles.md` |
| `hub.png` | Panel Homepage, el anterior a Homarr (retirado el 31-oct) | `docs/05-panel-homarr.md` |
| `alerta-ntfy.png` | Una notificación de seguridad de ntfy en el móvil | `docs/03-acceso-remoto-y-seguridad.md` |
| `optiplex-interior.jpg` | El OptiPlex por dentro con los discos montados | README y `docs/08-servidor-nuevo.md` |
| `adguard.png` | Panel de AdGuard Home (resumen, sin la lista de consultas) | `docs/11-dns-adguard-home.md` |
| `immich.png` | Immich en el móvil o el navegador, **con fotos de prueba o difuminadas** | `docs/13-fotos-y-archivos.md` |
| `nzbget-velocidad.png` | NZBGet en el servidor descargando a toda velocidad (con el nombre difuminado) | `docs/08-servidor-nuevo.md` |

**No subir nunca:** capturas de Home Assistant con la alarma, fotos de las cámaras, el historial de la alarma ni fotos o archivos personales de Immich y Nextcloud. Describen la seguridad física de la casa.

**Antes de subir una captura, tapa o difumina:**

- El dominio de DuckDNS, el nombre de la red de Tailscale (`…ts.net`), las IP `100.x`, la IP pública, claves de API, nombres de usuario y contraseñas.
- **Los nombres de lo que se está descargando o hay en la biblioteca** (paneles de descargas, página Actividad, pósters de Jellyfin, widget de reproducciones).
- **En AdGuard:** la lista de consultas y los nombres de los aparatos.
- **Los nombres de proveedores e indexadores** (widget de indexadores, grupos del panel de BitTorrent).

Para el tablero de Homarr queda mejor una captura **con los clientes de descarga en reposo**: se ve el diseño sin tener que difuminar nada.
