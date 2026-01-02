## 1. Analytics and Dashboards

- **Pareja:** Rafael Pulido Cifuentes y Daniel Ruiz López

## 2. Nivel de acabado

- **Nivel objetivo:** 10 — La pareja opta a la máxima puntuación porque ha realizado, o tiene previstas y justificadas, todas las tareas necesarias para alcanzar el nivel 10.

- **Nota breve sobre justificaciones posteriores:**
  - Las justificaciones detalladas de cada requisito se añadirán individualmente en las secciones correspondientes cuando proceda. Este documento declara la intención y el alcance: la implementación y el conjunto de evidencias necesarias para optar al 10 están completas o en curso de formalización por la pareja.

## 3. Descripción de el microservicio en la aplicación

- Resumen funcional (qué hace, para quién va dirigido):

  - Este microservicio se encarga del cálculo y almacenamiento de métricas (beat metrics) para los "beats" que suben los usuarios a la aplicación, y de exponer esas métricas mediante endpoints REST para su visualización en dashboards y widgets en el frontend.
  - Flujo principal:
    1. El usuario sube un beat (archivo).
    2. El endpoint `POST /analytics/beat-metrics` recibe la petición y valida que el usuario tiene permiso sobre el beat (ver `app/endpoints/beat_metrics.py`).
    3. El audio se guarda/descarga mediante `AudioFileHandler` y se analiza con el componente de audio (`app/services/audio_analyzer.py`). El análisis extrae `coreMetrics` y `extraMetrics`.
    4. El resultado se persiste en la colección `beat_metrics` de MongoDB (`app/services/beat_metrics_service.py`).
    5. Tras completar el cálculo, el servicio notifica al microservicio de beats (PUT a la URL configurada en `settings.BEATS_SERVICE_URL`) para marcar el beat como con métricas calculadas.
    6. El frontend consulta los endpoints de `dashboards` y `beat-metrics` para mostrar widgets y paneles (`app/endpoints/dashboards.py`, `app/services/dashboard_service.py`).

- Requisitos clave atendidos (resumen):

  - API REST completa con endpoints de consulta y gestión de métricas y dashboards (GET/POST/PUT/DELETE).
  - Autenticación/autorización sobre endpoints mediante middleware (`app/middleware/authentication.py`).
  - Persistencia en MongoDB para `beat_metrics` y `dashboards`.
  - Procesamiento de audio y cálculo de métricas (análisis por `audio_analyzer`).
  - Comunicación con otros microservicios (notificación al microservicio de beats) usando `httpx`.
  - Tests de componente y de endpoints incluidos en `tests/`.

## 4. Descripción del API REST del microservicio

Provee una descripción por endpoint (método, ruta, request, response, códigos HTTP, ejemplos).

### Dashboards

- GET /analytics/dashboards

  - Descripción: Lista dashboards. Usuarios normales ven solo sus dashboards; los administradores ven todos.
  - Parámetros: `skip` (int, opcional), `limit` (int, opcional)
  - Respuesta (200): Lista de objetos `DashboardResponse`.

- GET /analytics/dashboards/{dashboard_id}

  - Descripción: Obtiene un dashboard por su identificador.
  - Respuesta (200): `DashboardResponse`; 404 si no existe.

- POST /analytics/dashboards

  - Descripción: Crea un nuevo dashboard. `owner_id` se asigna desde el contexto del usuario.
  - Body (JSON): `DashboardCreate` — ejemplo: `{ "name": "Mi panel", "beatId": "beat_12345" }`
  - Respuesta (201): `DashboardResponse`.

- PUT /analytics/dashboards/{dashboard_id}

  - Descripción: Actualiza un dashboard (solo nombre permitidos en la API pública).
  - Body (JSON): `DashboardUpdate` — ejemplo: `{ "name": "Panel actualizado" }`
  - Respuesta (200): `DashboardResponse`.

- DELETE /analytics/dashboards/{dashboard_id}
  - Descripción: Elimina un dashboard.
  - Respuesta (200): Mensaje de éxito.

Referencias de implementación:

- `app/endpoints/dashboards.py`
- `app/services/dashboard_service.py`

### Widgets

- GET /analytics/widgets

  - Descripción: Lista widgets del sistema; permite filtrar por `dashboardId`.
  - Parámetros: `dashboardId` (opcional), `skip`, `limit`.
  - Respuesta (200): Lista de `WidgetResponse`.

- GET /analytics/widgets/{widget_id}

  - Descripción: Recupera un widget por su identificador.
  - Respuesta (200): `WidgetResponse`.

- POST /analytics/widgets

  - Descripción: Crea un widget asociado a un dashboard.
  - Body (JSON): `WidgetCreate` — ejemplo: `{ "dashboardId": "id_del_dashboard", "metricType": "BPM" }`
  - Respuesta (201): `WidgetResponse`.

- PUT /analytics/widgets/{widget_id}

  - Descripción: Actualiza un widget.
  - Body (JSON): `WidgetUpdate`.
  - Respuesta (200): `WidgetResponse`.

- DELETE /analytics/widgets/{widget_id}

  - Descripción: Elimina un widget.
  - Respuesta (200): Mensaje de éxito.

- GET /analytics/dashboards/{dashboard_id}/widgets
  - Descripción: Lista widgets de un dashboard específico.
  - Respuesta (200): Lista de `WidgetResponse`.

Referencias de implementación:

- `app/endpoints/widgets.py`
- `app/services/widget_service.py`

### Beat Metrics

- GET /analytics/beat-metrics

  - Descripción: Lista métricas de beats; permite filtrar por `beatId`.
  - Parámetros: `beatId` (opcional), `skip`, `limit`.
  - Respuesta (200): Lista de `BeatMetricsResponse`.

- GET /analytics/beat-metrics/{beat_metrics_id}

  - Descripción: Recupera métricas por su identificador.
  - Respuesta (200): `BeatMetricsResponse`.

- POST /analytics/beat-metrics

  - Descripción: Crea una entrada de métricas analizando un audio.
  - Envío: `multipart/form-data` para `audioFile` o formulario con `audioUrl`.
  - Campos: `beatId` (required), `audioFile` (file, opcional), `audioUrl` (string, opcional).
  - Respuesta (201): `BeatMetricsResponse`.

- PUT /analytics/beat-metrics/{beat_metrics_id}

  - Descripción: Actualiza una entrada de métricas.
  - Body (JSON): `BeatMetricsUpdate`.
  - Respuesta (200): `BeatMetricsResponse`.

- DELETE /analytics/beat-metrics/{beat_metrics_id}
  - Descripción: Elimina una entrada de métricas.
  - Respuesta (200): Mensaje de éxito.

Referencias de implementación:

- `app/endpoints/beat_metrics.py`
- `app/services/beat_metrics_service.py`
