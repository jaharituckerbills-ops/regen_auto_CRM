# 02 — Catalog & Coverage

> **Owner:** Agent 02 (Catalog & Coverage) · **Phase:** 1 (CRM brain) · **Status:** draft for Agent 10 synthesis and owner video reconciliation
> **Scope:** what the business sells (categories, services, pricing, durations, checklists/photos/questions, recurring options, short links, customer add-ons, company add-ons, inventory) and where it operates (service areas/zones, drive-time isochrones, polygons, staff-to-zone assignment, travel charges, zone tax rates, shops, distance filtering, booking-map icons, out-of-area override).
> **Not in scope (referenced only):** availability math & travel time (03), job execution (04), totals/tax order/payments (05), subscription packages & discount codes (06), customer assets (07), notifications/reporting (08), worker profiles/permissions (09), booking-site UX & hosting (01).

### Citation legend

| Tag | Source file |
|---|---|
| `[S: T01]` | `research/transcripts/01_mP9YP49qDiI.txt` (Subscriptions) |
| `[S: T02]` | `research/transcripts/02_rMVRwp-anZM.txt` (Route Optimization) |
| `[S: T03]` | `research/transcripts/03_-d1cjZPEmoQ.txt` (Timesheets; permission pattern only) |
| `[S: T04]` | `research/transcripts/04_pASG8RTrhWc.txt` (Quotes & Invoices) |
| `[S: T05]` | `research/transcripts/05_0qNwovgNTqM.txt` (Online Booking Page) |
| `[S: T06]` | `research/transcripts/06_0atw7gNWDzg.txt` (Services & Prices) |
| `[S: T06d]` | `research/transcripts/06_0atw7gNWDzg.description` (chapter list) |
| `[S: T07]` | `research/transcripts/07_uD5ND6GyoYA.txt` (Add-Ons) |
| `[S: T08]` | `research/transcripts/08_w3D1W6oOaNY.txt` (Jobs & Appointments) |
| `[S: T09]` | `research/transcripts/09_xO4jahos3rE.txt` (CRM Tips) |
| `[S: T11]` | `research/transcripts/11_8sT9cFazZAA.txt` (Service Areas) |
| `[S: T11d]` | `research/transcripts/11_8sT9cFazZAA.description` |
| `[S: T13]` | `research/transcripts/13_C2EsjjXfOOE.txt` (Add Team Member) |
| `[S: T14]` | `research/transcripts/14_5zqqoRbV1DU.txt` (Recurring Job) |
| `[S: T15]` | `research/transcripts/15_Dh1Ib3a4psU.txt` (Book a Job) |
| `[S: T16]` | `research/transcripts/16_G43PcI4hWOk.txt` (Payment Methods) |
| `[S: T19]` | `research/transcripts/19_ZPf7JSlvIcw.txt` (Schedule a Job) |
| `[S: llms]` | `research/site/llms.txt` (fieldd official facts, 2026-10-23) |
| `[I]` | Inferred: industry convention or design decision by Agent 02. Never Fieldd fact. |

Transcripts 03, 10, 12, 17, 18, 20 were skimmed; they contain nothing catalog-specific beyond what is cited elsewhere (e.g. T09 mentions top-performing services/service areas in reporting; T08 mentions filtering jobs by service area).

### Source discrepancies to resolve with the owner's video

| # | Discrepancy | Sources | Working assumption |
|---|---|---|---|
| D1 | Catalog is reached via **Settings → Pricing** in T06/T07, but via **Settings → Services** in T02. | `[S: T06]` "head to the settings tab and then click pricing"; `[S: T07]` "settings, click pricing, and select customer add-ons"; `[S: T02]` "go to settings, then select services" | Same screen, renamed in the 2026 settings rework (`[S: llms]` "Settings: hundreds of features, now easier to find"). We call it **Settings → Services & Pricing**. `[I]` |
| D2 | Services use **save then publish** (T02, T06); add-ons "save automatically" and "once I press save, all updates are automatically reflected" (T07); service areas: "once you press save, your online booking page will instantly update" (T11). | `[S: T02]` `[S: T06]` `[S: T07]` `[S: T11]` | Fieldd appears to have two models. We adopt **one** model: autosaved drafts + explicit Publish for the sellable catalog (services, categories, add-ons, pairings); coverage (zones/shops) goes live on Save with impact preview. See §5.9. `[I]` |
| D3 | Creating a new item: "click the plus button ... and select service" (T06) vs "click the new button at the top right and choose service" (T02). | `[S: T06]` `[S: T02]` | Both exist: a global "New" button (top right) and per-category "+" buttons. `[I]` |
| D4 | Add-on "min or max quantities" could mean per-booking quantity limits **or** inventory par levels. | `[S: T07]` "part numbers, stock levels, and min or max quantities to help manage my inventory" | Model both separately (order qty limits + reorder/par levels). Open question Q4. `[I]` |
| D5 | Travel charge "applied to any mobile service booked within this zone" — per service line or once per booking? | `[S: T11]` | Once per job visit by default; per-service mode available. Open question Q2. `[I]` |

---

## 1. Summary

The Catalog & Coverage module is the single source of truth for **what a business sells** and **where it sells it**. The catalog holds categories (which act as pages in the booking flow `[S: T06]`), services (name, description, image, price, cost, duration, mobile/shop flags `[S: T06]`, standard or route type `[S: T02]`), per-service worker checklists, before/after photo counts and customer questions `[S: T06]`, recurring-booking options `[S: T06]`, per-service booking short links `[S: T06]`, customer add-ons and internal company add-ons with optional product inventory `[S: T07]`. Coverage holds service areas ("zones") generated as drive-time areas around a center point and editable by hand `[S: T11]`, staff assigned to each zone, a fixed travel charge and custom tax rate per zone, and physical shops with assigned workers and distance-based booking filters `[S: T11]`.

Every other module reads from here: the booking site (01) renders the published catalog and checks coverage; scheduling (03) gets durations, route type and eligible workers; the job workflow (04) snapshots checklists/photos/questions; payments (05) receive priced lines, cost, travel charge and the resolved tax rate; subscriptions/discounts (06) reference service and add-on IDs; team (09) maps workers to services, zones and shops. Changes to the catalog are versioned and published atomically so the booking site, CRM and worker app stay consistent `[S: T06]` ("These new services will also appear in the CRM and the Fieldworker app"). Bookings always snapshot the price, cost, duration and tax rate they were sold at, so editing the catalog never silently re-prices existing work `[I]`.

---

## 2. Roles

| Role | What they do here | Source |
|---|---|---|
| **Owner** | Picks an industry pricing template at signup; full CRUD on catalog and coverage; publishes; sets tax rates and travel charges; deletes/archives. | template at signup `[S: T06]`, `[S: T07]`; default zone from signup city `[S: T11]` |
| **Admin** | Same as owner for catalog/coverage (owner, operator or admin can change settings per T03 pattern). | `[S: T03]` permission pattern; catalog-specific matrix `[I]` |
| **Manager** | Edit catalog drafts, inventory counts, zone staff assignment; publish only if granted `catalog.publish`. | `[I]` |
| **Foreman** | Read catalog; add add-ons/company add-ons to jobs; adjust stock on job completion. | `[I]` (foreman/manager distinction exists for timesheets `[S: T03]`) |
| **Worker** | Sees published services, checklists, photo requirements and worker questions inside the worker app; can add customer/company add-ons to a job in the worker POS before the job is complete; only offered services they are allowed to perform. | `[S: T06]` checklists in worker app; `[S: T07]` "worker app POS before the job is marked complete"; allowed services `[S: T13]` |
| **Customer** | Browses categories → services → add-ons on the booking site, answers service questions, chooses recurring frequency if enabled, sees travel charge and tax, uses service short links; is blocked if outside coverage. | `[S: T06]`, `[S: T07]`, `[S: T11]` |
| **System** | Seeds template + default zone at signup; generates isochrones; resolves address → zone; decrements inventory; emits events; caches published catalog for the booking site. | `[S: T11]` default zone; rest `[I]` |
| **AI assistant (MCP / built-in assistant)** | Can read and (CRM-level) modify catalog; the booking-level MCP reads only the published catalog and coverage check. AI setup imports services and pricing from a website. | `[S: llms]` "CRM MCP ... Full business-level access", "Bookings MCP ... Customer-level access only", "AI setup: fieldd brings over a business's services, pricing, and branding from its website" |

### 2.1 Permission matrix `[I]` (Agent 09 owns the RBAC engine; these are the catalog permission keys it must support)

| Permission key | Owner | Admin | Manager | Foreman | Worker | Customer (public) |
|---|---|---|---|---|---|---|
| `catalog.read_published` | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ (public fields only) |
| `catalog.read_cost` (see cost/profit) | ✓ | ✓ | configurable (default ✓) | ✗ | ✗ | ✗ |
| `catalog.edit_draft` | ✓ | ✓ | configurable (default ✓) | ✗ | ✗ | ✗ |
| `catalog.publish` | ✓ | ✓ | configurable (default ✗) | ✗ | ✗ | ✗ |
| `catalog.apply_template` | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ |
| `catalog.archive_delete` | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ |
| `inventory.adjust` | ✓ | ✓ | ✓ | configurable | ✗ | ✗ |
| `coverage.edit` (zones, shops) | ✓ | ✓ | configurable (default ✗) | ✗ | ✗ | ✗ |
| `coverage.edit_fees_tax` | ✓ | ✓ | ✗ | ✗ | ✗ | ✗ |
| `coverage.assign_staff` | ✓ | ✓ | ✓ | ✗ | ✗ | ✗ |
| `coverage.override_out_of_area` (book outside zones from CRM) | ✓ | ✓ | ✓ | configurable | configurable | ✗ |
| `job.add_addon` (customer add-on to job) | ✓ | ✓ | ✓ | ✓ | ✓ (worker POS) | ✗ after booking |
| `job.add_company_addon` | ✓ | ✓ | ✓ | ✓ | ✓ (worker POS) | ✗ never visible |

Cost fields must be stripped server-side from every payload a role without `catalog.read_cost` receives (never rely on UI hiding).

---

## 3. Screens

All layout descriptions are `[I]` unless cited; the transcripts describe controls and order, not pixels. Navigation path working assumption: **Settings → Services & Pricing** (tabs: Services · Customer add-ons · Company add-ons · Checklists & questions (global) · Inventory) and **Settings → Service areas** (map + zone/shop list). See D1.

### 3.1 Settings → Services & Pricing (catalog home)

- **Where:** Settings tab → "Pricing" `[S: T06]` / "Services" `[S: T02]`.
- **Layout `[I]`:**
  - Header: title, **Publish** button (primary; shows count badge of unpublished changes), **View booking page** link (opens live booking site; T06 "view my online booking page to see everything live" `[S: T06]`), **New ▾** menu (Category, Service, Route service) `[S: T02]` "click the new button at the top right and choose service".
  - Tab strip: Services | Customer add-ons `[S: T07]` | Company add-ons `[S: T07]` | Global checklists & questions `[S: T06]` ("I can configure global checklists and photo requirements in my settings") | Inventory `[I]`.
  - Template banner (shown when catalog was seeded from a template, or empty): "Using template: *Mobile Detailing*" or "Start from an industry template" → template picker (§3.2) `[S: T06]` "If you selected a template during signup, you'll see that pricing model here. If you didn't ... you can browse and select one".
  - Body: list of categories in display order; each category is a collapsible card with icon, name, flags (Mobile only / Shop only), and its services as rows (thumbnail, name, price, duration, type badge "Route", status badge Draft/Published/Hidden, unpublished-change dot). Each category card has a **+** button `[S: T06]` "I'll click the plus button again and select service".
  - Top-level **+** button to add a category `[S: T06]` "I'll click the plus button to add a category".
  - Drag handles to reorder categories and services (order = booking-site order) `[I]`.
  - Search box + filters (type, mobile/shop, status, has unpublished changes) `[I]`.
- **Actions per service row `[I]`:** Edit, Duplicate `[S: T06]`, Copy short link `[S: T06]`, Hide from booking site, Archive, Delete (only if never used).
- **States:**
  - Empty: no template chosen and no categories → hero card "Pick your industry to load services and add-ons in seconds" + "Start from scratch" `[S: T06]` (both options are offered).
  - Loading: skeleton rows (no layout shift).
  - Error: inline banner with retry; drafts are local-first so edits are not lost `[I]`.
  - Success: toast on publish "Published revision #N — live on booking site, CRM and team app" `[I]`.
- **Mobile behavior `[I]`:** single column; tabs become a segmented dropdown; drag-reorder via "Move up/down" actions; Publish sticky at bottom.

### 3.2 Pricing template picker

- **Where:** Signup wizard (industry step) and Settings → Services & Pricing → "Browse templates" `[S: T06]` `[S: T07]` "We offer a range of industry curated pricing templates ... Each template comes with its own set of add-ons that pair with your services".
- **Layout `[I]`:** grid of industry cards (icon, industry name, "N services · M add-ons"), preview drawer listing categories/services/add-ons with prices, currency note, **Use this template** button.
- **Industries to ship first `[I]`** (drawn from `[S: llms]` industry list: auto detailing mobile and shop, mobile mechanics, mobile oil change, house cleaning, carpet cleaning, pressure washing, junk removal, lawn care, real estate photography, dog walking): Mobile auto detailing, Shop auto detailing, House cleaning, Carpet cleaning, Pressure washing, Lawn care, Window cleaning, Mobile oil change, Junk removal, Dog walking/grooming.
- **Apply modes `[I]`** when the catalog is not empty: **Add alongside** (merge; template items get a "(template)" suffix if names collide) or **Replace** (archives existing unreferenced items, hides referenced ones). Replace requires typing the business name to confirm.
- **Fields `[I]`:** industry (required), price region/currency (defaults from business country), "Include add-ons" (default on), "Include sample questions & checklists" (default on).
- **States:** loading previews; error if template version unavailable; success → lands on catalog home in Draft with banner "Review prices, then Publish".
- Templates create **drafts**; nothing goes live until Publish `[I]` (exception: during signup, the seeded catalog is auto-published so the booking page works immediately `[I]`; verify against video).

### 3.3 Category editor (service categories)

- **Opened by:** "+" add category `[S: T06]`.
- **Fields:**

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Name | text | required, 1–60 chars, unique among non-archived service categories (case-insensitive) | — | `[S: T06]` ("I'll name this category residential cleaning") |
| Fulfillment restriction | radio: Any · Mobile only · Shop only | if Mobile only, all child services must allow mobile; if Shop only, child services must allow shop | Any | Mobile only `[S: T06]`; Shop only `[I]` |
| Icon | icon picker (built-in set) or uploaded SVG/PNG | uploaded ≤ 256 KB, square, sanitised SVG | first icon in set | `[S: T06]` "assign it an icon" |
| Description | text, optional | ≤ 300 chars | empty | `[I]` (booking-page subtitle) |
| Visible on booking site | toggle | — | on | `[I]` |
| Display order | integer (drag) | — | append | `[I]` |

- **Behavior:** categories "act as separate pages in your booking flow" `[S: T06]`, so each visible category with ≥ 1 visible, eligible service is a page/step on the booking site (Agent 01 renders). A category with zero eligible services for the customer's location is hidden from that customer `[I]`.
- **Delete:** allowed only if empty; otherwise "Archive category" archives the category and requires moving or archiving its services `[I]`.

### 3.4 Service editor

Opened by "+" inside a category then "Service" `[S: T06]`, or New → Service `[S: T02]`. Full-page editor (desktop: form left, live booking-card preview right `[I]`; mobile: stacked sections). Sections in the order the transcript walks through them `[S: T06]` / `[S: T06d]`:

#### 3.4.1 Basics

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Service type | radio: Standard · Route | Route only selectable if Route Optimization is activated in Toolkit | Standard | `[S: T02]` "Select route", "make sure it's activated in your toolkit" |
| Category | select | required (route services too) | the category whose "+" opened it | `[S: T02]` "Be sure to select a category this service is linked to" |
| Name | text | required, 1–80 chars; unique within category among non-archived | — | `[S: T06]` |
| Description | rich-text-lite (bold, italic, bullet list, line breaks; no HTML) | ≤ 2,000 chars | empty | `[S: T06]` "write a short description" |
| Image | image upload | JPG/PNG/WebP/HEIC ≤ 10 MB; server re-encodes to WebP/AVIF at 3 sizes; crop to 4:3 | none (category icon used as fallback) | `[S: T06]` "upload an image" |
| Price | money (integer minor units) | ≥ 0; ≤ 10,000,000 minor units; required unless pricing mode is "Quote required" | — | `[S: T06]` "set a price" |
| Pricing mode | select: Fixed · Starting at · Hourly · Quote required | Hourly requires duration; Quote required hides price on booking site and routes to quote request (Agent 04) | Fixed | `[I]` |
| Cost | money | ≥ 0; may exceed price (warn "negative margin") | 0 | `[S: T06]` "To track my profit accurately, I can also enter the cost of the service" |
| Margin preview | read-only | (price − cost) / price, shown only to `catalog.read_cost` | — | `[I]` |
| Duration | duration picker (hours + minutes, 5-min steps) | 5 min – 24 h; required for Standard; for Route used as capacity estimate | 60 min | `[S: T06]` "set the time it typically takes to complete ... Field will prevent double bookings" |
| Fulfillment | checkboxes: Mobile (we come to you) · Shop (customer comes to us) | ≥ 1 checked; must respect category restriction | Mobile (if category Mobile only) | `[S: T06]` "I'll mark it as mobile only"; shop `[S: T11]` |
| Taxable | toggle | — | on | `[I]` (T16 "apply tax" exists at booking `[S: T16]`) |
| Visible on booking site | toggle (on = listed; off = CRM/worker app only, still reachable by short link if "Allow via link" on) | — | on | `[I]` |
| Allow via short link when hidden | toggle | — | off | `[I]` |
| Requires customer asset | select: none · vehicle · property · equipment | — | from template (vehicle for detailing) | `[I]`, ties to customer assets `[S: T05]` |
| Buffer after | duration | 0–4 h | 0 | `[I]` (cleanup/drying time; consumed by 03) |
| Max per day | integer, optional | ≥ 1 | none | `[I]` (capacity limit; 03) |
| Internal notes | text | ≤ 2,000 chars, never shown to customers | empty | `[I]` |

#### 3.4.2 Pricing variations `[I]` (whole subsection inferred; standard in detailing/cleaning)

Optional **variation groups** that change price and duration. Each group is single-select (or quantity) and shown to the customer as a step inside the service card.

| Field | Type | Validation | Default |
|---|---|---|---|
| Group name | text | required, ≤ 40 chars (e.g. "Vehicle size", "Bedrooms", "Square footage") | — |
| Selection | radio: Single choice · Quantity (per unit) | — | Single choice |
| Required | toggle | — | on |
| Options[] (single choice) | name, price mode (**Replace base** / **Add to base**), price (cents), cost delta, duration delta (min, may be negative but result ≥ 5 min) | ≥ 2 options; exactly one marked default | — |
| Per-unit (quantity) | unit label ("sq ft", "window", "bag"), unit price (cents, may be fractional via milli-cents for sq-ft pricing — see §5.2), unit duration (seconds), included units, min units, max units, step | min ≤ max; step ≥ 1 | — |
| Asset mapping | maps option → asset attribute value (e.g. vehicle class `suv` → option "SUV/Crossover"; property bedrooms 3 → "3 bedrooms") | each attribute value maps to ≤ 1 option | none |

