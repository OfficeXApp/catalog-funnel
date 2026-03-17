---
name: catalog-kit
description: |
  Build and manage marketing catalogs, landing pages, and multi-step funnels with your AI agent. Create catalogs from JSON schemas, publish them instantly, run A/B tests with weighted variants, and track visitor analytics — all through conversation.
  Use when: (1) Creating or updating a catalog/funnel/landing page, (2) Checking analytics like visitors, conversions, and drop-off rates, (3) Running A/B tests on different catalog versions, (4) AI-routing visitors to the right catalog variant with natural language hints, (5) Managing API keys for team access, (6) Uploading videos for catalogs, (7) Viewing individual visitor journeys, (8) Reviewing response distributions for form fields, (9) Creating sandboxes to safely edit catalogs without affecting production, (10) Using the element inspector to get exact component references for AI agents, (11) Submitting form data headlessly via the Agent API for AI agent integrations, (12) Uploading and compressing images for fast loading, (13) Authoring catalogs as TypeScript files with full type safety, (14) Uploading and hosting downloadable files (PDFs, ZIPs, docs) with credit-based billing, (15) Building custom interactive UI with the CatalogKit global API bridge (window.CatalogKit) for inline scripts, real-time field access, and multi-form isolation, (16) AI agents can fill out catalog forms step-by-step via the stateful Agent Session API.
  Triggers: catalog funnel, catalog kit, funnel builder, landing page, lead capture, create catalog, catalog analytics, conversion funnel, form builder, ab test, catalog api, ai routing, variant routing, hint routing, sandbox, element inspector, devtools, image upload, image compression, webp, typescript, ts config, file upload, file download, downloadable, hosted files, CatalogKit, window.CatalogKit, global api, inline script, html script, custom ui, api bridge, multi-form, agent api, headless form, agent session, form submission api
---

# Catalog Kit

Build and manage marketing catalogs, landing pages, and multi-step funnels — directly through your AI agent. Create catalogs with 60+ component types, publish them instantly, run A/B tests with weighted variants, and monitor conversion analytics in real time.

> **Install on OfficeX:** [officex.app/store/en/app/catalog-kit](https://officex.app/store/en/app/catalog-kit)

## What You Can Do

- **Create catalogs** — build lead capture forms, product catalogs, multi-step funnels from a JSON schema
- **Publish instantly** — catalogs go live at your subdomain (SUBDOMAIN.catalogkit.cc) or custom domain
- **Check analytics** — see visitors, conversions, page drop-off, field completions, referrer sources, and revenue
- **Run A/B tests** — use weighted variants to split traffic to find what converts best
- **AI variant routing** — auto-route visitors to the best catalog variant using natural language hints
- **Sandbox editing** — clone a catalog to safely make changes without affecting the live version, then promote when ready
- **Element inspector** — hold Shift+Alt to hover-inspect any element (including the top navbar) and copy its exact `pageId/componentId` reference for AI agents
- **View visitor journeys** — trace exactly what each visitor did step by step
- **Manage access** — create API keys for team members or integrations
- **Managed media hosting** — images and videos are stored, compressed, and served via CDN for you — no need to bring your own S3 bucket
- **Upload images (free)** — automatic WebP compression, thumbnail generation, and CDN delivery at no credit cost
- **Upload videos** — automatic HLS transcoding for adaptive streaming, served via CDN
- **Upload & download files** — host downloadable files (PDFs, ZIPs, docs) on S3 with CDN delivery, credit-billed per 50MB
- **Agent API** — AI agents can fill out catalog forms headlessly via the stateful session API, with server-side validation and progressive disclosure
- **TypeScript-as-config** — author catalogs as .ts files with full type safety, then push via CLI

## Getting Started

After installing Catalog Kit on OfficeX, you receive credentials automatically. You can also sign up at the dashboard and create API keys from Settings.

```bash
# Your API key (created from Settings page or received on install)
export CATALOG_KIT_TOKEN="cfk_..."
```

The production API URL is `https://api.catalogkit.cc` (used by default — no env var needed).

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

## Managing Catalogs

### List your catalogs

```
GET https://api.catalogkit.cc/api/v1/catalogs
```

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
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ]
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
  "visibility": "public"
}
```

- `slug` — URL-friendly name (lowercase, hyphens). Your catalog will be live at your configured domain
- `status` — `"published"` (live) or `"draft"` (hidden). Default: `"published"`
- `visibility` — `"public"` (listed) or `"unlisted"` (link-only). Default: `"unlisted"`

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
  "old_slug_action": "redirect"
}
```

