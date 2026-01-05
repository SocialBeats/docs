# Suscripciones óptimas de las APIs externas

## Introducción

En este documento se recogen los análisis justificativos de la suscripciones óptimas de cada API externa usada en el proyecto. Se elaborará un estudio para cada API externa, dividiendo el documento en secciones. Cada sección está destinada a un microsercicio, y cada microservicio tiene el análisis de sus APIs externas usadas. En caso de que no haya ninguna, se indicará.

## Análisis justificativos de las suscripciones óptimas

### User Auth

### Beats Upload

### Beats-interactions

#### OpenRouter API

##### Contexto y propósito

La API de **OpenRouter** se utiliza dentro del microservicio **Beats-interactions** para habilitar funcionalidades de inteligencia artificial orientadas al análisis, clasificación y moderación de contenido (comentarios, descripciones, metadatos, etc.).

OpenRouter actúa como un *gateway* unificado que da acceso a **más de 400 modelos** (número que se actualiza constantemente), permitiendo seleccionar dinámicamente el modelo más adecuado según coste, latencia y calidad.

##### Planes de suscripción disponibles

OpenRouter ofrece tres planes principales adaptados a distintos niveles de uso y madurez del proyecto.

###### 1. Plan Gratuito (Free Tier)

- Acceso a modelos gratuitos (variantes con sufijo `:free`)
- **Límite de 50 solicitudes por día** a modelos gratuitos
- **Límite de 20 solicitudes por minuto (RPM)** a modelos gratuitos
- Sin necesidad de tarjeta de crédito para empezar
- Ideal para:
  - Prototipado
  - Pruebas técnicas
  - Validación inicial del flujo de IA
- **Coste:** $0
- **Nota:** El uso gratuito de modelos populares puede estar sujeto a limitaciones adicionales por parte del proveedor durante horas pico


###### 2. Plan Pay-as-you-go (Pago por uso)

- Acceso a **más de 400 modelos de IA** (catálogo completo)
- **Sin límites en modelos de pago** (requiere saldo mínimo de $10 en créditos)
- **Modelos gratuitos (`:free`) limitados a:**
  - **1.000 solicitudes por día** (con al menos $10 en créditos)
  - **50 solicitudes por día** (con menos de $10 en créditos)
  - **20 RPM** (solicitudes por minuto) en ambos casos
- Facturación transparente basada en:
  - Tokens de entrada y salida
  - Coste específico de cada modelo
- Tarifa de plataforma:
  - ~5.5% sobre el coste del modelo
  - Mínimo de $0.80 (para pagos no cripto)
- Sistema de créditos prepagados con recarga automática o manual
- Sin compromisos ni contratos a largo plazo
- Protección DDoS mediante Cloudflare
- Ideal para:
  - Productos en producción
  - Escenarios con demanda variable o crecimiento progresivo

###### 3. Plan Enterprise

- Precios personalizados
- Descuentos por volumen
- Créditos prepagados con compromiso anual
- Facturación empresarial (PO, invoices, etc.)
- Enrutamiento regional y *failover* automático
- Políticas avanzadas de retención de datos (incluido **Zero Data Retention**)
- Soporte técnico dedicado
- Ideal para:
  - Empresas con alto volumen
  - Requisitos legales, de privacidad o rendimiento específicos

##### Estimación de uso del proyecto

###### Funcionalidades previstas

- Análisis semántico y lírico de beats
- Generación automática de descripciones
- Clasificación y etiquetado por género o mood
- Sistemas de recomendación personalizados
- Moderación de contenido generado por usuarios

###### Estimación de volumen

- Usuarios activos iniciales: **100–500**
- Interacciones por usuario/día: **5–20**
- Solicitudes diarias estimadas: **500–10.000**
- Tokens promedio por solicitud: **500–2.000** (entrada + salida)


###### Modelos candidatos

- **Modelos ligeros / medianos**
  - GPT-3.5
  - Claude Haiku
  - Llama 3  
  → Clasificación, análisis simple y tareas repetitivas

- **Modelos avanzados**
  - GPT-4
  - Claude Sonnet  
  → Recomendaciones complejas, análisis profundo y generación de contenido de mayor calidad

##### Análisis de costes

