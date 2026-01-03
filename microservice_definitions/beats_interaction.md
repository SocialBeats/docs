## 1. Beats-interaction

**Pareja**: Daniel Galván Cancio y Jaime Linares Barrera

## 2. Nivel de acabado

**Nivel objetivo**: 10 - Se opta a máxima puntuación porque el microservicio implementa:
- API REST completa (CRUD + endpoints de agregación/consulta).
- Autenticación JWT (bearer).
- Persistencia MongoDB.
- Integración con Kafka y Materialized Views (usuarios y beats).
- Integración con API externa (OpenRouter) + rate limiting + estrategia de “deferred moderation” con CronJob.
- Healthchecks (API/Kafka/Moderation).
- Mecanismos de moderación y reporting (ModerationReports).
- Control de errores, validaciones, y códigos HTTP coherentes.

## 3. Descripción del microservicio en la aplicación

### Resumen funcional

El microservicio **beats-interaction** gestiona las **interacciones sociales** de los usuarios sobre beats y playlists dentro de la aplicación:
- **Comentarios** sobre beats y playlists.
- **Ratings** (puntuación + comentario opcional) sobre beats y playlists.
- **Playlists**: creación, edición, visibilidad (pública/privada), colaboradores, e items (beats añadidos).
- **Moderación**:
    - Creación de **Moderation Reports** contra comentarios/ratings/playlists.
    - Estado del reporte: `Checking` | `Rejected` | `Accepted`.
    - Integración con moderación automática vía IA.
    - Healthcheck del subsistema de moderación (Redis/Cron/pending reports).

Además, para minimizar latencia entre microservicios y evitar lecturas directas a servicios externos, utiliza Materialized Views de:
- `users` (desde user-auth)
- `beats` (desde beats-upload)

### Flujo principal (alto nivel)

1. El usuario realiza una acción (crear playlist / comentar / puntuar / reportar).
2. El microservicio valida autenticación (bearerAuth JWT).
3. Se validan reglas de dominio (p.ej. no duplicar rating por beat/playlist, límites de longitud, playlist pública para rating, etc.).
4. Se aplica moderación:
    - Moderación síncrona si el SLA del rate-limit lo permite.
    - Si no, el contenido pasa a cola/pendiente y un CronJob lo revisa después.
5. Se persiste en MongoDB la entidad correspondiente.
6. Opcional: se publican/consumen eventos Kafka para mantener Materialized Views y consistencia entre microservicios.

## 4. Arquitectura y componentes relevantes

### Persistencia (MongoDB)

Colecciones principales:
- `comments`
- `ratings`
- `playlists`
- `moderationReports`
- `users_materialized`
- `beats_materialized`

### Seguridad

- **Autenticación**: JWT vía `Authorization: Bearer <token>`
- **Autorización** (ejemplos):
    - Solo el autor puede editar/eliminar su comment.
    - Solo el autor del rating puede editar/eliminar su rating.
    - Solo el owner puede editar/eliminar playlist (colaboradores pueden añadir/quitar beats si está permitido).
    - Reportes: un usuario no puede reportar su propio contenido.

### Kafka + Materialized View

- **Objetivo**: disponer de datos “cacheados”/materializados para validar y enriquecer respuestas sin depender de llamadas runtime a otros microservicios.
- **Eventos consumidos típicos**:
    - `UserCreated` / `UserUpdated` / `UserDeleted`: actualizar `UserMaterialized`
    - `BeatCreated` / `BeatUpdated` / `BeatDeleted`: actualizar `BeatMaterialized`
- Uso en endpoints:
    - Comprobación de existencia de `userId` cuando Kafka/materialized está habilitado.
    - Respuestas enriquecidas como `DetailedPlaylist` (incluye `collaboratorsData` y `beatsData`).

## 5. Descripción del API REST del microservicio

La especificación OpenAPI (OAS) define rutas, request/response y ejemplos. Aquí se resume por grupos funcionales.

### 5.1 Documentation & meta

- GET `/api/v1/about`
    
    Devuelve el README renderizado en HTML.

- GET `/api/v1/version`

    Devuelve versión del API desde .version.

- GET `/api/v1/changelog`

    Devuelve changelog en HTML con filtros por versiones/rango.

### 5.2 Health

- GET `/api/v1/health`

    Healthcheck general (API, DB, uptime, entorno).

- GET `/api/v1/kafka/health`

    Estado de conectividad con Kafka.

- GET `/api/v1/moderation/health`

    Estado del subsistema de moderación (Redis, cron status, pending reports, etc.).

### 5.3 Comments

#### Comentarios en beats

- POST `/api/v1/beats/{beatId}/comments`

    Crea comentario asociado al beat autenticado.

- GET `/api/v1/beats/{beatId}/comments`

    Lista paginada de comentarios del beat (page, limit).

#### Comentarios en playlists

- POST `/api/v1/playlists/{playlistId}/comments`

    Crea comentario asociado a playlist.

