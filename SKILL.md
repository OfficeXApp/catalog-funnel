---
name: catalog-kit
description: |
  Build and manage marketing catalogs, landing pages, and multi-step funnels with your AI agent. Create catalogs from JSON schemas, publish them instantly, run A/B tests with weighted variants, and track visitor analytics — all through conversation.
  Use when: (1) Creating or updating a catalog/funnel/landing page, (2) Checking analytics like visitors, conversions, and drop-off rates, (3) Running A/B tests on different catalog versions, (4) AI-routing visitors to the right catalog variant with natural language hints, (5) Managing API keys for team access, (6) Uploading videos for catalogs, (7) Viewing individual visitor journeys, (8) Reviewing response distributions for form fields, (9) Creating sandboxes to safely edit catalogs without affecting production, (10) Using the element inspector to get exact component references for AI agents, (11) Submitting form data headlessly via the Agent API for AI agent integrations, (12) Uploading and compressing images for fast loading, (13) Authoring catalogs as TypeScript files with full type safety, (14) Uploading and hosting downloadable files (PDFs, ZIPs, docs) with credit-based billing, (15) Building custom interactive UI with the CatalogKit global API bridge (window.CatalogKit) for inline scripts, real-time field access, and multi-form isolation, (16) AI agents can fill out catalog forms step-by-step via the stateful Agent Session API, (17) Configuring advanced Stripe checkout with 3D Secure verification and authorization holds for free trial funnels, (18) Previewing catalogs locally with hot reload before deploying to cloud, (19) Using local file references (images, videos, scripts) that auto-upload to CDN on push, (20) Adding cart and checkout to funnels with built-in cart UI (floating button, slide-out drawer, order summary) and Stripe payment integration, (21) Validating catalog schemas locally before pushing, (22) Scaffolding new catalogs from templates, (23) Diffing local vs remote catalog schemas, (24) Testing real Stripe checkout locally with your own test keys, (25) Monitoring local dev events (page views, field changes, checkout) via SSE stream for AI agent testing.
  Triggers: catalog funnel, catalog kit, funnel builder, landing page, lead capture, create catalog, catalog analytics, conversion funnel, form builder, ab test, catalog api, ai routing, variant routing, hint routing, sandbox, element inspector, devtools, image upload, image compression, webp, typescript, ts config, file upload, file download, downloadable, hosted files, CatalogKit, window.CatalogKit, global api, inline script, html script, custom ui, api bridge, multi-form, agent api, headless form, agent session, form submission api, stripe checkout, 3d secure, 3ds, free trial, payment verification, trial end behavior, billing server, stripe webhooks, local dev, local preview, dev server, hot reload, local assets, local files, catalogs dev, cart, shopping cart, cart sidebar, cart drawer, checkout page, order summary, page offer, offer acceptance, add to cart, cart button, payment page, order bump, upsell, catalog validate, catalog init, catalog diff, catalog open, scaffold template, visibility conditions, debug panel, form state debug, validation overlay, local checkout, stripe test mode, dev events, event stream, local analytics, sse events
---

# Catalog Kit

Build and manage marketing catalogs, landing pages, and multi-step funnels — directly through your AI agent. Create catalogs with 60+ component types, publish them instantly, run A/B tests with weighted variants, and monitor conversion analytics in real time.