Examples seeded by templates `[I]`:
- Auto detailing — "Vehicle size": Coupe/Sedan (+$0, +0 min), Small SUV/Crossover (+$20, +15 min), Large SUV/Minivan (+$40, +30 min), Truck (+$40, +30 min), Van/Oversize (+$60, +45 min). Condition surcharges (pet hair, heavy soil, sand) are modelled as **add-ons**, not variations, because they are optional upsells `[S: T07]` pattern.
- House cleaning — "Bedrooms" 1–6 and "Bathrooms" 1–5 (Add to base); or "Square footage" tiers (<1,000 / 1,000–1,999 / 2,000–2,999 / 3,000+).
- Pressure washing — per-unit "sq ft" with included 0, min 200, step 50.
- Lawn care — "Lot size" tiers.

Max 3 variation groups per service, 20 options per group `[I]` (booking-site simplicity).

#### 3.4.3 Checklists & photos (worker-facing)

`[S: T06]` "I can add checkboxes and questions for my workers to complete while on the job. These appear in the field worker app ... I can also require before and after photos by specifying how many should be uploaded at each stage."

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Include global checklist | toggle | — | on | global exists `[S: T06]`; toggle `[I]` (mirrors the global-questions toggle) |
| Checklist items[] | ordered list; each: label (≤ 120 chars), kind (Checkbox · Question), question answer type (Text · Number · Yes/No · Single choice · Multi choice), options (for choice), required (bool), stage (Before start · During · Before complete) | ≤ 50 items; labels non-empty | empty | checkbox+question `[S: T06]`; answer types/stages `[I]` |
| Before photos required | integer | 0–20 | 0 (or global default) | `[S: T06]` |
| After photos required | integer | 0–20 | 0 (or global default) | `[S: T06]` |
| Photo instructions | text | ≤ 300 chars | empty | `[I]` |
| Block completion until satisfied | toggle | — | on | `[I]` (Agent 04 enforces) |

#### 3.4.4 Recurring booking options (customer-facing)

`[S: T06]` "I can turn on recurring booking options. I can suggest booking frequencies, offer discounts, and let customers choose a schedule that works best for them. If I don't turn this on, I can still manually create a recurring booking out of any service, but the customer won't be able to through the online portal."

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Offer recurring on booking site | toggle | — | off | `[S: T06]` |
| Suggested frequencies[] | list of {interval unit (day/week/month), interval count, label, discount type (% / fixed), discount value, highlight ("Most popular")} | 1–6 entries; % 0–100 (stored in basis points); fixed ≤ service base price | Weekly, Every 2 weeks, Every 4 weeks, Monthly with 0 discount `[I]` | frequencies + discount `[S: T06]`; % or fixed `[S: T14]`; daily/weekly/monthly units `[S: T14]` |
| Allow one-time booking | toggle | at least one-time or one frequency must be offered | on | `[I]` |
| Customer-selectable custom frequency | toggle (customer may pick any interval within bounds) | min/max interval | off | "let customers choose a schedule that works best for them" `[S: T06]`; bounds `[I]` |
| Default end condition offered online | Never / after N visits / on date | — | Never | `[S: T14]` (CRM end conditions); online `[I]` |
| Discount applies to | Service price only · Service + add-ons | — | Service price only | `[I]` |

Agent 03 owns the recurrence engine; Agent 06 owns subscription packages (different product: a billing plan vs a repeating booking) `[S: T01]`.

#### 3.4.5 Customer questions (booking-time)

`[S: T06]` "Just like checklist and photos, I can create global questions in my settings. But if this specific service needs more information, I can add questions just for it. Customers will answer these questions before they complete the booking process ... If the service doesn't need as much detail, I can turn off the global questions."

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Include global booking questions | toggle | — | on | `[S: T06]` |
| Service questions[] | ordered; each: prompt (≤ 200 chars), help text, answer type (Short text · Long text · Number · Yes/No · Single choice · Multi choice · Date · Photo upload), options (choice, ≤ 20), required, internal-only label, "Show if" condition (another answer equals X) | ≤ 20 questions | empty | per-service questions `[S: T06]`; types/conditions `[I]` |
| Price impact per option | optional cents + duration delta on choice options | — | none | `[I]` (lets "Is there pet hair? Yes +$25" work without add-ons; off by default to keep add-ons the upsell path) |

Note: **intake form** questions (saved to the customer profile) and the **global booking questions** list live under Online scheduling settings and are owned by Agent 01/07 `[S: T05]`. Agent 02 owns the per-service question list and the per-service "include global questions" flag.

#### 3.4.6 Booking short link

`[S: T06]` "I can copy this link and share it on my website, in emails, or on social media. It will take the customer directly to this specific service without going through the full booking flow."

- Read-only URL field + **Copy** button + **QR** button `[I]` (QR generation owned by 01) + editable slug `[I]`.
- Format `[I]`: `https://{booking-host}/s/{serviceSlug}` where booking-host is the business's booking subdomain (Agent 01). Slug: lowercase kebab of name, unique per business, stable across renames (old slugs kept as redirects).
- Link available only after the service is published; for drafts show "Publish to activate this link" `[I]`.
- Optional UTM/source param builder `[I]` feeding booking source tracking `[S: T05]` ("booking source section").

#### 3.4.7 Footer actions

- **Save** (writes draft; autosaves every change after 800 ms debounce anyway `[I]`), **Duplicate** `[S: T06]`, **Publish** (catalog-wide) `[S: T02]` "click save, then click publish", **Discard draft changes** `[I]`, **Archive** `[I]`.
- Unsaved/unpublished indicator: "Draft — not yet live" chip `[I]`.

### 3.5 Duplicate service dialog

`[S: T06]` "Since they are similar, I'll duplicate the first one and adjust the necessary information."
- Fields `[I]`: New name (default "{name} (copy)"), Target category (default same), Copy: ☑ variations ☑ checklists & photos ☑ questions ☑ recurring options ☑ add-on pairings ☐ worker assignments (09) — default all checked except worker assignments, which default to checked too because duplicates are usually siblings `[I]`.
- Result: new draft service opened in editor; new short-link slug; never copies IDs, revisions, or sales history.

### 3.6 Customer add-ons tab

`[S: T07]` "head to settings, click pricing, and select customer add-ons. These are the upsell items your customers will see when booking."

- **Layout `[I]`:** add-on categories as cards (image + name), each listing add-ons (thumbnail, name, price, extra duration, stock badge for products, "Paired with N/M services"). "+" on the page adds a category `[S: T07]` "First, add a category. All it needs is a name and an image"; "+" inside a category creates an add-on `[S: T07]`.
- **Add-on category fields:** Name (required, ≤ 60) `[S: T07]`, Image (required per transcript "All it needs is a name and an image" — we make it optional with icon fallback `[I]`), display order `[I]`.
- **Add-on editor fields:**

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Name | text | required, ≤ 80 | — | `[S: T07]` |
| Image | image | as service image | none | `[S: T07]` |
| Description | text | ≤ 1,000 | empty | `[S: T07]` |
| Price | money | ≥ 0 | — | `[S: T07]` |
| Cost | money | ≥ 0 | 0 | `[S: T07]` "I can also enter the cost to accurately track my profit" |
| Kind | radio: Service (labor) · Product · Fee | — | Service | `[S: T07]` distinguishes "if the add-on is a service" / "if the add-on is a product"; Fee `[I]` |
| Extra duration | duration (0–8 h, 5-min steps) | only for Service kind (Product may have duration too if installed — allowed) | 0 | `[S: T07]` "This extra time will be added to the overall booking" |
| Quantity selectable | toggle + order qty min/max | min ≥ 1, max ≥ min, max ≤ 99 | off (qty fixed 1) | `[I]` / D4 |
| Duration scales with quantity | toggle | — | on | `[I]` |
| Part number / SKU | text | ≤ 64, unique per business among products | empty | `[S: T07]` "part numbers" |
| Track stock | toggle | Product only | off | `[S: T07]` "stock levels" |
| Stock on hand | integer (or decimal for fluids with unit) | ≥ 0 unless backorder allowed | 0 | `[S: T07]` |
| Reorder point (min stock) | integer | ≥ 0 | 0 | `[S: T07]` "min ... quantities" (D4) |
| Par level (max stock) | integer | ≥ reorder point | none | `[S: T07]` "max quantities" (D4) |
| When out of stock | select: Hide online · Show "Sold out" · Allow backorder | — | Show "Sold out" | `[I]` |
| Taxable | toggle | — | on (product) / inherits (service) | `[I]` |
| Paired services | checklist of all services, grouped by category; "Select all"/"Clear" | — | **all services on** | `[S: T07]` "By default, Field will attach it to every service, but I can manually turn it off for any service that shouldn't offer it" |
| Also pair with services created later | toggle | — | on | `[I]` (resolves ambiguity in "attach it to every service") |
| Visible on booking site | toggle | — | on | `[I]` |
| Max per booking | integer | ≥ 1 | none | `[I]` |

- **Save model:** `[S: T07]` "Your changes save automatically" → autosave to draft (debounced), status chip "Saving… / Saved / Couldn't save — retry". Go-live via catalog Publish (D2) `[I]`.
- **States:** empty ("Add-ons are where many businesses earn a large share of revenue — add your first" — paraphrase, not Fieldd copy) `[S: T07]` claim; loading; autosave error with retry and local queue.

### 3.7 Company add-ons tab

`[S: T07]` "If you have add-ons that you don't want your customers to see, like labor fees or equipment charges, you can set those up in the company add-on section. The process is exactly the same ... these add-ons will only appear when scheduling jobs within the field CRM or worker app."

- Same editor as §3.6 with: no "Visible on booking site" (always false), pairing still applies (controls which job types offer it in CRM/worker app) `[S: T07]` "I can configure all the same options".
- Extra fields `[I]`: "Default on new jobs for paired services" (auto-add, e.g. a disposal fee), "Editable price at point of sale" (lets worker enter variable amount, e.g. extra labor hours), "Requires manager approval above $X".

### 3.8 Global checklists, photos & questions

`[S: T06]` global checklists, photo requirements and customer questions exist "in my settings".
- **Where `[I]`:** Services & Pricing → Global tab (worker checklist + photo defaults). Global **booking** questions may live under Online scheduling → Booking questions `[S: T05]` (Agent 01 owns that screen; Agent 02 owns the data and merge rule). Verify against video.
- Fields: same item editors as §3.4.3 / §3.4.5, plus "Apply to: All services · Mobile services · Shop services · Selected categories" `[I]`.

### 3.9 Inventory view `[I]` (better-than-Fieldd; Fieldd shows stock only inside the add-on editor `[S: T07]`)

- Table of product add-ons (customer and company): SKU, name, on hand, reserved (future jobs), available, reorder point, par level, status chip (OK/Low/Out/Backorder), last movement.
- Actions: Adjust (+/−, reason: received, damaged, count correction, used off-job), Bulk CSV import/export (aligns with T09 "download icon ... export important data" `[S: T09]`), View movements.
- Low-stock filter and "Reorder list" (on hand ≤ reorder point → suggested qty = par − on hand).

### 3.10 Settings → Service areas (coverage home)

`[S: T11]` "head to the settings tab, then select service areas. Here you'll see that field has already created a default service area based on the city you entered when signing up."

- **Layout `[I]`:** split view — left panel list (Zones section, Shops section, each with "+" `[S: T11]` "Click the plus button"), right panel full-height map showing all zone polygons (semi-transparent fills, distinct colors), shop pins, and worker home pins (optional layer).
- Zone list row: color swatch, name, "≤ N min drive", staff avatars (count), travel charge badge, tax badge, status (Active/Disabled).
- Shop list row: icon, name, address, assigned workers count, distance limit badge.
- Map controls: search address ("Is this address covered?" test tool `[I]`), layer toggles, zoom-to-all.
- **Empty state:** never truly empty after signup (default zone exists) `[S: T11]`; if all deleted: "Customers can't book mobile services until you add a service area".
- **Mobile behavior `[I]`:** list first, map behind a "Map" toggle; polygon editing disabled on phones < 600 px (view-only) with message "Edit shapes on a larger screen" — creating by drive time still works.

### 3.11 Zone editor

`[S: T11]` sequence: plus → name → "type in the city or address you'd like the zone to be centered around" → "input the maximum amount of time you're willing to travel within this zone" → "Click draw and watch field create an optimized travel area around your selected center point" → "save here or ... further customize the zone" → save → "Field will then ask you if you want to add staff to the zone".

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Name | text | required, ≤ 60, unique among active zones | — | `[S: T11]` |
| Center | address/city autocomplete → geocoded point | must geocode; precision ≥ locality | — | `[S: T11]` |
| Max travel time | minutes (slider + input) | 5–120 | 30 `[I]` | `[S: T11]` |
| Draw | button → generates isochrone polygon | requires center + time; disabled while generating | — | `[S: T11]` |
| Polygon | map shape editor: drag vertices, add vertex (click midpoint), delete vertex, add hole/exclusion, draw additional parts (multi-polygon) | valid, non-self-intersecting, ≥ 3 vertices per ring, area ≥ 0.25 km², ≤ 2,000 vertices total | generated isochrone | "further customize the zone to your liking" `[S: T11]`; editor specifics `[I]` |
| Drive-time mode | Typical traffic · No traffic | — | Typical traffic | `[I]` |
| Avoid | tolls / highways / ferries checkboxes | — | none | `[I]` |
| Travel charge — name | text | ≤ 40; required if amount > 0 | "Travel fee" | `[S: T11]` "Simply create a name and enter a fixed dollar amount" |
| Travel charge — amount | money | ≥ 0, ≤ 100,000 minor units | 0 | `[S: T11]` |
| Travel charge — applies | Once per visit · Per mobile service line | — | Once per visit | D5 `[I]` |
| Travel charge — taxable | toggle | — | follows business setting (default off) | `[I]` |
| Travel charge — waive above order subtotal | money, optional | — | none | `[I]` |
| Custom tax rate | toggle + percentage (up to 4 decimals) + label (e.g. "Austin 8.25%") | 0–30% | off (use business default) | `[S: T11]` "You can also assign a custom tax rate" |
| Priority | integer (drag order in list) | — | creation order | `[I]` (overlap resolution §10.3) |
| Booking-page icon | icon picker / upload | as category icon | default pin | `[S: T11]` "custom icons for your online booking page" |
| Color | swatch | — | auto | `[I]` |
| Accept online bookings | toggle | — | on | `[I]` |
| Enabled services | All services · Selected services | — | All | `[I]` (e.g. "no ceramic coating in outer zone") |
| Status | Active / Disabled | — | Active | `[I]` |

- **States:** generating isochrone (spinner on map, cancel button, ≤ 10 s target); generation failed (fallback: "Draw a circle of N km instead" or "Draw manually") `[I]`; invalid polygon (red vertices, Save disabled with reason); save success → staff prompt (§3.12).
- **Impact preview on save `[I]`:** "This change moves 3 upcoming bookings outside any zone and changes the travel charge on 12 future jobs (existing jobs keep their price)."

### 3.12 "Add staff to this zone?" prompt

`[S: T11]` "Field will then ask you if you want to add staff to the zone."
- Modal `[I]`: list of active workers with avatar, home-base distance to zone, current zones; checkboxes; "Select all"; buttons **Add staff** / **Skip**.
- Same assignment editable later from the zone editor and from the worker profile (Agent 09) `[S: T11]` "when you add a new worker ... you'll see your service areas and shops come up in their worker profile"; `[S: T13]` "assign them to a service area".

### 3.13 Shop editor

`[S: T11]` "Just like when you're adding a zone, enter a name and an address, then click save. For easier shop scheduling, you can assign workers to your shop in the people tab."

| Field | Type | Validation | Default | Source |
|---|---|---|---|---|
| Name | text | required, ≤ 60, unique | — | `[S: T11]` |
| Address | address autocomplete → geocoded rooftop point | must geocode to street-level precision | — | `[S: T11]` |
| Workers | multi-select (also editable in People tab) | — | none | `[S: T11]` |
| Distance-based filtering | toggle + max distance (km/mi by locale) or max drive minutes | 1–500 km / 5–180 min | off | `[S: T11]` "distance-based filtering for shop scheduling. This helps limit bookings from customers who may be too far away from your shop" |
| Booking-page icon | icon | — | storefront | `[S: T11]` |
| Custom tax rate | as zone | — | inherit (§5.7) | `[I]` |
| Services offered here | All shop services · Selected | — | All | `[I]` |
| Phone, photo, arrival instructions | text/image | — | empty | `[I]` (shown on booking confirmation by 01) |
| Opening hours / bays | delegated to Agent 03/09 availability | — | — | `[I]` |
| Timezone | derived from geocode, overridable | IANA tz | derived | `[I]` |

### 3.14 Out-of-area booking override (CRM)

`[S: T11]` "If a customer is outside of your service area, you can always override it by manually booking the appointment through the Field CRM."
- Screen owned by Agent 03 (booking from CRM). Agent 02 provides the coverage result; UI `[I]`: warning banner "123 Main St is outside all service areas" with **Book anyway** (requires `coverage.override_out_of_area`), optional "Nearest zone: Austin (4 min outside)" and a manual travel-charge field.

### 3.15 Inline service creation from booking (CRM)

`[S: T15]` "choose an existing service from the drop down or create a new one"; `[S: T19]` "pick a service from the drop down or type in a new one and save it for next time".
- Agent 03 owns the booking form; Agent 02 provides `catalog.createServiceQuick` (name, price, duration, mobile/shop, category default "Other") which creates a **published but hidden-from-booking-site** service so it is usable immediately and does not leak an unreviewed item onto the public site `[I]`. "Save it for next time" unchecked → creates a **one-off line** (custom line item on the job, not a catalog entry) `[I]`.
- `[S: T19]` "attach the cost of each service to track profits automatically for every job" → per-job cost override allowed on the job line (owned by 04/05, default from catalog cost).

---

## 4. Workflows

Notation: **writes** = records created/updated; **emits** = events (see §9); **notifies** = notification triggers handed to Agent 08.

### W1. Signup seeding (template + default zone)

1. Owner completes signup with business name, industry, city/address, country `[S: T11]` (city entered at signup) and optionally picks an industry pricing template `[S: T06]` `[S: T07]`.
2. System resolves currency and default tax behavior from country `[I]`.
3. If a template was chosen: instantiate the template (§10.8) → categories, services, variations, add-on categories, add-ons, pairings, sample checklists/questions. All template prices are in the business currency (templates store prices per region; fallback converts and rounds to "nice" price points such as $X9/$X5) `[I]`.
4. Geocode the signup city → center point. Request a drive-time isochrone with default 30 min `[I]` (verify: Fieldd may instead use the city boundary polygon). Create zone named after the city `[S: T11]` ("default service area based on the city"), priority 1, no travel charge, no custom tax, status Active.
5. If the business has no workers yet besides the owner, assign the owner to the default zone and to all services (solo operator path) `[I]`.
6. Auto-publish revision #1 so the booking page works on first visit `[I]`.
- **writes:** `PricingTemplateApplication`, `Category*`, `Service*`, `AddOn*`, `AddOnPairing*`, `ServiceArea`, `WorkerZoneAssignment`, `CatalogRevision#1`.
- **emits:** `catalog.template.applied`, `catalog.revision.published`, `coverage.zone.created`, `coverage.zone.staff_assigned`.
- **failure handling:** geocode or isochrone failure must not block signup; create zone with a 25 km circle approximation and flag "Review your service area" task on the dashboard `[I]`.

### W2. Apply a template later

