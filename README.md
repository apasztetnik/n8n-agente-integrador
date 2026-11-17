# Asistente de UX Findings · Agente integrador en n8n

Agente de IA construido en n8n que ayuda a un equipo de diseño a consultar y entender los hallazgos (*findings*) de sesiones de user testing registrados en una planilla de Google Sheets.

Es un proyecto integrador: cada módulo del curso suma una capa nueva sobre el mismo workflow. Por lo que desde este primer módulo planteo el proyecto bajo un método que me permita escalabilidad. Para armar este proyecto me basé en una necesidad real de mi trabajo diario y la potencié utilizando Claude para refinar el alcance teniendo en cuenta los módulos futuros del curso. 

## El problema

Los findings de cada release se documentan en una planilla con una pestaña por versión (por ejemplo, `1.10.0` y `1.11.0`). Cada finding tiene módulo, submódulo, acción del usuario, estado, design system, ticket asociado, nivel de severidad, descripción, recomendación y heurística de usabilidad.

Con decenas de findings por versión, responder preguntas simples lleva tiempo: qué está pendiente, qué es grave, qué problemas se repiten o qué afecta a un módulo puntual.

## Qué hace el agente (v1)

El usuario pregunta por chat y el agente consulta la planilla para responder.

**Ejemplos:**
- "¿Qué findings de nivel 2 siguen pendientes?"
- "¿Qué problemas tiene el módulo de gestión de usuarios?"

**Lo que NO hace:** no modifica la planilla ni toma decisiones sobre los findings. Si la consulta pide cambiar o decidir algo (cerrar un finding, cambiar su nivel, aprobar una release), deriva el caso a revisión humana.

## Flujo

```
[Chat Trigger] → [AI Agent] → [IF: ¿requiere revisión humana?]
                     │              ├── Sí → [Gmail: aviso de escalamiento]
                     │              └── No → [Gmail: log de la consulta]
                     ├── Chat Model: Anthropic (Claude Sonnet)
                     ├── Memory: Simple Memory
                     └── Tool: Google Sheets (solo lectura)
```

## Guardrails

- Máximo 5 iteraciones por consulta.
- Acceso de solo lectura a la planilla.
- Derivación a revisión humana para pedidos de cambio o decisión.
- Registro de cada ejecución por Gmail.

## Derivación a revisión humana

Derivan a humano:

- “Cerrá el finding 22, ya está resuelto.”
- “Cambiá el finding 7 a Level 1.”
- “¿Podemos lanzar la 1.10.0 con estos pendientes?”
- “¿Cuál de estos findings puedo dejar para la próxima versión?”
- “Marcá como Resolved todos los de User Management.”

No derivan (las responde el agente):

- “¿Qué findings de Level 2 siguen pendientes?”
- “¿Qué problemas tiene Identity Provider?”
- “¿Cuántos findings están In Review?”

## Hoja de ruta

| Módulo | Evolución |
|---|---|
| M1 | Agente base: consulta de findings, derivación humana y log. |
| M2 | Multiagente: consulta, alta de findings y reportes. |
| M3 | Memoria persistente por sesión. |
| M4 | Integraciones con aprobación humana. |
| M5 | RAG sobre heurísticas y design system. |
| M6 | Carga de findings por voz. |
| M7–M10 | Diseño vertical, supervisión, gobernanza y propuesta comercial. |

## Entregas

- `checkpoint1_agustin_pasztetnik.json`: workflow del Módulo 1.
