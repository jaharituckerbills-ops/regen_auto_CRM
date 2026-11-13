# 01 — Booking Portal & Subdomain Hosting

> Agent 01 · Phase 1 "CRM brain" · Status: draft v1 (pre-video) · Date: 2026-11-12
> Scope: everything a customer experiences on the business's booking site (the "customer portal" /
> "customer hub"), how that site is put on the business's own web presence (subdomain/custom
> domain, buttons, embed, QR, short links), customer self-service accounts, portal style and
> portal settings, and the performance/reliability bar of a public booking site.
> Infrastructure-agnostic per BRIEF: no vendor or framework is assumed. Where a mechanism has
> several common implementations, the options are listed and the decision is deferred to the
> stack discussion (Stage 2).

## 0. Conventions used in this file

**Source tags**

| Tag | File |
|---|---|
| `[S: T01]` … `[S: T20]` | `research/transcripts/NN_<id>.txt` for video NN (e.g. `T05` = `research/transcripts/05_0qNwovgNTqM.txt`) |
| `[S: T05d]` etc. | the matching `.description` file (chapter list) |
| `[S: llms]` | `research/site/llms.txt` (Fieldd's own facts, updated 2026-10-23; authoritative) |
| `[S: web <url>]` | third-party web source (lower confidence) |
| `[I]` | inference: industry convention or reasoned guess. **Not** a confirmed Fieldd fact. |
| `[D]` | design decision for *our* platform (not a claim about Fieldd at all) |

Research note: web searches for Fieldd custom-domain documentation returned nothing Fieldd-specific
(only the FielddEDU video page) [S: web https://fieldd.co/media-center/how-to-set-up-your-online-booking-page-fielddedu];
comparable booking SaaS products (Zoho Bookings, Lead Prosper, Agiled, Kinnovis) all implement
custom booking hostnames as "enter your subdomain → add a CNAME/DNS record → platform verifies"
[S: web https://help.zoho.com/portal/en/kb/articles/bookings-custom-domain]
[S: web https://support.leadprosper.io/article/569-adding-custom-subdomains-to-your-customer-buyer-portal]
[S: web https://docs.agiled.app/docs/settings/custom-domain-setup]
[S: web https://help.kinnovis.com/en/articles/14125319-using-your-own-domain-for-the-booking-customer-portal].
swash.co itself could not be fetched from this research environment (network-restricted), so
the exact swash.co hostname/DNS setup is **unverified** — see §14 for how the owner can confirm it
in 2 minutes.

**Terminology**

- **Business / tenant** — a service company using the platform (e.g. a mobile detailer, a cleaner).
- **Customer** — the business's end client who books on the portal.
- **Portal** — the public, business-branded booking site + logged-in customer area. Fieldd calls it
  "online booking page" / "customer portal" in video 05 and "customer hub" in video 02 and on its
  site [S: T05][S: T02][S: llms]. The admin settings area was called "Online Scheduling → Style" in
  video 05 and "Customer hub → Style → Text" in video 02 [S: T05][S: T02] — likely a rename during the
  October 2026 settings rebuild ("Settings: hundreds of features, now easier to find") [S: llms][I].
- **Platform host** — the fallback hostname the platform gives every tenant, e.g.
  `acme.book-platform.example` [D].
- **Custom host** — the tenant's own hostname, e.g. `book.acmedetailing.com` [D].
- **Booking session** — one visitor's in-progress booking (draft), persisted server-side from the
  first meaningful interaction. Fieldd's equivalent is the "booking attempt" visible in real time
  on the lead board [S: T10].
- Money is always **integer minor units (cents)** with an ISO-4217 currency code. Times are stored
  as UTC instants plus the IANA timezone of the service location.

**Ownership boundaries (who owns what, so this file does not redefine other modules)**

| Concern | Owner | This module's role |
|---|---|---|
| Services, categories, prices, durations, add-ons, service areas, shops, travel fees, zone tax, short-link *slug* on a service | 02 | Renders them; calls 02's price-quote and area-check queries |
| Slot availability, holds, appointment reservation, auto-dispatch, route days | 03 | Calls `GetAvailability`, `CreateHold`, `ConfirmHold` |
| Job/appointment record, single job ID, quote→book link, on-my-way status | 04 | Triggers job creation via booking saga; renders status on tracking page |
| Payments, card on file, deposits, pre-auth, Apple Pay, refunds, cancellation-fee charge, totals/tax order | 05 | Embeds 05's payment element; calls 05's intents API |
| Subscription packages, gift cards, discount codes, referral *reward rules* | 06 | Renders self-purchase/upsell; calls 06's validate/redeem/purchase commands |
| Customer profile, contacts, addresses, assets/vehicles, intake answers storage, leads, abandoned bookings, magic data | 07 | Streams session progress to 07; calls 07 upsert/lookup |
| All SMS/email/push templates & sending, review requests, referral marketing, dashboard tiles | 08 | Emits events; 08 decides what to send/show |
| Worker profile/photo shown on tracking page, worker-area assignment, RBAC | 09 | Read-only consumer |
| **Portal config, style, text overrides, domains/TLS, embeds, QR, short-link routing, booking-flow orchestration, booking sessions, attribution, booking-question definitions, terms & cancellation policy, portal auth, tracking page** | **01** | Owner |

---

## 1. Summary

The booking portal is the business's public storefront and self-service desk. A customer lands on
it from the business's website button, Google Business Profile, an email/SMS link, a QR code or a
per-service short link [S: T05][S: T06]; picks a category and service, adds upsells, proves they are
inside a service area (or picks a shop), picks a time (or a route day like "Anytime Thursday"),
answers questions, signs in or creates an account, adds their vehicle/home, accepts terms, pays a
deposit or saves a card, and gets a confirmed appointment that the platform auto-dispatches
[S: T05][S: T06][S: T07][S: T11][S: T02][S: T16][S: llms]. Logged-in customers keep saved assets, rebook
past services, cancel/reschedule under the business's cancellation policy, buy and send gift cards,
buy subscriptions, use referral codes and follow the technician live on the way
[S: T05][S: T01][S: llms]. The owner's top requirement — and the way the reference business swash.co
uses Fieldd — is that this site runs on a **subdomain of the business's own website**
(e.g. `book.example.com`) [BRIEF]. We do that with a CNAME to our edge, automatic on-demand TLS, and
Host-header tenant resolution, with a platform hostname as a permanent fallback. Because it is a
public site on the business's brand, it must be fast on mid-range phones, never double-book, and
never lose a booking even when payments, availability or the network fail mid-flow.

## 2. Roles

| Role | What they do here | Source |
|---|---|---|
| Owner / Operator | Configure style, installation (link/domain/embed/QR), booking questions, intake form, customer assets, terms, cancellation policy, analytics, booking sources, additional settings; connect custom domain; view portal visits & booking sources | [S: T05] |
| Admin | Same as owner for portal settings unless RBAC (Agent 09) restricts; domain connect is owner/admin only | [I] |
| Manager / Foreman | Read booking-source reports; usually no portal-setting edits | [I] |
| Worker | No portal access. Indirect: their "on my way" status and live location feed the tracking page; their name/photo may show there | [S: T11][S: llms][I] |
| Customer (anonymous visitor) | Browse catalog, check area, see availability, start booking. Visit is tracked automatically | [S: T05] |
| Customer (identified, not logged in) | Has typed phone/email in the flow; becomes a lead/abandoned booking if they leave | [S: T10][S: T05] |
| Customer (logged in) | Account area: assets, history, rebook, manage/cancel/reschedule, gift cards, subscriptions, referral code, intake form updates, invoices | [S: T05][S: T01][S: T17][S: llms] |
| Gift-card recipient | Receives a code/link; may redeem on the portal without an account | [S: T05][S: T09][I] |
| AI agent via Bookings MCP | Customer-level actions only — Fieldd ships "Bookings MCP: 100+ actions for booking customers. Built for AI voice apps and custom online booking experiences. Customer-level access only" | [S: llms] |
| System | Edge router (host→tenant), certificate manager, domain verifier, booking saga, abandon detector, attribution recorder, reconciliation jobs | [D] |

**Permission rule [D]:** every public endpoint is tenant-scoped by Host (never by a tenant id the
client sends), and customer-scoped by the portal session. The Bookings-MCP surface is exactly the
public + customer API in §8 with an API-key principal of kind `customer_agent`; it can never read
another customer's data or business-private fields (cost, margins, company add-ons [S: T07]).

---
## 3. Screens

Screens are split into **(A) public booking flow**, **(B) customer account area**, **(C) public
utility pages** (tracking, gift card, short-link landing, errors) and **(D) admin settings** inside
the CRM. Fieldd's exact visual layout is unknown until the owner's walkthrough video; all layout
statements below are `[I]`/`[D]` unless tagged. The owner's swash.co recording is the visual source
of truth for (A)–(C); the Fieldd tutorials are the source for (D).

### 3.0 Global portal chrome (every public page) [D unless tagged]

| Region | Content | Notes |
|---|---|---|
| Header | Business logo (links to business's main website, configurable), business name, "Sign in"/avatar, optional phone "Call us" link | Logo dimensions reserved via width/height attributes → no layout shift |
| Progress | Step indicator "Step 3 of 8 · Add-ons" (text + bar). Hidden on catalog landing | Steps vary by config (§4.2); count recomputed when optional steps are skipped |
| Main | Step content | Max content width ~720px; single column on mobile |
| Sticky summary | Mobile: bottom sheet "1 service · 2 add-ons · $189.00 · Continue". Desktop: right rail cart | Totals always come from server quote (§5.1) |
| Footer | Terms link, privacy link, business contact, optional "Powered by <platform>" badge (removable on higher tier — Fieldd removes its branding only on Pro) | [S: llms] "Pro: … no fieldd branding" |
| Toasts / live region | `aria-live="polite"` for "Slot no longer available", "Saved", etc. | a11y |

Style tokens (colors, font, map style, text overrides) from §3.D1 apply everywhere [S: T05].

### 3.A Public booking flow

The step list below is the **canonical superset**. The tenant's config + the chosen service decide
which steps appear and (within constraints) in what order (§4.2, §10.3). Fieldd states that
"categories act as separate pages in your booking flow" [S: T06], that add-ons are "upsell items your
customers will see when booking through your online booking page" [S: T07], that subscriptions can be
"offered online during booking" [S: T01], that customers answer questions "before they complete the
booking process" [S: T06], that customers are "prompted to accept [terms] before completing a booking"
[S: T05], and that the booking page reflects the service areas where customers can book [S: T11]. The
exact Fieldd order is **unverified** → see §14.

#### A0. Landing / category list
- **Route:** `/` (also `/c/{categorySlug}` deep link).
- **Layout:** hero (business name, short tagline text override, optional cover image); location-mode
  switch if the business has both mobile and shop services ("We come to you" / "Visit our shop")
  [S: T06 categories can be "mobile only"][S: T11 shops][I]; grid/list of categories, each with icon
  [S: T06 "assign it an icon"] or image, name, optional "from $X" (min price from 02).
- **Actions:** select category → A1. "Sign in" → B0. "Rebook" shortcut if logged in and has history.
- **States:** loading = skeleton tiles with fixed aspect ratio; empty (no published services) =
  "Online booking isn't available yet — call/text us" with business contact (never a blank page);
  error = cached last-good catalog served from edge with banner "Some info may be out of date".
- **Mobile:** 1-column cards, 48px min tap height.
- **Single-category shortcut [D]:** if only one category is published, skip A0 and land on A1.

#### A1. Service list (within category)
- **Route:** `/c/{categorySlug}`.
- **Per service card:** image [S: T06], name, short description [S: T06], price (or "from"), duration
  display (optional; some businesses hide it) [S: T06 duration exists][I], badges (Mobile / Shop
  [S: T06 "mobile only"], "Popular", "Subscription available"), "Select" button. Multi-select allowed
  if tenant enables multiple services per booking [S: T15 "add extra services" in CRM][I for portal].
- **Never shown:** cost, margin, company add-ons [S: T06 cost "to track my profit"][S: T07].
- **States:** empty category is hidden from A0 [D].

#### A2. Service options / variant (conditional)
- Shown only when the service has pricing variants that depend on a customer choice (e.g. vehicle
  size, number of rooms, sq ft). Agent 02 owns variant definitions. [I — common in detailing/cleaning]
- If price depends on the **asset** (vehicle class), this step merges with asset selection (A9) and
  moves before pricing. Logged-in customers pick a saved asset; guests choose a class or enter the vehicle.
- **Fields:** radio group of variants (label, price delta, duration delta).

#### A3. Add-ons / upsells
- **Content:** customer add-on categories (name + image) [S: T07], each add-on with image, name,
  description, price, optional duration [S: T07]; quantity stepper when the add-on is a product with
  min/max quantities [S: T07 "min or max quantities"]; out-of-stock add-ons disabled with label
  [S: T07 "stock levels"][I for UX].
- Only add-ons paired with the selected service(s) are shown (default: all services) [S: T07].
- Duration of selected add-ons is added to the booking duration and re-queried for availability
  [S: T07 "extra time will be added to the overall booking to prevent overlaps"].
- **Actions:** toggle add-on, change quantity, "Skip", "Continue". Running total updates (server quote,
  debounced 250 ms, optimistic display marked "updating…").
- **States:** no eligible add-ons → step auto-skipped (not shown as a blank page) [D].

#### A4. Frequency / subscription upsell (conditional)
- **Recurring option:** if the service has recurring booking enabled, customer can choose a frequency
  from the suggested list with the configured discount; if disabled, customers cannot self-book
  recurring [S: T06].
- **Subscription upsell:** packages flagged "upsell during booking" are shown as a card: name,
  description, price per cycle, commitment options with discount, included services/discounts (e.g.
  "20% off all services + 1 free add-on monthly") [S: T01]. Choosing one switches the order to
  "subscribe + first visit" (06 computes price; 03 auto-schedules if the package has automatic
  scheduling [S: T01]).
- **Actions:** "One-time", "Every 2 weeks (save 10%)", "Join <package>", "No thanks".
- Fieldd placement of this step is unknown [I] → verify.

#### A5. Location: address & service-area check (mobile) / shop choice (shop)
- **Mobile path fields:** address autocomplete (single line → structured: line1, line2/unit, city,
  region, postal, country, lat/lng, place id); unit/apt; access notes (gate code, parking) as a
  booking question if configured. Map preview with the pin [S: T05 "the map" is styleable][I].
- **Check:** call 02 `CheckServiceArea(lat,lng, serviceIds)` → `{inArea, zoneId, travelFeeCents,
  taxRateBps, workersAvailable}`. Travel charge is a fixed amount per zone applied to mobile services
  [S: T11]; zone tax rate may differ [S: T11].
- **Out of area UX [D]:** "We don't currently service 123 Main St." + options: (a) try another address,
  (b) choose a shop if shop services exist, (c) "Ask us anyway" → creates a lead with the address
  (Agent 07) so the business can override by booking manually in the CRM, which Fieldd explicitly
  allows [S: T11 "you can always override it by manually booking … through the Field CRM"].
- **Shop path:** list of shops (name, address, distance, hours), sorted by distance from an entered
  postcode/geolocation; shops beyond the business's distance filter are hidden or marked unavailable
  [S: T11 "distance-based filtering for shop scheduling"]. Custom icons per area/shop on the booking
  page [S: T11].
- **States:** geocoder down → manual structured form accepted; area check retried server-side at
  commit; if still unverifiable, booking proceeds flagged `area_unverified` for staff review [D].
- **Saved addresses:** logged-in customers pick from saved addresses (07) with "Add new".

#### A6. Date & time
- **Timed services:** calendar strip (next 14 days, swipe for more) + slot list grouped
  Morning/Afternoon/Evening. Days with no availability are visibly disabled (not hidden) with
  `aria-disabled`. First available slot pre-highlighted (mirrors CRM "first available time for the
  first available worker" [S: T15]).
- **Route services:** customer picks a **day**, not a time; label "Anytime Thursday" (customizable at
  Settings → Customer hub → Style → Text → Routes text) [S: T02].
- **Arrival windows:** optional; Fieldd's pitch is that realistic durations let you avoid relying on
  arrival windows [S: T06], so default = exact start time; window display ("Arrive 9–10 am") is a
  tenant option [I/D].
- **Preferred worker:** optional "Any professional" vs named worker (if tenant allows) [S: T04 quote
  has "preferred worker" field][I for portal].
- **Timezone:** times shown in the *service location's* timezone with a label if it differs from the
  browser's ("Times shown in Central Time") [D].
- **Hold:** selecting a slot creates a short hold via 03 (§10.5). Countdown shown only in the last
  2 minutes ("We're holding 10:00 for you · 1:45").
- **States:** loading skeleton; "No times in the next 30 days" → join waitlist / request a time
  (lead) [D]; slot taken between view and click → inline message + nearest 3 alternatives.

#### A7. Booking questions
- Global questions + service-specific questions; per-service toggle to suppress global questions
  [S: T05][S: T06]. Answers belong to *this booking* (job), unlike intake form answers (A10).
- **Question types [I/D]:** short text, long text, number, yes/no, single choice, multi choice,
  dropdown, date, photo/file upload (e.g. photo of stain/damage), address-like text. Each: label,
  help text, required flag, conditional visibility (show if service ∈ X or answer Y = Z), max length.
- **Validation:** required, type, min/max, file type (jpeg/png/heic/pdf) and size (≤10 MB each, ≤5
  files) [D].

#### A8. Contact / account
- **Fields:** first name (required), last name (required), mobile phone (required by default; E.164
  normalized; country picker defaulting to business country), email (required by default),
  SMS consent checkbox (unchecked by default, separate for transactional vs marketing where law
  requires), "Create an account" implicit via passwordless (OTP) [D].
- **Existing customer detection [D]:** if phone/email matches an existing customer, offer "Welcome
  back — send me a code to load my saved vehicles and addresses". Never reveal that an account
  exists to someone who can't prove ownership (show the same neutral prompt either way).
- **Capture timing:** phone/email are sent to the server on field blur (debounced) so an abandoned
  attempt still has contact details — this is how Fieldd can follow up with drop-offs via "magic
  data" [S: T10][S: T05]. Must be disclosed in privacy notice (§5.10).
- Fieldd: "Customers can now visit your booking page, create an account, and book their first
  service" [S: T05] — account creation is part of first booking; whether guest checkout exists is
  unknown → verify.

#### A9. Assets (vehicle / home / equipment)
- Enabled in settings "Customer assets" [S: T05]. Asset types depend on industry: cars, homes,
  equipment [S: T05].
- **Vehicle fields:** year, make, model (cascading lookup), trim (optional), color, plate, VIN
  (optional), photo, nickname; VIN or plate lookup [S: llms "vehicle lookup by make, model, and year, or
  by VIN and plate; vehicle photos and colors"]. Multiple vehicles per booking/customer ("garage")
  [S: llms].
- **Home fields [I]:** property type, sq ft, bedrooms, bathrooms, stories, pets, access notes.
- Saved to the customer profile (07) so they aren't re-entered next time [S: T05].
- Logged-in: pick from saved assets (checkbox list for multi-asset services) + "Add new".

#### A10. Intake form (first booking or when incomplete)
- Long-term profile questions; answers saved to the customer's profile; customer can log in later to
  update [S: T05]. Shown only when required intake questions are unanswered for this customer [D].

#### A11. Review & terms
- **Content:** line items (service, variant, add-ons × qty, travel fee [S: T11], discount, gift card,
  tax [S: T11 zone tax], deposit due now, balance due later), date/time, address/shop, assets,
  answers summary with "Edit" links back to steps.
- **Promo / gift card / referral code field** (single "Have a code?" input that tries discount →
  gift card → referral, or separate inputs per tenant config) [S: T09][S: T16][S: llms referral codes
  "work online"][D].
- **Terms checkbox:** "I agree to the <Terms & Conditions>" link (opens in new tab/sheet), required
  when the business configured a terms link [S: T05]. Cancellation policy summary sentence shown next
  to it (e.g. "Free cancellation up to 24 h before; $25 fee after") [D].

#### A12. Payment
- Behaviour comes from business payment settings (05): pay after job, card on file (charge now or
  after job), deposit, prepayment, pre-authorization [S: T16][S: T04].
- **UI:** 05's embedded payment element (card fields hosted by processor so card data never touches
  our servers — PCI scope SAQ-A) [S: llms "PCI-compliant"][I]; Apple Pay / Google Pay buttons when
  available [S: llms "Apple Pay"]; saved cards for logged-in customers; tip **not** asked here by
  default (tips are POS/after job [S: llms "tipping"][I]).
- **Copy:** "You won't be charged until after your service" / "A $50 deposit is due today" / "We'll
  place a temporary $X hold on your card" — chosen by mode.
- **States:** processing (button disabled, spinner, prevents double submit); 3-D Secure challenge;
  declined → inline error, keep everything entered; network loss → resume (§10.6).

#### A13. Confirmation
- **Content:** "You're booked!" + job number (single job ID that later carries invoice [S: T04]),
  date/time, address, assigned pro if already dispatched (auto-dispatch [S: llms]) else "We'll assign
  a pro", what happens next, "Add to calendar" (.ics + Google link), "Manage booking" link, account
  nudge (set up account if passwordless not completed), share/referral prompt [S: llms referral].
- **Messages:** confirmation email/SMS sent by 08 off `portal.booking.confirmed`; reminders later
  [S: T15 "send reminders directly to the customer"].
- Page is reload-safe and bookmarkable: `/b/{publicBookingToken}`.

#### A14. Request-only / quote path (conditional) [I/D]
- Services flagged "request quote" (no fixed price) end at "Send request" instead of payment; 04
  creates a quote on the same job ID; the quote email includes a "Book online" button that returns
  the customer to A6 with everything prefilled [S: T04 "book online button so the customer can
  self-schedule if they accept the quote"].

### 3.B Customer account area ("My account")

| Screen | Route | Content / actions | Source |
|---|---|---|---|
| B0 Sign in | `/account/sign-in` | Phone or email → 6-digit OTP (SMS/email) or magic link; "Trust this device" (30-day refresh token). No passwords by default [D]. Social login optional later | [S: T05 "create an account"][D] |
| B1 Home | `/account` | Next appointment card (manage, track when en route), quick "Book again", active subscription summary, gift card balance, referral code | [S: T05][S: T01][S: llms] |
| B2 Appointments | `/account/appointments` | Upcoming / Past tabs. Each: service, date, address, status, total, pro. Actions: View, Reschedule, Cancel, Rebook, Pay invoice, Download receipt | [S: T05 "rebook previous services"][S: T17 invoices paid through the customer portal] |
| B3 Appointment detail | `/account/appointments/{jobPublicId}` | Line items, answers, photos shared by business (if enabled), invoice/receipt, cancellation policy text, tracking link when en route | [I] |
| B4 Reschedule | `/account/appointments/{id}/reschedule` | Same date/time component as A6 with same service duration and location; shows fee if within policy window | [S: T05][I] |
| B5 Cancel | `/account/appointments/{id}/cancel` | Policy summary with computed fee (§5.6); reason picker + free text (feedback) [S: T05 "collect feedback"]; optional "Would you rather reschedule?" interstitial [S: T05 "choose whether or not to prompt them to reschedule"]; confirm | [S: T05] |
| B6 Assets ("Garage" / "Properties") | `/account/assets` | List, add, edit, archive; photos; service history per asset | [S: T05][S: llms "garage"] |
| B7 Addresses | `/account/addresses` | Saved addresses; default flag | [S: llms multiple addresses][I] |
| B8 Profile & intake | `/account/profile` | Name, phones, emails, notification preferences, intake form answers editable | [S: T05 "log in to update the information later"] |
| B9 Payment methods | `/account/payment-methods` | Cards on file (brand, last4, expiry), add/remove/default; cannot remove a card backing an active subscription or pre-auth without replacement | [S: llms card on file][I] |
| B10 Subscriptions | `/account/subscriptions` | Browse self-purchasable packages, buy, view active (next billing, included services, remaining credits), commitment end, cancel/pause per 06 rules | [S: T01] |
| B11 Gift cards | `/account/gift-cards` and public `/gift-cards` | Buy (amount presets + custom, design, recipient name/email/phone, message, send now/scheduled), view sent/received, check balance | [S: T05 "buy and send gift cards"][S: T09][S: llms AI gift card design] |
| B12 Referrals | `/account/referrals` | Personal referral code + share link, rewards earned; code also enterable at checkout | [S: llms "referral codes … work online and in the customer app"] |
| B13 Invoices & quotes | `/account/invoices`, `/q/{token}`, `/i/{token}` | View/accept quote → book; pay invoice | [S: T04][S: T17] |
| B14 Messages (optional) | `/account/messages` | Thread with business (08 inbox) | [S: llms live chat][I] |

Branded **native customer app** (iOS/Android) is a Scale-tier Fieldd feature [S: llms]; it is out of
scope for Phase 1 here — we design the account area as an installable PWA so a native shell can wrap
it later [D].

### 3.C Public utility pages

| Page | Route | Notes |
|---|---|---|
| C1 Live tracking ("on my way") | `/t/{trackingToken}` | Map with worker's live position + ETA, worker first name/photo (09), vehicle (llms: "the vehicle on every job"), job status timeline (on my way → arrived → in progress → complete). Token is unguessable, expires 2 h after job completion; no login needed [S: llms "on-my-way tracking"][S: T11 "live tracking … real-time updates on your arrival"][D] |
| C2 Manage-booking link | `/b/{publicBookingToken}` | Lets a non-logged-in customer view/cancel/reschedule from the email link; sensitive actions require OTP step-up if policy says so [D] |
| C3 Short-link landing | `/s/{slug}` (and `/go/{code}`) | Redirects/prefills a specific service, skipping the category pages [S: T06] |
| C4 Gift card redeem | `/gift/{code}` | Shows balance & design, "Book now with this gift card" (applies code to session) [I] |
| C5 Domain not connected | any unknown/unverified host | Neutral "This booking page isn't set up yet" (no tenant data leaked) [D] |
| C6 Maintenance / degraded | — | "Online booking is temporarily limited — leave your details and we'll confirm by text" (captures lead) [D] |
| C7 404 / expired link | — | Branded, links back to `/` |

### 3.D Admin settings (inside the CRM) — "Settings → Online Scheduling / Customer hub"

Fieldd's sections as narrated, in order: **Style → (Save) → Installation → Booking questions →
Intake form → Customer assets → Terms → Cancellation policy → Third-party analytics → Booking sources
→ Additional settings**; "any updates … reflected immediately on your customer portal" [S: T05].
Video 05's description chapters confirm the order [S: T05d]. (Note: the description file for video
04 contains chapter titles about the customer portal that do not match video 04's content — likely a
copy-paste error on Fieldd's YouTube; it nevertheless repeats "Installation methods (link, embed,
QR)" [S: T04d].)

#### D1. Style
- **Fields [S: T05 "colors, fonts, the map, and even the text"]:**
  - Brand colors: primary (buttons/links), accent, background, surface, text — color pickers with hex
    input; **auto contrast check** (WCAG AA 4.5:1 for text, 3:1 for UI) with warning and "Fix
    automatically" which derives an accessible shade [D].
  - Logo (SVG/PNG ≤ 1 MB; we generate 1×/2×/WebP/AVIF), favicon, cover/hero image (≤ 5 MB → resized).
  - Font: curated list (system stack default for speed + ~12 self-hosted web fonts, subsetted,
    `font-display: swap`, metric-matched fallback to avoid CLS) [D].
  - Corner radius (sharp/rounded/pill), button style, light/dark/auto theme for portal [D].
  - Map: style (standard/muted/dark), pin color, show/hide map on address step [S: T05 "the map"].
  - **Text overrides:** keyed list of every customer-visible string with default value and override
    field, grouped (Landing, Services, Add-ons, Location, Date & time, **Routes text** e.g. "Anytime
    {weekday}" [S: T02], Questions, Account, Payment, Confirmation, Emails-in-portal). Plain text with
    `{placeholders}` only; no HTML (XSS) [D]. Per-locale overrides when multi-language arrives [D].
- **Preview:** live side-by-side preview (mobile/desktop toggle) using the draft config [D].
- **Save:** Fieldd moves you to Installation after Save [S: T05]. We: Save draft / Publish; publish
  invalidates edge cache within ≤ 5 s (§10.8).

#### D2. Installation
Fieldd offers: copy link (for website, Google Business Profile, emails), "brand the link to your own
domain by following the steps below", iframe embed, book-now button, QR code download [S: T05].
Our screen:
1. **Your booking link** — platform host URL with Copy button + "Open" + share buttons [S: T05].
2. **Use your own domain** (wizard, §4.1): status pill (Not connected / Waiting for DNS / Verifying /
   Issuing certificate / Active / Action needed), the exact DNS records to add (table: Type, Host/Name,
   Value/Target, TTL) with copy buttons, provider-specific instructions (accordion: GoDaddy, Namecheap,
   Cloudflare, Squarespace/Google Domains, Wix, Shopify, IONOS, Route 53, "Other"), "Check now" button,
   last-checked time, diagnostics output (what we see in DNS vs what we expect) [D].
3. **Add to your website**:
   - "Book now" link button — HTML snippet + platform-specific guides (WordPress, Wix, Squarespace,
     Webflow, Shopify, GoDaddy builder) [D].
   - Inline embed (iframe) — snippet with auto-resize loader; height, start page (category/service),
     hide header option [S: T05 "insert an iframe"][D].
   - Popup button — loader snippet + button label/color/position (inline or floating corner)
     [S: T05 "book now button"][D].
4. **QR code** — PNG/SVG/PDF download, size presets (business card, flyer, yard sign), foreground
   color, logo in center (with error correction H), target (home/category/service), campaign tag
   [S: T05 "download a QR code to place on print materials"][S: llms "new QR code booking options"].
5. **Google Business Profile** — instructions to set the "Booking"/"Appointment" link to the booking
   URL with `?src=gbp` [S: T05 mentions Google business profile][D].
6. **Short links** — list of per-service links (from 02's service editor "booking short link"
   [S: T06]) + custom links (name, target, prefill, UTM preset) [D].

#### D3. Booking questions [S: T05][S: T06]
- Table of global questions: drag to reorder, label, type, required, applies to (all / selected
  categories / selected services), visibility conditions, active toggle. Service-specific questions
  are edited in the service editor (02) but stored in this module's question model (§7) with
  `scope=service` [D].

#### D4. Intake form [S: T05]
- Same builder as D3 but answers persist to the customer profile (07) and are editable by the
  customer later. Options: "ask on first booking only" (default) / "ask until complete" / "never ask
  online (staff only)" [D].

#### D5. Customer assets [S: T05]
- Enable assets; asset type (Vehicle / Property / Equipment / Custom); which fields are shown and
  required; whether asset selection is required for categories X; max assets per booking; vehicle
  lookup on/off (VIN/plate) [S: llms]. Field definitions are owned by 07; this screen controls only
  portal visibility/requirement [D].

#### D6. Terms [S: T05]
- Terms URL (external link) **or** hosted terms text (rich text, versioned) [S: T05 says "add a link";
  hosted text is D]. Toggle "Require acceptance before booking". Each publish creates a new
  `TermsVersion` (immutable) so acceptances reference exactly what was shown.

#### D7. Cancellation policy [S: T05]
- Allow online cancellation (on/off); allow online reschedule (on/off); free-cancellation window
  (hours before start, e.g. 24); late-cancellation fee (fixed cents or % of total, cap); no-show
  fee (applied by staff, displayed in policy); deposit refundability (refund / keep / convert to
  credit); reschedule limit (max N reschedules, min notice); **collect feedback on cancel** (reason
  list editable + free text) [S: T05]; **prompt to reschedule instead** (on/off) [S: T05]; policy text
  shown to customers (auto-generated from settings, overridable) [D].

#### D8. Third-party analytics [S: T05]
- Integrations by ID only: Google Analytics 4 measurement ID, Google Tag Manager container ID,
  Google Ads conversion ID/label, Meta Pixel ID, TikTok Pixel ID, Microsoft UET tag ID; toggle
  "send conversion value"; consent mode (require cookie consent in EU/UK; default "deny until
  accepted" there) [D]. Note: Fieldd "automatically tracks your customer portal visits with no setup
  needed" [S: T05] — our built-in analytics also needs no setup.

#### D9. Booking sources [S: T05]
- Shows where customers find the booking page; tracked automatically and viewable on the dashboard
  [S: T05]. Our screen: table of sources (Website button, Embed, Popup, QR (per QR code), Short link
  (per link), Google Business Profile, Email, SMS, Social, Direct, Referral code, Campaign (utm)),
  visits, sessions started, contact captured, bookings, conversion %, revenue — date range filter.
  "Add custom source" creates a tagged link [D]. Dashboard tile is rendered by 08.

#### D10. Additional settings [S: T05 — contents unknown]
Proposed list [I/D] (verify which exist in Fieldd): booking lead time (min notice, e.g. 2 h) and
horizon (max days ahead, e.g. 60); slot interval display (15/30/60); allow multiple services per
booking; allow guest checkout vs require account; require phone / require email; show prices
(always / "from" / hide until address); show durations; show worker choice; allow customer notes;
booking confirmation mode (instant vs "request — business approves"); business hours message;
holiday closure banner; language/locale; currency display; "Powered by" badge (tier-gated);
redirect URL after booking (e.g. business's thank-you page for ad conversion); allowed embed
origins; SEO title/description/OG image; noindex toggle; maintenance mode.

---

## 4. Workflows

### 4.1 How the booking site runs on the business's own subdomain

Fieldd's only on-record statement is: "You can also brand the link to your own domain by following
the steps below" [S: T05]. Everything else in this section is how a hosted multi-tenant booking app
**most likely** does it [I], and how **we** will do it [D]. The owner's reference, swash.co, runs
its scheduling on a subdomain [BRIEF]; confirm the mechanics with the checks in §14.

#### 4.1.1 The moving parts (big picture)

```
 Customer's phone
      │  https://book.acmedetailing.com/
      ▼
 ┌────────────────────┐   1. DNS: book.acmedetailing.com  CNAME  t-7f3k.edge.book-platform.example
 │ Business's DNS host │      (record the BUSINESS adds at GoDaddy/Cloudflare/etc.)
 └────────────────────┘
      │  resolves to our anycast edge IPs
      ▼
 ┌──────────────────────────────────────────────────────────────┐
 │ Platform edge (CDN / reverse proxy, many POPs)               │
 │ 2. TLS handshake: SNI = book.acmedetailing.com               │
 │    → cert store hit? serve.  miss? → on-demand ACME issuance │
 │      ONLY if host is in verified-domains allowlist ("ask")   │
 │ 3. HTTP: Host header → tenant lookup (edge KV, cached)       │
 │ 4. Serve cached shell/catalog; proxy API calls to origin     │
 └──────────────────────────────────────────────────────────────┘
      │  X-Tenant-Id (set by edge, never by client), X-Original-Host
      ▼
 Origin: portal app + booking API (multi-tenant, one codebase)
```

Every tenant also gets the **platform host** `{slug}.book-platform.example` which works from minute
one, needs no DNS work, and keeps working forever as the fallback [D].

#### 4.1.2 DNS record design [I → D]

| Case | Record the business adds | Why |
|---|---|---|
| Subdomain (recommended, owner's #1 requirement) — `book.example.com` | `CNAME book → t-{tenantShortId}.edge.book-platform.example` | Per-tenant CNAME target lets us move one tenant (e.g. to another region/edge) without the business touching DNS; target is a name we control that resolves to anycast IPs [I] |
| Ownership pre-verification (optional, recommended) | `TXT _platform-verify.book → "pv=<random 32-char token>"` | Lets us verify ownership **before** cutting traffic over (zero-downtime migration from another booking tool), and is required if the business's DNS is proxied (see Cloudflare row) [I] |
| Apex domain — `example.com` (discouraged) | `A`/`AAAA` to our published static anycast IPs, or provider `ALIAS/ANAME/CNAME-flattening` to the CNAME target | Apex cannot hold a CNAME (RFC 1034 conflicts with SOA/NS/MX). Apex usually hosts the business's main website, so we steer to a subdomain [I] |
| Business DNS on Cloudflare with proxy ("orange cloud") on | Must be **DNS only** ("grey cloud") for the CNAME, **or** we validate via TXT/DNS-01 delegation | A proxied record makes the browser talk to the business's Cloudflare, which must then forward to us with the right Host — common source of "too many redirects"/525 errors [I] |
| CAA records present on the business's domain | Add `CAA 0 issue "<our CA>"` (we tell them which CA(s)) | If `example.com` has CAA allowing only another CA, our ACME issuance is refused [I] |
| `_acme-challenge` delegation (optional, for wildcard or proxied setups) | `CNAME _acme-challenge.book → book-example-com.acme.book-platform.example` | Lets us answer DNS-01 challenges in our own zone [I] |

Suggested hostnames shown in the wizard: `book.`, `booking.`, `schedule.`, `app.`, `my.` (for the
account area). Fieldd tenants appear to use patterns like this; verify the swash.co hostname [I].

#### 4.1.3 TLS: automatic certificate issuance [I → D]

1. **On-demand issuance at first handshake**: when a TLS ClientHello arrives with SNI
   `book.example.com` and we have no cert, the edge asks the control plane "may I issue for this
   name?" (the *ask* hook). Answer is YES only if a `CustomDomain` row exists for that exact
   hostname with status ∈ {`verifying`, `issuing`, `active`, `renewal_due`} **and** DNS currently
   points at us. Otherwise the handshake fails fast (no cert for random names → stops attackers
   pointing junk domains at us to burn CA rate limits) [I — this is how "on-demand TLS" servers such
   as Caddy are normally run].
2. **Pre-emptive issuance (better) [D]**: we don't wait for the first visitor. As soon as the
   verifier sees the CNAME resolving to us, the control plane requests the certificate (ACME HTTP-01
   served by our edge at `/.well-known/acme-challenge/*`, or TLS-ALPN-01, or DNS-01 when delegated).
   First real visitor never waits for issuance.
3. **CA strategy**: primary ACME CA (e.g. Let's Encrypt) + secondary ACME CA as automatic fallback
   when the primary errors or rate-limits [I]. Respect CA rate limits (per registered domain per
   week, failed validations per hostname per hour, new orders per account per 3 hours) — we
   serialize and back off per hostname [I].
4. **Renewal**: at 2/3 of lifetime (or per ACME Renewal Information when offered by the CA); public
   CA certificate lifetimes are shrinking industry-wide, so renewal must be fully automatic and
   monitored, never manual [I]. Failure → `renewal_due` → retries with backoff → alert owner
   7 days before expiry with exact DNS diagnosis → at expiry, the edge keeps redirecting HTTP→
   platform host so links still work (see 4.1.6).
5. **Storage**: private keys encrypted at rest (KMS), replicated to all edge POPs; OCSP stapling;
   TLS 1.2+ only, HTTP/2 + HTTP/3, HSTS (`max-age` modest, **no** `includeSubDomains`, **no**
   preload — the parent domain is the business's, not ours) [D].
6. **Managed alternative**: CDN "SaaS custom hostname" products do steps 1–5 for us (customer
   CNAMEs to our CDN-hosted fallback origin; CDN validates & issues). Either path satisfies this spec;
   stack decision deferred [I].

#### 4.1.4 Tenant resolution by Host header [D]

1. Edge receives request; computes `host = lowercase(stripPort(stripTrailingDot(HostHeader)))`;
   IDN → punycode.
2. Reject if `host` ≠ TLS SNI (domain-fronting guard) for custom hosts.
3. Lookup in edge KV `host → {tenantId, hostKind: platform|custom, canonicalHost, status, configVersion}`;
   KV is pushed from the control plane on every change (push, not pull; ≤ 5 s global propagation)
   with a 60 s local TTL fallback.
4. Platform hosts are parsed by pattern `{slug}.book-platform.example` (slug lookup in same KV).
5. Not found / not active → C5 "not set up" page (HTTP 404, `noindex`, no tenant data).
6. Found → set `X-Tenant-Id`, `X-Host-Kind` headers (stripped from any client input) and serve.
7. **Canonical host rule**: when a custom host is `active`, requests to the platform host for
   *page* routes 301-redirect to the custom host (keeps SEO and cookies on one origin); API routes,
   webhooks and tracking links are served on both without redirect. If the custom domain becomes
   unhealthy, canonical automatically flips back to the platform host (4.1.6).
8. Origin services **never** derive tenant from body/query params on public routes.

#### 4.1.5 Verification flow in settings (owner's view) [D]

1. Owner opens **Settings → Online booking → Installation → Use your own domain** and types
   `book.example.com`.
2. Client + server validation: valid hostname (RFC 1123 labels, ≤ 253 chars), has at least 3 labels
   unless apex mode chosen, not on our own domains, not on the Public Suffix List as a bare suffix,
   not already claimed by another tenant (if claimed and verified elsewhere → "This domain is in use
   by another account. Contact support." — never reveal which tenant), not a known free-hosting
   suffix we refuse.
3. Server creates `CustomDomain{status: pending_dns}` with generated `verifyToken` and per-tenant
   CNAME target; returns the records table.
4. UI shows records + provider detection: we look up the domain's NS records and show the matching
   provider's instructions first ("Your DNS is managed by Cloudflare — here's exactly where to click").
5. Background verifier polls (every 1 min for 15 min, every 5 min for 2 h, hourly up to 72 h, then
   daily for 30 days, then gives up → `abandoned`), plus "Check now" button (rate-limited 1/10 s).
   Uses multiple public resolvers + authoritative NS query to dodge stale caches.
6. When CNAME (or A/AAAA in apex mode) resolves to our edge **and** (TXT token matches **or** CNAME
   is direct) → `verifying` → issue cert (4.1.3 step 2) → `issuing` → on success `active`.
7. On `active`: edge KV updated; owner emailed "book.example.com is live"; installation snippets,
   QR codes (if they target the canonical host via short link) and notification links (08) switch to
   the custom host automatically; event `portal.domain.activated`.
8. Owner can **remove** the domain any time → `removed`; edge stops serving it after 24 h grace
   (redirect to platform host during grace); cert deleted.
9. One custom booking host per tenant on budget tier; extra hosts (e.g. per brand/location,
   `my.` for accounts) on higher tiers [D].

#### 4.1.6 What happens when DNS is wrong (and how we fail safe) [D]

| Situation | What the visitor sees | What we do |
|---|---|---|
| Record never added / typo in name | Browser "site can't be reached" (NXDOMAIN) — **we cannot serve anything** | Wizard keeps status `pending_dns`, diagnostics "No record found for book.example.com"; we never switch generated links to the custom host until `active` |
| CNAME points somewhere else (old booking tool, website builder) | Old site | `dns_mismatch` with "We see it pointing to X; it should point to Y" |
| Typo'd target (e.g. `edge.book-platfrom…`) | NXDOMAIN or other site | Same; Levenshtein hint "Did you mean …?" |
| Points to us, not verified/claimed by anyone | TLS failure (no cert) or C5 page over HTTP | Ask hook refuses issuance; no data leak |
| Cloudflare proxied record | Redirect loops / 52x errors | Detect Cloudflare IPs in resolution → specific instruction "turn the cloud grey" |
| CAA blocks our CA | Cert issuance fails | `error_caa` with exact record to add |
| Active domain later deleted/changed by business (website redesign, DNS migration) | Visitors to old links hit NXDOMAIN/other site | Daily re-check → `detached`; owner alerted immediately (email + in-app); canonical flips to platform host; **all outgoing links (confirmations, reminders, tracking, invoices) switch to platform host** so existing customers aren't stranded; keep cert until expiry in case it's a short outage |
| Cert renewal failing | Nothing yet (old cert valid) | `renewal_due` alerts from 21 days before expiry; at expiry HTTPS fails for that host → links already switched to platform host |
| Business's domain expired | Nothing reachable | Same as detached |
| DNSSEC broken on business domain | SERVFAIL for some resolvers | Detect via validating resolver; specific message |
| Two tenants claim same host (sold business, agency) | — | First verified wins; second needs TXT proof → support-assisted transfer; transfer is atomic in KV |

**Key safety principle [D]:** a custom domain is a *vanity alias*, never a single point of failure.
Every link the platform generates is computed by `bookingBaseUrl(tenant)` which returns the custom
host only while it is `active` and healthy, else the platform host.

#### 4.1.7 Sessions, cookies and payments across hosts [D]

- Customer session cookie is **host-only** (`__Host-` prefix, `Secure`, `HttpOnly`, `SameSite=Lax`),
  so a session on `book.example.com` is independent of the platform host. Switching canonical host
  re-authenticates via a one-time token exchange (`/auth/handoff?t=…`, 60 s TTL, single use).
- The platform host suffix `book-platform.example` must be added to the **Public Suffix List** so
  tenant A's pages cannot set cookies readable by tenant B [I — standard practice for multi-tenant
  subdomains].
- The portal origin is **separate from the admin CRM origin** so any XSS in tenant-controlled
  content cannot reach staff sessions.
- **Apple Pay** on the web requires each domain serving the payment sheet to be registered with the
  processor/Apple (domain-association file at `/.well-known/apple-developer-merchantid-domain-association`).
  On `active`, we call 05's `RegisterWalletDomain(host)`; until registered, Apple Pay button is hidden
  on that host (card still works) [I].
- Processor 3-D Secure, Google Pay and hosted card fields work on any HTTPS origin [I].

#### 4.1.8 Website "Book now" buttons, embed, popup, QR, short links, GBP, deep links [D unless tagged]

Fieldd's installation options: simple link (website, Google Business Profile, emails, "wherever it
works best"), own-domain link, iframe, book-now button, QR download [S: T05]; per-service booking
short link that skips the full flow [S: T06]; QR code booking options [S: llms]; postcard QR scans
show up on the lead board [S: T20].

1. **Plain link button (recommended default).** `<a href="https://book.example.com/?src=website"
   class="…">Book now</a>`. Zero JS on the business site, best performance, best for Apple Pay and
   3-D Secure, full-page experience on phones. The settings screen gives copy-paste HTML and
   builder-specific steps.
2. **Inline embed (iframe).** Snippet:
   ```html
   <div data-platform-booking="acme" data-start="/c/detailing" style="min-height:640px"></div>
   <script async src="https://book-platform.example/embed/v1.js"></script>
   ```
   Loader (≤ 5 KB gzipped, no dependencies) creates an iframe pointed at the canonical host with
   `?embed=1&src=embed&parent=<origin>`; listens to `postMessage({type:'resize',height})` from the
   portal (origin-checked) to grow the iframe — no inner scrollbars; reserves `min-height` so the host
   page doesn't shift (CLS). `allow="payment"` set on the iframe for wallets. Lazy (IntersectionObserver)
   when below the fold.
   **Known iframe limits:** Safari/Firefox block third-party cookies → logged-in state inside an
   embed on a *different* site is unreliable. Mitigations: partitioned cookies (CHIPS) for the embed
   session; account actions open in a new tab ("Manage my bookings ↗"); booking itself never depends
   on a cookie (session id kept in memory + `sessionStorage`). If the business's site is on the same
   registrable domain as the custom host (`example.com` + `book.example.com`), cookies are
   first-party — another reason to recommend the subdomain.
3. **Popup ("book-now button") mode.** Same loader with `data-mode="popup"`: renders a button (or
   binds to any element with `data-platform-book`) that opens a full-screen modal iframe on mobile /
   centered 480×760 modal on desktop; focus-trapped, `Esc` closes, restores focus, `history.pushState`
   so Back closes the modal. Pre-connect (`<link rel=preconnect>`) on hover/touchstart so it opens
   fast. If the loader fails (blocked by ad-blocker/CSP), the button's `href` falls back to the plain
   link — never a dead button.
4. **QR code.** QR encodes a short link `https://book.example.com/q/{qrId}` (not the long URL) →
   destination editable later without reprinting; scan increments `qr_scans` and sets
   `src=qr:{qrId}`. Downloads: SVG (print), PNG 1024/2048, PDF with bleed. Logo overlay uses error
   correction level H; contrast enforced (dark on light). Postcard/flyer QR codes per campaign (08)
   resolve the same way and create a lead on scan only if the visitor submits contact data
   [S: T20 "once a customer scans the QR code on your postcard their information will show up here in the lead board" — implies contact capture after scan][I].
5. **Per-service short links.** Each service has a booking short link that "will take the customer
   directly to this specific service without going through the full booking flow" [S: T06]. Ours:
   `https://book.example.com/s/{serviceSlug}` (stable, human readable, slug owned by 02) plus
   tenant-defined custom links `https://book.example.com/go/{code}` with saved prefill and UTM preset.
   Short links resolve server-side (302 for custom links whose destination can change, 301 never —
   so edits are never cached forever by browsers).
6. **Google Business Profile.** The business pastes `https://book.example.com/?src=gbp` into GBP's
   booking/appointment link field [S: T05 "place it on your … Google business profile"]. Reserve with
   Google (native Google booking) is a separate partner integration — not in Fieldd sources; open
   question.
7. **Deep links with preselection.** Query contract (all optional, unknown params ignored, invalid
   values ignored with no error):
   | Param | Meaning | Example |
   |---|---|---|
   | `category` | category slug | `category=detailing` |
   | `service` | service slug(s), comma-sep | `service=full-detail` |
   | `variant` | variant key | `variant=suv` |
   | `addons` | add-on slugs with optional qty | `addons=pet-hair,ozone:1` |
   | `mode` | `mobile` \| `shop` | `mode=shop` |
   | `shop` | shop slug | `shop=north-austin` |
   | `zip` / `address` | prefill location (re-checked) | `zip=78704` |
   | `date` | `YYYY-MM-DD` start date for calendar | `date=2026-12-03` |
   | `worker` | preferred worker public slug (if allowed) | `worker=sam` |
   | `promo` | discount code (validated at A11) | `promo=FALL20` |
   | `ref` | referral code | `ref=JANE7K` |
   | `gift` | gift card code | `gift=…` |
   | `package` | subscription package slug | `package=monthly-wash` |
   | `src` | booking-source key | `src=website`, `src=qr:abc` |
   | `utm_*`, `gclid`, `gbraid`, `wbraid`, `fbclid`, `msclkid`, `ttclid` | attribution | — |
   | `embed`, `parent` | set by loader | — |
   | `lang` | locale | `lang=es` |
   Preselected steps are skipped only if valid and still available; the progress bar shows them as
   done with Edit links.
8. **Booking-source attribution** [S: T05 "booking source section … where your customers are finding
   your booking page … tracked for you and can be viewed directly from your dashboard"]:
   - On the first request of a visit, the edge/portal records `AttributionTouch{src, utm_*, click ids,
     referrer host, landing path, ts}`; `src` precedence: explicit `src` param → short-link/QR id →
     `utm_source/medium` mapping → referrer classification (search engine → "organic search",
     google.com/maps → GBP-likely, social domains, business's own website domain → "website") →
     "direct".
   - First-touch stored in a first-party cookie/`localStorage` (90 days, on the portal origin, consent
     permitting); last-touch per session. Booking stores **both** + list of touches (≤ 20).
   - Embedded/popup: loader passes the parent page's `utm_*`/click ids and `document.referrer` into
     the iframe URL so attribution survives the iframe boundary.
   - Gift card, subscription and referral purchases carry attribution too.

### 4.2 Customer booking flow — step by step with side effects

Records created at each step (column "Writes") use the entities in §7. "→07" means a progress
event streamed to Agent 07 for abandoned-booking capture. All writes are idempotent (§10.6).

| # | Step (screen) | Customer action | Server work | Writes / events | Abandon capture |
|---|---|---|---|---|---|
| 0 | Landing (A0) | Opens any portal URL | Host→tenant; serve cached shell + catalog; attribution parse | `PortalVisit` (lightweight, batched, cookie-less count if no consent) → `portal.visit.recorded` | Visit counted (Fieldd tracks portal visits automatically [S: T05]) — anonymous, no lead |
| 1 | Category (A0→A1) | Picks category | none (static) | `BookingSession` created lazily on first *selection*: `{id, tenantId, visitorId, attribution, step:'category'}` → `portal.session.started` | Anonymous session (no PII) — counts in funnel |
| 2 | Service (A1/A2) | Picks service(s)/variant | 02 `QuotePrice` (base) | session.cart updated; `portal.session.step_reached{step:'service'}` | Funnel |
| 3 | Add-ons (A3) | Toggles add-ons/qty | 02 quote; inventory check (soft) | cart.addons; duration recomputed | Funnel |
| 4 | Frequency/subscription (A4) | Chooses one-time / recurring / package | 06 `QuoteSubscription`, 02 recurring discount | cart.frequency / cart.packageId | Funnel |
| 5 | Location (A5) | Enters address or picks shop | 02 `CheckServiceArea` → zone, travel fee [S: T11], tax rate [S: T11]; out-of-area branch | session.location {structured address, lat/lng, zoneId, inArea}; out-of-area "ask us anyway" → 07 `CreateLead{reason:'out_of_area'}` | Address is PII-ish; still not a lead until contact |
| 6 | Date/time (A6) | Picks slot or route day [S: T02] | 03 `GetAvailability(services, durationMin, location, zone, window)`; on pick 03 `CreateHold` (TTL 10 min, extendable to 20) | session.slot {start, end, tz, holdId, routeDay?} → `portal.session.step_reached{step:'datetime'}` | Funnel; hold auto-expires if abandoned |
| 7 | Questions (A7) | Answers | validation only; file uploads to quarantined storage → AV scan | session.answers | Funnel |
| 8 | Contact/account (A8) | Types name/phone/email (blur-saved) | 07 `MatchCustomer` (silent); OTP send if returning-customer sign-in chosen | session.contact → **`portal.session.contact_captured`** → 07 creates/updates **abandoned booking / lead** with how far they got [S: T10] | **From here an exit becomes an actionable abandoned booking** (drip "finish your booking" [S: T12]) |
| 9 | Assets (A9) | Picks/enters vehicle/home | 07 vehicle lookup (VIN/plate/YMM [S: llms]) | session.assets (draft, not yet in profile) | Lead enriched |
| 10 | Intake (A10) | Answers profile questions | — | session.intake | — |
| 11 | Review & terms (A11) | Enters code; ticks terms | 06 `ValidateCode` (discount/gift/referral); 05 `QuoteTotals` (authoritative totals, tax order) | session.codes, session.quoteId (+ quote hash), `TermsAcceptance` draft (version id, ts, ip, ua) | Lead shows cart value |
| 12 | Payment (A12) | Enters card / wallet / nothing | 05 `CreateIntent(mode, amount, customerRef, idempotencyKey=sessionId+quoteHash)`; client confirms with processor; 3DS | `PaymentIntentRef` on session; session.status=`awaiting_payment` → `portal.session.payment_started` | Payment-step abandonment flagged "high intent" |
| 13 | Commit | Taps "Book" | **Booking saga** (§10.4): 03 `ConfirmHold` → 07 `UpsertCustomer` (+assets, addresses, intake) → 04 `CreateJob` (single job ID [S: T04]) → 05 `AttachPayment` (capture deposit / save card / pre-auth) → 06 `RedeemCode`/`StartSubscription` → mark session `converted` | `Booking` (portal-side record linking session→job), `TermsAcceptance` finalized, `CancellationPolicySnapshot`; events **`portal.booking.confirmed`**, plus 03/04/05/06/07 events; 03 auto-dispatch [S: llms] | 07 marks abandoned booking `converted` (removes from follow-up drips) |
| 14 | Confirmation (A13) | Views/adds to calendar | — | — | — |
| 15 | Messages | — | 08 sends confirmation; reminders later [S: T15]; team notified [S: T15] | 08's records | — |

**Ordering constraints enforced by the flow engine [D]:**
- Service (and variant/add-ons → duration) must precede date/time (availability depends on duration
  [S: T06][S: T07]).
- Location must precede date/time for mobile services (availability depends on zone, workers
  assigned to zone [S: T11] and travel time [S: T19]).
- Asset precedes price if the service's price depends on asset class.
- Contact may be moved **earlier** ("contact-first" mode, e.g. right after service) — better
  abandoned-capture, slightly lower top-of-funnel completion; tenant option, default = after
  date/time [D]. Fieldd's position unknown → verify.
- Payment is always last before commit; terms acceptance is on/just before the pay screen.

**Abandonment definition [D]:** a session with `contact_captured` and no `converted` after **30 min
of inactivity** emits `portal.session.abandoned{lastStep, cartValueCents, contact, slot?}`. 07 owns
the abandoned-booking record and its lifecycle; 08 owns any follow-up ("text a couple of hours
later to see if they want to finish their booking" [S: T12]). Sessions without contact remain
anonymous funnel analytics only (count of "visited but didn't book" [S: T10]). Resuming: every
abandoned follow-up link is `https://{host}/r/{resumeToken}` which restores the session (cart, address,
answers) and re-queries availability (old hold is gone).

### 4.3 Returning customer: sign-in and rebook
1. Customer taps "Sign in" (or "Welcome back" prompt in A8) → enters phone/email.
2. Server always answers "If we have an account for that, we've sent a code" (no enumeration).
3. OTP (6 digits, 10-min TTL, 5 attempts, 3 sends/15 min per identifier, per-IP limits) via 08's
   transactional channel; or magic link.
4. On verify: `PortalIdentity` linked to 07's customer id; session cookie (host-only, 30-day sliding
   if "trust this device"); event `portal.account.signed_in`. First verification for a new person
   creates the identity → `portal.account.created` (07 creates/links customer).
5. **Rebook** from B2: loads the past job's services, variants, add-ons (only those still published
   and paired [S: T07]), address, assets, answers (questions still active); prices re-quoted at
   *current* prices with a note if changed; jumps to A6 date/time [S: T05 "rebook previous services"].
6. If an item is no longer offered → shown struck through with "no longer available".

### 4.4 Reschedule (customer self-service) [S: T05 cancellation/reschedule prompt][D detail]
1. From B3/C2, "Reschedule" visible only if tenant allows online reschedule and job status ∈
   {scheduled, confirmed} (not en-route/in-progress/completed) and start > now + minNotice.
2. Shows policy: free or fee (§5.6); reschedule counter (limit N).
3. Date/time picker queries 03 with *same* services/duration/location, excluding the job's own
   current slot from conflicts.
4. Pick → 03 `CreateHold` → confirm → 03 `MoveAppointment(jobId, newSlot, expectedVersion)`
   (optimistic concurrency) → fee via 05 if applicable → `portal.reschedule.completed`.
5. 08 notifies customer + assigned worker(s) (CRM drag-reschedule likewise "notif[ies] the necessary
   people automatically" [S: T08]).
6. Failure (slot taken) → keep old appointment untouched; show alternatives.

### 4.5 Cancel [S: T05 "If a customer cancels, you can collect feedback … and choose whether or not to prompt them to reschedule"]
1. "Cancel" → if tenant enabled "prompt to reschedule", interstitial "Would a different time work?"
   → Reschedule / Continue cancelling.
2. Policy computation (§5.6) → shows fee/refund preview: "Cancelling now: $25 late-cancellation
   fee will be charged to Visa ••4242. Your $50 deposit: $25 refunded."
3. Feedback: reason (single choice from tenant list, e.g. Schedule conflict / Found another provider /
   Price / Weather / No longer needed / Other) + optional text (≤ 1000 chars); required or optional per
   setting.
4. Confirm (idempotent) → 04 `CancelJob(jobId, by:'customer', reason)` → 05 fee charge / refund per
   snapshot → 03 frees capacity → events `portal.cancellation.completed`, `portal.feedback.submitted`.
5. 08 sends cancellation confirmation to customer and notifies business/worker.
6. Recurring/subscription visit: choice "Cancel only this visit" vs "Cancel all future visits"
   (latter routes to 06's subscription cancel rules and commitment terms [S: T01 commitment options]).

### 4.6 Gift card purchase & send [S: T05 "buy and send gift cards"][S: T09 "sold online through your booking page"][S: llms AI gift card design]
1. `/gift-cards` (public, no login needed) → pick design (tenant's designs), amount (presets + custom
   within min/max), recipient name + email/phone, sender name, message (≤ 300 chars, profanity-neutral,
   plain text), delivery: now / scheduled date-time (recipient's timezone default = business tz).
2. 06 `QuoteGiftCard` → 05 payment (charge now; gift cards are prepaid) → 06 `IssueGiftCard`
   (ledger owned by 06) → event `portal.giftcard.purchased`; 08 delivers to recipient (scheduled) and
   receipt to purchaser.
3. Fieldd notes gift cards online appear when using its payment product [S: T09 "If you're using Field
   Pay, you'll also see your gift cards"] → we gate online gift-card sales on payments being
   connected [I/D].

### 4.7 Subscription self-purchase & upsell [S: T01]
- Self-purchase (B10 or `/plans` page): only packages flagged "available for self-purchase" [S: T01].
- Upsell during booking (A4): only packages flagged "upsell during booking" [S: T01].
- Flow: choose package → commitment option (e.g. pay 6 months upfront, discount) [S: T01] → if package
  has automatic scheduling, pick preferred day/time pattern (03 validates) [S: T01] → payment
  (card on file mandatory for recurring billing) → 06 `StartSubscription` → if auto-scheduling, 03
  creates recurring jobs → `portal.subscription.purchased`.
- If both flags off → package not visible online, CRM-only [S: T01 "If these options are turned off,
  the package can only be sold from within the CRM"].

### 4.8 Referral codes [S: llms "referral codes tracked in one place that work online and in the customer app"; Scale tier "referral marketing"]
- Logged-in customers see their code + share link `https://{host}/?ref=CODE` (B12).
- New customer arriving with `?ref=` → code stored on session (and first-touch attribution); shown at
  A11 as "Referral applied: $10 off your first booking" if 06 validates (rules: new customers only,
  not self-referral — same phone/email/payment fingerprint/address → rejected).
- On booking completion → `portal.referral.used` → 06 issues rewards per rules (possibly after job
  completion to prevent abuse) → 08 notifies referrer.

### 4.9 Live "on my way" tracking page [S: llms "on-my-way tracking"][S: T11 "live tracking … real-time updates on your arrival"]
1. Worker taps "On my way" in the team app (04) → 04 emits `job.worker.on_my_way` with ETA.
2. We mint `TrackingLink{token, jobId, expiresAt}`; 08 sends SMS "Sam is on the way, ETA 9:42 —
   track: https://{host}/t/{token}".
3. Page subscribes to location updates (SSE/WebSocket, fallback poll every 15 s); shows map, ETA,
   worker first name + photo (09), vehicle (if configured), status timeline.
4. Location is shown **only** while status = on_my_way and only for that job; after "arrived" the map
   freezes on the service address; after completion page shows "Service complete" + receipt/review
   link (08 review booster [S: llms "Google review booster"]) and expires 2 h later.
5. Privacy: location precision rounded to ~50 m until within 1 km; never show worker's home/previous
   jobs; no worker last name/phone unless tenant opts in.

### 4.10 Quote → online booking [S: T04]
1. Business sends quote with "Book online" button [S: T04].
2. Customer opens `/q/{token}` → views quote → "Accept & book" → flow starts at A6 (date/time) with
   services/prices **locked to the quote** (quoteId on session) → A11/A12 → commit attaches to the
   **same job ID** (04 transitions quote→appointment) [S: T04 "Field keeps everything in sync and updates the job card automatically"].

### 4.11 Pay an invoice / view receipt [S: T17 "payments charged on invoices through the customer portal"]
- `/i/{token}` or B13 → 05 payment element → `portal.invoice.payment_started`; 05 owns result.

### 4.12 Owner publishes portal settings [S: T05 "Any updates … reflected immediately"]
1. Owner edits style/settings → draft saved (autosave every 2 s idle, like add-ons "save
   automatically" [S: T07]).
2. Publish → new immutable `PortalConfigVersion` (n+1) → edge KV `configVersion` bump + cache-tag
   purge `tenant:{id}:config` → live in ≤ 5 s globally → `portal.config.published`.
3. Catalog changes from 02 (`catalog.*.published`) purge `tenant:{id}:catalog` the same way.
4. In-flight booking sessions keep their quoted prices/terms version; at commit, re-validate: if a
   service was unpublished or price changed, show "Prices updated" review step instead of failing
   silently.
5. Rollback: "Restore previous version" re-publishes version n-1 as n+2.

### 4.13 AI-agent / Bookings-MCP booking [S: llms Bookings MCP]
Same sequence as 4.2 via the public API with an agent principal: `start_session`, `list_services`,
`check_area`, `get_availability`, `hold_slot`, `set_contact`, `quote`, `create_payment_link`
(agents never handle raw card data — they send the customer a pay/confirm link), `confirm_booking`.
Rate-limited per key; every booking tagged `src=agent:{keyName}`.

---

## 5. Business rules & math

### 5.1 Price display vs authoritative totals [D]
- The portal **never computes money itself** for anything it will charge. Displayed running totals
  come from 02 `QuotePrice` (lines) and, from A11 on, 05 `QuoteTotals` (discounts, gift cards, travel
  fee, tax order, deposit). The portal only formats integer cents for display
  (`Intl.NumberFormat(locale,{style:'currency',currency})`).
- Every quote returns `quoteId` + `quoteHash` (hash of lines, codes, location zone, tax rate, policy
  versions, price-book version) and `expiresAt` (15 min). Payment intents and commit reference the
  hash; if the server-side recomputation at commit yields a different hash → `409 QUOTE_CHANGED` →
  portal shows a diff and asks the customer to confirm the new total. **Never charge an amount the
  customer did not see.**
- Line display order [D]: services → variants → add-ons → travel fee [S: T11] → subtotal → discounts
  (code, recurring [S: T06][S: T14], subscription [S: T01]) → tax (zone rate [S: T11]) → total →
  gift card applied → **due today** (deposit/prepay) → **due after service**. Tax-before/after-discount
  order is 05's rule.
- "From $X" on A0/A1 = min over variants of base price, excluding add-ons, travel fees, tax [D].
- Rounding: all arithmetic in 05/02 with integer cents; percentage results use banker's rounding
  per line (05 decides); the portal displays exactly what it received.

### 5.2 Duration
`bookingDurationMin = Σ service.durationMin(variant) + Σ addon.durationMin × qty` [S: T06][S: T07];
computed by 02, consumed by 03. Portal displays "about 2 h 30 min" rounded to nearest 5 min (display
only), or hides durations if tenant setting says so.

### 5.3 Availability window rules (input to 03) [D defaults, configurable]
- `minNotice` (default 2 h; route services: default "book by 6 pm the day before") — earliest
  bookable start = `now + minNotice`, rounded up to slot interval.
- `horizonDays` (default 60).
- Slot list = 03's response; portal does not filter except UI grouping. Route day label =
  `routesText` template with `{weekday}` (default "Anytime {weekday}") [S: T02].
- Display timezone = service location tz (mobile: zone's tz; shop: shop's tz).

### 5.4 Hold rules [D]
- One active hold per booking session. New slot pick releases previous hold.
- TTL 10 min, sliding: any interaction on steps after A6 extends to max 20 min total; payment step
  extends once by +10 min (3DS can be slow). Expired hold at commit → try to re-hold the same slot
  atomically; if gone → `409 SLOT_TAKEN` + alternatives; payment intent (not yet captured) is
  cancelled/voided.
- Holds count as busy capacity for other visitors' availability queries (03).

### 5.5 Deposit / payment mode display (05 owns the rules) [S: T04 deposits, prepayments, pre-authorization][S: T16]
| Mode | Customer sees | At commit |
|---|---|---|
| Pay after job | "No payment needed today" (card optional) | Nothing charged |
| Card on file, charge after | "Card saved securely; charged after service" | SetupIntent → saved card |
| Card on file, charge at booking | "Charged today" | Payment captured |
| Deposit | "Deposit $X due today; balance after service" | Deposit captured |
| Prepayment (full) | "Paid in full today" | Full capture |
| Pre-authorization | "Temporary hold of $X; final charge after service" | Authorization only (note: card auths expire ~7 days → 05 handles re-auth for far-future bookings [I]) |

### 5.6 Cancellation & reschedule fee math [S: T05 policy exists; formulas D]
```
hoursBefore = (appointment.startUtc - nowUtc) / 3600
policy = job.cancellationPolicySnapshot      // snapshot at booking time, NOT current settings
if !policy.onlineCancelAllowed: show "Contact us to cancel" (phone/text link)
if hoursBefore >= policy.freeWindowHours: fee = 0
else:
  base = policy.feeType == 'fixed' ? policy.feeCents
       : round_half_even(job.totalCents * policy.feePercentBps / 10000)
  fee = min(base, policy.feeCapCents ?? ∞, job.totalCents)
depositOutcome:
  refundable → refund = depositPaid - fee (if ≥ 0) else charge (fee - depositPaid) to card on file
  non_refundable → refund = 0; fee is satisfied by deposit first
  credit → issue account credit (06 gift-card-style ledger) of depositPaid - fee
```
- Reschedule: same window logic with `rescheduleFee` (default 0) and `maxReschedules` (default 2);
  rescheduling **within** the window moving to a later date may be allowed fee-free if the tenant
  enables "reschedule is free, cancellation isn't" (common in detailing) [I].
- Fee charging requires a card on file or deposit; if neither, the fee is recorded as a balance due
  (05) and shown in the account.
- Weather/business-initiated cancellations are staff actions (04) — never charge the customer.
- DST: `hoursBefore` uses UTC instants, so a 24 h window across a DST change is exactly 24 real hours [D].

### 5.7 Service-area & shop rules (02 computes; portal applies) [S: T11]
- Mobile service bookable iff location inside ≥ 1 active zone that has ≥ 1 worker assigned who can
  perform all selected services [S: T11 staff to zone][S: T13 services per worker][I].
- Overlapping zones → 02 returns the chosen zone (rule: smallest travel time / priority); portal shows
  that zone's travel fee and tax.
- Shop list filtered by distance limit [S: T11]. Shop services don't take travel fees [S: T11 fee is
  "applied to any mobile service booked within this zone"].
- Mixed cart (mobile + shop service) is **not allowed** in one booking [D] — the cart enforces a
  single location mode (categories flagged mobile only [S: T06]).

### 5.8 Attribution rules [D]
- Source classification precedence (4.1.8 #8). Booking credited to **last non-direct touch within 30
  days** for the dashboard "booking source" (Fieldd's single "booking source" view [S: T05]); first
  touch also stored for reporting.
- Internal navigation (portal → portal) never creates a touch. Payment-processor redirect returns
  (3DS) are excluded as referrers.

### 5.9 Text override rules [S: T05 "text used to describe elements"][S: T02 routes text][D]
- Max 200 chars per short string, 2000 per long; placeholders validated against the key's allowlist
  (e.g. routesText allows only `{weekday}`, `{date}`); unknown placeholder → validation error.
- Empty override = default.

### 5.10 Consent & privacy rules [I/D — legal review needed]
- Phone/email captured before submission (A8 blur) is used for **transactional** follow-up of that
  booking only unless marketing consent given; abandoned-booking SMS in the US is likely marketing
  under TCPA-style rules → we require an explicit SMS-consent checkbox (unchecked by default) for
  marketing follow-ups; email follow-ups follow CAN-SPAM/GDPR rules (08 enforces) [I].
- Privacy notice link in footer and next to the contact form: "We save what you enter so we can help
  you finish booking."
- EU/UK visitors: analytics and pixels off until consent; our own first-party, cookie-less visit
  counting continues (aggregate) [D].
- Terms acceptance stores: termsVersionId, url or text hash, timestamp (UTC), IP, user agent, session id.

### 5.11 Booking limits & abuse [D]
- Max active (future, unpaid) online bookings per customer: default 5.
- Velocity limits per IP / device / phone: session creation 30/min/IP, OTP 3/15 min, booking commit
  5/hour/phone. Bot defense: invisible challenge on commit when risk score high (headless UA, data-
  center IP, too-fast form fill). Never on first page load (performance).
- Card testing defense lives in 05 but portal limits payment attempts to 5 per session.

---

## 6. State machines

### 6.1 CustomDomain

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `AddDomain(host)` | host valid & unclaimed; tenant tier allows | `pending_dns` | generate verifyToken + CNAME target; start poller |
| `pending_dns` | `DnsCheckPassed` | CNAME→our target (or A/AAAA = our IPs) and (TXT ok or direct CNAME) | `verifying` | — |
| `pending_dns` | `DnsCheckMismatch` | resolves elsewhere | `dns_mismatch` | store observed records for diagnostics |
| `dns_mismatch` | `DnsCheckPassed` | as above | `verifying` | — |
| `pending_dns`/`dns_mismatch` | `PollerTimeout` | > 30 days | `abandoned` | email owner once |
| `verifying` | `StartIssuance` | CAA allows our CA | `issuing` | ACME order |
| `verifying` | `CaaBlocked` | — | `error_caa` | show CAA instructions |
| `issuing` | `CertIssued` | — | `active` | push to edge KV & cert store; `portal.domain.activated`; 05 `RegisterWalletDomain`; switch `bookingBaseUrl` |
| `issuing` | `IssuanceFailed` | retries < 5 (backoff 1m,5m,30m,2h,6h) | `issuing` | — |
| `issuing` | `IssuanceFailed` | retries exhausted | `error_issuance` | alert owner + support; try secondary CA first |
| `error_caa`/`error_issuance` | `CheckNow` | fixed | `verifying` | — |
| `active` | `DailyCheckFailed` | 3 consecutive failures over ≥ 2 h | `detached` | canonical→platform host; links switch; owner alert; `portal.domain.detached` |
| `detached` | `DnsCheckPassed` | — | `active` | links switch back; `portal.domain.reattached` |
| `active` | `RenewalWindow` | 2/3 lifetime | `renewal_due` | ACME renew |
| `renewal_due` | `CertIssued` | — | `active` | — |
| `renewal_due` | `CertExpired` | — | `detached` | same as detached |
| any | `RemoveDomain` | owner/admin | `removed` | 24 h redirect grace then delete cert & KV entry |
| `removed` | `AddDomain(same host)` | — | `pending_dns` | new token |

### 6.2 BookingSession

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | first selection | — | `browsing` | `portal.session.started` |
| `browsing` | `StepCompleted(s)` | step valid | `browsing` | `portal.session.step_reached` |
| `browsing` | `ContactCaptured` | phone or email valid | `identified` | `portal.session.contact_captured` → 07 abandoned-booking upsert |
| `identified` | `StepCompleted(s)` | — | `identified` | progress → 07 |
| `identified` | `PaymentStarted` | quote valid, hold valid | `awaiting_payment` | 05 intent |
| `awaiting_payment` | `PaymentAuthorized` / `NoPaymentRequired` | — | `committing` | start saga |
| `awaiting_payment` | `PaymentFailed` | attempts < 5 | `identified` | show error; keep data |
| `committing` | `SagaSucceeded` | — | `converted` | `portal.booking.confirmed`; 07 marks converted |
| `committing` | `SagaFailed(retryable)` | — | `committing` | retry with same idempotency key |
| `committing` | `SagaFailed(slot_taken)` | — | `identified` | void payment; offer alternatives; `portal.booking.failed{reason}` |
| `committing` | `SagaFailed(fatal)` | — | `needs_attention` | void/refund; alert business; customer sees "We've received your request; we'll confirm shortly" + lead to 07 with full cart (never "lost") |
| `browsing`/`identified` | `Inactivity(30m)` | identified only emits | `abandoned` | `portal.session.abandoned` |
| `abandoned` | `Resume` (resume link / same device) | < 30 days | `identified` | re-query availability |
| any non-terminal | `Expire` | > 30 days idle | `expired` | PII purged from session (lead remains in 07 per retention) |
| `browsing` | `Inactivity(30m)` | no contact | `expired_anonymous` | aggregate counters only |
| `needs_attention` | staff resolves in CRM | — | `converted` or `closed` | — |

### 6.3 SlotHold (owned by 03; portal's view)
`none → held (CreateHold) → confirmed (ConfirmHold at commit) | released (new pick / cancel) | expired (TTL)`.
Portal must handle `expired` at any later step gracefully (5.4).

### 6.4 OTP challenge (portal auth)

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `RequestCode(identifier)` | rate limits ok | `sent` | 08 transactional send; code hash stored |
| `sent` | `Verify(code)` | match, not expired | `verified` | session issued; identity linked |
| `sent` | `Verify(code)` | mismatch, attempts < 5 | `sent` | attempts++ |
| `sent` | `Verify` | attempts = 5 | `locked` | 15-min lock |
| `sent` | `Resend` | ≤ 3/15 min | `sent` | new code, old invalid |
| `sent` | 10 min elapsed | — | `expired` | — |

### 6.5 Customer-initiated change request on a job (portal view)

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `viewing` | `RescheduleStart` | policy allows; job status ok; reschedules < max | `rescheduling` | — |
| `rescheduling` | `SlotConfirmed` | 03 move ok (version match) | `done` | fee via 05 if any; `portal.reschedule.completed` |
| `rescheduling` | `MoveConflict` | — | `rescheduling` | alternatives |
| `viewing` | `CancelStart` | policy allows online cancel | `cancel_offer` (if prompt-to-reschedule on) else `cancel_confirm` | — |
| `cancel_offer` | `ChooseReschedule` | — | `rescheduling` | — |
| `cancel_offer` | `ContinueCancel` | — | `cancel_confirm` | — |
| `cancel_confirm` | `Confirm(reason)` | feedback required → present | `done` | 04 cancel; 05 fee/refund; `portal.cancellation.completed`, `portal.feedback.submitted` |

### 6.6 PortalConfig publishing
`draft → published(vN)`; publish creates immutable version; `rollback(vK)` creates `published(vN+1)`
with vK content. Edge serves only published versions.

### 6.7 TrackingLink
`issued (on_my_way) → live (first view) → arrived (map frozen) → completed → expired (+2 h)`; `revoked`
if the job is reassigned to another worker (new link issued) or cancelled.

---

## 7. Data model (stack-agnostic, TypeScript-style)

```ts
type ID = string;            // ULID/UUIDv7 — time-ordered, globally unique
type Cents = number;         // integer minor units
type Instant = string;       // ISO-8601 UTC
type TZ = string;            // IANA tz, e.g. "America/Chicago"

// ---------- Portal configuration ----------
interface PortalSite {
  id: ID; tenantId: ID;                 // 1:1 with tenant (multi-brand later: 1:N)
  platformSlug: string;                 // unique, [a-z0-9-]{3,40}, reserved words blocked
  status: 'live' | 'maintenance' | 'disabled';
  publishedConfigVersion: number;
  draftConfig: PortalConfig;
  createdAt: Instant; updatedAt: Instant;
}

interface PortalConfigVersion {          // immutable
  portalSiteId: ID; version: number; config: PortalConfig;
  publishedBy: ID; publishedAt: Instant; note?: string;
}

interface PortalConfig {
  style: PortalStyle;
  textOverrides: Record<TextKey, string>;      // only overridden keys
  flow: FlowSettings;
  assets: AssetSettings;                        // visibility/requirement only (07 owns asset schema)
  termsVersionId?: ID;  requireTerms: boolean;
  cancellationPolicyId: ID;
  analytics: AnalyticsIntegration[];
  seo: SeoSettings;
  additional: AdditionalSettings;
}

interface PortalStyle {
  colors: { primary: string; accent: string; background: string; surface: string; text: string }; // #RRGGBB
  autoContrast: boolean;
  fontFamily: 'system' | CuratedFontId;
  radius: 'sharp' | 'rounded' | 'pill';
  theme: 'light' | 'dark' | 'auto';
  logoAssetId?: ID; faviconAssetId?: ID; heroAssetId?: ID;
  map: { style: 'standard' | 'muted' | 'dark'; pinColor: string; showOnAddressStep: boolean };
  showPoweredBy: boolean;                       // tier-gated
}

interface FlowSettings {
  contactPosition: 'after_service' | 'after_datetime' | 'before_payment';
  guestCheckout: boolean;
  requirePhone: boolean; requireEmail: boolean;
  multipleServices: boolean;
  showPrices: 'always' | 'from' | 'after_address';
  showDurations: boolean;
  allowWorkerChoice: boolean;
  confirmationMode: 'instant' | 'request';      // request = business approves
  minNoticeMinutes: number; horizonDays: number; slotIntervalMinutes: 15 | 30 | 60;
  holdTtlMinutes: number;                        // default 10
  abandonAfterMinutes: number;                   // default 30
  postBookingRedirectUrl?: string;               // https only
  arrivalWindowDisplay?: { enabled: boolean; widthMinutes: number };
}

interface AdditionalSettings {
  maxActiveOnlineBookingsPerCustomer: number;
  allowedEmbedOrigins: string[];                 // frame-ancestors; empty = any
  locale: string; supportedLocales: string[];
  closureBanner?: { text: string; from: Instant; to: Instant };
}

interface SeoSettings { title?: string; description?: string; ogImageAssetId?: ID; noindex: boolean }

// ---------- Hosting ----------
interface CustomDomain {
  id: ID; tenantId: ID; portalSiteId: ID;
  hostname: string;                       // lowercase punycode, UNIQUE across platform where status ∉ {removed, abandoned}
  purpose: 'booking' | 'account';
  mode: 'cname' | 'apex';
  cnameTarget: string;                    // t-{shortId}.edge.<platform>
  verifyToken: string;                    // TXT value
  status: 'pending_dns' | 'dns_mismatch' | 'verifying' | 'issuing' | 'active' | 'renewal_due'
        | 'detached' | 'error_caa' | 'error_issuance' | 'removed' | 'abandoned';
  isCanonical: boolean;                   // at most one per portalSite
  lastCheckAt?: Instant; lastCheckResult?: DnsDiagnostics;
  consecutiveFailures: number;
  certificateId?: ID;
  walletDomainRegistered: boolean;        // Apple Pay (via 05)
  createdBy: ID; createdAt: Instant; activatedAt?: Instant; removedAt?: Instant;
}

interface DnsDiagnostics {
  resolvers: { resolver: string; cname?: string[]; a?: string[]; aaaa?: string[]; txt?: string[]; caa?: string[]; error?: string }[];
  nsProvider?: string;                    // detected DNS host
  cloudflareProxied?: boolean;
  hint?: string;                          // human-readable fix
}

interface Certificate {
  id: ID; hostname: string; issuer: string; notBefore: Instant; notAfter: Instant;
  keyRef: string;                         // KMS reference, never raw key in DB
  status: 'active' | 'renewing' | 'expired' | 'revoked';
  lastRenewalError?: string;
}

interface EdgeHostRecord {                // projection pushed to edge KV
  host: string; tenantId: ID; portalSiteId: ID; kind: 'platform' | 'custom';
  canonicalHost: string; status: 'active' | 'grace_redirect' | 'inactive'; configVersion: number;
}

// ---------- Links, QR, embeds ----------
interface ShortLink {
  id: ID; tenantId: ID;
  kind: 'service' | 'custom' | 'qr' | 'resume' | 'quote' | 'tracking';
  code: string;                           // service: service slug; custom: [a-z0-9-]{2,40}
  target: DeepLinkPrefill;                // what to preselect
  sourceKey: string;                      // e.g. "qr:flyer-fall", "website"
  utm?: Partial<Record<'utm_source'|'utm_medium'|'utm_campaign'|'utm_term'|'utm_content', string>>;
  active: boolean; clicks: number; createdAt: Instant;
}

interface DeepLinkPrefill {
  categorySlug?: string; serviceSlugs?: string[]; variant?: string;
  addons?: { slug: string; qty?: number }[]; mode?: 'mobile' | 'shop'; shopSlug?: string;
  date?: string; workerSlug?: string; promo?: string; packageSlug?: string; lang?: string;
}

interface QrCode {
  id: ID; tenantId: ID; shortLinkId: ID; label: string;
  fgColor: string; withLogo: boolean; createdAt: Instant; scans: number;
}

interface EmbedInstall {                   // observed installs (from loader beacons)
  tenantId: ID; parentOrigin: string; mode: 'inline' | 'popup'; firstSeen: Instant; lastSeen: Instant;
}

// ---------- Booking session ----------
interface BookingSession {
  id: ID; tenantId: ID; portalSiteId: ID;
  visitorId: string;                      // random, first-party
  identityId?: ID; customerId?: ID;       // when signed in / matched (07 id)
  status: 'browsing' | 'identified' | 'awaiting_payment' | 'committing' | 'converted'
        | 'abandoned' | 'needs_attention' | 'expired' | 'expired_anonymous' | 'closed';
  channel: 'web' | 'embed' | 'popup' | 'agent' | 'app';
  lastStep: StepKey; furthestStep: StepKey; stepHistory: { step: StepKey; at: Instant }[];
  cart: {
    mode?: 'mobile' | 'shop';
    lines: { serviceId: ID; variantKey?: string; addons: { addonId: ID; qty: number }[]; assetIds?: ID[] }[];
    frequency?: { kind: 'once' } | { kind: 'recurring'; ruleKey: string } | { kind: 'package'; packageId: ID; commitmentKey?: string };
    quoteId?: ID; quoteHash?: string; quoteExpiresAt?: Instant;
    codes: { kind: 'discount' | 'gift' | 'referral'; code: string; status: 'valid' | 'invalid' }[];
    fromQuoteJobId?: ID;                  // quote → book (4.10)
  };
  location?: { kind: 'address'; address: StructuredAddress; zoneId?: ID; inArea?: boolean; areaUnverified?: boolean }
           | { kind: 'shop'; shopId: ID };
  slot?: { startUtc: Instant; endUtc: Instant; tz: TZ; routeDay?: string; holdId?: ID; holdExpiresAt?: Instant; preferredWorkerId?: ID };
  answers: Record<ID /*questionId*/, AnswerValue>;
  intake: Record<ID, AnswerValue>;
  contact?: { firstName?: string; lastName?: string; phoneE164?: string; email?: string; smsConsent: boolean; marketingConsent: boolean };
  draftAssets: DraftAsset[];
  termsAcceptance?: { termsVersionId: ID; acceptedAt: Instant; ip: string; userAgent: string };
  payment?: { intentRef: string; mode: PaymentMode; status: 'created' | 'requires_action' | 'authorized' | 'succeeded' | 'failed' | 'voided' };
  attribution: { firstTouch?: AttributionTouch; lastTouch: AttributionTouch; touches: AttributionTouch[] };
  idempotencyKey: string;                 // = id; used for commit
  resumeToken?: string;                   // hashed at rest
  createdAt: Instant; updatedAt: Instant; lastActivityAt: Instant; expiresAt: Instant;
  version: number;                        // optimistic concurrency for PATCH
}

type StepKey = 'landing' | 'category' | 'service' | 'variant' | 'addons' | 'frequency' | 'location'
  | 'datetime' | 'questions' | 'contact' | 'assets' | 'intake' | 'review' | 'payment' | 'confirmed';

interface AttributionTouch {
  at: Instant; sourceKey: string;         // normalized ("website","gbp","qr:abc","short:full-detail","email","sms","organic_search","paid_search","social","referral_code","direct","agent:x")
  utm?: Record<string, string>; clickIds?: Record<string, string>;
  referrerHost?: string; landingPath: string; embedParentOrigin?: string;
}

interface PortalVisit {                   // aggregated, privacy-light
  tenantId: ID; day: string; sourceKey: string; visits: number; sessionsStarted: number;
  contactsCaptured: number; bookings: number; revenueCents: Cents;
}

// ---------- Booking (portal-side link) ----------
interface PortalBooking {
  id: ID; tenantId: ID; sessionId: ID; jobId: ID /* 04, single job ID */; customerId: ID;
  publicToken: string;                    // for /b/{token}; hashed at rest
  confirmedAt: Instant; sourceKey: string; firstTouchSourceKey?: string;
  termsAcceptanceId?: ID; cancellationPolicySnapshotId: ID;
  channel: BookingSession['channel'];
}

// ---------- Questions ----------
interface BookingQuestion {
  id: ID; tenantId: ID;
  purpose: 'booking' | 'intake';
  scope: 'global' | 'service';
  serviceIds?: ID[]; categoryIds?: ID[];   // applicability
  label: string; helpText?: string;
  type: 'short_text' | 'long_text' | 'number' | 'boolean' | 'single_choice' | 'multi_choice' | 'dropdown' | 'date' | 'file';
  options?: { key: string; label: string }[];
  required: boolean; min?: number; max?: number; maxLength?: number;
  visibleIf?: { questionId: ID; equals: string | string[] } | { serviceIn: ID[] };
  position: number; active: boolean; version: number;
}
interface ServiceQuestionSettings { serviceId: ID; suppressGlobal: boolean } // [S: T06] "turn off the global questions"

type AnswerValue = string | number | boolean | string[] | { fileIds: ID[] };

// ---------- Terms & cancellation ----------
interface TermsVersion {
  id: ID; tenantId: ID; kind: 'url' | 'hosted'; url?: string; body?: string; bodySha256: string;
  publishedAt: Instant;
}
interface TermsAcceptance {
  id: ID; tenantId: ID; termsVersionId: ID; customerId?: ID; sessionId: ID; jobId?: ID;
  acceptedAt: Instant; ip: string; userAgent: string;
}
interface CancellationPolicy {
  id: ID; tenantId: ID; version: number;
  onlineCancelAllowed: boolean; onlineRescheduleAllowed: boolean;
  freeWindowHours: number;
  feeType: 'none' | 'fixed' | 'percent'; feeCents?: Cents; feePercentBps?: number; feeCapCents?: Cents;
  rescheduleFeeCents: Cents; maxReschedules: number; minRescheduleNoticeHours: number;
  depositOutcome: 'refundable' | 'non_refundable' | 'credit';
  collectFeedback: 'off' | 'optional' | 'required';
  feedbackReasons: { key: string; label: string }[];
  promptReschedule: boolean;
  customerText?: string;                  // override; else generated
}
interface CancellationPolicySnapshot { id: ID; jobId: ID; policy: CancellationPolicy; takenAt: Instant }
interface CancellationFeedback {
  id: ID; tenantId: ID; jobId: ID; customerId: ID; reasonKey: string; text?: string;
  offeredReschedule: boolean; choseReschedule: boolean; createdAt: Instant;
}

// ---------- Analytics ----------
interface AnalyticsIntegration {
  kind: 'ga4' | 'gtm' | 'google_ads' | 'meta_pixel' | 'tiktok' | 'ms_uet';
  externalId: string;                     // validated format per kind
  conversionLabel?: string; sendValue: boolean; consentRequiredRegions: string[];
  enabled: boolean;
}

// ---------- Portal auth ----------
interface PortalIdentity {
  id: ID; tenantId: ID; customerId: ID /* 07 */;
  identifiers: { kind: 'phone' | 'email'; value: string; verifiedAt?: Instant }[];
  createdAt: Instant; lastSignInAt?: Instant; disabled: boolean;
}
interface OtpChallenge {
  id: ID; tenantId: ID; identifier: string; codeHash: string; attempts: number;
  status: 'sent' | 'verified' | 'locked' | 'expired'; expiresAt: Instant; sentVia: 'sms' | 'email';
}
interface PortalSession {
  id: ID; identityId: ID; tenantId: ID; host: string; createdAt: Instant; expiresAt: Instant;
  trustedDevice: boolean; revokedAt?: Instant;
}

// ---------- Tracking ----------
interface TrackingLink {
  token: string /* ≥128-bit, hashed at rest */; tenantId: ID; jobId: ID; workerId: ID;
  status: 'issued' | 'live' | 'arrived' | 'completed' | 'expired' | 'revoked';
  issuedAt: Instant; expiresAt: Instant;
}
```

**Keys & indexes [D]:** `CustomDomain.hostname` unique (partial: active states);
`PortalSite.platformSlug` unique; `BookingSession(tenantId, status, lastActivityAt)` for abandon
sweeps; `ShortLink(tenantId, kind, code)` unique; `PortalBooking.sessionId` unique (one booking per
session = idempotency backstop); `TermsAcceptance(sessionId)`; `TrackingLink.token` unique.

**Relations:** PortalSite 1—1 Tenant; PortalSite 1—N CustomDomain; BookingSession N—1 PortalSite;
BookingSession 0..1—1 PortalBooking 1—1 Job (04); PortalIdentity N—1 Customer (07); BookingQuestion
N—M Service (02); CancellationPolicySnapshot 1—1 Job.

**Retention [D]:** anonymous sessions 30 days; identified-but-unconverted sessions 90 days (lead
persists in 07 under its own policy); converted sessions 13 months (attribution analytics) then
reduced to PortalBooking; OTP challenges 24 h; terms acceptances for life of customer + 7 years.

### 7A. Addendum (2026-11-13): theme & brand data comes from Agent 11

The 2026-11-13 owner requirements (BRIEF) add deep tenant branding: editable **light and dark
palettes**, shadows/elevation, radius, spacing density, **uploaded custom fonts**, custom icons
(icon set or uploaded SVGs for categories/services/nav), **logos per placement with sizing
controls and light/dark variants**, copy overrides everywhere, responsive desktop/tablet/phone,
and later native apps. **Agent 11 (Branding, Theming & Responsive Design System) owns that model.**
Consequences for this module (they refine, not replace, §3.D1 and `PortalStyle` in §7):

- `PortalStyle.colors/fontFamily/radius/theme/logoAssetId/faviconAssetId` are a **portal-local
  projection** of Agent 11's `BrandTheme`. The source of truth is 11's theme record; the portal stores
  only `themeId` + `themeVersion` per published `PortalConfigVersion` plus portal-only settings
  (map style/pin, hero image, "powered by" badge, `showOnAddressStep`).
- The portal consumes 11's compiled **design tokens** (CSS custom properties for both modes, e.g.
  `--color-primary`, `--color-surface`, `--shadow-2`, `--radius-md`, `--density`, `--font-heading`,
  `--font-body`, font-size scale) and 11's **asset manifest** (logo per placement: `portal_header`,
  `portal_header_mobile`, `favicon`, `app_icon`, `og_image`; each with light/dark variants, intrinsic
  width/height and the sizing chosen by the business; icon map for categories/services/nav).
- Customer-facing light/dark switch: default follows `prefers-color-scheme`; a toggle in the portal
  header persists the choice per visitor (`localStorage`, wrapped in try/catch) and per signed-in
  customer (profile preference via 07) [D, required by BRIEF 2026-11-13].
- Text overrides (§3.D1, §5.9) remain owned by 01 **for portal strings**; if Agent 11 centralizes all
  copy overrides into one "copy" registry, 01's `TextKey` list becomes a namespace (`portal.*`) in it.
- Performance guardrails the portal imposes on 11's output (see §10.9, §11.7): tokens delivered inline
  in the HTML `<head>` (≤ 4 KB), custom fonts self-hosted, subset, WOFF2, ≤ 2 families × 2 weights
  preloaded, `font-display: swap` with metric-matched fallbacks; uploaded SVG icons sanitized by 11
  and inlined as a sprite; every logo rendered with explicit width/height (no CLS).

---

## 8. API surface

Conventions [D]: JSON over HTTPS; public routes are tenant-scoped **by Host only** (edge-injected
`X-Tenant-Id`); all mutating calls accept `Idempotency-Key`; optimistic concurrency via
`If-Match: <version>` on session PATCH; errors are `{code, message, details?, retryable}` with stable
`code` strings. Auth kinds: `public` (anonymous, rate-limited), `session` (booking-session bearer = 
session id + signed secret in memory/sessionStorage), `customer` (portal session cookie), `agent`
(Bookings-MCP API key, customer-level), `staff:<permission>` (CRM, permission names per Agent 09:
`settings.online_booking.manage` etc.), `system` (internal service identity). Names below are
command/query names; REST paths are illustrative.

### 8.1 Public: config, catalog, attribution

| Name | Path (illustrative) | Input | Output | Auth |
|---|---|---|---|---|
| `portal.config.get` | `GET /api/portal/config` | — (Host) | published `PortalConfig` (public subset) + theme token URL/version (Agent 11) + feature flags + `canonicalHost` | public, edge-cached (tag `tenant:{id}:config`) |
| `portal.catalog.get` | `GET /api/portal/catalog?mode=` | `mode?` | categories, services (public fields only), customer add-ons + pairings, variants, recurring options, self-purchasable packages (06), questions (booking) — all from 02/06 projections | public, edge-cached (tag `tenant:{id}:catalog`) |
| `portal.visit.record` | `POST /api/portal/v` (beacon) | `{visitorId?, landingPath, referrer, utm, clickIds, src, embedParentOrigin?}` | `204` | public; batched; consent-aware |
| `portal.link.resolve` | `GET /s/{slug}`, `/go/{code}`, `/q/{qrId}` | path | `302` to `/` with prefill params + `src` | public; increments counters |

### 8.2 Public: booking session

| Name | Path | Input | Output | Auth |
|---|---|---|---|---|
| `booking.session.start` | `POST /api/booking/sessions` | `{visitorId, prefill?: DeepLinkPrefill, attribution, channel}` | `{sessionId, sessionSecret, version, steps: StepPlan}` | public |
| `booking.session.get` | `GET /api/booking/sessions/{id}` | — | full session (minus secrets) + `StepPlan` | session |
| `booking.session.update` | `PATCH /api/booking/sessions/{id}` | partial `{cart?, location?, answers?, intake?, contact?, draftAssets?, codes?}` + `If-Match` | updated session, new `StepPlan`, `quote?` | session |
| `booking.session.checkArea` | `POST …/{id}/location` | `{address \| shopId}` | `{inArea, zone?, travelFeeCents?, taxRatePpm?, eligibleServiceIds, alternatives?: shops[]}` (wraps 02 `coverage.resolveLocation` / `coverage.resolveShop`) | session |
| `booking.session.availability` | `GET …/{id}/availability?from&to` | date range ≤ 62 days | 03's day/slot list (`slot.token` opaque) + `ttlSec` | session |
| `booking.session.hold` | `POST …/{id}/hold` | `{slotToken}` | `{holdId, expiresAt, start, end}` / `409 SLOT_TAKEN {alternatives}` (wraps 03 `scheduling.hold.create`) | session |
| `booking.session.extendHold` | `POST …/{id}/hold/extend` | — | `{expiresAt}` / `410 HOLD_EXPIRED` | session |
| `booking.session.quote` | `POST …/{id}/quote` | — | `{quoteId, quoteHash, lines, totals, dueNowCents, dueLaterCents, expiresAt}` (02 `catalog.priceSelection` + 05 `payments.quoteTotals`) | session |
| `booking.session.applyCode` | `POST …/{id}/codes` | `{code}` | `{kind, status, message, quote}` (06 validation; referral via 06/08) | session; 10 attempts/session |
| `booking.session.acceptTerms` | `POST …/{id}/terms` | `{termsVersionId}` | `{acceptedAt}` | session |
| `booking.session.preparePayment` | `POST …/{id}/payment` | `{quoteHash}` | `{paymentSessionId, clientSecret, mode, amountCents}` (05 `payments.prepareBookingPayment`) | session |
| `booking.session.confirm` | `POST …/{id}/confirm` | `{quoteHash, paymentSessionId?}` + `Idempotency-Key` (= sessionId) | `200 {bookingId, jobId, jobNumber, start, end, publicToken, manageUrl}` / `409 QUOTE_CHANGED \| SLOT_TAKEN \| HOLD_EXPIRED` / `202 {status:'needs_attention'}` | session |
| `booking.session.resume` | `GET /r/{resumeToken}` | — | redirect into restored session | public (token) |
| `booking.session.uploadUrl` | `POST …/{id}/uploads` | `{questionId, contentType, size}` | pre-signed upload target (quarantine bucket) | session; ≤ 5 files × 10 MB |
| `booking.request.submit` | `POST …/{id}/request` | `{note?}` | `{leadId}` — "ask us anyway"/request-a-time/degraded mode | session |

`StepPlan` = ordered list of `{step: StepKey, required, complete, skippedReason?}` computed server-side
(§10.3) so web, embed, native app and MCP agents run the same flow logic.

### 8.3 Customer auth & account

| Name | Input | Output | Auth |
|---|---|---|---|
| `portal.auth.requestCode` | `{identifier (phone/email), channel?}` | `{challengeId}` (always 200; no enumeration) | public, rate-limited |
| `portal.auth.verifyCode` | `{challengeId, code, trustDevice}` | sets cookie; `{customer summary}` | public |
| `portal.auth.magicLink` | `GET /auth/m/{token}` | sets cookie, redirect | public (token) |
| `portal.auth.handoff` | `GET /auth/handoff?t=` | sets cookie on canonical host | public (one-time token, 60 s) |
| `portal.auth.signOut` | — | clears session | customer |
| `portal.me.get` / `.update` | profile fields, notification prefs, theme preference | profile | customer |
| `portal.me.intake.get` / `.update` | answers | answers (stored by 07) | customer |
| `portal.assets.list/create/update/archive` | asset fields (07 schema) | asset | customer (proxy to 07) |
| `portal.vehicle.lookup` | `{vin} \| {plate, region} \| {year, make?, model?}` | candidates (07) | session or customer; rate-limited |
| `portal.addresses.list/create/update/delete` | address | address | customer (proxy to 07) |
| `portal.appointments.list` | `{status: upcoming\|past, cursor}` | appointments | customer |
| `portal.appointments.get` | `{jobPublicId}` | detail incl. policy snapshot, actions allowed | customer or booking `publicToken` |
| `portal.appointments.rescheduleOptions` | `{jobPublicId, from, to}` | slots + fee preview | customer/publicToken |
| `portal.appointments.reschedule` | `{jobPublicId, slotToken, expectedVersion}` + Idempotency-Key | updated appointment, fee charged | customer/publicToken (+OTP step-up if configured) |
| `portal.appointments.cancelPreview` | `{jobPublicId, scope: this\|future}` | `{feeCents, refundCents, creditCents, policyText, offerReschedule}` | customer/publicToken |
| `portal.appointments.cancel` | `{jobPublicId, scope, reasonKey?, text?}` + Idempotency-Key | `{status:'cancelled', feeCents, refundCents}` | customer/publicToken |
| `portal.appointments.rebook` | `{jobPublicId}` | new booking session prefilled (+ list of unavailable items) | customer |
| `portal.paymentMethods.*` | list/add (SetupIntent via 05)/remove/setDefault | — | customer |
| `portal.subscriptions.list/purchase/cancel/pause` | per 06 commands | — | customer |
| `portal.giftcards.quote/purchase` | design, amount, recipient, message, deliverAt | `{giftCardPublicId, receiptUrl}` | public (purchase) / customer (history) |
| `portal.giftcards.balance` | `{code}` | `{balanceCents, expiresAt?}` | public, rate-limited 10/min/IP |
| `portal.referrals.get` | — | `{code, shareUrl, rewards[]}` (06/08) | customer |
| `portal.invoices.get/pay` | `{token}` | invoice + 05 payment session | public (token) |
| `portal.quotes.get/accept` | `{token}` | quote; accept → booking session prefilled & locked (4.10) | public (token) |

### 8.4 Tracking

| Name | Input | Output | Auth |
|---|---|---|---|
| `portal.tracking.get` | `GET /t/{token}` | `{status, eta, worker:{firstName, photoUrl}, vehicle?, serviceAddress(approx), timeline}` | public (token) |
| `portal.tracking.stream` | SSE/WebSocket `/t/{token}/stream` | location/ETA/status events (≤ 1 per 5 s) | public (token) |

### 8.5 Admin (CRM) — portal settings, domains, links

| Name | Input | Output | Auth |
|---|---|---|---|
| `portal.settings.getDraft` / `.saveDraft` | `PortalConfig` partial | draft + validation warnings (contrast, missing terms…) | `staff:settings.online_booking.manage` |
| `portal.settings.publish` | `{note?}` | `{version}` | same |
| `portal.settings.versions` / `.rollback` | `{version}` | — | same |
| `portal.preview.token` | — | short-lived URL rendering draft config + draft theme | same |
| `portal.questions.list/create/update/reorder/archive` | `BookingQuestion` | — | same (service-scoped ones also `settings.catalog.manage`) |
| `portal.terms.publish` | `{kind, url \| body}` | `TermsVersion` | same |
| `portal.cancellationPolicy.update` | `CancellationPolicy` | new version | same |
| `portal.analytics.update` | `AnalyticsIntegration[]` | validated list | same |
| `portal.domains.add` | `{hostname, purpose, mode}` | `CustomDomain` + records to add + provider guide | `staff:settings.online_booking.manage` + owner/admin role |
| `portal.domains.check` | `{domainId}` | `DnsDiagnostics`, status | same; 1 per 10 s |
| `portal.domains.remove` | `{domainId}` | — | same |
| `portal.domains.setCanonical` | `{domainId}` | — | same |
| `portal.links.list/create/update/archive` | `ShortLink` | — | same |
| `portal.qr.create/download` | `{shortLinkId \| target, label, format, size, fg, withLogo}` | file | same |
| `portal.embed.snippet` | `{mode, start, options}` | HTML snippet | same |
| `portal.sources.report` | `{from, to, groupBy}` | rows per source: visits, sessions, contacts, bookings, revenue, conversion | `staff:marketing.analytics.view` (09) |
| `portal.funnel.report` | `{from, to, sourceKey?}` | step-by-step drop-off counts | same |
| `portal.sessions.list` | `{status, from, to}` | live booking attempts (for lead board, 07 is primary) | same |

### 8.6 System / internal

| Name | Purpose | Caller |
|---|---|---|
| `portal.edge.resolveHost` | KV read; fallback RPC on KV miss | edge |
| `portal.tls.ask` | `{hostname}` → `200` allow / `403` deny on-demand issuance | edge TLS terminator |
| `portal.domains.verifierTick` | scheduled DNS checks | scheduler |
| `booking.sessions.sweep` | inactivity → abandoned/expired; release holds | scheduler (every 1 min) |
| `booking.reconcile.run` | orphan payment intents vs sessions (§10.6) | scheduler (every 5 min) |
| `portal.bookingBaseUrl` | `{tenantId}` → canonical healthy base URL | 08, 04, 05, 06 when generating links |

**Bookings-MCP parity [S: llms]:** each tool in the customer-level MCP maps 1:1 onto §8.2–§8.4
(e.g. `list_services` → `portal.catalog.get`, `get_availability` → `booking.session.availability`,
`book` → `booking.session.confirm`). Payment by an agent is always a **pay link** sent to the
customer (05 hosted page), never raw card data through the agent [D].

---

## 9. Interfaces with other modules

### 9.0 Event naming reconciliation (for Agent 10)

Sections 4 and 6 use short working names `portal.session.*` / `portal.booking.*`. Other agents'
specs already consume different names for the same facts. **Canonical names this module publishes
are the left column**; the right column lists the names other specs used, which Agent 10 should map
or rename in the master event catalog. One fact = one event; no double-publishing.

| Canonical (01 publishes) | Same fact as named elsewhere | Consumers |
|---|---|---|
| `booking.session.started` | `portal.session.started` (§4/§6), `booking.attempt.started` (07) | 07, 08 (funnel) |
| `booking.session.step_reached` | `portal.session.step_reached`, `booking.attempt.step_reached` (07) | 07 |
| `booking.session.contact_captured` | `portal.session.contact_captured`, `booking.attempt.contact_captured` (07); 08 uses this name | 07, 08 |
| `booking.session.abandoned` | `portal.session.abandoned`, `lead.attempt.abandoned` (07 derives its own) ; 03 & 08 use this name | 07, 08, 03 |
| `booking.session.expired` | `booking.attempt.expired` (07) | 07 |
| `booking.session.payment_started` | `portal.session.payment_started` | 07 (high-intent flag) |
| `booking.appointment.created` (payload `source: online \| embed \| popup \| app \| agent`, `sessionId`, `jobId`) | `portal.booking.confirmed` (§4/§6), `booking.attempt.completed` (07); 07 & 08 consume `booking.appointment.created` | 07, 08 (N-B confirmation, A-01), 06, 10 |
| `booking.appointment.requested` | request/approval mode (§7 `confirmationMode='request'`); 08 N-B02 | 08, 04 |
| `booking.session.failed` | `portal.booking.failed` | 08 (ops alert when `needs_attention`) |
| `booking.reschedule.completed` | `portal.reschedule.completed`; 08 consumes `booking.appointment.rescheduled` (emitted by 03 as `scheduling.job.rescheduled`) | 08 |
| `booking.cancellation.completed` | `portal.cancellation.completed`; 08 `booking.appointment.canceled`; 04 `jobs.job.cancelled` | 08, 06 |
| `booking.feedback.submitted` | `portal.feedback.submitted` | 08 (reports) |
| `portal.domain.activated` | 05 consumes `booking.domain.verified` | 05 (wallet domain registration), 08 (link base) |
| `portal.domain.detached` / `portal.domain.reattached` / `portal.domain.removed` | — | 05, 08 |
| `portal.config.published` | — | 08 (email branding uses booking link), edge |
| `portal.account.created` | 07 consumes `portal.account.registered`; 08 `customer.account.created` | 07, 08 |
| `portal.account.signed_in` | 09 `auth.session.created` is staff-side; keep separate | 08 (security notice optional) |
| `portal.auth.challenge_created` | 08 consumes `auth.challenge.created` (N-A02 login code) | 08 |
| `portal.qr.scanned` | same name in 07 | 07, 08 |
| `portal.shortlink.clicked` | 02 has `catalog.shortlink.dead_hit` for unknown/unpublished slugs | 08 (reports), 02 |
| `portal.visit.recorded` (aggregated) | — | 08 (dashboard "portal visits" [S: T05]) |
| `portal.giftcard.purchase_requested` | 06 emits `giftcards.card.issued`; 08 `giftcard.giftcard.purchased` | 06 |
| `portal.subscription.purchase_requested` | 06 emits `subscriptions.subscription.activated` | 06 |
| `portal.referral.applied` | 08 `referral.code.issued` / `referral.referral.rewarded` | 06, 08 |
| `portal.tracking.viewed` | — | 08 (optional analytics) |
| `portal.terms.accepted` | — | 07 (consent ledger), 05 (card-on-file consent proof) |

### 9.1 Agent 02 — Catalog & Coverage

| Direction | What | Contract |
|---|---|---|
| CONSUMES (query) | Public catalog projection | categories (name, icon/image → icon via 11, mobile/shop flag [S: T06]), services (public fields, variants, recurring options [S: T06], duration, image, **short-link slug** [S: T06]), customer add-ons + categories + pairings + stock state [S: T07]. Company add-ons and cost **never** in the public projection [S: T07]. |
| CONSUMES (query) | `catalog.priceSelection` | line pricing & durations for display (no totals) — 01 never computes prices |
| CONSUMES (query) | `coverage.resolveLocation`, `coverage.resolveShop` | `{covered, primaryZoneId, travelCharge, taxRate, eligibleServiceIds, nearestZone}`; shop distance filter [S: T11] |
| CONSUMES (events) | `catalog.revision.published`, `catalog.service.published/archived/price_changed`, `catalog.addon.published/archived`, `catalog.addon_pairing.changed`, `coverage.zone.*`, `coverage.shop.*`, `inventory.stock.depleted/restored` | purge edge cache tag `tenant:{id}:catalog` ≤ 5 s; in-flight sessions re-quote at commit |
| PRODUCES | `coverage.location.out_of_area_attempted` input (01 calls 02's event/command on out-of-area with address) ; `portal.shortlink.clicked`; unknown slug → 02's `catalog.shortlink.dead_hit` | 02 owns slug uniqueness; 01 owns the redirect route |
| SLA | public projection read p99 < 50 ms from cache; `resolveLocation` p95 < 300 ms (geocode cached) |

### 9.2 Agent 03 — Scheduling & Dispatch

| Direction | What | Contract |
|---|---|---|
| CONSUMES (query) | `scheduling.availability.search` | input lines, location, range ≤ 62 d, channel `online`; output days/slots with opaque `token`, route-day entries with label "Anytime Thursday" [S: T02], `ttlSec` (default 60). **01 never caches slots longer than `ttlSec`.** |
| CONSUMES (command) | `scheduling.hold.create/extend/release` | idempotency key = sessionId + slot token; TTL 10 min, max total 20 min (aligned with §5.4) |
| CONSUMES (command) | `scheduling.booking.commit(holdId, jobDraft, idempotencyKey)` | atomic: confirm hold → create appointment (with 04 job id) → auto-dispatch [S: llms]; returns `{jobId, jobNumber, start, end, workerIds?}` or `409 SLOT_TAKEN` |
| CONSUMES (command) | `scheduling.job.reschedule(jobId, slotToken, expectedVersion)` | for B4 |
| CONSUMES (events) | `scheduling.hold.expired` (UI notice), `scheduling.job.rescheduled` / `scheduling.job.unscheduled` (refresh account views), `scheduling.dispatch.decided` (show assigned pro on confirmation/account), `schedule.eta.updated` (tracking ETA) | — |
| PRODUCES | `booking.session.abandoned` (03 releases holds defensively), hold release on step-back | — |

### 9.3 Agent 04 — Job lifecycle

| Direction | What | Contract |
|---|---|---|
| CONSUMES | single job ID & job number for confirmation [S: T04]; job status for account/tracking; quote documents (`/q/{token}`) & invoices (`/i/{token}`) render data | job public id is unguessable; job number is display-only |
| CONSUMES (events) | `jobs.job.status_changed`, `job.job.on_my_way` / `job.job.en_route`, `jobs.job.started`, `jobs.job.completed`, `jobs.job.cancelled`, `jobs.quote.sent/accepted`, `jobs.invoice.issued/paid` | drive tracking link lifecycle (§6.7), account views, quote→book (4.10) |
| PRODUCES | job draft inside `scheduling.booking.commit` (lines, answers, assets, address, notes, terms acceptance id, cancellation policy snapshot id, source) ; `CancelJob(by:'customer', reasonKey)` command | 04 owns cancellation side-effects on the job; 01 owns policy evaluation & feedback |

### 9.4 Agent 05 — Payments

| Direction | What | Contract |
|---|---|---|
| CONSUMES (query) | `payments.quoteTotals` | authoritative totals, tax order, deposit/due-now; 01 shows exactly this |
| CONSUMES (command) | `payments.prepareBookingPayment({draftBookingId=sessionId, customer, lines, zoneId, discountCodes, giftCards})` → `{paymentSessionId, clientSecret, mode}`; `payments.confirmBookingPayment(paymentSessionId)` server-side during saga | modes: after-job, card-on-file (now/after), deposit, prepayment, pre-auth [S: T04][S: T16] |
| CONSUMES (UI component) | processor-hosted payment element (card, Apple Pay, Google Pay) embedded in A12 & B9 (SAQ-A) | 05 provides the component; 01 provides the container, theme tokens (11) and copy |
| CONSUMES (command) | cancellation fee charge / deposit refund / credit; reschedule fee | input = 01's computed fee + policy snapshot id; 05 executes & is idempotent on `(jobId, 'cancel_fee')` |
| CONSUMES (events) | `payments.payment.succeeded/requires_action/authorized`, `payments.payment.failed`, `payments.refund.succeeded` | session state transitions & account receipts |
| PRODUCES | `portal.domain.activated/detached/removed` (05 registers/unregisters wallet domains — **05 listens for `booking.domain.verified`; Agent 10 to align**); `portal.terms.accepted` (card-on-file consent proof) | — |
| Rule | Never charge an amount whose `quoteHash` the customer didn't see; void/cancel intents if saga fails before job creation | — |

### 9.5 Agent 06 — Subscriptions, discounts, gift cards, referral rewards

| Direction | What | Contract |
|---|---|---|
| CONSUMES (query) | packages flagged self-purchase / upsell-during-booking [S: T01], commitment options, included discounts | portal never shows CRM-only packages |
| CONSUMES (command) | discount code validate → reserve at commit (`discounts.redemption.reserved` → `committed`); gift card balance/redeem; referral code validate; subscription purchase/cancel/pause; gift card purchase/issue | reservation tied to sessionId, released on abandon/expire |
| CONSUMES (events) | `giftcards.card.issued` (confirmation page), `subscriptions.subscription.activated/cancel_scheduled`, `subscriptions.package.published/archived` (cache purge) | — |
| PRODUCES | `portal.giftcard.purchase_requested`, `portal.subscription.purchase_requested`, `portal.referral.applied`, `booking.appointment.created` (for referral reward evaluation and first-booking promos) | — |

### 9.6 Agent 07 — Customers, leads, assets, abandoned bookings

| Direction | What | Contract |
|---|---|---|
| PRODUCES (events) | full session telemetry (§9.0): started, step_reached (per step, not keystroke), contact_captured (name/phone/email/address + consent flags), payment_started, abandoned, expired, appointment.created | 07 owns the lead / abandoned-booking record, magic-data enrichment [S: T10][S: T20], dedupe, lead board |
| CONSUMES (command) | `MatchCustomer` (silent, server-only — result never revealed to an unauthenticated visitor), `UpsertCustomer` with addresses, assets, intake answers at commit; asset CRUD for B6; vehicle lookup (VIN/plate/YMM [S: llms]) | 07 owns schemas for assets & intake storage |
| CONSUMES (events) | `customer.customer.merged` (re-point PortalIdentity), `customer.customer.erased` (delete PortalIdentity, sessions), `customer.consent.changed`, `customer.asset.updated`, `customer.intake.updated` | — |
| PRODUCES | `portal.account.created` (07 expects name `portal.account.registered`), `portal.account.deleted`, `portal.qr.scanned` | linking rules (verified identifier only) per 07 §state machine |

### 9.7 Agent 08 — Notifications, marketing, reporting

| Direction | What | Contract |
|---|---|---|
| PRODUCES | every customer-facing trigger above; 01 sends **no** messages itself. Payloads include `links.*` computed by `portal.bookingBaseUrl` (manage link, resume link, tracking link, invoice/quote link) | 08 owns templates/consent; 08's `links.booking` variable = `portal.bookingBaseUrl(tenant)` |
| Triggers fired by 01 (catalog in 08) | booking confirmation (online), request received (approval mode), reschedule confirmation, cancellation confirmation (+fee/refund), OTP / magic link (`auth.challenge.created`), resume-your-booking (abandoned; consent-gated), gift card delivery & receipt, subscription welcome, tracking link (on-my-way, triggered by 04's event but link minted by 01), domain activated/detached alerts to owner, cert renewal failing alert to owner | — |
| CONSUMES (query) | 08's consent state for the contact step checkbox text (TCPA disclosure in 08 §consent table) | 01 renders 08's exact disclosure string per locale |
| CONSUMES | review page & unsubscribe page are **hosted on the booking host** (08 specs say so) → 01 provides routing/theming shell; 08 provides content | — |
| PRODUCES (reporting) | `portal.visit.recorded` aggregates, funnel counts, booking-source attribution per booking | 08 renders dashboard tiles ("booking sources" [S: T05]) |

### 9.8 Agent 09 — Team, permissions

| Direction | What | Contract |
|---|---|---|
| CONSUMES | permission `settings.online_booking.manage` (portal settings), owner/admin role for domains, `marketing.analytics.view` for reports; worker public profile (first name, photo, optional bio) for confirmation & tracking | worker phone/last name never exposed unless tenant opts in |
| CONSUMES (events) | `team.member.deactivated` (remove from "choose your pro" list; revoke live tracking links), `team.location.pinged` (via 03/04 → tracking stream) | — |

### 9.9 Agent 11 — Branding, theming & responsive design system (added 2026-11-13)

| Direction | What | Contract |
|---|---|---|
| CONSUMES (query) | compiled theme tokens for light + dark (colors, shadows, radius, density, typography scale, font faces) and asset manifest (logos per placement incl. `portal_header`, `portal_header_mobile`, `favicon`, `app_icon`, `og_image`, light/dark variants with sizes; icon map for categories/services/nav) — versioned (`themeId`, `themeVersion`) | delivered as one ≤ 4 KB CSS block + manifest JSON; fonts as WOFF2 URLs on our CDN |
| CONSUMES (events) | `branding.theme.published` (name to be fixed by 11/10) → purge `tenant:{id}:config` and re-render hero/OG images | ≤ 5 s propagation |
| PRODUCES | portal constraints: contrast minima (AA), max font files/weights preloaded, required logo placements & aspect-ratio bounds, breakpoints used by the portal (phone < 600 px, tablet 600–1024, desktop > 1024), embed/popup sizing | 11 validates uploads against these |
| Shared | Style screen §3.D1 becomes a portal-specific tab inside 11's brand editor (live preview of booking flow in light & dark, phone/tablet/desktop) | 01 supplies preview routes `portal.preview.token` |

### 9.10 Agent 10 — integrator notes
- Saga ownership: 01 orchestrates the online booking saga; 03's `scheduling.booking.commit` is the
  single atomic reservation point; 05 verifies payment server-side inside it. Same saga is reused
  by MCP agents and by the future native customer app.
- `portal.bookingBaseUrl` is the only allowed way for any module to generate customer links.