1. Settings → Services & Pricing → Browse templates `[S: T06]` → pick industry → preview.
2. Choose **Add alongside** or **Replace** `[I]`.
3. System instantiates into the **draft** (never directly live) `[I]`. Replace: unreferenced existing items → archived in draft; referenced items (used by future jobs, subscriptions, discounts) → hidden in draft, not archived.
4. Banner "Template added to draft — review and Publish".
- **emits:** `catalog.template.applied` (mode, counts).

### W3. Create a service category

1. Click "+" (add category) `[S: T06]`.
2. Enter name, choose Mobile only/Shop only/Any, pick icon `[S: T06]`.
3. Save → draft category appended to end of order.
- **emits:** `catalog.category.created` (draft).

### W4. Create a standard service

1. In a category click "+" → Service `[S: T06]` (or New → Service `[S: T02]`).
2. Enter name, description, image, price, cost, mobile/shop, duration `[S: T06]`.
3. Optional: variations (§3.4.2), checklists/photos (§3.4.3), recurring options (§3.4.4), questions (§3.4.5).
4. Save → draft service. Autosave runs continuously; Save just forces flush and exits editor `[I]`.
5. System side effects on save (draft): validate; compute `effectiveFulfillment` (service ∩ category restriction); recompute "eligible workers" preview (from Agent 09 skills) and warn "No worker can perform this service yet" `[I]`.
6. On publish (W11) the service appears on the booking page, CRM and worker app `[S: T06]`.
- **emits (draft):** `catalog.service.created` with `status=draft`.
- **notifies:** none.

### W5. Create a route service

1. Toolkit → activate Route Optimization (Toolkit is "a new home for tools that grow the business" `[S: llms]`; activation owned by Agent 03) `[S: T02]`.
2. Settings → Services → New → Service `[S: T02]`.
3. Name, select **Route**, select category (required) `[S: T02]`.
4. Save, then Publish `[S: T02]`.
5. On the booking site the customer selects a **day** not a time; slot label e.g. "Anytime Thursday", text editable at Settings → Customer hub → Style → Text → Routes text (Agent 01 owns the text) `[S: T02]`.
- Route service rules: duration still required (capacity estimate for the day route, 03); recurring options allowed (weekly route customers are the common case) `[I]`; which weekdays a route runs per zone is configured by Agent 03 route settings — Agent 02 exposes `routeDays` optional per service×zone if 03 decides it belongs in catalog (open question Q7).
- **emits:** `catalog.service.created` (`type=route`).

### W6. Configure checklists & photo requirements

1. In service editor → Checklists & photos `[S: T06]`.
2. Add checkbox items and worker questions; set before/after photo counts `[S: T06]`.
3. Optionally turn off inherited global checklist `[I]`.
4. Publish. New jobs created after publish snapshot the merged checklist (§5.10). Existing not-started jobs keep their snapshot; admin may "Refresh checklist from service" on a not-started job (Agent 04 action) `[I]`.
- **emits:** `catalog.service.updated` (changedFields includes `checklist`/`photoRequirements`).

### W7. Configure recurring booking options

1. Toggle on recurring options; add suggested frequencies with optional discounts; allow custom frequency `[S: T06]`.
2. Publish → booking site shows frequency picker on that service; Agent 03 creates a recurring series when customer picks one.
3. If toggle off: booking site offers one-time only; CRM can still create recurring jobs for any service `[S: T06]` `[S: T14]`.

### W8. Configure customer questions

1. Per-service questions + toggle "include global questions" `[S: T06]`.
2. Publish. Booking site asks merged questions before completing the booking `[S: T06]`. Answers stored on the job (Agent 04) with a copy of the question text (snapshot) `[I]`.

### W9. Share a service short link

1. Service editor → Booking short link → Copy `[S: T06]`.
2. Customer opens `/s/{slug}` → Agent 01 resolves via `catalog.resolveShortLink` → lands on that service's configuration step with category pages skipped `[S: T06]` "without going through the full booking flow". Coverage check still happens (address step), and add-on upsell still shows `[I]`.
3. If service archived/hidden-without-link-allowed → link resolves to the booking home with a non-blocking notice "That service is no longer available — here's what we offer" `[I]`; HTTP 200 (not 404) to avoid broken-looking marketing links; record a `catalog.shortlink.dead_hit` metric `[I]`.

### W10. Duplicate a service

1. Row menu or editor → Duplicate `[S: T06]`.
2. Dialog (§3.5) → confirm.
3. Deep copy (§10.9) → new draft opened in editor; adjust name/price `[S: T06]`.
- **emits:** `catalog.service.created` (`duplicatedFrom` set).

### W11. Publish catalog changes

1. Click Publish `[S: T06]` `[S: T02]`.
2. System runs publish validation (§10.6): every visible service has price (unless quote-required), duration, category, ≥ 1 fulfillment mode; every category visible has ≥ 1 visible service; no add-on pairs to an archived service; route services only if Route Optimization active; image processing complete.
3. Show diff summary dialog `[I]`: "+2 services, 3 price changes (Interior Detail $149 → $159), 1 add-on archived". Warnings (non-blocking): "No worker assigned to 'Ceramic Coating'", "Service has no image".
4. Confirm → atomic revision: write `CatalogRevision N+1` (immutable snapshot), set `business.publishedRevisionId`, invalidate booking-site cache/CDN for this business.
5. Booking page, CRM and worker app now read revision N+1 `[S: T06]` "These new services will also appear in the CRM and the Fieldworker app".
- **emits:** `catalog.revision.published` (revision, diff), plus per-entity `catalog.service.published`, `catalog.service.price_changed`, `catalog.addon.published`, etc.
- **notifies:** optional internal notice to team app "Service list updated" (Agent 08 decides) `[I]`.

### W12. Create a customer add-on

1. Customer add-ons tab → "+" category (name + image) `[S: T07]`.
2. "+" in category → name, image, description, price, cost `[S: T07]`.
3. If service kind: extra duration `[S: T07]`. If product: part number, stock, min/max quantities `[S: T07]`.
4. Pairing: all services on by default; toggle off exceptions `[S: T07]`.
5. Autosave `[S: T07]` → draft. Publish to go live (our model, D2).
- **emits:** `catalog.addon.created`, `catalog.addon_pairing.changed`.

### W13. Create a company add-on

Same as W12 in Company add-ons tab `[S: T07]`; never published to the booking-site snapshot (public snapshot filters `audience=company`) — appears in CRM and worker app only `[S: T07]`.

### W14. Add an add-on to an existing job (upsell after booking)

`[S: T07]` "If myself or a team member upsells a customer after the job is booked, these add-ons can still be added to the job in the field CRM or in the worker app POS before the job is marked complete."
1. CRM job card or worker app POS → Add add-on → list filtered to add-ons paired with the job's services (customer + company), in-stock or backorder-allowed `[I]`.
2. Select, qty → Agent 04 adds a job line with snapshot (price, cost, duration, tax flag, SKU).
3. Agent 02: if product with stock tracking → create reservation (or immediate decrement if job is in progress, §5.6).
4. Agent 03: extra duration extends the job end; if it causes overlap with the worker's next job, warn (do not auto-move) `[I]`.
5. Agent 05: totals recomputed; if card pre-authorised for lower amount, re-auth per 05 rules.
6. Guard: job status must not be `completed`/`cancelled` `[S: T07]` "before the job is marked complete". After completion, changes go through invoice adjustment (05) `[I]`.
- **emits (from 04):** `job.line.added`; Agent 02 consumes for inventory.

### W15. Create zone from drive time

1. Service areas → "+" → name → center (city/address) → max travel time → Draw `[S: T11]`.
2. System geocodes center, calls isochrone provider (§10.2), simplifies polygon, shows it on the map.
3. Optional manual edits (W16).
4. Save → validation → impact preview → persisted, **live immediately** `[S: T11]` "Once you press save, your online booking page will instantly update".
5. Prompt "Add staff to this zone?" (W17) `[S: T11]`.
- **writes:** `ServiceArea`, `ZoneGeometryVersion`.
- **emits:** `coverage.zone.created`, `coverage.zone.geometry_changed`.

### W16. Edit polygon manually

1. Zone editor → drag/add/delete vertices, add exclusion holes, add separate parts `[I]` ("further customize the zone to your liking" `[S: T11]`).
2. Client-side validity check on each edit (self-intersection highlight); server re-validates.
3. Save → `geometrySource = manual` (zone remembers generation params so "Regenerate from drive time" is possible, with warning it discards manual edits) `[I]`.
- **emits:** `coverage.zone.geometry_changed` (with `addedAreaKm2`, `removedAreaKm2`, affected future bookings count).

### W17. Assign staff to zone

1. Post-save prompt or zone editor → Staff section, or worker profile → Service areas (Agent 09) `[S: T11]` `[S: T13]`.
2. Select workers → Save.
3. Effects: Agent 03 uses zone staff as the candidate pool for mobile bookings at addresses in that zone `[S: T11]` "It helps ensure that no one is traveling too far"; `[S: T13]` "This helps field know where this worker is available for jobs".
- **writes:** `WorkerZoneAssignment` rows.
- **emits:** `coverage.zone.staff_assigned` / `coverage.zone.staff_unassigned`.
- **guard:** removing the last worker from a zone with future bookings → warn "N future jobs in this zone have no eligible worker" `[I]`.

### W18. Set travel charge and custom tax rate

1. Zone editor → travel charge name + fixed amount `[S: T11]`; custom tax rate `[S: T11]`.
2. Save → applies to **new** quotes/bookings only; existing jobs keep snapshots `[I]`. Option on save `[I]`: "Also update N not-yet-paid future jobs" (explicit, logged) — Agent 05 recomputes those totals.
- **emits:** `coverage.travel_charge.changed`, `coverage.tax_rate.changed`.

### W19. Create a shop and assign workers

1. Service areas → Shops "+" → name + address → Save `[S: T11]`.
2. Assign workers in People tab (Agent 09) `[S: T11]` or from shop editor `[I]`.
3. Optional: distance-based filter, booking-page icon `[S: T11]`.
4. Booking site: when the customer chooses "At the shop" (or a shop-only service), Agent 01 lists shops (filtered by distance if enabled, sorted nearest first) `[I]`.
- **emits:** `coverage.shop.created`, `coverage.shop.staff_assigned`.

### W20. Address → coverage resolution at booking (system)

1. Customer enters address (booking site) or admin enters job address (CRM).
2. `coverage.resolveLocation(address)` → geocode (cached) → point-in-polygon over active zones (§10.3) → result `{covered, primaryZoneId, allZoneIds, travelCharge, taxRate, eligibleWorkerIds, eligibleServiceIds, nearestZone?}`.
3. Booking site filters categories/services to `eligibleServiceIds` for mobile; shop services shown if a shop is within distance.
4. Not covered → booking site shows out-of-area message and optional "Notify me / request a quote" capture which creates a lead (Agent 07) `[I]`; CRM shows override (W21).
- **emits:** `coverage.location.out_of_area_attempted` (for 07 leads and 08 heatmaps/reporting) `[I]`. No event for successful lookups (too chatty); metrics only.

### W21. CRM out-of-area override

1. Admin books in CRM; coverage says not covered `[S: T11]`.
2. Admin with permission clicks Book anyway → chooses worker manually (no zone staff pool), optional manual travel charge, tax = business default unless set `[I]`.
3. Job saved with `coverage.override = {by, at, reason?, nearestZoneId, distanceOutsideKm}` `[I]`.
- **emits:** `coverage.override.used` (audit; consumed by 10/08 reporting).

### W22. Archive/delete catalog items

1. Delete allowed only if the item was never published **and** is not referenced; otherwise **Archive** `[I]`.
2. Archive effects: removed from next published revision; existing jobs, recurring series, subscriptions, discounts keep referencing it by ID (snapshots keep names/prices).
3. Referenced by **future** recurring occurrences or active subscription packages → block archive with list and options: "Keep offering to existing customers only" (= hidden from booking site, still valid for CRM/recurring) or "Replace with…" (maps to another service for future occurrences; Agent 03/06 apply) `[I]`.
- **emits:** `catalog.service.archived` / `catalog.addon.archived`; `catalog.service.replaced` (with mapping).

### W23. Inventory adjustment and consumption

1. Manual: Inventory → Adjust (reason) → `StockMovement` row; on-hand updated atomically `[I]`.
2. Automatic: product add-on on a job → reservation on job creation; conversion to consumption on `job.job.completed`; release on cancellation or line removal (§5.6) `[I]`.
3. Crossing reorder point → `inventory.stock.low`; reaching 0 → `inventory.stock.depleted` → booking-site visibility per "When out of stock" setting.
- **notifies:** low-stock alert to owner/admin (Agent 08 catalog: "Inventory low") `[I]`.

### W24. AI-assisted setup (import services from website)

`[S: llms]` "AI setup: fieldd brings over a business's services, pricing, and branding from its website or existing software. New businesses can go live in minutes from one prompt."
1. Owner enters website URL (or uploads a price list/CSV) `[I]`.
2. Extractor proposes categories/services/prices/durations/add-ons as a **draft** with per-item confidence; durations default from template when missing `[I]`.
3. Owner reviews side-by-side, accepts, publishes. Never auto-publishes imported prices `[I]`.
- **emits:** `catalog.import.completed` `[I]`.

---

## 5. Business rules & math

### 5.1 Money

- All money is **integer minor units** (cents) with an ISO-4217 currency on the business; zero-decimal currencies (JPY) and three-decimal (KWD) handled via currency exponent `[I]`.
- Catalog never computes final totals; it returns **priced lines** (pre-discount, pre-tax) plus the applicable tax rate and fees. Agent 05 owns discount order, tax order, tips, rounding of totals.
- Percentages (recurring discount, tax) are stored as integers: discounts in **basis points** (1 bp = 0.01%), tax rates in **parts per million** (ppm; 8.25% = 82,500 ppm; 8.875% = 88,750 ppm) to support 4-decimal tax rates `[I]`.
- Per-unit pricing (e.g. $0.15/sq ft) stored as **milli-cents per unit** (15,000 milli-cents); line amount = `roundHalfEven(unitMilliCents × qty / 1000)` cents `[I]`.

### 5.2 Service line price

```
basePrice            = service.price                       (cents)
variationAdj         = Σ over selected single-choice options:
                         mode=replace → option.price − basePrice  (only one replace group allowed per service)
                         mode=add     → option.price
perUnitAdj           = Σ over quantity groups: roundHalfEven(unitMilliCents × max(0, qty − includedUnits) / 1000)
questionAdj          = Σ selected question options' priceDelta    (only if enabled)
servicePrice         = max(0, basePrice + variationAdj + perUnitAdj + questionAdj)
serviceCost          = service.cost + Σ option.costDelta (+ per-unit cost)
```
- Exactly **one** variation group per service may use "replace base" mode; others must be "add" `[I]` (prevents ambiguous double replacement).
- `Starting at` pricing mode: booking site shows "From $X"; the actual price is still `servicePrice` from selections, final adjustments by admin (05) `[I]`.
- `Hourly`: price = rate × duration hours, rounded half-even to cents; final billed by actual time is Agent 05/04's concern `[I]`.
- `Quote required`: no price; booking becomes a quote request (Agent 04) `[I]`.

### 5.3 Add-on line price

```
addonLinePrice = addon.price × qty
addonLineCost  = addon.cost × qty
addonDuration  = addon.extraDuration × (durationScalesWithQty ? qty : 1)
```
`qty` constrained to `[orderQtyMin, orderQtyMax]` when quantity selectable, else 1 `[I]`.

### 5.4 Duration

```
serviceDuration = service.duration + Σ option.durationDelta + Σ unitDuration × billableUnits + Σ questionDurationDelta
                  clamped to ≥ 5 min
jobWorkDuration = Σ serviceDuration(lines for this visit) + Σ addonDuration
jobBlockedTime  = jobWorkDuration + max(service.bufferAfter over lines)
```
- `[S: T06]` realistic durations prevent double bookings; `[S: T07]` add-on extra time "will be added to the overall booking to prevent overlaps".
- Durations stored in **seconds** (integer). UI shows h/min.
- Multi-worker split (`[S: llms]` "split a job or its services between team members") → Agent 03 decides per-worker blocks; catalog gives per-line durations so 03 can split `[I]`.
- Travel time is **not** in catalog duration; Agent 03 computes it (`[S: T19]` "Field even factors in the travel time"; `[S: llms]` "travel time for mobile jobs on the calendar").

### 5.5 Profit

```
lineProfit = linePrice − lineCost       (per line, cents, may be negative)
jobProfit  = Σ lineProfit − allocatedFees (Agent 05 decides whether processing fees/travel are included)
```
- Cost is snapshotted on the job line at booking; editable per job `[S: T19]` "attach the cost of each service to track profits automatically for every job".
- Company add-ons (e.g. labor fee) carry price and cost like any line `[S: T07]`.

### 5.6 Inventory

- Quantities are integers in the item's unit (decimal units out of scope for v1) `[I]`.
- `available = onHand − reserved`.
- Reservation lifecycle per job line `[I]`:
  - job line created (future job) → `reserved += qty` (movement type `reserve`);
  - job completed → `reserved −= qty; onHand −= qty` (`consume`);
  - line removed / job cancelled → `reserved −= qty` (`release`);
  - qty edited → delta reserve/release.
- Recurring series: reserve only for occurrences within the next 30 days (rolling) to avoid locking all stock for an infinite series `[I]`.
- Oversell guard: booking-site add-on selection fails if `available < qty` and backorder not allowed; CRM/worker can override with warning (`allowNegative` permission) `[I]`.
- All stock changes are **conditional atomic updates** (`UPDATE ... WHERE available >= qty` or equivalent compare-and-set) with an append-only `StockMovement` ledger; on-hand is derivable from the ledger (rebuild job) `[I]`.
- Low stock: `onHand ≤ reorderPoint` (edge-triggered once per crossing; re-armed when it goes above) `[I]`.

### 5.7 Tax rate resolution (precedence)

Agent 02 resolves **which rate applies**; Agent 05 applies it (order vs discounts, rounding).

```
if customer.taxExempt (07) or line.taxable == false     → 0
else if job.taxOverride (admin set on job, T16 "apply tax")  → override       [S: T16] (owned by 05)
else if fulfillment == shop:
         shop.customTaxRate
      ?? primaryZoneContaining(shop.location).customTaxRate
      ?? business.defaultTaxRate
else (mobile):
         primaryZone(jobLocation).customTaxRate                   [S: T11]
      ?? business.defaultTaxRate
      ?? 0
```
- "Primary zone" = winner of overlap resolution (§10.3). Tax follows the **service location** (destination-based), not the business HQ `[I]`; this matches T11 "operates across multiple cities with different tax requirements" `[S: T11]`.
- Travel charge taxability: per-zone flag, default from business setting (default not taxable) `[I]`; jurisdictions differ — owner decides.
- Out-of-area override jobs: business default rate unless admin sets one `[I]`.
- Tax rates are **effective-dated** (`effectiveFrom`) so a scheduled rate change applies to jobs **performed** on/after the date `[I]` (better than Fieldd; verify whether Fieldd supports).
- Rate is snapshotted on the job at booking; re-resolved if job address changes before completion `[I]`.

### 5.8 Travel charge

