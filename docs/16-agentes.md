# Agentes: un homelab que se vigila solo (en desarrollo)

[← Volver al README](../README.md)

> **Estado: en desarrollo.** Lo que hay funciona y lo uso, pero es un trabajo por fases y todavía no está terminado. Lo cuento aquí para dejar la idea y el camino; los detalles pueden cambiar.

## Por qué

Quiero que el homelab se vigile y se avise solo, y que la IA piense **solo cuando hace falta**. Uso Claude Code con un plan de cuota limitada, así que la regla es: **lo barato hace lo repetitivo y Claude solo interviene cuando algo no cuadra**.

| Nivel | Quién | Qué hace | Coste |
|---|---|---|---|
| 0 | Scripts + Beszel + ntfy | Comprobar, vigilar 24/7, avisar | 0 tokens |
| 1 | Un modelo barato por API (opcional) | Interpretar un aviso que sigue abierto | Céntimos |
| 2 | Subagentes pequeños de Claude Code | Leer logs largos y resumirlos | Poca cuota |
| 3 | Claude (supervisor) | Diagnosticar un problema, **solo lectura** | Cuota normal |

## Qué hay hecho

- **Vigía sin IA (0 tokens):** scripts de bash lanzados por temporizadores de systemd cada 5 minutos. Mira contenedores caídos o reiniciándose, servicios que no responden, discos llenos, montajes y que las copias de cada noche hayan salido bien. Avisa al móvil por ntfy una vez al abrirse un problema y otra al resolverse, con un resumen diario.
- **Vigilancia del pipeline de descargas:** consulta los gestores de series y películas, los indexadores y los clientes de descarga, y avisa de atascos, bucles de descarga, indexadores caídos y descargas paradas. Hay también un panel de solo lectura.
- **Subagentes:** uno lee logs y devuelve un resumen corto, otro hace una revisión de salud en solo lectura y otro valora el pipeline de descargas.
- **Supervisor (apagado de momento):** cuando un problema sigue abierto, puede lanzar a Claude para que lo **diagnostique** y deje un informe. Nunca cambia nada: el arreglo lo decido yo, a mano.
- **Triaje con un modelo barato (apagado):** hecho y probado, pero desactivado hasta calcular su coste real.
- **Chat web:** una página para hablar con Claude desde el móvil, con permisos limitados y con botón de aprobación para cada orden que cambia algo.

## Qué falta

- Activar el supervisor y el triaje con IA, tras un periodo de pruebas.
- Un informe semanal con tendencias (indexadores, perfiles que no consiguen castellano).
- Un widget del vigía en Homarr.

## Reglas que me he puesto

- **Solo lectura por defecto.** El supervisor no puede editar, borrar ni usar `sudo`.
- **Nada de secretos:** las claves nunca se leen, ni se mandan a ninguna API. A los modelos externos solo van líneas de error de una lista cerrada de contenedores.
- **La IA nunca silencia un aviso:** solo puede añadir una explicación.
- **Los topes de gasto** son parte del diseño: máximo de llamadas al día y de turnos por diagnóstico.
