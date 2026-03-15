---
name: catalog-funnel
description: |
  Build and manage marketing catalogs, landing pages, and multi-step funnels with your AI agent. Create catalogs from JSON schemas, publish them instantly, run A/B tests with weighted variants, and track visitor analytics — all through conversation.
  Use when: (1) Creating or updating a catalog/funnel/landing page, (2) Checking analytics like visitors, conversions, and drop-off rates, (3) Running A/B tests on different catalog versions, (4) AI-routing visitors to the right catalog variant with natural language hints, (5) Managing API keys for team access, (6) Uploading videos for catalogs, (7) Viewing individual visitor journeys, (8) Reviewing response distributions for form fields, (9) Creating sandboxes to safely edit catalogs without affecting production, (10) Using the element inspector to get exact component references for AI agents.
  Triggers: catalog funnel, catalog kit, funnel builder, landing page, lead capture, create catalog, catalog analytics, conversion funnel, form builder, ab test, catalog api, ai routing, variant routing, hint routing, sandbox, element inspector, devtools
---

# Catalog Funnel

Build and manage marketing catalogs, landing pages, and multi-step funnels — directly through your AI agent. Create catalogs with 56+ component types, publish them instantly, run A/B tests with weighted variants, and monitor conversion analytics in real time.

> **Install on OfficeX:** [officex.app/store/en/app/catalog-funnel](https://officex.app/store/en/app/catalog-funnel)

## What You Can Do

- **Create catalogs** — build lead capture forms, product catalogs, multi-step funnels from a JSON schema
- **Publish instantly** — catalogs go live at your subdomain (SUBDOMAIN.catalogkit.cc) or custom domain
- **Check analytics** — see visitors, conversions, page drop-off, field completions, referrer sources, and revenue
- **Run A/B tests** — use weighted variants to split traffic to find what converts best
- **AI variant routing** — auto-route visitors to the best catalog variant using natural language hints
- **Sandbox editing** — clone a catalog to safely make changes without affecting the live version, then promote when ready
- **Element inspector** — hold Shift+Alt to hover-inspect any element and copy its exact `pageId/componentId` reference for AI agents
- **View visitor journeys** — trace exactly what each visitor did step by step
- **Manage access** — create API keys for team members or integrations
- **Upload videos** — add video content with automatic HLS transcoding

## Getting Started

After installing Catalog Funnel on OfficeX, you receive credentials automatically. You can also sign up at the dashboard and create API keys from Settings.

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

### Component Types (56 total)

**Input (27):** `short_text`, `long_text`, `rich_text`, `email`, `phone`, `url`, `password`, `number`, `currency`, `date`, `datetime`, `time`, `date_range`, `dropdown`, `multiselect`, `multiple_choice`, `checkboxes`, `picture_choice`, `star_rating`, `slider`, `file_upload`, `signature`, `address`, `location`, `switch`, `checkbox`, `choice_matrix`, `ranking`, `opinion_scale`

**Display (11):** `heading`, `paragraph`, `banner`, `image`, `video`, `pdf_viewer`, `social_links`, `html`, `divider`, `faq`, `testimonial`, `pricing_card`

**Layout (3):** `section_collapse`, `table`, `subform`

**Page features:** `payment`, `captcha`

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

Score-based routing: `{ "score": "percent", "operator": "greater_than", "value": 80 }`

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

---

## CLI

Manage catalogs from the command line:

```bash
npx catalogs catalog push schema.json --publish    # Create or update a catalog from a JSON file
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

> **Note:** Use quotes around the hint value for readability — browsers automatically encode `"` to `%22` and spaces to `+`/`%20`. Both `hint`/`hints` and `user_id`/`domain` are accepted.

### Route a visitor with a hint (POST — JSON body)

If URL encoding is a concern, use the POST alternative with a JSON body:

```bash
curl -X POST https://api.catalogkit.cc/public/route-variant \
  -H "Content-Type: application/json" \
  -d '{
    "user_id": "USER_ID",
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

# After AI routing resolves, browser URL updates to:
https://SUBDOMAIN.catalogkit.cc/my-catalog/problem-aware-male?ref=253
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

Built-in developer tool for AI agent workflows. Hold **Shift+Alt** and hover over any element in a live catalog to see its exact reference path (`pageId/componentId`) with a one-click copy button.

This makes it trivial to tell an AI agent exactly which element to modify — no guessing, no digging through JSON.

The reference format matches the schema introspection endpoint (`GET /api/v1/catalogs/:id/schema/ids`), so copied references map 1:1 to API paths.

**How to use:**

1. Open any catalog in the browser
2. Hold **Shift+Alt** — a "Inspector active" indicator appears
3. Hover over any element — it highlights with an indigo border and shows a tooltip with `pageId/componentId (type)`
4. Click **Copy** to copy the reference to clipboard
5. Paste the reference into your AI agent conversation (e.g. "change the heading at `landing/hero-title`")

---

## Event Tracking (Free)

Visitor events are tracked automatically by the catalog frontend. You can also send custom events:

```
POST https://api.catalogkit.cc/events
```

**Valid event types:** `page_view`, `field_change`, `field_complete`, `form_submit`, `action_click`, `exit_intent`, `session_start`, `session_resume`, `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`, `payment_info_added`, `offer_declined`, `lead_captured`, `video_play`, `video_pause`, `video_progress`, `video_complete`, `video_chapter`, `video_seek`

Batch up to 25 events: `POST /events/batch` with `{ "events": [...] }`