- Applies only when fulfillment = mobile and the location is covered by a zone with `travelCharge.amount > 0` `[S: T11]` "This fee will be applied to any mobile service booked within this zone".
- Default mode **once per visit** (one line "Travel fee" per job visit regardless of number of services) `[I]`; alternative mode **per mobile service line** (D5).
- Overlapping zones: travel charge of the primary zone only, never summed `[I]`.
- Route services: travel charge applies like any mobile service (configurable "Exempt route services" `[I]`).
- Waiver: if pre-discount subtotal ≥ `waiveAboveSubtotal` → 0 `[I]`.
- Recurring occurrences: charged each occurrence (it is per visit) `[I]`.
- Subscriptions (06) may include "free travel" as an entitlement → 06 tells 05 to zero the line `[I]`.
- Travel charge is emitted as a separate priced line `{kind: 'travel_fee', label: zone.travelCharge.name}` to Agent 05.

### 5.9 Draft vs publish model

- **Catalog (services, categories, variations, checklists, questions, recurring options, add-ons, add-on categories, pairings, global checklists/questions):** every edit autosaves to the **draft workspace**; Publish creates an immutable `CatalogRevision` snapshot. Booking site, Bookings-MCP, CRM booking pickers and worker app read the **published** revision `[S: T06]` (publish then live) `[I]` (unifies D2).
- Admins can open "Preview draft" on the booking site (signed preview URL, noindex) `[I]`.
- **Coverage (zones, shops, travel charge, tax, staff assignment):** live on Save with impact preview `[S: T11]`. Rationale: coverage edits are rare, operational, and Fieldd shows them as instant.
- **Inventory counts and inventory settings (reorder point):** live immediately (operational data, not marketing content) `[I]`.
- **Quick-create from CRM booking (W15 in §3.15):** published immediately as hidden service `[I]`.
- **Small-business mode `[I]`:** business setting "Publish changes automatically" (default **off** for templates, user can enable) → every successful draft save auto-publishes after 5 s idle. Gives Fieldd-like "save = live" for solo operators.

### 5.10 Snapshot rules (what a booking freezes)

At booking/quote creation the job line stores: `serviceId`, `catalogRevisionId`, name, description (short), variation selections (labels), unit price, cost, duration, taxable, resolved tax rate, travel-charge line, fulfillment mode, zone ID / shop ID, checklist items, photo counts, question prompts + answers, add-on name/price/cost/SKU/qty `[I]`. Catalog changes after that do **not** change the job unless an admin explicitly "re-prices from catalog" `[I]`.
- **Recurring series** (03): future occurrences use the series' locked price by default; owner may choose "Apply new catalog price to future occurrences" at publish time when a price change touches services in active series (publish dialog shows count) `[I]`. Open question Q6.

### 5.11 Fulfillment (mobile vs shop) rules

- `service.fulfillment ⊆ {mobile, shop}`, non-empty. Category restriction intersects: category "Mobile only" → service must include mobile and the shop option is disabled `[S: T06]`.
- Customer can book a service as mobile only if the address is covered by ≥ 1 active zone that enables that service and has ≥ 1 eligible worker; as shop only if ≥ 1 shop offers it and (if filtering on) is within distance `[S: T11]` `[I]`.
- Mixed cart: all services in one visit must share one fulfillment mode (one location) `[I]`.
- Add-ons inherit the visit's fulfillment; add-ons can optionally be restricted to mobile/shop `[I]`.

### 5.12 Add-on pairing

- Default: a new add-on pairs with **all existing services** `[S: T07]` and (our flag, default on) all **future** services `[I]`.
- A new service gets pairings from every add-on with `pairWithFutureServices = true` `[I]`.
- Booking site shows an add-on for a cart if it is paired with **any** service in the cart; it attaches to the first paired service line for duration/worker split purposes (customer may pick which service when multiple paired, CRM only) `[I]`.
- Company add-ons follow the same pairing rule in CRM/worker app `[S: T07]`.

### 5.13 Recurring discount

- Discount per occurrence: `%` (basis points of the eligible base) or fixed cents (capped at the eligible base so line never < 0) `[S: T14]` (fixed or percentage) `[I]` cap.
- Eligible base = service line price (default) or service + add-ons (setting) `[I]`.
- Stacking with discount codes and subscription discounts is Agent 05/06's rule; Agent 02 recommends **non-stacking: best single discount wins** unless the code says "stackable" `[I]`.
- Discount config is snapshotted on the recurring series at creation `[I]`.

### 5.14 Coverage rules

- Points exactly on a polygon boundary are **inside** `[I]`.
- Disabled zones and zones with `acceptOnlineBookings=false` are excluded from booking-site coverage but still usable in CRM (with normal travel/tax) `[I]`.
- Zone with **zero assigned staff** → treated as "all active workers who are allowed the service" (solo/early business friendliness) **with** a persistent warning on the zone `[I]` (Open question Q3 — Fieldd behavior unknown).
- Worker eligibility for a mobile job = ∪ staff of **all** zones containing the point (not just primary) ∩ workers allowed the service (09) ∩ available (03) `[I]`. Rationale: overlapping zones exist because two teams can both reach the area.
- Shop booking eligibility = shop's assigned workers ∩ allowed for service `[S: T11]` `[I]`.
- Max travel time on a zone is a **generation parameter**, not an enforcement rule; enforcement is the polygon `[I]`.

### 5.15 Validation summary

| Entity | Rule |
|---|---|
| Category | name unique (case-insensitive, trimmed, NFC-normalised) among non-archived; icon required |
| Service | price ≥ 0 int; cost ≥ 0 int; duration 300–86,400 s; ≥ 1 fulfillment; category required; route requires toolkit active; slug unique |
| Variation | 2–20 options; one default; resulting duration ≥ 300 s; resulting price ≥ 0 |
| Add-on | price/cost ≥ 0; orderQtyMin ≤ orderQtyMax; reorderPoint ≤ parLevel; SKU unique among products |
| Zone | geometry valid (OGC simple), ≤ 2,000 vertices, area ≥ 0.25 km² and ≤ 50,000 km²; travel charge ≥ 0; tax 0–300,000 ppm |
| Shop | street-level geocode; distance filter 1–500 km |
| Strings | trimmed, no control chars, emoji allowed, max lengths enforced server-side |
| Images | magic-byte check, strip EXIF/GPS, re-encode, max 10 MB, max 8,000 px side |

---

## 6. State machines

### 6.1 Service (catalog item) lifecycle — also applies to Category, AddOn, AddOnCategory

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `create` | valid draft fields | `draft` | emit `catalog.service.created` |
| `draft` | `edit` | `version` matches (optimistic lock) | `draft` | autosave; `hasUnpublishedChanges=true` |
| `draft` | `publish` | publish validation passes | `published` | included in revision N+1; emit `catalog.service.published`; short link active |
| `draft` (never published) | `delete` | no references | — (hard delete) | emit `catalog.service.deleted` |
| `published` | `edit` | lock ok | `published` + pending draft | `hasUnpublishedChanges=true`; live revision unchanged |
| `published` | `publish` (with pending edits) | validation | `published` | new revision; emit `catalog.service.updated` (+`price_changed` if price/cost/duration changed) |
| `published` | `hide` | — | `hidden` (after publish) | not listed on booking site; still in CRM/worker app; link works only if `allowViaLink` |
| `hidden` | `unhide` | — | `published` (after publish) | — |
| `published`/`hidden` | `archive` | no blocking references (§W22) or user chose replacement | `archived` (after publish) | removed from pickers; emit `catalog.service.archived`; short link → fallback |
| `archived` | `restore` | name not colliding | `draft` | needs publish to go live |
| any | `discardDraft` | has pending draft | last published state | emit `catalog.draft.discarded` |

Note: visibility state (`hidden`) and lifecycle (`archived`) are separate fields in the model; the table merges them for readability.

### 6.2 Catalog revision

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `building` | snapshot assembled | validation ok | `ready` | content hash computed |
| `building` | validation fails | — | `rejected` | errors returned to publisher; nothing changes |
| `ready` | pointer swap (CAS on `business.publishedRevisionId == expected`) | no concurrent publish | `live` | previous live → `superseded`; cache purge; emit `catalog.revision.published` |
| `ready` | CAS fails | concurrent publish | `rejected` | client reloads and re-publishes |
| `live` | newer publish | — | `superseded` | kept forever (jobs reference it) |
| `superseded` | `rollback` (owner action) | — | creates new revision copying it → `live` | emit `catalog.revision.published` (`rollbackOf`) `[I]` |

### 6.3 Zone

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `startCreate` | name, center | `drafting` (client-only) | — |
| `drafting` | `draw` | center geocoded | `generating` | isochrone request (idempotency key = hash(center, minutes, mode)) |
| `generating` | provider success | polygon valid after simplification | `drafting` (with geometry) | — |
| `generating` | provider error/timeout (10 s) | — | `drafting` (no geometry, error shown) | offer circle/manual fallback |
| `drafting` | `save` | geometry valid | `active` | emit `coverage.zone.created`; staff prompt |
| `active` | `editGeometry` + save | valid; impact confirmed | `active` | new `ZoneGeometryVersion`; emit `coverage.zone.geometry_changed` |
| `active` | `disable` | — | `disabled` | excluded from booking site; future jobs untouched; emit `coverage.zone.disabled` |
| `disabled` | `enable` | — | `active` | emit `coverage.zone.enabled` |
| `active`/`disabled` | `archive` | not the only active zone **or** user confirms "no mobile coverage" | `archived` | staff assignments ended; emit `coverage.zone.archived` |

### 6.4 Isochrone generation job

`pending → requesting → (succeeded | failed | timed_out)`; `succeeded → simplified → validated`; retries: 2 with jittered backoff for 5xx/timeouts, none for 4xx `[I]`.

### 6.5 Inventory item stock status (derived)

| Condition | Status |
|---|---|
| `!trackStock` | `untracked` |
| `available > reorderPoint` | `ok` |
| `0 < available ≤ reorderPoint` | `low` |
| `available ≤ 0 && outOfStockBehavior=backorder` | `backorder` |
| `available ≤ 0` otherwise | `out` |

Transitions `ok→low`, `low→out`, `*→ok` emit `inventory.stock.low`, `inventory.stock.depleted`, `inventory.stock.restored` (edge-triggered).

### 6.6 Autosave (client)

| State | Event | Next |
|---|---|---|
| `clean` | field change | `dirty` (debounce 800 ms) |
| `dirty` | debounce fires | `saving` |
| `saving` | 2xx | `clean` (`Saved` chip) |
| `saving` | 409 version conflict | `conflict` → show merge dialog (theirs/mine per field) |
| `saving` | network error | `offline_queued` (persist to local storage, retry with backoff) |
| `offline_queued` | online | `saving` |
| any | leave page with `dirty/saving/offline_queued` | block navigation with prompt |

---

## 7. Data model

Stack-agnostic TypeScript-style interfaces. Conventions `[I]`: IDs are opaque strings (ULID/UUIDv7, sortable); every row has `businessId` (tenant key — every query filtered by it), `createdAt`, `updatedAt` (UTC ISO-8601), `version` (int, optimistic lock). Money = `Cents` (integer). Durations = `Seconds` (integer). Geometry = GeoJSON (WGS84, `[lng, lat]`).

```ts
type ID = string;
type Cents = number;          // integer minor units
type MilliCents = number;     // integer, 1/1000 of a minor unit (per-unit pricing only)
type Seconds = number;        // integer
type Bps = number;            // basis points, 10000 = 100%
type Ppm = number;            // parts per million, 1_000_000 = 100% (tax rates)
type ISODateTime = string;    // UTC
type ISODate = string;        // YYYY-MM-DD in business timezone
type CurrencyCode = string;   // ISO-4217
type Fulfillment = 'mobile' | 'shop';
type LifecycleStatus = 'draft' | 'published' | 'archived';

interface CatalogSettings {               // one per business
  businessId: ID;
  currency: CurrencyCode;
  defaultTaxRatePpm: Ppm | null;          // business default (05 may own UI; 02 owns resolution)
  pricesIncludeTax: boolean;              // false in US; true common in AU/UK [I]
  travelChargeTaxableDefault: boolean;    // default false [I]
  autoPublish: boolean;                   // small-business mode §5.9, default false
  draftRevisionBase: ID | null;           // published revision the draft started from
  publishedRevisionId: ID | null;
  templateApplicationIds: ID[];
  routeOptimizationEnabled: boolean;      // mirrored from Toolkit (03 owns the toggle) [S: T02]
  includeGlobalChecklistDefault: boolean;
}

interface PricingTemplate {               // platform-level, not tenant data
  id: ID; industryKey: string;            // 'auto_detailing_mobile', 'house_cleaning', ...
  version: number; name: string; regionPrices: Record<CurrencyCode, 'native' | 'converted'>;
  payload: TemplatePayload;               // categories/services/addons/pairings/questions/checklists
  publishedAt: ISODateTime;
}

interface PricingTemplateApplication {
  id: ID; businessId: ID; templateId: ID; templateVersion: number;
  mode: 'signup' | 'add_alongside' | 'replace';
  appliedBy: ID; appliedAt: ISODateTime;
  createdEntityIds: ID[];                 // for undo/report
}

interface ServiceCategory {
  id: ID; businessId: ID;
  name: string; description?: string;
  iconRef: IconRef;                       // [S: T06]
  restriction: 'any' | 'mobile_only' | 'shop_only';   // mobile_only [S: T06]
  visibleOnline: boolean;
  sortOrder: number;                      // fractional index string preferred for concurrent reorder [I]
  status: LifecycleStatus; hasUnpublishedChanges: boolean;
  version: number;
}

interface IconRef { kind: 'builtin' | 'upload'; key: string; /* builtin name or asset id */ }

interface Service {
  id: ID; businessId: ID; categoryId: ID;
  type: 'standard' | 'route';             // [S: T02]
  name: string; descriptionRich?: string; imageAssetId?: ID;
  pricingMode: 'fixed' | 'starting_at' | 'hourly' | 'quote_required';
  price: Cents | null;                    // null only for quote_required
  hourlyRate?: Cents;
  cost: Cents;                            // [S: T06]
  duration: Seconds;                      // [S: T06]
  bufferAfter: Seconds;                   // [I]
  maxPerDay?: number;                     // [I]
  fulfillment: Fulfillment[];             // [S: T06] mobile only; shop [S: T11]
  taxable: boolean;
  visibleOnline: boolean; allowViaLinkWhenHidden: boolean;
  requiredAssetType?: 'vehicle' | 'property' | 'equipment' | null;   // [I] ties to 07
  variationGroups: VariationGroup[];      // [I]
  checklist: ServiceChecklistConfig;      // [S: T06]
  questions: ServiceQuestionConfig;       // [S: T06]
  recurring: RecurringOptions;            // [S: T06]
  shortLinkSlug: string;                  // [S: T06]
  zoneScope: 'all' | 'selected';          // [I]
  zoneIds?: ID[];                         // when selected
  shopScope: 'all' | 'selected'; shopIds?: ID[];
  internalNotes?: string;
  duplicatedFromId?: ID;
  status: LifecycleStatus; hidden: boolean; hasUnpublishedChanges: boolean;
  sortOrder: number; version: number;
  createdVia: 'editor' | 'template' | 'crm_quick_create' | 'ai_import' | 'duplicate';
}

interface VariationGroup {                // [I]
  id: ID; name: string; required: boolean; sortOrder: number;
  kind: 'single_choice' | 'quantity';
  options?: VariationOption[];            // single_choice
  priceMode?: 'replace_base' | 'add_to_base';
  unit?: { label: string; unitPrice: MilliCents; unitCost: MilliCents; unitDuration: Seconds;
           includedUnits: number; minUnits: number; maxUnits: number; step: number; };
  assetAttribute?: string;                // e.g. 'vehicle.sizeClass', 'property.bedrooms'
}
interface VariationOption {
  id: ID; label: string; price: Cents; costDelta: Cents; durationDelta: Seconds;
  isDefault: boolean; assetValues?: string[]; sortOrder: number;
}

interface ServiceChecklistConfig {
  includeGlobal: boolean;                 // [I]
  items: ChecklistItem[];                 // [S: T06]
  beforePhotosRequired: number;           // [S: T06]
  afterPhotosRequired: number;            // [S: T06]
  photoInstructions?: string;
  blockCompletionUntilSatisfied: boolean; // [I]
}
interface ChecklistItem {
  id: ID; label: string; kind: 'checkbox' | 'question';
  answerType?: 'text' | 'number' | 'yes_no' | 'single_choice' | 'multi_choice';
  options?: string[]; required: boolean;
  stage: 'before_start' | 'during' | 'before_complete';   // [I]
  sortOrder: number;
}

interface ServiceQuestionConfig {
  includeGlobal: boolean;                 // [S: T06] "turn off the global questions"
  items: BookingQuestion[];
}
interface BookingQuestion {
  id: ID; prompt: string; helpText?: string;
  answerType: 'short_text' | 'long_text' | 'number' | 'yes_no' | 'single_choice' | 'multi_choice' | 'date' | 'photo';
  options?: { id: ID; label: string; priceDelta?: Cents; durationDelta?: Seconds }[];
  required: boolean; internalLabel?: string;
  showIf?: { questionId: ID; equals: string | string[] };   // [I]
  sortOrder: number;
}

interface GlobalChecklist {               // one per business per scope [S: T06]
  id: ID; businessId: ID;
  appliesTo: { mode: 'all' | 'mobile' | 'shop' | 'categories'; categoryIds?: ID[] };
  items: ChecklistItem[]; beforePhotosRequired: number; afterPhotosRequired: number;
  status: LifecycleStatus; version: number;
}
interface GlobalBookingQuestions {        // data owned here, screen may be 01's [S: T05][S: T06]
  id: ID; businessId: ID; items: BookingQuestion[];
  appliesTo: { mode: 'all' | 'mobile' | 'shop' | 'categories'; categoryIds?: ID[] };
  version: number;
}

interface RecurringOptions {              // [S: T06][S: T14]
  enabledOnline: boolean;
  allowOneTime: boolean;
  frequencies: RecurringFrequencyOption[];
  customFrequency?: { enabled: boolean; unit: 'day' | 'week' | 'month'; minInterval: number; maxInterval: number };
  defaultEnd: { kind: 'never' } | { kind: 'after_count'; count: number } | { kind: 'on_date_offset_days'; days: number };
  discountBase: 'service_only' | 'service_and_addons';
}
interface RecurringFrequencyOption {
  id: ID; label: string; unit: 'day' | 'week' | 'month'; interval: number;
  discount: { kind: 'none' } | { kind: 'percent'; bps: Bps } | { kind: 'fixed'; amount: Cents };
  highlighted: boolean; sortOrder: number;
}

interface AddOnCategory {                 // [S: T07] name + image
  id: ID; businessId: ID; audience: 'customer' | 'company';
  name: string; imageAssetId?: ID; sortOrder: number;
  status: LifecycleStatus; hasUnpublishedChanges: boolean; version: number;
}

interface AddOn {                         // [S: T07]
  id: ID; businessId: ID; categoryId: ID;
  audience: 'customer' | 'company';       // company = internal only [S: T07]
  kind: 'service' | 'product' | 'fee';    // fee [I]
  name: string; description?: string; imageAssetId?: ID;
  price: Cents; cost: Cents;
  priceEditableAtPos: boolean;            // [I] company add-ons
  extraDuration: Seconds;                 // [S: T07]
  durationScalesWithQty: boolean;
  quantity: { selectable: boolean; min: number; max: number };   // D4 [I]
  maxPerBooking?: number;
  taxable: boolean;
  fulfillment: Fulfillment[];             // [I] default both
  visibleOnline: boolean;                 // forced false for company
  autoAddForPairedServices: boolean;      // [I] company fees
  pairWithFutureServices: boolean;        // [I]
  inventoryItemId?: ID;                   // when kind=product and tracked
  status: LifecycleStatus; hidden: boolean; hasUnpublishedChanges: boolean;
  sortOrder: number; version: number;
}

interface AddOnPairing {                  // explicit rows = enabled pairs [S: T07]
  businessId: ID; addOnId: ID; serviceId: ID; enabled: boolean;
  // PK (addOnId, serviceId). Absence + addOn.pairWithFutureServices handled at service creation.
}

interface InventoryItem {                 // [S: T07] part numbers, stock, min/max
  id: ID; businessId: ID; addOnId: ID;
  sku?: string;                           // part number
  trackStock: boolean;
  onHand: number; reserved: number;       // available = onHand - reserved
  reorderPoint: number;                   // "min" (D4)
  parLevel?: number;                      // "max" (D4)
  outOfStockBehavior: 'hide_online' | 'show_sold_out' | 'allow_backorder';
  lowAlertArmed: boolean;
  version: number;
}
interface StockMovement {                 // append-only ledger [I]
  id: ID; businessId: ID; inventoryItemId: ID;
  type: 'receive' | 'adjust' | 'reserve' | 'release' | 'consume' | 'return' | 'count';
  qtyDelta: number; reservedDelta: number;
  jobId?: ID; jobLineId?: ID; reason?: string;
  actorId?: ID; idempotencyKey: string;   // unique per (businessId, idempotencyKey)
  at: ISODateTime;
}

interface ShortLink {                     // [S: T06]
  businessId: ID; slug: string; targetKind: 'service'; targetId: ID;
  active: boolean; createdAt: ISODateTime;
  // PK (businessId, slug); old slugs retained with active=true → redirect to current slug
}

interface CatalogRevision {               // immutable snapshot [I]
  id: ID; businessId: ID; number: number;   // monotonic per business
  status: 'building' | 'ready' | 'live' | 'superseded' | 'rejected';
  contentHash: string;                    // sha256 of canonical JSON
  publicSnapshot: PublicCatalogSnapshot;  // customer-visible (no cost, no company add-ons, no internal notes)
  internalSnapshot: InternalCatalogSnapshot; // full, for CRM/worker app
  diffFromPrevious: CatalogDiff;
  publishedBy: ID; publishedAt?: ISODateTime; rollbackOf?: ID;
}

interface ImageAsset { id: ID; businessId: ID; variants: { w: number; h: number; url: string; format: 'webp' | 'avif' | 'jpg' }[]; blurhash: string; width: number; height: number; }

// ------------------------- Coverage -------------------------

interface ServiceArea {                   // "zone" [S: T11]
  id: ID; businessId: ID;
  name: string; color: string; iconRef?: IconRef;          // icon [S: T11]
  isDefault: boolean;                     // created at signup [S: T11]
  center: { lat: number; lng: number; label: string; placeId?: string };   // [S: T11]
  generation?: {                          // [S: T11] drive-time
    maxTravelMinutes: number; traffic: 'typical' | 'none';
    avoid: ('tolls' | 'highways' | 'ferries')[];
    provider: string; generatedAt: ISODateTime;
  };
  geometry: GeoJSON.MultiPolygon;         // current
  geometrySource: 'isochrone' | 'manual' | 'circle_fallback' | 'imported';
  geometryVersionId: ID;
  bbox: [number, number, number, number]; // minLng, minLat, maxLng, maxLat (prefilter)
  areaKm2: number;
  timezone: string;                       // IANA, derived from center [I]
  priority: number;                       // lower = wins overlap [I]
  travelCharge?: {                        // [S: T11]
    name: string; amount: Cents; mode: 'per_visit' | 'per_mobile_service';
    taxable: boolean; waiveAboveSubtotal?: Cents; exemptRouteServices: boolean;
  };
  taxRates: EffectiveTaxRate[];           // custom tax [S: T11]; empty = inherit
  acceptOnlineBookings: boolean;
  serviceScope: 'all' | 'selected'; serviceIds?: ID[];
  status: 'active' | 'disabled' | 'archived';
  version: number;
}
interface EffectiveTaxRate { label: string; ratePpm: Ppm; effectiveFrom: ISODate; components?: { label: string; ratePpm: Ppm }[] }

interface ZoneGeometryVersion {           // history for audit/rollback [I]
  id: ID; zoneId: ID; geometry: GeoJSON.MultiPolygon; source: ServiceArea['geometrySource'];
  createdBy: ID; createdAt: ISODateTime;
}

interface Shop {                          // [S: T11]
  id: ID; businessId: ID; name: string;
  address: PostalAddress; location: { lat: number; lng: number }; timezone: string;
  iconRef?: IconRef;                      // [S: T11]
  distanceFilter?: { enabled: boolean; metric: 'straight_line_km' | 'drive_minutes'; max: number };  // [S: T11]
  taxRates: EffectiveTaxRate[];           // [I] empty = inherit
  serviceScope: 'all' | 'selected'; serviceIds?: ID[];
  phone?: string; photoAssetId?: ID; arrivalInstructions?: string;
  acceptOnlineBookings: boolean;
  status: 'active' | 'disabled' | 'archived'; sortOrder: number; version: number;
}

interface WorkerZoneAssignment {          // join owned by 02; edited from 02 zone screens and 09 worker profile [S: T11][S: T13]
  businessId: ID; workerId: ID; zoneId: ID; assignedBy: ID; assignedAt: ISODateTime;
}
interface WorkerShopAssignment {          // [S: T11] "assign workers to your shop in the people tab"
  businessId: ID; workerId: ID; shopId: ID; assignedBy: ID; assignedAt: ISODateTime;
}
// NOTE: WorkerServiceSkill (worker ↔ allowed services, [S: T13]) is OWNED BY AGENT 09.

interface GeocodeCacheEntry {             // [I]
  key: string;                            // normalised address hash (tenant-agnostic)
  lat: number; lng: number; precision: 'rooftop' | 'interpolated' | 'street' | 'postal' | 'locality';
  components: PostalAddress; provider: string; fetchedAt: ISODateTime; ttlDays: number;
}

interface CoverageResolution {            // returned, optionally stored on job [I]
  point: { lat: number; lng: number }; precision: GeocodeCacheEntry['precision'];
  covered: boolean;
  primaryZoneId?: ID; containingZoneIds: ID[];
  travelCharge?: { name: string; amount: Cents; mode: 'per_visit' | 'per_mobile_service'; taxable: boolean };
  taxRatePpm: Ppm | null; taxLabel?: string; taxSource: 'zone' | 'shop' | 'business_default' | 'none';
  eligibleWorkerIds: ID[];                // zone-staff ∪ ... (before skills/availability) 
  eligibleServiceIds: ID[];
  nearestZone?: { zoneId: ID; distanceKm: number };       // when not covered
  shopsInRange: { shopId: ID; distanceKm: number; driveMinutes?: number }[];
  resolvedAt: ISODateTime; catalogRevisionId: ID; coverageVersion: number;
}

interface CoverageOverride {              // stored on the job by 03/04 [S: T11]
  by: ID; at: ISODateTime; reason?: string; nearestZoneId?: ID; distanceOutsideKm?: number;
}
```

