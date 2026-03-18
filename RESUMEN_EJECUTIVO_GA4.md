# Resumen ejecutivo — Analítica GA4 de Guadua Quest

## ¿Qué se implementó?
- Integración de Google Analytics 4 (gtag).
- Eventos de embudo y comportamiento:
  - select_mode
  - start_quiz
  - answer_question
  - timeout_question
  - help_used
  - game_over
  - game_completed
  - finish_quiz
- Parámetros escalables para crecimiento:
  - game_mode
  - scenario_id
  - quiz_session_id
  - content_version
  - question_id
  - question_index
  - result
  - is_correct

## ¿Qué permite medir?
1. Conversión del flujo: selección → inicio → finalización.
2. Abandono por modo/escenario.
3. Precisión promedio y racha por sesión.
4. Tiempo de respuesta por pregunta.
5. Uso de ayudas (50/50 y experto).
6. Preguntas con mayor dificultad (errores/timeouts).

## KPI recomendados (mínimos)
- Partidas iniciadas.
- Partidas finalizadas.
- Tasa de finalización.
- Precisión promedio.
- Tiempo promedio de respuesta.
- Uso de ayudas por partida.

## Dónde visualizar
- Validación inmediata: GA4 Realtime y DebugView.
- Análisis: GA4 Explore.
- Reporte compartible: Looker Studio (dashboard).

## Recomendación de gobierno de datos
- Mantener eventos y parámetros estables para comparar histórico.
- Evitar texto largo como dimensiones (usar IDs).
- Incrementar content_version al cambiar banco de preguntas.

## ¿Este documento va en git o .gitignore?
### Recomendación principal
Sí, conservarlo versionado en el repositorio (NO ignorarlo).

### Cuándo sí conviene versionarlo
- Sirve como documentación operativa del equipo.
- No contiene secretos ni credenciales.
- Ayuda a mantener consistencia entre desarrollo, análisis y producto.

### Cuándo sí conviene ignorarlo
- Si incluye información sensible (tokens, IDs privados, URLs internas, correos personales, estrategia confidencial).
- Si es un borrador temporal personal no destinado al equipo.

### Política práctica sugerida
- Mantener en git la guía funcional.
- Si hay datos sensibles, crear una versión sanitizada para git y dejar la sensible fuera (o en gestor seguro).

## Próximo paso recomendado
- Crear un dashboard de 1 página para dirección con:
  - embudo de conversión,
  - abandono por modo,
  - precisión y tiempo promedio,
  - tendencias semanales.
