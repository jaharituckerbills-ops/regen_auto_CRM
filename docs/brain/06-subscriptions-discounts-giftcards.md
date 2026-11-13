# 06 — Subscriptions, Discounts & Gift Cards

> Agent 06 · Phase 1 "CRM brain" · 2026-11-12
> Scope: recurring revenue (subscription packages, customer subscriptions, prepaid "appointment
> packages"), price modifiers (discount codes, promotions, recurring/subscription discounts) and
> stored value (gift cards).
>
> Citation convention (per BRIEF.md): `[S: <file>]` = stated in a research file;
> `[I]` = inferred / industry convention / our design decision. Research file short names used
> below:
> - `01` = `research/transcripts/01_mP9YP49qDiI.txt` (+ `.description`) — Subscriptions
> - `05` = `research/transcripts/05_0qNwovgNTqM.txt` — Online Booking Page
> - `06` = `research/transcripts/06_0atw7gNWDzg.txt` — Services & Prices
> - `07` = `research/transcripts/07_uD5ND6GyoYA.txt` — Add-Ons
> - `08` = `research/transcripts/08_w3D1W6oOaNY.txt` — Jobs & Appointments
> - `09` = `research/transcripts/09_xO4jahos3rE.txt` — CRM Tips (discounts, gift cards)
> - `11` = `research/transcripts/11_8sT9cFazZAA.txt` — Service Areas (zone tax, travel fee)
> - `12` = `research/transcripts/12_4BwGvptaW6s.txt` — Drip Campaigns
> - `14` = `research/transcripts/14_5zqqoRbV1DU.txt` — Recurring Job
> - `15` = `research/transcripts/15_Dh1Ib3a4psU.txt` — Book a Job
> - `16` = `research/transcripts/16_G43PcI4hWOk.txt` — Payment Methods
> - `17` = `research/transcripts/17_UezcRXW1BcE.txt` — Transactions
> - `19` = `research/transcripts/19_ZPf7JSlvIcw.txt` — Schedule a Job
> - `llms` = `research/site/llms.txt` — Fieldd official facts (2026-10-23)
>
> So `[S: 01]` means "stated in transcript 01". All money is **integer minor units (cents)**.
> Nothing here assumes a stack; interfaces are TypeScript-style for precision only.

---

## 1. Summary

This module turns one-off service revenue into **predictable recurring revenue** and owns every
**price modifier** and **stored-value instrument** in the platform:

1. **Subscription packages** — sellable plans with a name, description, billing cycle
   (weekly / monthly / yearly) and price per cycle `[S: 01]`, optional **commitment options**
   (prepay several cycles up front for a discount) `[S: 01]`, optional **self-purchase online**
   and **upsell during booking** `[S: 01]`, optional **automatic scheduling** at a booking
   frequency `[S: 01]`, **included services** `[S: 01]` and **discounts / included price
   reductions** (e.g. "$99/mo → 20% off all services + 1 free add-on each month") `[S: 01]`.
2. **Customer subscriptions** — an instance of a package assigned to a customer, from the CRM
   (New → Subscription) `[S: 01]` or bought online `[S: 01]`, with a lifecycle (active, past
   due, paused, cancelled, renewed …) `[I]`, a per-period **benefit ledger** `[I]` and, when
   enabled, a linked **auto-scheduled recurring series** of jobs `[S: 01]` whose generation is
   delegated to Agent 03 `[I]`.
3. **Appointment packages / prepaid visit bundles** — the owner's specific interest. A one-time
   purchase that grants N visits (e.g. "5 Exterior Details, use within 12 months") `[I]`. Not
   shown in the Fieldd tutorials; we model it as a package with `billing.kind = 'one_time'` so it
   reuses the same entitlement ledger `[I]`.
4. **Discounts** — time-sensitive promotions and lasting discount codes `[S: 09]`, scoped to
   services, add-ons, customer types or booking conditions `[S: 09]`; attachable/creatable while
   booking a job `[S: 16]` `[S: 19]`; plus the per-occurrence **recurring discount** (fixed $ or
   %) defined on a recurring job `[S: 14]` and the recurring-booking discount suggested on a
   service `[S: 06]`. This module owns the **single price-modifier evaluation engine** that all
   of these flow through `[I]`.
5. **Gift cards** — issued manually or sold online through the booking page `[S: 09]`; customers
   can "buy and send gift cards" from the customer portal `[S: 05]`; can be added to a job under
   Options `[S: 16]`; listed alongside discounts when the business uses Fieldd's payment
   processor `[S: 09]`; AI-designed card artwork `[S: llms]`; included in the entry "Start" plan
   `[S: llms]`. We own codes, the balance **ledger**, partial redemption, expiry policy, refunds.

### 1.1 Three things that look alike — defined precisely

This is the most important conceptual boundary in the module. Getting it wrong produces double
discounts, unbilled jobs, and support nightmares.

| | **Recurring job** (video 14) | **Subscription** (video 01) | **Prepaid visit bundle** ("appointment package") |
|---|---|---|---|
| What it is | A **scheduling series**: the same job repeated on a frequency `[S: 14]` | A **billing agreement**: customer pays a fixed price every billing cycle `[S: 01]` | A **one-time purchase** of N visit credits `[I]` |
| Owner module | Agent 03 (series & occurrences) | Agent 06 (this) | Agent 06 (this) |
| Money flow | Each occurrence is priced and paid **per job** (job price − recurring discount) `[S: 14]` `[I]` | Charged **per cycle whether or not a job happens** `[I]`; covered jobs cost $0 or are discounted `[S: 01]` | Charged **once up front**; each redeemed visit costs $0 `[I]` |
| Discount type | Recurring discount: fixed $ or % off **each job** `[S: 14]` | Member benefits: included services, % off services, free add-ons per period `[S: 01]` | Bundle price < N × list price `[I]` |
| End condition | Infinite, end date, or after N jobs `[S: 14]` | Until cancelled; commitment term may restrict early cancel `[S: 01]` `[I]` | When credits used or expired `[I]` |
| Creates jobs? | Yes, always `[S: 14]` | Only if "automatic scheduling" is on `[S: 01]`; otherwise customer books and benefits apply `[I]` | No; customer books and redeems a credit `[I]` |
| Customer can skip a visit? | Yes (cancel one occurrence) `[I]` | Yes; the period's credit stays (and expires per rollover rule) `[I]` | n/a |
| Revenue predictability | Medium (customer may cancel any time; nothing charged until job) `[I]` | High (MRR) `[S: 01]` | Cash up front; deferred revenue `[I]` |
| Where seen in CRM | Jobs window → Recurring tab `[S: 14]` | Transactions → Subscriptions `[S: 01]` | Transactions → Subscriptions (filter "Bundles") `[I]` |

**Hybrid (subscription with automatic scheduling):** the subscription owns the money and the
benefits; it **asks Agent 03 to create a recurring series** whose occurrences carry
`funding = { kind: 'subscription', subscriptionId }`. Occurrences in such a series must **not**
also receive the series' recurring discount (video 14) — the subscription's coverage replaces
it. See §5.6 stacking matrix `[I]`.

### 1.2 Design principles

- **Ledgers, not counters.** Gift card balances and subscription benefits are computed from
  append-only ledgers; a cached balance is a projection that can always be rebuilt `[I]`.
- **One pricing pipeline.** Every price shown (booking widget, CRM, worker POS, invoice) comes
  from `pricing.evaluate()` with the same order of operations (§5.1); this module provides the
  modifier stages `[I]`.
- **Snapshots.** Packages and discounts are versioned; subscriptions and job price lines
  reference the version they were sold/evaluated under, so edits never retroactively change a
  customer's deal `[I]`.
- **Idempotent money.** Every charge, ledger entry and redemption carries an idempotency key
  derived from business identity (e.g. `sub:{id}:period:{seq}:charge`) `[I]`.
- **Gift cards are tender, not discounts.** They reduce the amount *paid*, never the price or
  tax base `[I]` (consistent with US sales-tax treatment of gift cards as cash equivalents).

---

## 2. Roles

| Role | Packages | Customer subscriptions | Discounts | Gift cards |
|---|---|---|---|---|
| **Owner** | Create/edit/archive/publish `[S: 01]` | Full: create, change plan, pause, cancel, refund, comp periods, override dunning | Full CRUD, view cost reports | Issue, void, adjust balance, refund, configure expiry/fees, design |
| **Admin** | Same as owner `[I]` | Same as owner except payout/processor settings `[I]` | Full CRUD `[I]` | Issue, void, adjust (with reason) `[I]` |
| **Manager** | View; edit if permission `subs.package.edit` `[I]` | Create, pause, skip visit, update payment method; cancel only if permitted `[I]` | Create codes within owner-set caps (e.g. max 20% off) `[I]` | Issue up to a per-issue cap; no void `[I]` |
| **Foreman / Worker** (team app) | — | See "Member" badge + covered items on job; cannot change subscription `[I]` | Apply pre-approved codes or manual discount up to cap at POS if permitted `[I]` (POS discount attach is shown for jobs `[S: 16]`) | Redeem at POS (scan / type code) `[I]`; sell gift cards at POS if permitted `[I]` |
| **Customer** (booking site / customer app) | Browse packages marked "available for self-purchase" `[S: 01]` | Buy `[S: 01]`, see benefits & usage, update card, pause/skip/cancel per business policy `[I]` | Enter promo code at checkout `[I]` | Buy and send `[S: 05]`, redeem at checkout `[I]`, check balance `[I]` |
| **System** (scheduler) | — | Bill cycles, dunning retries, renewals, commitment end, period rollover, auto-schedule horizon `[I]` | Expire promotions, enforce usage limits `[I]` | Scheduled delivery, expiry (where lawful), dormancy (off by default) `[I]` |
| **AI agents** (MCP / built-in assistant) | Read; create via CRM-level tools `[S: llms]` (MCP has CRM-level and booking-level access) | CRM MCP: full; Bookings MCP: customer-level purchase only `[S: llms]` `[I]` | CRM MCP: CRUD; Bookings MCP: apply code only `[I]` | AI card design `[S: llms]`; Bookings MCP: buy/redeem only `[I]` |

Permission keys (registered with Agent 09's RBAC matrix) `[I]`:
`subs.package.view|edit|publish|archive`, `subs.subscription.view|create|edit|pause|cancel|refund|comp`,
`subs.dunning.override`, `discounts.view|create|edit|delete|apply_manual`, `discounts.manual.max_pct`,
`discounts.manual.max_cents`, `giftcards.view|issue|void|adjust|refund|sell_pos|redeem_pos`,
`giftcards.issue.max_cents`, `reports.recurring_revenue.view`.

---

## 3. Screens

All layouts are `[I]` unless a citation says otherwise; the owner's walkthrough video is the
visual source of truth. Navigation entry points that are cited: **New → Subscription Package**,
**New → Subscription** `[S: 01]`; **Transactions → Subscriptions**, dropdown → **Packages**
`[S: 01]`; discounts and gift cards live in the CRM back office near Transactions/People
`[S: 09]` (exact nav placement unknown — verify).

### 3.1 New → Subscription Package (package editor wizard)

Entry: global **New** menu → "Subscription Package" `[S: 01]`; also "+ New package" on the
Packages list `[I]`, and "Create new" inline from the New → Subscription wizard `[S: 01]`.

Layout `[I]`: full-page stepper on desktop (left: step list; centre: form; right: live
**preview card** showing how the package appears on the booking page and a "customer pays / gets"
summary). Mobile: single column, steps as an accordion, preview collapses to a sticky bottom
sheet "Preview".

Steps follow the video's order `[S: 01]`: (1) Package → (2) Pricing & commitment → (3) Online
sales → (4) Scheduling → (5) Included services & discounts → (6) Review & save.

**Step 1 — Package** `[S: 01]`

| Field | Type | Validation | Default |
|---|---|---|---|
| Name | text | required, 2–80 chars, unique among non-archived packages (case-insensitive) `[I]` | — |
| Description | rich text (bold, lists, links) | ≤ 2,000 chars; sanitized HTML `[I]` | — |
| Package type | segmented: Subscription / Prepaid bundle | `[I]` (bundle is our extension) | Subscription |
| Image | image upload | ≤ 5 MB, jpg/png/webp; auto-resized `[I]` | none (generated gradient tile) |
| Internal name / SKU | text | optional, ≤ 40 `[I]` | — |
| Service area restriction | multi-select zones (Agent 02) | optional; empty = all zones `[I]` | all |
| Customer type restriction | residential / business / both | `[I]` | both |

**Step 2 — Pricing & commitment** `[S: 01]`

| Field | Type | Validation | Default |
|---|---|---|---|
| Billing cycle | select: Weekly / Every 2 weeks / Monthly / Quarterly / Yearly | weekly/monthly/yearly are cited `[S: 01]`; biweekly/quarterly `[I]` | Monthly |
| Price per cycle | money | integer cents, ≥ 0 (0 allowed for free tier/comp plans `[I]`), ≤ 1,000,000.00 | — |
| Currency | read-only | business currency `[I]` | business |
| Tax behaviour | select: Tax-exclusive / Tax-inclusive / Not taxable | `[I]`; resolved by Agent 05 tax engine per service address | business default |
| Setup / joining fee | money, optional | ≥ 0 `[I]` | 0 |
| Free trial | number + unit (days/cycles) | 0–90 days `[I]`; trial requires card on file | off |
| Commitment options | repeatable rows (see below) `[S: 01]` | max 6 rows `[I]` | none = month-to-month only |
| Allow month-to-month | toggle | at least one of month-to-month or ≥1 commitment must exist `[I]` | on |
| Benefit reset basis | select: Per billing cycle / Per calendar month | `[I]` | Per billing cycle |

Commitment option row `[S: 01]` ("pay for several months in advance … often comes with a
discount"); fields `[I]`:

| Field | Type | Validation |
|---|---|---|
| Term | integer cycles (e.g. 3, 6, 12) | 2–36, unique per package |
| Payment mode | select: **Prepay in full** (cited behaviour `[S: 01]`) / **Commit, pay per cycle** (`[I]`, common gym-style) | — |
| Discount | % or fixed $ off the term total | % 0–90; fixed < term total |
| After term ends | select: Renew same term / Roll to month-to-month / End subscription | default Roll to month-to-month `[I]` |
| Early cancellation | select: Not allowed (until term end) / Allowed, no refund / Allowed, prorated refund minus fee / Allowed, pay remaining balance | default "Allowed, no refund" for prepay; "Not allowed" disabled for prepay `[I]` |
| Label | text ≤ 40 | e.g. "Save 15% — 6 months" |

Inline computed helper `[I]`: "6 × $99.00 = $594.00 − 15% = **$504.90** ($84.15/mo effective)".

**Step 3 — Online sales** `[S: 01]`

| Field | Type | Notes |
|---|---|---|
| Available for self-purchase online | toggle | `[S: 01]`; when off, sold only from CRM `[S: 01]` |
| Upsell during booking | toggle | `[S: 01]` |
| Upsell trigger services | multi-select services | `[I]` — show the upsell only when the cart contains one of these; empty = when cart overlaps any included service |
| Upsell headline / savings message | text ≤ 120, with token `{savings}` | `[I]` e.g. "Members save {savings} on this booking" |
| Show on packages page | toggle | `[I]` booking site "Memberships" tab (Agent 01) |
| Sort order | number | `[I]` |
| Terms & cancellation text | rich text | `[I]` required if self-purchase on (auto-renewal disclosure — §5.11) |
| Max active subscribers | integer, optional | `[I]` capacity cap (e.g. limited founding-member offer) |

**Step 4 — Scheduling** `[S: 01]`

| Field | Type | Notes |
|---|---|---|
| Automatic scheduling | toggle | `[S: 01]` |
| Booking frequency | select: Weekly / Every 2 weeks / Every 3 weeks / Every 4 weeks / Monthly (same date) / Monthly (same weekday, e.g. 2nd Tue) / Every N months | `[S: 01]` "select the booking frequency"; options list `[I]` aligned with Agent 03 recurrence rules (video 14 cites daily/weekly/monthly `[S: 14]`) |
| Visit services | multi-select from included services | `[I]` which service(s) each auto visit books |
| Preferred window | customer chooses at purchase / owner sets | `[I]` |
| Worker assignment | Auto (best available) / Keep same worker | `[I]`; video 14 says auto picks best available worker per job `[S: 14]` |
| Scheduling horizon | read-only | system setting (default 8 weeks / 3 occurrences ahead) `[I]` |
| Visits beyond inclusion | select: Charge at member price / Not allowed | `[I]` (when frequency produces more visits than included count, e.g. weekly visits with "4 per month") |

When off: "Customers book whenever they like; package benefits apply automatically" `[I]`.

**Step 5 — Included services & discounts** `[S: 01]`

Two sub-sections.

*(a) Included services* `[S: 01]` — repeatable rows `[I]`:

| Field | Type | Validation |
|---|---|---|
| Service or add-on | picker (services, customer add-ons, company add-ons from Agent 02) | required |
| Quantity per period | integer or "Unlimited" | 1–99; Unlimited requires a fair-use cap or auto-scheduling `[I]` |
| Period | select: Per billing cycle / Per week / Per month / Per year / Whole subscription | `[I]` — enables "yearly plan, 1 free add-on **per month**" |
| Coverage | 100% (free) / % off / fixed $ off / fixed member price | `[I]` |
| Rollover | None / Roll over up to N / Roll over unlimited (expire after M periods) | default None `[I]` |
| Variant / size scoping | optional (e.g. only "Sedan" price tier) | `[I]` — if the service has variant pricing (Agent 02), coverage applies to the cheapest/selected variant; upcharge for larger variant is billed |
| Includes duration add-ons? | toggle | `[I]` whether attached time-based add-ons are covered |

*(b) Member discounts / included price reductions* `[S: 01]` — repeatable rows `[I]`:

| Field | Type |
|---|---|
| Discount | % off or fixed $ off |
| Applies to | All services / service categories / specific services / all add-ons / specific add-ons / products `[I]` |
| Excludes | travel fees, deposits, tips, gift card purchases (always excluded) `[I]` |
| Frequency | Every booking / First N bookings per period `[I]` |
| Stacks with promo codes | toggle, default off `[I]` |

Example rendered helper `[S: 01]`: "$99/month → 20% off all services + 1 free add-on each month."

Preview text auto-generated from rows `[I]`: "Includes: 1 × Full Detail per month · 1 × any add-on per
month · 20% off all other services".

**Step 6 — Review & save** `[S: 01]`

Read-only summary of every step with "Edit" links; estimated customer savings at typical usage
`[I]`; warnings (e.g. "Price $0 and no trial — is this a comp plan?", "Self-purchase on but no
terms text", "Included service X is mobile-only but package is offered in shop zones") `[I]`.
Buttons: **Save as draft**, **Save & publish** `[I]`. Video says "review your package details
and click save" `[S: 01]`.

States: loading (skeleton form), validation errors inline + summary at top, save conflict
("Edited by Alex 2 min ago — reload / overwrite") using optimistic version `[I]`, success toast
with "Assign to a customer" CTA `[I]`.

Editing a package with active subscribers `[I]`: a banner "34 active subscribers. Changes create
version 3. Apply to: ○ New subscribers only ○ Everyone at their next renewal (sends notice)".
Price increases to existing subscribers require notice (§5.11).

### 3.2 New → Subscription (assign to customer wizard)

Entry: **New → Subscription** `[S: 01]`; also customer profile → "Add subscription" `[I]`;
from a job → "Convert to membership" `[I]`.

Steps `[S: 01]`: (1) Customer → (2) Package → (3) Billing → (4) Review → (5) Payment & activate.

| Step | Fields / behaviour |
|---|---|
| 1 Customer | Search existing customer (typeahead by name/phone/email/address) or **+** to create a new one `[S: 01]` (inline mini-form owned by Agent 07: first/last name, phone, email, service address). Shows warning if customer already has an active subscription to the same package `[I]`. |
| 2 Package | Card list of active packages (incl. CRM-only ones) or **create new** `[S: 01]` (opens 3.1 in a drawer, returns selection). Choose commitment option if any `[S: 01]` `[I]`. Optional: apply promo code (subscription-scoped discounts) `[I]`. |
| 3 Billing | "When billing should begin" `[S: 01]`: Today / Specific date / After first visit `[I]`. "How often" `[S: 01]`: defaults to package cycle; editable only to cycles the package offers `[I]`. Billing anchor day shown `[I]`. Service address + asset/vehicle (Agent 07) `[I]`. If auto-scheduling: first visit date/time picker using Agent 03 availability, preferred window, worker preference `[I]`. Payment method: card on file / new card (processor-hosted fields, Agent 05) / "Invoice each cycle" (manual pay) `[I]`. Send receipt via SMS/email toggles `[I]`. |
| 4 Review | Summary: package, price, commitment, first charge amount & date, proration (if anchored), tax, discount, next charge date, scheduled visits preview (first 3), terms acceptance record method ("Customer agreed verbally / in person / sent link to accept") `[I]`. |
| 5 Payment & activate | "Process the payment and activate the subscription" `[S: 01]`. Button label: "Charge $X & activate" or "Activate (first charge on {date})" `[I]`. On success → subscription detail page `[S: 01]` ("you'll be able to view the customer's subscription details"). On card decline → stays on step with processor error and "Try another card / Send payment link" `[I]`. |

Alternative path `[I]`: "Send to customer to complete" — creates subscription in `pending_activation`
and sends a secure checkout link (customer enters card + accepts terms), which is the
compliance-safest way to capture consent for auto-renewal.

### 3.3 Transactions → Subscriptions (list) and → Packages (dropdown)

Entry: **Transactions** tab → **Subscriptions** `[S: 01]`. A dropdown switches the list between
**Subscriptions** and **Packages** `[S: 01]`.

**Subscriptions list** `[S: 01]` "view the list of active subscriptions. Click on any customer
to see their subscription details". Layout `[I]`:

- Top KPI strip: Active subscribers · MRR · Past due (count, $) · Churn (30d) · Upcoming renewals (7d).
- Filters: status (Active default, Trialing, Past due, Paused, Pending cancel, Cancelled,
  Expired, All), package, commitment (yes/no), billing cycle, next charge date range, service
  area, tag, payment method state (expiring soon / missing).
- Columns: Customer · Package · Status chip · Price/cycle · Commitment (ends {date}) · Next
  charge · Benefits used this period (e.g. "1/1 wash · 0/1 add-on") · Started · Lifetime value.
- Row click → subscription detail `[S: 01]`.
- Bulk: export CSV (every list exports, consistent with Fieldd's "download icon" pattern
  `[S: 09]`), send message (Agent 08), apply price change at renewal `[I]`.
- Empty state: "No subscriptions yet — create a package and sell it online or assign it to a
  customer" with two CTAs `[I]`.

**Packages list** `[S: 01]` "you'll see all available subscription packages". Columns `[I]`:
Name · Type (Subscription/Bundle) · Price/cycle · Online (self-purchase/upsell icons) · Auto-schedule
· Active subscribers · MRR · Status (Draft/Published/Archived). Actions: edit, duplicate, copy
booking short link (Agent 01; parallels per-service short links `[S: 06]`), archive.

### 3.4 Subscription detail (per customer)

Entry: list row, customer profile "Subscriptions" card, job card "Member" badge `[I]`. Layout `[I]`:

- **Header:** customer name (link to profile), package name + version, status chip, price/cycle,
  commitment progress ("Month 4 of 6"), next charge date & amount, payment method (brand ••••1234,
  expiry warning).
- **Actions menu:** Change package · Change billing date · Update payment method / send update
  link · Pause (choose resume date or "until resumed") · Skip next visit · Retry payment now
  (when past due) · Comp next period · Apply discount · Cancel (immediately / at period end /
  at commitment end) · Refund last charge · Resend receipt · View as customer.
- **Benefits this period** card: meters per entitlement ("Full Detail 1 of 1 used — Oct 14 job
  #4821"; "Free add-on 0 of 1 — expires Nov 30"; "20% off services — saved $36.00 this period").
- **Visits** (if auto-scheduling): next 3 upcoming jobs (date, worker, status, covered/charged),
  "Manage schedule" (opens Agent 03 series editor), past visits.
- **Billing history:** periods with charge status, amount, tax, discount, receipt link, refund
  link, dunning attempts timeline.
- **Activity log:** every state change with actor (user / customer / system / AI) and reason.
- **Savings to date** `[I]`: lifetime list-price value of covered items minus fees paid — great
  retention lever for the customer view.

States: past-due banner (red) with "Retry now" and "Send card update link"; paused banner
(grey) with resume date; pending-cancel banner with "Undo cancellation" `[I]`.

### 3.5 Customer profile — Subscriptions card (hosted by Agent 07)

Compact card: active subscription(s), status, next visit, benefits remaining, quick actions
(Skip, Pause, Cancel). Also a **Gift cards & credits** card listing gift cards owned/purchased with
balances `[I]`.

### 3.6 Job surfaces (hosted by Agents 03/04/05, data from 06)

- **CRM booking form → Options** `[S: 16]`: "attach or create a discount, apply tax, or add a
  gift card". Same in "Schedule a Job" `[S: 19]` ("add discounts, tax and a card on file").
  We provide: discount picker (search active codes + "Custom discount" with % / $ within the
  user's cap), gift card field (code entry, or pick from customer's wallet), and a **member
  coverage panel** that auto-appears when the customer has an active subscription ("Covered by
  Gold Membership: Full Detail (1 of 1 left this period). Member 20% off applied to add-ons.").
- **Recurring job → scheduling** `[S: 14]`: "set the discount the customer will receive for
  booking on a recurring basis … fixed dollar amount or a percentage off of each job". The
  field is rendered by Agent 03's form; the discount is stored as a `PriceModifier` of kind
  `recurring` (§7.4) evaluated by our engine.
- **Price breakdown** (every surface) `[I]`: Subtotal → Member coverage → Member discount →
  Recurring discount → Promo `CODE` → Travel fee → Tax → Total → Gift card applied → **Amount due**.
- **Worker app job view** `[I]`: "MEMBER" badge; covered lines show "$0.00 · Covered"; POS
  allows adding a gift card / discount if the worker has permission `[S: 16]` (POS can attach
  discounts/gift cards in CRM; worker-app POS parity `[I]`).

### 3.7 Discounts list & editor

Entry: CRM back office "Discounts" `[S: 09]` (nav location to verify). Video: "create time
sensitive promotions or lasting discount codes … tied to services, add ons, customer types or
booking conditions" `[S: 09]`.

**List** `[I]`: tabs Active · Scheduled · Expired · Archived. Columns: Name · Code (or
"Automatic") · Value (20% / $15) · Scope summary · Valid (dates) · Uses (used / limit) · Revenue
influenced · Discount cost · Status. Search by code. Export CSV.

**Editor** — single page with sections `[I]` (all fields `[I]` except where cited):

| Section | Field | Type / validation | Default |
|---|---|---|---|
| Basics | Name (internal) | text 2–80 | — |
| | Customer-facing label | text ≤ 60 (shown on receipt) | = name |
| | Application method | **Code** (customer/staff enters) / **Automatic** (applies when conditions met) / **Manual only** (staff picks from list) | Code |
| | Code | 3–32 chars, `[A-Z0-9_-]`, case-insensitive unique among active+scheduled; "Generate" button | — |
| | Bulk unique codes | toggle → generate N single-use codes with prefix (e.g. `FALL-7KQ2M`) | off |
| Value | Type | % off · $ off each eligible item · $ off order · Fixed price · Free item (add-on) | % off |
| | Amount | % 0.01–100 (2 dp) / cents > 0 | — |
| | Max discount cap | cents, optional (e.g. 20% up to $50) | none |
| Scope (`[S: 09]` services, add-ons) | Applies to | All services / categories / services / add-ons / subscription packages / gift cards **never** | All services |
| | Exclude items | multi-select | — |
| | Applies to travel fee | toggle | off |
| Customer types (`[S: 09]`) | Eligible customers | Everyone / New customers (no completed jobs) / Returning / Customer tags (any/all) / Residential / Business / Members of package(s) / Non-members / Specific customers | Everyone |
| Booking conditions (`[S: 09]`) | Channel | Online booking / CRM / Worker app / Customer app / AI booking (MCP) | all |
| | Minimum subtotal | cents | 0 |
| | Minimum # services in cart | int | 0 |
| | Service areas / shops | multi-select | all |
| | Service date window | date range + days of week + time-of-day window (off-peak promos) | any |
| | Lead time | book ≥ N days ahead / last-minute ≤ N hours | any |
| | Booking type | One-time / Recurring series / Subscription purchase | all |
| | First booking only | toggle | off |
| Time window ("time sensitive" `[S: 09]`) | Starts / Ends | datetime in business TZ; Ends optional ("lasting" `[S: 09]`) | now / none |
| | Validity basis | Booking date / Service date / Both | Booking date |
| Limits | Total uses | int, optional | unlimited |
| | Uses per customer | int, optional | 1 for codes? → default unlimited, recommend 1 |
| | Total discount budget | cents, optional ("stop after $2,000 given away") | none |
| Recurring behaviour | On a recurring series | First occurrence only / First N occurrences / Every occurrence | First occurrence |
| | On a subscription purchase | Once (first charge) / First N cycles / Forever | Once |
| Stacking | Combine with | Nothing (exclusive) / Other promo codes / Member discounts / Recurring discounts (checkboxes) | Member+Recurring unchecked |
| | Priority | int (lower = first) | 100 |
| Display | Show on booking page banner | toggle + banner text | off |
| | Shareable link | auto: `https://<booking-subdomain>/?promo=CODE` (Agent 01 pre-fills) | — |

States: preview tester `[I]` — "Test against a cart": pick services/customer/date → shows whether
eligible and why not (each failed condition listed). Save validates conflicts ("Code FALL20 already
used by an active discount").

### 3.8 Gift cards list, detail, issue dialog, designer

Entry: near Discounts `[S: 09]` ("If you're using [the processor], you'll also see your gift cards
listed here"). We do **not** gate gift cards on our own processor `[I]` — §12.

**List** `[I]`: columns Code (masked `•••• 7KQ2`) · Recipient · Purchaser · Source (Manual /
Online / POS / Promotional / Refund) · Issued · Original value · Balance · Last used · Status
(Active / Redeemed / Voided / Expired / Pending delivery). KPI strip: Outstanding liability ·
Sold (30d) · Redeemed (30d) · Breakage estimate. Filters, search by full code, recipient,
purchaser. Export CSV.

**Issue gift card dialog** ("issued manually" `[S: 09]`) `[I]`:

| Field | Type / validation | Default |
|---|---|---|
| Kind | Paid (customer paid) / Promotional (free) / Store credit (refund substitute) | Paid |
| Amount | cents, 1.00–2,000.00 (owner-configurable max) | presets 25/50/100 |
| Payment (if Paid) | card/cash/POS via Agent 05; creates a sale | — |
| Recipient | customer picker or name + email/phone | — |
| Purchaser | customer picker (optional) | — |
| Message | ≤ 300 chars | — |
| Design | template gallery / AI design `[S: llms]` / upload | business default |
| Delivery | Send now / Schedule (date+time, recipient's TZ) / Print / Don't send | Send now |
| Expiry | per policy (§5.10); promotional cards may expire with disclosure | none |
| Internal note | text | — |

**Detail page** `[I]`: card preview, full code reveal (permission-gated, audited), balance,
**ledger table** (date, type, amount, balance after, job/invoice link, actor), actions: Resend,
Adjust balance (+/− with reason), Void, Refund (to original payment), Reissue new code (lost card
— moves balance), Change recipient.

**Designer** `[S: llms]` ("design a gift card in seconds with AI") `[I]`: prompt box ("Holiday
card with a snowy car wash"), brand colours/logo auto-applied, 4 variants, choose → saved as
`GiftCardDesign`; fallback templates if AI unavailable; designs reviewed by a safety filter;
fixed aspect 1.586:1 (ID-1 card) with safe text area for amount/code.

### 3.9 Customer-facing surfaces (rendered by Agent 01; contract in §9)

- **Packages / Memberships page** on the booking subdomain: cards for packages with
  `selfPurchase=true` `[S: 01]`; detail view → checkout (account, address, card, terms,
  commitment choice, first visit if auto-scheduling).
- **Upsell during booking** `[S: 01]`: after services/add-ons are chosen (before payment), a card:
  "Become a Gold Member — this wash would be **$0** and you'd save $36/mo. $99/mo, cancel
  anytime." Actions: "Add membership" (cart re-prices instantly via our quote API) / "No thanks".
  Must not shift layout (reserve height) and must be dismissible; shown at most once per session
  `[I]`.
- **Promo code field** at checkout (collapsed "Have a code?" link) `[I]`; pre-filled from `?promo=`.
- **Gift card field** at payment step; "Apply" shows remaining balance after use `[I]`.
- **Buy a gift card** page `[S: 05]` `[S: 09]`: amount presets + custom, design picker, recipient,
  message, delivery date, purchaser details, pay.
- **Check balance** page `[I]` (code + captcha-less rate-limited lookup).
- **Customer account → My membership** `[I]`: benefits used/remaining, next charge, next visits,
  skip/reschedule visit, update card, pause/cancel (policy-dependent; online cancel must be as
  easy as online sign-up, §5.11), receipts.
- **Customer account → Gift cards** `[I]`: saved cards (wallet) with balances; "buy and send"
  history `[S: 05]`.

---

## 4. Workflows

Side-effect notation: **W** = record written, **E** = event emitted (catalog in §9), **N** =
notification trigger (Agent 08 owns templates), **S** = status change.

### 4.1 W-PKG-1 Create a subscription package `[S: 01]`

1. Owner opens New → Subscription Package `[S: 01]`.
2. Enters name, description `[S: 01]`, type (subscription / bundle `[I]`).
3. Chooses billing cycle and price `[S: 01]`; adds commitment options `[S: 01]`.
4. Toggles self-purchase and upsell `[S: 01]`; if both off → CRM-only `[S: 01]`.
5. Toggles automatic scheduling and picks frequency `[S: 01]`.
6. Adds included services and discounts `[S: 01]`.
7. Reviews and saves `[S: 01]`.
   - **W** `SubscriptionPackage` (status `draft` or `published`) + `PackageVersion v1` (immutable
     snapshot of pricing, entitlements, benefits, scheduling) `[I]`.
   - **E** `subscriptions.package.created`; if published also `subscriptions.package.published`.
   - Agent 01 cache invalidation for the packages page / upsell index `[I]`.

### 4.2 W-PKG-2 Edit / version / archive a package `[I]`

1. Edit any field → if the package has 0 subscriptions ever, edit v1 in place; otherwise create
   `PackageVersion v(n+1)`.
2. Owner chooses propagation: *new subscribers only* (default) or *existing at next renewal*.
3. If existing at renewal **and** price increases or benefits decrease (a "material adverse
   change"): schedule `PlannedChange` per subscription effective at the first renewal ≥ now +
   notice period (default 30 days); **N** `subscription.price_change.notice` to each subscriber.
4. Archive: package disappears from booking site and New → Subscription; existing subscriptions
   continue on their version until cancelled; **E** `subscriptions.package.archived`.
5. Delete: allowed only if never sold (else archive).

### 4.3 W-SUB-1 Assign a subscription in the CRM `[S: 01]`

1. New → Subscription `[S: 01]`.
2. Choose existing customer or **+** new `[S: 01]` → Agent 07 `customers.customer.created` if new.
3. Choose existing package or create new `[S: 01]`; choose commitment option `[I]`.
4. Choose billing start and frequency `[S: 01]`; address/asset; payment method; first visit (if
   auto-scheduled) `[I]`.
5. Review `[S: 01]` — system computes a **QuoteSnapshot** (first charge, tax, discounts, next
   charge date, visits preview) via `subscriptions.quote` `[I]`.
6. "Process the payment and activate" `[S: 01]`:
   1. **W** `Subscription` status `pending_activation`, idempotency key = wizard session id `[I]`.
   2. If first charge > 0 and billing starts today → call Agent 05 `payments.charge.create`
      (`purpose: 'subscription_initial'`, key `sub:{id}:period:1:charge`).
   3. On `payments.charge.succeeded` → **W** `SubscriptionPeriod #1` (status `paid`),
      **W** entitlement `grant` ledger rows for period 1, **S** `active` (or `trialing`),
      **E** `subscriptions.subscription.activated`, **N** `subscription.welcome` + receipt.
   4. If auto-scheduling → **E** `subscriptions.schedule.requested` → Agent 03 creates the series
      and first occurrences; each job created carries `funding.subscriptionId` and our engine
      places entitlement **reservations** (§10.4).
   5. On `payments.charge.failed` → subscription stays `pending_activation` (not `past_due`; the
      customer never had service) and the UI shows the decline; after 24 h unresolved → status
      `incomplete_expired` `[I]`.
   6. If billing starts in the future → `active` immediately with period #1 `scheduled`; first
      charge runs on start date via the billing run (§10.1). Benefits are granted only when the
      period is paid (setting `grantBenefitsBeforePayment`, default false) `[I]`.
7. Subscription detail is shown `[S: 01]`.

### 4.4 W-SUB-2 Customer self-purchase online `[S: 01]` (UI by Agent 01)

1. Customer browses packages page or sees an upsell `[S: 01]`.
2. Selects package + commitment; signs in / creates account (Agent 01/07).
3. Enters service address (must be inside an allowed service area — Agent 02 zone check) `[I]`.
4. If auto-scheduling: picks first visit slot (Agent 03 availability) and preferred window.
5. Enters card via processor-hosted fields (Agent 05) with **off-session / recurring consent**
   flag (needed for SCA regions and network MIT rules) `[I]`.
6. Accepts terms: explicit checkbox next to auto-renewal disclosure (§5.11); **W** `ConsentRecord`
   (text hash, timestamp, IP, user agent) `[I]`.
7. Submit → same as 4.3 step 6 with `channel = 'online'`. Idempotency key = checkout session id
   so a double-tap never creates two subscriptions `[I]`.
8. **N** welcome + receipt; customer account shows "My membership".

### 4.5 W-SUB-3 Upsell during booking `[S: 01]`

1. Cart has services; Agent 01 calls `subscriptions.upsellCandidates(cart, customer?)` `[I]`.
2. We return packages with `upsellDuringBooking=true` `[S: 01]` whose trigger services intersect
   the cart, ranked by customer savings on *this* booking; excluded if customer already has an
   active subscription to that package `[I]`.
3. Customer accepts → cart gains a `subscription_purchase` line; `pricing.evaluate` reprices the
   booking with membership coverage applied as if active (`assumeMembership: packageVersionId`) `[I]`.
4. On checkout: **one combined payment intent** for (first subscription charge + any non-covered
   booking amount) or two charges, per processor capability (Agent 05 decides) `[I]`. The job is
   created with `funding.subscriptionId` and consumes the period-1 entitlement.
5. Atomicity: if subscription activation fails, the booking is repriced at non-member price and
   the customer is asked to confirm before charging — never silently charge a different amount `[I]`.

### 4.6 W-SUB-4 Billing cycle renewal (system) `[I]`

1. Scheduler (every 5 min) selects subscriptions with `nextBillingAt <= now` and status in
   (`active`, `trialing` at trial end, `past_due` for retries handled by dunning).
2. For each, in its own transaction with a lease (§10.1): close current period (expire unused
   non-rollover entitlements, **E** `subscriptions.entitlement.expired`), create next period,
   apply `PlannedChange` (price change / plan change / pause start), compute amount
   (price − subscription-scoped discounts ± proration credits), tax via Agent 05.
3. If the period is covered by a prepaid commitment → no charge; period marked `prepaid`.
4. Else charge card on file off-session (Agent 05). Success → period `paid`, grant entitlements,
   **E** `subscriptions.subscription.renewed`, **N** receipt. Failure → period `unpaid`,
   **S** `past_due`, **E** `subscriptions.subscription.payment_failed`, start dunning (4.7).
5. Manual-pay subscriptions (`collection = 'invoice'`) → Agent 05/04 creates an invoice with due
   date; period `awaiting_payment`; dunning uses invoice reminders instead of card retries.

### 4.7 W-SUB-5 Dunning (failed payment recovery) `[I]`

Default schedule (owner-configurable, Settings → Payments → Subscriptions):

| Attempt | When | Action | Customer notification (Agent 08) |
|---|---|---|---|
| 0 | renewal time | charge fails | `subscription.payment_failed` (SMS+email, card-update link) |
| 1 | +1 day | retry | — (only on fail: none, avoid spam) |
| 2 | +3 days | retry | `subscription.payment_failed_reminder` |
| 3 | +5 days | retry | — |
| 4 | +7 days | final retry | `subscription.final_notice` ("membership will be {action} tomorrow") |
| End | +8 days | apply **final action**: Cancel (default) / Pause / Keep past due (manual) | `subscription.cancelled_nonpayment` or `subscription.paused` |

Rules:
- Retries respect processor decline codes: hard declines (stolen/lost card, closed account,
  "do not honor" repeated) skip remaining automatic retries and wait for a card update `[I]`.
- A card update by customer or staff triggers an **immediate** retry `[I]`.
- Retries run at the customer's local 10:00 (higher approval rates, no 3 a.m. SMS) `[I]`.
- Owner gets a daily digest of past-due subscriptions (Agent 08) `[I]`.
- **Jobs during dunning** (setting `pastDueJobPolicy`): `keep` (default for first 7 days — job
  stays booked, worker app shows "Membership past due — collect payment"), then `hold`
  (auto-scheduled jobs inside an unpaid period get status `on_hold`; slot released after 48 h
  notice) or `charge_as_regular` (job is repriced at non-member price and charged after service)
  `[I]`.
- Benefits of an unpaid period are not granted; reservations against it are `provisional` and
  convert on payment, or are released when the period is written off `[I]`.
- Recovery → **S** `active`, **E** `subscriptions.subscription.recovered`; the billing anchor does
  **not** move (period dates unchanged; customer pays the missed period) `[I]`.

### 4.8 W-SUB-6 Pause / resume `[I]`

1. Staff or customer (if `customerCanPause`) chooses pause start (now or next renewal — default
   next renewal so the paid period is honoured) and resume date (max `maxPauseDays`, default 90).
2. **W** `PlannedChange{type:'pause'}` → at effective time **S** `paused`; no billing; no new
   entitlements; auto-scheduled jobs inside the pause window are **cancelled** via Agent 03
   (`scheduling.occurrence.cancel` with reason `subscription_paused`); **N** to customer and
   assigned workers (Agent 08 handles job-cancel notices).
3. Commitment: pause extends `commitmentEndsAt` by the paused duration `[I]`.
4. Resume (scheduled or manual): new billing anchor = resume date (default) or keep old anchor
   with a prorated first charge (setting) `[I]`; charge; **S** `active`; **E**
   `subscriptions.subscription.resumed`; request scheduling to regenerate occurrences.

### 4.9 W-SUB-7 Skip a visit `[I]`

Customer/staff skips one auto-scheduled occurrence → Agent 03 cancels that occurrence → our
reservation is released → the entitlement stays available in the period (customer may book
another time) and expires at period end per rollover. Optional setting `skipCreditsNextPeriod`
(off). Skipping does not change billing (a subscription is a billing agreement, §1.1).

### 4.10 W-SUB-8 Change package (upgrade / downgrade) `[I]`

1. Staff (or customer if allowed) selects new package/version and timing:
   **Immediately** (default for upgrades) or **At next renewal** (default for downgrades).
2. Immediate: compute proration (§5.4) → charge (or credit) now; close current period early
   (consumed entitlements stay consumed); open a new period on the new version starting now with
   the anchor reset to now (default) or kept (setting); grant new entitlements minus what was
   already consumed of identical items this period (§5.4.3).
3. Reservations on future jobs are re-evaluated against the new version; jobs whose coverage
   changes are repriced and the customer is notified only if their price changes `[I]`.
4. Scheduling frequency differs → request Agent 03 to update the series from the next
   unlocked occurrence.
5. During a commitment, a downgrade is blocked unless `allowDowngradeDuringCommitment` `[I]`.
6. **E** `subscriptions.subscription.plan_changed`.

### 4.11 W-SUB-9 Cancel `[I]`

| Mode | Billing | Benefits | Auto-scheduled jobs | Default for |
|---|---|---|---|---|
| At period end | No further charges | Usable until `currentPeriodEnd` | Jobs dated ≤ period end stay (covered); later jobs cancelled at cancellation time with notice | Customer self-cancel, staff default |
| Immediately, no refund | Stops | End now; unused expire | All future jobs cancelled or converted (see below) | Non-payment final action |
| Immediately, prorated refund | Refund unused time minus value of benefits consumed this period (never negative) via Agent 05 | End now | As above | Staff discretion |
| At commitment end | Charges continue until term end | Continue | Continue until term end | Commitment early-cancel "not allowed" |

Future auto-scheduled jobs on cancel — setting `onCancelFutureJobs` `[I]`:
`cancel` (default) / `convert_to_paid` (keep the jobs, reprice at non-member price, notify the
customer with an opt-out link) / `ask` (staff chooses per cancellation). Converting to paid is
effectively turning the subscription into a normal **recurring job** (§1.1) — handy for
"I still want weekly service, just not the membership".

Steps: **W** cancellation record (reason code + free text — reuse Agent 01's cancellation
feedback pattern `[S: 05]`), **S** `pending_cancel` or `cancelled`, **E**
`subscriptions.subscription.cancel_scheduled` / `…cancelled`, **N** confirmation; optional
"win-back" offer before confirming (owner can configure a retention discount, e.g. 1 month 50%
off, or pause instead) `[I]`.

### 4.12 W-SUB-10 Commitment end & renewal `[S: 01]` `[I]`

At `commitmentEndsAt` per commitment option's "after term ends" setting:
- **Renew same term**: create next commitment; if prepay, charge the term total; **N**
  `subscription.renewal_notice` sent `renewalNoticeDays` before (default 15; some state laws
  require 15–45 days for terms ≥ 12 months — §5.11).
- **Roll to month-to-month**: price becomes package list price per cycle (no commitment
  discount); **N** notice beforehand.
- **End**: **S** `expired`; future jobs per `onCancelFutureJobs`.
**E** `subscriptions.commitment.ended`, `subscriptions.commitment.renewed`.

### 4.13 W-SUB-11 Prepaid visit bundle purchase & redemption `[I]`

1. Customer buys bundle online or staff sells it (New → Subscription with a bundle package).
2. Single charge; **W** `Subscription{kind:'bundle', status:'active'}` with one period spanning
   `validity` (e.g. 12 months) and `grant` rows for N visit credits.
3. Each booking of a covered service reserves a credit; completion consumes; cancellation releases.
4. Expiry: unused credits expire at end of validity **unless** jurisdiction treats the bundle as
   a stored-value/gift instrument (open question §13); default validity setting "no expiry"
   for US-safe behaviour `[I]`.
5. When credits reach 0 → **S** `depleted`; **N** "Buy another pack?" with one-tap repurchase.

### 4.14 W-DSC-1 Create a discount `[S: 09]`

1. Discounts → New. Fill fields (§3.7). Test against sample cart.
2. Save → **W** `Discount` + `DiscountVersion`. If `startsAt` in future → status `scheduled`.
3. **E** `discounts.discount.created`. If "show banner" → Agent 01 shows banner while active.
4. Scheduler flips `scheduled → active` at `startsAt` and `active → expired` at `endsAt`
   (evaluation also checks dates directly, so scheduler lag never grants an expired discount).

### 4.15 W-DSC-2 Apply a discount during booking `[S: 16]` `[S: 19]`

1. Code entered (customer) or picked/created (staff) `[S: 16]`.
2. `pricing.evaluate` runs eligibility (§10.5); ineligible → specific reason ("This code is
   valid for bookings on Mon–Thu", "Already used", "Minimum $80").
3. On booking confirm: **W** `DiscountRedemption{status:'reserved'}` with atomic usage counter
   increment (§10.6). **E** `discounts.redemption.reserved`.
4. On payment captured / job completed (whichever finalizes revenue in Agent 05's model):
   status `committed`. **E** `discounts.redemption.committed`.
5. On job cancelled before payment: `released` (counter decremented) unless the discount has
   `consumeOnCancel=true` `[I]`.
6. Price snapshot stored on the job's price lines (Agent 04/05) — later edits to the discount
   never change an existing job.

### 4.16 W-DSC-3 Recurring discount on a recurring job `[S: 14]`

1. Staff creates recurring job (Agent 03) and sets recurring discount: fixed $ or % "off of each
   job" `[S: 14]`.
2. Agent 03 stores it on the series; each generated occurrence's pricing call includes
   `modifiers: [{kind:'recurring', seriesId, value}]`.
3. Online, if a service has "recurring booking options" with suggested frequencies and discounts
   `[S: 06]` (Agent 02 stores per-frequency discount), the customer's chosen frequency yields
   the same `recurring` modifier.
4. Edits "committed across all future bookings" `[S: 14]` → Agent 03 updates future occurrences;
   we reprice only occurrences not yet locked (locked = in progress, completed, or paid).

### 4.17 W-GC-1 Issue a gift card manually `[S: 09]`

1. Gift cards → Issue. Fill dialog (§3.8).
2. If Paid → Agent 05 records a **gift card sale** (not service revenue; no sales tax in most US
   jurisdictions `[I]`) and returns `paymentId`.
3. **W** `GiftCard{status:'active'|'pending_delivery'}`, ledger `issue +amount`. Code generated
   (§10.8); only hash + last4 stored after delivery render `[I]`.
4. Delivery: now/scheduled/print → **N** `giftcard.delivered` to recipient (designed email + SMS
   link to a "view card" page); purchaser gets a receipt + "delivered" confirmation.
5. **E** `giftcards.card.issued`.

### 4.18 W-GC-2 Buy & send a gift card online `[S: 05]` `[S: 09]`

1. Customer opens "Gift cards" on booking site (Agent 01) `[S: 05]`.
2. Chooses amount, design, recipient, message, delivery date.
3. Pays (Agent 05; card; gift cards cannot be bought with gift cards `[I]`).
4. As 4.17 steps 3–5 with `source='online'`. Fraud controls: velocity limits per card/IP/email,
   max $ per order, optional manual review above threshold (gift cards are a top card-testing /
   money-laundering vector) `[I]`.

### 4.19 W-GC-3 Redeem a gift card (partial) `[S: 16]` `[I]`

1. At checkout / POS / CRM Options `[S: 16]`, enter or pick card.
2. `giftcards.authorize(code, amount)` → returns usable amount = min(balance, amountDue).
3. Payment orchestration (Agent 05) creates a **hold** ledger entry (`hold −x`), then on capture
   converts to `redeem −x`; on failure/cancel → `release +x`. Holds auto-expire after 7 days.
4. Remaining due charged to other tender. **E** `giftcards.card.redeemed`.
5. Balance after → shown on receipt; if 0 → status `redeemed` (still viewable).

### 4.20 W-GC-4 Refunds involving gift cards `[I]`

- **Refund of a job paid partly with a gift card:** default rule *refund to original tenders,
  gift card last-in-first-out reversed proportionally*: e.g. job $120 paid $50 GC + $70 card;
  full refund → `refund_credit +$50` to the gift card and $70 to card; partial refund $30 →
  allocated proportionally ($12.50 GC / $17.50 card) with largest-remainder rounding; owner can
  override per refund ("refund all to card", "all to gift card"). Card portion executed by Agent 05.
- **Refund of a gift card purchase:** only allowed if the gift card is unused (balance ==
  original, no holds); voids the card (`void −balance`) then refunds payment. Partially used →
  refund of remaining balance only by owner override (cash-out), never automatic.
- **Cash-out laws:** some states require cash redemption of small remaining balances (e.g.
  California < $10) `[I]` → setting `cashOutThresholdCents` per business locale with a
  "Cash out balance" action that records `cash_out −x` and a manual payout entry.

### 4.21 W-GC-5 Lost card / reissue `[I]`

Staff verifies identity → "Reissue" → new code; ledger `transfer_out` on old (status `replaced`)
and `transfer_in` on new, atomic. Old code then rejects with "This card was replaced".

---

## 5. Business rules & math

### 5.1 Pricing pipeline — order of operations `[I]`

Coordinated with Agent 02 (base prices) and Agent 05 (tax, tips, tender). This module owns
stages 3–6 and stage 9. All values integer cents.

```
1. Base lines        (Agent 02)  service/add-on prices, variant/size pricing, quantity
2. Line adjustments  (Agent 02)  manual price override by staff (if permitted)
3. Membership coverage (06)      included-service entitlements → line set to covered price (often 0)
4. Member discount     (06)      package % / $ off on remaining eligible lines
5. Recurring discount  (06)      series discount (video 14) — SKIPPED on subscription-funded jobs
6. Promo / manual discounts (06) codes, automatic promos, staff manual discount; stacking rules
7. Fees              (02/05)     travel fee (zone) [S: 11], surcharges — discountable only if scoped
8. Tax               (05)        on post-discount taxable lines, per zone tax rate
9. Gift card tender  (06 + 05)   reduces amount due, not price or tax
10. Deposit / tip / remaining tender (05)
```

Rules:
- A line's price never goes below 0; an order-level discount never exceeds the sum of eligible
  lines `[I]`.
- Discounts apply to **pre-tax** amounts (merchant-funded discounts reduce the taxable base in
  US jurisdictions generally `[I]`); gift cards do not reduce the tax base `[I]`.
- Tips are computed by Agent 05 on either pre-discount or post-discount subtotal (Agent 05
  setting); our output provides both `[I]`.
- Each modifier records `{source, sourceId, versionId, amountCents, lineAllocations[]}` so
  invoices can show and reports can attribute discount cost `[I]`.

### 5.2 Money rounding `[I]`

- Percent on a line: `round_half_even(lineCents × bp / 10000)` where `bp` = basis points
  (20% = 2000). Store percentages as basis points (integers) — no floats anywhere.
- Order-level amount spread over lines: **largest-remainder allocation** proportional to each
  eligible line's current amount so `Σ allocations == discount` exactly (needed for per-line tax
  and per-line refunds).
- Caps (`maxDiscountCents`) apply after computing, before allocation.
- Currency with 0 decimals (JPY) / 3 decimals (KWD): the minor-unit exponent comes from the
  business currency; all formulas operate on minor units (37 countries `[S: llms]`).

### 5.3 Billing period math `[I]`

- **Anchor**: `billingAnchor = {dayOfMonth|dayOfWeek|monthDay, timeOfDay, tz}` set at first
  billing date in the **business** timezone (customers of one business share the business
  calendar; service happens at the business's location region).
- Monthly next date: `addMonths(anchorDate, n)` with day clamped to month length: anchor 31 →
  Feb 28/29, Apr 30, then **back to 31** in months that have it (compute from anchor, never
  from the previous clamped date).
- Yearly anchor Feb 29 → Feb 28 in non-leap years.
- Weekly: anchor + 7n days in local calendar (DST-safe: add calendar days, then resolve local
  time → UTC; if 10:00 falls in a DST gap, shift forward to first valid instant).
- Billing runs at local `timeOfDay` (default 06:00) so "charged on the 1st" is the 1st locally.
- Period: `[periodStart, periodEnd)` half-open; `periodEnd` = next anchor.
- **Benefit periods** are independent from **billing charges**: a 6-month prepaid commitment
  creates 6 monthly benefit periods but 1 charge. A yearly plan with "1 free add-on per month"
  creates 1 billing period and 12 monthly **entitlement windows** for that entitlement.

### 5.4 Proration `[I]`

Time-based, using whole **seconds** of the period for fairness across DST (or days — setting;
default days in business TZ for readability on receipts).

5.4.1 Unused credit of current period:
```
unusedFraction = (periodEnd − changeAt) / (periodEnd − periodStart)
creditCents    = floor(pricePaidCents × unusedFraction) − consumedValueAdjustment
```
`consumedValueAdjustment` (fair-use rule): if included entitlements were consumed this period,
credit is reduced by `max(0, consumedListValue − usedTimeValue)` where `usedTimeValue =
pricePaid × (1 − unusedFraction)`. Prevents "join, get $250 detail free, downgrade, get credit".
Credit is floored at 0.

5.4.2 Upgrade charge now: `newPrice × unusedFraction − creditCents`, rounded half-even; if
negative → stored as **account credit** applied to next renewal (not refunded) unless staff
chooses refund.

5.4.3 Entitlements on immediate change: for each entitlement in the new version:
`grantNow = max(0, newQtyPerWindow − consumedThisWindowOfSameItem)`; non-matching old
entitlements expire immediately (reservations on future jobs re-evaluated).

5.4.4 Commitment prepay plan changes: treat remaining prepaid value =
`termPaid × remainingCycles / termCycles`; upgrade charges difference to the new plan's
equivalent; downgrade during prepaid term → blocked by default.

### 5.5 Benefit consumption rules `[I]`

- **Window selection:** a job consumes from the entitlement window that contains the job's
  **scheduled service date** (business TZ), not the booking date. Rationale: a customer booking
  next month's wash this month should not burn this month's credit.
- **States:** `grant(+q)` → `reserve(−q)` at booking → `consume` at job completion (reservation
  converted) → or `release(+q)` on cancel/reschedule-out. Available = grants − reservations −
  consumptions − expirations.
- **Future windows:** reservation against a window whose period is not yet paid is
  `provisional`; becomes firm when the period is paid; if the subscription ends before that
  window, the job is repriced at non-member price and the customer notified (Agent 08).
- **Allocation when multiple entitlements could cover the same line:** choose the one that
  expires soonest, then the most specific (service-specific before category before "any add-on"),
  then oldest grant (FIFO).
- **Allocation when one entitlement could cover multiple lines:** cover the **most expensive
  eligible line** first (maximizes customer value; predictable).
- **Unlimited entitlements:** consumption is recorded for reporting; fair-use cap (e.g. max 1
  per day / 5 per window) enforced at reservation.
- **Rollover:** at window close, `rollover = min(available, rolloverMax)`; written as an
  `expire(−available)` + `grant(+rollover, rolledFrom=windowId, expiresAt=…)`.
- **Reschedule across windows:** release in old window, reserve in new window atomically; if new
  window has no availability → job repriced; staff sees warning before confirming.
- **No-show / late cancel:** if the business's cancellation policy (Agent 01/04) charges a fee,
  the credit is **consumed** (setting `noShowConsumesCredit`, default true).
- **Job edited after completion** (e.g. add-on removed): consumption reversed with `release`
  dated in the original window even if closed (window-closed releases become `expire` at once
  unless staff chooses "credit to current window").

### 5.6 Stacking matrix (defaults; owner-configurable) `[I]`

| Combination | Default | Rationale |
|---|---|---|
| Membership coverage + anything on the **same line** | Coverage wins; no further discount on a $0 line | Nothing to discount |
| Member % discount + recurring discount (video 14) | **Not stacked**; apply the larger per line | Prevents 20% + 15% = 35% surprise |
| Member % discount + promo code | Not stacked unless discount `combinesWith.member=true` | Owner opt-in |
| Recurring discount + promo code | Not stacked unless `combinesWith.recurring=true`; promo on series applies per its "recurring behaviour" (first occurrence by default) | |
| Promo + promo | Max **1 code** per booking + any number of *automatic* discounts marked combinable | Simple mental model |
| Automatic vs code, both exclusive | Pick the one giving the customer the larger saving; tie → lower priority number | "Best price" guarantee |
| Manual staff discount | Treated as a promo with `combinesWith` = all (staff intent), capped by permission caps | |
| Any discount on gift card purchases / subscription first charge | Gift cards: **never** discountable (avoid arbitrage/law issues). Subscription charge: only discounts whose scope includes packages | |
| Gift card + anything | Always allowed (it's tender) | |

When stacked, discounts apply **sequentially** (each on the amount remaining after the
previous), ordered by stage then priority — multiplicative, never additive, so 100% caps are
natural.

### 5.7 Discount eligibility rules `[S: 09]` `[I]`

- Time window evaluated against `validityBasis` timestamp(s) in the business TZ; `endsAt`
  inclusive to the minute shown in the UI (store as exclusive instant = shown minute + 60 s).
- "New customer" = no **completed** job and no active subscription; evaluated by customer id
  **and** by normalized phone/email/address match (anti-abuse via Agent 07 dedupe) `[I]`.
- Usage per customer counts `reserved + committed` redemptions.
- Codes are case-insensitive, trimmed, Unicode-normalized (NFKC), `O/0` and `I/1` not
  interchangeable (we don't guess) — but the generator avoids ambiguous chars.
- Recurring series: eligibility evaluated once at series creation for "first occurrence only";
  for "every occurrence" each occurrence is evaluated at generation but date window checked on
  **series booking date** (so a promo booked in-window keeps applying) — setting.
- A discount edited after redemption does not change past redemptions (versioned).

### 5.8 Gift card rules `[S: 09]` `[S: 05]` `[S: 16]` `[I]`

- Value: `min 1.00`, `max` owner-set (default 2,000.00 — larger amounts raise AML/KYC and
  prepaid-access regulatory questions) `[I]`.
- Balance = Σ ledger amounts; never negative (enforced in the same transaction as the insert).
- Gift cards may pay for: services, add-ons, products, tips (setting, default yes), subscription
  charges (setting, default **no** for automatic renewals — a member's card on file is primary;
  a customer can explicitly choose "use gift card balance for renewals") `[I]`.
- Gift cards cannot buy gift cards `[I]`.
- Multiple gift cards per payment allowed (up to 5) `[I]`.
- Gift card currency = business currency; multi-location businesses: gift card valid at all
  locations of the same business account (setting for multi-business hub `[S: llms]` Pro tier) `[I]`.
- Revenue accounting: sale → liability (deferred revenue); redemption → revenue recognized
  against the job; breakage recognized only per owner's accountant (we report, not book) `[I]`.

### 5.9 Gift card codes `[I]`

- 16 characters from Crockford Base32 alphabet (no I, L, O, U), displayed `XXXX-XXXX-XXXX-XXXX`;
  ~80 bits entropy minus a 5-bit check character → brute-force infeasible.
- Stored: `codeHash = HMAC-SHA256(pepper, normalizedCode)`, `last4`. Full code visible only at
  issuance / to permitted staff via re-derivation? — not possible with a hash, so we store an
  **encrypted** copy (envelope encryption) for staff "reveal" with audit log.
- Lookup endpoints rate-limited (per IP 10/min, per account 30/hour, exponential backoff),
  uniform error "Code not found or not active" (no oracle on status).

### 5.10 Gift card expiry & fees — legal notes `[I]` (not legal advice; owner/counsel to confirm)

- **US federal — Credit CARD Act of 2009 / Regulation E (12 CFR 1005.20):** a store gift card
  may not expire earlier than **5 years** after issuance (or last funds load); dormancy/inactivity
  fees only after **12 months** of inactivity, max one per month, with clear disclosure. Funds
  must remain available if the physical card expires (replacement). Promotional cards issued
  for free (loyalty, award, promotional) are largely exempt **if** clearly labeled with the
  expiry `[I]`.
- **US states** are often stricter: e.g. California generally prohibits expiry dates and
  service fees on purchased gift cards and requires cash redemption for balances under $10;
  several other states (e.g. WA, ME, RI, CT, …) restrict expiry/fees or set cash-out thresholds;
  unclaimed-property (escheat) laws in some states may require remitting unredeemed balances `[I]`.
- **Canada** provinces (e.g. Ontario, Quebec, BC) generally prohibit expiry on purchased cards
  `[I]`; **EU/UK/AU**: AU requires ≥ 3-year expiry since 2019 `[I]`.
- **Our default:** purchased gift cards **never expire** and have **no fees** (legal
  everywhere, simplest); promotional cards may carry an expiry with the date printed on the card
  and in the email; a per-locale "cash-out threshold" setting; escheat reporting export.
  Changing defaults requires owner acknowledgement of a compliance warning.

### 5.11 Auto-renewal & cancellation compliance `[I]` (not legal advice)

- Many US states have Automatic Renewal Laws (e.g. California ARL, as amended 2024–2025;
  NY, IL, VA, CO, etc.): clear and conspicuous disclosure of renewal terms **before**
  purchase, affirmative consent, acknowledgment email with cancellation instructions, **online
  cancellation if sign-up was online**, and advance notice of price changes and (for longer
  terms) of renewals. The federal FTC "click-to-cancel" negative option rule (2024) was vacated
  by a federal court in 2025 `[I]`; the state laws still apply.
- Platform behaviour by default (satisfies the strictest common denominator): consent record,
  acknowledgment email with a one-click cancel link, self-serve cancel always available online
  for subscriptions bought online (even if `customerCanCancel=false` for CRM-sold ones — the
  setting is forced on for online purchases), price-increase notice ≥ 30 days, renewal notice for
  terms ≥ 12 months 15–45 days before (default 30), annual reminder for yearly plans.
- Cancellation flow may show **one** retention offer, then must allow cancel.

### 5.12 Subscription tax `[I]`

The subscription charge is taxed per the service address's zone tax (Agent 02 zone tax `[S: 11]` "assign a custom tax rate"
per service area) and per taxability of the
included services (Agent 05 tax engine). Covered jobs have `$0` taxable value (tax collected on
the subscription charge instead). Member-discounted jobs are taxed on the discounted amount.
Mixed packages (taxable + non-taxable services) → tax engine allocates package price across
included items by list-price ratio (setting; open question §13).

### 5.13 Metrics formulas (reported by Agent 08) `[I]`

```
normalizedMonthlyCents(sub) =
    weekly:     price × 52 / 12
    biweekly:   price × 26 / 12
    monthly:    price
    quarterly:  price / 3
    yearly:     price / 12
    prepaid commitment: termTotal / termMonths   (not the list price)
    bundle:     excluded from MRR (reported as "bundle sales")
MRR            = Σ normalizedMonthly over subs in (active, past_due ≤ 30d, trialing? no)
Active subs    = count(status in active, past_due)
Gross churn %  = cancelled+expired in month / active at month start
Net MRR churn  = (churned MRR + contraction − expansion) / MRR at month start
Dunning recovery % = recovered / entered past_due (cohort, 30d)
Member utilization = consumed entitlements / granted entitlements (per package)
Member value ratio = list value of covered items / subscription revenue (target < 1 for profitability)
Discount cost  = Σ committed redemption amounts (by discount, by month)
Gift card liability = Σ balances (active cards)
Gift card breakage estimate = liability on cards with no activity > 24 months (report only)
```

---

## 6. State machines

All transitions are `[I]` (Fieldd tutorials only show "activate" `[S: 01]`). Every transition
writes a `SubscriptionEvent` audit row with actor and reason, and is guarded by optimistic
concurrency (`version` column) so two staff clicks or a staff click + scheduler can't both win.

### 6.1 Subscription

States: `draft` · `pending_activation` · `trialing` · `active` · `past_due` · `paused` ·
`pending_cancel` · `cancelled` · `expired` · `incomplete_expired` · `depleted` (bundles only).

| # | From | Event | Guard | To | Side effects |
|---|---|---|---|---|---|
| 1 | — | `create` (CRM wizard / checkout) | customer, package version published or CRM-only, address in allowed zone | `pending_activation` | W Subscription; quote snapshot stored |
| 2 | `pending_activation` | `initial_payment_succeeded` | trial = 0 | `active` | W period #1 paid, grants; E activated; N welcome; schedule request |
| 3 | `pending_activation` | `activate_with_trial` | trial > 0 and payment method verified (setup intent / $0 auth) | `trialing` | grants for trial window if `trialIncludesBenefits`; E trial_started |
| 4 | `pending_activation` | `activate_deferred_start` | billing start date > today | `active` | period #1 `scheduled`; benefits on payment |
| 5 | `pending_activation` | `initial_payment_failed` | — | `pending_activation` | show decline; N payment link if sent-to-customer |
| 6 | `pending_activation` | `timeout` (24 h default) | not paid | `incomplete_expired` | release any reservation; cancel provisional jobs |
| 7 | `trialing` | `trial_end_charge_succeeded` | — | `active` | period paid, grants |
| 8 | `trialing` | `trial_end_charge_failed` | — | `past_due` | dunning starts |
| 9 | `trialing` | `cancel` | — | `cancelled` | no charge; jobs per policy |
| 10 | `active` | `renewal_charge_succeeded` / `prepaid_period_started` | — | `active` | close period, open next, grants; E renewed |
| 11 | `active` | `renewal_charge_failed` | — | `past_due` | period unpaid; dunning attempt 0; N payment_failed |
| 12 | `past_due` | `retry_succeeded` / `manual_payment_recorded` | — | `active` | grants for period; provisional reservations firm; E recovered |
| 13 | `past_due` | `retry_failed` | attempts < max | `past_due` | schedule next attempt; maybe N reminder; apply `pastDueJobPolicy` at day threshold |
| 14 | `past_due` | `dunning_exhausted` | final action = cancel | `cancelled` | E cancelled(reason=nonpayment); jobs per policy |
| 15 | `past_due` | `dunning_exhausted` | final action = pause | `paused` | unpaid period written off or kept as receivable (setting) |
| 16 | `past_due` | `dunning_exhausted` | final action = keep | `past_due` | flagged for manual follow-up; no more auto retries |
| 17 | `active` | `pause_effective` | pause allowed; not in prepaid term unless allowed | `paused` | cancel jobs in window; extend commitment end |
| 18 | `paused` | `resume` (manual / scheduled) | payment method valid | `active` | charge resume period; regenerate schedule; E resumed |
| 19 | `paused` | `resume_charge_failed` | — | `past_due` | dunning |
| 20 | `active`/`trialing`/`past_due` | `cancel_at_period_end` | commitment allows | `pending_cancel` | future jobs beyond period end cancelled; E cancel_scheduled |
| 21 | `pending_cancel` | `undo_cancel` | before period end | `active` | regenerate schedule; E cancel_reverted |
| 22 | `pending_cancel` | `period_end` | — | `cancelled` | close period; E cancelled |
| 23 | any non-terminal | `cancel_immediately` | permission `subs.subscription.cancel`; commitment allows or override | `cancelled` | optional prorated refund (Agent 05); jobs per policy |
| 24 | `active` | `commitment_end` | after-term = end | `expired` | E commitment.ended; jobs per policy |
| 25 | `active` | `commitment_end` | after-term = renew / month-to-month | `active` | new commitment or list price; E commitment.renewed |
| 26 | `active` (bundle) | `last_credit_consumed` | kind = bundle | `depleted` | N repurchase offer |
| 27 | `active` (bundle) | `validity_end` | kind = bundle | `expired` | expire credits (if lawful) |
| 28 | any | `customer_deleted` (GDPR erase, Agent 07) | — | `cancelled` | cancel immediately, anonymize; keep financial records |
| 29 | any | `dispute_opened` on a subscription charge (Agent 05) | — | unchanged + flag `disputed` | setting: auto-pause on dispute (default on) |
| 30 | `cancelled`/`expired` | `reactivate` | within 90 days | `pending_activation` | new subscription id? → **No**: new Subscription row linked via `previousSubscriptionId` (clean ledger) |

Terminal: `cancelled`, `expired`, `incomplete_expired`.

### 6.2 SubscriptionPeriod (billing period)

| From | Event | To | Side effects |
|---|---|---|---|
| — | billing run creates | `scheduled` (future) / `due` | — |
| `due` | covered by prepaid term | `prepaid` | grants |
| `due` | charge requested | `charging` | lease held, idempotency key |
| `charging` | success | `paid` | grants; receipt |
| `charging` | failure | `unpaid` | dunning |
| `charging` | processor timeout / unknown | `charging` (pending) | reconcile via Agent 05 webhook or status query; never double-charge |
| `unpaid` | retry success / manual payment | `paid` | grants |
| `unpaid` | write-off (cancel) | `void` | release provisional reservations |
| `paid` | refund full | `refunded` | optional: expire unconsumed grants |
| `paid` | refund partial | `partially_refunded` | — |
| `due` | comp (staff "comp next period") | `comped` | grants; $0 |
| `scheduled` | invoice collection mode | `awaiting_payment` | invoice sent (Agent 05/04) |

### 6.3 Entitlement ledger entry types (not a state machine — an append-only log)

`grant(+q)`, `reserve(−q, jobId)`, `confirm_reservation(jobId)` (provisional → firm, 0 qty),
`consume(0, jobId)` (marks reserve as consumed), `release(+q, jobId)`, `expire(−q)`,
`rollover_grant(+q)`, `adjust(±q, reason, actor)`.
Reservation lifecycle: `provisional` → `firm` → `consumed` | `released`.

### 6.4 Discount

| From | Event | Guard | To |
|---|---|---|---|
| — | save with startsAt ≤ now | — | `active` |
| — | save with startsAt > now | — | `scheduled` |
| `scheduled` | startsAt reached | — | `active` |
| `active` | endsAt reached | — | `expired` |
| `active` | usage/budget limit reached | — | `exhausted` (auto-revives to `active` if reservations released below limit) |
| `active`/`scheduled` | pause | permission | `paused` |
| `paused` | resume | — | `active`/`scheduled`/`expired` by date |
| any | archive | — | `archived` (hidden; redemptions kept) |
| `expired` | extend endsAt | — | `active` |

### 6.5 DiscountRedemption

`reserved` → (`committed` | `released`); `committed` → `reversed` (refund of the job: setting
`refundRestoresUse`, default true for per-customer limit, false for global counts).

### 6.6 Gift card

| From | Event | Guard | To | Side effects |
|---|---|---|---|---|
| — | issue (paid/manual) with delivery now | payment ok (if paid) | `active` | ledger issue; N delivered |
| — | issue with scheduled delivery | — | `pending_delivery` | balance usable? **No** until delivered (prevents purchaser misuse) |
| `pending_delivery` | delivery time | — | `active` | N delivered |
| `active` | redeem to 0 | — | `redeemed` | — |
| `redeemed` | refund_credit / adjust + | — | `active` | — |
| `active` | void | permission; no open holds | `voided` | ledger void(−balance) |
| `active` | reissue | — | `replaced` | balance transferred |
| `active` | expire | promotional only, or lawful locale | `expired` | ledger expire(−balance) |
| `active` | fraud flag (chargeback on purchase) | — | `frozen` | holds rejected; owner review |
| `frozen` | unfreeze / void | — | `active` / `voided` | — |

---

## 7. Data model

TypeScript-style, stack-agnostic `[I]`. Conventions: `id` = opaque string (ULID recommended for
sortable keys); every entity has `businessId` (tenant key; all queries scoped), `createdAt`,
`updatedAt`, `version` (optimistic concurrency); timestamps are UTC instants; `LocalDate` =
calendar date in business TZ; money `Cents = integer`; percentages `BasisPoints = integer`
(10000 = 100%).

### 7.1 Packages

```ts
type BillingInterval = 'week' | 'month' | 'year';
type PackageKind = 'subscription' | 'bundle';           // bundle = prepaid visit pack [I]

interface SubscriptionPackage {
  id: ID; businessId: ID;
  kind: PackageKind;
  status: 'draft' | 'published' | 'archived';
  currentVersionId: ID;                                  // FK PackageVersion
  name: string;                                          // [S: 01]
  slug: string;                                          // for booking short link (Agent 01)
  sortOrder: number;
  activeSubscriberCount: number;                         // projection
  createdBy: ID; createdAt; updatedAt; version: number;
}

interface PackageVersion {                               // immutable once any subscription references it
  id: ID; packageId: ID; versionNo: number;
  name: string; description: RichText; imageAssetId?: ID; // [S: 01]
  billing: {
    kind: 'recurring' | 'one_time';
    interval?: BillingInterval; intervalCount?: number;  // weekly/monthly/yearly [S: 01]; biweekly = week×2 [I]
    priceCents: Cents;                                   // [S: 01]
    setupFeeCents: Cents;
    taxBehavior: 'exclusive' | 'inclusive' | 'exempt';
    trialDays: number;
    allowMonthToMonth: boolean;
    validityDays?: number;                               // bundles
  };
  commitmentOptions: CommitmentOption[];                 // [S: 01]
  sales: {
    selfPurchase: boolean;                               // [S: 01]
    upsellDuringBooking: boolean;                        // [S: 01]
    upsellTriggerServiceIds: ID[];
    upsellHeadline?: string;
    showOnPackagesPage: boolean;
    termsText: RichText;
    maxActiveSubscribers?: number;
    zoneIds: ID[];                                       // empty = all
    customerType: 'residential' | 'business' | 'both';
  };
  scheduling: {
    autoSchedule: boolean;                               // [S: 01]
    frequency?: RecurrenceRule;                          // [S: 01] booking frequency; shape owned by Agent 03
    visitItems: { catalogItemId: ID; quantity: number }[];
    workerMode: 'auto' | 'keep_same';
    overageMode: 'member_price' | 'not_allowed';
  };
  entitlements: EntitlementDef[];                        // included services [S: 01]
  memberDiscounts: MemberDiscountDef[];                  // included price reductions [S: 01]
  benefitResetBasis: 'billing_cycle' | 'calendar_month';
  policies: {
    customerCanPause: boolean; maxPauseDays: number;
    customerCanCancel: boolean;                          // forced true for online purchases (§5.11)
    customerCanSkip: boolean;
    onCancelFutureJobs: 'cancel' | 'convert_to_paid' | 'ask';
    noShowConsumesCredit: boolean;
  };
  createdAt; createdBy: ID;
}

interface CommitmentOption {
  id: ID;
  termCycles: number;                                    // e.g. 6
  paymentMode: 'prepay' | 'per_cycle';                   // prepay is cited behaviour [S: 01]
  discount: { type: 'percent'; bp: BasisPoints } | { type: 'amount'; cents: Cents };
  afterTerm: 'renew_same' | 'month_to_month' | 'end';
  earlyCancel: 'not_allowed' | 'no_refund' | 'prorated_refund_minus_fee' | 'pay_remaining';
  earlyCancelFeeCents?: Cents;
  label?: string;
}

interface EntitlementDef {
  id: ID;                                                // stable across versions when "same" benefit
  target: { kind: 'service' | 'addon' | 'category' | 'any_addon' | 'any_service'; ids: ID[] };
  variantScope?: ID[];                                   // only these size/variant tiers (Agent 02)
  quantity: number | 'unlimited';
  fairUse?: { maxPerDay?: number; maxPerWindow?: number };
  window: 'billing_cycle' | 'week' | 'month' | 'year' | 'subscription';
  coverage: { type: 'free' } | { type: 'percent'; bp: BasisPoints } | { type: 'amount'; cents: Cents } | { type: 'fixed_price'; cents: Cents };
  rollover: { mode: 'none' } | { mode: 'capped'; max: number; expiresAfterWindows: number } | { mode: 'unlimited'; expiresAfterWindows: number };
  coversTimeAddons: boolean;
}

interface MemberDiscountDef {
  id: ID;
  value: { type: 'percent'; bp: BasisPoints } | { type: 'amount'; cents: Cents };  // e.g. 20% [S: 01]
  appliesTo: TargetScope;                                // all services / categories / items / add-ons
  excludes: ID[];
  frequency: { mode: 'every_booking' } | { mode: 'first_n_per_window'; n: number };
  combinesWithPromos: boolean;
}
```

### 7.2 Customer subscriptions

```ts
type SubscriptionStatus = 'draft' | 'pending_activation' | 'trialing' | 'active' | 'past_due'
  | 'paused' | 'pending_cancel' | 'cancelled' | 'expired' | 'incomplete_expired' | 'depleted';

interface Subscription {
  id: ID; businessId: ID;
  customerId: ID;                                        // Agent 07
  serviceAddressId?: ID; assetIds: ID[];                 // Agent 07 (vehicle/home)
  packageId: ID; packageVersionId: ID;
  kind: PackageKind;
  status: SubscriptionStatus;
  channel: 'crm' | 'online' | 'upsell' | 'customer_app' | 'worker_app' | 'mcp' | 'import';
  collection: 'card_auto' | 'invoice';
  paymentMethodId?: ID;                                  // Agent 05 vault reference (never PAN)
  priceCents: Cents;                                     // effective price/cycle (snapshot; may differ from version after grandfathering)
  interval: BillingInterval; intervalCount: number;
  billingAnchor: { localDate: LocalDate; localTime: string; tz: string };
  startedAt?: Instant; trialEndsAt?: Instant;
  currentPeriodId?: ID; currentPeriodStart?: Instant; currentPeriodEnd?: Instant;
  nextBillingAt?: Instant;
  commitment?: { optionId: ID; termCycles: number; startsAt: Instant; endsAt: Instant; paymentMode: 'prepay'|'per_cycle'; cyclesPaid: number };
  pause?: { startsAt: Instant; resumesAt?: Instant; reason?: string };
  cancel?: { requestedAt: Instant; effectiveAt: Instant; mode: 'period_end'|'immediate'|'commitment_end'; reasonCode: string; reasonText?: string; by: ActorRef };
  appliedDiscounts: { discountId: ID; versionId: ID; remainingCycles: number | 'forever' }[];
  accountCreditCents: Cents;                             // from downgrade proration
  plannedChanges: PlannedChange[];
  scheduleSeriesId?: ID;                                 // Agent 03 recurring series (auto-scheduling)
  preferredWindow?: { daysOfWeek: number[]; startLocal: string; endLocal: string };
  preferredWorkerId?: ID;
  dunning?: { attempt: number; nextAttemptAt?: Instant; lastDeclineCode?: string; startedAt: Instant };
  flags: { disputed?: boolean; manualReview?: boolean };
  consentRecordId?: ID;
  previousSubscriptionId?: ID;
  externalRef?: string;                                  // migrated from another platform
  createdBy: ActorRef; createdAt; updatedAt; version: number;
}

interface PlannedChange {
  id: ID; type: 'price_change' | 'plan_change' | 'pause' | 'resume' | 'cancel';
  effectiveAt: Instant | 'next_renewal';
  payload: unknown; noticeSentAt?: Instant; createdBy: ActorRef;
}

interface SubscriptionPeriod {
  id: ID; subscriptionId: ID; seq: number;               // 1,2,3…  unique(subscriptionId, seq)
  start: Instant; end: Instant;                          // [start, end)
  status: 'scheduled'|'due'|'charging'|'paid'|'prepaid'|'comped'|'unpaid'|'awaiting_payment'|'void'|'refunded'|'partially_refunded';
  amount: { subtotalCents: Cents; discountCents: Cents; creditAppliedCents: Cents; taxCents: Cents; totalCents: Cents };
  chargeId?: ID; invoiceId?: ID;                         // Agent 05 / 04
  idempotencyKey: string;                                // sub:{id}:period:{seq}:charge
  coveredByCommitmentChargeId?: ID;
}

interface EntitlementWindow {                            // materialized per EntitlementDef × window
  id: ID; subscriptionId: ID; entitlementDefId: ID; periodId?: ID;
  start: Instant; end: Instant;
  state: 'provisional' | 'open' | 'closed';
}

interface EntitlementLedgerEntry {                       // append-only
  id: ID; subscriptionId: ID; windowId: ID; entitlementDefId: ID;
  type: 'grant'|'reserve'|'confirm'|'consume'|'release'|'expire'|'rollover_grant'|'adjust';
  quantity: number;                                      // signed
  jobId?: ID; jobLineId?: ID;
  listValueCents?: Cents;                                // value of what was covered (savings reporting, proration fair-use)
  reservationState?: 'provisional'|'firm'|'consumed'|'released';
  idempotencyKey: string;                                // e.g. job:{jobId}:line:{lineId}:reserve
  actor: ActorRef; reason?: string; at: Instant;
}

interface DunningAttempt {
  id: ID; subscriptionId: ID; periodId: ID; attemptNo: number;
  scheduledAt: Instant; executedAt?: Instant;
  result?: 'succeeded'|'failed'|'skipped_hard_decline'|'skipped_no_method';
  declineCode?: string; chargeId?: ID;
}

interface ConsentRecord {
  id: ID; customerId: ID; subscriptionId?: ID;
  kind: 'auto_renewal' | 'card_on_file_mit' | 'terms';
  textHash: string; textSnapshotId: ID;
  capturedVia: 'checkbox_online'|'staff_verbal'|'signed_link'|'in_person_signature';
  ip?: string; userAgent?: string; at: Instant; staffId?: ID;
}

interface SubscriptionEvent {                            // audit log
  id: ID; subscriptionId: ID; type: string; from?: SubscriptionStatus; to?: SubscriptionStatus;
  actor: ActorRef; reason?: string; data?: unknown; at: Instant;
}
```

### 7.3 Discounts

```ts
interface Discount {
  id: ID; businessId: ID; currentVersionId: ID;
  status: 'scheduled'|'active'|'paused'|'exhausted'|'expired'|'archived';
  method: 'code' | 'automatic' | 'manual_only';
  code?: string; codeNormalized?: string;                // unique per business among non-archived
  isBulk: boolean;                                       // has DiscountCode children
  usage: { totalReserved: number; totalCommitted: number; costCommittedCents: Cents };
  createdBy: ID; createdAt; updatedAt; version: number;
}

interface DiscountVersion {
  id: ID; discountId: ID; versionNo: number;
  name: string; customerLabel: string;
  value:
    | { type: 'percent'; bp: BasisPoints; maxCents?: Cents }
    | { type: 'amount_per_item'; cents: Cents }
    | { type: 'amount_per_order'; cents: Cents }
    | { type: 'fixed_price'; cents: Cents }
    | { type: 'free_item'; catalogItemIds: ID[]; quantity: number };
  scope: { include: TargetScope; exclude: ID[]; includeTravelFee: boolean; includePackages: boolean }; // [S: 09]
  customerEligibility: {                                  // "customer types" [S: 09]
    mode: 'everyone'|'new'|'returning'|'tags'|'members'|'non_members'|'specific';
    tagIds?: ID[]; tagMatch?: 'any'|'all'; packageIds?: ID[]; customerIds?: ID[];
    customerType?: 'residential'|'business'|'both';
  };
  bookingConditions: {                                    // "booking conditions" [S: 09]
    channels?: Channel[]; minSubtotalCents?: Cents; minItems?: number;
    zoneIds?: ID[]; shopIds?: ID[];
    serviceDate?: { from?: LocalDate; to?: LocalDate; daysOfWeek?: number[]; timeFrom?: string; timeTo?: string };
    leadTime?: { minHours?: number; maxHours?: number };
    bookingTypes?: ('one_time'|'recurring'|'subscription_purchase')[];
    firstBookingOnly?: boolean;
  };
  window: { startsAt: Instant; endsAt?: Instant; basis: 'booked_at'|'service_at'|'both' };  // time-sensitive vs lasting [S: 09]
  limits: { totalUses?: number; perCustomer?: number; budgetCents?: Cents };
  recurring: { series: 'first'|'first_n'|'every'; seriesN?: number; subscription: 'once'|'first_n'|'forever'; subscriptionN?: number };
  stacking: { combinesWith: { promo: boolean; member: boolean; recurring: boolean }; priority: number };
  display: { bannerText?: string; showBanner: boolean };
  consumeOnCancel: boolean; refundRestoresUse: boolean;
  createdAt; createdBy: ID;
}

interface DiscountCode {                                  // bulk single-use codes
  id: ID; discountId: ID; code: string; codeNormalized: string;
  status: 'unused'|'reserved'|'used'|'disabled'; assignedCustomerId?: ID;
}

interface DiscountRedemption {
  id: ID; businessId: ID; discountId: ID; discountVersionId: ID; discountCodeId?: ID;
  customerId?: ID; customerFingerprint: string;           // normalized phone/email/address hash for "new customer" abuse checks
  target: { kind: 'job'; jobId: ID } | { kind: 'series'; seriesId: ID } | { kind: 'subscription_period'; periodId: ID } | { kind: 'order'; orderId: ID };
  amountCents: Cents;
  status: 'reserved'|'committed'|'released'|'reversed';
  idempotencyKey: string; createdAt; updatedAt;
}

// Recurring discount from video 14 — stored by Agent 03 on the series, evaluated here:
interface RecurringModifier { seriesId: ID; value: { type: 'percent'; bp: BasisPoints } | { type: 'amount'; cents: Cents } } // [S: 14]

interface PriceModifierApplied {                          // attached to quote/job price snapshot (Agent 04/05 store it)
  stage: 'coverage'|'member'|'recurring'|'promo'|'manual';
  source: { kind: 'entitlement'|'member_discount'|'recurring'|'discount'; id: ID; versionId?: ID };
  label: string; amountCents: Cents;
  allocations: { lineId: ID; cents: Cents }[];
}
```

### 7.4 Gift cards

```ts
interface GiftCard {
  id: ID; businessId: ID;
  codeHash: string; codeEncrypted: string; last4: string;   // §5.9
  kind: 'paid' | 'promotional' | 'store_credit';
  source: 'manual' | 'online' | 'pos' | 'customer_app' | 'refund' | 'campaign' | 'import'; // manual/online [S: 09]
  status: 'pending_delivery'|'active'|'redeemed'|'voided'|'replaced'|'expired'|'frozen';
  currency: string; initialValueCents: Cents;
  balanceCents: Cents;                                   // projection of ledger; checked in same tx
  purchaserCustomerId?: ID; purchaserContact?: Contact;
  recipientCustomerId?: ID; recipientContact?: Contact; recipientName?: string;
  message?: string; designId?: ID;                       // AI design [S: llms]
  delivery: { mode: 'now'|'scheduled'|'print'|'none'; scheduledAt?: Instant; tz?: string; deliveredAt?: Instant; channels: ('email'|'sms')[] };
  expiresAt?: Instant;                                   // null for purchased cards by default (§5.10)
  saleOrderId?: ID; salePaymentId?: ID;                  // Agent 05
  replacedById?: ID;
  lastActivityAt: Instant;
  createdBy: ActorRef; createdAt; updatedAt; version: number;
}

interface GiftCardLedgerEntry {                          // append-only, source of truth
  id: ID; giftCardId: ID; seq: number;                   // unique(giftCardId, seq) — serializes writes
  type: 'issue'|'load'|'hold'|'release'|'redeem'|'refund_credit'|'adjust'|'void'|'expire'|'transfer_out'|'transfer_in'|'cash_out';
  amountCents: Cents;                                    // signed
  balanceAfterCents: Cents;
  holdId?: ID; paymentId?: ID; jobId?: ID; invoiceId?: ID; refundId?: ID;
  reason?: string; actor: ActorRef; idempotencyKey: string; at: Instant;
}

interface GiftCardHold {
  id: ID; giftCardId: ID; amountCents: Cents; paymentIntentId: ID;
  status: 'held'|'captured'|'released'|'expired'; expiresAt: Instant;
}

interface GiftCardDesign {
  id: ID; businessId: ID; name: string;
  source: 'template' | 'ai' | 'upload'; prompt?: string; // [S: llms]
  imageAssetId: ID; textColor: string; isDefault: boolean; archived: boolean;
}

interface CustomerWalletEntry { customerId: ID; giftCardId: ID; addedAt: Instant }  // saved cards in account
```

### 7.5 Settings (per business) `[I]`

```ts
interface SubscriptionSettings {
  billingTimeLocal: string;                    // '06:00'
  grantBenefitsBeforePayment: boolean;         // false
  dunning: { scheduleDays: number[]; retryLocalTime: string; finalAction: 'cancel'|'pause'|'keep'; hardDeclineStopsRetries: boolean };
  pastDueJobPolicy: { keepDays: number; then: 'hold'|'charge_as_regular'|'keep' };
  renewalNoticeDays: number; priceChangeNoticeDays: number;
  prorationUnit: 'day'|'second'; resetAnchorOnPlanChange: boolean;
  autoPauseOnDispute: boolean; pendingActivationTimeoutHours: number;
}
interface DiscountSettings { maxCodesPerBooking: number; bestPriceGuarantee: boolean; manualDiscountCaps: Record<RoleId, { maxBp: BasisPoints; maxCents: Cents }> }
interface GiftCardSettings { enabled: boolean; minCents: Cents; maxCents: Cents; presetsCents: Cents[]; allowCustomAmount: boolean;
  purchasedCardsExpire: false | { years: number }; promoCardsExpireDays?: number; cashOutThresholdCents?: Cents;
  allowForTips: boolean; allowForSubscriptions: boolean; maxCardsPerPayment: number; fraudReviewAboveCents?: Cents }
```

### 7.6 Keys & indexes `[I]`

- `Subscription(businessId, status, nextBillingAt)` — billing run scan.
- `Subscription(businessId, customerId)`; partial unique `(customerId, packageId) where status in non-terminal` (unless `allowMultiplePerCustomer` — e.g. two vehicles each with a plan: then unique on `(customerId, packageId, assetId)`).
- `SubscriptionPeriod unique(subscriptionId, seq)`; `unique(idempotencyKey)`.
- `EntitlementLedgerEntry unique(idempotencyKey)`; `(windowId)`.
- `Discount unique(businessId, codeNormalized) where status not archived`; `DiscountCode unique(businessId, codeNormalized)` (shared namespace with Discount codes).
- `DiscountRedemption unique(idempotencyKey)`; `(discountId, customerId, status)`.
- `GiftCard unique(businessId, codeHash)`; `GiftCardLedgerEntry unique(giftCardId, seq)`, `unique(idempotencyKey)`.

---

## 8. API surface

Commands mutate; queries read. Transport-agnostic (REST/RPC/MCP tool names map 1:1) `[I]`. Every
command takes `idempotencyKey` (required for anything that moves money or consumes a limited
resource) and `expectedVersion` where it edits an existing record. Errors are typed
(`code`, `message`, `field?`, `retryable`) — never free text only. Auth column uses Agent 09
permission keys (§2); "customer" = authenticated booking-site/customer-app session scoped to own
records; "public" = unauthenticated but rate-limited; "system" = internal scheduler/service
identity. The CRM MCP exposes the staff commands; the Bookings MCP exposes only the customer
ones `[S: llms]` `[I]`.

Naming is aligned with peer specs: Agent 01 calls `QuoteSubscription`, `StartSubscription`,
`ValidateCode`, `RedeemCode`, `QuoteGiftCard`, `IssueGiftCard`; Agent 05 calls
`discounts.evaluate` and `giftcards.hold|capture|release|refund|issueOrReload`. Aliases are listed.

### 8.1 Packages

| Name | Input | Output | Auth |
|---|---|---|---|
| `subscription.package.create` | `PackageDraft` (all §7.1 version fields), `publish: boolean` | `{packageId, versionId, warnings[]}` | `subs.package.edit` (+`publish`) |
| `subscription.package.update` | `packageId, expectedVersion, patch, propagation: 'new_only'\|'at_renewal', noticeDays?` | `{versionId, affectedSubscriptions, plannedChanges}` | `subs.package.edit` |
| `subscription.package.publish` / `.unpublish` / `.archive` / `.duplicate` / `.delete` | `packageId` | package | `subs.package.publish` / `archive` / `edit` |
| `subscription.package.get` | `packageId, versionId?` | `PackageVersion` + stats | `subs.package.view` |
| `subscription.package.list` | filters (status, kind, selfPurchase, q), cursor | page | `subs.package.view` |
| `subscription.package.listPublic` | `zoneId?, customerType?` | public package cards (no internal fields) | public |
| `subscription.package.previewSavings` | `packageVersionId, sampleCart` | `{memberTotal, regularTotal, savings}` | `subs.package.view` / public for published |

### 8.2 Subscriptions

| Name | Input | Output | Auth |
|---|---|---|---|
| `subscription.quote` (alias 01 `QuoteSubscription`) | `{customerId?\|draftCustomer, packageVersionId, commitmentOptionId?, billingStart, addressId\|address, discountCode?, cart?}` | `QuoteSnapshot {firstChargeCents, taxCents, discounts[], nextChargeAt, periods preview, visits preview, cartRepriced?, quoteHash, expiresAt}` | `subs.subscription.create` / customer |
| `subscription.create` (alias 01 `StartSubscription`) | `{quoteHash, customerId, packageVersionId, commitmentOptionId?, billingStart, collection, paymentMethodId?, firstVisit?{start, workerId?}, preferredWindow?, assetIds, consent: ConsentInput, channel, sendToCustomer?: boolean, idempotencyKey}` | `{subscriptionId, status, chargeResult?, checkoutLink?}` | `subs.subscription.create` / customer |
| `subscription.get` | `subscriptionId` | full record + current period + entitlement balances + upcoming visits | `subs.subscription.view` / customer(own) |
| `subscription.list` | filters (§3.3), sort, cursor | page + KPI strip | `subs.subscription.view` |
| `subscription.listForCustomer` | `customerId` | subscriptions (+ bundles) | `subs.subscription.view` / customer(own) |
| `subscription.changePlan` | `subscriptionId, newPackageVersionId, commitmentOptionId?, timing: 'now'\|'next_renewal', prorationPreviewHash, idempotencyKey` | `{plannedChange\|appliedChange, chargeOrCredit}` | `subs.subscription.edit` / customer if allowed |
| `subscription.previewChange` | same minus hash | `ProrationPreview {creditCents, chargeCents, entitlementDiff[], repricedJobs[], hash}` | same |
| `subscription.adjust` (live adjustment, §8.7) | `subscriptionId, ops: AdjustOp[], effective: 'now'\|'next_renewal'\|'this_job', previewHash, consent?, idempotencyKey` | `{applied[], charge?, jobRepriced[]}` | `subs.subscription.edit` (+ per-op permissions) |
| `subscription.changeBillingDate` | `subscriptionId, newAnchorLocalDate, prorate: boolean` | updated + preview | `subs.subscription.edit` |
| `subscription.updatePaymentMethod` | `subscriptionId, paymentMethodId` → triggers immediate retry if past due | `{retried, result?}` | `subs.subscription.edit` / customer(own) |
| `subscription.sendPaymentUpdateLink` | `subscriptionId, channels` | `{linkId, expiresAt}` | `subs.subscription.edit` |
| `subscription.pause` / `.resume` | `subscriptionId, startsAt\|'next_renewal', resumesAt?` | updated | `subs.subscription.pause` / customer if allowed |
| `subscription.skipVisit` | `subscriptionId, jobId` | `{releasedEntitlements}` | `subs.subscription.edit` / customer if allowed |
| `subscription.cancel` | `subscriptionId, mode, refund: 'none'\|'prorated', futureJobs: 'cancel'\|'convert_to_paid', reasonCode, reasonText?` | `{status, refundId?, jobsAffected[]}` | `subs.subscription.cancel` / customer (online-purchased always) |
| `subscription.undoCancel` | `subscriptionId` | updated | `subs.subscription.cancel` / customer(own) |
| `subscription.retryPayment` | `subscriptionId, periodId` | charge result | `subs.dunning.override` |
| `subscription.recordManualPayment` | `periodId, paymentId` (Agent 05 cash/check) | period paid | `subs.dunning.override` |
| `subscription.compPeriod` | `subscriptionId, reason` | period comped | `subs.subscription.comp` |
| `subscription.refundPeriod` | `periodId, amountCents\|'full', expireUnconsumed: boolean` | `{refundId}` | `subs.subscription.refund` |
| `subscription.entitlement.adjust` | `subscriptionId, entitlementDefId, windowId, delta, reason` | ledger entry | `subs.subscription.comp` |
| `subscription.entitlement.balances` | `subscriptionId, at?` | per entitlement: granted/reserved/consumed/available/expiresAt | `subs.subscription.view` / customer(own) |
| `subscription.coverageForJob` | `jobId` or `{customerId, serviceDate, lines}` | which lines are covered/discounted and by what | internal (03/04/05) / worker read |
| `subscription.upsellCandidates` | `{cart, customerId?, zoneId, channel}` | ranked `{packageId, versionId, headline, savingsCents, memberCart}`[] | public / customer |
| `subscription.reactivate` | `previousSubscriptionId, …create input` | new subscription | `subs.subscription.create` / customer |
| `subscription.export` | filters | CSV job id | `subs.subscription.view` |
| `subscription.import` | CSV rows (migration: external ids, next bill date, card token ref) | dry-run report / commit | owner |
| `subscription.billing.runDue` | `{now, shard}` | counts | system |
| `subscription.dunning.runDue` | `{now, shard}` | counts | system |

### 8.3 Pricing / discounts

| Name | Input | Output | Auth |
|---|---|---|---|
| `pricing.modifiers.evaluate` (alias 05 `discounts.evaluate`) | `EvalRequest {businessId, context:{channel, customerId?, customerFingerprint?, bookedAt, serviceAt, zoneId, shopId?, bookingType, seriesId?, occurrenceIndex?, subscriptionId?, assumeMembership?: versionId}, lines: PriceLine[] (post stage 2), codes: string[], manual?: ManualDiscount[], recurring?: RecurringModifier, dryRun}` | `EvalResult {lines with modifiers, applied: PriceModifierApplied[], rejected: {code, reason, failedConditions[]}[], entitlementPlan[], warnings[], evalHash}` | internal + staff + customer (no internal fields) |
| `discount.validateCode` (alias 01 `ValidateCode`) | `code, cartContext` | `{valid, kind: 'discount'\|'giftcard'\|'referral', label, reason?}` (single field accepts all three) | public (rate-limited) |
| `discount.redemption.reserve` (alias 01 `RedeemCode`) | `{evalHash, target, idempotencyKey}` | redemptions[] | internal |
| `discount.redemption.commit` / `.release` / `.reverse` | `redemptionId` or `target` | updated | internal |
| `discount.create` / `.update` / `.pause` / `.resume` / `.archive` / `.duplicate` | `DiscountDraft` / patch | discount + version | `discounts.create` / `edit` / `delete` |
| `discount.generateBulkCodes` | `discountId, count ≤ 10,000, prefix` | job id → CSV | `discounts.create` |
| `discount.test` | `discountId, sampleContext` | eligibility trace | `discounts.view` |
| `discount.list` / `.get` / `.stats` | filters | page / detail / `{uses, cost, revenueInfluenced}` | `discounts.view` |
| `discount.listApplicable` | `jobContext` | discounts staff can pick in Options `[S: 16]` | `discounts.apply_manual` |
| `discount.issueForCustomer` (for 08 referral/drip rewards) | `{templateDiscountId\|spec, customerId, expiresAt?, reason}` | single-use code | system (08) / `discounts.create` |

### 8.4 Gift cards and account credit

| Name | Input | Output | Auth |
|---|---|---|---|
| `giftcard.quote` (alias 01 `QuoteGiftCard`) | `amountCents, designId, delivery` | `{totalCents, limitsOk}` | public |
| `giftcard.issue` (alias 01 `IssueGiftCard`) | `{kind, amountCents, salePaymentId?, recipient, purchaser?, message?, designId?, delivery, expiresAt?, idempotencyKey}` | `{giftCardId, last4, status, revealOnceCode?}` | `giftcards.issue` (cap) / system after online payment |
| `giftcard.lookup` | `code` | `{giftCardId, last4, balanceCents, status, expiresAt?}` | customer/public (rate-limited), staff |
| `giftcard.authorize` | `code\|giftCardId, amountCents` | `{usableCents}` | internal |
| `giftcard.hold` / `.capture` / `.release` | `{giftCardId, amountCents, paymentIntentId, ttl?}` / `holdId` | hold / ledger entry | internal (05) |
| `giftcard.refund` | `{giftCardId, amountCents, refundId, idempotencyKey}` | ledger entry | internal (05) |
| `giftcard.issueOrReload` (05 refunds-as-credit, 08 rewards) | `{customerId, amountCents, kind: 'store_credit'\|'promotional', reason, sourceRef}` | card (customer-bound credit, no shareable code needed) | internal / `giftcards.issue` |
| `giftcard.adjust` | `giftCardId, deltaCents, reason` | ledger entry | `giftcards.adjust` |
| `giftcard.void` / `.reissue` / `.freeze` / `.unfreeze` / `.cashOut` | `giftCardId, reason` | updated | `giftcards.void` / `adjust` / `refund` |
| `giftcard.resend` / `.changeRecipient` / `.reschedule` | … | updated | `giftcards.issue` / purchaser (before delivery) |
| `giftcard.reveal` | `giftCardId` | full code (audited) | `giftcards.view` + `giftcards.reveal` |
| `giftcard.ledger` | `giftCardId` | entries | `giftcards.view` / holder |
| `giftcard.list` / `.stats` | filters | page / liability, sold, redeemed, breakage | `giftcards.view` |
| `giftcard.wallet.add` / `.list` | `code` / — | wallet | customer |
| `giftcard.design.generate` | `{prompt, brandTokens}` | 4 candidate designs | `giftcards.issue` / owner (`[S: llms]` AI design) |
| `giftcard.design.save` / `.list` / `.archive` | … | designs | `giftcards.issue` |
| `giftcard.delivery.runDue` / `giftcard.expiry.runDue` | `{now}` | counts | system |

### 8.5 Reporting queries (consumed by Agent 08)

`report.recurring.mrr(range, groupBy)`, `report.recurring.churn(range)`,
`report.recurring.dunning(range)`, `report.recurring.utilization(packageId, range)`,
`report.discounts.cost(range, groupBy)`, `report.giftcards.liability(asOf)`,
`report.giftcards.activity(range)`, `report.giftcards.escheat(asOf, stateRules)` — formulas §5.13.
Auth `reports.recurring_revenue.view` / `discounts.view` / `giftcards.view`.

### 8.6 Webhooks / outbound

All §9 PRODUCES events are available to business webhooks/MCP subscribers with PII minimized
(customer id, not contact details) `[I]`.

### 8.7 Live package adjustment from native apps (owner requirement 2026-11-13)

The owner wants staff to **adjust a client's package on site, or during a phone call, from the
native desktop and iPhone apps** (BRIEF.md, 2026-11-13). This is an addendum workflow
**W-SUB-12** (numbered after §4.21) plus the API it needs `[I]`.

**Use cases**
1. *On site:* worker/foreman at a vehicle: "Add interior to my plan" → upgrade package now,
   cover today's add-on, card on file charged prorated difference, receipt on the customer's phone.
2. *On a call:* office staff with the customer on the phone: downgrade at next renewal, pause
   for vacation, move billing date, swap the visit day, add a second vehicle, apply a retention
   discount, or build a brand-new subscription + first booking in one flow.
3. *One-off goodwill:* comp today's add-on (`adjust` entitlement +1) without changing the plan.

**AdjustOp vocabulary** (single command `subscription.adjust` composes several ops atomically):

```ts
type AdjustOp =
  | { op: 'change_package'; packageVersionId: ID; commitmentOptionId?: ID }
  | { op: 'custom_price'; priceCents: Cents; reason: string }            // per-subscription override, permission subs.subscription.custom_price
  | { op: 'add_entitlement'; def: EntitlementDef; scope: 'this_window' | 'ongoing' }   // ongoing = subscription-level customization
  | { op: 'remove_entitlement'; entitlementDefId: ID; effective: 'next_renewal' }
  | { op: 'grant_once'; entitlementDefId: ID; quantity: number; reason: string } // goodwill
  | { op: 'apply_discount'; discountId?: ID; spec?: { bp?: BasisPoints; cents?: Cents; cycles: number | 'forever' } }
  | { op: 'change_frequency'; rule: RecurrenceRule }                      // forwarded to Agent 03
  | { op: 'change_visit_slot'; daysOfWeek: number[]; startLocal: string; workerId?: ID }
  | { op: 'add_asset'; assetId: ID }                                       // second vehicle → per-asset pricing rule from package
  | { op: 'change_billing_date'; anchorLocalDate: LocalDate }
  | { op: 'pause'; startsAt: Instant | 'next_renewal'; resumesAt?: Instant }
  | { op: 'cancel'; mode: 'period_end' | 'immediate'; reasonCode: string };
```

Per-subscription customizations (custom price, ongoing extra entitlements) are stored as a
**subscription-level overlay** `{ overrides: { priceCents?, extraEntitlements[], removedEntitlementIds[] } }`
on top of the package version, so the package stays clean and reports can show "customized" `[I]`.

**Steps (W-SUB-12)**
1. Staff opens the customer (search by phone — caller-ID lookup on desktop app if telephony is
   integrated later) or the job card on iPhone; the Subscription panel shows status, benefits
   left, next charge, next visits.
2. Staff composes changes; every edit calls `subscription.previewChange`/`adjust(dryRun)` which
   returns: money now (charge/credit), next charge amount/date, entitlement diff, jobs repriced
   (including **today's job** when on site), notices that will be sent, and a `previewHash`.
   Preview must return in < 300 ms p95 so it feels live during a call `[I]`.
3. **Consent capture** (required when price goes up, a new recurring charge starts, or the
   commitment changes): on site → customer taps "I agree" on the staff device (signature-style
   screen, terms text visible) or receives an SMS/email link to approve; on a phone call →
   staff selects "Verbal consent" (recorded with staff id, timestamp, script text hash) **and**
   the system sends the written confirmation with a cancel link immediately — some jurisdictions
   require the confirmation for phone sales `[I]`. Stored as `ConsentRecord`.
4. Staff taps **Apply**: `subscription.adjust({ops, previewHash, consent, idempotencyKey})`.
   Server re-evaluates; if the hash no longer matches (price changed, someone else edited,
   job moved) → `409 PREVIEW_STALE` with a fresh preview — never apply a different amount than
   the one shown `[I]`.
5. Side effects: plan change per §4.10 / proration §5.4; charge via Agent 05 off-session
   (card present via Tap to Pay on iPhone possible through Agent 05 POS when the customer wants
   to use a different card); today's job repriced through Agent 04/05 (`jobs.job.totals_changed`);
   schedule changes forwarded to Agent 03; **E** `subscription.subscription.adjusted` (+ specific
   events such as `plan_changed`); **N** confirmation to customer (Agent 08); audit row with
   actor, device, channel (`native_desktop` / `native_ios` / `crm_web`) and, if phone,
   call reference.
6. **Offline (iPhone on site with poor signal)** `[I]`: preview requires the server (money must
   be authoritative). The app may queue a *request* ("Adjust package — pending sync") but the
   customer is told it's pending; on reconnect, the queued op is submitted with its original
   idempotency key and the preview is re-shown to staff for confirmation if anything changed.
   Benefits on today's job are not applied offline unless already reserved.

Permissions: `subs.subscription.edit` (base), `subs.subscription.custom_price`,
`subs.subscription.comp` (grant_once, cap per role e.g. 1 per window for workers),
`discounts.apply_manual` with caps; workers default to `grant_once` + `change_package` to
**published** packages only; office staff all ops; owner can restrict `cancel` to office roles.
