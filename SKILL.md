---
name: catalog-funnel
description: |
  REST API client for the Catalog Funnel app on OfficeX. Declarative funnel builder for catalogs, quizzes, and multi-step forms with intent-based personalization, video tracking, and Stripe checkout.
  Use when: (1) Creating or managing funnel/catalog/quiz schemas via API, (2) Pushing catalog JSON configs, (3) Querying funnel analytics and visitor journeys, (4) Managing API keys, (5) Uploading and transcoding videos for funnels, (6) Creating Stripe checkout sessions, (7) Setting up A/B split tests for traffic routing.
  Triggers: catalog funnel, quiz funnel, funnel builder, catalog schema, funnel analytics, form builder, quiz builder, catalog api, conversion funnel, landing page builder, split test, ab test, traffic split
---

# Catalog Funnel -- API Skill

Declarative funnel builder SaaS for catalogs, quizzes, and multi-step forms. Merchants define funnels as JSON/TypeScript schemas with 56+ component types, intent-based personalization, conditional routing, popup triggers, video tracking, cart/checkout, and analytics. AI agents use this API to CRUD funnels and query conversion data.

> **Install on OfficeX:** [officex.app/store/en/app/catalog-funnel](https://officex.app/store/en/app/catalog-funnel)

## Prerequisites

After installing the app on OfficeX, you receive credentials via the install webhook. Or self-signup at the frontend and create API keys from Settings.

```bash
# Option A: OfficeX install credentials
OFFICEX_INSTALL_ID="your_install_id"
OFFICEX_INSTALL_SECRET="your_install_secret"

# Option B: API key (created from Settings page)
CF_API_KEY="cfk_..."

# API base URL
CF_API_URL="https://catalog-funnel-api.cloud.zoomgtm.com"
```

## Authentication

**OfficeX credentials:** Bearer token = Base64(install_id:install_secret)

```bash
TOKEN=$(echo -n "${OFFICEX_INSTALL_ID}:${OFFICEX_INSTALL_SECRET}" | base64)
curl -H "Authorization: Bearer $TOKEN" $CF_API_URL/api/v1/catalogs
```

**API key:** Pass the `cfk_...` token directly as Bearer.

```bash
curl -H "Authorization: Bearer cfk_..." $CF_API_URL/api/v1/catalogs
```

## API Reference

### Base URLs

| Stage | API URL |
|---|---|
| Production | `https://catalog-funnel-api.cloud.zoomgtm.com` |
| Staging | `https://catalog-funnel-api-staging.cloud.zoomgtm.com` |

Frontend: `https://{subdomain}.catalogs.cloud.zoomgtm.com/{catalog-slug}`

### GET /health

Health check (unauthenticated).

```json
{ "ok": true, "stage": "production" }
```

---

### Catalogs (CRUD)

All catalog endpoints require authentication.

#### GET /api/v1/catalogs

List all catalogs for the authenticated user.

#### POST /api/v1/catalogs

Create a new catalog. Required fields: `slug`, `name`, `schema`.

**Request Body:**
```json
{
  "slug": "my-funnel",
  "name": "My Funnel",
  "schema": { ... },
  "status": "published",
  "visibility": "public"
}
```

#### GET /api/v1/catalogs/:id

Get a single catalog with full schema.

#### PUT /api/v1/catalogs/:id

Update a catalog. Supports partial updates. Slug changes can create redirects via `old_slug_action`: `"redirect"` (default) or `"release"`.

#### DELETE /api/v1/catalogs/:id

Delete a catalog.

---

### Public Catalog Fetch (No Auth)

### Split Tests (A/B Routing)

#### POST /api/v1/split-tests

Create a split test — maps a slug to multiple destination catalogs with weighted traffic distribution.

```json
{
  "slug": "marketing-quiz",
  "name": "Quiz A/B Test",
  "destinations": [
    { "slug": "quiz-v1", "weight": 50, "label": "Control" },
    { "slug": "quiz-v2", "weight": 50, "label": "Variant A" }
  ]
}
```

- GET /api/v1/split-tests -- List all
- GET /api/v1/split-tests/:slug -- Get one
- PUT /api/v1/split-tests/:slug -- Update (name, destinations, status: active|paused)
- DELETE /api/v1/split-tests/:slug -- Delete

---

#### GET /public/catalogs/:subdomain/:slug

Fetch a published catalog schema. Resolution order: direct catalog → active split test → slug redirect → 404. Split test responses include `split_test` metadata with `chosen_slug` and `chosen_label`.

---

### Analytics

All analytics endpoints require authentication with `analytics:read` permission. Each call costs **1 credit**. Event ingestion is FREE.

Events are stored in a dedicated analytics DynamoDB table with 180-day TTL. Daily and hourly rollup counters are atomically incremented for fast timeseries queries.

#### GET /api/v1/analytics/catalogs/:id

Aggregate funnel metrics: unique visitors, conversions, page drop-off, field completions, variant breakdown, referrer sources, checkout stats, revenue.

**Query params:** `start`, `end` (ISO dates)

#### GET /api/v1/analytics/catalogs/:id/events

Raw events with filters and cursor pagination.

**Query params:** `start`, `end`, `cursor`, `limit` (default 100, max 500), `event_type`, `page_id`, `component_id`, `variant_slug`, `utm_source`, `utm_medium`, `utm_campaign`, `referrer`

**Response includes** `cursor` for next page (null when done).

#### GET /api/v1/analytics/catalogs/:id/timeseries

Rollup data as a timeseries array — fast reads from pre-computed counters.

**Query params (required):** `start`, `end` (ISO dates), `interval` (`day` or `hour`)

**Response:**
```json
{
  "ok": true,
  "data": [
    { "date": "2024-01-01", "page_views": 150, "sessions": 80, "form_submits": 25, "checkout_completes": 5, "revenue_cents": 4900, ... }
  ]
}
```

#### GET /api/v1/analytics/catalogs/:id/dropoff

Page and field drop-off analysis.

**Query params:** `start`, `end` (ISO dates)

**Response:**
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

#### GET /api/v1/analytics/catalogs/:id/responses

Answer breakdowns per component — shows distribution of responses.

**Query params:** `start`, `end`, `page_id`, `component_id` (all optional)

**Response:**
```json
{
  "ok": true,
  "data": {
    "components": {
      "questions/q1": {
        "total_responses": 200,
        "distribution": {
          "Call To Action": { "count": 112, "percent": 56 },
          "Click To Act": { "count": 28, "percent": 14 },
          "Create The Ad": { "count": 60, "percent": 30 }
        }
      }
    }
  }
}
```

#### GET /api/v1/analytics/tracers/:tracerId

Full visitor journey — every event for a single visitor in chronological order.

**Response includes** summary: total events, first/last seen, pages viewed, submitted status.

---

### Schema Introspection

#### GET /api/v1/catalogs/:id/schema/ids

Flat JSON map of all page and component IDs with labels and types. Useful for building analytics queries programmatically.

**Response:**
```json
{
  "pages": {
    "questions": { "title": "Marketing Knowledge", "index": 0 },
    "results": { "title": "Your Results", "index": 1 }
  },
  "components": {
    "questions/q1": { "type": "multiple_choice", "label": "What does CTA stand for?", "quiz": true },
    "questions/email": { "type": "email", "label": "Your Email", "required": true }
  },
  "routing_entry": "questions"
}
```

---

### Event Tracking (No Auth — FREE)

#### POST /events

Track a single event. Valid types: `page_view`, `field_change`, `field_complete`, `form_submit`, `action_click`, `exit_intent`, `session_start`, `session_resume`, `cart_add`, `cart_remove`, `checkout_start`, `checkout_skip`, `checkout_complete`, `payment_info_added`, `offer_declined`, `lead_captured`, `video_play`, `video_pause`, `video_progress`, `video_complete`, `video_chapter`, `video_seek`

#### POST /events/batch

Track up to 25 events at once.

### Variant Analytics

Every catalog gets an automatic `catalog:{catalog_id}` tag. To group variant catalogs for cross-variant analytics, add the base catalog's `catalog:{base_id}` tag to each variant's `schema.tags`. API keys scoped with matching `tag_patterns` can query analytics across all tagged variants.

### Webhooks

All event types are forwarded to the catalog's `webhook_url` (if configured). Payload includes `event_id` (ULID) for deduplication and `schema_ref` with human-readable page/component context.

---

### API Keys

Require `api_keys:manage` permission.

- POST /api/v1/api-keys -- Create (roles: reader, editor, admin, custom)
- GET /api/v1/api-keys -- List (secrets redacted)
- DELETE /api/v1/api-keys/:keyId -- Revoke
- POST /api/v1/api-keys/:keyId/rotate -- Rotate

---

### Videos

- POST /api/v1/videos/upload -- Upload for HLS transcoding
- GET /api/v1/videos/:videoId/status -- Transcode status
- GET /api/v1/videos/:videoId/hls_url -- HLS playback URL

---

## CatalogSchema Reference

Minimal example:

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

### Quiz Scoring

```json
{
  "id": "q1", "type": "multiple_choice",
  "label": "What is 2+2?",
  "options": ["3", "4", "5"],
  "quiz": { "correct_answer": "4", "points": 10, "explanation": "Basic math" }
}
```

Score-based routing: `{ "score": "percent", "operator": "greater_than", "value": 80 }`

### Routing & Conditions

Operators: `equals`, `not_equals`, `contains`, `not_contains`, `greater_than`, `less_than`, `greater_than_or_equal`, `less_than_or_equal`, `starts_with`, `ends_with`, `regex`, `in`, `not_in`, `is_empty`, `is_not_empty`, `between`

### Popups

Trigger types: `exit_intent`, `scroll_depth`, `inactive`, `timed`, `page_count`, `custom`, `video_progress`, `video_chapter`

### CLI

```bash
npx catalogs catalog push schema.json --publish
npx catalogs catalog list
npx catalogs video upload ./intro.mp4
npx catalogs video status VIDEO_ID
```