- GET `/api/v1/playlists/{playlistId}/comments`

    Lista paginada de comentarios de playlist.

#### Operaciones sobre comment

- GET `/api/v1/comments/{commentId}`

    Obtiene un comment por id (útil para moderación).

- PUT/PATCH `/api/v1/comments/{commentId}`

    Edita el texto (solo autor).

- DELETE `/api/v1/comments/{commentId}`

    Borra comment (idempotente en vuestro diseño: devuelve éxito aunque no exista o sea inválido, según OAS).

### 5.4 Ratings

#### Ratings en beats

- POST `/api/v1/beats/{beatId}/ratings`

    Crea rating (solo uno por usuario y beat).

- GET `/api/v1/beats/{beatId}/ratings`

    Lista paginada + promedio (average) + contador (count).

- GET `/api/v1/beats/{beatId}/ratings/me`

    Rating del usuario autenticado para ese beat.

#### Ratings en playlists

- POST `/api/v1/playlists/{playlistId}/ratings`

    Crea rating (playlist debe existir y ser pública).

- GET `/api/v1/playlists/{playlistId}/ratings`

    Lista paginada + average + count.

- GET `/api/v1/playlists/{playlistId}/ratings/me`

    Rating del usuario autenticado para esa playlist.

#### Operaciones sobre rating

- GET `/api/v1/ratings/{ratingId}`

    Obtiene rating por id.

- PUT/PATCH `/api/v1/ratings/{ratingId}`

    Edita score y comment opcional (solo autor).

- DELETE `/api/v1/ratings/{ratingId}`

    Borrado idempotente con deleted: true/false (y 401 si no es dueño).

### 5.5 Playlists

- POST `/api/v1/playlists`

    Crea playlist (name, description, isPublic, collaborators, items).

- GET `/api/v1/playlists/me`

    Playlists del usuario autenticado (owner o collaborator).

- GET `/api/v1/playlists/user/{userId}`

    Playlists públicas del usuario objetivo + las compartidas con el requester.

- GET `/api/v1/playlists/public`

    Listado público con filtros + paginación (name, ownerId, page, limit).

- GET `/api/v1/playlists/{id}`

    Obtiene playlist si: pública o requester es owner/collaborator. Responde normalmente con DetailedPlaylist (enriquecida con materialized view).

- PUT/PATCH `/api/v1/playlists/{id}`

    Actualiza playlist (solo owner): name/description/isPublic/collaborators/items.

- DELETE `/api/v1/playlists/{id}`
    
    Elimina playlist (solo owner).

#### Items

- POST `/api/v1/playlists/{id}/items`

    Añade beat (beatId) si owner/collaborator. Sin duplicados. Máx 250.

- DELETE `/api/v1/playlists/{id}/items/{beatId}`

    Elimina beat de la playlist si owner/collaborator.

### 5.6 Moderation Reports

Creación de reportes (el `authorId` se infiere según el recurso reportado):
- POST `/api/v1/comments/{commentId}/moderationReports`
- POST `/api/v1/ratings/{ratingId}/moderationReports`
- POST `/api/v1/playlists/{playlistId}/moderationReports`

Consultas:
- `GET /api/v1/moderationReports`

    Lista completa de reports (sin paginación, orden desc).

- `GET /api/v1/moderationReports/{moderationReportId}`

    Report por id.

- `GET /api/v1/moderationReports/me`

    Reports donde el usuario autenticado es `authorId` (reportado).

- `GET /api/v1/moderationReports/users/{userId}`

    Reports donde el `authorId` es el usuario indicado.

## 6. Moderación con IA (OpenRouter) + estrategia SLA

### Qué se modera

Actualmente se modera:
- Título y descripción de playlists
- Comentarios
- Texto de ratings

### API externa integrada
- **Proveedor**: OpenRouter
- **Modelo**: `meta-llama/llama-3.2-3b-instruct:free`
- **Motivo**: opción gratuita suficiente para moderación básica y rápida.

### Rate limit aplicado en beats-interaction (capa interna)

Para evitar bloqueos del provider y mantener estabilidad:
- **Máx. 18 requests/minuto**
- **Máx. 45 requests/día**
- Si se excede, el contenido queda pendiente y lo procesa un **CronJob** cuando “haya hueco”.

## 7. Gestión de errores y validación (criterios comunes)

Ejemplos:
- **401 Unauthorized**: token missing/invalid o no-owner en acciones sensibles (editar/borrar).
- **403 Forbidden**: acceso denegado (p.ej. playlist privada sin permisos).
- **404 Not found**: beat/playlist/comment/rating inexistente.
- **409 Conflict**: ya existe moderation report en revisión.
- **422 Unprocessable Entity**:
    - validaciones (longitud, rango score)
    - reglas de negocio (no reportar propio contenido)
    - kafka enabled y `userId` no existe en materialized store
- **500 Internal Server Error**: errores inesperados.