When changing the slug, `old_slug_action` controls what happens to the old URL:
- `"redirect"` (default) — old URL redirects to the new one
- `"release"` — old URL becomes available for reuse

### Delete a catalog

```
DELETE https://api.catalogkit.cc/api/v1/catalogs/:id
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
      { "id": "v2", "slug": "new-headline", "weight": 50, "description": "New headline" }
    ]
  }
}
```

Variants with `target_slug` route visitors to a different catalog entirely. Variants without `target_slug` apply personalization hints within the same catalog.

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
- `GET /api/v1/api-keys` — List all keys (secrets redacted)
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

Upload the file using the presigned `upload_url` (PUT request with the image body). Compression happens automatically — use `compressed_url` as the `src` in your image components.

### Check compression status

```
GET https://api.catalogkit.cc/api/v1/images/:imageId/status
```

### List images

```
GET https://api.catalogkit.cc/api/v1/images
```

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

Every catalog gets an automatic `catalog:{catalog_id}` tag. To compare analytics across catalog variants (e.g. for A/B tests), add the base catalog's `catalog:{base_id}` tag to each variant's `schema.tags`. API keys scoped with matching `tag_patterns` can then query analytics across all tagged variants.

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
| `copyable` | `boolean` | Show a copy-to-clipboard icon next to the input. Works on editable inputs (short_text, long_text, email, phone, url, number, currency, date, datetime, time, password, dropdown, address). The icon appears once the field has a value. |

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

The copy icon appears next to the input once it has a value. Clicking it copies the current value to clipboard with a checkmark confirmation. Unlike `readonly`, the field remains fully editable. Supported on: `short_text`, `long_text`, `email`, `phone`, `url`, `number`, `currency`, `date`, `datetime`, `time`, `password`, `dropdown`, `address`.

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

Values are stored with compound IDs: `checkboxComponentId.optionValue.inputId`.

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

Available methods: `getField`, `setField`, `getAllFields`, `setButtonLoading`, `setButtonDisabled`, `setValidationError`, `setComponentProp`, `goNext`, `goBack`.

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
| `kit.getField(id)` | Get current value of any form field |
| `kit.getAllFields()` | Frozen copy of all form values |
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
| **Component props** | |
| `kit.setComponentProp(id, prop, value)` | Override any component prop at runtime (e.g. `hidden`, `label`, `options`). Works on ALL component types — display and input alike |
| **Events** | |
| `kit.on(event, callback)` | Subscribe to lifecycle events (see Events section below) |
| `kit.off(event, callback)` | Unsubscribe |
| **Utilities** | |
| `kit.fetch` | Alias for `globalThis.fetch` |

### Events — scoped lifecycle hooks

Events follow the pattern `event` or `event:scope_id`. Unscoped listeners fire for all pages/fields. Scoped listeners fire only for the specified page or field.

| Event | Scope | Payload | Async? | Description |
|-------|-------|---------|--------|-------------|
| `fieldchange` | field ID | `{ fieldId, value, prevValue }` | No | A form field value changed |
| `pageenter` | page ID | `{ pageId }` | No | Page became active (after transition) |
| `pageexit` | page ID | `{ pageId }` | Yes | About to leave page (after beforenext) |
| `beforenext` | page ID | `{ pageId, preventDefault(), setNextPage(id) }` | Yes | After validation, before navigation — can block or redirect |
| `submit` | page ID | `{ pageId, formState, preventDefault() }` | Yes | Final page submission — can block |

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

