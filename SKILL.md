---
name: catalog-kit
description: |
  Build and manage marketing catalogs, landing pages, and multi-step funnels with your AI agent. Create catalogs from JSON schemas, publish them instantly, run A/B tests with weighted variants, and track visitor analytics — all through conversation.
  Use when: (1) Creating or updating a catalog/funnel/landing page, (2) Checking analytics like visitors, conversions, and drop-off rates, (3) Running A/B tests on different catalog versions, (4) AI-routing visitors to the right catalog variant with natural language hints, (5) Managing API keys for team access, (6) Uploading videos for catalogs, (7) Viewing individual visitor journeys, (8) Reviewing response distributions for form fields, (9) Creating sandboxes to safely edit catalogs without affecting production, (10) Using the element inspector to get exact component references for AI agents, (11) Adding scripting hooks for dynamic behavior like API calls, conditional routing, and cross-page state, (12) Uploading and compressing images for fast loading, (13) Authoring catalogs as TypeScript files with full type safety and real function hooks.
  Triggers: catalog funnel, catalog kit, funnel builder, landing page, lead capture, create catalog, catalog analytics, conversion funnel, form builder, ab test, catalog api, ai routing, variant routing, hint routing, sandbox, element inspector, devtools, hooks, scripting, on_change, on_enter, on_submit, image upload, image compression, webp, typescript, ts config, on_init, on_tick, globals, timers, global state
---

# Catalog Kit

Build and manage marketing catalogs, landing pages, and multi-step funnels — directly through your AI agent. Create catalogs with 57+ component types, publish them instantly, run A/B tests with weighted variants, and monitor conversion analytics in real time.

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
- **Upload videos** — add video content with automatic HLS transcoding
- **Upload images** — upload images with automatic WebP compression and thumbnail generation (free)
- **Scripting hooks** — add imperative logic (API calls, dynamic routing, cross-page state) at page and component lifecycle points
- **TypeScript-as-config** — author catalogs as .ts files with full type safety and real function hooks, then push via CLI

## Getting Started

After installing Catalog Kit on OfficeX, you receive credentials automatically. You can also sign up at the dashboard and create API keys from Settings.

```bash
# Your API key (created from Settings page or received on install)
CF_API_KEY="cfk_..."

# Production API
CF_API_URL="https://api.catalogkit.cc"
```

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

## Images

Upload images with automatic compression to WebP for fast loading. Compression is free and happens automatically via a background Lambda.

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
- **Originals**: Auto-deleted after 30 days (compressed versions persist)

---

## Videos

Upload video content to use in your catalogs with automatic HLS transcoding:

- `POST /api/v1/videos/upload` — Upload a video file
- `GET /api/v1/videos/:videoId/status` — Check transcoding progress
- `GET /api/v1/videos/:videoId/hls_url` — Get the playback URL

---

## Webhooks

If your catalog has a `webhook_url` configured in its schema, all visitor events are forwarded there in real time. Each webhook payload includes an `event_id` (ULID) for deduplication and `schema_ref` with human-readable page/component context.

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

### Component Types (57 total)

**Input (27):** `short_text`, `long_text`, `rich_text`, `email`, `phone`, `url`, `password`, `number`, `currency`, `date`, `datetime`, `time`, `date_range`, `dropdown`, `multiselect`, `multiple_choice`, `checkboxes`, `picture_choice`, `star_rating`, `slider`, `file_upload`, `signature`, `address`, `location`, `switch`, `checkbox`, `choice_matrix`, `ranking`, `opinion_scale`

**Display (12):** `heading`, `paragraph`, `banner`, `image`, `video`, `pdf_viewer`, `social_links`, `html`, `divider`, `faq`, `testimonial`, `pricing_card`, `timeline`

**Layout (3):** `section_collapse`, `table`, `subform`

**Page features:** `payment`, `captcha`

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

### Embedded Buttons

Add inline buttons to `multiple_choice`, `checkboxes`, and `timeline` components. Buttons render alongside each option or timeline item — useful for "check the box after opening this link" patterns.

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
        "checkbox": { "label": "Joined" }
      }
    ]
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
- `"hidden"` — the component is completely hidden when prefilled (useful for passing data silently)

```json
{
  "id": "referral_code",
  "type": "short_text",
  "props": { "label": "Your Referral Code" },
  "prefill_mode": "readonly"
}
```

