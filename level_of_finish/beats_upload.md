# Nivel de acabado del microservicio beats-upload

## 1. Beats-upload

**Pareja**: Daniel Vela Camacho y Miguel Encina Martínez

## 2. Nivel de acabado

**Nivel objetivo**: 10 - Se opta a máxima puntuación porque el microservicio implementa:

### MICROSERVICIO BÁSICO QUE GESTIONE UN RECURSO

- El backend debe ser una API REST tal como se ha visto en clase implementando al menos los métodos GET, POST, PUT y DELETE y devolviendo un conjunto de códigos de estado adecuado: **REALIZADO**.
    - El microservicio implementa un CRUD completo para la gestión de *Beats*. Los controladores se encuentran en `src/controllers/beatController.js` y las rutas en `src/routes/beatRoutes.js`, soportando operaciones de creación (subida), lectura (listado y detalle), actualización y borrado.

- La API debe tener un mecanismo de autenticación: **REALIZADO**.
    - La autenticación se verifica mediante el middleware ubicado en `src/middlewares/authMiddlewares.js`. El microservicio confía en la validación previa realizada por el API Gateway, verificando las cabeceras `x-gateway-authenticated`, `x-user-id` y `x-roles` para identificar al usuario y sus permisos.

- Debe tener un frontend que permita hacer todas las operaciones de la API (este frontend puede ser individual o estar integrado con el resto de frontends): **REALIZADO**.
    - El frontend está integrado en el repositorio común. Permite a los usuarios subir nuevos beats, ver su biblioteca, editar metadatos y eliminarlos. La lógica de conexión con la API se encuentra en `frontend/src/services/beatsService.js`.

- Debe estar desplegado y ser accesible desde la nube (ya sea de forma individual o como parte de la aplicación): **REALIZADO**.
    - El microservicio forma parte del despliegue en Kubernetes de la aplicación socialbeats, siendo accesible a través del API Gateway.

- La API que gestione el recurso también debe ser accesible en una dirección bien versionada: **REALIZADO**.
    - Todos los endpoints están versionados bajo el prefijo `/api/v1`, como se puede observar en `src/main.js` y en la definición de rutas en `src/routes/`.

- Se debe tener una documentación de todas las operaciones de la API incluyendo las posibles peticiones y las respuestas recibidas: **REALIZADO**.
    - Se dispone de una definición OpenAPI completa en el archivo `spec/oas.yaml`, detallando schemas, parámetros y respuestas de todos los endpoints.

- Debe tener persistencia utilizando *MongoDB* u otra base de datos no SQL: **REALIZADO**.
    - Se utiliza MongoDB como base de datos principal. La conexión se establece en `src/db.js` y los modelos se definen utilizando Mongoose en `src/models/beat.js`.

- Deben validarse los datos antes de almacenarlos en la base de datos (por ejemplo, haciendo uso de *mongoose*): **REALIZADO**.
    - Se implementa validación a dos niveles:
        1. Middleware de validación de entrada (`src/middlewares/validationMiddleware.js`) para peticiones HTTP.
        2. Esquemas de Mongoose con tipos y validadores en `src/models/Beat.js` para integridad de datos.