### 7.1 Relations

- `Business 1—N ServiceCategory 1—N Service`; `Service 1—N VariationGroup 1—N VariationOption` (embedded).
- `Business 1—N AddOnCategory 1—N AddOn`; `AddOn N—M Service` via `AddOnPairing`.
- `AddOn 1—0..1 InventoryItem 1—N StockMovement`.
- `Service 1—N ShortLink` (current + historical slugs).
- `Business 1—N CatalogRevision`; `Business.publishedRevisionId → CatalogRevision`.
- `Business 1—N ServiceArea 1—N ZoneGeometryVersion`; `ServiceArea N—M Worker` via `WorkerZoneAssignment`; `Shop N—M Worker` via `WorkerShopAssignment`.
- `Service N—M ServiceArea` via `zoneScope/zoneIds` (sparse; default all) and `Service N—M Shop` via `shopScope/shopIds`.
- External references INTO this module (must remain resolvable forever; hence archive, not delete): job lines (04), quotes/invoices (04/05), recurring series (03), subscription packages (06), discount codes (06), worker skills (09), reports (08), leads with desired service (07).

### 7.2 Indexes `[I]`

- `(businessId, status, sortOrder)` on categories/services/add-ons.
- `(businessId, slug)` unique on short links.
- `(businessId, sku)` unique partial (where sku not null) on inventory.
- Spatial index on `ServiceArea.geometry` (R-tree/GiST or bbox columns) filtered by `businessId, status`.
- `(businessId, workerId)` and `(businessId, zoneId)` on assignments.
- `(businessId, idempotencyKey)` unique on stock movements.

### 7.3 Public snapshot shape (what the booking site downloads) `[I]`

```ts
interface PublicCatalogSnapshot {
  revision: number; currency: CurrencyCode; pricesIncludeTax: boolean;
  categories: { id: ID; name: string; description?: string; icon: string; restriction: 'any'|'mobile_only'|'shop_only'; serviceIds: ID[] }[];
  services: {
    id: ID; categoryId: ID; type: 'standard'|'route'; name: string; description?: string; image?: ImageVariantSet;
    pricingMode: Service['pricingMode']; price: Cents | null; duration: Seconds; fulfillment: Fulfillment[];
    variationGroups: Omit<VariationGroup, 'unit'> & { unit?: Omit<NonNullable<VariationGroup['unit']>, 'unitCost'> }[];
    questions: BookingQuestion[];                // merged with globals already
    recurring: RecurringOptions | null;          // null when not enabled online
    addOnIds: ID[]; slug: string; requiredAssetType?: string;
    zoneScope: 'all'|'selected'; zoneIds?: ID[]; shopScope: 'all'|'selected'; shopIds?: ID[];
  }[];
  addOnCategories: { id: ID; name: string; image?: ImageVariantSet }[];
  addOns: { id: ID; categoryId: ID; name: string; description?: string; image?: ImageVariantSet; price: Cents;
            extraDuration: Seconds; quantity: AddOn['quantity']; soldOut: boolean; fulfillment: Fulfillment[] }[];
  shops: { id: ID; name: string; address: PostalAddress; location: LatLng; icon?: string; distanceFilter?: Shop['distanceFilter'] }[];
  zonesForMap?: { id: ID; name: string; icon?: string; color: string; simplifiedGeometry: GeoJSON.MultiPolygon }[]; // display only, coarse
}
```
- **Never** in public snapshot: `cost`, company add-ons, internal notes, stock counts (only `soldOut`), worker IDs, tax rates (resolved per address via API), full-resolution zone polygons (only simplified display shapes, opt-in) `[I]`.
- Size budget: ≤ 30 KB gzipped for a typical 40-service catalog; images referenced by URL, never inlined `[I]` (BRIEF performance bar).

---

## 8. API surface

Transport-agnostic command/query names. Auth: `staff(<permission>)` = authenticated staff with permission (Agent 09 RBAC); `public(business)` = anonymous booking-site/Bookings-MCP scoped to one business via host header or key; `system` = internal service identity. All mutating commands accept `idempotencyKey` and `expectedVersion` (optimistic concurrency) and return the new `version` `[I]`. Errors are typed (`VALIDATION_FAILED{fieldErrors}`, `VERSION_CONFLICT{current}`, `NOT_FOUND`, `FORBIDDEN`, `REFERENCED{refs}`, `PROVIDER_UNAVAILABLE`, `OUT_OF_STOCK`).

### 8.1 Catalog — admin

| Name | Input | Output | Auth |
|---|---|---|---|
| `catalog.listTemplates` | `{country?}` | `TemplateSummary[]` | staff(any) |
| `catalog.previewTemplate` | `{templateId, currency}` | `TemplatePayload` (priced) | staff(any) |
| `catalog.applyTemplate` | `{templateId, mode, include:{addons, questions, checklists}}` | `{applicationId, counts}` | staff(`catalog.apply_template`) |
| `catalog.getDraft` | `{}` | full draft tree + `hasUnpublishedChanges` | staff(`catalog.edit_draft`) |
| `catalog.getPublished` | `{revision?: number}` | `InternalCatalogSnapshot` (cost stripped if no `read_cost`) | staff(`catalog.read_published`) |
| `catalog.createCategory` / `updateCategory` / `archiveCategory` / `deleteCategory` / `reorderCategories` | entity fields / `{ids, order}` | entity | staff(`catalog.edit_draft`) (archive/delete: `catalog.archive_delete`) |
| `catalog.createService` / `updateService` (patch) | `ServiceInput` | `Service` | staff(`catalog.edit_draft`) |
| `catalog.createServiceQuick` | `{name, price, duration, fulfillment, categoryId?, saveForLater: boolean}` | `{serviceId?} \| {oneOffLine}` | staff(booking create, 03) `[S: T15]` `[S: T19]` |
| `catalog.duplicateService` | `{serviceId, newName?, categoryId?, copy:{...}}` | `Service` | staff(`catalog.edit_draft`) `[S: T06]` |
| `catalog.archiveService` | `{serviceId, replacementServiceId?, keepForExisting?: boolean}` | `{archived, affected:{series, subscriptions, discounts}}` | staff(`catalog.archive_delete`) |
| `catalog.restoreService` | `{serviceId}` | `Service` | staff(`catalog.archive_delete`) |
| `catalog.reorderServices` | `{categoryId, ids}` | ok | staff(`catalog.edit_draft`) |
| `catalog.setServiceSlug` | `{serviceId, slug}` | `{slug, url}` | staff(`catalog.edit_draft`) |
| `catalog.updateGlobalChecklist` / `updateGlobalQuestions` | items | entity | staff(`catalog.edit_draft`) |
| `catalog.createAddOnCategory` / `update…` / `archive…` | | | staff(`catalog.edit_draft`) |
| `catalog.createAddOn` / `updateAddOn` / `archiveAddOn` | `AddOnInput` | `AddOn` | staff(`catalog.edit_draft`) |
| `catalog.setPairings` | `{addOnId, serviceIds: 'all' \| ID[], pairWithFutureServices}` | pairing summary | staff(`catalog.edit_draft`) |
| `catalog.previewPublish` | `{}` | `{diff, errors[], warnings[], affectedRecurringSeries, affectedFutureJobs}` | staff(`catalog.publish`) |
| `catalog.publish` | `{expectedDraftHash, applyPriceToFutureRecurring?: boolean}` | `{revisionId, number}` | staff(`catalog.publish`) |
| `catalog.discardDraft` | `{entityIds?}` | ok | staff(`catalog.edit_draft`) |
| `catalog.rollbackToRevision` | `{revisionId}` | `{revisionId}` (new) | staff(`catalog.publish`) |
| `catalog.importFromWebsite` | `{url} \| {fileId}` | `{importId}` (async) → draft proposals | staff(`catalog.apply_template`) `[S: llms]` |
| `catalog.exportCsv` / `importCsv` | — / file | file / report | staff(`catalog.edit_draft`) `[I]` |

### 8.2 Catalog — runtime (used by other modules)

| Name | Input | Output | Auth |
|---|---|---|---|
| `catalog.priceSelection` | `{revisionId?, lines:[{serviceId, variationSelections, units, questionAnswers}], addOns:[{addOnId, qty, attachToServiceId?}], fulfillment, location: LatLng \| {shopId}, recurringFrequencyId?, customerId?}` | `{lines: PricedLine[], travelLine?: PricedLine, taxRatePpm, taxSource, recurringDiscount?, totalWorkDuration, blockedDuration, revisionId, warnings}` — **no totals** | staff / public(business) (cost omitted for public) / system |
| `catalog.getServiceForJob` | `{serviceId, revisionId}` | checklist (merged), photo counts, worker questions, booking questions, duration | system (04) |
| `catalog.listAddOnsForJob` | `{jobId}` or `{serviceIds, audience: 'customer'\|'company'\|'both'}` | add-ons paired + stock state | staff(`job.add_addon`) `[S: T07]` |
| `catalog.resolveShortLink` | `{host, slug}` | `{serviceId, categoryId, active, redirectSlug?}` | public(business) |
| `catalog.getPublicSnapshot` | `{host}` (ETag) | `PublicCatalogSnapshot` (cacheable, `Cache-Control: public, max-age=60, stale-while-revalidate=86400`, purged on publish) | public(business) |

```ts
interface PricedLine {
  kind: 'service' | 'addon' | 'company_addon' | 'travel_fee' | 'question_surcharge';
  refId: ID; label: string; detail?: string;          // e.g. "SUV/Crossover"
  qty: number; unitPrice: Cents; amount: Cents;       // amount = pre-discount line amount
  cost?: Cents;                                       // omitted for public callers
  duration: Seconds; taxable: boolean; sku?: string;
  catalogRevisionId: ID;
}
```

### 8.3 Inventory

| Name | Input | Output | Auth |
|---|---|---|---|
| `inventory.list` | filters `{status?, audience?}` | `InventoryRow[]` | staff(`catalog.read_published`) |
| `inventory.adjust` | `{itemId, qtyDelta, type, reason, idempotencyKey}` | `{onHand, reserved, status}` | staff(`inventory.adjust`) |
| `inventory.reserve` / `release` / `consume` | `{jobId, jobLineId, itemId, qty, idempotencyKey}` | `{ok} \| OUT_OF_STOCK{available}` | system (driven by 04 events) |
| `inventory.movements` | `{itemId, cursor}` | `StockMovement[]` | staff(`inventory.adjust`) |
| `inventory.exportCsv` / `importCounts` | | | staff(`inventory.adjust`) |

### 8.4 Coverage — admin

| Name | Input | Output | Auth |
|---|---|---|---|
| `coverage.listZones` / `listShops` | `{includeArchived?}` | entities | staff(any) |
| `coverage.generateIsochrone` | `{center: LatLng \| address, maxTravelMinutes, traffic, avoid}` | `{geometry, areaKm2, provider}` | staff(`coverage.edit`) (rate-limited 20/min/business) `[S: T11]` |
| `coverage.validateGeometry` | `{geometry}` | `{valid, errors[], repaired?}` | staff(`coverage.edit`) |
| `coverage.previewZoneChange` | `{zoneId?, patch}` | `{futureJobsNowUncovered, futureJobsFeeChanged, futureJobsTaxChanged, overlaps:[zoneId]}` | staff(`coverage.edit`) |
| `coverage.createZone` / `updateZone` / `disableZone` / `enableZone` / `archiveZone` / `reorderZones` | | entity | staff(`coverage.edit`; fees/tax fields need `coverage.edit_fees_tax`) |
| `coverage.setZoneStaff` | `{zoneId, workerIds}` | assignments | staff(`coverage.assign_staff`) `[S: T11]` |
| `coverage.setWorkerZones` | `{workerId, zoneIds, shopIds}` | assignments | staff(`coverage.assign_staff`) — called by 09 worker profile `[S: T13]` |
| `coverage.createShop` / `updateShop` / `archiveShop` | | entity | staff(`coverage.edit`) `[S: T11]` |
| `coverage.setShopStaff` | `{shopId, workerIds}` | assignments | staff(`coverage.assign_staff`) |

### 8.5 Coverage — runtime

| Name | Input | Output | Auth |
|---|---|---|---|
| `coverage.geocode` | `{address}` | `{candidates[{label, lat, lng, precision}]}` | public(business) (rate-limited per IP) / staff |
| `coverage.resolveLocation` | `{location: LatLng \| address, serviceIds?, fulfillment: 'mobile', at?: ISODate}` | `CoverageResolution` | public(business) (eligibleWorkerIds omitted) / staff / system |
| `coverage.listShopsForCustomer` | `{location, serviceIds?}` | shops in range sorted by distance | public(business) `[S: T11]` |
| `coverage.resolveShop` | `{shopId, serviceIds?, at?}` | `{taxRatePpm, taxSource, eligibleWorkerIds, timezone}` | staff/system |
| `coverage.zonesForWorker` | `{workerId}` | zones + shops | staff/system (03, 09) |
| `coverage.workersForPoint` | `{location}` | worker IDs (zone staff union) | system (03) |