To prefill values, pass them as URL parameters matching the component ID: `?referral_code=ABC123`. The readonly input renders with a clipboard icon — clicking it copies the value and shows a brief checkmark confirmation.

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
  "quiz": { "correct_answer": "Call To Action", "points": 10, "explanation": "CTA = Call To Action" }
}
```

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
   - A feedback banner shows "Correct!" or "You got the wrong answer."
   - The explanation text is displayed (if provided)
   - Options become **locked**
   - A banner says "Answers revealed! Review your results above, then click Continue to proceed."
   - The page **auto-scrolls** to keep the Continue button visible
3. The visitor clicks **Continue again** to proceed to the next page

Works with both `multiple_choice` (single-select) and `checkboxes` (multi-select) components. Omit `reveal_on_select` or set to `false` for the default behavior (no inline feedback — use `reveal_answers` on a later page instead).

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

Each item supports: `title` (required), `description` (optional, markdown), `icon` (emoji in colored circle), `image` (URL for a round image), `color` (per-item color, falls back to theme), `button` (embedded button, see [Embedded Buttons](#embedded-buttons)), `checkbox` (`true` or `{ "label": "Custom" }` for an interactive checkbox).

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

### Nested Inputs in Checkboxes

Checkbox options support an `inputs` array. When a checkbox option is selected, nested inputs slide in below it in an indented left-bordered panel. Values are stored with compound IDs: `checkboxComponentId.optionValue.inputId`.

```json
{
  "id": "interests",
  "type": "checkboxes",
  "props": {
    "label": "What are you interested in?",
    "options": [
      {
        "value": "coaching",
        "label": "1-on-1 Coaching",
        "inputs": [
          { "id": "coach_pref", "type": "short_text", "label": "Preferred coach name", "placeholder": "Optional" }
        ]
      },
      {
        "value": "group",
        "label": "Group Sessions",
        "inputs": [
          { "id": "group_size", "type": "dropdown", "label": "Preferred group size", "props": { "options": ["Small (3-5)", "Medium (6-10)", "Large (10+)"] } }
        ]
      },
      { "value": "self_paced", "label": "Self-Paced Learning" }
    ]
  }
}
```

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

### Scripting / Hooks

Imperative escape hatches within the declarative config. **Hooks must be authored as TypeScript functions** and pushed via the CLI (`npx catalogs catalog push catalog.ts`). The CLI serializes real functions into the correct format automatically — do not write hook strings in JSON by hand.

Attach hooks to pages (`hooks.on_enter`, `hooks.on_before_next`, `hooks.on_exit`, `hooks.on_submit`) or components (`hooks.on_change`). Global hooks on the schema: `global_hooks.on_page_enter`, `global_hooks.on_page_exit`, `global_hooks.on_field_change`.

Each hook receives a `ScriptContext` (`ctx`) with:
- Read-only: `formState`, `vars`, `hints`, `url_params`, `page_id`, `quiz_scores`, `field_id`/`field_value`/`prev_value` (on_change only)
- Mutation methods: `setField(id, value)`, `setVar(key, value)`, `setComponentProp(id, prop, value)`, `setNextPage(pageId)`
- `fetch` for async API calls
- Timers: `setTimeout(fn, ms)`, `setInterval(fn, ms)`, `clearTimeout(id)`, `clearInterval(id)` — auto-cleaned on page transition
- Popup control: `showPopup(popupId)`, `dismissPopup(popupId)`
- Global state: `globals`, `setGlobal(key, value)` — persists across pages for entire catalog session
- Cross-page reads: `getField(componentId)`, `getAllFields()`, `getParam(key)`, `getAllParams()`

`on_before_next` and `on_submit` can return `{ prevent: true }` to block navigation or `{ next_page: "page_id" }` to override routing. Scripts have a 5-second timeout and never crash the renderer.

**Catalog-level hooks** (`global_hooks`): `on_page_enter`, `on_page_exit`, `on_field_change`, `on_init` (runs once on load), `on_tick` (runs on interval).

```typescript
// In your catalog.ts file — hooks are real functions, type-checked and auto-serialized by the CLI
const catalog = {
  pages: {
    landing: {
      title: "Get Started",
      hooks: {
        on_enter: (ctx) => {
          ctx.setVar("entered_at", Date.now());
        },
        on_before_next: (ctx) => {
          if (!ctx.formState.email) return { prevent: true };
        },
      },
      components: [/* ... */],
    },
    results: {
      title: "Your Results",
      hooks: {
        on_enter: (ctx) => {
          const s = ctx.quiz_scores;
          const correct = s?.total || 0;
          const total = s?.max || 0;
          ctx.setComponentProp("score-display", "text", `You scored ${correct} / ${total}`);
        },
      },
      components: [/* ... */],
    },
  },
} satisfies CatalogSchema;
```

**Important:** The API validates hook syntax at write time. Malformed hooks are rejected with a clear error — they will never silently fail for visitors.

---

## CLI

Manage catalogs from the command line:

```bash
npx catalogs catalog push schema.json --publish    # Push a JSON catalog
npx catalogs catalog push catalog.ts --publish     # Push a TypeScript catalog (functions auto-serialized)
npx catalogs catalog list                           # List all your catalogs
npx catalogs video upload ./intro.mp4               # Upload a video
npx catalogs video status VIDEO_ID                  # Check transcoding progress
```

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

Visitor events are tracked automatically by the catalog frontend. You can also send custom events:

```
POST https://api.catalogkit.cc/events
```

**Valid event types:** `page_view`, `field_change`, `field_complete`, `form_submit`, `action_click`, `exit_intent`, `session_start`, `session_resume`, `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`, `payment_info_added`, `offer_declined`, `lead_captured`, `video_play`, `video_pause`, `video_progress`, `video_complete`, `video_chapter`, `video_seek`, `page_auto_skipped`, `popup_shown`, `popup_dismissed`, `popup_converted`

Batch up to 25 events: `POST /events/batch` with `{ "events": [...] }`