- Debe haber definida una imagen Docker del proyecto: **REALIZADO**.
    - El proyecto cuenta con `Dockerfile` y `Dockerfile-dev` para la construcción de imágenes de producción y desarrollo respectivamente. La última imagen de docker del microservicio se encuentra en [https://hub.docker.com/r/socialbeats/beats-upload](https://hub.docker.com/r/socialbeats/beats-upload).

- Gestión del código fuente: El código debe estar subido a un repositorio de Github siguiendo Github Flow: **REALIZADO**.
    - El código del microservicio se encuentra accesible en la siguiente ruta: [https://github.com/SocialBeats/beats-upload](https://github.com/SocialBeats/beats-upload). La metodología de ramas y de commits se encuentran en [https://github.com/SocialBeats/docs/tree/main/work_methodology/work_methodology.md](https://github.com/SocialBeats/docs/tree/main/work_methodology/work_methodology.md)

- Integración continua: El código debe compilarse, probarse y generar la imagen de Docker automáticamente usando GitHub Actions u otro sistema de integración continua en cada commit: **REALIZADO**.
    - Todos lo archivos que se encuentran dentro de la carpeta `github/workflows/` sirven para la integración continua. En concreto son:
        - `conventional-commits.yml`: se encarga de verificar que se sigue conventional commits.
        - `create-releases.yml`: siempre que se hace *push* de una *tag* a la rama *main* se crea y pública una versión del microservicio en Docker. Es el workflow más importante porque integra el ciclo de CD.
        - `linter.yml`: verificar, al crear una pull o hacer push a main o develop, que todos los archivos siguen ESLint. En caso de fallar por incumplimiento de lint, se aplica un `npm run lint:fix` para hacer que todos los archivos sigan con el estilo definido.
        - `run-tests.yml`: ejecuta los tests inproc, al crear una pull o hacer push a main o develop, para comprobar que no se ha roto ninguna funcionalidad.


- Debe haber pruebas de componente implementadas en Javascript para el código del backend utilizando Jest o similar: **REALIZADO**.
    - Se dispone de una suite completa de pruebas unitarias y de integración utilizando *Vitest* (compatible con Jest). Las pruebas cubren controladores, servicios, middlewares y utilidades, ubicadas en la carpeta `tests/`.

### MICROSERVICIO AVANZADO QUE GESTIONE UN RECURSO

- Implementar un frontend con rutas y navegación: **REALIZADO**.
    - El frontend común utiliza `react-router` para gestionar la navegación entre las diferentes vistas de gestión de beats, perfil y subida.

- Implementar cachés o algún mecanismo para optimizar el acceso a datos de otros recursos: **REALIZADO**.
    - Se utiliza **CloudFront** (CDN) para la distribución optimizada de contenido estático (archivos de audio e imágenes). Se generan URLs firmadas para acceso seguro y de baja latencia desde el borde (edge). La implementación se encuentra en `src/utils/cloudfrontSigner.js`.

- Consumir alguna API externa (distinta de las de los grupos de práctica) a través del backend o algún otro tipo de almacenamiento de datos en cloud como Amazon S3: **REALIZADO**.
    - El microservicio se integra con **Amazon S3** para el almacenamiento escalable de los archivos binarios (moviendo la carga fuera de la base de datos). La configuración y cliente se encuentran en `src/config/s3.js`.

- Implementar el patrón “rate limit” al hacer uso de servicios externos: **REALIZADO**.
    - Se implementa control de concurrencia y rate limiting para las operaciones contra S3 utilizando la librería `bottleneck`, protegiendo la estabilidad del sistema externo y evitando errores por exceso de peticiones. Ver `src/config/s3.js`.

- Implementar un mecanismo de autenticación basado en JWT o equivalente: **REALIZADO**.
    - Se utiliza un sistema de autenticación basado en JWT, donde el microservicio valida y extrae la identidad del usuario de los tokens decodificados y pasados por el API Gateway. Ver `src/middlewares/authMiddlewares.js`.

- Implementar el patrón “circuit breaker” en las comunicaciones con otros servicios: **REALIZADO**.
    - Se utiliza lógica de reintentos y resiliencia en el consumidor de Kafka (`src/services/kafkaConsumer.js`) para manejar desconexiones temporales sin afectar la disponibilidad del servicio.

- Implementar mecanismos de gestión de la capacidad como throttling o feature toggles para rendimiento: **REALIZADO**.
    - Se utiliza el módulo `toobusy-js` en `src/config/s3.js` para monitorizar el *event loop lag* y aplicar *backpressure* (rechazando peticiones con 503) cuando el servidor se encuentra bajo carga excesiva, protegiendo así la estabilidad del nodo.

### NIVEL HASTA 5 PUNTOS

- Microservicio básico completamente implementado: **REALIZADO**.

- Documento de nivel de acabado: **REALIZADO** (Este documento).

- Customer Agreement y análisis de esfuerzos: **REALIZADO** (Documentación global del proyecto).

### NIVEL HASTA 7 PUNTOS

- Debe incluir todos los requisitos del nivel hasta 5 puntos: **REALIZADO**.
    - Explicado anteriormente.

- Aplicación basada en microservicios básica: **REALIZADO**.

- Al menos 3 de las características del microservicio avanzado implementados: **REALIZADO**.
    - Explicado anteriormente

### NIVEL HASTA 9 PUNTOS

- Mínimo 20 pruebas de componente: **REALIZADO**.
    - El proyecto cuenta con una extensa batería de pruebas (más de 20 tests) cubriendo casos de éxito y error en `tests/unit` y `tests/integration`.

- Tener el API REST documentado con swagger (OpenAPI): **REALIZADO**.
    - Se puede ver en el `spec/oas.yaml`. Ese archivo se autogenera gracias al *js-doc* de los archivos en `src/routes`. Ese contenido lo utiliza la librería *swagger-jsdoc* para crear el oas.yaml. Además tenemos definidos los schemas de nuestras entidades en `src/models/OASSchemas.js`. El microservicio dispone de una UI de Swagger que se renderiza gracias lo anterior y a la librería *swagger-ui-express*.

- Al menos 5 de las características del microservicio avanzado implementados: **REALIZADO**.
    - Explicado anteriormente

### NIVEL HASTA 10 PUNTOS

- Al menos 6 características avanzadas: **REALIZADO**.
    - Se han implementado: Frontend rutas, CDN/Caché, S3 Externo, Rate Limit (S3), JWT Auth, Circuit Breaker/Resiliencia Kafka, Gestión de Capacidad (`toobusy-js`).

## 3. Descripción del microservicio en la aplicación

### Resumen Funcional
**Beats-upload** es el microservicio encargado de la ingestión, almacenamiento y gestión del ciclo de vida de los *beats* (instrumentales) en la plataforma SocialBeats. Sus responsabilidades principales incluyen:

- **Gestión de Beats**: API REST completa para subir, listar, buscar, editar y eliminar beats.
- **Almacenamiento Cloud**: Integración directa con **AWS S3** para almacenar archivos de audio (MP3, WAV, FLAC, AAC) e imágenes de portada de forma segura y escalable.
- **Distribución de Contenido (CDN)**: Generación de **URLs firmadas de CloudFront** para permitir la reproducción segura, streaming optimizado y distribución de baja latencia desde el edge.
- **Validación de Metadatos**: Asegura la integridad de los datos musicales (BPM, Key, Género, Tags, duración, etc.) tanto a nivel de middleware como de modelo Mongoose.
- **Control de Acceso**: Gestión de beats públicos y privados, con autorización basada en propiedad del recurso.
- **Procesamiento de Eventos**: Reacciona a eventos de dominio (como `USER_DELETED`) a través de **Kafka** para mantener la consistencia del sistema (borrado en cascada de beats del usuario eliminado).
- **Emisión de Eventos**: Publica eventos como `BEAT_CREATED`, `BEAT_UPDATED`, `BEAT_DELETED` para que otros microservicios mantengan sus vistas materializadas actualizadas.
- **Integración con Pricing**: Valida los límites del plan de usuario (número máximo de beats, tamaño máximo por beat) mediante integración con el servicio **SPACE** antes de permitir la subida.

### Flujo principal (alto nivel)

1. **Subida de Beat**: El usuario solicita una URL prefirmada para subir el archivo de audio (`POST /upload-url`). El sistema valida su plan de precios y límites.
2. **Upload directo a S3**: El frontend sube el archivo directamente a S3 usando la URL prefirmada (evitando pasar el binario por el backend).
3. **Registro del Beat**: Una vez subido el archivo, el usuario crea el beat (`POST /beats`) con los metadatos y la clave S3 del archivo.
4. **Validación y Persistencia**: El microservicio valida los datos (middleware + Mongoose), asocia el beat al usuario y lo persiste en MongoDB.
5. **Evento Kafka**: Se emite el evento `BEAT_CREATED` para que otros microservicios (como `beats-interaction`) actualicen sus vistas materializadas.
6. **Reproducción**: Cuando un usuario quiere escuchar un beat, se genera una URL firmada de CloudFront con expiración temporal para streaming seguro.
7. **Estadísticas**: Las reproducciones (`/play`) y descargas (`/download`) incrementan los contadores del beat.
8. **Reacción a eventos externos**: Si se recibe un evento `USER_DELETED` vía Kafka, se eliminan automáticamente todos los beats de ese usuario (incluyendo archivos en S3).

## 4. Arquitectura y componentes relevantes

### Arquitectura

```fs
.
│
├── .github                             # Plantillas de issues y workflows de Github
│   ├── ISSUE_TEMPLATE
│   │   ├── bug-report.yml
│   │   ├── issue-template.yml
│   │   └── security-vulnerability.yml
│   └── workflows
│       ├── conventional-commits.yml    # Validación de commits convencionales
│       ├── create-releases.yml         # CD: Creación de releases y push a Docker Hub
│       ├── linter.yml                  # Verificación de estilo con Prettier
│       └── run-tests.yml               # CI: Ejecución de tests unitarios
│
├── .husky                              # Husky para la gestión de githooks
│   ├── commit-msg
│   ├── pre-commit
│   └── _
│       └── husky.sh
│
├── .vscode                             # Configuración del entorno de desarrollo
│   └── settings.json
│
├── scripts                             # Scripts auxiliares
│   ├── copyEnv.cjs                     # Script para copiar archivos .env según entorno
│   └── test-s3-flow.js                 # Script de prueba del flujo S3
│
├── spec                                # Especificación de la API
│   └── oas.yaml                        # OpenAPI Spec autogenerada
│
├── src
│   ├── db.js                           # Conexión con MongoDB
│   ├── config
│   │   └── s3.js                       # Cliente S3 con Bottleneck (rate limit) y toobusy-js
│   ├── controllers
│   │   └── beatController.js           # Controlador de beats (maneja requests/responses)
│   ├── middlewares
│   │   ├── authMiddlewares.js          # Verificación de autenticación vía headers del Gateway
│   │   ├── authorizationMiddleware.js  # Autorización: requireAuth, requireOwnership, requireBeatAccess
│   │   └── validationMiddleware.js     # Validación de entrada con express-validator
│   ├── models
│   │   ├── Beat.js                     # Modelo Mongoose del Beat con validaciones
│   │   └── index.js                    # Export de modelos
│   ├── routes
│   │   ├── aboutRoutes.js              # Rutas de documentación, changelog y versión
│   │   ├── beatRoutes.js               # Rutas del CRUD de beats (documentadas con Swagger)
│   │   └── healthRoutes.js             # Endpoint de salud
│   ├── services
│   │   ├── beatService.js              # Lógica de negocio: S3, CloudFront, CRUD, Kafka
│   │   ├── kafkaConsumer.js            # Consumidor Kafka con circuit breaker
│   │   └── waveformService.js          # Generación de waveforms para visualización
│   └── utils
│       ├── cloudfrontSigner.js         # Generación de URLs firmadas de CloudFront
│       ├── spaceConnection.js          # Cliente para integración con SPACE (pricing)
│       └── versionUtils.js             # Utilidades para versión y changelog
│
├── tests
│   ├── integration                     # Tests out-of-process
│   │   └── beatService.test.js
│   ├── setup
│   │   └── setup.js                    # Configuración de tests
│   └── unit                            # Tests unitarios (in-process)
│       ├── config
│       │   └── s3.test.js
│       ├── controllers
│       │   └── beatController.test.js
│       ├── health.test.js
│       ├── middlewares
│       │   ├── authMiddlewares.test.js
│       │   ├── authorizationMiddleware.test.js
│       │   └── validationMiddleware.test.js
│       ├── models
│       │   └── beat.test.js
│       ├── routes
│       │   └── aboutRoutes.test.js
│       ├── services
│       │   ├── beatService.download.test.js
│       │   ├── beatService.test.js
│       │   ├── kafkaConsumer.test.js
│       │   └── waveformService.test.js
│       └── utils
│           ├── cloudfrontSigner.test.js
│           └── versionUtils.test.js
│
├── .dockerignore
├── .env.example                        # Variables de entorno para desarrollo local
├── .env.docker.example                 # Variables para app en Docker, DB en host
├── .env.docker-compose.example         # Variables para full-docker
├── .eslintrc.json
├── .gitignore
├── .markdownlint.json
├── .prettierrc.json
├── .version                            # Archivo de versión
├── CHANGELOG.md
├── commitlint.config.cjs
├── docker-compose-dev.yml              # Compose para desarrollo
├── docker-compose.yml                  # Compose para producción
├── Dockerfile                          # Imagen de producción
├── Dockerfile-dev                      # Imagen de desarrollo (con hot-reload)
├── LICENSE
├── logger.js                           # Logger centralizado (Winston)
├── main.js                             # Entry point de la aplicación
├── package.json
├── README.md
└── vitest.config.js                    # Configuración de Vitest
```

### Persistencia (MongoDB)

Colecciones principales:

- `beats`: Almacena toda la información de los beats (metadatos, referencias a S3, estadísticas, información del creador).

El modelo `Beat` incluye los siguientes campos principales:
- `title`, `genre`, `tags`, `description`: Metadatos del beat
- `audio.s3Key`, `audio.s3CoverKey`: Referencias a los archivos en S3
- `audio.filename`, `audio.size`, `audio.format`: Info del archivo
- `audio.quality.bitrate`, `audio.quality.sampleRate`: Calidad del audio
- `audio.waveform`: Array de picos para visualización
- `stats.plays`, `stats.downloads`: Contadores de estadísticas
- `isPublic`, `isDownloadable`, `promoted`: Flags de estado
- `createdBy.userId`, `createdBy.username`, `createdBy.roles`: Info del creador (desnormalizada)

### Seguridad

- **Autenticación**: Se verifica mediante las cabeceras inyectadas por el API Gateway (`x-gateway-authenticated`, `x-user-id`, `x-roles`). El middleware `authMiddlewares.js` valida estos headers.
- **Autorización**:
    - `requireAuth`: Verifica que el usuario esté autenticado.
    - `requireOwnership`: Solo el propietario o admin puede modificar/eliminar un beat.
    - `requireBeatAccess`: Permite acceso a beats públicos, o privados si el usuario es el propietario.
- **URLs Firmadas**: Los archivos en S3 son privados. Se generan URLs firmadas de CloudFront con expiración temporal para acceso controlado.
- **Validación de Uploads**: Las URLs prefirmadas de S3 incluyen políticas que limitan tamaño (15MB máx), tipo MIME y carpeta de destino.

### Kafka (Eventos)

- **Eventos consumidos**:
    - `USER_DELETED`: Al recibir este evento, se eliminan todos los beats del usuario (incluyendo archivos en S3) para mantener consistencia.
    - `USER_CREATED`, `USER_UPDATED`: Ignorados (no relevantes para este microservicio).

- **Eventos producidos**:
    - `BEAT_CREATED`: Emitido al crear un beat exitosamente.
    - `BEAT_UPDATED`: Emitido al actualizar metadatos de un beat.
    - `BEAT_DELETED`: Emitido al eliminar un beat.
    - `BEAT_PLAYS_INCREMENTED`: Emitido al incrementar reproducciones.
    - `BEAT_DOWNLOADS_INCREMENTED`: Emitido al incrementar descargas.

El circuit breaker en `kafkaConsumer.js` implementa reintentos con backoff exponencial y cooldown cuando se alcanza el máximo de reintentos, permitiendo que el servicio continúe funcionando aunque Kafka esté temporalmente indisponible.

### Integración con AWS S3 y CloudFront

- **S3**: Almacenamiento de archivos de audio e imágenes de portada.
    - Upload directo desde frontend mediante URLs prefirmadas (POST presigned).
    - El backend no procesa binarios, solo genera las URLs y registra metadatos.
    - Políticas de bucket restringen tamaño y tipos de archivo.

- **CloudFront**: CDN para distribución optimizada.
    - URLs firmadas con expiración temporal.
    - Streaming con soporte para range requests (seek/scrubbing).
    - Distribución desde edge locations para baja latencia global.

### Mecanismos de Resiliencia

- **Bottleneck** (`src/config/s3.js`): Limita las operaciones concurrentes contra S3 (máximo 5 simultáneas por defecto) para evitar saturación.
- **toobusy-js** (`main.js` y `src/config/s3.js`): Monitoriza el event loop lag y rechaza peticiones (503) cuando el servidor está sobrecargado, protegiendo la estabilidad.
- **Circuit Breaker Kafka** (`src/services/kafkaConsumer.js`): Reintentos con backoff y cooldown para conexiones con Kafka.
- **Graceful Shutdown** (`main.js`): Cierre ordenado de conexiones (Kafka, MongoDB) y finalización de requests en curso antes de apagar el contenedor.

## 5. Descripción del API REST del microservicio

La especificación OpenAPI (OAS) define rutas, request/response y ejemplos. Aquí se resume por grupos funcionales aunque recomendamos revisar el archivo `spec/oas.yaml`.

### 5.1 Documentation & Meta

- **GET `/api/v1/about`**: Devuelve el README renderizado en HTML.
- **GET `/api/v1/version`**: Devuelve la versión del API desde `.version`.
- **GET `/api/v1/changelog`**: Devuelve el changelog en HTML con filtros por versiones/rango.
- **GET `/api/v1/docs`**: Swagger UI interactiva para explorar la API.

### 5.2 Health

- **GET `/api/v1/health`**: Healthcheck general (status, versión, uptime, timestamp, environment, estado de la base de datos).

### 5.3 Beats - Upload

- **POST `/api/v1/beats/upload-url`**
    - Genera una URL prefirmada (presigned POST) para subir archivos directamente a S3.
    - Valida el plan del usuario (límites de beats y tamaño).
    - Requiere autenticación.
    - Request: `{ extension, mimetype, size }`
    - Response: `{ url, fields, fileKey, expiresIn, maxFileSize }`

### 5.4 Beats - CRUD

- **POST `/api/v1/beats`**
    - Crea un nuevo beat con los metadatos proporcionados.
    - Requiere autenticación y validación de entrada.
    - Emite evento `BEAT_CREATED` a Kafka.

- **GET `/api/v1/beats`**
    - Lista paginada de beats públicos con filtros opcionales.
    - Filtros: `genre`, `artist`, `minBpm`, `maxBpm`, `tags`, `sortBy`, `sortOrder`.
    - No requiere autenticación.

- **GET `/api/v1/beats/search`**
    - Búsqueda de beats por título, artista, tags o descripción.
    - Query param: `q` (término de búsqueda, mínimo 2 caracteres).
    - Paginación con `page` y `limit`.

- **GET `/api/v1/beats/my-beats`**
    - Obtiene todos los beats del usuario autenticado (incluye privados).
    - Requiere autenticación.
    - Filtros y paginación disponibles.

- **GET `/api/v1/beats/stats`**
    - Estadísticas globales: total beats, total plays, total downloads, promedio duración, distribución por género.

- **GET `/api/v1/beats/{id}`**
    - Obtiene detalle de un beat específico.
    - Si es privado, requiere ser el propietario.

- **PUT `/api/v1/beats/{id}`**
    - Actualiza metadatos de un beat.
    - Requiere autenticación y ser propietario.
    - Emite evento `BEAT_UPDATED` a Kafka.

- **DELETE `/api/v1/beats/{id}`**
    - Elimina un beat permanentemente (BD y archivos S3).
    - Requiere autenticación y ser propietario.
    - Emite evento `BEAT_DELETED` a Kafka.

### 5.5 Beats - Streaming y Descargas

- **GET `/api/v1/beats/{id}/audio`**
    - Redirige a URL firmada de CloudFront para streaming.

- **POST `/api/v1/beats/{id}/play`**
    - Incrementa contador de reproducciones.
    - Requiere autenticación.
    - Si es privado, requiere ser propietario.
    - Emite evento `BEAT_PLAYS_INCREMENTED`.

- **GET `/api/v1/beats/{id}/download`**
    - Genera URL de descarga (solo si `isDownloadable = true`).
    - Incrementa contador de descargas.
    - Requiere autenticación.
    - Emite evento `BEAT_DOWNLOADS_INCREMENTED`.

### 5.6 Beats - Batch y Promoción

- **POST `/api/v1/beats/batch/signed-urls`**
    - Genera URLs firmadas para múltiples beats a la vez (máximo 10).
    - Útil para cargar playlists optimizando requests.
    - Request: `{ beatIds: [...] }`

- **PATCH `/api/v1/beats/{id}/promote`**
    - Activa/desactiva promoción de un beat.
    - Los beats promocionados aparecen primero en búsquedas y exploración.
    - Valida que el usuario tenga el add-on `promotedBeat` en su plan.
    - Requiere autenticación y ser propietario.

## 6. Integración con AWS (S3 + CloudFront)

### Estrategia de almacenamiento

- **Bucket S3 privado**: Los archivos no son accesibles directamente; requieren URL firmada.
- **Estructura de carpetas**: `users/{userId}/{timestamp}-{filename}.{ext}`
- **Tipos permitidos**: Audio (mp3, wav, flac, aac) e imágenes de portada (jpg, jpeg, png, webp).
- **Tamaño máximo**: 15MB por archivo (enforced en la política del presigned POST).

### Flujo de subida con URL prefirmada

1. Frontend solicita URL prefirmada: `POST /api/v1/beats/upload-url`
2. Backend valida límites del plan del usuario (vía SPACE).
3. Backend genera presigned POST URL con políticas restrictivas.
4. Frontend sube el archivo directamente a S3 (multipart/form-data).
5. Frontend crea el beat: `POST /api/v1/beats` con la `fileKey` obtenida.

### CDN y URLs firmadas

- **CloudFront** actúa como CDN delante de S3.
- Las URLs firmadas se generan con `@aws-sdk/cloudfront-signer`.
- Expiración configurable (default 1 hora para acceso normal, 2 horas para streaming).
- Soporte para **range requests** permitiendo seek/scrubbing en reproductores de audio.

## 7. Gestión de errores y validación

### Códigos de estado HTTP

- **200 OK**: Operación exitosa.
- **201 Created**: Beat creado exitosamente.
- **302 Found**: Redirección a URL de streaming/descarga.
- **400 Bad Request**: Datos de entrada inválidos (validación fallida, formato incorrecto).
- **401 Unauthorized**: Autenticación requerida o token inválido.
- **403 Forbidden**: Sin permisos (no es propietario, beat privado, feature no disponible en plan).
- **404 Not Found**: Beat no encontrado.
- **422 Unprocessable Entity**: Límites del plan excedidos.
- **500 Internal Server Error**: Error inesperado del servidor.
- **503 Service Unavailable**: Servidor sobrecargado (toobusy-js) o servicio externo no disponible.

### Validación de datos

1. **Middleware de validación** (`validationMiddleware.js`): Usa `express-validator` para validar request body y query params antes de llegar al controlador.

2. **Validación Mongoose** (`Beat.js`): El schema define tipos, valores permitidos (enums), longitudes máximas, valores mínimos/máximos y mensajes de error personalizados.

3. **Validación de negocio** (`beatService.js`): Reglas de dominio como consistencia entre extensión y MIME type, límites del plan de usuario, etc.

## 8. Tests

El proyecto cuenta con una suite completa de tests unitarios e integración utilizando **Vitest**:

- **Tests unitarios** (`tests/unit/`): Prueban componentes de forma aislada con mocks.
    - `controllers/beatController.test.js`
    - `services/beatService.test.js`, `beatService.download.test.js`
    - `services/kafkaConsumer.test.js`
    - `services/waveformService.test.js`
    - `middlewares/authMiddlewares.test.js`, `authorizationMiddleware.test.js`, `validationMiddleware.test.js`
    - `models/beat.test.js`
    - `config/s3.test.js`
    - `utils/cloudfrontSigner.test.js`, `versionUtils.test.js`
    - `routes/aboutRoutes.test.js`
    - `health.test.js`

- **Tests de integración** (`tests/integration/`): Prueban el flujo completo con base de datos y servicios reales.
    - `beatService.test.js`

### Ejecución de tests

```bash
npm test              # Ejecuta todos los tests unitarios
npm run test:watch    # Modo watch
npm run test:coverage # Genera reporte de cobertura
```
