# Tablero ejecutivo en vivo — Looker Studio (Guadua Quest)

Objetivo: montar un dashboard ejecutivo de 1 página conectado a GA4 y listo para seguimiento semanal.

## 0) Alcance y tiempo
- Duración estimada: 15–20 minutos.
- Resultado: 1 página con KPI, embudo, abandono, precisión, tiempo y ayudas.

## 1) Antes de empezar (GA4)
En GA4 deben existir definiciones personalizadas para estos parámetros de evento:
- game_mode
- scenario_id
- result
- content_version
- question_id
- question_index
- is_correct
- help_type

Y estas métricas personalizadas:
- response_time_seconds
- accuracy_pct
- best_streak
- score

Conversión activa recomendada:
- finish_quiz

## 2) Crear reporte en Looker Studio (en vivo)
1. Ir a Looker Studio.
2. Create → Report.
3. Elegir conector Google Analytics.
4. Seleccionar tu propiedad GA4 de Guadua Quest.
5. Add to report.

## 3) Estructura de la página ejecutiva
Layout sugerido (de arriba a abajo):
- Fila 1: 4 scorecards KPI
- Fila 2: embudo principal + barras de resultado
- Fila 3: precisión por modo + tiempo promedio + uso de ayudas
- Fila 4: preguntas críticas (falladas/timeout)

## 4) Controles globales
Agregar al tope de la página:
- Date range control
- Filtro por game_mode
- Filtro por content_version

## 5) KPI (fila 1)

### KPI 1 — Partidas iniciadas
- Tipo: Scorecard
- Métrica: Event count
- Filtro: event_name = start_quiz

### KPI 2 — Partidas finalizadas
- Tipo: Scorecard
- Métrica: Event count
- Filtro: event_name = finish_quiz

### KPI 3 — Tasa de finalización
- Tipo: Scorecard
- Campo calculado recomendado: completion_rate
- Fórmula:
  SUM(CASE WHEN event_name = "finish_quiz" THEN event_count ELSE 0 END)
  /
  SUM(CASE WHEN event_name = "start_quiz" THEN event_count ELSE 0 END)
- Formato: Percent

### KPI 4 — Precisión promedio
- Tipo: Scorecard
- Métrica: Avg accuracy_pct
- Filtro: event_name = finish_quiz

## 6) Embudo y cierre (fila 2)

### Embudo principal
- Tipo: Funnel chart
- Dimensión: event_name
- Métrica: Event count
- Incluir solo:
  - select_mode
  - start_quiz
  - finish_quiz
- Orden de pasos:
  1) select_mode
  2) start_quiz
  3) finish_quiz

### Resultado final
- Tipo: Bar chart
- Dimensión: result
- Métrica: Event count
- Filtro: event_name = finish_quiz
- Meta visual: comparar completed vs game_over

## 7) Rendimiento (fila 3)

### Precisión por modo
- Tipo: Bar chart
- Dimensión: game_mode
- Métrica: Avg accuracy_pct
- Filtro: event_name = finish_quiz

### Tiempo promedio por respuesta
- Tipo: Scorecard
- Métrica: Avg response_time_seconds
- Filtro: event_name = answer_question

### Uso de ayudas
- Tipo: Bar chart
- Dimensión: help_type
- Métrica: Event count
- Filtro: event_name = help_used

## 8) Alertas de contenido (fila 4)

### Top preguntas más falladas
- Tipo: Table
- Dimensiones:
  - question_id
  - question_index
- Métrica: Event count
- Filtros:
  - event_name = answer_question
  - is_correct = false
- Orden: Event count desc
- Límite: 10

### Top preguntas con timeout
- Tipo: Table
- Dimensiones:
  - question_id
  - question_index
- Métrica: Event count
- Filtro: event_name = timeout_question
- Orden: Event count desc
- Límite: 10

## 9) Configuración de “en vivo” para operación
- Rango por defecto: últimos 7 días.
- Comparativo: período anterior activado en scorecards.
- Frecuencia de revisión:
  - Operativa: diaria (5 min)
  - Comité: semanal (20 min)

## 10) Definición de semáforos ejecutivos
- Tasa de finalización:
  - Verde: >= 65%
  - Amarillo: 50%–64%
  - Rojo: < 50%
- Precisión promedio:
  - Verde: >= 75%
  - Amarillo: 60%–74%
  - Rojo: < 60%
- Tiempo promedio por respuesta:
  - Verde: <= 14 s
  - Amarillo: 15–20 s
  - Rojo: > 20 s

## 11) Publicación y acceso
1. Share → Manage access.
2. Agregar equipo lector.
3. Activar enlace de solo lectura.
4. (Opcional) Programar envío por correo semanal.

## 12) Checklist de validación final
- Se ven datos en todos los gráficos.
- El embudo tiene los 3 pasos en orden.
- El filtro game_mode cambia todos los componentes.
- El filtro content_version permite comparar cambios.
- completed y game_over aparecen en resultado final.

## 13) Si algo no aparece
- Verificar que el parámetro exista como custom definition en GA4.
- Confirmar nombre exacto del parámetro/evento.
- Esperar propagación (hasta ~24 h).
- Revisar Realtime/DebugView para confirmar envío desde el juego.