###### Escenario conservador (500 solicitudes/día)

- Uso mensual: ~15.000 solicitudes  
- Tokens mensuales estimados: 15M – 30M  
- Coste con modelos económicos (ej. GPT-3.5 a $0.50 / $1.50 por 1M tokens):  
  → **$7.50 – $45 / mes**
- Tarifa de plataforma (5.5%):  
  → **$0.80 – $2.50 / mes**

**Total estimado:**  
 **$8.30 – $47.50 / mes**

###### Escenario de crecimiento (5.000 solicitudes/día)

- Uso mensual: ~150.000 solicitudes  
- Tokens mensuales estimados: 150M – 300M  
- Coste estimado: $75 – $450 / mes  
- Tarifa de plataforma: $4.13 – $24.75  

**Total estimado:**  
**$79.13 – $474.75 / mes**

###### Escenario de alto volumen (10.000+ solicitudes/día)

- Uso mensual: 300.000+ solicitudes  
- Tokens mensuales: 300M+  
- Coste estimado: $150 – $900+ / mes  

En este punto, el **Plan Enterprise** suele resultar más rentable gracias a los descuentos por volumen y acuerdos personalizados.

##### Suscripción óptima: **Plan Pay-as-you-go**

###### Justificación

1. **Flexibilidad total**  
   El proyecto se encuentra en una fase temprana, donde el tráfico real aún es incierto. El modelo pay-as-you-go permite escalar sin riesgo.

2. **Sin costes fijos ni compromisos**  
   No hay contratos ni pagos mínimos, lo que reduce la fricción financiera en las primeras etapas.

3. **Acceso completo al ecosistema de modelos**  
   Más de 400 modelos disponibles (y en constante crecimiento) para optimizar coste vs calidad según cada caso de uso.

4. **Control total del gasto**  
   La facturación por tokens permite monitorizar y ajustar el consumo en tiempo real.  
   Presupuesto recomendado inicial: **$50–$100/mes**.

5. **Escalabilidad natural**  
   Al superar consistentemente los $500/mes, se puede migrar fácilmente al plan Enterprise con mejores condiciones.

6. **Ventajas técnicas clave**
   - Compatibilidad directa con el SDK de OpenAI
   - Failover automático entre modelos
   - API unificada y estable
   - Soporte para Zero Data Retention (cumplimiento de privacidad)

##### Plan de acción propuesto

###### Fase 1 — Desarrollo (Mes 1–3)
- Uso del **Plan Gratuito**
- Validación técnica y pruebas funcionales
- Ajuste de prompts y flujos de IA

###### Fase 2 — Lanzamiento beta (Mes 4–6)

- Migración al **Plan Pay-as-you-go**
- Presupuesto inicial: $50–$100/mes
- Monitorización de consumo y rendimiento

###### Fase 3 — Escalado (Mes 7+)

- Evaluación mensual de costes
- Si el gasto supera los $500/mes de forma estable → considerar **Plan Enterprise**

##### Optimizaciones de coste recomendadas

- Usar modelos más económicos para tareas simples
- Implementar caché de respuestas frecuentes
- Aplicar rate limiting por usuario
- Monitorizar métricas de uso desde el dashboard de OpenRouter
- Separar workloads críticos y no críticos por modelo

##### Conclusión

El **Plan Pay-as-you-go de OpenRouter** es la opción más equilibrada para el microservicio **Beats-interactions**, ofreciendo:

- Flexibilidad total  
- Costes controlados  
- Escalabilidad progresiva  
- Acceso a modelos de última generación  

Esta estrategia permite crecer de forma sostenible, minimizando riesgos financieros y manteniendo un alto nivel técnico desde el inicio.

---

### Analytics and Dashboards

#### Estudio de Viabilidad Técnica y Económica: Integración de Azure Translator API en Ecosistema de Producción Musical

Esta sección analiza la viabilidad de integrar el servicio de traducción de Microsoft Azure en nuestra plataforma social de *beats*. Se evalúa el impacto del uso de una caché en Redis y la eficiencia de costes bajo distintos escenarios de consumo y planes de usuario.