1. **Template interpolation:** `{{field_id}}`, `{{var:key}}`, and `{{global:key}}` in HTML content are replaced with the current field value, script variable, or global value respectively. Fields and vars are reactive on re-render. Globals persist across pages but don't trigger re-renders on their own (pair with a `setField` or `setVar` call if you need reactivity).
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
| `setValidationError` doesn't display anything | Wrong `componentId` passed — must match the `id` of an input component on the current page | Check the component `id` in your schema matches the first argument |
| Script doesn't execute | `html` component not on the current page, or `content` prop missing `<script>` tags | Ensure the `html` component is in the page's `components` array and the content is wrapped in `<script>...</script>` |

### Debug mode

Append `?debug_mode=verbose` or `?debug_mode=slim` to any catalog URL for console logging.

| Mode | What it logs |
|---|---|
| `slim` | CatalogKit registration, script execution, pageenter events, quiz score updates |
| `verbose` | All of slim + full formState at page transitions, quiz answer details, listener counts, fieldchange events |

Example: `https://yoursubdomain.catalogkit.cc/your-catalog?debug_mode=verbose`

---

## CLI (`@officexapp/catalogs-cli`)

Install the CLI from npm:

```bash
npm install -g @officexapp/catalogs-cli
```

Manage catalogs from the command line:

```bash
catalogs catalog push schema.json --publish    # Push a JSON catalog
catalogs catalog push catalog.ts --publish     # Push a TypeScript catalog (functions auto-serialized)
catalogs catalog list                           # List all your catalogs
catalogs video upload ./intro.mp4               # Upload a video
catalogs video status VIDEO_ID                  # Check transcoding progress
catalogs whoami                                 # Test API connection
```

Or run without installing via `npx @officexapp/catalogs-cli <command>`.

---

## AI Variant Routing

Automatically route visitors to the best catalog variant using natural language hints. Instead of requiring exact variant slugs, pass a description and let the AI pick the right variant.

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
    "reason": "ai_matched"
  }
}
```

`reason` values: `ai_matched` (LLM picked best match), `weighted_random` (randomly selected by weight), `hybrid_ai` (hybrid mode, LLM picked), `hybrid_random_fallback` (hybrid mode, LLM failed, random pick), `single_variant` (only one variant exists), `no_variants` (catalog has no variants), `fallback` (LLM couldn't decide, returned first variant). `target_slug` is included when the variant routes to a different catalog.

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

The frontend holds rendering for up to 400ms while AI routing resolves. If routing completes within that window (typical), visitors see the correct variant catalog directly with no flash. If routing is slow, the base catalog renders first and the variant swaps in when ready.

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

By default, `GET /api/v1/catalogs` hides sandboxes. Add `?include_sandboxes=true` to include them. Each catalog response includes `sandbox_of` (null for regular catalogs, parent catalog ID for sandboxes).

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

**Detail panel:** After clicking to copy, a dismissible panel appears in the bottom-right showing the full JSON that was copied. This persists after releasing Shift+Alt so you can review what was captured.

**AI agent workflow example:**

1. User holds Shift+Alt, hovers over a heading, clicks to copy
2. User pastes into Claude: "change this element: `{...copied JSON...}` to say 'Welcome Back'"
3. AI agent reads the `catalog_id`, `page_id`, `component_id`, and `api_endpoint` from the JSON
4. AI agent fetches the catalog via `GET /api/v1/catalogs/{catalog_id}`, finds the component at `schema.pages.{page_id}.components` where `id == component_id`, updates the text, and PUTs back

---

## Event Tracking (Free)

Visitor events are tracked automatically by the catalog frontend using first-party same-origin requests (ad blocker proof). You can also send custom events via the API:

```
POST https://api.catalogkit.cc/events
```

**Valid event types:** `page_view`, `field_change`, `field_complete`, `form_submit`, `action_click`, `exit_intent`, `session_start`, `session_resume`, `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`, `payment_info_added`, `offer_declined`, `lead_captured`, `video_play`, `video_pause`, `video_progress`, `video_complete`, `video_chapter`, `video_seek`, `page_auto_skipped`, `popup_shown`, `popup_dismissed`, `popup_converted`

Batch up to 25 events: `POST /events/batch` with `{ "events": [...] }`

**Note:** The catalog frontend uses same-origin paths (`/e`, `/e/batch`) proxied through CloudFront for reliability. The cross-origin API endpoints above are for server-side or external integrations.
