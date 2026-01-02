# Standardized Consumption Model Datasheet

> Plantilla reproducible para documentar los modelos de consumo de las APIs de los microservicios.

## Objetivo

Proveer una plantilla estandarizada que todos los miembros del grupo puedan usar para describir límites, cuotas, y configuración de consumo de APIs.

---

## Campos (explicación breve)

- **Associated SaaS:** Nombre del servicio externo o nota interna.
- **Type:** `Full` / `Partial` / `Internal` (según aplique).
- **(\*) Pricing Configurations:** Número de configuraciones / enlace a planes.
- **Capacity (Quota):** Límite temporal (ej. 10000 units/day). Indicar ventana (diaria/hora/minuto).
- **Auto-Recharge / Extra charge:** Si hay recarga automática o cargos extra.
- **Max Power (Rate Limit):** Límite de tasa (value) y tipo de throttling (sliding window, token bucket, etc.).
- **Per-Request cost:** Coste por petición en unidades (si aplica).
- **Cooling period:** Tiempo de enfriamiento / backoff recomendado.
- **Segmentation:** Segmentación de límites por usuario/tenant/IP (si aplica).
- **Shared limits:** Si hay límites compartidos entre endpoints/tenants.

---

## API Template

- **Associated SaaS:**
  - Type: TBD (To be determined)
  - (\*) Pricing Configurations: TBD
- **Segmentation:**
  - TBD
- **Capacity (Quota):**
  - Value: TBD
  - Window: TBD
- **Auto-Recharge:** TBD
- **Extra charge:** TBD
- **Max Power (Rate Limit):**
  - Value: TBD
  - Throttling: TBD
- **Per-Request cost:** TBD
- **Cooling period:** TBD
- **Shared limits:** TBD

---

## Analytics and Dashboards

### Quotable (Open Source)

Al ser Open Source y funcionar en base a "best effort", muchos valores no están publicados o son desconocidos (se marca como "Unknown" cuando aplica, similar a la ficha de Spotify).

- **Associated SaaS**
  - Type: Partial SaaS (Data Provider)
  - (\*) Pricing Configurations: 1 (Open Source / Free) — https://github.com/lukePeavey/quotable
- **Segmentation:**
  - Unauthenticated / Anonymous: Yes
- **Capacity (Quota):**
  - Value: Not published
  - Window: Not specified (public global access)
- **Auto-Recharge / Extra charge:**
  - Auto-Recharge: N/A
  - Extra charge: None (open source)
- **Max Power (Rate Limit):**
  - Value: Unknown
  - Throttling: IP Rate Limiting (Protección contra DDoS) / Fair Usage Policy (IP-based)
- **Per-Request cost:** 0 units (Free)
- **Cooling period:** TBD (Depends on IP block duration and ISP; generalmente hasta que se restablezca el bloque de IP)
- **Shared limits:** Yes (Global pool for public access)

Notes: Al ser un servicio público y mantenido como proyecto OSS, no existe un SLA ni garantías de capacidad. Las políticas de uso justo y los límites pueden cambiar sin aviso.

---

### Azure AI Translator

Esta ficha muestra únicamente las características del plan F0 (plan gratuito).

- **Associated SaaS**
  - Type: Partial SaaS (Cloud API)
  - (\*) Pricing Configurations: Multiple (Free F0, S1, etc.) — https://azure.microsoft.com/es-es/pricing/details/cognitive-services/translator/
- **Segmentation:**
  - Authenticated: Yes (API Key required)
- **Capacity (Quota):**
  - Value: 2,000,000 characters / month (Tier F0 sample quota)
  - Window: Monthly
- **Auto-Recharge / Extra charge:**
  - Auto-Recharge: Monthly (se renueva al inicio del ciclo de facturación)
  - Extra charge: None for F0 (service en F0 se detiene o no procesa más requests al alcanzar el límite; en planes pagados se aplican costes según uso)
- **Max Power (Rate Limit):**
  - Value: ~33,300 characters / minute (aprox.; depende del SKU y región)
  - Throttling: Sliding Window (1 min)
- **Per-Request cost:** Variable (depende del número de caracteres por petición y del SKU escogido)
- **Cooling period:**
  - 1 Month (si se excede la cuota mensual — se espera al siguiente ciclo) / 1 Minute (si se excede el rate limit en ventana deslizante)
- **Shared limits:** Subscription scope (los límites y la cuota aplican por suscripción/resource)

---

## Referencias

- Quotable (GitHub): https://github.com/lukePeavey/quotable
- Azure Translator pricing: https://azure.microsoft.com/es-es/pricing/details/cognitive-services/translator/
