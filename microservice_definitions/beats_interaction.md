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

### Arquitectura

```
.
├── .dockerignore
├── .env
├── .env.development.example
├── .env.docker-compose.example
├── .env.docker.example
├── .env.example
├── .eslintrc.json
├── .gitignore
├── .markdownlint.json
├── .prettierrc.json
├── .version
├── CHANGELOG.md
├── commitlint.config.cjs
├── docker-compose-dev.yml
├── docker-compose-test.yml
├── docker-compose.yml
├── Dockerfile
├── Dockerfile-dev
├── LICENSE
├── logger.js
├── main.js
├── package-lock.json
├── package.json
├── README.md
├── vitest.config.js
├── vitest.integration.config.js
│
├── .github             # Plantillas de issues y workflows de Github
│   ├── ISSUE_TEMPLATE
│   │   ├── bug-report.yml
│   │   ├── issue-template.yml
│   │   └── security-vulnerability.yml
│   └── workflows
│       ├── conventional-commits.yml
│       ├── create-releases.yml
│       ├── linter.yml
│       └── run-tests.yml
│
├── .husky              # Husky para la gestión de githooks
│   ├── commit-msg
│   ├── pre-commit
│   └── _
│       ├── .gitignore
│       ├── applypatch-msg
│       ├── commit-msg
│       ├── h
│       ├── husky.sh
│       ├── post-applypatch
│       ├── post-checkout
│       ├── post-commit
│       ├── post-merge
│       ├── post-rewrite
│       ├── pre-applypatch
│       ├── pre-auto-gc
│       ├── pre-commit
│       ├── pre-merge-commit
│       ├── pre-push
│       ├── pre-rebase
│       └── prepare-commit-msg
│
├── .vscode             # Configuración del entorno de desarrollo
│   └── settings.json
│
├── scripts             # Scripts para preparar el entorno de desarrollo
│   └── copyEnv.cjs
│
├── spec                # Especificación de la API
│   └── oas.yaml
│
├── src
│   ├── cache.js        # Conexión con Redis
│   ├── db.js           # Conexión con Mongo
│   ├── middlewares     # Midlewares para la autenticación
│   │   └── authMiddlewares.js
│   ├── models          # Modelo de datos
│   │   ├── BeatMaterialized.js
│   │   ├── Comment.js
│   │   ├── ModerationReport.js
│   │   ├── Playlist.js
│   │   ├── Rating.js
│   │   ├── UserMaterialized.js
│   │   ├── OASSchemas.js
│   │   └── models.js
│   ├── routes          # Documentación de las rutas y definición de endpoints
│   │   ├── aboutRoutes.js
│   │   ├── commentRoutes.js
│   │   ├── healthRoutes.js
│   │   ├── moderationReportRoutes.js
│   │   ├── playlistRoutes.js
│   │   └── ratingRoutes.js
│   ├── services        # Lógica de gestión de peticiones
│   │   ├── commentService.js
│   │   ├── kafkaConsumer.js
│   │   ├── moderationReportService.js
│   │   ├── playlistService.js
│   │   └── ratingService.js
│   └── utils           # Métodos útiles y conexiones a OpenRouter
│       ├── moderationCron.js
│       ├── moderationEngine.js
│       ├── moderationWorker.js
│       ├── openRouterClient.js
│       ├── rateLimit.js
│       ├── spaceConnection.js
│       └── versionUtils.js
│
└── tests
    ├── integration     # Tests outproc
    │   ├── integration.comment.test.js
    │   ├── integration.health.test.js
    │   ├── integration.playlist.test.js
    │   └── integration.rating.test.js
    ├── setup           # Configuración de tests inproc y outproc
    │   ├── setup-integration.js
    │   └── setup.js
    └── unit            # Tests unitarios
        ├── entities.comment.test.js
        ├── entities.ModerationReport.test.js
        ├── entities.playlist.test.js
        ├── entities.rating.test.js
        ├── routes.comment.test.js
        ├── routes.health.test.js
        ├── routes.moderationReport.test.js
        ├── routes.playlist.test.js
        ├── routes.rating.test.js
        ├── services.comment.test.js
        ├── services.kafkaConsumer.test.js
        ├── services.moderationReport.test.js
        ├── services.playlist.test.js
        └── services.rating.test.js
```

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
- **Comunicación con otros microservicios**: Existe una variable de entorno llama INTERNAL_API_KEY que sive para comunicarse con el servicio de usuarios de forma síncrona (para realizar un command que solicita la eliminación de un usuario tras 5 denuncias aceptadas)

### Kafka + Materialized View

- **Eventos consumidos**: Nuestro objetivo es tener datos “cacheados”/materializados para validar y enriquecer respuestas sin depender de llamadas runtime a otros microservicios. Este "caché" es persistente, por lo cual se actualiza con los eventos de eliminado y actualización de los recursos.

    - `USER_CREATED` / `USER_UPDATED` / `USER_DELETED`: actualizar `UserMaterialized`
    - `BEAT_CREATED` / `BEAT_UPDATED` / `BEAT_DELETED`: actualizar `BeatMaterialized`
    - Uso en endpoints:
        - Comprobación de existencia de `userId` cuando Kafka/materialized está habilitado.
        - Respuestas enriquecidas como `DetailedPlaylist` (incluye `collaboratorsData` y `beatsData`).

- **Eventos creados**: Nos comunicamos con el microservicio de social y enviar eventos sociales para el feed de la aplicación.

    - Creación de un comentario
    - Creación de un rating

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
- **402 Payment Required**: cuando se excede el SLA de nuestra API.
- **403 Forbidden**: acceso denegado (p.ej. playlist privada sin permisos).
- **404 Not found**: beat/playlist/comment/rating inexistente.
- **409 Conflict**: ya existe moderation report en revisión.
- **422 Unprocessable Entity**:
    - validaciones (longitud, rango score)
    - reglas de negocio (no reportar propio contenido)
    - kafka enabled y `userId` no existe en materialized store
- **500 Internal Server Error**: errores inesperados.
- **503 Service unavailable**: cuando el microservicio está caído, en proceso de actualización o creandose y el API-Gateway no puede comunicarse con él..
