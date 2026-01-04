# Documento de uso de Inteligencia Artificial

## 1. Introducción

El uso de herramientas de **Inteligencia Artificial (IA)** se ha convertido en un apoyo fundamental en el desarrollo de software moderno. Estas herramientas permiten aumentar la productividad, reducir tareas repetitivas y facilitar el arranque de nuevas funcionalidades, especialmente en proyectos complejos como aplicaciones basadas en microservicios.

En el contexto de este proyecto, la IA se ha utilizado como **herramienta de asistencia**, nunca como sustituto del criterio técnico ni del conocimiento adquirido en la asignatura. Somos conscientes de que los modelos de IA pueden **alucinar**, generar código incorrecto o proponer soluciones que no encajan con la arquitectura del sistema. Por este motivo, todo el contenido generado ha sido **revisado, adaptado y validado manualmente** antes de integrarse en el proyecto final.

El uso de IA ha sido **responsable y controlado**, centrándose en:
- Acelerar tareas mecánicas o repetitivas.
- Obtener primeras versiones sobre las que iterar.
- Explorar alternativas de diseño o detectar errores.
- Mejorar la calidad del código, los tests y el frontend.

En ningún caso se ha delegado en la IA la toma de decisiones críticas de diseño, seguridad o arquitectura.

## 2. Uso de la IA por microservicios

A continuación se describe el uso de la IA en cada uno de los microservicios del proyecto. En aquellos microservicios donde el uso ha sido mínimo o inexistente, se indica explícitamente.

### 2.1. User-auth



### 2.2. Beats-upload



### 2.3. Beats-interaction

El microservicio **beats-interaction** ha utilizado la Inteligencia Artificial siempre como **herramienta de apoyo**. A continuación se detallan los distintos casos de uso y las herramientas empleadas.

#### 2.3.1. Generación de tests unitarios y de integración

La IA se ha utilizado para **generar suites de tests unitarios y de integración**, empleando **Vitest** como framework de pruebas, con el objetivo de:

- Cubrir **todos los caminos posibles** (escenarios positivos y negativos).
- Detectar casos límite y ramas de código complejas que podrían pasarse por alto manualmente.
- Asegurar una alta cobertura de validaciones, reglas de negocio y condiciones de error.

Los tests generados por la IA nunca se han utilizado de forma directa. En todos los casos, han sido:
- Analizados críticamente.
- Ajustados al dominio real de la aplicación.
- Adaptados a la arquitectura del microservicio y a las convenciones del proyecto.
- Refinados manualmente para garantizar precisión y coherencia con el comportamiento esperado.

Este enfoque ha permitido construir baterías de pruebas muy exhaustivas, como puede verse en entidades complejas como **Playlist**, donde se validan múltiples combinaciones de estados, referencias y reglas de negocio.

**Ejemplo de prompt utilizado:**

```text
Genera tests unitarios con Vitest para el servicio de Playlist teniendo en cuenta:
- Validaciones de nombre y descripción
- Límites de longitud
- Usuario no autenticado
- Usuario que excede su cuota
- Casos de error controlados
- Escenarios de éxito
- Cobertura de todas las ramas condicionales
- Crea una suite de tests por cada función

Este es el servicio de playlist: {{código del servicio de playlist}}.
```

#### 2.3.2. Generación y mejora de vistas del frontend

La IA se ha utilizado para:
- Crear una **primera versión** de algunas vistas del frontend, evitando empezar desde cero.
- Mejorar estilos CSS para que encajen con los colores, tipografía y distribución general de la aplicación.
- Proponer estructuras más limpias y coherentes de componentes React.

Posteriormente, estas vistas han sido **refactorizadas manualmente** para ajustarlas al diseño final y a los componentes reutilizables del proyecto.

**Ejemplo de prompt utilizado:**

```text
Genera una primera versión de una vista React para mostrar playlists de un usuario, usando una estructura clara y estilos acordes a una aplicación tipo Spotify/SoundCloud y siguiendo los colores de la vista principal.

{{código css de la vista principal}}
```

#### 2.3.3. Resolución de bugs y problemas técnicos

Durante el desarrollo del microservicio, la IA se ha usado como apoyo para:
- Analizar errores complejos.
- Detectar posibles causas de fallos en tests o rutas.
- Proponer soluciones alternativas a problemas concretos.

En todos los casos, las respuestas de la IA se han tratado como **hipótesis**, nunca como soluciones definitivas, y han sido validadas manualmente.

**Ejemplo de prompt utilizado:**
```text
Tengo este error en un test de integración con Vitest.
Dime posibles causas y soluciones.

{{adjunto error}}
{{adjunto el código del test y de la ruta}}
```

#### 2.3.4. Herramientas de IA utilizadas

Para el desarrollo del microservicio **beats-interaction**, se han utilizado distintas herramientas de Inteligencia Artificial generativa, principalmente como apoyo durante el desarrollo y la fase de pruebas.

La herramienta utilizada de forma **principal** ha sido **ChatGPT**, empleada para:
- Generación de tests unitarios y de integración.
- Creación de primeras versiones de vistas del frontend.
- Mejora de estilos y estructura de componentes React.
- Análisis de errores y resolución de bugs.

De manera **puntual**, también se han utilizado otros modelos como **Claude** y **Gemini**, principalmente para:
- Contrastar respuestas en problemas concretos.
- Obtener explicaciones alternativas sobre errores complejos.
- Comparar enfoques diferentes ante un mismo problema técnico.

El uso de múltiples herramientas ha permitido reducir el riesgo de errores derivados de posibles alucinaciones de un único modelo y reforzar la validación de las soluciones propuestas. En todos los casos, las respuestas generadas por estas IAs han sido revisadas críticamente y adaptadas al contexto real del proyecto antes de su aplicación.

### 2.4. Analytics-and-dashboards



### 2.5. Social


## 3. Conclusión

La Inteligencia Artificial ha sido utilizada en este proyecto como una **herramienta de apoyo responsable**, alineada con buenas prácticas de desarrollo software. Su uso ha permitido:

- Aumentar la productividad.
- Mejorar la cobertura de tests.
- Reducir el tiempo de arranque de nuevas funcionalidades.
- Detectar errores y casos límite.

En todo momento se ha mantenido el **control humano** sobre el código, las decisiones de diseño y la arquitectura, garantizando que el resultado final es coherente, seguro y acorde a los objetivos de la asignatura.

