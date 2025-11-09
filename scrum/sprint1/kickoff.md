<p align="center">
  <img src="../.img/social_beats_logo.jpeg" alt="Logo Social Beats" width="400" />
</p>

<p align="center" style="font-size: 30px; font-weight: bold;">
  SOCIAL BEATS  -  REUNIÓN DE KICKOFF 02/11/2025
</p>


**Participantes**

| Nombre completo                  | Rol | Contacto              |
| -------------------------------- | --- | --------------------- |
| Andrés Martínez Reviriego        | --  | andmarrev@alum.us.es  |
| Benjamín Ignacio Maureira Flores | --  | benmauflo@alum.us.es  |
| Daniel Galvan Cancio             | --  | dangalcan@alum.us.es  |
| Daniel Ruiz López                | --  | danruilop1@alum.us.es |
| Daniel Vela Camacho              | --  | danvelcam@alum.us.es  |
| Jaime Linares Barrera            | --  | jailinbar@alum.us.es  |
| Miguel Encina Martínez           | --  | migencmar@alum.us.es  |
| Ramón Gavira Sánchez             | --  | ramgavsan@alum.us.es  |
| Rafael Pulido Cifuentes          | --  | rafpulcif@alum.us.es  |
| Sergio Álvarez Piñón             | --  | seralvpin@alum.us.es  |

<br>


# 1. ORDEN DEL DÍA
El objetivo de esta reunión de arranque es establecer la metodología de trabajo, revisar los hitos clave de la asignatura, definir los requisitos de la aplicación y organizar las tareas transversales inmediatas.


# 2. ORGANIZACIÓN Y METODOLOGÍA

Se ha acordado adoptar la metodología Scrum para la gestión del proyecto, adaptando sus reuniones y artefactos a las necesidades de la asignatura.

## 2.1. Hitos Clave de la Asignatura (Milestones)

- **6 de noviembre**: Grupos y temática definidos.
- **13 de noviembre**: División en microservicios, APIs de cada micro definidas, diseño del frontend preliminar y borrador del CA y servicios externos.
- **27 de noviembre**: API y frontend al 50%.
- **4 de diciembre**: API Implementada al 100% e integración. Frontend al 50-75%. (Hito clave para feedback).
- **7 de enero**: Entrega final.

## 2.2. Planificación de Sprints

Se definen 4 sprints con las siguientes fechas y objetivos:

- #### Sprint 1 (30 oct - 13 nov)
    - **Objetivo**: Gestión y definición.
    - **Tareas**: Incluye tareas transversales de gestión (creación de Teams, organización, repo, brainstorming, definición de temática) y un Sprint Planning inicial. El objetivo es cumplir el hito del 13 de nov.

- #### Sprint 2 (13 nov - 27 nov)
    - **Objetivo**: Desarrollo inicial API y Frontend
    - **Tareas**: Trabajo por parejas en la API del microservicio asignado (objetivo > 50%) e inicio del desarrollo del frontend.

- #### Sprint 3 (27 nov - 11 dic)
    - **Objetivo**: Consolidación API y avance en Front.
    - **Tareas**: Este sprint contiene el hito de feedback del 4 de diciembre. El objetivo es tener la API al 99-100% para esa fecha. La última semana del sprint se dedicará a implementar el feedback y avanzar el frontend a >75%.

- #### Sprint 4 (11 dic - 7 ene)
    - **Objetivo**: Cierre y entrega final.
    - **Tareas**: Finalización, integración, pruebas y preparación de la entrega. Se justifica la duración de aproximadamente 1 mes debido al periodo de Navidad.

## 2.3. Reuniones y artefactos Scrum

Se crearán plantillas para estandarizar las reuniones.

#### 1. Sprint Planning
- Revisión y acumulación del backlog.
- Definición del sprint goal.
- Estalecimiento de la definición de hecho (DoD).

