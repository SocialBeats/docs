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

### Azure AI Translator (Tier F0)

- **Associated SaaS**
  - Type: Partial SaaS (Cloud API)
  - (\*) Pricing Configurations: Multiple (Free F0, S1, etc.) — https://azure.microsoft.com/es-es/pricing/details/cognitive-services/translator/
- **Segmentation:**
  - Authenticated: Yes (API Key required / Subscription scope)
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

Notes: Azure ofrece límites estrictos y monitorización a nivel de suscripción; para producción recomendamos usar un plan pagado (S1 o superior) y configurar alertas de uso.

---

## Referencias

- Quotable (GitHub): https://github.com/lukePeavey/quotable
- Azure Translator pricing: https://azure.microsoft.com/es-es/pricing/details/cognitive-services/translator/

---

## Beats-interactions

### OpenRouter API (meta-llama/llama-3.2-3b-instruct:free)

Hemos integrado la API de OpenRouter para moderar el contenido de la aplicación. Actualmente se modera el título y descripción de las playlists, los comentarios y el texto de los ratings. Hemos decidido usar la versión gratuita de OpenRouter y el modelo **meta-llama/llama-3.2-3b-instruct:free** (uno de los recomendados por la documentación oficial). Para evitar exceder el límite y ser baneados, hemos implementado un rate-limit algo convervador, hacemos un máximo de 18 requests por minuto y 45 diarias. La forma de funcionamiento es que si algo no se puede moderar en el momento, cuando el SLA lo permita un cronjob lo moderará.

- **Associated SaaS**
  - Type: Partial SaaS (AI Model Gateway / API Aggregator)
  - (\*) Pricing Configurations: Multiple (Free tier, Pay-as-you-go, Enterprise) — https://openrouter.ai/pricing
- **Segmentation:**
  - Authenticated: Yes (API Key required)
  - Model-specific: Yes (Free tier applies only to models with `:free` suffix)
- **Capacity (Quota):**
  - Value: 50 requests / day (accounts without credits)
  - Value: 1,000 requests / day (accounts with ≥ $10 credits purchased)
  - Window: Daily (resets at midnight UTC)
- **Auto-Recharge / Extra charge:**
  - Auto-Recharge: Daily (quota resets automatically each day)
  - Extra charge: None for free models (requests are blocked after reaching daily limit; paid models require credits)
- **Max Power (Rate Limit):**
  - Value: 20 requests / minute (RPM)
  - Throttling: Sliding Window (1 minute)
- **Per-Request cost:** 0 credits (Free model variant)
- **Cooling period:**
  - 1 Minute (if rate limit exceeded — wait for sliding window to reset)
  - 1 Day (if daily quota exceeded — wait for next daily cycle)
  - Note: Failed requests (429 errors) still count toward daily quota
- **Shared limits:** API Key scope (limits apply per API key across all free models with `:free` suffix)

**Additional Notes:**

- **Model:** meta-llama/llama-3.2-3b-instruct:free (3.21B parameters, multilingual, 128K context window)
- **DDoS Protection:** Cloudflare protection active; excessive usage may trigger additional blocks
- **402 Errors:** May occur if account has negative credit balance (even for free models)
- **429 Errors:** Indicates rate limit or quota exceeded; backoff and retry recommended
- **Provider Rate Limiting:** Free-tier models may experience additional rate limiting during peak times at provider level
- **No SLA:** Free tier operates on best-effort basis without service guarantees
- **Upgrade Benefits:** Purchasing ≥ $10 credits increases daily quota from 50 to 1,000 requests
- **API Compatibility:** OpenAI-compatible API format (easy migration/integration)

---

**References:**

- OpenRouter API Documentation: https://openrouter.ai/docs
- Rate Limits Documentation: https://openrouter.ai/docs/limits
- Model Details: https://openrouter.ai/meta-llama/llama-3.2-3b-instruct:free
- Pricing: https://openrouter.ai/pricing

---