##### Introducción y Contexto
En este ecosistema, la API de Azure se utiliza para traducir frases inspiracionales relacionadas con el mundo de la música. Debido a que el proyecto se encuentra en una fase académica, este estudio es fundamental para determinar la suscripción óptima y asegurar que la arquitectura de la aplicación sea escalable antes de enfrentarse a datos reales de tráfico.

##### Definición de Asunciones del Modelo
Para proyectar el consumo de recursos, he establecido las siguientes asunciones basadas en el diseño técnico del sistema:

* **Estrategia de Caché Asimétrica:** Se ha implementado una capa de persistencia con Redis. La API de origen de las frases actualiza el contenido cada 1 hora, mientras que nuestra caché de traducción en Redis tiene una persistencia de 24 horas.
* **Carga Operativa Base:** Se estima que el sistema procesará una frase nueva cada hora, resultando en 24 frases únicas al día.
* **Métrica de Caracteres:** Se estima una longitud media de 150 caracteres por frase.
* **Direccionalidad Lingüística:** El sistema traduce bidireccionalmente entre inglés y español ($D=2$).
* **Distribución de Usuarios:** Se contemplan tres planes (FREE, PRO, STUDIO), aunque en el modelo actual el contenido traducido es global y compartido.

##### Análisis de Datos y Resultados

###### Cálculo del Volumen Mensual
Basado en la configuración de la caché de Redis, el volumen mensual de caracteres ($V_m$) que la API de Azure debe procesar es independiente del número de usuarios, calculándose mediante la siguiente fórmula:

$$V_m = (F \times C \times D) \times T$$

Donde:
* $F$ (Frases por día) = 24
* $C$ (Caracteres promedio) = 150
* $D$ (Direcciones de traducción) = 2
* $T$ (Días del mes) = 30

Sustituyendo los valores:
$$V_m = (24 \times 150 \times 2) \times 30 = 216.000 \text{ caracteres/mes}$$

###### Análisis de Sensibilidad: Escenario de Aleatoriedad
Adicionalmente, se ha considerado un escenario crítico donde la frase inspiracional es aleatoria para cada usuario. Bajo esta premisa, aunque Redis sigue compartiendo traducciones para frases idénticas, la probabilidad de *Cache Miss* aumenta significativamente.

Si suponemos un pool de 5.000 frases y una base de 1.000 usuarios consultando frases distintas, el volumen podría escalar a 1.500 frases únicas diarias:
$$V_{m(\text{estrés})} = (1.500 \times 150 \times 2) \times 30 = 13.500.000 \text{ caracteres/mes}$$

Este escenario de riesgo demuestra que la arquitectura actual de ''frase global'' es el principal motor de ahorro, manteniendo el consumo dentro de los límites gratuitos.

###### Suscripción Óptima
A continuación, se compara el consumo estimado con los límites de Azure:

| Plan Azure | Límite Gratuito (F0) | Consumo Estimado |
| :--- | :--- | :--- |
| Caracteres / Mes | 2.000.000 | 216.000 |
| Coste Económico | 0,00 € | 0,00 € |
| Margen de Seguridad | - | 89,2% |

##### Planes de precios y Evolución del Modelo
En el estado actual de la aplicación, los planes FREE, PRO y STUDIO no afectan la viabilidad, ya que la traducción es un recurso compartido. Sin embargo, se plantean los siguientes escenarios futuros para ajustar la API a los planes de precios:

1. **Traducción de Usuario (PRO/STUDIO):** Permitir la traducción de perfiles o descripciones de *beats* personales, lo que vincularía el coste al crecimiento de usuarios.
2. **Personalización de Idiomas (STUDIO):** Acceso a más de dos idiomas de forma simultánea.
3. **Prioridad de Refresco:** Los usuarios STUDIO podrían forzar la actualización de la frase, saltándose la caché horaria de la API de frases.

##### Conclusiones Finales
El estudio concluye que la **suscripción óptima es el Plan F0 (Gratuito)**. La implementación de Redis como puente entre la API de frases y la API de traducción permite que el proyecto sea económicamente viable a coste cero. La arquitectura asimétrica diseñada no solo garantiza la gratuidad del servicio en su fase académica, sino que establece una base sólida para escalar a planes de pago (S1) solo cuando se introduzcan funcionalidades de traducción personalizada por usuario.

---

### Social