#### 2. Daily Meetings
- Se realizarán 1 o 2 veces por semana.
- Se utiizará una plantilla asíncrona (en formato markdown) que cada pareja rellenará para dejar trazabilidad de la reunión y mantener la sincronización en el equipo.

#### 3. Sprint Review
- Cada pareja presentará el incremento de su microservicio.
- Se tomará nota de lo que se ha completado, lo que queda pendiente y los motivos (impedimentos).
- Se hará un balance general del incremento del producto.

#### 1. Sprint Retrospective
- Se usará MetroRetro para comentar cómo se ha trabajado a nivel de equipo y se evaluará el proceso Scrum.


# 3. Requisitos de la aplicación

Se definen los requisitos mínimos obligatorios y una lista de extras deseables:

## 3.1. Requisitos obligatorios (Must-Haves)

Todo microservicio o la aplicación en su conjunto debe cumplir con:

- Un plan de precios con features, usage limits y add-ons.
- Una API Gateway con autenticación JWT (revisar throttling).
- Pruebas automatizadas.
- Todo microservicio debe tener CRUD y OpenAPI automático.
- Autenticación de cada microservicio con JWT (revisar posible incompatibilidad/redundancia con el JWT de la Gateway).
- Despliegue accesible en la nube.
- APIs versionadas.
- Persistencia en NoSQL (ej. MongoDB).
- Contenerización con Docker.
- Uso estricto de Gitlow.
- Integración Continua (CI).
- Pruebas de componente (Jest o similar).

## 3.2. Requisitos adicionales (a elegir 6)

Se identifican 4 requisitos adicionales que se implementarán casi con seguridad:
1. Consumo de APIs externas o almacenamientos (ej. S3).
2. Mecanismo de autenticación avanzado (cubierto por JWT).
3. Gestión de capacidad, throttling o feature toggles (probablemente en la Gateway).
4. Frontend con rutas y navegación (aplicación multi-pantalla).

Quedan por definir 2 requisitos adicionales más, que surgirán del desarrollo (ej. patrón rate limit al consumir APIs, implementación de caché).


# 4. Tareas transversales inmediatas

Para cumplir con los hitos del 6 y 13 de noviembre, se definen 5 "equipos" o frentes de trabajo transversales.

**Objetivo General (para 6 Nov)**:
- Tener clara la estructura de la aplicación, el alcance y los microservicios (mínimo 1 por pareja).

**Objetivo (para 13 Nov)**:
- Tener definida la API (contrato) de cada microservicio.

### Equipos transversales:

- #### Equipo "Plantilla de microservicio":
    - **Misión**: Definir la estructura base de un microservicio (mínimo indispensable) de la que partirá cada pareja.
    - **Tareas**: Investigar e incluir lo mínimo para OpenAPI automático y JWT.
    - **Nota**: Se debe consultar con el profesor hasta qué punto debe estar implementado. La idea es proveer las fuentes bibliográficas y tutoriales, no dar el trabajo hecho si el profesor dice que cada uno debe realizar eso.

- #### Equipo "API Gateway":
    - **Misión**: Definir una arquitectura preliminar de la API Gateway, incluyendo el flujo de autenticación.

- #### Equipo "Frontend":
    - **Misión**:  Montar el proyecto base de frontend, definir la estructura de carpetas, configuración de estilos, etc y pensar las paginas transversales de la app (homepage).

- #### Equipo "Legal/Producto":
    - **Misión**: Redactar los borradores del Pricing, SLA (Acuerdo de nivel de servicio) y CA (Customer Agreement).

- #### Equipo "Scrum":
    - **Misión**: Preparar el tablero de tareas en Github Projects y las plantillas para las ceremonias (punto 2.3).


# 5. Dudas a resolver (próxima tutoría)

- Consultar sobre la implementación de JWT: ¿Es necesario tanto en la Gateway como en cada micro, o basta con que la Gateway valide y pase la información del usuario a los micros?

- Definir el alcance de la "Plantilla de Microservicio" para que sirva de guía sin solapar el trabajo individual de cada pareja.