# Plantilla exacta: Dashboard GA4 + Looker Studio (Guadua Quest)

## 1) Dónde queda localizada la plantilla
- Este documento queda en tu proyecto local: `PLANTILLA_DASHBOARD_GA4.md`.
- El dashboard final queda en la nube, en tu cuenta de Looker Studio.
- La fuente de datos será tu propiedad de Google Analytics 4 (GA4).

## 2) Prerrequisitos (una sola vez)
1. En GA4 entra a Admin > Custom definitions.
2. Crea estas dimensiones de evento:
   - game_mode
   - scenario_id
   - quiz_session_id
   - content_version
   - question_id
   - question_index
   - block
   - help_type
   - result
   - is_correct
3. Crea estas métricas personalizadas (evento, Number):
   - response_time_seconds
   - accuracy_pct
   - best_streak
   - score
   - lives_left
4. En Admin > Events marca finish_quiz como conversión.
5. Espera propagación de datos (ideal: 24 horas).

## 3) Eventos esperados en tu juego
- select_mode
- start_quiz
- answer_question
- timeout_question
- help_used
- game_over
- game_completed
- finish_quiz

## 4) Crear el reporte en Looker Studio
1. Ve a Looker Studio > Create > Report.
2. Selecciona conector Google Analytics.
3. Elige tu propiedad GA4 del proyecto.
4. Añade control de rango de fechas global.
5. Añade control de filtro por game_mode.

---

## 5) Página 1: Embudo y KPI ejecutivos

### A) Scorecard 1: Partidas iniciadas
- Métrica: Event count
- Filtro: event_name = start_quiz
- Título sugerido: Partidas iniciadas

### B) Scorecard 2: Partidas finalizadas
- Métrica: Event count
- Filtro: event_name = finish_quiz
- Título sugerido: Partidas finalizadas

### C) Scorecard 3: Tasa de finalización
- Campo calculado: completion_rate
- Fórmula:
  - SUM(CASE WHEN event_name = "finish_quiz" THEN event_count ELSE 0 END)
    /
    SUM(CASE WHEN event_name = "start_quiz" THEN event_count ELSE 0 END)
- Formato: Percent
- Título sugerido: Tasa de finalización

### D) Embudo principal
- Tipo: Funnel chart
- Dimensión de paso: event_name
- Métrica: Event count
- Filtro inclusivo de eventos:
  - select_mode
  - start_quiz
  - finish_quiz
- Orden de pasos manual:
  1. select_mode
  2. start_quiz
  3. finish_quiz
- Breakdown opcional: game_mode

### E) Barras por resultado final
- Tipo: Bar chart
- Dimensión: result
- Métrica: Event count
- Filtro: event_name = finish_quiz
- Resultado esperado: completed vs game_over

---

## 6) Página 2: Rendimiento del juego

### A) Tabla por modo/escenario
- Tipo: Table
- Dimensiones:
  - game_mode
  - scenario_id
- Métricas:
  - Event count (filtrado a finish_quiz)
  - Avg accuracy_pct
  - Avg best_streak
  - Avg score
- Filtro recomendado: event_name = finish_quiz

### B) Serie temporal de volumen
- Tipo: Time series
- Dimensión: Date
- Métricas:
  - Event count (start_quiz)
  - Event count (finish_quiz)
- Implementación práctica:
  - Duplica gráfico o usa blend de dos fuentes con filtros por evento.

### C) Uso de ayudas
- Tipo: Bar chart
- Dimensión: help_type
- Métrica: Event count
- Filtro: event_name = help_used

### D) Tiempo promedio de respuesta
- Tipo: Scorecard
- Métrica: Avg response_time_seconds
- Filtro: event_name = answer_question
- Título sugerido: Tiempo promedio por respuesta

---

## 7) Página 3: Calidad de preguntas

### A) Top preguntas más falladas
- Tipo: Table
- Dimensiones:
  - question_id
  - question_index
  - block
- Métrica: Event count
- Filtros:
  - event_name = answer_question
  - is_correct = false
- Orden: Event count (descendente)
- Límite: Top 20

### B) Preguntas con mayor tiempo de respuesta
- Tipo: Table
- Dimensiones:
  - question_id
  - question_index
  - block
- Métrica: Avg response_time_seconds
- Filtro: event_name = answer_question
- Orden: Avg response_time_seconds (descendente)
- Límite: Top 20

### C) Timeouts por pregunta
- Tipo: Table
- Dimensiones:
  - question_id
  - question_index
  - block
- Métrica: Event count
- Filtro: event_name = timeout_question
- Orden: Event count (descendente)

---

## 8) Filtros globales recomendados
- date range
- game_mode
- scenario_id
- content_version

Esto te permite comparar cambios entre versiones sin mezclar resultados históricos.

---

## 9) KPI mínimos recomendados
1. Partidas iniciadas
2. Partidas finalizadas
3. Tasa de finalización
4. Precisión promedio
5. Tiempo promedio de respuesta
6. Uso de ayudas por partida

### Fórmula sugerida: ayudas por partida
- help_per_start =
  SUM(CASE WHEN event_name = "help_used" THEN event_count ELSE 0 END)
  /
  SUM(CASE WHEN event_name = "start_quiz" THEN event_count ELSE 0 END)

---

## 10) Validación rápida (QA)
1. Abre el juego y ejecuta un flujo completo:
   - seleccionar modo
   - iniciar partida
   - responder bien y mal
   - usar ayuda
   - generar timeout
   - completar o perder
2. Verifica en GA4 Realtime y DebugView.
3. Verifica en Looker Studio que aparezcan dimensiones personalizadas.
4. Si no aparecen, revisa:
   - que la definición personalizada exista en GA4
   - que el nombre del parámetro coincida exactamente
   - que haya pasado tiempo de propagación

---

## 11) Escalabilidad futura (recomendado)
- Mantener nombres de eventos estables.
- No enviar textos largos de pregunta como dimensión.
- Usar question_id estable (ya implementado).
- Incrementar content_version cuando cambies banco/preguntas.
- Si crece mucho el análisis, conectar export de GA4 a BigQuery.

---

## 12) Convención de naming sugerida
- Eventos: snake_case y verbo + objeto
  - start_quiz, answer_question, finish_quiz
- Parámetros: snake_case
  - question_id, response_time_seconds, accuracy_pct
- Valores controlados:
  - result: completed | game_over
  - help_type: 50_50 | experto
