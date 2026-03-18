# Guadua Quest

Juego interactivo sobre política pública de la guadua en Colombia.

## Objetivo
Guadua Quest evalúa conocimientos normativos mediante preguntas por bloques temáticos, con mecánicas de vidas, tiempo, ayudas y racha.

## Estructura del proyecto
- `index.html`: aplicación principal (UI, lógica de juego y eventos de analítica).
- `PLANTILLA_DASHBOARD_GA4.md`: guía completa para construir dashboard en GA4/Looker Studio.
- `RESUMEN_EJECUTIVO_GA4.md`: versión ejecutiva resumida para dirección/equipo.
- `LICENSE`: licencia del proyecto.

## Ejecución local
Al ser un sitio estático, puedes abrir `index.html` directamente en navegador.

Para validaciones de analítica, es recomendable servir por HTTP local (por ejemplo con Live Server en VS Code).

## Analítica implementada (GA4)
### Eventos principales
- `select_mode`
- `start_quiz`
- `answer_question`
- `timeout_question`
- `help_used`
- `game_over`
- `game_completed`
- `finish_quiz`

### Parámetros clave
- `game_mode`
- `scenario_id`
- `quiz_session_id`
- `content_version`
- `question_id`
- `question_index`
- `result`
- `is_correct`
- `response_time_seconds`
- `accuracy_pct`
- `best_streak`
- `score`

## Validación rápida de eventos
1. Abrir el juego.
2. Seleccionar modo e iniciar partida.
3. Responder preguntas (correctas e incorrectas).
4. Usar ayudas.
5. Forzar timeout al menos una vez.
6. Completar partida o perder por vidas.
7. Verificar llegada de eventos en GA4 Realtime y DebugView.

## Escalabilidad recomendada
- Mantener eventos y parámetros con naming estable.
- Evitar texto largo como dimensión (usar IDs).
- Incrementar `content_version` al actualizar banco de preguntas.
- Mantener `question_id` estable para comparabilidad histórica.

## Gobierno de documentación
Se recomienda versionar en git la documentación operativa de analítica (sin secretos).  
Si existe información sensible, mantener versión sanitizada en repositorio.

## Próximos pasos sugeridos
- Publicar dashboard de 1 página en Looker Studio con embudo + KPIs.
- Activar `finish_quiz` como conversión principal en GA4.
- Revisar semanalmente abandono por modo y preguntas con mayor error.