> **Install on OfficeX:** [officex.app/store/en/app/catalog-kit](https://officex.app/store/en/app/catalog-kit)

## What You Can Do

- **Create catalogs** — build lead capture forms, product catalogs, multi-step funnels from a JSON schema
- **Publish instantly** — catalogs go live at your subdomain (SUBDOMAIN.catalogkit.cc) or custom domain
- **Check analytics** — see visitors, conversions, page drop-off, field completions, referrer sources, and revenue
- **Run A/B tests** — use weighted variants to split traffic to find what converts best
- **AI variant routing & prefill** — auto-route visitors to the best catalog variant and pre-fill qualifying form fields using natural language hints
- **Sandbox editing** — clone a catalog to safely make changes without affecting the live version, then promote when ready
- **Element inspector** — hold Shift+Alt to hover-inspect any element (page components, top navbar, cart button, cart drawer, cart items, sticky bottom bar, checkout page, popups) and copy its exact `pageId/componentId` reference for AI agents
- **View visitor journeys** — trace exactly what each visitor did step by step
- **Manage access** — create API keys for team members or integrations
- **Managed media hosting** — images and videos are stored, compressed, and served via CDN for you — no need to bring your own S3 bucket
- **Upload images (free)** — automatic WebP compression, thumbnail generation, and CDN delivery at no credit cost
- **Upload videos** — automatic HLS transcoding for adaptive streaming, served via CDN
- **Upload & download files** — host downloadable files (PDFs, ZIPs, docs) on S3 with CDN delivery, credit-billed per 50MB
- **Cart & checkout** — built-in cart UI (floating button + slide-out drawer) that collects page offers and sends them to Stripe Checkout. No custom cart HTML needed
- **Agent API** — AI agents can fill out catalog forms headlessly via the stateful session API, with server-side validation and progressive disclosure
- **TypeScript-as-config** — author catalogs as .ts files with full type safety, then push via CLI
- **Local preview** — `catalogs catalog dev my-catalog.ts` previews locally with hot reload, visibility conditions, debug panel, and validation overlay
- **Dev toolbar** — persistent toolbar at top showing catalog slug, schema version badge (`v1.0`), pages graph, element inspector toggle, debug mode toggle, Stripe status, events viewer, and production link. Minimizable to a floating pill (click to re-expand); state persists across reloads via sessionStorage
- **Local file references** — use `./images/hero.png` in schemas, auto-uploaded to CDN on push
- **Validate locally** — `catalogs catalog validate my-catalog.ts` checks routing, component IDs, orphan pages, reserved page IDs, and more — no token needed
- **Scaffold catalogs** — `catalogs catalog init` creates a new catalog from a template (quiz-funnel, lead-capture, product-catalog, blank)
- **Diff against remote** — `catalogs catalog diff my-catalog.ts` shows structural changes vs the deployed version
- **Open in browser** — `catalogs catalog open my-slug` opens the published catalog URL in your default browser
- **Local Stripe checkout** — add `STRIPE_SECRET_KEY=sk_test_...` and `STRIPE_PUBLISHABLE_KEY=pk_test_...` to your `.env` and the dev server creates real Stripe checkout sessions and inline card fields locally. Keys never leave your machine
- **Local dev events** — page views, field changes, and checkout events stream to your terminal and an SSE endpoint (`/__dev_events_stream`) that AI agents can subscribe to. Zero production pollution
- **Custom JavaScript** — inject custom client-side logic via `html` components with `<script>` tags and the `window.CatalogKit` API bridge
- **Custom HTML components** — render arbitrary HTML/CSS/JS inside catalogs using `type: "html"` components
- **Custom React components** — register React components on `window.__catalogkit_components` for fully custom interactive UI

## Scripting & Custom Logic — Overview

Catalog Kit provides **two scripting systems** for custom logic. Understanding when to use each is critical:

### 1. CatalogKit Global API (`window.CatalogKit`) — inline `<script>` in `html` components

**This is the primary scripting system.** Add an `html` component with a `<script>` tag to any page. The script has full access to the catalog runtime via `window.CatalogKit.get()`.

```json
{
  "id": "my_script",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\nkit.on('beforenext:my_page', async (e) => {\n  // custom logic here\n});\n</script>"
  }
}
```

**Essential pattern:** `type: "html"` → `<script>` tag → `window.CatalogKit.get()` → `kit.on(event, callback)`

**Use for:** server-side validation, dynamic routing, conditional UI, API calls, timers, price calculators, submit interception, custom DOM.

**Key API methods:**
- `kit.getField(id)` / `kit.setField(id, value)` — read/write form fields
- `kit.on('beforenext:pageId', async (e) => { ... })` — intercept navigation, call `e.preventDefault()` to block
- `kit.setValidationError(id, msg)` — show inline error on a field
- `kit.setButtonLoading(bool)` / `kit.setButtonDisabled(bool)` — control the Continue button
- `kit.setComponentProp(id, prop, value)` — dynamically change any component prop (hide, relabel, swap options)
- `kit.getVar(key)` / `kit.setVar(key, value)` — script variables (reactive, available in templates as `{{var:key}}`)
- `kit.getGlobal(key)` / `kit.setGlobal(key, value)` — cross-page globals (persist across navigation)

**Critical mistake to avoid:** `window.CatalogKit` is a **registry**, not an instance. You MUST call `.get()` first:
```javascript
// ✅ CORRECT
const kit = window.CatalogKit.get();
kit.on('pageenter', () => { ... });

// ❌ WRONG — will throw "is not a function"
window.CatalogKit.on('pageenter', () => { ... });
```

### 2. TypeScript page hooks (`on_enter`, `on_change`, `beforenext`) — in `.ts` catalog files

When authoring catalogs as TypeScript, you can write hooks directly on pages as real functions. The CLI serializes them to strings when pushing.

```typescript
pages: {
  checkout: {
    title: "Checkout",
    components: [...],
    hooks: {
      on_enter: (ctx) => {
        ctx.setButtonDisabled(true);
        ctx.fetch("https://api.example.com/check")
          .then(r => r.json())
          .then(data => { ctx.setField("status", data.status); ctx.setButtonDisabled(false); });
      },
      on_change: async (ctx) => {
        // ctx.field_id, ctx.field_value available
      },
    },
  },
}
```

**Use for:** page-level lifecycle hooks in TypeScript catalogs. The `ctx` object has `setField`, `setButtonDisabled`, `setButtonLoading`, `setValidationError`, `setProp`, `fetch`, `field_id`, `field_value`.

### Which to use?

| Scenario | Use |
|---|---|
| Server-side validation before navigation | CatalogKit `beforenext` script |
| Dynamic dropdown options from API | CatalogKit `pageenter` script |
| Conditional show/hide of components | CatalogKit `fieldchange` script |
| Price calculator or live widget | CatalogKit script with DOM manipulation |
| Submit interception to your backend | CatalogKit `submit` script |
| Simple page-enter/change hooks in TS catalogs | TypeScript page hooks |
| Custom React component | Register on `window.__catalogkit_components` |
| Display dynamic data in HTML | Template interpolation: `{{field_id}}`, `{{var:key}}`, `{{global:key}}` (supports dotted nested keys like `{{checkboxId.optionValue.inputId}}`) |

### Custom HTML & display

`html` components serve double duty — they can render **visible HTML/CSS** AND run **invisible scripts**:

```json
{
  "id": "price_display",
  "type": "html",
  "props": {
    "content": "<div style='text-align:center;font-size:32px;font-weight:bold;'>{{var:price}}</div>\n<script>\nconst kit = window.CatalogKit.get();\nkit.on('fieldchange:quantity', () => {\n  kit.setVar('price', '$' + (Number(kit.getField('quantity')) * 29));\n});\n</script>"
  }
}
```

> **Full API reference, events table, and 11 cookbook examples** are in the [CatalogKit Global API](#catalogkit-global-api-windowcatalogkit) section below.

---

## Getting Started

After installing Catalog Kit on OfficeX, you receive credentials automatically. You can also sign up at the dashboard and create API keys from Settings.

```bash
# Your API key (created from Settings page or received on install)
export CATALOG_KIT_TOKEN="cfk_..."
```

The production API URL is **`https://api.catalogkit.cc`** — hardcoded as the default in both the CLI and all SDK examples. You do not need to set an API URL env var. The CLI and REST API both use this URL automatically.

### Authentication

Pass your API key as a Bearer token on all requests:

```bash
curl -H "Authorization: Bearer cfk_..." \
  https://api.catalogkit.cc/api/v1/catalogs
```

If you installed via OfficeX, you can also use your install credentials:

```bash
TOKEN=$(echo -n "${OFFICEX_INSTALL_ID}:${OFFICEX_INSTALL_SECRET}" | base64)
curl -H "Authorization: Bearer $TOKEN" \
  https://api.catalogkit.cc/api/v1/catalogs
```

---

## Current User

Check who you are authenticated as — returns your email, subdomain, custom domain, credits, and auth details.

```
GET https://api.catalogkit.cc/api/v1/me
```

**Response (200):**
```json
{
  "ok": true,
  "data": {
    "user_id": "usr_abc123",
    "email": "you@example.com",
    "username": "yourname",
    "subdomain": "1e6b7940",
    "custom_domain": "shop.example.com",
    "catalog_url": "https://shop.example.com",
    "stripe_key_set": true,
    "stripe_key_last4": "4x7K",
    "credits": 850,
    "officex_connected": true,
    "auth_method": "api_key",
    "api_key_id": "kx9f2",
    "is_superadmin": false,
    "created_at": "2025-01-15T10:30:00.000Z"
  }
}
```

The CLI equivalent is `catalogs whoami`, which prints this same information in a human-readable format.

---

## Settings

### Get settings

```
GET https://api.catalogkit.cc/api/v1/settings
```

**Response (200):**
```json
{
  "ok": true,
  "data": {
    "user_id": "usr_abc123",
    "subdomain": "my-brand",
    "custom_domain": "shop.example.com",
    "stripe_key_set": true,
    "stripe_key_last4": "4x7K"
  }
}
```

### Update settings

```
PUT https://api.catalogkit.cc/api/v1/settings
```

```json
{
  "subdomain": "my-brand",
  "custom_domain": "shop.example.com",
  "stripe_secret_key": "rk_live_..."
}
```

All fields are optional — only include the ones you want to change.

| Field | Description |
|---|---|
| `subdomain` | 2-32 chars, lowercase alphanumeric + hyphens. Your catalogs are served at `<subdomain>.catalogkit.cc`. Must be unique. |
| `custom_domain` | Serve catalogs from your own domain. Requires DNS setup (CNAME + TXT verification record). |
| `stripe_secret_key` | Stripe secret or restricted key for accepting payments. Set to `null` to remove. |

**Response (200):**
```json
{
  "ok": true,
  "data": {
    "user_id": "usr_abc123",
    "subdomain": "my-brand",
    "custom_domain": "shop.example.com",
    "stripe_key_set": true,
    "stripe_key_last4": "4x7K"
  }
}
```

> Changing the subdomain updates all catalog and redirect URLs automatically.

---

## Managing Catalogs

### List your catalogs

```
GET https://api.catalogkit.cc/api/v1/catalogs
```

**Query params:** `limit` (default 50, max 200), `cursor` (opaque pagination token), `include_sandboxes` (`true` to include sandbox catalogs)

Supports cursor-based pagination for accounts with many catalogs. When more results are available, the response includes a `cursor` value — pass it as `?cursor=...` to fetch the next page. When `cursor` is `null`, you've reached the end.

**Response:**
```json
{
  "ok": true,
  "data": [
    {
      "catalog_id": "01HXY...",
      "slug": "my-funnel",
      "name": "My Funnel",
      "status": "published",
      "visibility": "public",
      "version": "1.0.3",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "cursor": "eyJwayI6Ik..."
}
```

### Create a catalog

```
POST https://api.catalogkit.cc/api/v1/catalogs
```

```json
{
  "slug": "spring-sale",
  "name": "Spring Sale Landing Page",
  "schema": { ... },
  "status": "published",
  "visibility": "public",
  "version": "1.0.0-initial"
}
```

- `slug` — URL-friendly name (lowercase, hyphens). Your catalog will be live at your configured domain
- `status` — `"published"` (live) or `"draft"` (hidden). Default: `"published"`
- `visibility` — `"public"` (listed) or `"unlisted"` (link-only). Default: `"unlisted"`
- `version` — Semver string (e.g. `"1.0.0"` or `"1.0.0-launch"`). Defaults to `"1.0.0"` if omitted. Always returned in responses.

**Response (201):**
```json
{
  "ok": true,
  "data": {
    "catalog_id": "01HXY...",
    "slug": "spring-sale",
    "name": "Spring Sale Landing Page",
    "status": "published",
    "visibility": "public",
    "version": "1.0.0-initial",
    "url": "https://SUBDOMAIN.catalogkit.cc/spring-sale"
  }
}
```

### View a catalog

```
GET https://api.catalogkit.cc/api/v1/catalogs/:id
```

Returns the full catalog including its schema.

### Update a catalog

```
PUT https://api.catalogkit.cc/api/v1/catalogs/:id
```

All fields are optional — only send what you want to change:

```json
{
  "name": "Updated Name",
  "schema": { ... },
  "status": "draft",
  "visibility": "public",
  "slug": "new-slug",
  "old_slug_action": "redirect",
  "version": "2.0.0-redesign"
}
```

When changing the slug, `old_slug_action` controls what happens to the old URL:
- `"redirect"` (default) — old URL redirects to the new one
- `"release"` — old URL becomes available for reuse

**Versioning:** Every update auto-bumps the patch version (e.g. `1.0.0` → `1.0.1`). To set a specific version with a label, pass `version` explicitly (e.g. `"2.0.0-black-friday"`). The server always returns the new version in the response.

### Delete a catalog

```
DELETE https://api.catalogkit.cc/api/v1/catalogs/:id
```

### Cache Busting

Public catalog URLs support a `_cb` query parameter to control caching:

| Parameter | Behavior |
|---|---|
| `?_cb=latest` | Always fetches fresh from database, skips all caches. Use after publishing to verify changes instantly. |
| `?_cb=1.0.3` | Skips cache only if the cached version doesn't match `1.0.3`. Efficient for pinning to a known version. |
| *(no param)* | Normal caching — 30s fresh + 60s stale-while-revalidate. Best for visitor performance. |

**Example:** After updating your catalog, verify changes are live:
```
GET https://SUBDOMAIN.catalogkit.cc/spring-sale?_cb=latest
```

The public catalog response always includes `version` so you can confirm which version is being served:
```json
{
  "ok": true,
  "data": {
    "catalog_id": "01HXY...",
    "version": "1.0.4-spring-update",
    "schema": { ... }
  }
}
```

---

## Analytics & Results

All analytics endpoints require authentication. Each analytics call costs **1 credit**. Event tracking (visitor activity) is **free**.

### Overview metrics

```
GET https://api.catalogkit.cc/api/v1/analytics/catalogs/:id
```

**Query params:** `start`, `end` (ISO dates, e.g. `2024-01-01`)

Returns aggregate metrics: unique visitors, total page views, form submissions, conversion rate, page-level views, variant breakdown, referrer sources, checkout stats, and revenue.

### Timeseries (daily/hourly trends)

```
GET https://api.catalogkit.cc/api/v1/analytics/catalogs/:id/timeseries
```

**Query params (required):** `start`, `end` (ISO dates), `interval` (`day` or `hour`)

```json
{
  "ok": true,
  "data": [
    { "date": "2024-01-01", "page_views": 150, "sessions": 80, "form_submits": 25, "checkout_completes": 5, "revenue_cents": 4900 }
  ]
}
```

### Drop-off analysis

See exactly where visitors abandon your funnel:

```
GET https://api.catalogkit.cc/api/v1/analytics/catalogs/:id/dropoff
```

**Query params:** `start`, `end` (ISO dates)

```json
{
  "ok": true,
  "data": {
    "total_visitors": 500,
    "pages": [
      { "page_id": "intro", "visitors": 500, "drop_off_rate": 0 },
      { "page_id": "questions", "visitors": 350, "drop_off_rate": 30 }
    ],
    "fields": [
      { "field_id": "questions/email", "completions": 300, "completion_rate": 85.7 }
    ]
  }
}
```

### Response distributions

See how visitors answered each question or form field:

```
GET https://api.catalogkit.cc/api/v1/analytics/catalogs/:id/responses
```

**Query params:** `start`, `end`, `page_id`, `component_id` (all optional)

```json
{
  "ok": true,
  "data": {
    "components": {
      "questions/q1": {
        "total_responses": 200,
        "distribution": {
          "Option A": { "count": 112, "percent": 56 },
          "Option B": { "count": 28, "percent": 14 },
          "Option C": { "count": 60, "percent": 30 }
        }
      }
    }
  }
}
```

### Raw events

Browse individual visitor events with filtering:

```
GET https://api.catalogkit.cc/api/v1/analytics/catalogs/:id/events
```

**Query params:** `start`, `end`, `cursor`, `limit` (default 100, max 5000), `event_type`, `page_id`, `component_id`, `variant_slug`, `utm_source`, `utm_medium`, `utm_campaign`, `referrer`

Response includes a `cursor` for pagination (null when done).

### Visitor journey

Trace a single visitor's complete journey through your catalog:

```
GET https://api.catalogkit.cc/api/v1/analytics/tracers/:tracerId
```

Returns every event in chronological order with a summary: total events, first/last seen, pages viewed, and whether they submitted.

---

## Agent API (Headless Form Submission)

AI agents can interact with catalogs programmatically without a browser. The Agent API provides a stateful session that walks through the catalog page-by-page, with server-side validation and progressive field disclosure.

### Start a session

```
POST https://api.catalogkit.cc/agent/v1/sessions
```

```json
{
  "session_id": "my-unique-session-id",
  "catalog_slug": "enterprise-demo",
  "user_id": "usr_abc123"
}
```

**Response:**
```json
{
  "ok": true,
  "data": {
    "session_id": "my-unique-session-id",
    "status": "active",
    "step": {
      "page_id": "qualification",
      "title": "Tell us about your company",
      "agent_context": "Qualify the lead before showing pricing",
      "fields": [
        {
          "id": "company_name",
          "type": "short_text",
          "label": "Company Name",
          "required": true,
          "agent_hint": "The legal entity name"
        },
        {
          "id": "industry",
          "type": "dropdown",
          "label": "Industry",
          "required": true,
          "options": [
            { "value": "saas", "label": "SaaS" },
            { "value": "healthcare", "label": "Healthcare" }
          ]
        }
      ],
      "disclosed": [
        { "type": "heading", "text": "Enterprise Demo Request" }
      ]
    }
  }
}
```

### Advance to next step

```
POST https://api.catalogkit.cc/agent/v1/sessions/{session_id}/advance
```

```json
{
  "page_id": "qualification",
  "answers": {
    "company_name": "Acme Corp",
    "industry": "healthcare"
  }
}
```

**Response (next step):**
```json
{
  "ok": true,
  "data": {
    "step": {
      "page_id": "pricing",
      "title": "Choose your plan",
      "fields": [
        {
          "id": "selected_plan",
          "type": "dropdown",
          "label": "Plan",
          "required": true,
          "options": [
            { "value": "pro", "label": "Pro - $99/mo" },
            { "value": "enterprise", "label": "Enterprise - Custom" }
          ]
        }
      ]
    }
  }
}
```

**Response (complete):**
```json
{
  "ok": true,
  "data": {
    "complete": true,
    "form_state": { "company_name": "Acme Corp", "industry": "healthcare", "selected_plan": "pro" }
  }
}
```

### Check session status

```
GET https://api.catalogkit.cc/agent/v1/sessions/{session_id}
```

### Key behaviors

- **Idempotent**: Resuming with an existing `session_id` returns the current step (no duplicate sessions)
- **Server-gated**: Only the current page's fields are returned — pricing and later pages are hidden until conditions are met
- **Validated**: Answers are validated server-side (required fields, email format, number ranges, etc.)
- **Progressive**: Conditional fields and routing work the same as the browser UI — the server evaluates visibility and routing conditions
- **Sessions expire** after 1 hour of inactivity (TTL refreshed on each advance)

### Agent-friendly schema fields

When creating catalogs, add these optional fields to improve AI agent interactions:

- `agent` on catalog root — `{ enabled: true, description: "What this catalog does" }`
- `agent_context` on pages — explains the page's purpose to agents
- `agent_hint` on components — explains what a field means semantically

---

## A/B Testing with Weighted Variants

Test different versions of your catalog by adding weighted variants to your schema. Set `variant_routing: "random"` for weighted random routing, `"hint"` for AI-based routing, or `"hybrid"` for both.

```json
{
  "schema": {
    "variant_routing": "random",
    "variants": [
      { "id": "v1", "slug": "control", "weight": 50, "description": "Original" },
      { "id": "v2", "slug": "new-headline", "weight": 50, "description": "New headline" },
      { "id": "v3", "slug": "old-test", "weight": 50, "enabled": false, "description": "Paused variant" }
    ]
  }
}
```

Variants with `target_slug` route visitors to a different catalog entirely. Variants without `target_slug` apply personalization hints within the same catalog. Set `enabled: false` to exclude a variant from all routing (hint, random, hybrid) without deleting it — useful for pausing underperforming A/B test arms. Direct URL access (`/slug/variant-slug`) still works for disabled variants so existing links don't break.

---

## Personalization Architecture: Variants vs Dynamic Offers

**Q: Can I dynamically choose which offers to show each visitor based on `?hint=` or other signals?**

Catalog Kit uses a **static variants + dynamic routing** model. The AI picks *which variant* to show (via `?hint=`), but each variant defines a fixed, predetermined sequence of pages and offers. This is intentional — here's why:

### How it works

1. **Visitor arrives** with `?hint="startup founder looking for analytics"` (or any natural language)
2. **AI routing** (sub-400ms) reads the hint + variant descriptions and picks the best-fit variant
3. **Variant activates** — the visitor sees a fully predetermined funnel with specific pages, offers, and copy
4. **`__variants` personalization** — within that variant, individual component props (headlines, images, descriptions) can further adapt based on hint values

### Why not fully dynamic offer selection?

| Concern | Why static variants win |
|---|---|
| **Pricing integrity** | Each variant maps to known Stripe price IDs. Dynamic assembly risks showing wrong prices or incompatible bundles. |
| **Auditability** | "Why did this visitor see offer X?" is answerable: they hit variant B, which shows offers 1→2→3. No LLM forensics needed. |
| **Compliance** | OfficeX customers set credit rate limits. Predictable offer sequences make spend auditable and controllable. |
| **Performance** | One LLM call for variant routing is fast (~400ms). Adding per-page offer selection would double latency and cost. |
| **Testing** | Each variant is independently testable with known conversion metrics. Dynamic offers make A/B analysis statistically unreliable. |

### What to do instead

- **Add more variants** — 4–8 variants with AI hint routing covers most personalization needs, and each is a fully testable funnel
- **Use `__variants` on component props** — vary headlines, images, copy, even offer titles/prices per hint value within a single catalog structure
- **Use `target_slug`** — A/B test entirely different catalog structures by routing variants to separate catalogs
- **Use conditional routing edges** — route visitors to different pages based on `url_param`, `field`, `hint`, `score`, or `video` conditions

### The mental model

Think of it as: **AI chooses the path, but each path is a paved road — not generated on the fly.** This gives you the personalization benefits of dynamic content with the reliability and measurability of static funnels.

```
?hint="enterprise CTO"
    → AI routes to variant "enterprise"
        → Page 1: enterprise headline + ROI calculator offer
        → Page 2: annual pricing offer (predetermined Stripe price)
        → Page 3: enterprise checkout

?hint="solo freelancer"
    → AI routes to variant "freelancer"
        → Page 1: freelancer headline + quick-start offer
        → Page 2: monthly pricing offer (different Stripe price)
        → Page 3: simple checkout
```

Both paths are fully defined in the catalog schema. The AI just picks which one fits.

---

## Schema Introspection

Get a map of all pages and components in a catalog — useful for understanding the structure before querying analytics:

```
GET https://api.catalogkit.cc/api/v1/catalogs/:id/schema/ids
```

```json
{
  "pages": {
    "landing": { "title": "Get Started", "index": 0 },
    "details": { "title": "Your Details", "index": 1 }
  },
  "components": {
    "landing/email": { "type": "email", "label": "Your Email", "required": true },
    "landing/company": { "type": "short_text", "label": "Company Name" }
  },
  "routing_entry": "landing"
}
```

---

## API Keys

Manage API keys for team members or integrations.

- `POST /api/v1/api-keys` — Create a key (roles: `reader`, `editor`, `admin`, `custom`). Returns the secret once — store it securely.
- `GET /api/v1/api-keys` — List all keys (secrets redacted). Auto-paginates internally to return all keys.
- `DELETE /api/v1/api-keys/:keyId` — Revoke a key
- `POST /api/v1/api-keys/:keyId/rotate` — Rotate: revokes old key, creates new one with same config

---

## Media Hosting

Catalog Kit includes **managed media storage** — you do not need to bring your own S3 bucket or CDN. Upload images and videos through the API, and we handle storage, compression, transcoding, and CDN delivery automatically. All media URLs returned are production-ready and can be used directly as `src` values in your catalog components.

---

## Images

Upload images with automatic compression to WebP for fast loading. Image uploads are **free** (no credits charged) — compression happens automatically via a background Lambda and files are served through our CDN.

### Upload an image

```
POST https://api.catalogkit.cc/api/v1/images/upload
```

```json
{
  "filename": "hero-banner.png",
  "content_type": "image/png",
  "size_bytes": 2500000,
  "no_compress": false
}
```

**Response (201):**
```json
{
  "ok": true,
  "data": {
    "image_id": "01ABC...",
    "upload_url": "https://s3.amazonaws.com/...",
    "original_url": "https://cdn.../media/images/original/...",
    "compressed_url": "https://cdn.../media/images/compressed/...webp",
    "thumbnail_url": "https://cdn.../media/images/compressed/...thumb.webp",
    "no_compress": false
  }
}
```

Upload the file to the presigned `upload_url` using a **PUT** request. You **must** set the `Content-Type` header to match the `content_type` you provided in the upload request (e.g. `image/png`). The presigned URL is signed for that specific content type — mismatched headers will be rejected by S3, and omitting it may cause browsers to block the image (ORB / CORB errors).

```
PUT <upload_url>
Content-Type: image/png   ← must match the content_type from the upload request
<binary image body>
```

Compression happens automatically — use `compressed_url` as the `src` in your image components.

### Check compression status

```
GET https://api.catalogkit.cc/api/v1/images/:imageId/status
```

### List images

```
GET https://api.catalogkit.cc/api/v1/images
```

**Query params:** `limit` (default 50, max 200), `cursor` (opaque pagination token)

Supports cursor-based pagination. Pass `cursor` from the previous response to fetch the next page. `cursor` is `null` when there are no more results.

### Opt-out of compression

Set `"no_compress": true` in the upload request. The original URL is used directly.

### Compression details

- **Output format**: WebP (best compression, universal browser support)
- **Max size**: 2048px width (aspect ratio preserved, no upscaling)
- **Thumbnail**: 400px width, quality 70
- **Supported input**: JPEG, PNG, GIF, WebP, TIFF, BMP, AVIF, HEIC/HEIF
- **Cost**: Free (no credits charged)
- **Originals**: Auto-deleted after 1 year (compressed versions persist)

---

## Videos

Upload video content to your managed media bucket with automatic HLS transcoding for adaptive streaming. Videos are served via CDN — no external hosting needed.

- `POST /api/v1/videos/upload` — Get a presigned upload URL (credits charged per 100MB)
- `POST /api/v1/videos/:videoId/transcode` — Start HLS transcoding (credits charged per estimated minute)
- `GET /api/v1/videos/:videoId/status` — Check transcoding progress and get the playback URL

---

## Files

Upload and host downloadable files (PDFs, ZIPs, documents, etc.) on managed S3 storage with CDN delivery. Files are scoped per-user and billed at **1 credit per 50MB** (minimum 1 credit). Files are retained for **1 year**.

### Upload a file

```
POST https://api.catalogkit.cc/api/v1/files/upload
```

```json
{
  "filename": "pricing-guide.pdf",
  "content_type": "application/pdf",
  "size_bytes": 5000000
}
```

**Response (201):**
```json
{
  "ok": true,
  "data": {
    "file_id": "01ABC...",
    "upload_url": "https://s3.amazonaws.com/...",
    "cdn_url": "https://cdn.../media/files/...",
    "filename": "pricing-guide.pdf",
    "size_bytes": 5000000,
    "credits_charged": 1
  }
}
```

Upload the file using the presigned `upload_url` (PUT request with the file body). Use the `cdn_url` as the `src` in a `file_download` display component.

### Get download URL

```
GET https://api.catalogkit.cc/api/v1/files/:fileId/download
```

Returns a presigned download URL (1-hour expiry) with `Content-Disposition: attachment` for browser download.

### List files

```
GET https://api.catalogkit.cc/api/v1/files
```

**Query params:** `limit` (default 50, max 200), `cursor` (opaque pagination token)

Supports cursor-based pagination. Pass `cursor` from the previous response to fetch the next page. `cursor` is `null` when there are no more results.

### File Download Component

Use the `file_download` display component to render a download button in your catalog:

```json
{
  "id": "download_guide",
  "type": "file_download",
  "props": {
    "src": "https://cdn.../media/files/user123/fileId/pricing-guide.pdf",
    "filename": "Pricing Guide.pdf",
    "size_bytes": 5000000,
    "button_text": "Download",
    "style": "primary",
    "description": "Complete pricing breakdown"
  }
}
```

Props: `src` (required), `filename` (required), `size_bytes`, `button_text`, `style` ("primary" | "secondary" | "outline" | "ghost"), `description`, `icon`.

The download opens in a new tab to prevent losing form progress on mobile.

---

## Webhooks

If your catalog has a `webhook_url` configured in its schema, all visitor events are forwarded there in real time. Each webhook payload includes an `event_id` (ULID) for deduplication and `schema_ref` with human-readable page/component context.

> **Important:** `webhook_url` is for **passive event forwarding only** — it fires asynchronously after events occur and does NOT block page navigation. If you need to validate form data against your server before allowing the user to proceed (e.g., check if an email is already registered, verify a wallet address, or run custom eligibility logic), you must use a **CatalogKit `beforenext` script** instead. See [Server-Side Form Validation](#server-side-form-validation-common-pattern) below.

---

## Server-Side Form Validation (Common Pattern)

The platform does **not** automatically call your server when the user clicks Continue. Client-side validation (required fields, format checks) runs automatically, but any custom server-side validation must be wired up by the developer using the CatalogKit scripting bridge.

**If your server returns an error (e.g., HTTP 422 with a rejection reason) but no error message appears on the frontend, this is the fix:**

Add an `html` component with an inline `<script>` to the page that needs server validation:

```json
{
  "id": "server_validator",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('beforenext:YOUR_PAGE_ID', async (e) => {\n  kit.setButtonLoading(true);\n  try {\n    const res = await fetch('https://your-api.com/validate', {\n      method: 'POST',\n      headers: { 'Content-Type': 'application/json' },\n      body: JSON.stringify({\n        email: kit.getField('email'),\n        name: kit.getField('name'),\n      }),\n    });\n    const data = await res.json();\n\n    if (!res.ok || data.status === 'rejected') {\n      // Show the server's error message on a specific field\n      kit.setValidationError('email', data.reason || data.error || 'Validation failed');\n      e.preventDefault(); // Block navigation — user stays on this page\n    }\n  } catch (err) {\n    kit.setValidationError('email', 'Network error — please try again');\n    e.preventDefault();\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
  }
}
```

**Key requirements:**
- **`e.preventDefault()`** — without this, the page navigates away even if validation fails
- **`kit.setValidationError(componentId, message)`** — displays the error inline above the field; pass `null` to clear it later
- **`kit.setButtonLoading(true/false)`** — shows a spinner on Continue while the server call is in-flight
- **Scoped event** — use `beforenext:YOUR_PAGE_ID` (not just `beforenext`) to only run on the relevant page

> **See also:** The [CatalogKit Global API](#catalogkit-global-api-windowcatalogkit) section has more advanced examples including dynamic routing based on server response, real-time blur validation, and conditional UI.

---

## Variant Analytics

Every catalog gets its `catalog_id` (e.g. `catalog_550e8400-e29b-41d4-a716-446655440000`) automatically added as a tag. To compare analytics across catalog variants (e.g. for A/B tests), add the base catalog's `catalog_id` to each variant's `schema.tags`. API keys scoped with matching `tag_patterns` can then query analytics across all tagged variants.

---

## Catalog Schema Reference

A catalog schema defines your entire funnel as JSON. Here's a minimal lead capture example:

```json
{
  "slug": "lead-capture",
  "pages": [
    {
      "id": "landing",
      "title": "Get Started",
      "components": [
        { "id": "name", "type": "short_text", "label": "Your Name", "required": true },
        { "id": "email", "type": "email", "label": "Email", "required": true }
      ],
      "submit_label": "Submit"
    }
  ],
  "routing": { "entry": "landing", "edges": [] }
}
```

**Reserved page IDs:** Do not use `checkout`, `__checkout`, `__global`, `submitted`, or `__submitted` as page IDs — these are reserved for CatalogKit's built-in checkout, inspector, and submission systems. Avoid `__`-prefixed IDs in general. The CLI `catalog validate` command checks for this.

### Theme

Set theme options under `settings.theme`:

- `primary_color` (required) — hex color for buttons, accents, active states
- `font` — Google Font family name (e.g. `"Inter"`)
- `font_size` — base font size for body text and inputs in rem. Default: `1` (16px). Use `1.125` for 18px, `1.25` for 20px
- `mode` — `"light"` (default) or `"dark"`
- `border_radius` — global border radius in px
- `background_image` — URL for cover page background
- `background_color` — hex color for page background
- `background_overlay` — `"dark"`, `"light"`, `"none"`, or a number 0–1

### Component Types (61 total)

**Input (27):** `short_text`, `long_text`, `rich_text`, `email`, `phone`, `url`, `password`, `number`, `currency`, `date`, `datetime`, `time`, `date_range`, `dropdown`, `multiselect`, `multiple_choice`, `checkboxes`, `picture_choice`, `star_rating`, `slider`, `file_upload`, `signature`, `address`, `location`, `switch`, `checkbox`, `choice_matrix`, `ranking`, `opinion_scale`

**Display (16):** `heading`, `paragraph`, `banner`, `image`, `video`, `pdf_viewer`, `file_download`, `social_links`, `html`, `divider`, `faq`, `testimonial`, `pricing_card`, `timeline`, `iframe`, `modal`, `custom`

**Layout (3):** `section_collapse`, `table`, `subform`

**Page features:** `payment`, `captcha`

### Component-Level Visibility (all component types)

Every component (display AND input) supports these top-level fields for controlling visibility:

| Field | Type | Description |
|---|---|---|
| `hidden` | `boolean` | Statically hide the component — works on ALL types including display (`callout`, `image`, `banner`, etc.) |
| `visibility` | `ConditionGroup` | Dynamically show/hide based on form state, URL params, or hints — works on ALL types |

Use `hidden: true` for simple on/off. Use `visibility` for conditional logic. Both work on display and input components alike. Hidden components are excluded from validation.

### Shared Input Props

All input components support these base props for labels, help text, and validation:

| Prop | Type | Description |
|---|---|---|
| `label` | `string` | Main label displayed above the input |
| `sublabel` | `string` | Smaller secondary text below the main label (alias: `subheading`) |
| `description` | `string` | Helper text below the sublabel, lighter styling |
| `tooltip` | `string` | Info icon (ⓘ) next to label — hover/tap shows explanatory popover |
| `required` | `boolean` | Marks field as required (red asterisk) |
| `placeholder` | `string` | Placeholder text inside the input |
| `hidden` | `boolean` | Hides the field from the UI (legacy — prefer component-level `hidden` instead) |
| `copyable` | `boolean` | Show a copy-to-clipboard icon next to the input. Works on editable inputs (short_text, long_text, rich_text, email, phone, url, number, currency, date, datetime, time, password, dropdown, address). The icon appears once the field has a value. |

Example with all label props:
```json
{
  "id": "tg_username",
  "type": "short_text",
  "props": {
    "label": "Your Telegram Username",
    "sublabel": "We'll use this to add you to the team group",
    "tooltip": "Go to Telegram Settings > Username to find or set yours",
    "placeholder": "@username",
    "required": true
  }
}
```

#### Text Input Props (`short_text` & `long_text`)

Both `short_text` (single-line) and `long_text` (multi-line textarea) share these additional props on top of the shared input props above:

| Prop | Type | Default | Description |
|---|---|---|---|
| `min_length` | `number` | — | Minimum character count |
| `max_length` | `number` | — | Maximum character count |
| `default_value` | `string` | — | Pre-filled default value |
| `disabled` | `boolean` | `false` | Greys out the input, not interactive |
| `readonly` | `boolean` | `false` | Read-only with copy-to-clipboard button |

**`long_text`-only props (textarea):**

| Prop | Type | Default | Description |
|---|---|---|---|
| `rows` | `number` | `4` | Number of visible text rows (controls initial height) |
| `resize` | `string` | `"vertical"` | Whether the textarea is draggable to resize. Options: `"vertical"`, `"horizontal"`, `"both"`, `"none"` |

Example — a feedback textarea with 6 rows, no resize:
```json
{
  "id": "feedback",
  "type": "long_text",
  "props": {
    "label": "Your Feedback",
    "sublabel": "Tell us what you think",
    "tooltip": "Be as detailed as you like",
    "placeholder": "Write your thoughts here...",
    "required": true,
    "rows": 6,
    "resize": "none",
    "max_length": 2000
  }
}
```

### Other Option (free-text "Other, please specify")

Choice components (`multiple_choice`, `checkboxes`, `dropdown`) support an optional "Other" entry that lets visitors type a custom answer.

| Prop | Type | Default | Description |
|---|---|---|---|
| `other_option` | `boolean` | `false` | Appends an "Other" choice. Selecting it reveals a text input. |
| `other_label` | `string` | `"Other"` | Custom label for the "Other" button. |
| `other_placeholder` | `string` | — | Placeholder for the free-text input. |
| `require_all` | `boolean` | `false` | (checkboxes/multiple_choice) Require ALL options to be selected. When combined with `required: true` and `require_all_fields`, the button stays disabled until every option is checked and every nested required input is filled. |

Value is stored as `__other__:<text>`. **Do not set `other_option: true` unless you intentionally want a free-text fallback** — otherwise an unexpected textarea will render.

### Disabled Options

Individual options in `multiple_choice`, `checkboxes`, `dropdown`, and `picture_choice` can be marked as `disabled: true`. Disabled options are visible but not selectable — rendered at 50% opacity with `cursor-not-allowed`. Useful for hinting at future features or "coming soon" tiers.

```json
{
  "options": [
    { "value": "starter", "label": "Starter — Free" },
    { "value": "pro", "label": "Pro — $29/mo" },
    { "value": "enterprise", "label": "Enterprise — Coming Soon", "disabled": true }
  ]
}
```

### Picture Choice Component

Visual option picker with image cards. Each option has an image, label, and value. Supports single or multi-select.

```json
{
  "id": "platform",
  "type": "picture_choice",
  "props": {
    "label": "Select your platform",
    "required": true,
    "image_fit": "contain",
    "options": [
      { "label": "X (Twitter)", "value": "twitter", "image": "https://example.com/x-logo.png" },
      { "label": "LinkedIn", "value": "linkedin", "image": "https://example.com/linkedin-logo.png" },
      { "label": "Reddit", "value": "reddit", "image": "https://example.com/reddit-logo.png" }
    ]
  }
}
```

| Prop | Type | Default | Description |
|---|---|---|---|
| `options` | `array` | `[]` | Array of `{ label, value, image }` objects. `image` is a URL |
| `multiple` | `boolean` | `false` | Allow selecting more than one option |
| `image_fit` | `"contain"` / `"cover"` | `"contain"` | How images fit within the card. `contain` shows the full image with padding (safe default for mixed aspect ratios — logos, icons, photos). `cover` crops to fill the card (use only when all images share similar aspect ratios) |

**Choosing `image_fit`:** Use the default `"contain"` for logos, icons, or any set of images with varying dimensions — it guarantees every image is fully visible. Only switch to `"cover"` when all images are photos or illustrations with a consistent landscape aspect ratio.

### Image Component

The `image` display component renders an image with sensible defaults (full-width, cover fit) that can be fully overridden via CSS.

```json
{
  "id": "hero_img",
  "type": "image",
  "props": {
    "src": "https://cdn.example.com/hero.webp",
    "alt": "Hero banner",
    "border_radius": 16,
    "link": "https://example.com"
  }
}
```

| Property | Type | Default | Description |
|---|---|---|---|
| `src` | string | *(required)* | Image URL — use `compressed_url` from the image upload API for auto-optimized WebP |
| `alt` | string | `""` | Alt text for accessibility |
| `width` | number/string | — | HTML width attribute (helps prevent layout shift) |
| `height` | number/string | — | HTML height attribute (helps prevent layout shift) |
| `border_radius` | number | `16` | Border radius in px |
| `link` | string | — | Wraps the image in a clickable link (opens in new tab) |
| `use_original` | boolean | `false` | Use the original (uncompressed) image URL instead of the compressed version |

#### Custom Image Styling via CSS

By default, images are full-width with `object-fit: cover`. To customize sizing, positioning, or responsive behavior, use `settings.custom_css` and target images by their component `id`.

Every image component's wrapper `div` gets `id="{componentId}"`, so you can target the `<img>` tag with `#componentId img`.

**Example: Centered image with max-width and responsive breakpoints**

```json
{
  "settings": {
    "custom_css": "#hero_img img { max-width: 600px; margin: 0 auto; object-fit: contain; } @media (max-width: 768px) { #hero_img img { max-width: 100%; } }"
  },
  "pages": [
    {
      "id": "landing",
      "components": [
        { "id": "hero_img", "type": "image", "props": { "src": "https://cdn.example.com/hero.webp" } }
      ]
    }
  ]
}
```

**Example: Side-by-side images with different sizes on mobile vs desktop**

```json
{
  "settings": {
    "custom_css": "#photo_a img, #photo_b img { object-fit: contain; height: 200px; } @media (min-width: 768px) { #photo_a img, #photo_b img { height: 350px; } }"
  },
  "pages": [
    {
      "id": "gallery",
      "components": [
        { "id": "photo_a", "type": "image", "width": "half", "props": { "src": "https://cdn.example.com/a.webp" } },
        { "id": "photo_b", "type": "image", "width": "half", "props": { "src": "https://cdn.example.com/b.webp" } }
      ]
    }
  ]
}
```

**Common CSS overrides:**

| CSS Property | Example | Purpose |
|---|---|---|
| `max-width` | `max-width: 400px` | Limit image width (centered with `margin: 0 auto`) |
| `object-fit` | `object-fit: contain` | Show full image without cropping (default is `cover`) |
| `height` | `height: 300px` | Fixed height (combine with `object-fit`) |
| `border-radius` | `border-radius: 50%` | CSS override for border radius (e.g. circular) |
| `opacity` | `opacity: 0.8` | Semi-transparent image |
| `filter` | `filter: grayscale(100%)` | Apply CSS filters |

> **Tip:** The default `.ck-img` class sets `width: 100%; height: auto; object-fit: cover`. Any `#id img` selector has higher specificity and overrides these without needing `!important`.

### Heading Component

The `heading` display component supports three text levels:

```json
{
  "id": "hero",
  "type": "heading",
  "props": {
    "micro_heading": "Welcome to the program",
    "text": "Heading Title",
    "subtitle": "Supporting text below the heading",
    "level": 1,
    "align": "left"
  }
}
```

| Property | Type | Default | Description |
|---|---|---|---|
| `text` | string | *(required)* | Main heading text |
| `level` | 1–6 | `1` | HTML heading level (h1–h6), controls size |
| `micro_heading` | string | — | Small uppercase eyebrow text above the heading |
| `subtitle` | string | — | Supporting text below the heading |
| `align` | `"left"` / `"center"` / `"right"` | `"left"` | Text alignment |

Stack all three for a complete heading block: micro heading (small, uppercase), main heading (bold), and subtitle (lighter).

### Page Actions & CTA Buttons

Page action buttons (and the default submit/continue button) support `side_statement` and `reassurance` text to increase conversion:

**On page actions:**

```json
{
  "actions": [
    {
      "id": "cta",
      "label": "Get Started Now",
      "style": "primary",
      "side_statement": "No credit card required",
      "reassurance": "Cancel anytime. 30-day money back guarantee."
    }
  ]
}
```

**On the default submit button:**

```json
{
  "title": "Your Details",
  "submit_label": "Continue",
  "submit_side_statement": "Takes only 2 minutes",
  "submit_reassurance": "Your information is secure and never shared.",
  "components": [...]
}
```

| Property | Type | Description |
|---|---|---|
| `side_statement` | string | Text shown inline to the right of the button |
| `reassurance` | string | Small muted text shown below the button |
| `submit_side_statement` | string | Same as `side_statement` but for the default submit button (page-level) |
| `submit_reassurance` | string | Same as `reassurance` but for the default submit button (page-level) |
| `button_disabled_message` | string | Error message shown when clicking a disabled button (default: "Please fill in all required fields"). Used with `require_all_fields` or script-disabled buttons |

### Embedded Buttons

Add inline buttons to `multiple_choice`, `checkboxes`, `timeline`, and checkout cart items. Buttons render alongside each option or timeline item — useful for "check the box after opening this link" patterns. Cart items support a `button` for side links (e.g. "View Details"). Timeline items also support `side_button` which renders inline with the title (top-right of the card) instead of below the description.

**On choice options** (`multiple_choice` / `checkboxes`):

```json
{
  "id": "checklist",
  "type": "checkboxes",
  "props": {
    "label": "Complete These Steps",
    "options": [
      {
        "value": "download",
        "label": "Download Telegram",
        "button": { "label": "Open Telegram", "url": "https://t.me/download", "style": "primary", "size": "sm" }
      },
      {
        "value": "message",
        "label": "Message Coach AI",
        "button": { "label": "Open Chat", "url": "https://t.me/coach_bot", "target": "_blank", "icon": "💬" }
      }
    ]
  }
}
```

**On timeline items:**

```json
{
  "id": "steps",
  "type": "timeline",
  "props": {
    "items": [
      {
        "title": "Open Setter Coach AI",
        "description": "Your AI assistant walks you through Day 1.",
        "button": { "label": "Open Chat", "url": "https://t.me/coach_bot", "style": "primary", "size": "sm" },
        "checkbox": true
      },
      {
        "title": "Join Call Center",
        "description": "Get access to the team channel.",
        "button": { "label": "Join Channel", "url": "https://t.me/channel", "style": "outline" },
        "side_button": { "label": "Preview", "url": "https://t.me/channel/preview", "style": "ghost", "size": "sm" },
        "checkbox": { "label": "Joined" }
      }
    ]
  }
}
```

**On checkout cart items (via page offer):**

Cart items support an optional `button` that renders as a side link next to the price. Useful for "View Details" or "Learn More" links.

```json
{
  "offer": {
    "id": "growth-bundle",
    "title": "Growth Bundle",
    "price_display": "$49/mo",
    "stripe_price_id": "price_...",
    "button": { "label": "Details", "url": "https://example.com/growth", "style": "secondary", "size": "sm" }
  }
}
```

**Button properties:**

| Property | Type | Default | Description |
|---|---|---|---|
| `label` | string | *(required)* | Button text |
| `url` | string | *(required)* | Link URL |
| `target` | `"_blank"` / `"_self"` | `"_blank"` | Open in new tab or same tab |
| `size` | `"sm"` / `"md"` / `"lg"` | `"sm"` | Button size |
| `style` | `"primary"` / `"secondary"` / `"outline"` / `"ghost"` | `"primary"` | Visual style (uses theme color) |
| `icon` | string | — | Emoji or text icon before label |

**Timeline checkbox:** Set `checkbox: true` for a simple "Done" checkbox, or `checkbox: { "label": "Joined" }` for custom label. Checkboxes are purely visual (client-side toggle, not tracked as form data).

### Prefill Modes & Readonly Copy

Input components support a `prefill_mode` property that controls how prefilled values are displayed:

- `"editable"` (default) — prefilled value is shown in a normal editable input
- `"readonly"` — value is shown in a styled read-only input with a **copy-to-clipboard button**. The user can click the clipboard icon to copy the value. Useful for displaying generated codes, API keys, referral links, or any value the user needs to copy but shouldn't edit.
- `"hidden"` — the component is completely hidden when prefilled (useful for passing data silently). **Important:** the field only hides when it receives a value via prefill (URL params or defaults). If no prefill value is provided, the field renders as a normal editable input. This mode is designed for silently carrying data between catalogs — do not use it on fields you expect the user to fill manually.

```json
{
  "id": "referral_code",
  "type": "short_text",
  "props": { "label": "Your Referral Code" },
  "prefill_mode": "readonly"
}
```

To prefill values, pass them as URL parameters matching the component ID: `?referral_code=ABC123`. The readonly input renders with a clipboard icon — clicking it copies the value and shows a brief checkmark confirmation.

#### Copyable (editable inputs with copy icon)

For inputs that should remain **editable** but also let the user easily copy the value, use `copyable: true` in props:

```json
{
  "id": "generated_link",
  "type": "url",
  "props": { "label": "Your Share Link", "copyable": true }
}
```

The copy icon appears next to the input once it has a value. Clicking it copies the current value to clipboard with a checkmark confirmation. Unlike `readonly`, the field remains fully editable. Supported on: `short_text`, `long_text`, `rich_text`, `email`, `phone`, `url`, `number`, `currency`, `date`, `datetime`, `time`, `password`, `dropdown`, `address`.

### Auto-Skip Pages

Set `auto_skip: true` on a page to automatically skip it when all visible input fields already have values. This is useful for multi-step funnels where URL params or defaults pre-fill a page — the visitor jumps straight to the next page without seeing it.

```json
{
  "collect_info": {
    "title": "Your Details",
    "auto_skip": true,
    "components": [
      { "id": "email", "type": "email", "props": { "label": "Email", "required": true } },
      { "id": "name", "type": "short_text", "props": { "label": "Name", "required": true } }
    ]
  }
}
```

With `?email=user@example.com&name=John` (mapped via `prefill_mappings`), this page is skipped entirely. Rules:
- Only skips if the page has at least one visible input and **all** of them have values
- Display-only pages (no inputs) are never auto-skipped
- Runs after `on_enter` hooks, so hooks can set values that satisfy the skip condition
- Skipped pages do NOT appear in browser history (Back button jumps past them)
- A `page_auto_skipped` analytics event is fired for each skipped page

#### Chaining Catalogs with Auto-Skip

A common pattern is chaining two catalogs together — e.g., a registration form redirects to an onboarding flow, carrying collected data forward so already-answered pages are skipped.

**Step 1: Catalog A — redirect with form values as URL params**

Use `settings.completion.redirect_url` with `{{field_id}}` templates to pass form data to the next catalog:

```json
{
  "settings": {
    "completion": {
      "redirect_url": "https://yoursubdomain.catalogkit.cc/onboarding?email={{comp_email}}&name={{comp_name}}&phone={{comp_phone}}",
      "redirect_delay": 0
    }
  }
}
```

**Step 2: Catalog B — map URL params to component IDs + enable auto_skip**

In the receiving catalog, set up `prefill_mappings` so URL params populate the right fields, and `auto_skip: true` on pages that should be invisible when pre-filled:

```json
{
  "settings": {
    "url_params": {
      "prefill_mappings": {
        "email": "comp_email",
        "name": "comp_name",
        "phone": "comp_phone"
      }
    }
  },
  "pages": {
    "contact_info": {
      "title": "Your Contact Info",
      "auto_skip": true,
      "components": [
        { "id": "comp_email", "type": "email", "props": { "label": "Email", "required": true } },
        { "id": "comp_name", "type": "short_text", "props": { "label": "Name", "required": true } },
        { "id": "comp_phone", "type": "phone", "props": { "label": "Phone" } }
      ]
    },
    "preferences": {
      "title": "Your Preferences",
      "components": [...]
    }
  }
}
```

When a visitor arrives at Catalog B via `?email=a@b.com&name=John&phone=555`, the `contact_info` page is auto-skipped and they land directly on `preferences`. If any param is missing, they see the page with partial prefill.

### Disabled Button Until Required Fields Are Filled

The Continue/Submit button is **automatically disabled** whenever any visible `required` field on the current page is empty. Just set `required: true` on individual fields — no page-level flag needed.

```json
{
  "contact_info": {
    "title": "Your Details",
    "components": [
      { "id": "email", "type": "email", "props": { "label": "Email", "required": true } },
      { "id": "name", "type": "short_text", "props": { "label": "Name", "required": true } },
      { "id": "newsletter", "type": "checkbox", "props": { "label": "Subscribe to newsletter" } }
    ]
  }
}
```

In this example, the button stays disabled until both `email` and `name` have values. The optional `newsletter` checkbox doesn't block navigation.

> **Opt-out:** If you want the old click-then-validate behavior (button stays enabled, errors shown on click), set `require_all_fields: false` on the page explicitly.

**How it works:**
- Only checks visible, non-readonly, non-hidden required fields
- Respects visibility conditions — if a required field is conditionally hidden, it doesn't block
- Works with arrays (multiselect, checkboxes) — checks `value.length > 0`
- **`require_all` prop (checkboxes/multiple_choice):** When set to `true` on a checkboxes or multiple_choice component, ALL options must be selected (not just one). All nested required inputs are also validated regardless of selection state.
- **Boolean fields (`switch`, `checkbox`) require a truthy value** — a required switch/checkbox must be checked (toggled on) to satisfy validation. Unchecking re-disables the button.
- Works with both inline buttons and sticky bottom bars
- Nested inputs from checked checkboxes are included in validation (or ALL nested inputs when `require_all: true`)
- **Format validation:** Address types (`solana_address`, `evm_address`, `bitcoin_address`) keep the button disabled when the value is present but format-invalid (e.g. not a valid base58 Solana address). Applies to both top-level and nested inputs.
- **Respects script `propOverrides`** — if a script dynamically sets `required`, `hidden`, or `readonly` on a component via `ctx.setProp()`, the button state updates in real time
- The button renders with 50% opacity and `cursor-not-allowed` when disabled

#### Script-Controlled Button State

For more complex logic (e.g., async validation, API checks), use `setButtonDisabled()` and `setButtonLoading()` in script hooks:

```typescript
{
  hooks: {
    on_enter: (ctx) => {
      // Disable button until an API call succeeds
      ctx.setButtonDisabled(true);
      ctx.setButtonLoading(true);

      ctx.fetch("https://api.example.com/check")
        .then(r => r.json())
        .then(data => {
          ctx.setField("status", data.status);
          ctx.setButtonDisabled(false);
          ctx.setButtonLoading(false);
        });
    }
  }
}
```

You can also combine both approaches — required field checking handles the simple case automatically, while `setButtonDisabled(true)` from a script adds additional blocking conditions. The button is disabled if **either** any required fields are unfilled **or** `setButtonDisabled(true)` was called from a script.

**`setButtonLoading(true)`** shows a spinner animation on the button — useful for async operations like API calls where the user should wait.

Both `setButtonDisabled` and `setButtonLoading` reset to `false` automatically on page navigation.

#### Script-Controlled Validation Errors

Use `setValidationError(componentId, message)` to show custom error messages on any field from scripts. Pass `null` to clear:

```typescript
{
  hooks: {
    on_change: async (ctx) => {
      // Custom async validation or LLM-powered feedback
      const resp = await ctx.fetch("https://api.example.com/validate", {
        method: "POST",
        body: JSON.stringify({ answer: ctx.field_value }),
      });
      const data = await resp.json();
      if (!data.valid) {
        ctx.setValidationError(ctx.field_id, data.feedback); // e.g. "Almost! Think about X"
      } else {
        ctx.setValidationError(ctx.field_id, null); // Clear error
      }
    }
  }
}
```

This works with **any input type** — not just quiz components. Combine with `on_change` hooks to provide real-time feedback from REST APIs or LLMs as the user types/selects.

### Component Width (Multi-Column Layout)

Any component can have a `width` property to create side-by-side layouts. Adjacent sub-full-width components are automatically grouped into flex rows.

**Values:** `"full"` (default), `"half"`, `"third"`, `"two_thirds"`

```json
{
  "components": [
    { "id": "phone_img", "type": "image", "width": "half", "props": { "src": "https://example.com/phone.png" } },
    { "id": "phone_text", "type": "paragraph", "width": "half", "props": { "text": "**Your Phone**\n\nThis gig is 100% mobile-friendly." } },
    { "id": "leads_img", "type": "image", "width": "half", "props": { "src": "https://example.com/leads.png" } },
    { "id": "leads_text", "type": "paragraph", "width": "half", "props": { "text": "**Leads Vending Machine**\n\nGet your daily prospects." } }
  ]
}
```

Components stack vertically on mobile and go side-by-side on desktop. Mix widths freely — e.g. `"third"` + `"two_thirds"` for a sidebar layout.

### Multi-Page Routing

Route visitors through different pages based on their answers:

```json
{
  "routing": {
    "entry": "landing",
    "edges": [
      {
        "from": "landing",
        "to": "enterprise",
        "conditions": {
          "match": "all",
          "rules": [{ "field": "company_size", "operator": "greater_than", "value": 100 }]
        }
      },
      { "from": "landing", "to": "standard", "is_default": true }
    ]
  }
}
```

**Condition operators:** `equals`, `not_equals`, `contains`, `not_contains`, `greater_than`, `less_than`, `greater_than_or_equal`, `less_than_or_equal`, `starts_with`, `ends_with`, `regex`, `in`, `not_in`, `is_empty`, `is_not_empty`, `between`

### Quiz Scoring

Add quiz scoring to any multiple choice or input component:

```json
{
  "id": "q1",
  "type": "multiple_choice",
  "label": "What does CTA stand for?",
  "options": ["Click To Act", "Call To Action", "Create The Ad"],
  "quiz": {
    "correct_answer": "Call To Action",
    "points": 10,
    "explanation": "CTA = Call To Action",
    "wrong_message": "Not quite — CTA stands for Call To Action!",
    "correct_message": "You nailed it!",
    "option_messages": {
      "Click To Act": "Close, but 'Click To Act' isn't a standard marketing term.",
      "Create The Ad": "That's a common misconception — CTA is about the action, not the ad."
    }
  }
}
```

- `wrong_message` — custom text shown when the answer is wrong (default: "You got the wrong answer.")
- `correct_message` — custom text shown when the answer is right (default: "Correct!")
- `option_messages` — per-option messages keyed by option value, shown when that specific wrong option is selected (overrides `wrong_message` for that option)

Scoring is **case-insensitive** and tolerates type mismatches — `correct_answer: "Call To Action"` matches a user selecting `"call to action"`, and `correct_answer: ["c"]` (single-element array) works the same as `correct_answer: "c"` for single-select inputs.

Score-based routing: `{ "score": "percent", "operator": "greater_than", "value": 80 }`

### Inline Quiz Feedback (Reveal on Continue)

Show correct/incorrect feedback when the visitor clicks Continue by adding `reveal_on_select: true` to the quiz config:

```json
{
  "id": "q1",
  "type": "multiple_choice",
  "label": "What's the catch?",
  "options": [
    { "value": "a", "label": "No Babysitting Policy" },
    { "value": "b", "label": "Must show up consistently" },
    { "value": "c", "label": "All of the Above" }
  ],
  "quiz": {
    "correct_answer": "c",
    "points": 10,
    "explanation": "All three are true — this program rewards effort.",
    "reveal_on_select": true
  }
}
```

When `reveal_on_select` is `true`, the flow is two-step:
1. The visitor **selects their answers freely** (options are not locked)
2. When they click **Continue**, answers are revealed:
   - Correct answers get a **green border**
   - Wrong selections get a **red border**
   - A feedback banner shows the `correct_message` / `wrong_message` (or per-option `option_messages[value]` if set)
   - The explanation text is displayed (if provided)
   - Options become **locked**
   - A banner says "Answers revealed! Review your results above, then click Continue to proceed."
   - The page **auto-scrolls** to keep the Continue button visible
3. The visitor clicks **Continue again** to proceed to the next page

Works with both `multiple_choice` (single-select) and `checkboxes` (multi-select) components. Omit `reveal_on_select` or set to `false` for the default behavior (no inline feedback — use `reveal_answers` on a later page instead).

### Reveal Answers (Results Page)

Show a quiz results summary on any page by adding `reveal_answers` to the page config. This is the **recommended declarative approach** — no scripting needed:

```json
{
  "results": {
    "reveal_answers": {
      "from_pages": ["quiz_page_1", "quiz_page_2"],
      "show_score": true,
      "show_correct": true,
      "show_explanation": true
    }
  }
}
```

This automatically renders a score summary (e.g. "7 / 10") and per-question breakdowns with correct/incorrect indicators, explanations, and wrong-answer messages.

For **custom results UI** (e.g. dynamic headings, referral links with score), use `kit.getQuizScores()` in a script on the results page:

```json
{
  "id": "results_script",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\nkit.on('pageenter:results', () => {\n  const s = kit.getQuizScores();\n  document.querySelector('#score').textContent = s.total + ' / ' + s.max;\n});\n</script>"
  }
}
```

### Timeline

Display a vertical timeline with alternating or single-side layout:

```json
{
  "id": "process",
  "type": "timeline",
  "props": {
    "variant": "alternating",
    "items": [
      { "title": "Step 1: Setup", "description": "Create your account", "icon": "🏠", "color": "#f59e0b" },
      { "title": "Step 2: Configure", "description": "Set up your campaign", "icon": "🔍", "color": "#ef4444" },
      { "title": "Step 3: Launch", "description": "Go live", "icon": "📅", "color": "#22c55e" }
    ]
  }
}
```

**Variants:** `"default"` (all items on the right), `"alternating"` (items alternate left/right on desktop, stack on mobile).

Each item supports: `title` (required), `description` (optional, markdown), `icon` (emoji in colored circle), `image` (URL for a round image), `color` (per-item color, falls back to theme), `button` (embedded button below description, see [Embedded Buttons](#embedded-buttons)), `side_button` (embedded button rendered inline with the title at top-right of card), `checkbox` (`true` or `{ "label": "Custom" }` for an interactive checkbox).

### File Upload

Upload single or multiple files with drag-and-drop. Supports file type filtering, size limits, and multi-file mode.

```json
{
  "id": "resume",
  "type": "file_upload",
  "props": {
    "label": "Upload your resume",
    "accept": ".pdf,.doc,.docx",
    "max_size_mb": 10,
    "required": true
  }
}
```

Multi-file example:

```json
{
  "id": "portfolio",
  "type": "file_upload",
  "props": {
    "label": "Upload portfolio images",
    "multiple": true,
    "accept": "image/*",
    "max_files": 5,
    "max_size_mb": 10
  }
}
```

**Properties:** `multiple` (boolean, default false), `accept` (string, e.g. `"image/*,.pdf"`), `max_files` (number, default 10), `max_size_mb` (number, default 25).

### Password

Password input with a toggleable show/hide button (eye icon). Uses `type="password"` by default and switches to `type="text"` when the user clicks the eye icon.

```json
{
  "id": "user_password",
  "type": "password",
  "props": {
    "label": "Create a password",
    "placeholder": "Enter password",
    "required": true
  }
}
```

### Signature

Canvas-based drawing pad for capturing signatures. Value is stored as a base64 PNG data URL. Includes a Clear button to reset.

```json
{
  "id": "consent_signature",
  "type": "signature",
  "props": {
    "label": "Sign below to confirm",
    "required": true
  }
}
```

### Wallet Address Inputs

Three validated wallet address input types with inline validation:

- `evm_address` — Ethereum/EVM address (0x + 40 hex chars)
- `solana_address` — Solana address (32-44 base58 chars)
- `bitcoin_address` — Bitcoin address (Legacy, P2SH, Bech32, Taproot)

```json
{
  "id": "eth_wallet",
  "type": "evm_address",
  "props": { "label": "Your ETH Wallet", "required": true }
}
```

```json
{
  "id": "sol_wallet",
  "type": "solana_address",
  "props": { "label": "Solana Wallet" }
}
```

```json
{
  "id": "btc_wallet",
  "type": "bitcoin_address",
  "props": { "label": "Bitcoin Address" }
}
```

All three render as monospace text inputs with real-time format validation and visual feedback (green check / red X).

### Testimonial Sizes & Links

The `testimonial` component supports `size` variants for different layout densities:

```json
{
  "id": "review",
  "type": "testimonial",
  "props": {
    "text": "This changed everything for our team.",
    "author": "Jane Smith",
    "subtitle": "CEO at Acme Inc.",
    "avatar": "https://example.com/jane.jpg",
    "rating": 5,
    "link": "https://twitter.com/janesmith",
    "variant": "card",
    "size": "medium"
  }
}
```

| Property | Type | Default | Description |
|---|---|---|---|
| `text` | string | *(required)* | Quote text |
| `author` | string | *(required)* | Author name |
| `subtitle` | string | — | Role, company, or subtitle text (alias: `role`) |
| `avatar` | string | — | Profile picture URL |
| `rating` | number (1-5) | — | Star rating |
| `link` | string | — | Author name becomes a clickable link |
| `variant` | `"card"` / `"quote"` / `"minimal"` | `"card"` | Layout style |
| `size` | `"compact"` / `"medium"` / `"large"` | `"medium"` | Controls padding, text size, and avatar size |

### Callout

Highlighted callout boxes for tips, warnings, notes, and other important information. Supports 6 preset styles and an optional collapsible mode.

```json
{
  "id": "important",
  "type": "callout",
  "props": {
    "style": "warning",
    "title": "Important Notice",
    "text": "Complete all steps within 48 hours to keep your spot."
  }
}
```

Collapsible callout:

```json
{
  "id": "faq-note",
  "type": "callout",
  "props": {
    "style": "tip",
    "title": "Pro Tip",
    "text": "You can use **markdown** in the body text.",
    "collapsible": true
  }
}
```

| Property | Type | Default | Description |
|---|---|---|---|
| `style` | `"info"` / `"tip"` / `"warning"` / `"danger"` / `"note"` / `"success"` | `"info"` | Visual preset (color + default icon) |
| `title` | string | — | Bold heading text |
| `text` | string | — | Body text (supports markdown) |
| `icon` | string | — | Override the default icon (emoji) |
| `collapsible` | boolean | `false` | Renders as expandable/collapsible (requires `title`) |

### Iframe Component

Embed any external URL in your catalog. The `src` supports `{{field_id}}` templates for dynamic URLs that update as visitors fill in fields.

```json
{
  "id": "demo_embed",
  "type": "iframe",
  "props": {
    "src": "https://app.example.com/preview?email={{comp_email}}&plan={{comp_plan}}",
    "height": 500,
    "border_radius": 12,
    "title": "Live Preview"
  }
}
```

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `src` | string | — | URL to embed. Supports `{{field_id}}` templates (values are URL-encoded) |
| `height` | number \| string | `400` | Height in px or CSS value |
| `width` | string | `"100%"` | CSS width value |
| `border_radius` | number | `16` | Border radius in px |
| `sandbox` | string | `"allow-scripts allow-same-origin allow-forms"` | iframe sandbox attribute |
| `allow` | string | `""` | iframe allow attribute (e.g. `"camera; microphone"`) |
| `border` | boolean | `false` | Show a border around the iframe |
| `title` | string | `"Embedded content"` | Accessibility title |

The iframe URL re-resolves reactively — when a visitor fills in `comp_email`, the iframe immediately reloads with the updated URL.

### Modal (Info Popup)

A button that opens a scrollable modal dialog. Perfect for terms & conditions, privacy policies, detailed product info, or any content that would clutter the page. The body supports markdown-style formatting (bold, italic, links, lists).

**Basic modal (static content only):**

```json
{
  "id": "terms_modal",
  "type": "modal",
  "props": {
    "button_label": "View Terms & Conditions",
    "button_style": "link",
    "title": "Terms & Conditions",
    "body": "## 1. Acceptance of Terms\n\nBy accessing and using this service, you accept and agree to be bound by the terms...\n\n## 2. Use License\n\n- Permission is granted to temporarily use this service\n- This is the grant of a license, not a transfer of title\n\n## 3. Disclaimer\n\nThe materials on this website are provided on an **as is** basis...",
    "max_width": "640px"
  }
}
```

**Modal with embedded inputs (read & sign pattern):**

Modals can embed input and display components inside the body. Use `confirm_sets_field` to auto-set a form field value when the user confirms (e.g. auto-check a checkbox after signing). Use `require_inputs` to disable the confirm button until all required embedded inputs are filled.

```json
{
  "id": "agreement_modal",
  "type": "modal",
  "props": {
    "button_label": "Read & Sign Agreement",
    "button_style": "outline",
    "title": "Service Agreement",
    "body": "Please read the following terms carefully and sign below to confirm your acceptance.",
    "components": [
      { "id": "agreement_text", "type": "paragraph", "props": { "text": "By signing below you agree to all terms and conditions..." } },
      { "id": "sig", "type": "signature", "label": "Your Signature", "required": true },
      { "id": "typed_name", "type": "short_text", "label": "Type your full name", "required": true, "placeholder": "John Doe" }
    ],
    "confirm_label": "I Agree",
    "confirm_sets_field": { "field_id": "terms_accepted", "value": true },
    "require_inputs": true
  }
}
```

Embedded component values are stored with compound IDs: `modalComponentId.nestedInputId` (e.g. `agreement_modal.sig`, `agreement_modal.typed_name`).

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `button_label` | string | `"View"` | Text on the trigger button |
| `button_style` | `"primary"` \| `"outline"` \| `"ghost"` \| `"link"` | `"primary"` | Visual style of the trigger button |
| `title` | string | — | Header shown at the top of the modal |
| `body` | string | `""` | Scrollable content (supports markdown-style bold, italic, links, lists) |
| `max_width` | string | `"640px"` | Maximum width of the modal dialog |
| `components` | array | — | Embedded input/display components rendered inside the modal body (same format as checkbox nested inputs) |
| `confirm_label` | string | `"Confirm"` / `"Close"` | Footer button label. Defaults to "Confirm" when `components` present, "Close" otherwise |
| `confirm_sets_field` | `{ field_id, value }` | — | On confirm, set this field to the given value (e.g. auto-check a checkbox) |
| `require_inputs` | boolean | `false` | Disable confirm button until all required embedded inputs are filled |

The modal closes by clicking the X button, pressing Escape, clicking the backdrop overlay, or the footer button. When `components` are present, a Cancel button appears alongside the confirm button.

### Custom React Component

For power users who need full React interactivity beyond what the built-in component types offer. Load your own React components via an external script and reference them by name.

**Step 1:** Add a script tag that registers your components on `window.__catalogkit_components`:

```json
{
  "settings": {
    "scripts": [
      { "src": "https://cdn.example.com/my-components.js", "position": "head" }
    ]
  }
}
```

**Step 2:** In your script, register components:

```javascript
// my-components.js
window.__catalogkit_components = window.__catalogkit_components || {};

window.__catalogkit_components.PriceCalculator = ({ formState, setField, themeColor, quantity }) => {
  const price = (quantity || 1) * 29.99;
  return React.createElement('div', {
    style: { padding: '16px', borderRadius: '12px', border: '1px solid #e5e7eb' }
  },
    React.createElement('p', { style: { fontSize: '24px', fontWeight: 'bold', color: themeColor } },
      '$' + price.toFixed(2)
    ),
    React.createElement('button', {
      onClick: () => setField('comp_price', price),
      style: { marginTop: '8px', padding: '8px 16px', backgroundColor: themeColor, color: 'white', borderRadius: '8px', border: 'none', cursor: 'pointer' }
    }, 'Lock in price')
  );
};
```

**Step 3:** Reference it in your catalog schema:

```json
{
  "id": "price_calc",
  "type": "custom",
  "props": {
    "component": "PriceCalculator",
    "quantity": 3
  }
}
```

**Props passed to your component:**
| Prop | Description |
|------|-------------|
| `themeColor` | The catalog's theme color (hex string) |
| `formState` | Read-only snapshot of all form field values |
| `setField(componentId, value)` | Set any form field value |
| `...props` | All other props from the schema (e.g. `quantity` above) |

**Important notes:**
- Your script must register components on `window.__catalogkit_components` — the renderer polls for up to 5 seconds after page load
- Components are wrapped in an error boundary — if your component throws, a friendly error message is shown instead of crashing the catalog
- React is available globally (the catalog already loads it) — use `React.createElement` or bundle JSX yourself
- The component re-renders when `formState` changes, just like built-in components
- For TypeScript catalogs, `type: "custom"` works identically

### Nested Inputs in Timeline

Timeline items support an `inputs` array for embedding input fields inside timeline cards. Nested inputs render in an indented left-bordered panel. Values are stored with compound IDs: `timelineComponentId.inputId`.

```json
{
  "id": "onboarding",
  "type": "timeline",
  "props": {
    "items": [
      {
        "title": "Set Your Availability",
        "description": "Choose when you're free to take calls.",
        "icon": "📅",
        "inputs": [
          { "id": "timezone", "type": "dropdown", "label": "Timezone", "props": { "options": ["EST", "CST", "PST"] } },
          { "id": "hours", "type": "short_text", "label": "Available hours", "placeholder": "e.g. 9am-5pm" }
        ]
      },
      {
        "title": "Upload ID",
        "description": "We need a photo ID for verification.",
        "icon": "🪪",
        "inputs": [
          { "id": "id_photo", "type": "file_upload", "label": "Photo ID", "props": { "accept": "image/*" } }
        ]
      }
    ]
  }
}
```

### Checkboxes as Section Cards

Checkboxes are a first-class section card component. Each option acts as a card with nested sub-components (inputs, display content, even other checkboxes) below the toggle row. The toggle and nested content are separate DOM regions so clicks on nested inputs never accidentally toggle the checkbox.

**Default behavior:** Nested inputs are **always visible** so users can see what's required before checking. When all `required` nested inputs are filled, the checkbox **auto-checks** itself. This means users just fill in the fields and the checkbox marks itself complete.

**Conditional rendering:** Set `expand_on_select: true` on an option to hide its nested inputs until the checkbox is manually checked (old behavior). Auto-check is disabled for these options.

Options support: `value`, `label`, `description`, `image` (thumbnail), `button` (side link), `expand_on_select` (boolean), and `inputs` (array of nested sub-components).

Values are stored with compound IDs: `checkboxComponentId.optionValue.inputId`. Nested input `default_value` (in `input.props.default_value` or `input.default_value`) is initialized at startup, so nested values are available via `getAllFields()` and template interpolation (`{{checkboxId.optionValue.inputId}}`) from the first render.

> **CRITICAL for AI agents writing scripts:** Nested input values are **only** accessible via their compound ID. Using the bare `input.id` alone will **always return undefined**. For example, given a checkboxes component with `id: "tasks"`, an option with `value: "step_1"`, and a nested input with `id: "proof_url"`:
> - **CORRECT:** `kit.getField("tasks.step_1.proof_url")`
> - **WRONG:** `kit.getField("proof_url")` — returns `undefined`
> - **CORRECT event scope:** `kit.on("fieldchange:tasks.step_1.proof_url", ...)`
> - **WRONG event scope:** `kit.on("fieldchange:proof_url", ...)` — never fires

```json
{
  "id": "onboarding_tasks",
  "type": "checkboxes",
  "props": {
    "label": "Complete your onboarding",
    "options": [
      {
        "value": "gcash",
        "label": "Setup GCash USDC",
        "description": "Connect your crypto wallet for payouts",
        "button": { "label": "What is GCash?", "url": "https://example.com/gcash", "style": "ghost", "size": "sm" },
        "inputs": [
          { "id": "wallet", "type": "solana_address", "label": "Your GCash Solana USDC Address", "required": true },
          { "id": "note", "type": "paragraph", "props": { "text": "This is your Solana wallet address from GCash — **not** your GCash phone number." } }
        ]
      },
      {
        "value": "eth",
        "label": "Setup Ethereum Wallet",
        "image": "https://example.com/eth-icon.png",
        "inputs": [
          { "id": "eth-wallet", "type": "evm_address", "label": "Your ETH Address", "required": true }
        ]
      },
      {
        "value": "preferences",
        "label": "Set Your Preferences",
        "expand_on_select": true,
        "inputs": [
          { "id": "group_size", "type": "dropdown", "label": "Preferred group size", "props": { "options": ["Small (3-5)", "Medium (6-10)", "Large (10+)"] } },
          { "id": "sub_tasks", "type": "checkboxes", "label": "Sub-tasks", "props": { "options": [
            { "value": "read_docs", "label": "Read the documentation" },
            { "value": "watch_video", "label": "Watch intro video", "button": { "label": "Watch", "url": "https://example.com/video", "style": "primary", "size": "sm" } }
          ] } }
        ]
      },
      { "value": "self_paced", "label": "Self-Paced Learning" }
    ]
  }
}
```

**Supported nested item types:**

- **Input types:** `short_text`, `long_text`, `email`, `phone`, `url`, `password`, `number`, `dropdown`, `multiple_choice`, `checkboxes` (nested!), `switch`, `checkbox`, `star_rating`, `slider`, `opinion_scale`, `file_upload`, `signature`, `solana_address`, `evm_address`, `bitcoin_address`
- **Display types:** `paragraph`, `heading`, `banner`, `image`, `divider`, `html`, `callout` — rendered as static content (no form value stored)

**Nested input properties:**

Each item in the `inputs` array has these fields:

| Property | Type | Description |
|---|---|---|
| `id` | string | *(required)* Unique identifier for the nested input |
| `type` | string | *(required)* Input type (e.g. `short_text`, `solana_address`, `paragraph`) |
| `label` | string | Display label above the input |
| `placeholder` | string | Placeholder text |
| `required` | boolean | Mark this nested input as required. Can be set here OR inside `props.required` — both are supported. |
| `props` | object | Additional props passed to the input component (e.g. `{ "required": true, "sublabel": "...", "readonly": true }`) |

> **Important for AI agents:** `required` can be placed at `input.required` (top-level) OR `input.props.required` (inside props). Both work identically. Example: `{ "id": "wallet", "type": "solana_address", "required": true }` is equivalent to `{ "id": "wallet", "type": "solana_address", "props": { "required": true } }`.

> **Readonly & Copyable nested inputs:** Set `"readonly": true` inside `props` to render the nested input as a read-only field with a **copy-to-clipboard button** — ideal for pre-filled messages, codes, or links the user needs to copy. Set `"copyable": true` to keep the input editable but add a copy button alongside it. Supported types: `short_text`, `long_text`, `rich_text`, `email`, `phone`, `url`, `number`, `currency`, `date`, `datetime`, `time`, `password`, `dropdown`, `address`. Example: `{ "id": "msg", "type": "long_text", "label": "Message to send", "props": { "readonly": true } }`.

**Option properties:**

| Property | Type | Description |
|---|---|---|
| `value` | string | *(required)* Unique identifier for the option |
| `label` | string | *(required)* Display text |
| `description` | string | Small subtext below the label |
| `image` | string | Thumbnail image URL (rounded, 32x32) |
| `button` | EmbeddedButton | Side link button (see [Embedded Buttons](#embedded-buttons)) |
| `inputs` | array | Nested sub-components — always visible by default, auto-checks when required inputs filled |
| `expand_on_select` | boolean | When `true`, nested inputs only show after checkbox is checked (no auto-check). Default: `false` |

### Progress Line

Add a thin progress line at the top of the viewport (like Fillout.com) that fills as the visitor progresses:

```json
{
  "settings": {
    "progress_line": {
      "enabled": true,
      "position": "top",
      "height": 4,
      "color": "#3b82f6"
    }
  }
}
```

**Options:**
- `position`: `"top"` (fixed to top of viewport, default) or `"below_topbar"` (below the existing top bar)
- `height`: pixel height (default 4)
- `color`: override color (defaults to theme primary_color)

Independent of the existing `progress_bar` setting — both can coexist.

### Popups

Trigger popups based on visitor behavior:

```json
{
  "popups": [
    {
      "id": "exit-popup",
      "trigger": { "type": "exit_intent", "delay_ms": 3000 },
      "pages": ["landing"],
      "mode": "modal",
      "content": { "title": "Wait!", "body": "Get 10% off before you go" }
    }
  ]
}
```

**Trigger types:** `exit_intent`, `scroll_depth`, `inactive`, `timed`, `page_count`, `custom`, `video_progress`, `video_chapter`

### Page Transitions

Control the animation style and scroll behavior when navigating between pages:

```jsonc
{
  "settings": {
    "page_transition": "slide-up",  // "slide-up" | "fade" | "slide-left" | "scale" | "none"
    "page_scroll": "instant"        // "instant" | "smooth"
  }
}
```

**Transitions:** `slide-up` (default — fade + slide up), `fade` (opacity only), `slide-left` (slide from right, wizard feel), `scale` (subtle zoom in), `none` (instant swap).

**Scroll:** `instant` (default — jump to top) or `smooth` (animated scroll to top).

### Completion Screen

Customize what visitors see after submitting:

```json
{
  "settings": {
    "completion": {
      "heading": "You're all set!",
      "message": "We'll be in touch within 24 hours.",
      "redirect_url": "https://example.com",
      "redirect_delay": 3000,
      "actions": [
        { "type": "fill_again", "label": "Submit Again", "style": "secondary" },
        { "type": "share", "label": "Share", "style": "ghost" },
        { "type": "redirect", "label": "Visit Site", "url": "https://example.com", "style": "primary" }
      ]
    }
  }
}
```

**Action types:** `fill_again` (reset form), `share` (copy URL), `redirect` (navigate to URL). All fields are optional — omit `completion` entirely for a minimal checkmark screen.

### Dynamic Behavior (CatalogKit API)

For custom client-side logic, use the `window.CatalogKit` global API via inline `<script>` tags:

```html
<script>
  const kit = window.CatalogKit.get();
  kit.on('fieldchange:email', ({ value }) => {
    console.log('Email changed to', value);
  });
  kit.on('beforenext:pricing', async ({ preventDefault }) => {
    // Custom validation or API call
    const ok = await fetch('/validate', { method: 'POST', body: JSON.stringify(kit.getAllFields()) });
    if (!ok) preventDefault();
  });
</script>
```

Available events: `pageenter`, `pageexit`, `beforenext`, `submit`, `fieldchange` — all support scoping (e.g., `fieldchange:email`).

Available methods: `getField`, `setField`, `getAllFields`, `setButtonLoading`, `setButtonDisabled`, `setValidationError`, `setComponentProp`, `goNext`, `goBack`, `goToPage`.

---

## CatalogKit Global API (`window.CatalogKit`)

A live JavaScript bridge exposed on `window.CatalogKit` that gives any plain JavaScript — inline `<script>` tags in `html` components, external scripts, or browser console — full read/write access to the catalog runtime. This is **the recommended way** to build custom logic, server-side validation, conditional UI, and interactive widgets.

### Accessing an instance

**IMPORTANT: Always call `.get()` first.** `window.CatalogKit` is a registry, not an instance — it only has `.get()`. All API methods (`on`, `off`, `getField`, `setField`, etc.) live on the instance returned by `.get()`.

```javascript
// ✅ CORRECT — always use .get() to obtain an instance first
const kit = window.CatalogKit.get();           // most recently mounted catalog
const kit = window.CatalogKit.get('cat_abc');   // specific catalog by ID

// ❌ WRONG — will throw "is not a function"
window.CatalogKit.on('pageenter', ...);        // .on() does not exist on the registry
window.CatalogKit.getField('email');           // .getField() does not exist on the registry
```

**Multi-form isolation:** Multiple catalogs on the same page each register independently under their own `catalog_id`. They never bleed state into each other. Use `.get(id)` to target a specific one.

### API Reference

| Method | Description |
|--------|-------------|
| **Read state** | |
| `kit.getField(id)` | Get current value of any form field. For nested checkbox/multiple_choice inputs, use the compound ID: `kit.getField('checkboxId.optionValue.inputId')` |
| `kit.getAllFields()` | Frozen copy of all form values (includes nested compound keys like `checkboxId.optionValue.inputId`) |
| `kit.getVar(key)` | Get a script variable (also available in templates as `{{var:key}}`) |
| `kit.getAllVars()` | Frozen copy of all script variables |
| `kit.getUrlParam(key)` | Get a URL query parameter |
| `kit.getAllUrlParams()` | Frozen copy of all URL params |
| `kit.getPageId()` | Current page ID |
| `kit.getGlobal(key)` | Get a global (cross-page) value |
| `kit.getQuizScores()` | Get quiz scores: `{ total, max, percent, correct_count, question_count, answers[] }`. Each answer includes `{ component_id, page_id, label, options, given_answer, correct_answer, is_correct, points_earned, points_possible, explanation, wrong_message }` |
| **Write state** | |
| `kit.setField(id, value)` | Set a field value — immediately reflects on screen |
| `kit.setVar(key, value)` | Set a script variable (triggers re-render, available in templates as `{{var:key}}`) |
| `kit.setGlobal(key, value)` | Set a global (persists across pages, available in templates as `{{global:key}}` — note: does not trigger re-render on its own) |
| **Button control** | |
| `kit.setButtonLoading(bool)` | Show/hide loading spinner on Continue button |
| `kit.setButtonDisabled(bool)` | Enable/disable the Continue button |
| `kit.setValidationError(id, msg)` | Show a custom error on a field (`null` to clear) |
| **Navigation** | |
| `kit.goNext()` | Advance to next page (runs validation + hooks) |
| `kit.goBack()` | Go to previous page |
| `kit.goToPage(pageId)` | Navigate directly to any page by ID (adds current page to history, no validation) |
| **Component props** | |
| `kit.setComponentProp(id, prop, value)` | Override any component prop at runtime (e.g. `hidden`, `label`, `options`). Works on ALL component types — display and input alike |
| **Display cart** (visual UI: drawer, badges, order summary) | |
| `kit.openCart()` | Open the cart drawer programmatically |
| `kit.closeCart()` | Close the cart drawer |
| `kit.getDisplayItems()` | Get a frozen array of items shown in the cart UI |
| `kit.addDisplayItem(item)` | Programmatically add an arbitrary `CartItem` to the visual cart (no page-offer required, dedupes by `offer_id`). Item needs at minimum `offer_id`, `page_id`, `title` |
| `kit.removeDisplayItem(offerId)` | Remove an item from the visual cart by `offer_id` |
| **Payment items** (what actually gets sent to Stripe) | |
| `kit.setPaymentItems(items)` | Override line items sent to Stripe at checkout. Pass `null` to clear and fall back to display items. Lets you show a bundled deal in the UI while sending itemized charges to Stripe |
| `kit.getPaymentItems()` | Get the current Stripe override array, or `null` if using display items |
| `kit.startCheckout()` | Programmatically trigger the built-in checkout page (fires `before_checkout`, then shows checkout). Use this instead of DOM-clicking the cart checkout button |
| **Events** | |
| `kit.on(event, callback)` | Subscribe to lifecycle events (see Events section below) |
| `kit.off(event, callback)` | Unsubscribe |
| **Utilities** | |
| `kit.fetch` | Alias for `globalThis.fetch` |

### Events — scoped lifecycle hooks

Events follow the pattern `event` or `event:scope_id`. Unscoped listeners fire for all pages/fields. Scoped listeners fire only for the specified page or field.

| Event | Scope | Payload | Async? | Description |
|-------|-------|---------|--------|-------------|
| `fieldchange` | field ID | `{ fieldId, value, prevValue }` | No | A form field value changed. **For nested checkbox/multiple_choice inputs, scope must use the compound ID** (e.g. `fieldchange:checkboxId.optionValue.inputId`), not the bare input ID. |
| `pageenter` | page ID | `{ pageId }` | No | Page became active (after transition) |
| `pageexit` | page ID | `{ pageId }` | Yes | About to leave page (after beforenext) |
| `beforenext` | page ID | `{ pageId, preventDefault(), setNextPage(id) }` | Yes | After validation, before navigation — can block or redirect |
| `submit` | page ID | `{ pageId, formState, preventDefault() }` | Yes | Final page submission — can block |
| `cart_add` | — | `{ item, items }` | No | Offer added to cart |
| `cart_remove` | — | `{ offer_id, items }` | No | Offer removed from cart |
| `cart_open` | — | `{ items }` | No | Cart drawer opened |
| `cart_close` | — | `{ items }` | No | Cart drawer closed |
| `before_checkout` | — | `{ items, preventDefault() }` | Yes | Before checkout — can block or redirect |

**Scoping examples:**

```javascript
const kit = window.CatalogKit.get();

// Global — fires on every page
kit.on('beforenext', async (e) => { ... });

// Scoped — fires only on 'checkout' page
kit.on('beforenext:checkout', async (e) => { ... });

// Global — fires for any field
kit.on('fieldchange', (e) => { ... });

// Scoped — fires only when 'email' field changes
kit.on('fieldchange:email', (e) => { ... });

// Scoped — nested checkbox input (MUST use compound ID)
// Given: checkboxes id="tasks", option value="setup", nested input id="proof_url"
kit.on('fieldchange:tasks.setup.proof_url', (e) => { ... });
// ❌ WRONG: kit.on('fieldchange:proof_url', ...) — never fires

// Page-specific enter/exit
kit.on('pageenter:pricing', (e) => { ... });
kit.on('pageexit:checkout', (e) => { ... });
```

**DOM events:** For standard DOM events like `blur`, `focus`, `click`, `input`, `keydown` — use the DOM directly. CatalogKit only handles lifecycle events that have no DOM equivalent (page transitions and form state changes are managed by React internally).

```javascript
// DOM events — use the DOM directly
document.getElementById('email').addEventListener('blur', () => {
  const kit = window.CatalogKit.get();
  // validate on blur...
});
```

### Using with `html` components

`html` components support two features that make the bridge practical:

1. **Template interpolation:** `{{field_id}}`, `{{var:key}}`, and `{{global:key}}` in HTML content are replaced with the current field value, script variable, or global value respectively. Supports dotted nested keys for checkbox/multiple_choice compound IDs (e.g. `{{checkboxId.optionValue.inputId}}`). Whitespace inside braces is tolerated (e.g. `{{ field_id }}`). Fields and vars are reactive on re-render. Globals persist across pages but don't trigger re-renders on their own (pair with a `setField` or `setVar` call if you need reactivity).
2. **Inline script execution:** `<script>` tags inside `html` content are automatically executed after render, with full access to `window.CatalogKit`.

```json
{
  "id": "my_script",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n// your code here\n</script>"
  }
}
```

### Cookbook: common patterns

The following examples show real-world patterns using `html` components with inline `<script>` tags. Place these components on the relevant page in your catalog schema.

#### 1. Server-side form validation (beforenext + loading state)

Validate form data against your backend before allowing navigation. Shows a loading spinner on the Continue button and inline validation errors.

```json
{
  "id": "checkout_validator",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('beforenext:checkout', async (e) => {\n  kit.setButtonLoading(true);\n\n  try {\n    const res = await fetch('https://api.myapp.com/validate-order', {\n      method: 'POST',\n      headers: { 'Content-Type': 'application/json' },\n      body: JSON.stringify({\n        email: kit.getField('email'),\n        quantity: kit.getField('quantity'),\n      }),\n    });\n    const data = await res.json();\n\n    if (!data.ok) {\n      kit.setValidationError('email', data.error || 'Validation failed');\n      e.preventDefault();\n    }\n  } catch (err) {\n    kit.setValidationError('email', 'Network error — please try again');\n    e.preventDefault();\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
  }
}
```

#### 2. Dynamic content loading on page enter

Fetch data from your backend when a page loads and populate dropdown options dynamically.

```json
{
  "id": "plan_loader",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('pageenter:select_plan', async () => {\n  kit.setButtonLoading(true);\n  try {\n    const res = await fetch('https://api.myapp.com/plans');\n    const plans = await res.json();\n    kit.setComponentProp('plan_dropdown', 'options',\n      plans.map(p => ({ value: p.id, label: p.name + ' — ' + p.price + '/mo' }))\n    );\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
  }
}
```

#### 3. Conditional UI based on field value

Show or hide components based on user selections without server calls. Works on **all component types** — inputs AND display components (callout, banner, image, etc.).

```json
{
  "id": "conditional_ui",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('fieldchange:account_type', (e) => {\n  const isBusiness = e.value === 'business';\n  // Hide/show input fields\n  kit.setComponentProp('company_name', 'hidden', !isBusiness);\n  kit.setComponentProp('tax_id', 'hidden', !isBusiness);\n  kit.setComponentProp('company_name', 'required', isBusiness);\n  // Hide/show display components (banners, callouts, images, etc.)\n  kit.setComponentProp('business_info_banner', 'hidden', !isBusiness);\n});\n</script>"
  }
}
```

#### 4. Real-time email validation on blur (DOM event + CatalogKit)

Use native DOM events for blur/focus and CatalogKit for state reads and error display.

```json
{
  "id": "email_validator",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\n// Wait for DOM to render the email input\nsetTimeout(() => {\n  const el = document.querySelector('[data-component-id=\"email\"] input');\n  if (!el) return;\n\n  el.addEventListener('blur', async () => {\n    const email = kit.getField('email');\n    if (!email) return;\n\n    const res = await fetch('https://api.myapp.com/check-email?email=' + encodeURIComponent(email));\n    const data = await res.json();\n\n    if (data.taken) {\n      kit.setValidationError('email', 'This email is already registered');\n    } else {\n      kit.setValidationError('email', null);\n    }\n  });\n}, 100);\n</script>"
  }
}
```

#### 5. Dynamic routing based on API response

After server validation, route users to different pages based on the API response.

```json
{
  "id": "smart_router",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('beforenext:qualification', async (e) => {\n  kit.setButtonLoading(true);\n  try {\n    const res = await fetch('https://api.myapp.com/qualify', {\n      method: 'POST',\n      headers: { 'Content-Type': 'application/json' },\n      body: JSON.stringify({\n        revenue: kit.getField('annual_revenue'),\n        employees: kit.getField('team_size'),\n      }),\n    });\n    const { tier } = await res.json();\n    kit.setGlobal('qualified_tier', tier);\n\n    if (tier === 'enterprise') {\n      e.setNextPage('enterprise_onboarding');\n    } else if (tier === 'disqualified') {\n      kit.setValidationError('annual_revenue', 'We currently only serve businesses with $100k+ revenue');\n      e.preventDefault();\n    }\n    // else: continue to default next page\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
  }
}
```

#### 6. Timed actions — auto-advance, countdowns, delayed UI

Use `setTimeout`/`setInterval` combined with CatalogKit events for time-based logic.

```json
{
  "id": "auto_advance_timer",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\n// Auto-advance after 10 seconds on the 'intro' page\nkit.on('pageenter:intro', () => {\n  const timer = setTimeout(() => {\n    kit.goNext();\n  }, 10000);\n\n  // Clean up if user leaves manually\n  kit.on('pageexit:intro', () => clearTimeout(timer));\n});\n</script>"
  }
}
```

```json
{
  "id": "countdown_display",
  "type": "html",
  "props": {
    "content": "<div id=\"countdown\" style=\"text-align:center;font-size:24px;font-weight:bold;\"></div>\n<script>\nconst kit = window.CatalogKit.get();\nlet seconds = 30;\nconst el = document.getElementById('countdown');\nconst interval = setInterval(() => {\n  seconds--;\n  el.textContent = 'Offer expires in ' + seconds + 's';\n  if (seconds <= 0) {\n    clearInterval(interval);\n    kit.goNext(); // auto-advance when timer expires\n  }\n}, 1000);\nel.textContent = 'Offer expires in ' + seconds + 's';\n\nkit.on('pageexit:' + kit.getPageId(), () => clearInterval(interval));\n</script>"
  }
}
```

#### 7. Disable Continue until a condition is met

Keep the button disabled until the user completes a specific action, then enable it.

```json
{
  "id": "gate_logic",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\n// Disable Continue until terms are accepted AND email is filled\nkit.setButtonDisabled(true);\n\nfunction checkReady() {\n  const termsAccepted = kit.getField('accept_terms') === true;\n  const hasEmail = !!kit.getField('email');\n  kit.setButtonDisabled(!(termsAccepted && hasEmail));\n}\n\nkit.on('fieldchange:accept_terms', checkReady);\nkit.on('fieldchange:email', checkReady);\ncheckReady();\n</script>"
  }
}
```

#### 8. Submit interception — send data to your backend before completion

Intercept the final submission to send data to your own backend, and block submission if it fails.

```json
{
  "id": "submit_handler",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('submit', async (e) => {\n  kit.setButtonLoading(true);\n  try {\n    const res = await fetch('https://api.myapp.com/submissions', {\n      method: 'POST',\n      headers: { 'Content-Type': 'application/json' },\n      body: JSON.stringify(e.formState),\n    });\n    if (!res.ok) {\n      kit.setValidationError('email', 'Failed to save — please try again');\n      e.preventDefault();\n    }\n  } catch (err) {\n    kit.setValidationError('email', 'Network error');\n    e.preventDefault();\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
  }
}
```

#### 9. Live price calculator with reactive DOM

Combine `fieldchange` events with direct DOM manipulation for interactive widgets.

```json
{
  "id": "price_calc",
  "type": "html",
  "props": {
    "content": "<div style=\"background:#f0fdf4;border:1px solid #bbf7d0;border-radius:12px;padding:20px;text-align:center;\">\n  <div style=\"font-size:14px;color:#666;\">Your estimated price</div>\n  <div id=\"price\" style=\"font-size:36px;font-weight:bold;color:#16a34a;\">$0</div>\n  <div id=\"price-breakdown\" style=\"font-size:12px;color:#999;\"></div>\n</div>\n<script>\nconst kit = window.CatalogKit.get();\n\nfunction update() {\n  const qty = Number(kit.getField('quantity')) || 0;\n  const tier = kit.getField('tier') || 'basic';\n  const rates = { basic: 29, pro: 49, enterprise: 99 };\n  const rate = rates[tier] || 29;\n  const total = qty * rate;\n  document.getElementById('price').textContent = '$' + total.toLocaleString();\n  document.getElementById('price-breakdown').textContent = qty + ' x $' + rate + '/mo (' + tier + ')';\n}\n\nupdate();\nkit.on('fieldchange:quantity', update);\nkit.on('fieldchange:tier', update);\n</script>"
  }
}
```

#### 10. Conditional page skip based on external data

Decide at page-enter time whether to skip a page entirely.

```json
{
  "id": "skip_checker",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('pageenter:address_page', async () => {\n  // If we already have this user's address from a previous session, skip\n  const userId = kit.getUrlParam('uid');\n  if (!userId) return;\n\n  const res = await fetch('https://api.myapp.com/users/' + userId + '/address');\n  const data = await res.json();\n  if (data.address) {\n    kit.setField('address', data.address);\n    kit.goNext(); // immediately skip past this page\n  }\n});\n</script>"
  }
}
```

#### 11. Fetch server data and display via template interpolation

Fetch data from your backend and store it in vars/globals, then display it in HTML content using `{{var:key}}` or `{{global:key}}` templates. No direct DOM manipulation needed.

```json
[
  {
    "id": "pricing_fetcher",
    "type": "html",
    "props": {
      "content": "<script>\nconst kit = window.CatalogKit.get();\n\nkit.on('pageenter:pricing', async () => {\n  kit.setButtonLoading(true);\n  try {\n    const res = await fetch('https://api.myapp.com/pricing?email=' + encodeURIComponent(kit.getField('email')));\n    const data = await res.json();\n    kit.setVar('plan_name', data.plan_name);\n    kit.setVar('monthly_price', '$' + data.price);\n    kit.setVar('discount_pct', data.discount ? data.discount + '%' : '');\n    kit.setGlobal('customer_tier', data.tier);\n  } catch (err) {\n    kit.setVar('plan_name', 'Standard');\n    kit.setVar('monthly_price', 'Contact us');\n  } finally {\n    kit.setButtonLoading(false);\n  }\n});\n</script>"
    }
  },
  {
    "id": "pricing_display",
    "type": "html",
    "props": {
      "content": "<div style=\"background:#f8fafc;border-radius:12px;padding:24px;text-align:center;\">\n  <h3>Your plan: {{var:plan_name}}</h3>\n  <div style=\"font-size:32px;font-weight:bold;\">{{var:monthly_price}}/mo</div>\n  <div style=\"color:#16a34a;\">{{var:discount_pct}} discount applied</div>\n</div>"
    }
  }
]
```

**Template syntax reference:**

| Syntax | Source | Reactive? | Example |
|--------|--------|-----------|---------|
| `{{field_id}}` | `kit.getField(id)` — form fields | Yes | `{{email}}`, `{{quantity}}` |
| `{{var:key}}` | `kit.getVar(key)` — script variables | Yes (triggers re-render) | `{{var:plan_name}}`, `{{var:monthly_price}}` |
| `{{global:key}}` | `kit.getGlobal(key)` — cross-page globals | No (read at render time, pair with setVar to force re-render) | `{{global:customer_tier}}` |

#### 12. Reading nested checkbox inputs in scripts (compound IDs)

When checkboxes have nested inputs (e.g. proof URLs, wallet addresses), you **must** use the compound ID format `checkboxId.optionValue.inputId` — not the bare input ID. This is the #1 scripting mistake with nested inputs.

```json
{
  "id": "thread_verifier",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\n\n// Given a checkboxes component:\n//   id: \"threads_checklist\"\n//   options: [{ value: \"thread_1\", inputs: [{ id: \"proof_url\", ... }] }, ...]\n\n// ✅ CORRECT — use compound ID: checkboxId.optionValue.inputId\nconst url1 = kit.getField('threads_checklist.thread_1.proof_url');\nconst url2 = kit.getField('threads_checklist.thread_2.proof_url');\n\n// ❌ WRONG — bare input ID returns undefined\n// kit.getField('proof_url')  → always undefined\n\n// Listen for changes on nested inputs — also needs compound ID\nkit.on('fieldchange:threads_checklist.thread_1.proof_url', (e) => {\n  console.log('Thread 1 URL changed to:', e.value);\n});\n\n// Validate all nested inputs before navigation\nkit.on('beforenext:my_page', async (e) => {\n  const urls = [];\n  for (let i = 1; i <= 3; i++) {\n    const url = kit.getField('threads_checklist.thread_' + i + '.proof_url');\n    if (url && url.trim()) urls.push(url);\n  }\n  if (urls.length === 0) {\n    kit.setValidationError('threads_checklist', 'Please paste at least one URL');\n    e.preventDefault();\n  }\n});\n</script>"
  }
}
```

**When to use vars vs globals vs fields:**
- **`setField`** — when the value should appear in the form submission payload and be subject to validation (user-facing data)
- **`setVar`** — when the value is intermediate/computed data you want to display in templates but NOT submit as form data (e.g. prices fetched from API, labels, status messages). Triggers re-renders.
- **`setGlobal`** — when the value must persist across page navigations and you only need to read it in scripts or display it once (e.g. auth tokens, user tier). Does NOT trigger re-renders on its own.

**Iframe `src` also supports these templates:** `{{var:key}}` and `{{global:key}}` work inside `type: "iframe"` `src` props for dynamic embedded URLs.

### Best practices

- **CRITICAL: Always call `window.CatalogKit.get()` first** to get an instance. `window.CatalogKit` is a registry object — it only has `.get()`. Calling `window.CatalogKit.on(...)` or `window.CatalogKit.getField(...)` directly will throw `"is not a function"`. The correct pattern is `const kit = window.CatalogKit.get(); kit.on(...)`. This is required for multi-catalog isolation.
- **Clean up listeners** when appropriate — use `kit.off()` or scope cleanup to `pageexit` to avoid stale listeners.
- **Use `setButtonLoading(true)`** before async operations and `setButtonLoading(false)` in a `finally` block.
- **Prefer `setValidationError`** over custom error DOM — it integrates with the native validation system and auto-scrolls.
- **Use `setComponentProp(id, 'hidden', true/false)`** for dynamic conditional UI — works on ALL component types (display components like `callout`, `banner`, `image` AND input components). Hidden components are excluded from validation.
- **Use scoped events** (`beforenext:page_id`) instead of global events with `if` checks inside the callback.
- **For DOM events** (blur, focus, click, input) use the DOM directly — don't wait for CatalogKit to add them.
- **Use `setTimeout` with `pageenter`/`pageexit`** for timed logic — always clean up timers on exit.
- Scripts execute in a try/catch — errors are logged to console but never crash the catalog renderer.
- Async callbacks on `beforenext` and `submit` are fully awaited — you can safely `await fetch()` inside them.
- **Use `kit.getQuizScores()`** on results pages to access scores with full context (question labels, options, answers, explanations).

### Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Server returns 422/error but no error shows on frontend | No `beforenext` listener wired up | Add an `html` component with a `beforenext` script that calls `setValidationError()` and `preventDefault()` — see [Server-Side Form Validation](#server-side-form-validation-common-pattern) |
| Error shows but page still navigates away | Missing `e.preventDefault()` in the `beforenext` callback | Add `e.preventDefault()` after setting the validation error |
| `window.CatalogKit.on is not a function` | Calling methods on the registry instead of an instance | Use `const kit = window.CatalogKit.get(); kit.on(...)` |
| `kit.getField()` returns `undefined` for a nested checkbox input | Using the bare input ID instead of the compound ID | Use `kit.getField("checkboxId.optionValue.inputId")` — e.g. `kit.getField("tasks.step_1.proof_url")` not `kit.getField("proof_url")`. See [Cookbook #12](#12-reading-nested-checkbox-inputs-in-scripts-compound-ids). |
| `fieldchange:inputId` listener never fires for nested input | Same cause — bare input ID doesn't match the compound key in form state | Use `kit.on("fieldchange:checkboxId.optionValue.inputId", ...)` |
| `setValidationError` doesn't display anything | Wrong `componentId` passed — must match the `id` of an input component on the current page | Check the component `id` in your schema matches the first argument |
| Script doesn't execute | `html` component not on the current page, or `content` prop missing `<script>` tags | Ensure the `html` component is in the page's `components` array and the content is wrapped in `<script>...</script>` |
| Checkout crashes or behaves differently in prod vs dev | Using DOM manipulation (`document.querySelector`) to click the cart checkout button after `preventDefault()` + `setTimeout` | Use a terminal page (no outgoing routing edges) so checkout triggers automatically, or call `kit.startCheckout()`. See [Triggering Checkout](#triggering-checkout) |
| `Unexpected token '<', "<!DOCTYPE"... is not valid JSON` on checkout | Outdated CLI — the dev server fetch interceptor is missing the `/checkout/intent` route | Run `npm install -g @officexapp/catalogs-cli@latest` to update. Ensure both `STRIPE_SECRET_KEY` and `STRIPE_PUBLISHABLE_KEY` are in your `.env` file in the catalog directory |
| Stripe checkout shows "not configured" error | Missing Stripe keys in `.env` | Create a `.env` file in your catalog project directory with `STRIPE_SECRET_KEY=sk_test_...` and `STRIPE_PUBLISHABLE_KEY=pk_test_...`. The dev server checks the catalog directory, not your home directory |

### Debug mode

Append `?debug_mode=verbose` or `?debug_mode=slim` to any catalog URL for console logging.

| Mode | What it logs |
|---|---|
| `slim` | CatalogKit registration, script execution, pageenter events, quiz score updates |
| `verbose` | All of slim + full formState at page transitions, quiz answer details, listener counts, fieldchange events |

Example: `https://yoursubdomain.catalogkit.cc/your-catalog?debug_mode=verbose`

---

## TypeScript-as-Config (Recommended)

Author catalogs as `.ts` files with full type safety, then push via CLI. This is the recommended workflow for AI agents — it's easier to read, write, and diff than raw JSON.

### Setup

Install the types package for full editor type-checking and autocomplete:

```bash
npm install --save-dev @officexapp/catalog-types
```

### How it works

1. **Create a `.ts` file** that exports a `CatalogSchema` object as the default export
2. **Push it** via the CLI — the CLI transpiles the TS, serializes functions (hooks, scripts), and uploads the resulting JSON schema to the API

### Example catalog.ts

```typescript
import { CatalogSchema } from "@officexapp/catalog-types";

const catalog: CatalogSchema = {
  name: "Spring Sale Landing Page",
  slug: "spring-sale",
  settings: {
    theme: { primary_color: "#4F46E5", font: "Inter", mode: "light" },
    completion: { message: "Thanks for signing up!" },
  },
  pages: {
    landing: {
      title: "Get Started",
      components: [
        { id: "hero", type: "heading", props: { text: "Spring Sale 2025", level: 1 } },
        { id: "email", type: "email", props: { label: "Your Email", required: true } },
        { id: "name", type: "short_text", props: { label: "Your Name", required: true } },
      ],
      submit_label: "Submit",
    },
  },
  routing: { entry: "landing", edges: [] },
};

export default catalog;
```

### Key points

- **Import types** from `@officexapp/catalog-types` for `CatalogSchema`, component types, etc.
- **Functions are auto-serialized** — you can write hooks (`on_enter`, `on_change`, `beforenext`) as real functions in TS. The CLI serializes them to strings for the JSON schema.
- **Default export** — the CLI expects `export default catalog` (or any default export of a `CatalogSchema` object).
- **No need to JSON.stringify** — the CLI handles the entire TS → JSON → API upload pipeline.

> **⚠️ AI Agent Warning:** Do NOT manually compile `.ts` catalog files to JSON. Do NOT use `tsc`, `tsx`, `esbuild`, or any manual TS → JSON conversion step. The CLI (`catalogs catalog push catalog.ts`) handles TypeScript transpilation, function serialization, and API upload in a single command. Just pass the `.ts` file directly to the CLI — it does everything.

### Pushing a TypeScript catalog

```bash
# Set your token (only required env var)
export CATALOG_KIT_TOKEN="cfk_..."

# Push and publish — pass the .ts file directly, the CLI handles compilation
catalogs catalog push catalog.ts --publish

# Or via npx (no global install needed)
npx @officexapp/catalogs-cli catalog push catalog.ts --publish
```

The CLI handles the entire pipeline internally: transpile TS → extract default export → serialize functions → upload JSON to API. You never need to run these steps yourself.

---

## CLI (`@officexapp/catalogs-cli`)

Install the CLI from npm:

```bash
npm install -g @officexapp/catalogs-cli
```

> **Node.js requirement:** Node 18–22 (LTS recommended). Node 24+ is **not supported** — it breaks the `tsx` ESM loader hooks used to load TypeScript catalog files at runtime. We recommend **Node 20 or 22 LTS**. The CLI enforces this at startup — if your Node version is outside the 18–22 range it will refuse to run and print instructions to install a compatible version via [NVM](https://github.com/nvm-sh/nvm).

### Configuration

The CLI requires an **explicit** auth token — it will never silently pick up tokens from config files or `.env` files. This prevents accidentally operating on the wrong account.

| Env Var | Required | Default | Description |
|---|---|---|---|
| `CATALOG_KIT_TOKEN` | **Yes** | — | Your API key (format: `cfk_...`) |
| `CATALOG_KIT_API_URL` | No | `https://api.catalogkit.cc` | Override API URL (rarely needed) |

**Auth resolution** (only two sources, in order):

1. **`--token` CLI flag** — `catalogs --token cfk_... catalog push schema.json`
2. **`CATALOG_KIT_TOKEN` environment variable** — `export CATALOG_KIT_TOKEN="cfk_..."`

No config files (`~/.catalog-kit/config.json`) or `.env` files are read. This is intentional — implicit token resolution caused a bug where multiple CLI sessions could silently use different accounts.

> **Safety feature:** Before any mutating operation (`catalog push`, `video upload`), the CLI prints your authenticated identity (subdomain + email) so you can confirm you're operating on the correct account.

> **Common mistake:** The env var is `CATALOG_KIT_TOKEN`, not `CATALOGS_TOKEN`. The API URL is hardcoded to `https://api.catalogkit.cc` by default — do not set `CATALOGS_API_URL` (wrong name).

### Commands

```bash
catalogs catalog dev my-catalog.ts             # Preview locally at http://localhost:3456
catalogs catalog dev my-catalog.ts --port 8080 # Custom port
catalogs catalog push schema.json --publish    # Push a JSON catalog (auto-uploads local assets)
catalogs catalog push catalog.ts --publish     # Push a TypeScript catalog (functions auto-serialized)
catalogs catalog list                           # List all your catalogs
catalogs video upload ./intro.mp4               # Upload a video
catalogs video status VIDEO_ID                  # Check transcoding progress
catalogs whoami                                 # Show your user identity, email, subdomain, credits
```

Or run without installing via `npx @officexapp/catalogs-cli <command>`.

### Quick start example

```bash
# 1. Set your token (REQUIRED — no other auth sources are used)
export CATALOG_KIT_TOKEN="cfk_..."

# 2. Test connection — verify you're on the right account
catalogs whoami

# 3. Preview locally (no token needed — purely offline)
catalogs catalog dev my-catalog.ts

# 4. Push to cloud (prints your identity before pushing)
catalogs catalog push my-catalog.ts --publish

# Or pass the token directly:
catalogs --token cfk_... catalog push my-catalog.ts --publish
```

---

## Local Development & Local Assets

### `catalogs catalog dev <file>`

Preview your catalog locally without deploying. Starts a local server with hot reload:

```bash
catalogs catalog dev my-catalog.ts
# => Local preview: http://localhost:3456
# => Assets served from: /path/to/your/catalog/dir
# => Watching for changes...
```

No token required — `dev` mode is purely local. Edit your catalog file and save — the browser auto-refreshes via SSE.

**Dev server features:**
- **Production-quality rendering** — uses the same fonts (Outfit + DM Sans), CSS classes (`.cf-*`), and component styling as the live site
- **Shared engine** — conditions, routing, and validation use the exact same code as production (inlined at build time from `shared/engine/`), eliminating dev/prod divergence
- **Auto-reload** — browser refreshes automatically when you save your catalog file (SSE-based, no manual refresh needed)
- **Cover page layouts** — dark gradient overlay with floating content animation, matching production exactly
- **Interactive form state** — inputs maintain state, conditional routing works based on form values
- **Form validation** — `validatePage()` runs before advancing: required fields, email/URL/number format, min/max constraints. Red border + error message on invalid fields, auto-scroll to first error
- **Page actions** — `page.actions` render as styled buttons (primary, secondary, danger, ghost) with icon, side_statement, and reassurance support. Actions validate, set `__action_{pageId}` in formState, check offer acceptance, and route to next page
- **Prefill & default values** — `default_value` on components initializes formState on mount. URL param prefill via `settings.url_params.prefill_mappings`. `prefill_mode: "hidden"` hides prefilled fields; `prefill_mode: "readonly"` renders them as display-only text
- **Auto-skip pages** — pages with `auto_skip: true` are skipped (via `replaceState`) when all visible required inputs already have values
- **Browser history** — pushState/popstate integration: browser back button returns to previous page, auto-skip uses replaceState to stay invisible in history
- **Session persistence** — formState, currentPageId, and history are saved to localStorage (keyed by catalog slug). On return, a "Resume" / "Start Over" prompt appears if the user left mid-funnel. Cleared on submission
- **Cart & offers** — full cart UI (floating button + slide-out drawer) that collects page offers via `accept_field`/`accept_value`. Cart persists across pages, items can be removed
- **Cart settings** — `settings.cart`: `position` (4 corners), `hide_button`, `title`, `checkout_button_text`, `destination_url` (external redirect), `destination_page` (internal page navigation)
- **Sticky bottom bar** — `settings.sticky_bar` or `page.sticky_bar`: delay, scroll direction show/hide, template interpolation (`{{fieldId}}`), style variants (solid, glass, glass_dark, gradient), primary action dispatch, secondary actions, `field:<id>:<value>` dispatch
- **`__variants` resolution** — `*__variants` keys on component props and actions are resolved against hints (from `catalog.hints.defaults` + URL `?variant=slug`). Highest-scoring condition match wins
- **CatalogKit scripting API** — `window.CatalogKit` exposes `getField`, `setField`, `getPageId`, `goNext`, `goBack`, `goToPage`, `on`/`off` event listeners (`pageenter`, `pageexit`, `fieldchange`, etc.), and `setValidationError`. Inline `<script>` tags in `html` components are executed via `new Function()` (fingerprinted to avoid re-execution on re-renders)
- **Video watch tracking** — native `<video>` elements report `watch_percent` via `timeupdate`. Pages with `require_watch_percent` block navigation until the threshold is met
- **Hidden components respected** — components with `hidden: true` (at component level or `props.hidden`) are properly excluded from rendering, matching production behavior
- **Visibility conditions** — components with `visibility` condition groups are live-evaluated against formState and URL params (all 13 operators supported)
- **Dev toolbar** — fixed bar at top showing: catalog slug + schema version badge (`v1.0`), Pages button, Inspect button, Debug toggle, Stripe status indicator (green = enabled, yellow = stubbed), Events viewer, Prod link (opens production URL in new tab when published), and minimize button. Click minimize (`—`) to collapse to a floating pill in the top-right; click the pill to re-expand. Minimized state persists across reloads via sessionStorage
- **Pages graph** — click "Pages" in the toolbar to open a full-screen modal showing a visual graph of all pages and routing edges. Powered by Cytoscape.js with breadthfirst layout from the entry point. Entry page has a green border, current page glows with theme color. Edge labels show "conditional" or "default". Click any page node to navigate directly to it. Supports pan and zoom for large catalogs
- **Element inspector** — click the "Inspect" button in the toolbar (or hold Shift+Alt) to activate. Cursor changes to crosshair. Hover any component (including cart, sticky bar, checkout page, popups) to see a tooltip with its reference path, type, and label. Click to copy structured JSON with `pageId/componentId` reference for AI agents. Inspector automatically deactivates after copying, or click the Inspect button again to turn it off
- **Events viewer** — click "Events" in the toolbar to open a full-screen modal with a searchable, filterable table of all dev events (page views, field changes, checkout events). Filter by event type, search event data, and see timestamps. Events also stream to terminal and SSE endpoint
- **Debug mode** — click "Debug" in the toolbar to cycle through modes: none → slim → verbose. Mode is reflected in URL params (`?debug_mode=slim` or `?debug_mode=verbose`) and persists across page reloads via `history.replaceState()`
- **Catalog version** — schema version displayed as a badge (e.g. `v1.0`) next to the catalog slug in the toolbar, pulled from `schema.schema_version`
- **Production link** — "Prod" link in toolbar opens the published catalog URL in a new tab. URL is auto-resolved from: (1) catalog's `url` field, (2) subdomain-based URL (`https://{subdomain}.catalogkit.cc/{slug}`), (3) fallback `https://catalogkit.cc/c/{catalog_id}`. Shows as gray/disabled when catalog isn't published yet
- **Validation overlay** — validation errors and warnings appear in a fixed overlay at bottom-left, reappears on hot reload, dismissible via close button
- **Routing** — conditional page routing works locally using the shared engine (supports all operators, condition groups, edge priority, default edges)
- **Local Stripe checkout** — add `STRIPE_SECRET_KEY=sk_test_...` and `STRIPE_PUBLISHABLE_KEY=pk_test_...` to your `.env` and the dev server creates real Stripe checkout sessions locally via Stripe REST API. The publishable key enables inline card fields; the secret key enables hosted/embedded checkout sessions and PaymentIntent creation. Supports subscriptions, trials, promo codes, customer email prefill, and `stripe_price_id` references. Falls back to informative stub when no keys found
- **Local dev events** — page views, field changes, and checkout events stream to your terminal and broadcast via SSE at `/__dev_events_stream`. AI agents can subscribe. Recent events available as JSON at `GET /__dev_events?limit=50`. Zero production pollution

**Dev Preview Feature Parity:**
- **Supported component types (inputs):** short_text, long_text, email, phone, url, number, password, dropdown, multiple_choice, checkboxes, picture_choice, slider, star_rating, switch/checkbox, opinion_scale, date, datetime, time, date_range, address, currency, file_upload (stubbed), signature (stubbed)
- **Supported component types (display):** heading, paragraph, image, video, html, banner, callout, divider, pricing_card, testimonial, faq, accordion, timeline, file_download, iframe, table, social_links, tabs, countdown, comparison_table, progress_bar, modal
- **Prod-only features** (not in dev preview): popup/overlay modals with embedded inputs, quiz scoring & reveal, full analytics pipeline, AI prefill, EVM/Solana/Bitcoin address inputs, custom components via `window.__catalogkit_components`

### Local file references

Reference images, videos, scripts, and files with relative paths in your catalog schema. They work locally in `dev` mode AND are automatically uploaded to CDN on `push`:

```typescript
// my-catalog.ts
export default {
  slug: "my-funnel",
  schema_version: "1.0",
  pages: {
    intro: {
      title: "Welcome",
      components: [
        {
          id: "hero",
          type: "image",
          props: { src: "./images/hero.png" }         // local file
        },
        {
          id: "demo",
          type: "video",
          props: { src: "./videos/demo.mp4" }         // local file
        },
        {
          id: "custom_logic",
          type: "html",
          props: {
            content: '<script src="./scripts/checkout.js"></script>'  // local script
          }
        },
        {
          id: "brochure",
          type: "file_download",
          props: {
            src: "./files/brochure.pdf",              // local file
            filename: "brochure.pdf"
          }
        }
      ]
    }
  },
  // ...
};
```

**How it works:**

| Context | What happens to `./images/hero.png` |
|---|---|
| `catalogs catalog dev` | Served from local filesystem at `http://localhost:3456/assets/images/hero.png` |
| `catalogs catalog push` | Auto-uploaded to CDN, replaced with `https://d1abc...cloudfront.net/images/compressed/.../hero.webp` |

**Rules:**
- Relative paths (`./`, `../`) are resolved from the catalog file's directory
- Absolute URLs (`https://...`) are left untouched
- Images are auto-compressed to WebP on upload (free)
- Videos are auto-transcoded to HLS on upload (credit cost applies)
- Other files (PDFs, ZIPs, docs) are uploaded as-is (credit cost per 50MB)
- No folder structure required — just place files wherever you want relative to your catalog file

**Example project layout:**

```
my-project/
  my-catalog.ts          # your catalog schema
  images/
    hero.png
    logo.svg
  videos/
    demo.mp4
  scripts/
    calculator.js
  files/
    brochure.pdf
```

### Dev mode behavior

| Feature | Dev mode | Production |
|---|---|---|
| Stripe checkout | Live (test key) if `STRIPE_SECRET_KEY` in `.env`, otherwise stubbed with payload preview | Live |
| Events | Local only — terminal + SSE stream (`/__dev_events_stream`) | Production analytics |
| File serving | Local filesystem | CDN (CloudFront) |
| Hot reload | On file save (SSE) | N/A |
| Debug panel | `Ctrl+D` — formState, cart, routing, events | N/A |
| Validation | Live overlay in dev banner | N/A |

---

## AI Variant Routing & Prefill

Automatically route visitors to the best catalog variant **and pre-fill qualifying form fields** using natural language hints. Instead of creating hundreds of variants for every audience combination, use a single catalog with qualifying questions that get auto-answered and skipped when context is available.

### Route a visitor with a hint (GET — query param)

```
# Using user_id:
GET https://api.catalogkit.cc/public/route-variant?user_id=USER_ID&slug=my-catalog&hint="female entrepreneur interested in social media"

# Using custom domain instead:
GET https://api.catalogkit.cc/public/route-variant?domain=funnels.mycompany.com&slug=my-catalog&hint="female entrepreneur interested in social media"
```

> **Note:** Use quotes around the hint value for readability — browsers automatically encode `"` to `%22` and spaces to `+`/`%20`. Both `hint` and `hints` are accepted as the param name. Provide either `user_id` or `domain`.

### Route a visitor with a hint (POST — JSON body)

If URL encoding is a concern, use the POST alternative with a JSON body:

```bash
# Using user_id:
curl -X POST https://api.catalogkit.cc/public/route-variant \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "USER_ID",
    "slug": "my-catalog",
    "hint": "female entrepreneur interested in social media"
  }'

# Using custom domain:
curl -X POST https://api.catalogkit.cc/public/route-variant \
  -H "Content-Type: application/json" \
  -d '{
    "domain": "funnels.mycompany.com",
    "slug": "my-catalog",
    "hint": "female entrepreneur interested in social media"
  }'
```

Both `hint`/`hints` and `user_id`/`domain` are accepted.

**Response (same for GET and POST):**
```json
{
  "ok": true,
  "data": {
    "variant_slug": "problem-aware-female",
    "target_slug": "welcome-female-catalog",
    "reason": "ai_matched",
    "prefill": {
      "company_size": "11-50",
      "interest": "social_media"
    }
  }
}
```

`reason` values: `ai_matched` (LLM picked best match), `ai_prefill_only` (no variants, only field prefill), `ai_cached` (cached result), `weighted_random` (randomly selected by weight), `hybrid_ai` (hybrid mode, LLM picked), `hybrid_random_fallback` (hybrid mode, LLM failed, random pick), `single_variant` (only one variant exists), `no_variants` (catalog has no variants), `fallback` (LLM couldn't decide, returned first variant). `target_slug` is included when the variant routes to a different catalog. `prefill` is included when AI-prefillable fields were matched from the hint.

**Fallback behavior:**
- 0 variants, no prefillable fields → returns null, base catalog renders as-is
- 0 variants, has prefillable fields → LLM call for prefill only, returns `reason: "ai_prefill_only"`
- 1 variant → returns it directly (skip variant LLM), still runs prefill if fields exist
- 2+ variants → single LLM call for variant + prefill; if LLM fails, falls back to first variant
- API error → base catalog renders normally (routing failure is silent)
- Variants with `enabled: false` are excluded from all routing counts above (but remain accessible via direct URL)

### Frontend hint URLs

The frontend handles AI routing automatically — just add `hint` to the URL. Works with path-based URLs and custom domains:

```
# Path-based URL:
https://SUBDOMAIN.catalogkit.cc/my-catalog?hint="female entrepreneur"&ref=253

# Custom domain URL (works the same way):
https://funnels.mycompany.com/my-catalog?hint="female entrepreneur"&ref=253

# Silent redirect (for affiliates — suppresses event tracking):
https://SUBDOMAIN.catalogkit.cc/my-catalog?hint="problem aware male"&silent_redirect=true&ref=253

# After AI routing resolves, browser URL updates to the target catalog slug:
# (uses target_slug when the variant routes to a different catalog, otherwise variant_slug)
https://SUBDOMAIN.catalogkit.cc/my-catalog/welcome-female-catalog?ref=253
```

The frontend shows a branded loading screen (with optional rotating tips from `settings.loading_tips`) for up to 10 seconds while AI routing and prefill resolve. Once resolved, visitors see the personalized catalog with qualifying pages auto-skipped. If routing fails or times out, the base catalog renders normally.

### AI Prefill — Skip Qualifying Questions

Mark form fields as AI-prefillable to let the hint auto-answer them. Combined with `auto_skip: true` on pages, visitors skip past questions the AI already answered — landing deeper in the funnel instantly.

**Enable AI prefill on a field:**

```json
{
  "id": "company_size",
  "type": "dropdown",
  "agent_hint": "Number of employees in the company",
  "ai_prefill": {
    "enabled": true,
    "confidence": 0.8,
    "instructions": "Match to the closest range"
  },
  "prefill_mode": "readonly",
  "props": {
    "label": "Company Size",
    "options": [
      { "value": "1-10", "label": "1-10 employees" },
      { "value": "11-50", "label": "11-50 employees" },
      { "value": "51-200", "label": "51-200 employees" },
      { "value": "201+", "label": "201+ employees" }
    ]
  }
}
```

**`ai_prefill` config:**

| Field | Type | Default | Description |
|---|---|---|---|
| `enabled` | `boolean` | — | Opt-in toggle (required) |
| `confidence` | `number` | `0.7` | Minimum confidence threshold (0-1). The AI assigns a confidence score per field; values below this are discarded |
| `instructions` | `string` | — | Extra guidance for the AI when filling this specific field (e.g., "Only fill if the hint mentions a specific company name") |

**How it works:**

1. Visitor arrives with `?hint="50-person digital marketing agency interested in social media"`
2. The API extracts all `ai_prefill.enabled` fields from the catalog schema
3. A single LLM call handles both variant routing and field prefill
4. For selection fields (dropdown, multiple_choice, etc.), the AI picks from the exact option values — invalid values are rejected server-side
5. For free-text fields, the AI only fills when the hint contains a near-exact match (controlled by `instructions` and `confidence`)
6. Prefilled values merge into form state with priority: `default_value` < `AI prefill` < `URL params` (explicit always wins)
7. Pages with `auto_skip: true` are skipped when all visible fields have values

**Works without variants too.** A catalog with zero variants but AI-prefillable fields will still process the hint and prefill qualifying questions — no variant setup required.

**Loading tips:** Add `loading_tips` to your catalog settings to show rotating tips/messages during the AI loading screen:

```json
{
  "settings": {
    "loading_tips": [
      "We're personalizing your experience...",
      "Finding the best options for you",
      "Almost ready!"
    ]
  }
}
```

---

## Sandbox Mode

Edit catalogs safely without affecting production. A sandbox is a full clone of your catalog with its own URL and schema — make changes, preview live, and promote when ready.

### Create a sandbox

```
POST https://api.catalogkit.cc/api/v1/catalogs/:id/sandbox
```

```json
{
  "suffix": "redesign-v2"
}
```

**Response (201):**
```json
{
  "ok": true,
  "data": {
    "catalog_id": "01ABC...",
    "slug": "spring-sale--redesign-v2",
    "name": "Spring Sale Landing Page (Sandbox: redesign-v2)",
    "sandbox_of": "01HXY...",
    "parent_slug": "spring-sale",
    "url": "https://SUBDOMAIN.catalogkit.cc/spring-sale--redesign-v2"
  }
}
```

The sandbox is a regular catalog with its own URL. Edit it freely using `PUT /api/v1/catalogs/:sandbox_id` — your production catalog is untouched. The frontend shows an amber "SANDBOX" banner so you always know you're in sandbox mode.

### List sandboxes for a catalog

```
GET https://api.catalogkit.cc/api/v1/catalogs/:id/sandboxes
```

### Promote sandbox to production

Copy the sandbox schema to the parent catalog:

```
POST https://api.catalogkit.cc/api/v1/catalogs/:sandbox_id/promote
```

```json
{
  "delete_sandbox": true
}
```

By default the sandbox is deleted after promotion. Set `"delete_sandbox": false` to keep it.

### Discard a sandbox

```
DELETE https://api.catalogkit.cc/api/v1/catalogs/:sandbox_id
```

### Listing catalogs with sandboxes

By default, `GET /api/v1/catalogs` hides sandboxes. Add `?include_sandboxes=true` to include them. Each catalog response includes `sandbox_of` (null for regular catalogs, parent catalog ID for sandboxes). Results are paginated — use `?limit=50&cursor=...` to page through large lists.

---

## Element Inspector (DevEx)

Built-in developer tool for AI agent workflows. Hold **Shift+Alt** and hover over any element in a live catalog to see rich context — then click to copy a structured JSON block that an AI agent can use to pinpoint exactly what the user is referring to.

**How to use:**

1. Open any catalog in the browser
2. Hold **Shift+Alt** — an "Inspector active" indicator appears (shows the catalog slug and variant if applicable)
3. Hover over any element — it highlights with an indigo border and shows a multi-line tooltip with:
   - **Reference path** (e.g. `landing/hero-title`) and component type
   - **Label text** extracted from the component's DOM
   - **Catalog context** — slug, catalog ID prefix, variant slug, sandbox status
4. **Click anywhere** to copy a structured JSON block to clipboard
5. Paste the JSON into your AI agent conversation — it contains everything needed to locate and modify the element

**Copied JSON format:**

```json
{
  "ref": "landing/hero-title",
  "page_id": "landing",
  "component_id": "hero-title",
  "component_type": "heading",
  "label": "Get Started Today",
  "schema_path": "schema.pages.landing.components[id=\"hero-title\"]",
  "catalog_id": "01HXY...",
  "catalog_slug": "spring-sale",
  "variant_slug": "new-headline",
  "api_endpoint": "PUT https://api.catalogkit.cc/api/v1/catalogs/01HXY..."
}
```

**Fields in the copied JSON:**

| Field | Description |
|---|---|
| `ref` | Human-readable reference: `pageId/componentId` or `pageId/componentId#subElement` |
| `page_id` | The page containing this component |
| `component_id` | The component's unique ID within its page |
| `component_type` | Component type (e.g. `heading`, `email`, `multiple_choice`, `image`) |
| `label` | The visible label/heading text (if present) |
| `sub_element` | Sub-element within the component (e.g. `label`, `button`, `input:text`, `radio`, `option:b`) |
| `schema_path` | Exact path in the catalog schema JSON |
| `catalog_id` | Full catalog ID for API calls |
| `catalog_slug` | URL slug of the catalog |
| `variant_slug` | Active variant slug (if viewing a variant) |
| `variant_id` | Active variant ID (if viewing a variant) |
| `sandbox_of` | Parent catalog ID (if this is a sandbox) |
| `api_endpoint` | Ready-to-use PUT endpoint for updating the catalog |

**Sub-element targeting:** The inspector drills into child elements within components. Hovering a label, button, input, image, heading, or option card shows a more specific reference with a `#` suffix — e.g. `landing/email_field#label`, `landing/cta#button`, `quiz_page/q1#option:b`.

**Global UI elements:** The inspector also covers elements outside the page content area. These use `page_id: "__global"` or `page_id: "__checkout"`:

| Element | `component_id` | `component_type` |
|---|---|---|
| Floating cart button | `__cart_button` | `cart_button` |
| Cart sidebar drawer | `__cart_drawer` | `cart_drawer` |
| Individual cart item | `__cart_item_{offer_id}` | `cart_item` |
| Cart checkout button | `__cart_checkout_button` | `cart_checkout_button` |
| Sticky bottom bar | `__sticky_bottom_bar` | `sticky_bottom_bar` |
| Checkout page | `__checkout_page` | `checkout_page` |
| Checkout pay button | `__checkout_pay_button` | `checkout_pay_button` |
| Popup dialog | `__popup_{popupId}` | `popup` |

**Detail panel:** After clicking to copy, a dismissible panel appears in the bottom-right showing the full JSON that was copied. This persists after releasing Shift+Alt so you can review what was captured.

**AI agent workflow example:**

1. User holds Shift+Alt, hovers over a heading, clicks to copy
2. User pastes into Claude: "change this element: `{...copied JSON...}` to say 'Welcome Back'"
3. AI agent reads the `catalog_id`, `page_id`, `component_id`, and `api_endpoint` from the JSON
4. AI agent fetches the catalog via `GET /api/v1/catalogs/{catalog_id}`, finds the component at `schema.pages.{page_id}.components` where `id == component_id`, updates the text, and PUTs back

---

## Cart & Checkout

Catalog Kit has a **built-in cart and checkout system**. You do NOT need to build custom cart HTML — the platform provides a floating cart button, a slide-out cart drawer, and a full checkout page out of the box.

### How it works

1. **Page offers** — each page can define an `offer` object. When the visitor accepts the offer (via a form field), the item is automatically added to the cart.
2. **Cart button** — a floating button appears in the bottom-right corner showing the cart item count. It only appears when items are in the cart.
3. **Cart drawer** — clicking the cart button opens a right-side slide-out panel showing all accepted offers with images, titles, prices, and a remove button. A "Proceed to Checkout" button takes the visitor to the checkout page.
4. **Checkout page** — displays an order summary of all cart items and redirects to Stripe Checkout to complete payment.

### Display cart vs payment items

The cart system has **two independent layers**:

| Layer | What it controls | API methods |
|-------|-----------------|-------------|
| **Display cart** | What the visitor *sees* — the cart drawer, floating badge count, order summary UI | `addDisplayItem()`, `removeDisplayItem()`, `getDisplayItems()` |
| **Payment items** | What Stripe actually *charges* — the line items sent to Stripe Checkout | `setPaymentItems()`, `getPaymentItems()` |

**By default, these are the same.** If you don't call `setPaymentItems()`, the display items are sent to Stripe as-is. This is the normal flow for page-offer-based carts.

**When you decouple them**, you can show one thing in the cart UI and charge something different via Stripe. Use cases:

- **Bundled pricing** — display "Premium Bundle — $99" in the cart, but send itemized line items to Stripe for accounting (Team License $75 + Onboarding $24)
- **Dynamic discounts** — show original prices in the cart UI but send discounted amounts to Stripe
- **AI agent cart building** — an AI agent programmatically builds a custom cart without needing page offers in the schema
- **Upsell injection** — add a bonus item to payment items that isn't shown in the visual cart (e.g. a processing fee)

```javascript
const kit = window.CatalogKit.get();

// Show a bundle in the cart UI
kit.addDisplayItem({
  offer_id: "bundle-99", page_id: "custom",
  title: "Premium Bundle", price_display: "$99", amount_cents: 9900,
});

// But charge itemized line items via Stripe
kit.setPaymentItems([
  { offer_id: "license", page_id: "custom", title: "Team License (5 seats)", amount_cents: 7500 },
  { offer_id: "onboarding", page_id: "custom", title: "Onboarding Fee", amount_cents: 2400 },
]);

kit.startCheckout(); // Cart UI shows "Premium Bundle $99", Stripe charges two line items
```

To clear the override and go back to using display items for Stripe: `kit.setPaymentItems(null)`.

### Checkout settings

Configure checkout in `settings.checkout`:

```jsonc
{
  "settings": {
    "checkout": {
      "payment_type": "one_time",             // "one_time" | "subscription" | "pay_what_you_want"
      "title": "Complete Your Order",
      "subheading": "No commitment. Cancel anytime.", // Optional plain text below the title
      "stripe_publishable_key": "pk_live_...",

      // Payment options
      "allow_discount_codes": true,            // Show promo code field at Stripe checkout
      "free_trial": { "enabled": true, "days": 14 },  // Subscriptions only
      "payment_methods": ["card", "link"],
      "payment_description": "My Product",
      "client_reference_id": "{{comp_email}}", // Template strings supported

      // Prefill from form fields
      "prefill_fields": {
        "customer_email": "comp_email",        // Component ID to read email from
        "customer_name": "comp_name",
        "customer_phone": "comp_phone"
      },

      // Skip
      "allow_skip": true,                      // Allow "Continue without paying" button (default: true, set false to require payment)
      "skip_button_text": "No thanks, continue free", // Custom text for skip button (default: "Continue without paying")

      // Appearance
      "ui_mode": "hosted",                     // "hosted" (redirect) | "embedded" (Stripe embedded checkout)
                                               // When omitted + stripe_publishable_key is set: inline card fields (default)
      "button_text": "Subscribe Now",
      "summary_lines": [                       // Display-only lines below cart items (no calculations — editor defines all text)
        { "label": "Subtotal", "value": "$97.00" },
        { "label": "Tax (8%)", "value": "$7.76" },
        { "label": "Total", "value": "$104.76", "bold": true }
      ],
      "testimonial": {
        "enabled": true,
        "text": "Best investment I've made...",
        "author": "Jane S.",
        "avatar": "https://..."
      },
      "show_disclaimer": true,
      "disclaimer_text": "By purchasing you agree to our Terms",
      "components": [],                        // Extra display components below order summary

      // After payment
      "send_receipt": true,
      "success_redirect": "https://...",
      "success_page_id": "thank_you"
    }
  }
}
```

You also need to set your Stripe secret key via the settings API (see "Update settings" above):
```json
{ "stripe_secret_key": "rk_live_..." }
```

### Page offers (cart items)

Define an `offer` on any page. When the visitor's form field matches the `accept_value`, the offer is added to the cart automatically:

```jsonc
{
  "id": "pricing",
  "title": "Choose Your Plan",
  "components": [
    {
      "id": "offer_choice",
      "type": "multiple_choice",
      "label": "Select an option",
      "options": [
        { "value": "accept", "label": "Yes, I want this!" },
        { "value": "decline", "label": "No thanks" }
      ]
    }
  ],
  "offer": {
    "id": "growth-bundle",
    "title": "Growth Bundle",
    "price_display": "$49/mo",
    "price_subtext": "/month",          // Gray subtitle below price in cart/checkout.
    // Auto-derives from interval when omitted (e.g. interval:"month" → "/month").
    // Set to "" to suppress entirely. Use any string to override (e.g. "per year", "billed annually").
    "stripe_price_id": "price_...",   // Use a Stripe Price ID...
    // OR use inline pricing (no pre-configured Stripe price needed):
    // "amount_cents": 4900,          // $49.00
    // "currency": "usd",             // default: "usd"
    // "payment_type": "subscription", // "one_time" | "subscription" | "pay_what_you_want"
    // "interval": "month",           // for subscriptions: "day" | "week" | "month" | "year"
    "image": "https://...",
    "accept_field": "offer_choice",     // Component ID to watch
    "accept_value": "accept"            // Value that triggers add-to-cart
  }
}
```

**IMPORTANT:** Every offer that goes through Stripe checkout must have either `stripe_price_id` (a pre-configured Stripe Price) OR `amount_cents` (inline pricing). Without one of these, checkout will fail with a "Missing required param" error. Use `price_display` for the human-readable price shown in the UI.

**`price_subtext`** — the gray subtitle text shown below the price in the cart and checkout order summary (e.g. "/month", "per year", "billed annually"). When omitted, it auto-derives from `interval` (e.g. `interval: "month"` → "/month"). Set to `""` to suppress it entirely.

**Free trial pricing example:** For trial offers, put the main price in `price_display` and the trial info in `price_subtext` so they render on separate lines:

```jsonc
{
  "offer": {
    "price_display": "$30/month",
    "price_subtext": "7-day free trial included",  // Renders as smaller gray text below the green price
    "amount_cents": 3000,
    "payment_type": "subscription",
    "interval": "month"
  }
}
```

Do NOT combine everything into `price_display` (e.g. `"7-Day Free Trial then $30/month"`) — this prevents the two-line layout and makes the subtext invisible.

Cart items accumulate across pages — each page can present a different offer (e.g., main product on page 1, upsell on page 2, order bump on page 3). All accepted offers become Stripe Checkout line items.

### Cart item buttons

Cart items support an optional `button` that renders as a side link next to the price in the cart drawer and checkout summary:

```jsonc
{
  "offer": {
    "id": "growth-bundle",
    "title": "Growth Bundle",
    "price_display": "$49/mo",
    "stripe_price_id": "price_...",
    "button": { "label": "Details", "url": "https://example.com/growth", "style": "secondary", "size": "sm" }
  }
}
```

### Checkout page layout

The checkout page uses a two-column layout on desktop (single column on mobile):

**Left column:**
- Order summary (cart items with images, prices, remove buttons)
- Summary lines — tax, total, etc. (when `summary_lines` is set)
- Coupon code input (when `allow_discount_codes` is enabled)
- Testimonial (when `testimonial.enabled` is true)
- Custom components (when `components` array is set)

**Right column (sticky):**
- Item count + trial badge
- Customer info fields (email, name, phone — from `prefill_fields`)
- Card input fields (Stripe Elements — when `stripe_publishable_key` is set)
- Pay button + skip button (when `allow_skip` is true)
- Error messages
- 3DS "Extra verification required" notice (when `require_3ds` is true)
- "Powered by Stripe" badge

### Skip button

The skip button lets visitors continue without paying. It appears below the pay button in both embedded and inline card modes.

| Field | Type | Default | Description |
|---|---|---|---|
| `allow_skip` | `boolean` | `true` | Show/hide the skip button. Set `false` to require payment. |
| `skip_button_text` | `string` | `"Continue without paying"` | Custom text for the skip button. |

```jsonc
// Example: hide the skip button entirely
{ "checkout": { "allow_skip": false } }

// Example: customize the skip button text
{ "checkout": { "skip_button_text": "Maybe later — continue for free" } }
```

### Order summary lines

Add display-only rows below the cart items in the order summary card. Useful for subtotals, taxes, fees, discounts, or totals. **No calculations are performed** — the editor defines all label and value text exactly as it should appear.

| Field | Type | Required | Description |
|---|---|---|---|
| `summary_lines[].label` | `string` | Yes | Left-aligned label (e.g. "Subtotal", "Tax (8%)") |
| `summary_lines[].value` | `string` | Yes | Right-aligned value (e.g. "$97.00") |
| `summary_lines[].bold` | `boolean` | No | When `true`, renders the row in bold (use for totals) |

```jsonc
// Example: subtotal + tax + total
{
  "checkout": {
    "summary_lines": [
      { "label": "Subtotal", "value": "$97.00" },
      { "label": "Tax (8%)", "value": "$7.76" },
      { "label": "Total", "value": "$104.76", "bold": true }
    ]
  }
}

// Example: discount line
{
  "checkout": {
    "summary_lines": [
      { "label": "Subtotal", "value": "$150.00" },
      { "label": "Launch Discount", "value": "-$53.00" },
      { "label": "Total", "value": "$97.00", "bold": true }
    ]
  }
}
```

### Checkout UI modes

The checkout page supports three payment modes, determined automatically:

| Config | Mode | Behavior |
|---|---|---|
| `stripe_publishable_key` set, no `ui_mode` | **Inline card fields** (default) | Stripe Elements CardElement renders in the right column. Payment confirmed client-side via `/checkout/intent` endpoint. Works with test cards. |
| `stripe_publishable_key` set, `ui_mode: "embedded"` | **Embedded checkout** | Stripe's full embedded checkout UI renders in the right column. Stripe handles all card input and payment. |
| No `stripe_publishable_key`, or `ui_mode: "hosted"` | **Hosted redirect** | Clicking the pay button redirects to Stripe's hosted checkout page. |

**Inline card fields** is the recommended default for local development and production. It keeps the user on your page, supports 3DS challenges (Stripe handles the popup), and works with all Stripe test cards (`4242 4242 4242 4242`, `4000 0027 6000 3184` for 3DS, etc.).

### Supported checkout targets

Stripe hosted checkout is the default, but you can also redirect to: Polar.sh, LemonSqueezy, Gumroad, or any custom URL.

### Cart customization (`settings.cart`)

Customize the floating cart button, slide-out drawer, and checkout flow. All HTML fields support `{{field_id}}` template interpolation.

```jsonc
{
  "settings": {
    "cart": {
      // Config
      "icon": "bag",                           // "cart" (default) | "bag" | "basket" | image URL
      "title": "Your Selection",               // Drawer title (default: "Your Cart")
      "checkout_button_text": "Complete Order", // Override "Proceed to Checkout" text
      "destination_url": "https://pay.example.com?email={{email}}", // External URL (skips built-in checkout)
      "destination_page": "checkout",          // Internal page ID (navigates within funnel instead of Stripe overlay)
      "position": "bottom-left",               // "bottom-right" (default) | "bottom-left" | "top-right" | "top-left"
      "hide_button": false,                    // Hide floating button (use kit.openCart() to open programmatically)

      // HTML/CSS slots
      "header_html": "<div class='my-header'>Custom Header</div>",  // Replaces default header
      "footer_html": "<p>30-day money-back guarantee</p>",          // Inserted above checkout button
      "empty_html": "<p>Browse our offers to get started</p>",      // Replaces default empty state
      "css": ".ck-cart-drawer { border-radius: 16px; } .ck-cart-checkout-btn { background: linear-gradient(135deg, #667eea, #764ba2) !important; }"
    }
  }
}
```

**CSS class hooks** for external styling: `.ck-cart-drawer`, `.ck-cart-header`, `.ck-cart-empty`, `.ck-cart-item[data-offer-id="..."]`, `.ck-cart-footer`, `.ck-cart-footer-custom`, `.ck-cart-checkout-btn`.

**Icon presets:**
| Preset | Description |
|--------|-------------|
| `"cart"` | Shopping cart (default) |
| `"bag"` | Shopping bag |
| `"basket"` | Shopping basket |
| `"https://..."` | Custom image URL |

### Cart events (analytics + JS)

The following events are tracked automatically: `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`.

**JavaScript events** via `window.CatalogKit`:

| Event | Payload | Cancelable | Description |
|-------|---------|------------|-------------|
| `cart_add` | `{ item, items }` | No | An offer was added to the cart |
| `cart_remove` | `{ offer_id, items }` | No | An offer was removed from the cart |
| `cart_open` | `{ items }` | No | Cart drawer opened |
| `cart_close` | `{ items }` | No | Cart drawer closed |
| `before_checkout` | `{ items, preventDefault() }` | Yes | Fires before checkout — call `preventDefault()` to cancel and handle yourself |

```javascript
const kit = window.CatalogKit.get();

// Track cart additions
kit.on('cart_add', (e) => {
  console.log('Added:', e.item.title, 'Total items:', e.items.length);
});

// Intercept checkout — redirect to external payment
kit.on('before_checkout', (e) => {
  e.preventDefault();
  window.location.href = 'https://my-custom-checkout.com?items=' + e.items.length;
});

// ── Display cart (visual UI) ──
kit.openCart();                          // Open the cart drawer
kit.closeCart();                         // Close the cart drawer
kit.getDisplayItems();                   // Frozen array of items in the cart UI
kit.addDisplayItem({                     // Add arbitrary item (no page-offer needed)
  offer_id: "bundle-123",
  page_id: "custom",
  title: "Premium Bundle",
  price_display: "$99",
  amount_cents: 9900,
});
kit.removeDisplayItem("bundle-123");     // Remove by offer_id

// ── Payment items (what Stripe charges) ──
kit.setPaymentItems([                    // Override Stripe line items (null = use display items)
  { offer_id: "license", page_id: "custom", title: "Team License", amount_cents: 7500 },
  { offer_id: "onboard", page_id: "custom", title: "Onboarding", amount_cents: 2400 },
]);
kit.getPaymentItems();                   // Current override or null
kit.startCheckout();                     // Show checkout page (fires before_checkout first)
```

---

## Triggering Checkout

There are three ways visitors reach the built-in checkout page. Understanding these prevents common anti-patterns.

### 1. Terminal page (recommended for funnels)

When the submit button is clicked on a **terminal page** (a page with no outgoing routing edges) and `settings.checkout` is configured, the built-in checkout page renders automatically. This is the simplest and most reliable approach.

```json
{
  "routing": {
    "entry": "landing",
    "edges": [
      { "from": "landing", "to": "offers" },
      { "from": "offers", "to": "review", "conditions": { "match": "all", "rules": [{ "field": "cart_flag", "operator": "equals", "value": "yes" }] } },
      { "from": "offers", "to": "end_screen", "is_default": true }
    ]
  }
}
```

In this example, `review` has **no outgoing edges** — it is terminal. When the visitor clicks "Start Free Trial" on the review page, the platform fires the `submit` event and then shows the checkout page with the full order summary, payment button, and everything from `settings.checkout`.

After payment, `settings.checkout.success_page_id` routes the visitor to your thank-you page (e.g. `"success_page_id": "end_screen"`).

### 2. Cart checkout button

Visitors can always click the floating cart button → open the cart drawer → click "Proceed to Checkout". This triggers the `before_checkout` event and then shows the checkout page. No configuration needed beyond `settings.checkout`.

### 3. Programmatic via `kit.startCheckout()`

For custom buttons or script-driven flows, call `kit.startCheckout()` to show the checkout page programmatically:

```json
{
  "id": "custom_checkout_btn",
  "type": "html",
  "props": {
    "content": "<script>\nconst kit = window.CatalogKit.get();\ndocument.getElementById('my_pay_btn').addEventListener('click', () => {\n  kit.startCheckout();\n});\n</script>\n<button id=\"my_pay_btn\" style=\"padding:12px 24px;background:#16a34a;color:white;border:none;border-radius:8px;font-weight:bold;cursor:pointer;\">Pay Now</button>"
  }
}
```

This fires `before_checkout` (cancelable), then shows the built-in checkout page — identical to clicking the cart checkout button, but without requiring the cart drawer to be open.

### Common anti-pattern: DOM-clicking the cart checkout button

**Do NOT** intercept `beforenext`, prevent navigation, open the cart, and `setTimeout` + `document.querySelector` to click the cart checkout button. This creates race conditions between React state updates and DOM manipulation, causing crashes in production. Use one of the three methods above instead.

```javascript
// ❌ WRONG — fragile DOM hack, causes race conditions and crashes
kit.on('beforenext:review', (e) => {
  e.preventDefault();
  kit.openCart();
  setTimeout(() => {
    document.querySelector('.ck-cart-checkout-btn').click(); // DON'T DO THIS
  }, 300);
});

// ✅ CORRECT — make review a terminal page (no outgoing edges)
// The platform auto-shows checkout when the submit button is clicked

// ✅ ALSO CORRECT — trigger checkout from a script
kit.startCheckout();
```

### Recipe: Review page before checkout

A common pattern is showing a summary/review page before payment. Here is the recommended approach:

1. **Create a review page** with your summary content (headings, callouts, guarantees)
2. **Do NOT add routing edges from the review page** — make it terminal
3. **Set the submit button label** to your CTA (e.g. `"submit_label": "Start Free Trial"`)
4. **Configure `settings.checkout`** with your payment settings, components, testimonial, and disclaimer

```typescript
// Review page — terminal (no outgoing edges)
pages["review"] = {
  title: "Start Your 7-Day Trial",
  components: [
    { id: "summary_heading", type: "heading", props: { text: "Review Your Order", level: 1, align: "center" } },
    { id: "guarantee", type: "callout", props: { style: "success", title: "90-Day Guarantee", text: "If you don't see value, we'll refund you." } },
  ],
  submit_label: "Start Free Trial",
  submit_reassurance: "Cancel anytime. 90-day money-back guarantee.",
};

// Routing — review is terminal, no edge FROM review
edges: [
  { from: "last_offer", to: "review", conditions: { match: "all", rules: [{ field: "cart_flag", operator: "equals", value: "yes" }] } },
  { from: "last_offer", to: "end_screen", is_default: true },
  // NO edge from "review" — this makes it terminal
]

// settings.checkout handles the rest
settings: {
  checkout: {
    payment_type: "subscription",
    success_page_id: "end_screen",
    title: "Complete Your Order",
    button_text: "Start Free Trial",
    // ... 3DS, trial, components, testimonial, disclaimer
  }
}
```

When the visitor clicks "Start Free Trial" on the review page → platform auto-shows the built-in checkout with order summary → Stripe payment → redirects to `end_screen`.

To redirect visitors who click the cart checkout button from other pages to the review page first, use `before_checkout`:

```javascript
const kit = window.CatalogKit.get();
kit.on('before_checkout', (e) => {
  if (kit.getPageId() !== 'review') {
    e.preventDefault();
    kit.closeCart();
    kit.goToPage('review');
  }
  // On review page, let startCheckout/terminal-page flow handle it
});
```

---

## Checkout — 3D Secure & Trial Protection

Catalog Kit supports advanced Stripe checkout features for protecting free trial funnels from payment failures and chargebacks. The system uses **two API endpoints** — one for hosted/embedded Stripe Checkout Sessions, and one for inline card-element payments via Stripe Elements.

### Checkout intent strategy (`/checkout/intent`)

When using inline card fields (the default when `stripe_publishable_key` is set), the frontend calls `POST /checkout/intent` to create a Stripe PaymentIntent or SetupIntent. The strategy is **derived from your checkout config** — no explicit mode parameter needed:

| Checkout config | Intent type | What happens |
|---|---|---|
| Default (no overrides) | PaymentIntent, auto capture | Simple one-time charge |
| `setup_future_usage: "off_session"` | PaymentIntent + saves card | Charge now, reuse card for future payments |
| `capture_method: "manual"` | PaymentIntent, auth-only hold | Hold funds on card, capture later via webhook |
| `free_trial.enabled` (no hold) | **SetupIntent** | Verify card + 3DS, $0 charge, save for later |
| `free_trial.enabled` + `capture_method: "manual"` | PaymentIntent, manual capture + off_session | **3DS + hold + save card** for post-trial billing |

The endpoint reads `stripe_overrides.payment_intent_data` for `capture_method` and `setup_future_usage`, then composes the right intent. Simple catalogs with no overrides get a simple PaymentIntent. Complex flows layer on config — not modes.

**Request:**
```
POST https://api.catalogkit.cc/checkout/intent
Authorization: Bearer <api_key>
```

```json
{
  "user_id": "usr_...",
  "catalog_slug": "my-catalog",
  "tracer_id": "trc_...",
  "line_items": [
    { "offer_id": "pro-plan", "title": "Pro Plan", "amount_cents": 4900, "currency": "usd", "payment_type": "subscription", "interval": "month", "quantity": 1 }
  ],
  "form_state": { "comp_email": "user@example.com" }
}
```

**Response:**
```json
{
  "client_secret": "pi_xxx_secret_yyy",
  "intent_type": "payment",
  "customer_id": "cus_...",
  "capture_method": "manual"
}
```

The frontend uses `intent_type` to call either `stripe.confirmCardPayment()` or `stripe.confirmCardSetup()` with the Stripe CardElement.

### Checkout session (`/checkout/session`)

The original endpoint — creates a Stripe Checkout Session for hosted redirect or embedded checkout. Still used when `ui_mode: "hosted"` or `ui_mode: "embedded"` is explicitly set.

### 3D Secure Verification

Force bank-level authentication (OTP, biometric, bank app) on every card payment. This is the single most impactful setting for reducing payment failures at trial end — it verifies the cardholder is real and authenticated at signup.

```jsonc
{
  "settings": {
    "checkout": {
      "payment_type": "subscription",
      "require_3ds": true,
      "free_trial": { "enabled": true, "days": 14 },
      "trial_end_behavior": "cancel"
    }
  }
}
```

When enabled, the intent includes `payment_method_options.card.request_three_d_secure: "any"`. The frontend shows a "3D Secure Checkout" header badge and a blue explainer banner (in the right column, below the pay button) telling the user their bank will ask for verification. Button text defaults to "Start Free Trial" when a trial is active.

With inline card fields, 3DS challenges appear as a Stripe-managed popup over your page — the user never leaves your checkout.

### Trial End Behavior

Controls what Stripe does if the customer's payment method fails when the trial ends:

| Value | Behavior |
|---|---|
| `"cancel"` (default) | Cancel the subscription — no retries |
| `"create_invoice"` | Create an invoice and retry via Stripe's smart schedule |
| `"pause"` | Pause the subscription until payment resolves |

Recommended combo: `require_3ds: true` + `trial_end_behavior: "cancel"` — verifies the card upfront, and cleanly cancels if it still fails.

### Stripe Overrides (Advanced Pass-Through)

Use `stripe_overrides` to control Stripe behavior. These apply to both the `/checkout/session` (Checkout Sessions) and `/checkout/intent` (PaymentIntent/SetupIntent) endpoints.

```jsonc
{
  "settings": {
    "checkout": {
      "stripe_overrides": {
        "payment_intent_data": {
          "capture_method": "manual",
          "setup_future_usage": "off_session",
          "statement_descriptor": "ACME TRIAL"
        }
      }
    }
  }
}
```

**Available overrides:** `mode_override` (force Checkout Session mode — only applies to `/checkout/session`), `payment_intent_data` (capture_method, setup_future_usage, statement_descriptor, transfer_data), `subscription_data` (description, metadata), `consent_collection` (terms_of_service, promotions), `payment_method_options.card` (capture_method, request_three_d_secure).

When `mode_override: "payment"` is set for subscription items in Checkout Session mode, Catalog Kit automatically strips recurring pricing from inline items. Use `amount_cents` instead of `stripe_price_id` for recurring prices in payment mode.

### Bring Your Own Billing Server

Catalog Kit handles the checkout funnel (intent creation, 3DS, overrides) but does **not** manage post-payment lifecycle. Stripe webhooks are server-to-server and independent of the `catalogkit.cc` redirect URLs — configure them in your Stripe Dashboard to point at your own server.

**Setup:**
1. Stripe Dashboard → Developers → Webhooks → add your endpoint
2. Subscribe to events based on your flow:
   - Simple payment: `payment_intent.succeeded`
   - Hold + capture: `payment_intent.amount_capturable_updated`, `payment_intent.succeeded`
   - SetupIntent (trial): `setup_intent.succeeded`
   - Checkout Session: `checkout.session.completed`
   - Failures: `payment_intent.payment_failed`, `invoice.payment_failed`
3. Your server uses the same Stripe secret key you provided to Catalog Kit
4. Every intent includes metadata (`catalog_id`, `catalog_slug`, `user_id`, `tracer_id`, `line_items_json`, `trial_days`) for correlation

### Recipe: Simple One-Time Payment

No overrides needed. Set up `settings.checkout` and `stripe_publishable_key`, and the checkout page handles everything:

```jsonc
{
  "settings": {
    "checkout": {
      "payment_type": "one_time",
      "title": "Complete Your Purchase",
      "stripe_publishable_key": "pk_test_...",
      "prefill_fields": { "customer_email": "comp_email" },
      "button_text": "Pay Now"
    }
  }
}
```

The visitor enters their card inline, clicks "Pay Now", and the payment is processed immediately. No redirect, no holds, no future usage.

### Recipe: Subscription with Free Trial (No Hold)

Verify the card and save it for post-trial billing, without placing a hold:

```jsonc
{
  "settings": {
    "checkout": {
      "payment_type": "subscription",
      "title": "Start Your 14-Day Trial",
      "stripe_publishable_key": "pk_test_...",
      "require_3ds": true,
      "free_trial": { "enabled": true, "days": 14 },
      "trial_end_behavior": "cancel",
      "prefill_fields": { "customer_email": "comp_email" },
      "button_text": "Start Free Trial"
    }
  }
}
```

This creates a **SetupIntent** — the card is verified via 3DS and saved, but $0 is charged. Your billing server listens for `setup_intent.succeeded` and creates the subscription with the saved payment method.

### Recipe: Guarded 7-Day Trial (Hold + 3DS + Future Preauth)

The most conversion-protective free trial pattern. Instead of a $0 subscription trial, this authorizes the full subscription amount as a "pending" hold on the customer's card, then captures or voids based on trial outcome.

**Step 1 — Catalog Kit config:**

```jsonc
{
  "settings": {
    "checkout": {
      "payment_type": "subscription",
      "title": "Start Your 7-Day Trial",
      "subheading": "Your card will be verified but not charged during the trial.",
      "stripe_publishable_key": "pk_test_...",
      "require_3ds": true,
      "free_trial": { "enabled": true, "days": 7 },
      "stripe_overrides": {
        "payment_intent_data": {
          "capture_method": "manual",
          "setup_future_usage": "off_session"
        }
      },
      "prefill_fields": { "customer_email": "comp_email" },
      "button_text": "Start Free Trial",
      "show_disclaimer": true,
      "disclaimer_text": "A temporary hold for the full subscription amount will appear on your card. This is NOT a charge — it verifies your funds and is released if you cancel during the trial.",
      "testimonial": {
        "enabled": true,
        "text": "Pays for itself with just one customer. Most founders see results within the first week.",
        "author": "OfficeX Founders"
      }
    }
  }
}
```

Note: Use `amount_cents` on line items (not `stripe_price_id`) since the intent needs a concrete amount for the hold.

**What happens under the hood:** The `/checkout/intent` endpoint sees `free_trial.enabled` + `capture_method: "manual"` and creates a PaymentIntent with `capture_method: "manual"` + `setup_future_usage: "off_session"`. The visitor enters their card inline, completes 3DS verification, and the hold is placed — all without leaving your page.

**Step 2 — Your billing server handles the lifecycle:**

```
Webhook: payment_intent.amount_capturable_updated
├── Retrieve PaymentIntent → read latest_charge.payment_method_details.card.capture_before
├── Schedule capture at: min(trial_end, capture_before - 1 hour)
│   ⚠️ Visa often expires at 4d 18h (114 hours), Mastercard/Amex ~7 days
└── Store { payment_intent_id, customer_id, payment_method_id, capture_deadline }

Cron/Scheduler: At capture deadline
├── If user cancelled → paymentIntents.cancel(id)  (releases hold)
├── If trial active → paymentIntents.capture(id)    (converts to real charge)
└── After capture → Create Subscription using saved payment_method for Month 2+

User cancels during trial:
└── paymentIntents.cancel(id)  — MUST do this to release hold
    (Uncaptured auths incur card network fees and freeze customer funds)
```

**Step 3 — Handle edge cases on your server:**

| Scenario | Action |
|---|---|
| Card doesn't support separate auth/capture (LATAM debit, Indian banks, some prepaid) | Stripe returns 402 on confirm. Fallback: charge $1 verification fee immediately, or switch to SetupIntent ($0 verify) |
| `capture_before` is earlier than trial end (Visa ~4.75 days) | Capture early based on `capture_before - 1 hour`, not trial end date |
| User's bank shows "pending" charge → support ticket | Address in "Trial Started" email: "You may see a pending authorization for [amount] — this is not a charge and will be released if you cancel" |
| Hold expires without capture | Stripe auto-voids. Your server must detect this and either re-authorize or cancel gracefully |

**Why this pattern exists:** Standard subscription trials ($0 with saved card) have ~15-25% payment failure rates at trial end because the card was never tested for funds. A manual-capture hold proves the funds exist today, and 3DS proves the cardholder is real. Combined failure rate drops to ~3-5%.

---

## Event Tracking (Free)

Visitor events are tracked automatically by the catalog frontend using first-party same-origin requests (ad blocker proof). You can also send custom events via the API:

```
POST https://api.catalogkit.cc/events
```

**Valid event types:** `page_view`, `field_change`, `field_complete`, `form_submit`, `funnel_complete`, `action_click`, `exit_intent`, `session_start`, `session_resume`, `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`, `payment_info_added`, `offer_declined`, `lead_captured`, `video_play`, `video_pause`, `video_progress`, `video_complete`, `video_chapter`, `video_seek`, `page_auto_skipped`, `popup_shown`, `popup_dismissed`, `popup_converted`

> `funnel_complete` is a belt-and-suspenders completion event that fires alongside `form_submit` when the submitted state is confirmed. Both count toward submissions in analytics.

Batch up to 25 events: `POST /events/batch` with `{ "events": [...] }`

**Note:** The catalog frontend uses same-origin paths (`/e`, `/e/batch`) proxied through CloudFront for reliability. The cross-origin API endpoints above are for server-side or external integrations.
