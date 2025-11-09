<p align="center">
  <img src="../.img/social_beats_logo.jpeg" alt="Logo Social Beats" width="400" />
</p>

<p align="center" style="font-size: 30px; font-weight: bold;">
  SOCIAL BEATS  -  SPRINT PLANNING SPRINT X
</p>

<br>


**ÍNDICE**
- [**1. OBJETIVOS DEL SPRINT**](#1-objetivos-del-sprint)
- [**2. SPRINT BACKLOG**](#2-sprint-backlog)
- [**3. METODOLOGÍA INTERNA**](#3-metodología-interna)
    - [3.1. Gestión de Tareas en GitHub Project](#31-gestión-de-tareas-en-github-project)
    - [3.2. Flujo de desarrollo](#32-flujo-de-desarrollo)
    - [3.3. Definición de Hecho (DoD) de una tarea](#33-definición-de-hecho-dod-de-una-tarea)


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


# **1. OBJETIVOS DEL SPRINT**
El propósito de este informe es definir los objetivos a lograr durante el Sprint #X y describir la metodología para alcanzarlos. Se analizarán el proceso de **Sprint Planning**, la gestión de tareas con **GitHub Project**, y el cumplimiento de las estimaciones iniciales.

**🔴 Sprint Goal:** *sprint goal*.

Los siguientes **objetivos** del *Sprint* harán referencia a las épicas desglosadas en la plataforma *GitHub Project*.

- ✅ **Objetivo 1:** [Descripción breve del objetivo]
- ✅ **Objetivo 2:** [Descripción breve del objetivo]
- ✅ **Objetivo 3:** [Descripción breve del objetivo]

<br>

<br>


# **2. SPRINT BACKLOG**

| Objetivo   | ID         | Funcionalidad                              | Responsable(s)       |
| ---------- | ---------- | ------------------------------------------ | -------------------- |
| Objetivo 1 | #repo-0000 | Implementación del login con autenticación |                      |
| Objetivo 1 | #repo-0001 | Integración de API externa                 |                      |
| Objetivo 2 | #repo-0002 | Diseño del dashboard de usuario            |                      |
| Objetivo 2 | #repo-0003 | CRUD 1                                     |                      |
| Objetivo 3 | #repo-0004 | Datos de clases                            |                      |

<br>

<br>


# **3. METODOLOGÍA INTERNA**

En el siguiente apartado se resumirá la metodología interna seguida por el equipo de desarrollo.

## 3.1. Gestión de Tareas en GitHub Project

El equipo utiliza *GitHub Project* como herramienta de gestión de tareas donde las actividades están organizadas en distintas columnas que reflejan su estado dentro del flujo de trabajo. Esta herramienta cuenta con un **tablero Kanban** para facilitar el seguimiento de las tareas, generación de **gráficas Burn-down** que nos serán útiles en las retrospectivas, y asignación y **estimación de tareas** además de otras funciones que procurarán una buena organización del trabajo.


## 3.2. Flujo de desarrollo

1. **Inicio de la Tarea**
    - El desarrollador selecciona una tarea de la columna "Product Backlog" y la traslada a "Todo".
    - Esta acción indica que la tarea ha sido priorizada para su ejecución.

2. **Trabajo en Progreso**
    - Cuando se comienza a trabajar en la tarea, se mueve a la columna "In Progress".
    - Se debe registrar el tiempo de trabajo en **Clockify** de acuerdo al protocolo y la política de nombrado especificada en el ***Plan De Gestión De La Configuración***.

3. **Revisión de Código: Revisión por pares**
    - Al finalizar la implementación, el responsable de la tarea crea una *Pull Request (PR)* y traslada la tarea a la columna "In Review".
    - El otro miembro del equipo asignado se encarga de analizar el código y verificar su calidad.
    - Si la revisión es satisfactoria, el revisor aprueba la PR y fusiona los cambios.
    - Si se identifican errores o mejoras necesarias, la tarea se devuelve a "In Progress", notificando los ajustes requeridos.
    - Por norma general, el *testing* será realizado también acorde a la revisión por pares.


## 3.3. Definición de Hecho (DoD) de una tarea

Para que una tarea se considere terminada, debe cumplir con los siguientes requisitos:

- La funcionalidad **debe** estar completamente desarrollada y *cumplir con los requisitos* especificados en la **tarea**.

- Se deben **satisfacer** las **expectativas** del producto en términos de **comportamiento y usabilidad**.

- El código **debe seguir las buenas prácticas** establecidas por el equipo.

- Se debe **garantizar** la **legibilidad**, **mantenibilidad** y escalabilidad del código fuente.

- Todo el código **debe ser revisado por al menos un miembro distinto** al desarrollador original.

- El revisor debe verificar que el código funciona correctamente y cumple con los estándares definidos.

- Cada issue debe contar con al menos **un comentario positivo** de otro miembro del equipo antes de su aprobación final.