### 8.6 MCP mapping `[I]`

`[S: llms]` CRM MCP (business-level) and Bookings MCP (customer-level). Expose: Bookings MCP → `getPublicSnapshot`, `resolveShortLink`, `resolveLocation` (public form), `listShopsForCustomer`, `priceSelection` (public). CRM MCP → all §8.1–8.5 staff commands with the calling user's permissions; destructive commands (`archive*`, `applyTemplate replace`, `publish`) require an explicit `confirm: true` argument.

---

## 9. Interfaces with other modules

Event envelope `[I]` (Agent 10 finalises): `{eventId, type, businessId, occurredAt, actor:{kind, id}, entityId, version, payload, causationId?, correlationId?}`; at-least-once delivery, consumers idempotent on `eventId`.

### 9.1 PRODUCES — events

| Event | Payload (key fields) | Consumers |
|---|---|---|
| `catalog.template.applied` | templateId, version, mode, counts | 10, 08 (onboarding checklist) |
| `catalog.category.created` / `.updated` / `.archived` | categoryId, changedFields, status | 01 (via revision), 10 |
| `catalog.service.created` | serviceId, categoryId, type, status, createdVia, duplicatedFromId? | 09 (default worker skill assignment), 10 |
| `catalog.service.updated` | serviceId, changedFields[] | 03, 04, 09 |
| `catalog.service.price_changed` | serviceId, old/new {price, cost, duration}, revisionId | 03 (recurring series repricing choice), 06 (package value recalcs), 08 (reporting), 05 |
| `catalog.service.published` | serviceId, revisionId | 01, 09 |
| `catalog.service.archived` | serviceId, replacementServiceId? | 03 (future recurring), 06 (packages/discounts), 09 (skills), 07 (leads' desired service) |
| `catalog.service.replaced` | fromServiceId, toServiceId, scope | 03, 06 |
| `catalog.service.deleted` | serviceId (draft-only) | 09 |
| `catalog.addon.created` / `.updated` / `.archived` | addOnId, audience, kind | 06 (discounts/subscription free add-on), 04 |
| `catalog.addon_pairing.changed` | addOnId, addedServiceIds, removedServiceIds | 01 (via revision), 04 |
| `catalog.revision.published` | revisionId, number, diff summary, publicSnapshotHash | 01 (cache bust), 03, 04, 06, 08, 10, worker app sync |
| `catalog.draft.discarded` | entityIds | — (audit) |
| `catalog.import.completed` | importId, proposedCounts | 08 (onboarding) |
| `inventory.stock.adjusted` | itemId, delta, type, onHand, reserved | 08 (reporting) |
| `inventory.stock.low` | itemId, sku, onHand, reorderPoint, parLevel | **08** (admin notification "Inventory low") |
| `inventory.stock.depleted` | itemId | 08, 01 (via next snapshot `soldOut`) |
| `inventory.stock.restored` | itemId | 01 |
| `coverage.zone.created` / `.updated` / `.disabled` / `.enabled` / `.archived` | zoneId, changedFields | 03, 01, 09, 08 (reporting dims: "service areas" `[S: T09]`) |
| `coverage.zone.geometry_changed` | zoneId, geometryVersionId, addedAreaKm2, removedAreaKm2, futureJobsUncovered[] | 03 (re-evaluate eligibility for unassigned future jobs), 08 |
| `coverage.zone.staff_assigned` / `.staff_unassigned` | zoneId, workerIds | 03, 09 |
| `coverage.travel_charge.changed` | zoneId, old, new | 05 (only if owner chose to update unpaid future jobs), 01 |
| `coverage.tax_rate.changed` | scope (zone/shop/business), id, old, new, effectiveFrom | 05 |
| `coverage.shop.created` / `.updated` / `.archived` | shopId | 01, 03, 09 |
| `coverage.shop.staff_assigned` / `.staff_unassigned` | shopId, workerIds | 03, 09 |
| `coverage.location.out_of_area_attempted` | point (rounded to ~100 m for privacy), address hash, nearestZoneId, distanceKm, source (booking_site/crm), sessionId? | 07 (lead capture), 08 (demand heatmap — Pro feature per `[S: llms]` "heatmaps") |
| `coverage.override.used` | jobId, by, nearestZoneId, distanceOutsideKm | 08, 10 (audit) |

### 9.2 CONSUMES — events

| Event | From | Our reaction |
|---|---|---|
| `account.business.created` (incl. signup city, industry, templateId?, country) | 10/platform (signup owner TBD) | W1 seeding |
| `toolkit.route_optimization.toggled` | 03 | update `routeOptimizationEnabled`; if turned off with published route services → publish validation warns; route services hidden online `[I]` |
| `booking.job.created` / `booking.job.updated` / `booking.job.cancelled` | 03/04 | inventory reserve/adjust/release for product lines |
| `job.line.added` / `job.line.removed` / `job.line.qty_changed` | 04 | inventory reserve/release (W14) |
| `job.job.completed` | 04 | inventory consume |
| `job.job.reopened` | 04 | inventory reverse-consume (`return` movement) `[I]` |
| `team.worker.created` | 09 | none automatically; zone/shop assignment UI offered `[S: T11]` |
| `team.worker.deactivated` / `.deleted` | 09 | end zone/shop assignments; warn zones left with 0 staff |
| `team.worker.skills_changed` | 09 | recompute "no worker can perform" warnings |
| `customer.asset.updated` | 07 | none stored; variation auto-selection is evaluated at pricing time from asset attributes `[I]` |
| `subscription.package.updated` / `discount.code.updated` | 06 | maintain reverse-reference index (blocks unsafe archive) |

### 9.3 Contracts per module

**Agent 01 — Booking Portal (reads catalog & coverage)**
- 01 fetches `catalog.getPublicSnapshot` (ETag/CDN) once per session; categories render as pages in `sortOrder` `[S: T06]`; category/service icons and images from snapshot `[S: T06]`.
- Address step → `coverage.resolveLocation`; 01 filters services to `eligibleServiceIds` and shows `travelCharge` as a visible line before checkout (no surprise fees) `[I]`.
- Shop flow → `coverage.listShopsForCustomer` (distance filter applied server-side `[S: T11]`), zone/shop booking-page icons `[S: T11]`.
- Service step → variation groups, booking questions (already merged with globals per `includeGlobal`) `[S: T06]`, recurring frequency picker when `recurring != null` `[S: T06]`.
- Add-on step → `addOnIds` per service, union over cart `[S: T07]`; `soldOut` handling.
- Short links `/s/{slug}` → `catalog.resolveShortLink` → deep-link `[S: T06]`.
- Route services → day picker, not times; label from 01's "Routes text" setting `[S: T02]`.
- Price display/preview → `catalog.priceSelection` (public) — 01 must not compute prices itself; Agent 05 computes the authoritative total at checkout.
- Out-of-area → message + lead capture (07) using `coverage.location.out_of_area_attempted`.
- Preview-draft mode: 01 accepts a signed `previewToken` that switches the snapshot source to draft.

**Agent 03 — Scheduling & Dispatch**
- Durations: `PricedLine.duration`, `totalWorkDuration`, `blockedDuration` (incl. `bufferAfter`) `[S: T06]` `[S: T07]`.
- `service.type` (`route` → day-based route capacity, no time slot) `[S: T02]`; `maxPerDay` `[I]`.
- Candidate worker pool for mobile: `coverage.workersForPoint` (zone staff union) ∩ 09 skills ∩ 03 availability `[S: T11]` `[S: T13]`; for shop: shop staff.
- Zone `timezone` for jobs in zones far from HQ `[I]`; shop `timezone`.
- Recurring options (frequency list, discount spec, end defaults) `[S: T06]` `[S: T14]`.
- Zone filter on calendar (`[S: T08]` "sort by ... service areas") → 03 stores `zoneId`/`shopId` on each job from `CoverageResolution`.
- Out-of-area override: 03 calls `resolveLocation`; on `covered=false` and permission → stores `CoverageOverride` `[S: T11]`.
- 03 must handle `catalog.service.archived` / `replaced` for future recurring occurrences and `coverage.zone.geometry_changed` for future unassigned jobs.

**Agent 04 — Job Lifecycle & Field Workflow**
- At job creation 04 calls `catalog.getServiceForJob` and snapshots: merged checklist (global first, then service items, de-duplicated by label) `[S: T06]`, before/after photo counts (max of global and service when both apply) `[I]`, worker questions, `blockCompletionUntilSatisfied`.
- Customer question answers + prompt text stored on job.
- Add-ons after booking (CRM and worker POS) via `catalog.listAddOnsForJob`; must emit `job.line.*` events for inventory `[S: T07]`.
- Quote-required services create a quote (single job ID across quote→invoice `[S: T04]`).
- Route jobs follow normal job workflow (photos, checklists) `[S: T02]`.

**Agent 05 — Payments & Money**
- Receives `PricedLine[]` (service, add-on, company add-on, travel fee, question surcharge) with `amount`, `cost`, `taxable`; `taxRatePpm` + `taxSource`; `recurringDiscount` spec; `pricesIncludeTax`.
- 05 owns: discount order, tax computation & rounding (per line vs per invoice), tips, deposits, refunds, totals. 02 never totals.
- Tax override on a job ("apply tax" `[S: T16]`) is 05's field; 02's precedence puts it above zone rate.
- If owner opts to "update unpaid future jobs" on fee/tax change, 05 recomputes those jobs on `coverage.travel_charge.changed` / `coverage.tax_rate.changed` with `applyToJobIds`.

**Agent 06 — Subscriptions, Discounts & Gift Cards**
- Packages include services by `serviceId`, may give % off services and free add-ons (e.g. "20% off all services and one free add-on each month" `[S: T01]`); discount codes tie to services/add-ons (`[S: T09]` "Each discount can be tied to services, add ons").
- 06 must reference **IDs** (not names), listen to `catalog.service.archived/replaced`, `catalog.addon.archived`, and register references via `catalog.registerReference({kind, refId, serviceIds, addOnIds})` `[I]` so 02 can block unsafe archive.
- Recurring-booking discount (02, per service) ≠ subscription discount (06): non-stacking recommendation §5.13.

**Agent 07 — Customers, Leads & Assets**
- Asset attributes (vehicle size class, make/model → size class; property bedrooms/sq ft) used by `priceSelection` to auto-select variation options `[I]`; `[S: llms]` vehicle lookup by make/model/year/VIN supports deriving size class.
- `customer.taxExempt` flag consumed in tax resolution.
- Out-of-area attempts → leads (`coverage.location.out_of_area_attempted`).

**Agent 08 — Marketing, Comms & Reporting**
- Notification triggers fired by this module: `inventory.stock.low` (owner/admin), `inventory.stock.depleted`, optional `catalog.revision.published` (team app "services updated"). No customer-facing notifications originate here.
- Reporting dimensions: service, category, add-on, zone, shop (`[S: T09]` top performing services and service areas). Jobs must carry `serviceId`, `categoryId`, `zoneId`, `shopId` snapshots.
- Heatmaps (Pro `[S: llms]`) can use out-of-area attempts + job locations.

**Agent 09 — Team, Permissions & Timesheets**
- 09 owns `WorkerServiceSkill` (which services a worker can be assigned `[S: T13]`), default for new services (recommend: workers with "all services" flag auto-get new services) `[I]`.
- 09's worker profile shows zones & shops and calls `coverage.setWorkerZones` `[S: T11]` `[S: T13]`; 02 owns the join rows.
- 09 enforces the permission keys in §2.1.

**Agent 10 — Integrator**
- Owns final event envelope, signup event name, and conflict resolution of D1–D5.

**Agent 11 — Branding, Theming & Responsive Design System** (added per BRIEF owner requirements 2026-11-13)
- **Custom icons for categories, services, add-on categories, zones and shops are owned by Agent 11** (icon-set choice, SVG upload, sanitisation, light/dark variants, sizing). Agent 02 stores only an `IconRef` (`{kind: 'builtin'|'upload', key}`) pointing at Agent 11's icon registry; the icon pickers in §3.3, §3.11, §3.13 are Agent 11 components embedded in 02's screens. Validation rules quoted in §3 (≤ 256 KB, sanitised SVG) defer to 11's final rules.
- Category/service/add-on **images** remain 02 content assets (`ImageAsset`), but rendering (radius, shadows, aspect crops, dark-mode treatment) follows 11's tokens.
- Zone polygon fill colors on the booking-site map (`zonesForMap.color`) must be derived from the tenant palette with sufficient contrast in light and dark mode; 02 stores a palette **token name** (e.g. `zone.1`) by default, a raw hex only when the owner overrides `[I]`.
- Customer-facing labels that originate in the catalog are owner content (service names, question prompts); generic labels around them ("Travel fee", "Sold out", "Anytime {day}", out-of-area message) are copy-override keys owned by 11/01 — 02 supplies defaults only (travel charge name is owner content per `[S: T11]`).
- All 02 admin screens must meet 11's responsive breakpoints (desktop/tablet/phone); polygon editing on phones is view-only (§3.10) and that degradation must be documented in 11's surface matrix.

---

## 10. Algorithms / pseudo-code

### 10.1 Geocoding with cache and precision gate `[I]`

```ts
async function geocode(address: string, biasPoint?: LatLng): Promise<GeocodeResult[]> {
  const key = sha256(normalizeAddress(address));          // lowercase, collapse spaces, expand abbreviations ("St"→"street"); unit number kept
  const hit = await cache.get(key);
  if (hit && !expired(hit)) return [hit];
  const results = await withTimeout(provider.geocode(address, { bias: biasPoint, country: business.country }), 3000)
                   .catch(e => fallbackProvider ? fallbackProvider.geocode(address) : Promise.reject(PROVIDER_UNAVAILABLE));
  const ranked = results.sort(byPrecisionThenRelevance);
  if (ranked[0]) await cache.put(key, ranked[0], ttlDays = 90);
  return ranked.slice(0, 5);
}

function acceptableForCoverage(r: GeocodeResult, mode: 'mobile'|'shop_filter'): boolean {
  if (mode === 'mobile') return ['rooftop','interpolated','street'].includes(r.precision);
  return true;                                             // postal/locality is fine for "shops near me"
}
// If precision too low for mobile: booking site asks customer to drop/confirm a pin (01 UI); CRM shows warning but allows.
```

### 10.2 Isochrone generation `[I]`

```ts
async function generateZoneGeometry(center: LatLng, minutes: number, opts): Promise<MultiPolygon> {
  assert(minutes >= 5 && minutes <= 120);
  const idem = hash(center.lat.toFixed(5), center.lng.toFixed(5), minutes, opts.traffic, opts.avoid.sort());
  const cached = await isoCache.get(idem); if (cached) return cached;
  let poly: MultiPolygon;
  try {
    poly = await retry(() => withTimeout(isoProvider.isochrone({
              profile: 'driving', contourMinutes: minutes,
              departAt: opts.traffic === 'typical' ? nextWeekday(10, 0, zoneTz) : undefined,
              avoid: opts.avoid, denoise: 0.5, generalizeMeters: 50 }), 10_000),
           { attempts: 3, backoff: jittered(500), retryOn: [TIMEOUT, HTTP_5XX] });
  } catch {
    // Fallback: circle whose radius approximates average speed × time
    poly = circlePolygon(center, radiusKm = minutes * AVG_KMH(opts) / 60, segments = 64); // AVG_KMH ≈ 40 urban
    poly.meta = { source: 'circle_fallback' };
  }
  poly = removeHolesSmallerThan(poly, 0.05 /* km² */);     // drop islands of unreachable blocks
  poly = dropPartsSmallerThan(poly, 0.25 /* km² */, keepPartContaining = center);
  poly = simplifyTopologyPreserving(poly, toleranceMeters = 50);   // Douglas–Peucker variant that never self-intersects
  while (vertexCount(poly) > 2000) poly = simplifyTopologyPreserving(poly, tolerance *= 1.5);
  const v = validate(poly); if (!v.valid) poly = makeValid(poly);  // buffer(0) style repair
  await isoCache.put(idem, poly, ttlDays = 30);
  return poly;
}
```

### 10.3 Point → zone resolution with overlap rules `[I]`

```ts
function resolveZones(p: LatLng, ctx: { channel: 'online'|'crm', serviceIds?: ID[] }): { primary?: Zone; containing: Zone[] } {
  const candidates = zoneIndex(businessId)                 // in-memory per-business R-tree, rebuilt on coverage.zone.* events
      .query(p)                                            // bbox prefilter
      .filter(z => z.status === 'active' || (ctx.channel === 'crm' && z.status === 'disabled')) // §5.14: disabled zones still usable in CRM
      .filter(z => ctx.channel === 'crm' || z.acceptOnlineBookings)
      .filter(z => pointInMultiPolygon(p, z.geometry));    // boundary-inclusive
  const servingRequested = ctx.serviceIds
      ? candidates.filter(z => z.serviceScope === 'all' || ctx.serviceIds!.every(id => z.serviceIds!.includes(id)))
      : candidates;
  const pool = servingRequested.length ? servingRequested : [];
  pool.sort((a, b) => a.priority - b.priority              // 1. explicit priority (list order)
                   || a.areaKm2 - b.areaKm2                // 2. smaller (more specific) zone wins
                   || haversine(p, a.center) - haversine(p, b.center)   // 3. closer center
                   || a.id.localeCompare(b.id));           // 4. deterministic tie-break
  return { primary: pool[0], containing: candidates };
}

function pointInMultiPolygon(p, mp): boolean {
  for (const polygon of mp.coordinates) {
    const [outer, ...holes] = polygon;
    if (onBoundary(p, outer) ) return true;                // inclusive (epsilon 1e-9 deg)
    if (!rayCast(p, outer)) continue;
    if (holes.some(h => rayCast(p, h) && !onBoundary(p, h))) continue;  // inside a hole = outside (hole edge counts as inside zone)
    return true;
  }
  return false;
}
// Planar ray casting on lng/lat is acceptable at city scale (< 300 km); zones crossing the antimeridian are rejected at validation.
```

### 10.4 Full coverage resolution `[I]`

```ts
async function resolveLocation(input, caller): Promise<CoverageResolution> {
  const pt = input.location.lat ? input.location : (await geocode(input.location))[0] ?? throwErr('ADDRESS_NOT_FOUND');
  const { primary, containing } = resolveZones(pt, { channel: caller.channel, serviceIds: input.serviceIds });
  const covered = !!primary;
  const staff = unique(containing.flatMap(z => zoneStaff(z.id)));
  const eligibleWorkerIds = staff.length ? staff
        : (primary ? activeWorkers() /* zero-staff zone rule §5.14 */ : []);
  const eligibleServiceIds = publishedServices()
        .filter(s => s.fulfillment.includes('mobile') && !s.hidden)
        .filter(s => containing.some(z => z.serviceScope === 'all' || z.serviceIds!.includes(s.id)))
        .filter(s => s.zoneScope === 'all' || s.zoneIds!.some(id => containing.some(z => z.id === id)));
  const tax = resolveTaxRate({ fulfillment: 'mobile', zone: primary, at: input.at ?? todayIn(primary?.timezone) });
  const res: CoverageResolution = {
    point: pt, precision: pt.precision ?? 'rooftop', covered,
    primaryZoneId: primary?.id, containingZoneIds: containing.map(z => z.id),
    travelCharge: primary?.travelCharge && primary.travelCharge.amount > 0 ? pick(primary.travelCharge) : undefined,
    taxRatePpm: tax.ratePpm, taxLabel: tax.label, taxSource: tax.source,
    eligibleWorkerIds: caller.isPublic ? [] : eligibleWorkerIds,
    eligibleServiceIds,
    nearestZone: covered ? undefined : nearestZone(pt),      // min distance from point to polygon edges, km
    shopsInRange: shopsInRange(pt, input.serviceIds),
    resolvedAt: now(), catalogRevisionId: publishedRevisionId(), coverageVersion: coverageVersion() };
  if (!covered && caller.channel === 'online') emit('coverage.location.out_of_area_attempted', redact(res));
  return res;
}
```

### 10.5 Tax rate resolution `[I]`

```ts
function resolveTaxRate({ fulfillment, zone, shop, at, customer, jobOverride, lineTaxable }): { ratePpm: Ppm|null; label?: string; source } {
  if (lineTaxable === false || customer?.taxExempt) return { ratePpm: 0, source: 'none' };
  if (jobOverride != null) return { ratePpm: jobOverride, source: 'job_override' };     // 05 field [S: T16]
  const pickRate = (rates: EffectiveTaxRate[]) => rates
      .filter(r => r.effectiveFrom <= at).sort((a, b) => b.effectiveFrom.localeCompare(a.effectiveFrom))[0];
  if (fulfillment === 'shop' && shop) {
    const r = pickRate(shop.taxRates) ?? pickRate(resolveZones(shop.location, {channel:'crm'}).primary?.taxRates ?? []);
    if (r) return { ratePpm: r.ratePpm, label: r.label, source: 'shop' };
  }
  if (fulfillment === 'mobile' && zone) {
    const r = pickRate(zone.taxRates); if (r) return { ratePpm: r.ratePpm, label: r.label, source: 'zone' };  // [S: T11]
  }
  const d = settings.defaultTaxRatePpm;
  return d != null ? { ratePpm: d, source: 'business_default' } : { ratePpm: null, source: 'none' };
}
// `at` = job's service date in the job location's timezone (not booking date) so effective-dated changes follow performance date.
```

### 10.6 Publish (validation, snapshot, atomic swap) `[I]`

```ts
async function publish(actor, expectedDraftHash, opts) {
  return withBusinessLock(businessId, 'catalog_publish', async () => {     // serialises publishes per tenant
    const draft = await loadDraftTree();
    if (hashCanonical(draft) !== expectedDraftHash) throw VERSION_CONFLICT;  // someone edited after preview
    const errors = [], warnings = [];
    for (const s of draft.services.filter(live)) {
      if (s.pricingMode !== 'quote_required' && s.price == null) errors.push(err(s, 'price_required'));
      if (s.type === 'route' && !settings.routeOptimizationEnabled) errors.push(err(s, 'route_toolkit_off'));
      if (!s.fulfillment.length) errors.push(err(s, 'fulfillment_required'));
      if (!fulfillmentAllowedByCategory(s)) errors.push(err(s, 'category_restriction'));
      if (imagePending(s.imageAssetId)) errors.push(err(s, 'image_processing'));
      if (!(await anyWorkerCan(s.id))) warnings.push(warn(s, 'no_worker_for_service'));
      if (!coverageServes(s)) warnings.push(warn(s, 'no_zone_or_shop_offers_service'));
    }
    for (const c of draft.categories.filter(live)) if (!draft.services.some(s => live(s) && !s.hidden && s.categoryId === c.id))
      warnings.push(warn(c, 'empty_category_hidden_online'));
    for (const p of draft.pairings) if (!live(byId(p.serviceId)) || !live(byId(p.addOnId))) p.enabled = false; // silently pruned
    if (errors.length) return { status: 'rejected', errors, warnings };
    const internal = buildInternalSnapshot(draft);
    const pub = buildPublicSnapshot(internal);           // strips cost, company add-ons, notes, stock, worker ids; merges global questions
    assertSize(gzip(pub) <= 300_000);                    // hard cap 300 KB; budget 30 KB
    const prev = await getLiveRevision();
    const rev = await insertRevision({ number: (prev?.number ?? 0) + 1, internal, pub, diff: diff(prev?.internal, internal), status: 'ready' });
    const swapped = await cas('business.publishedRevisionId', prev?.id ?? null, rev.id);
    if (!swapped) { await markRejected(rev); throw VERSION_CONFLICT; }
    await markSuperseded(prev); await clearUnpublishedFlags(draft);
    await outbox.add('catalog.revision.published', { revisionId: rev.id, number: rev.number, diff: summarize(rev.diff) });
    for (const ch of rev.diff.priceChanges) await outbox.add('catalog.service.price_changed', { ...ch, revisionId: rev.id, applyToFutureRecurring: !!opts.applyPriceToFutureRecurring });
    // cache purge happens in outbox consumer (retryable), not inline
    return { status: 'live', revisionId: rev.id, number: rev.number, warnings };
  });
}
```
- Events go through a transactional **outbox** written in the same transaction as the pointer swap, so a crash never publishes without events or vice-versa `[I]`.

### 10.7 Price selection (preview pricing for 01/03/05) `[I]`

```ts
function priceSelection(req, caller): PriceSelectionResult {
  const rev = loadRevision(req.revisionId ?? publishedRevisionId());
  const lines: PricedLine[] = []; const warnings = [];
  let work = 0, buffer = 0;
  for (const l of req.lines) {
    const s = rev.service(l.serviceId) ?? throwErr('SERVICE_NOT_AVAILABLE');
    if (!s.fulfillment.includes(req.fulfillment)) throwErr('FULFILLMENT_NOT_OFFERED');
    const sel = l.variationSelections ?? autoSelectFromAsset(s, req.customerAssetId);  // 07 asset attributes
    let price = s.price ?? 0, cost = s.cost, dur = s.duration;
    for (const g of s.variationGroups) {
      if (g.kind === 'single_choice') {
        const o = g.options!.find(o => o.id === sel[g.id]) ?? (g.required ? throwErr('VARIATION_REQUIRED', g.id) : null);
        if (!o) continue;
        price += g.priceMode === 'replace_base' ? (o.price - (s.price ?? 0)) : o.price;
        cost += o.costDelta; dur += o.durationDelta;
      } else {
        const u = clampUnits(l.units?.[g.id] ?? g.unit!.minUnits, g.unit!);
        const billable = Math.max(0, u - g.unit!.includedUnits);
        price += roundHalfEven(g.unit!.unitPrice * billable / 1000);
        cost  += roundHalfEven(g.unit!.unitCost  * billable / 1000);
        dur   += g.unit!.unitDuration * billable;
      }
    }
    for (const a of answerSurcharges(s, l.questionAnswers)) { price += a.priceDelta ?? 0; dur += a.durationDelta ?? 0; }
    if (s.pricingMode === 'hourly') price = roundHalfEven(s.hourlyRate! * dur / 3600);
    lines.push({ kind: 'service', refId: s.id, label: s.name, detail: labels(sel), qty: 1, unitPrice: Math.max(0, price),
                 amount: Math.max(0, price), cost, duration: Math.max(300, dur), taxable: s.taxable, catalogRevisionId: rev.id });
    work += Math.max(300, dur); buffer = Math.max(buffer, s.bufferAfter);
  }
  for (const a of req.addOns) {
    const ad = rev.addOn(a.addOnId);
    if (!ad || (caller.isPublic && ad.audience === 'company')) throwErr('ADDON_NOT_AVAILABLE');
    if (!req.lines.some(l => rev.paired(ad.id, l.serviceId))) throwErr('ADDON_NOT_PAIRED');
    const qty = ad.quantity.selectable ? clamp(a.qty, ad.quantity.min, ad.quantity.max) : 1;
    if (stockAvailable(ad) < qty && ad.outOfStock !== 'allow_backorder') (caller.isPublic ? throwErr('OUT_OF_STOCK') : warnings.push('out_of_stock'));
    const d = ad.extraDuration * (ad.durationScalesWithQty ? qty : 1);
    lines.push({ kind: ad.audience === 'company' ? 'company_addon' : 'addon', refId: ad.id, label: ad.name, qty,
                 unitPrice: ad.price, amount: ad.price * qty, cost: ad.cost * qty, duration: d, taxable: ad.taxable, sku: ad.sku, catalogRevisionId: rev.id });
    work += d;
  }
  const cov = req.fulfillment === 'mobile' ? resolveLocation({ location: req.location, serviceIds: req.lines.map(l => l.serviceId) }, caller) : null;
  if (req.fulfillment === 'mobile' && !cov!.covered && !caller.canOverrideCoverage) throwErr('OUT_OF_AREA');
  let travelLine;
  if (cov?.travelCharge) {
    const preSubtotal = sum(lines.map(l => l.amount));
    const waived = cov.travelCharge.waiveAboveSubtotal != null && preSubtotal >= cov.travelCharge.waiveAboveSubtotal;
    const n = cov.travelCharge.mode === 'per_mobile_service' ? lines.filter(l => l.kind === 'service').length : 1;
    if (!waived) travelLine = { kind: 'travel_fee', refId: cov.primaryZoneId, label: cov.travelCharge.name, qty: n,
                                unitPrice: cov.travelCharge.amount, amount: cov.travelCharge.amount * n, cost: 0, duration: 0,
                                taxable: cov.travelCharge.taxable, catalogRevisionId: rev.id };
  }
  const tax = req.fulfillment === 'shop' ? resolveTaxRate({ fulfillment: 'shop', shop: shopById(req.location.shopId), ... })
                                         : { ratePpm: cov!.taxRatePpm, source: cov!.taxSource };
  const rd = req.recurringFrequencyId ? recurringDiscountSpec(rev, req) : undefined;    // spec only; 05 applies
  return { lines: stripCostIfNeeded(lines, caller), travelLine, taxRatePpm: tax.ratePpm, taxSource: tax.source,
           recurringDiscount: rd, totalWorkDuration: work, blockedDuration: work + buffer, revisionId: rev.id, warnings };
}
```

### 10.8 Template instantiation `[I]`

```ts
function instantiateTemplate(t: PricingTemplate, mode, currency) {
  const idMap = new Map<string, ID>();                     // template-local keys → new IDs
  tx(() => {
    if (mode === 'replace') for (const e of liveCatalogEntities()) referenced(e) ? setHidden(e) : archive(e);
    for (const c of t.payload.categories) {
      const name = mode === 'add_alongside' && nameTaken(c.name) ? `${c.name} (template)` : c.name;
      idMap.set(c.key, createCategory({ ...c, name, status: 'draft' }));
    }
    for (const s of t.payload.services) idMap.set(s.key, createService({ ...s, categoryId: idMap.get(s.categoryKey),
          price: localPrice(s, currency), cost: localCost(s, currency), shortLinkSlug: uniqueSlug(s.name), createdVia: 'template' }));
    for (const ac of t.payload.addOnCategories) idMap.set(ac.key, createAddOnCategory(ac));
    for (const a of t.payload.addOns) idMap.set(a.key, createAddOn({ ...a, categoryId: idMap.get(a.categoryKey), price: localPrice(a, currency) }));
    for (const p of t.payload.pairings) setPairing(idMap.get(p.addOnKey), p.serviceKeys === 'all' ? 'all' : p.serviceKeys.map(k => idMap.get(k)));
  });
  // localPrice: native regional price if present, else FX-convert then roundToNicePrice (…9 or …5 endings, ≥ 1 major unit)
}
```

### 10.9 Duplicate service (deep copy) `[I]`

```ts
function duplicateService(src: Service, opts): Service {
  const copy = structuredClone(src);
  copy.id = newId(); copy.status = 'draft'; copy.hidden = src.hidden; copy.version = 1;
  copy.name = opts.newName ?? nextFreeName(`${src.name} (copy)`, src.categoryId);   // "(copy 2)", "(copy 3)"…
  copy.categoryId = opts.categoryId ?? src.categoryId; copy.duplicatedFromId = src.id; copy.createdVia = 'duplicate';
  copy.shortLinkSlug = uniqueSlug(copy.name);
  remapIds(copy.variationGroups, copy.checklist.items, copy.questions.items, copy.recurring.frequencies); // fresh child IDs; showIf references remapped
  if (!opts.copy.variations) copy.variationGroups = [];
  if (!opts.copy.checklists) copy.checklist = defaultChecklistConfig();
  if (!opts.copy.questions) copy.questions = { includeGlobal: true, items: [] };
  if (!opts.copy.recurring) copy.recurring = defaultRecurring();
  insert(copy);
  if (opts.copy.pairings) copyPairings(src.id, copy.id);
  if (opts.copy.workerSkills) requestSkillCopy(src.id, copy.id);   // command to 09
  if (opts.copy.coverageScope) { /* zoneIds/shopIds already cloned */ } else { copy.zoneScope = 'all'; copy.shopScope = 'all'; }
  return copy;
}
```

### 10.10 Shops in range / distance filter `[I]`

```ts
function shopsInRange(p: LatLng, serviceIds?: ID[]) {
  return activeShops()
    .filter(s => s.acceptOnlineBookings)
    .filter(s => !serviceIds || s.serviceScope === 'all' || serviceIds.every(id => s.serviceIds!.includes(id)))
    .map(s => ({ shop: s, km: haversineKm(p, s.location) }))
    .filter(({ shop, km }) => !shop.distanceFilter?.enabled || shop.distanceFilter.metric !== 'straight_line_km' || km <= shop.distanceFilter.max)
    .sort((a, b) => a.km - b.km)
    .slice(0, 25)
    // drive_minutes filters: batch a single matrix request (origins=[p], destinations=shops) — one provider call, cached 24h by rounded p
    .then(applyDriveMinuteFilters);
}
```

### 10.11 Inventory reservation (idempotent, race-safe) `[I]`

```ts
async function reserve(itemId, qty, jobLineId, idemKey) {
  if (await movements.exists(businessId, idemKey)) return movements.get(idemKey).result;   // idempotent replay
  return tx(async () => {
    const item = await selectForUpdate(itemId);              // or CAS loop on version
    const available = item.onHand - item.reserved;
    if (available < qty && item.outOfStockBehavior !== 'allow_backorder' && !callerMayOversell()) throw OUT_OF_STOCK(available);
    await update(item, { reserved: item.reserved + qty, version: item.version + 1 });
    await movements.insert({ type: 'reserve', qtyDelta: 0, reservedDelta: qty, jobLineId, idempotencyKey: idemKey });
    await maybeEmitThresholdEvents(item);                     // edge-triggered low/depleted
  });
}
// consume(jobLineId): reserved -= q, onHand -= q (one movement). release: reserved -= q. Each keyed by `${jobLineId}:${op}:${lineVersion}`.
// Nightly reconciler recomputes onHand/reserved from the ledger and open job lines; mismatches → alert + auto-correct movement 'count'.
```

### 10.12 Short-link slug generation `[I]`

```ts
function uniqueSlug(name: string): string {
  let base = slugify(name, { lower: true, strict: true, transliterate: true }).slice(0, 48) || 'service';
  if (RESERVED.has(base)) base += '-service';                 // reserved: 'admin','api','s','book','login',…
  let slug = base, n = 2;
  while (shortLinks.exists(businessId, slug)) slug = `${base}-${n++}`;
  return slug;
}
// Rename never changes slug automatically; owner may edit slug → old slug kept as redirect (active=true, targetId same).
// Slug reuse by a different service is blocked for 180 days after archive to avoid misdirecting old marketing links.
```

### 10.13 Zone change impact preview `[I]`

```ts
async function previewZoneChange(zoneId, patch) {
  const before = getZone(zoneId), after = { ...before, ...patch };
  const jobs = await jobsApi.futureJobs({ zoneIdOrContainedBy: before.bbox ∪ after.bbox, status: ['scheduled','unassigned'] }); // 03/04 query
  return {
    futureJobsNowUncovered: jobs.filter(j => inside(j.point, before) && !resolveZonesWith(j.point, after).primary).map(id),
    futureJobsFeeChanged:   jobs.filter(j => j.zoneId === zoneId && feeDiffers(before, after)).map(id),
    futureJobsTaxChanged:   jobs.filter(j => j.zoneId === zoneId && taxDiffers(before, after, j.date)).map(id),
    overlaps: otherActiveZones().filter(z => intersects(z.geometry, after.geometry)).map(z => z.id),
  };
}
```

---

## 11. Edge cases & failure modes

### 11.1 Catalog editing & publishing
1. Two admins edit the same service concurrently → `version` mismatch on autosave → field-level merge dialog; never last-write-wins silently.
2. Admin A publishes while admin B is mid-edit → B's unsaved local edits stay in B's draft queue; B's next autosave lands in the (now new) draft; B sees "Published by A 1 min ago — your changes are still unpublished".
3. Publish preview shown, then someone edits → `expectedDraftHash` mismatch → re-preview required (prevents publishing something not reviewed).
4. Two publishes at once → per-tenant lock + CAS; second gets `VERSION_CONFLICT`.
5. Publish succeeds but CDN purge fails → outbox retries purge; snapshot endpoint also checks `publishedRevisionId` with 60 s max-age, so worst-case staleness ≤ 60 s.
6. Booking session started on revision N, publish to N+1 mid-checkout → checkout re-prices against N+1; if price changed, 01 shows "Price updated from $X to $Y" and requires confirmation; if service removed, the cart line is flagged unavailable. Grace option `[I]`: honour revision N for 15 minutes after `priceSelection` returned (quote token).
7. Service archived while in someone's cart → same as 6.
8. Category made "Mobile only" while it contains shop-only services → validation error listing the services; offer "Switch these to mobile" bulk action.
9. Empty category (all services hidden) → hidden online automatically; warning only.
10. Price 0 services (free inspection) → allowed; booking site shows "Free"; Agent 05 must handle $0 jobs (no card capture unless deposit rule).
11. Cost > price → allowed with negative-margin warning (loss leaders exist).
12. Duration very long (multi-day jobs, e.g. 12 h ceramic coating) → allowed up to 24 h; > 24 h jobs are out of scope v1 (Q8).
13. Variation "replace base" option cheaper than base → price decreases; must never go < 0 (clamped, validation warns).
14. Variation group required but customer's asset doesn't map → customer must choose manually; never default silently to cheapest when asset mapping exists but is ambiguous.
15. Asset auto-selection disagrees with customer choice → customer choice wins online; CRM shows "Asset is SUV but Sedan pricing selected" warning.
16. Question with `showIf` referencing a deleted question → validation removes the condition and warns; on duplicate, references remapped.
17. Global questions toggled off but a global question is marked "required for all services" (legal waiver) `[I]` → global items can be flagged `cannotBeDisabled`; per-service toggle hides only the others.
18. Image upload of HEIC/CMYK/huge PNG → server transcodes; failure keeps the previous image and shows error; publish blocked while processing (max 60 s), then fails gracefully with "Image could not be processed".
19. SVG icon upload with scripts → sanitised by Agent 11 pipeline; rejected if unsanitisable.
20. Service renamed → slug unchanged; reports group by ID, display latest name; job snapshots keep old name.
21. Unicode/emoji/RTL in names → NFC normalise; uniqueness compares case-folded NFC; max length counts grapheme clusters, not bytes.
22. Template applied twice (`add_alongside`) → "(template)" suffix collision → "(template 2)".
23. Template replace while recurring series reference old services → those services hidden, not archived; series continue.
24. Route Optimization turned off in Toolkit while route services are live → route services hidden online at next publish; CRM keeps existing route jobs; warning banner.
25. Route service with recurring weekly → allowed; Agent 03 assigns to weekly route day.
26. Autosave offline for hours → local queue persists; on reconnect replays in order with original `expectedVersion`; conflicts surface for merge.
27. Browser crash mid-edit → local draft recovered on reopen ("Restore unsaved changes?").
28. Auto-publish mode + half-finished edit → auto-publish runs only after validation passes and 5 s idle; never publishes a service in invalid state (it stays draft).
29. Rollback to revision with now-archived worker skills/zones → rollback creates a new revision; services referencing archived zones fall back to `zoneScope='all'` with warning.
30. Very large catalogs (500+ services, e.g. multi-location franchise) → public snapshot paginated by category beyond 300 KB; admin list virtualised.

### 11.2 Add-ons & inventory
31. Add-on paired to zero services → never shown; warning "Not offered with any service".
32. New service created → gets pairings from add-ons with `pairWithFutureServices`; owner sees them listed in the service editor (no hidden surprise).
33. Add-on paired with service A only, cart has A and B → shown once; attached to A for duration/split.
34. Two customers book the last unit simultaneously → atomic reservation; second gets `OUT_OF_STOCK`; booking site re-fetches and shows "Sold out" before payment, not after.
35. Reservation leak (job deleted without event) → nightly reconciler releases reservations for lines that no longer exist.
36. Job completed offline in worker app, synced later → consume uses idempotency key; double sync doesn't double-decrement.
37. Job reopened after completion → `return` movement re-adds stock and re-reserves.
38. Stock adjusted negative by count correction below reserved → available negative → status `out`; future reserved jobs flagged "Insufficient stock for job #123".
39. Quantity limits changed after bookings exist → existing job lines keep their qty; only new selections validated.
40. SKU duplicated across a customer add-on and a company add-on representing the same physical product → allowed if both link to the **same** `InventoryItem` `[I]` (one stock pool); otherwise SKU uniqueness error.
41. Company add-on with editable POS price → worker enters negative or huge amount → bounds: 0 ≤ amount ≤ 10× configured price or manager approval (05/09 enforce).
42. Add-on added after job completed → blocked `[S: T07]`; route user to invoice adjustment (05).
43. Add-on extra duration pushes job into next appointment → warning to adder and dispatcher; never auto-reschedule.
44. Archived add-on referenced by subscription "free add-on" entitlement → archive blocked or replacement required (06 reference index).

### 11.3 Coverage
45. Isochrone provider down/slow → 10 s timeout → circle fallback or manual draw; zone creation never blocked.
46. Isochrone returns multipart shape with islands (bridges, rivers) → tiny parts dropped, part containing center kept, other large parts kept.
47. Center on an island/ferry-only location → isochrone tiny; warn "This area is very small (0.4 km²) — check your center or travel time".
48. Self-intersecting manual polygon → client highlights; server rejects or auto-repairs with confirmation showing repaired shape.
49. Polygon crosses antimeridian → rejected (out of market scope); zones > 50,000 km² rejected.
50. Overlapping zones with different travel fees/tax → deterministic primary per §10.3; overlap shown on map with hatching and listed in zone editor ("Overlaps Round Rock — Austin wins (priority 1)").
51. Address exactly on a boundary → inside (deterministic).
52. Address geocodes to postal-code centroid (rural, new build) → mobile booking requires pin confirmation; CRM warns.
53. Geocoder returns wrong city for ambiguous street name → bias by business country and zone bboxes; show the resolved address back to the customer for confirmation.
54. Customer address changes after booking (CRM edit) → re-resolve coverage; if zone changed → travel fee/tax recalculation proposed (05) and eligible workers re-evaluated (03); if now uncovered → requires override permission.
55. Zone shrunk so future booked jobs fall outside → jobs are **kept** (no auto-cancel), flagged in impact preview and as "Outside service area" on the job; dispatcher decides.
56. Zone deleted/archived with assigned workers and future jobs → staff assignments ended; jobs retain `zoneId` snapshot for reporting; future jobs flagged.
57. Only zone archived → mobile online booking disabled; booking site shows shop services only, or "Online booking unavailable" if none; confirm dialog states this.
58. Zone has zero staff → falls back to all eligible workers with warning (Q3); if business turns on strict mode `[I]`, zero-staff zone = not bookable online.
59. All zone staff deactivated → `team.worker.deactivated` triggers warning on zone and on affected future jobs.
60. Worker assigned to zone but not allowed any service offered there → 09/02 warning "Worker can't perform any service in this zone".
61. Shop address in no zone → shop booking still works (shop is independent); shop tax falls back to business default.
62. Shop distance filter by drive minutes but matrix provider down → fall back to straight-line with a 1.3 detour factor, log degraded mode.
63. Customer far away with a shop service and filter off → allowed (owner chose).
64. Business operates across timezones (zone far from HQ) → zone `timezone` used for "today", tax effective dates and route days.
65. Tax rate changes at midnight local → effective-dated; job performed on/after date uses the new rate; jobs already invoiced unchanged.
66. Tax rate entered as 8.25 vs 0.0825 confusion → UI accepts percent only, shows "8.25%" preview and a sample "$100 → $8.25 tax"; reject > 30%.
67. Travel fee in a zone where business wants per-mile pricing → not supported v1 (fixed only per `[S: T11]`); open question Q9.
68. Travel fee waived threshold uses pre-discount subtotal; discount code dropping below threshold does not re-add fee (documented) — or does? decision needed by 05 (Q10).
69. CRM override job: no zone, no travel fee, business default tax; reporting buckets as "Outside service areas".
70. Out-of-area event spam (bots hammering address checks) → per-IP rate limit, event dedupe per session/address hash per 24 h.
71. Privacy: out-of-area points stored rounded (~100 m) and address hashed for analytics; full address only kept if the customer submits the lead form.
72. Geocode cache poisoning across tenants → cache key is address-only but values are provider output (not tenant input); tenant corrections (pin moves) stored per tenant, never written to shared cache.

### 11.4 Cross-module / data integrity
73. Event delivered twice → consumers idempotent on `eventId`; inventory ops keyed by idempotency key.
74. Event out of order (`job.line.removed` before `job.line.added`) → inventory handler checks line version; stale events ignored, reconciler fixes residue.
75. Subscription package references a service that becomes hidden → still valid for subscription bookings (hidden ≠ archived).
76. Discount code tied to archived service → 06 marks code partially invalid; archive dialog lists the code.
77. Worker skill rows (09) for archived service → retained for history; excluded from dispatch.
78. Reports grouping by category when a service moves categories → report uses job snapshot `categoryId` (historical truth) with option "current categories" `[I]`.
79. Currency changed after setup → disallowed once any transaction exists; before that, all catalog money converted with confirmation.
80. Bookings MCP/AI agent attempts to read cost or company add-ons → stripped server-side (public role); CRM MCP destructive ops require `confirm: true`.
81. AI website import extracts nonsense prices ($0, $99999) → flagged low-confidence; never auto-published.
82. Clock skew between worker device and server for consume timestamps → server time authoritative for ledger; device time stored as metadata.

---

## 12. Better than Fieldd

### 12.1 Concrete improvements
1. **One save/publish model with a diff.** Fieldd mixes publish (services) and autosave-live (add-ons, zones) (D2). We autosave every draft, publish atomically with a human-readable diff ("Interior Detail $149 → $159; 2 add-ons added"), and offer one-click rollback to any revision.
2. **Draft preview link** of the booking site before publishing (signed, noindex).
3. **Pricing variations built in** (vehicle size, bedrooms, sq ft, per-unit) with **asset auto-selection** from the customer's saved vehicle/home (07) — fewer taps for returning customers, fewer under-quoted jobs.
4. **Price-impact guardrails:** publish dialog counts affected recurring series and asks whether to apply new prices to future occurrences — no silent price hikes, no silent revenue loss.
5. **Coverage tester:** type any address on the Service Areas map and see covered?/zone/travel fee/tax/eligible workers instantly — removes guesswork when customers call.
6. **Zone change impact preview** before saving (jobs that become uncovered, fees/tax changing).
7. **Deterministic overlap rules** (priority → smallest area → nearest center), visible on the map with hatching.
8. **Effective-dated tax rates** and tax components (state + city) with a "$100 → $8.25" preview.
9. **Travel fee options:** per visit vs per service, waive above subtotal, exempt route services, taxable flag — all optional, defaults match Fieldd's simple fixed fee.
10. **Inventory that actually reserves stock** for future jobs, has an append-only ledger, low-stock alerts, reorder list and CSV import/export — Fieldd shows stock fields only on the add-on `[S: T07]`.
11. **Dead short-link protection:** renamed services keep their links; archived services' links land on a friendly fallback, not a 404.
12. **Quick-create from CRM is safe:** creates a hidden-online service so phone bookings ("take a phone call and build a service/booking for the client live" — owner requirement) never leak unreviewed items to the public site.
13. **AI/website import as draft with confidence scores**, never auto-live (`[S: llms]` AI setup made safer).
14. **Tiny, cache-friendly public snapshot** (≤ 30 KB gz target, ETag + stale-while-revalidate, images by responsive URL with blurhash placeholders) → no layout shift, fast TTI on mid-range 4G phones (BRIEF performance bar).
15. **Offline-tolerant admin editing:** local autosave queue, crash recovery, conflict merge dialog.
16. **Accessibility:** polygon editor has a keyboard/list mode (edit vertices numerically, "expand by 2 km" buffer tool), all pickers screen-reader labelled, color is never the only zone identifier (names + patterns).
17. **Bulk operations:** multi-select services → change category, adjust prices by % or fixed amount (with rounding to .99/.00), toggle mobile/shop, pair add-ons.
18. **Cost privacy enforced server-side** by permission, not hidden in UI.
19. **Stable IDs and reference index** → safe archive with "Replace with…" mapping for recurring series, subscriptions and discounts.
20. **Native-app readiness:** every admin command in §8 is a plain API usable by the future desktop/iPhone apps (owner requirement) — e.g. a staffer on site can adjust a client's package/selected options live via `priceSelection` + job line edits.

### 12.2 Budget tier notes `[I]` (final packaging decided with owner; Fieldd's tiers per `[S: llms]` gate heatmaps etc. at Pro)

| Capability | Starter (just-starting business) | Growth | Pro |
|---|---|---|---|
| Templates, categories, services, add-ons, checklists/photos/questions, short links, recurring options | ✓ | ✓ | ✓ |
| Pricing variations (single group) | ✓ | ✓ (3 groups) | ✓ |
| Per-unit pricing, question surcharges | — | ✓ | ✓ |
| Company add-ons | ✓ | ✓ | ✓ |
| Inventory stock tracking (counts, low-stock alert) | ✓ (≤ 25 tracked items) | ✓ | ✓ |
| Reservations ledger, reorder list, CSV inventory | — | ✓ | ✓ |
| Service zones | 1 zone + 1 shop | 5 zones / 3 shops | unlimited |
| Drive-time isochrone generation | ✓ (rate-limited) | ✓ | ✓ |
| Manual polygon editing, multipart/holes | ✓ | ✓ | ✓ |
| Travel fee per zone, custom zone tax | ✓ | ✓ | ✓ |
| Effective-dated tax, tax components | — | ✓ | ✓ |
| Revision rollback, draft preview link | preview ✓, rollback last 3 | ✓ | ✓ |
| AI website import | 1 run | ✓ | ✓ |
| Out-of-area demand heatmap | — | — | ✓ |

Cost drivers to watch (feed the API keys & integrations register): geocoding calls (cache aggressively, 90-day TTL), isochrone calls (only on Draw; cached 30 days), distance-matrix calls (only for drive-minute shop filters; cached 24 h by rounded point), image processing/storage. **Integrations register entries from this module:** geocoding provider key, isochrone/routing provider key, distance-matrix key (may be same vendor), map tiles key (booking-site map + admin map; client-exposed key must be domain-restricted to the tenant booking hosts), image storage/CDN credentials, AI extraction model key (website import). All are **platform-owned** keys stored in the platform secret store, rotated at least every 90 days `[I]`; tenants never supply keys for these in v1.

---

## 13. Open questions

| # | Question | Why it matters | Default until answered |
|---|---|---|---|
| Q1 | Is Fieldd's catalog screen "Settings → Pricing" or "Settings → Services" today (D1), and are add-ons separate tabs? | Navigation/IA | "Services & Pricing" with tabs |
| Q2 | Is the zone travel charge applied once per booking or per mobile service line (D5)? | Totals | once per visit |
| Q3 | What happens when a zone has no staff assigned — bookable by anyone or not bookable? | Solo operators vs. teams | all eligible workers + warning |
| Q4 | Do add-on "min/max quantities" mean per-booking quantity limits or inventory par levels (D4)? | Data model & UI | model both |
| Q5 | Does Fieldd's default zone at signup use a drive-time isochrone (what minutes?) or the city boundary? | Signup seeding | 30-min isochrone |
| Q6 | When a service price changes, do existing recurring series re-price in Fieldd? | Revenue/customer trust | keep locked; owner opt-in to apply |
| Q7 | Where are route days configured (per zone? per service? per worker)? Does route service carry a duration? | 02/03 boundary | 03 owns; 02 exposes optional `routeDays` |
| Q8 | Multi-day services (e.g. 2-day paint correction) — supported? | Duration model | not v1 |
| Q9 | Should travel charge support distance/time-based amounts (per mile beyond X) in addition to fixed? | Competitive feature | fixed only v1 |
| Q10 | Travel-fee waiver threshold: pre- or post-discount subtotal? Is the travel fee taxable by default? | Tax compliance, 05 | pre-discount; not taxable |
| Q11 | Are per-service checklists able to disable global checklists (as questions can)? | Worker workflow | yes (toggle) |
| Q12 | Do add-ons require Publish in Fieldd or go live on autosave? Should our solo-operator "auto-publish" default be on? | D2, onboarding friction | off; on for signup seed only |
| Q13 | Shop distance-based filtering: straight-line distance or drive time? Units? | Booking UX | support both, default straight-line |
| Q14 | Are zone/shop "custom icons" map markers, or tiles on a location-choice step of the booking page? | 01/11 UI | map markers + location tiles |
| Q15 | Can a service have different prices per zone or per shop (e.g. city vs suburbs)? | Pricing model | not v1 (use travel fee); add `ZonePriceOverride` later |
| Q16 | Does duplicate copy add-on pairings and worker assignments in Fieldd? | Duplicate behavior | yes |
| Q17 | Are customer questions answered per service or once per booking when the same global question applies to multiple services? | Booking UX | once per booking (deduped by question ID) |
| Q18 | Tax-inclusive pricing for non-US tenants (AU/UK/CA GST/HST) — required at launch? | 05 tax math | flag supported, US-first |
| Q19 | Should hidden services be bookable through short links (private offers)? | Marketing | yes via `allowViaLinkWhenHidden` |
| Q20 | Signup event ownership (`account.business.created`) — Agent 01, 09 or 10? | Seeding trigger | Agent 10 to assign |

---

## 14. Verify against video

The owner's walkthrough (https://youtu.be/vyELvdWCdnc, customer side) plus any future admin recording. Customer-side items first, then admin items (if the admin CRM is shown).

### 14.1 Customer side (booking site on the business subdomain)
- [ ] Are categories shown as separate pages/steps, as tabs, or as a single scrolling list? Order of categories vs. settings order. `[S: T06]`
- [ ] Category icon style (line/filled), size, and whether an image or icon appears per category.
- [ ] Service card layout: image position/aspect, name, description length/truncation ("read more"?), price format ("$149", "From $149"), duration shown or not.
- [ ] Is there a vehicle-size / home-size choice? Where (before services, inside service card, as a separate step)? Does price change live?
- [ ] Is the address asked **before** or **after** choosing services? What happens when the address is outside the service area (message text, lead capture, phone number shown)?
- [ ] Is there a map showing service areas or shops? Marker icons (custom icons `[S: T11]`), polygon displayed or not.
- [ ] Mobile vs shop choice: how the customer picks "we come to you" vs "visit our shop"; how shops are listed (distance shown? sorted?).
- [ ] Is a travel fee shown? As a separate line, at which step, with what label?
- [ ] Is tax shown separately before checkout? Label and rate shown?
- [ ] Add-on step: categories with images `[S: T07]`, add-on cards (image, description, price, extra time shown?), quantity selectors, "sold out" states, multi-select behaviour, skip button.
- [ ] Are add-ons offered only for some services (pairing evidence)?
- [ ] Recurring options: frequency choices, discount display ("Save 10%"), default selection, one-time option `[S: T06]`.
- [ ] Customer questions: placement, required markers, answer types seen (text, choice, photo?), whether global and service questions are merged `[S: T06]`.
- [ ] Route-type service: "Anytime {day}" style day picker instead of times `[S: T02]`.
- [ ] Short link behaviour: opening a service-specific link skips category pages? URL format (`/s/...`, query param, path?) `[S: T06]`.
- [ ] Booking-site load: does the catalog appear instantly or after a spinner? Any layout shift? Image loading behaviour.
- [ ] Dark mode availability on the booking site (owner requirement; Agent 11).

### 14.2 Admin side (if recorded)
- [ ] Exact nav path and labels: Settings → Pricing / Services / Services & Pricing (D1); tab names "Customer add-ons", "Company add-ons".
- [ ] Template banner/picker: list of industries, preview content, what happens if applied over an existing catalog.
- [ ] Category editor fields: name, mobile only (shop only?), icon picker style `[S: T06]`.
- [ ] Service editor: field order and labels (name, description, image, price, cost, duration, mobile/shop), section names (checklists, photos, recurring, questions, short link) `[S: T06d]`; is there a variation/size pricing section?
- [ ] Route option placement on the service editor and whether a duration is still required `[S: T02]`.
- [ ] Checklist editor: item types, required flags, before/after photo count inputs; global checklist location `[S: T06]`.
- [ ] Recurring options: frequency list editor, discount type (% / $), "let customers choose" toggle.
- [ ] Questions: answer types, "turn off global questions" toggle wording.
- [ ] Short link format and copy button; QR option?
- [ ] Duplicate: what is copied, resulting name ("Copy of …"?).
- [ ] Publish button location, unpublished-changes indicator, confirmation dialog, and whether add-ons need publishing (D2, Q12).
- [ ] Add-on editor: kind (service/product), extra time, part number, stock, min/max quantities and their meaning (D4), pairing UI (all on by default) `[S: T07]`, autosave indicator.
- [ ] Company add-ons: identical editor? Any extra fields (editable price at POS)?
- [ ] Service areas: default zone shape at signup (isochrone vs city boundary, Q5), "+" placement, max travel time input units/range, Draw animation, vertex editing tools `[S: T11]`.
- [ ] "Add staff to zone?" prompt after save — exact wording and UI `[S: T11]`.
- [ ] Travel charge fields (name + fixed $) and custom tax rate input format (percent decimals) `[S: T11]`.
- [ ] Shop editor fields; distance-based filter setting (units, drive time vs distance, Q13); custom icon setting `[S: T11]`.
- [ ] Overlapping zones: does Fieldd allow them, and how does it display/resolve them?
- [ ] Worker profile: service area and shop assignment controls, allowed-services list `[S: T13]`.
- [ ] CRM booking out-of-area override: warning text and flow `[S: T11]`; inline "create new service" from the booking form `[S: T15]` `[S: T19]`.
- [ ] Adding add-ons after booking in CRM job card and worker POS; blocked after completion? `[S: T07]`
- [ ] Inventory: is there any stock list/report outside the add-on editor? Low-stock alerts?
- [ ] Empty states (no services, no add-ons, no zones) and error messages seen.
