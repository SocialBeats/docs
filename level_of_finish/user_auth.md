# Nivel de acabado del microservicio user-auth

> **Nota**: Todas las referencias a archivos de código en este documento incluyen hipervínculos directos al repositorio de GitHub para facilitar su acceso y revisión.

## 1. User-auth

**Pareja**: Benjamin Ignacio Maureira Flores y Ramon Gavira Sanchez

## 2. Nivel de acabado

**Nivel objetivo**: 10 - Se opta a máxima puntuación porque el microservicio implementa:

### MICROSERVICIO BÁSICO QUE GESTIONE UN RECURSO

#### El backend debe ser una API REST tal como se ha visto en clase implementando al menos los métodos GET, POST, PUT y DELETE y devolviendo un conjunto de códigos de estado adecuado: ✅

Tenemos todos los endpoints necesarios para la gestión completa de usuarios y perfiles. CRUD de perfiles y operaciones de autenticación, además de rutas para administradores y rutas dedicadas a features, como el 2FA o la subida de archivos. Todo el código se encuentra en [`src/routes`](https://github.com/SocialBeats/user-auth/tree/main/src/routes), [`src/services`](https://github.com/SocialBeats/user-auth/tree/main/src/services) y [`src/controllers`](https://github.com/SocialBeats/user-auth/tree/main/src/controllers).

---

#### La API debe tener un mecanismo de autenticación: ✅
Este microservicio **ES** el encargado de la autenticación, somos los proveedores de tokens que se usan en el flujo de comunicación entre los demás microservicios, usando los endpoints de `login`, `logout` y `refresh`. Hemos implementado JWT (Access y Refresh tokens) con rotación de tokens y lista negra en Redis. Sin embargo, la autenticación y comprobación de firma del token se realiza en la API Gateway, el middleware [`src/middlewares/authMiddlewares.js`](https://github.com/SocialBeats/user-auth/blob/main/src/middlewares/authMiddlewares.js) se encarga de inyectar los valores del token en nuestras peticiones para poder ser usados en el microservicio.

---

#### Debe tener un frontend que permita hacer todas las operaciones de la API: ✅
Integrado en el [repositorio común de frontend](https://github.com/SocialBeats/frontend). Incluye vistas de login, registro, recuperación de contraseña, configuración de perfil, activación de 2FA y gestión de cuenta. Rutas principales en el frontend: `/auth/*` y `/profile/*`. Gracias a la creación de un [cliente de Axios](https://github.com/SocialBeats/frontend/tree/develop/src/api) y el [uso de servicios](https://github.com/SocialBeats/frontend/tree/develop/src/services) en el frontend, podemos interactuar con la API

---

#### Debe estar desplegado y ser accesible desde la nube: ✅
Desplegado en el clúster de Kubernetes de Digital Ocean junto al resto de la aplicación. https://socialbeats.es/socialbeats

---

#### La API que gestione el recurso también debe ser accesible en una dirección bien versionada: ✅
Endpoints disponibles bajo el prefijo `/api/v1`.

---

#### Se debe tener una documentación de todas las operaciones de la API incluyendo las posibles peticiones y las respuestas recibidas: ✅
Definido en [`spec/oas.yaml`](https://github.com/SocialBeats/user-auth/blob/main/spec/oas.yaml) usando Swagger/OpenAPI 3.0.

---

#### Debe tener persistencia utilizando *MongoDB* u otra base de datos no SQL: ✅
Conexión gestionada en [`src/db.js`](https://github.com/SocialBeats/user-auth/blob/main/src/db.js). Colecciones `users` y `profiles`.

---

#### Deben validarse los datos antes de almacenarlos en la base de datos (por ejemplo, haciendo uso de *mongoose*): ✅
Validaciones en esquemas de Mongoose ([`src/models/User.js`]((https://github.com/SocialBeats/user-auth/blob/main/src/models/User.js)), [`src/models/Profile.js`]((https://github.com/SocialBeats/user-auth/blob/main/src/models/Profile.js))) y validaciones lógicas adicionales en servicios.

---

#### Debe haber definida una imagen Docker del proyecto: ✅
Imagen disponible en Docker Hub. Se utiliza `Dockerfile` optimizado.

---

#### Gestión del código fuente (Git Flow): ✅
Código en GitHub siguiendo la metodología de ramas del grupo.

---

#### Integración continua: ✅
Workflows en `.github/workflows/` para CI (tests), linter y CD (release).

---

#### Debe haber pruebas de componente implementadas en Javascript para el código del backend utilizando Jest o similar: ✅
Tests implementados con **Vitest**. Incluye unidad (`tests/unit`) e integración (`tests/integration`). Hemos puesto especial énfasis en la seguridad (casos negativos de auth, tokens expirados, 2FA).

### MICROSERVICIO AVANZADO QUE GESTIONE UN RECURSO

- Usar el patrón materialized view para mantener internamente el estado de otros microservicios: **NO APLICA / NO REALIZADO**.
    - `user-auth` actúa como fuente de verdad principal para usuarios y no requiere consumir datos pesados de otros microservicios para su funcionamiento core. Sí emite eventos para que otros construyan sus vistas.

- Implementar cachés o algún mecanismo para optimizar el acceso a datos de otros recursos: **REALIZADO**.
    - Se utiliza **Redis** intensivamente para:
        - Almacenamiento y validación de Refresh Tokens.
        - Gestión de tokens temporales para 2FA.
        - Listas de revocación (blacklist) para logout seguro.
    - Se utiliza en el uso del CDN para almacenar y acceder a los archivos subidos por los usuarios al almacenamiento de archivos S3.

- Consumir alguna API externa a través del backend: **REALIZADO**.
    - Integración con **Resend** para el envío transaccional de correos electrónicos (verificación, reset de password).
    - Integración con **Persona** para la verificación de identidad de los usuarios.

- Implementar el patrón “rate limit” al hacer uso de servicios externos: **REALIZADO**.
    - Implementado para evitar abuso en el envío de emails (para evitar errores 429) y en los intentos de login/verificación (protección contra fuerza bruta).

- Implementar un mecanismo de autenticación basado en JWT o equivalente: **REALIZADO**.
    - Núcleo del servicio. Implementación robusta con `accessToken` (vida corta) y `refreshToken` (vida larga, rotatorio).

- Implementar el patrón “circuit breaker” en las comunicaciones con otros servicios: **REALIZADO**.
    - Aplicado en `src/services/emailService.js` para las comunicaciones con la API de **Resend**, protegiendo el sistema de fallos en el proveedor de correos y gestionando reintentos y estados de circuito (abierto, cerrado, semi-abierto).

- Implementar un microservicio adicional haciendo uso de una arquitectura serverless: **REALIZADO**.
    - Hemos usado el microservicio de `persona-verification-function` como activador de nuestro Function as a Service (FaaS), desplegado en DigitalOcean, que se encarga de ejecutar la lógica de la verificación de identidad de los usuarios.

- Implementar mecanismos de gestión de la capacidad como throttling o feature toggles: **REALIZADO**.
    - Utilizamos la librería `toobusy-js` en `main.js` como middleware para monitorizar el event loop de Node.js. Si el lag excede 100ms, el servidor entra en modo de protección y rechaza nuevas peticiones con un `503 Service Unavailable` y cabecera `Retry-After`.

- Cualquier otra extensión al microservicio básico: **REALIZADO**.
    - **Autenticación en 2 pasos (2FA)**: Implementación completa con TOTP (Google Authenticator) y códigos QR.
    - **Gestión de roles**: Sistema de roles (admin, user, beatmaker) integrado.
    - **Logger y Healthchecks**: Estándar del proyecto.
    - **Changelog y versionado**: Endpoints automáticos.

### NIVEL HASTA 5 PUNTOS

- Microservicio básico completamente implementado. **REALIZADO**.
    - Explicado anteriormente.

- Diseño de un customer agreement para la aplicación en su conjunto con, al menos, tres planes de precios que consideren características funcionales y extrafuncionales. **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación.

- Ficha técnica normalizada del modelo de consumo de las APIs externas utilizadas en la aplicación y que debe incluir al menos algún servicio externo de envío de correos electrónicos con un plan de precios múltiple como SendGrid. **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación. Nuestra parte de la ficha técnica incluye las APIs de *Resend* (emails) y *Persona* (verificación de identidad).

- Documento incluido en el repositorio del microservicio (o en el wiki del repositorio en Github) por cada pareja **REALIZADO**.
    - Es este mismo documento. Además está explicado en el documento de nivel de acabado de la aplicación.

- Vídeo de demostración del microservicio o aplicación funcionando. **REALIZADO**.
    - Es el video de nuestro microservicio y el video demo de la presentación.

- Presentación preparada para ser presentada en 30 minutos por cada equipo de 8/10 personas. **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación.

### NIVEL HASTA 7 PUNTOS

- Debe incluir todos los requisitos del nivel hasta 5 puntos: **REALIZADO**.
    - Explicado anteriormente.

- Aplicación basada en microservicios básica implementada: **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación.

- Análisis justificativo de la suscripción óptima de las APIs del proyecto: **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación. Nuestra parte es el análisis de las suscripciones de *Resend* y *Persona*.

- Al menos 3 de las características del microservicio avanzado implementados: **REALIZADO**.
    - Explicado anteriormente.

### NIVEL HASTA 9 PUNTOS

- Un mínimo de 20 pruebas de componente implementadas incluyendo escenarios positivos y negativos: **REALIZADO**.
    - Sí. Se han implementado tests exhaustivos con **Vitest** cubriendo toda la lógica de autenticación, gestión de tokens, 2FA y perfiles. Se pueden consultar en la carpeta `tests/`. Incluyen casos de éxito, errores de validación, tokens expirados y seguridad.

- Tener el API REST documentado con swagger (OpenAPI): **REALIZADO**.
    - Se puede ver en el `spec/oas.yaml`. Generado automáticamente mediante *swagger-jsdoc* a partir de la documentación en el código (`src/routes`). También incluye definición de esquemas en `src/models`. Disponible visualmente a través de Swagger UI en la ruta `/api/v1/docs`.

- Al menos 5 de las características del microservicio avanzado implementados: **REALIZADO**.
    - Explicado anteriormente.

- Al menos 3 de las características de la aplicación basada en microservicios avanzada implementados: **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación.

### NIVEL HASTA 10 PUNTOS

- Al menos 6 características del microservicio avanzado implementados: **REALIZADO**.
    - Explicado anteriormente.

- Al menos 4 características de la aplicación basada en microservicios avanzada implementados: **REALIZADO**.
    - Tarea grupal, explicado en el documento de nivel de acabado de la aplicación.

- Documento de uso de IA: **REALIZADO**
    - Se incluye en el repositorio de documentación y se detalla el uso de herramientas de IA generativa para la generación de código y tests.

## 3. Descripción del microservicio en la aplicación

### Resumen funcional

El microservicio **user-auth** es el corazón de la gestión de identidades y perfiles de SocialBeats. Responsabilidades principales:

- **Gestión de cuentas**: Registro, login, recuperación de contraseña, verificación de email.
- **Seguridad**: Emisión y validación de JWT, rotación de Refresh Tokens, 2FA (TOTP).
- **Perfiles**: Gestión de datos públicos del usuario (avatar, bio, redes sociales).
- **Administración**: Endpoints protegidos para gestión de usuarios.

### Flujo principal

1. El usuario se registra en la aplicación, rellenando el formulario y verificando su correo.
2. El usuario se loguea y obtiene sus tokens de acceso y refresco.
3. El usuario accede a su perfil, donde puede modificar sus datos personales, su avatar, su bio y sus redes sociales, entre otros.
4. Tras completar todos los datos necesarios en el perfil, el usuario puede verificar su identidad con Persona.


### Arquitectura

```fs
.
│
├── .github                             # Plantillas de issues y workflows de Github
│   ├── ISSUE_TEMPLATE
│   │   ├── bug-report.yml
│   │   ├── feature_request.yml
│   │   └── pull_request_template.md
│   └── workflows
│       ├── conventional-commits.yml
│       ├── create-releases.yml
│       ├── linter.yml
│       └── run-tests.yml
│
├── .husky                              # Husky para la gestión de githooks
│   ├── commit-msg
│   ├── pre-commit
│   └── _
│       └── (scripts de husky)
│
├── .vscode                             # Configuración del entorno de desarrollo
│   ├── extensions.json
│   ├── launch.json
│   └── settings.json
│
├── scripts                             # Scripts para preparar el entorno de desarrollo
│   └── copyEnv.cjs
│
├── spec                                # Especificación de la API
│   └── oas.yaml
│
├── src
│   ├── config
│   │   ├── index.js
│   │   └── swagger.js
│   ├── controllers                     # Controladores de las rutas
│   │   ├── adminController.js
│   │   ├── authController.js
│   │   ├── profileController.js
│   │   ├── tokenValidationController.js
│   │   ├── twoFactorController.js
│   │   └── uploadController.js
│   ├── middlewares                     # Middlewares de Express
│   │   ├── authMiddlewares.js
│   │   ├── internalMiddleware.js
│   │   └── roleMiddleware.js
│   ├── models                          # Modelos Mongoose (Schemas)
│   │   ├── Profile.js
│   │   └── User.js
│   ├── routes                          # Definición de rutas (Endpoints)
│   │   ├── aboutRoutes.js
│   │   ├── adminRoutes.js
│   │   ├── authRoutes.js
│   │   ├── healthRoutes.js
│   │   ├── profileRoutes.js
│   │   ├── twoFactorRoutes.js
│   │   └── uploadRoutes.js
│   ├── services                        # Lógica de negocio
│   │   ├── adminService.js
│   │   ├── authService.js
│   │   ├── emailService.js
│   │   ├── kafkaProducer.js
│   │   ├── profileService.js
│   │   ├── tokenService.js
│   │   └── twoFactorService.js
│   ├── utils                           # Utilidades
│   │   ├── initAdmin.js
│   │   ├── spaceConnection.js
│   │   └── versionUtils.js
│   ├── db.js                           # Conexión a MongoDB
│   └── logger.js                       # Configuración de Winston Logger
│
├── tests
│   ├── integration                     # Tests de integración
│   │   ├── admin.test.js
│   │   ├── auth.test.js
│   │   ├── health.test.js
│   │   └── profile.test.js
│   ├── setup                           # Configuración de entorno de tests
│   │   ├── setup.js
│   │   └── unit-setup.js
│   └── unit                            # Tests unitarios
│       ├── controllers
│       │   ├── adminController.test.js
│       │   ├── authController.test.js
│       │   ├── profileController.test.js
│       │   ├── tokenValidationController.test.js
│       │   ├── twoFactorController.test.js
│       │   └── uploadController.test.js
│       ├── middlewares
│       │   ├── authMiddleware.test.js
│       │   ├── roleMiddleware.test.js
│       └── services
│           ├── adminService.test.js
│           ├── authService.test.js
│           ├── emailService.test.js
│           ├── kafkaProducer.test.js
│           ├── profileService.test.js
│           ├── tokenService.test.js
│           └── twoFactorService.test.js
│
├── .dockerignore
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
├── docker-compose.yml
├── Dockerfile
├── Dockerfile-dev
├── LICENSE
├── main.js
├── package-lock.json
├── package.json
├── README.md
└── vitest.config.js
```

### Componentes clave

- `src/services/authService.js`: Orquestador principal de lógica de registro/login.
- `src/services/tokenService.js`: Abstracción para el manejo de tokens JWT y comunicación con Redis.
- `src/services/emailService.js`: Cliente para envío de correos (Resend).
- `src/services/twoFactorService.js`: Lógica de generación y validación de secretos TOTP y QR.

### Persistencia (MongoDB)

- `users`: Credenciales, roles, secretos 2FA, estado de verificación.
- `profiles`: Información pública (nombre, bio, avatar, links).

### Seguridad

- Passwords hasheadas con **bcrypt**.
- Secretos 2FA encriptados.
- Tokens JWT firmados.

### Kafka

- **Eventos producidos**:
    - `USER_CREATED`: Al registrarse.
    - `USER_UPDATED`: Al cambiar datos clave.
    - `USER_DELETED`: Al eliminar cuenta.
- **Eventos consumidos**: (Ninguno crítico para el core business).

## 5. Descripción del API REST

### 5.1 Auth

- `POST /api/v1/auth/register`: Registro de nuevos usuarios.
- `POST /api/v1/auth/login`: Inicio de sesión (soporta flujo 2FA).
- `POST /api/v1/auth/refresh-token`: Obtención de nuevo access token.
- `POST /api/v1/auth/logout`: Revocación de sesión.
- `POST /api/v1/auth/verify-email`: Verificación de cuenta.
- `POST /api/v1/auth/forgot-password` / `reset-password`: Recuperación y reseteo de contraseña.

### 5.2 Profile

- `GET /api/v1/profile/me`: Perfil propio.
- `GET /api/v1/profile/me/completion-status`: Estado de completado del perfil.
- `PUT /api/v1/profile/me`: Actualización de perfil.
- `DELETE /api/v1/profile/me`: Eliminación de cuenta (usuario y perfil).
- `GET /api/v1/profile/{identifier}`: Perfil público por nombre de usuario o ID.

### 5.3 2FA

- `POST /api/v1/2fa/setup`: Iniciar configuración (genera QR).
- `POST /api/v1/2fa/verify`: Verificar código y activar.
- `POST /api/v1/2fa/disable`: Desactivar 2FA.

### 5.4 Admin

- `GET /api/v1/admin/users`: Listado de usuarios (solo admin).
- `DELETE /api/v1/admin/users/{userId}`: Banear/Eliminar usuario.

## 6. Gestión de errores

Uso de códigos HTTP estándar:
- **400**: Bad Request (validación fallida).
- **401**: Unauthorized (token inválido o expirado).
- **403**: Forbidden (falta de rol o permisos).
- **404**: Not Found (usuario/perfil no existe).
- **409**: Conflict (email/username ya en uso).
- **429**: Too Many Requests (rate limit excedido).
