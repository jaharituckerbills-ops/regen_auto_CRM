# 04 — Job Lifecycle & Field Workflow

> Agent 04 · Phase 1 "CRM brain" · 2026-11-12
> Scope: the **Job** as the single central record from first quote to closed invoice, the quote and
> invoice documents that hang off it, the master job status state machine, and how a worker
> performs the job in the field (worker app: day list → on-my-way → start → checklists/photos →
> upsell → payment hand-off → complete), including offline-first sync and photo upload reliability.
> Product name: "the platform". Stack-agnostic. Money is always integer minor units (cents).

## 0. Reading guide

### 0.1 Source legend

| Tag | File |
|---|---|
| `[S: t01]` … `[S: t20]` | `research/transcripts/NN_<id>.txt` (NN = video number; e.g. `t04` = `04_pASG8RTrhWc.txt`) |
| `[S: t04.desc]` etc. | `research/transcripts/NN_<id>.description` (YouTube description; chapter lists are often generic/auto-generated → **low confidence**) |
| `[S: llms]` | `research/site/llms.txt` (fieldd official facts, last updated 2026-10-23 — authoritative) |
| `[I]` | Inference: industry convention or our design decision. Never a claim about Fieldd. |
| `[D]` | Our **design decision** for the platform (a special case of `[I]` that we are proposing, not inferring about Fieldd). |

Source-quality notes (important for anyone re-reading the research):

- The *only* video dedicated to this module is **t04 (Quotes & Invoices)** — ~400 words of narration. Most
  of the job-card and worker-flow detail below is therefore `[I]`/`[D]` and must be verified against
  the owner's walkthrough video (section 14).
- `t04.desc` **chapter list does not match the t04 video** — its chapters ("What the customer portal can
  do", "Installation methods (link, embed, QR)") describe video 05. Treat the t04 chapters as a
  copy/paste error; only the t04 description prose is relevant. `[S: t04.desc]`
- `t15.desc` and `t16.desc` chapter lists ("Tap 'New Job' or 'Add Booking'", "Tap 'Add Payment'",
  "Select Payment Type", "Enter Amount & Save") read as generic auto-generated text and are **not**
  consistent with the narration. They hint at an "Add Payment" action on the worker-app job detail;
  treat as low-confidence hints only. `[S: t15.desc, t16.desc]`

### 0.2 What the research actually says about this module (fact inventory)

Every fact below is quoted/paraphrased from source; all other content in this document builds on these.

| # | Fact | Source |
|---|---|---|
| F1 | Competitors assign separate numbers to jobs, quotes and invoices; Fieldd tracks each job "from the initial quote, appointment to the final invoice … within a single job card with the same ID". | `[S: t04]` |
| F2 | Create a quote via **New → Quote**. Business information is already filled in. | `[S: t04]` |
| F3 | On the quote you add or create the customer profile and add the service(s). | `[S: t04]` |
| F4 | You can include **quote terms** and a **"book online" button** so the customer can self-schedule if they accept. | `[S: t04]` |
| F5 | Right-hand side of the quote editor: **job address, preferred worker, customer assets, contact info to notify** the customer. | `[S: t04]` |
| F6 | **Preview** the quote and **send** it. | `[S: t04]` |
| F7 | If the customer accepts **and books**, Fieldd keeps everything in sync and **updates the job card automatically**; at that point Fieldd **generates an invoice** for the job. | `[S: t04]` |
| F8 | Standalone invoice: **New → Invoice**, fill in info, preview and send. | `[S: t04]` |
| F9 | "Everything shown here can also be done in the field worker app" — for **estimators or on-site staff** to deliver quotes and help customers book immediately. | `[S: t04]` |
| F10 | Quote/invoice look and the emails sent when a quote or invoice is sent, plus **reminder settings**, live in **Settings → Communication → Email**. | `[S: t04]` |
| F11 | "Right below that" is the **Payment** section: deposits, prepayments, pre-authorization for jobs. | `[S: t04]` |
| F12 | Jobs tab shows upcoming appointments with **job time, address, service, payment status, assigned workers**; drag to reschedule (time or staff) and Fieldd notifies the necessary people. | `[S: t08]` |
| F13 | Jobs tab filters: **job status, tags, service areas, assigned staff, customers**. Also manage **recurring jobs**, track **open quotes**, view **all invoices**, "each clearly marked with their status". | `[S: t08]` |
| F14 | Sidebar search finds **job number, invoice, staff, customer**. | `[S: t08]` |
| F15 | New → Appointment: pick existing service or create one; **plus** button for extra services/add-ons; find/create customer; add job address; schedule (first available time/worker or manual); **Book now** → added to calendar, assigned team notified, customer reminders sent. Edit later from calendar or search. | `[S: t15]` |
| F16 | Job editor left side: service (dropdown or type a new one and save it for next time), **discounts, tax, card on file**, **cost of each service** to track profit per job. Right side: customer, date/time or auto-find slot + worker (travel time, closest worker), then **customer assets, notes, job tags**, repeat toggle. | `[S: t19]` |
| F17 | Job form section **"Options and payment"**: Options = attach/create **discount**, apply **tax**, add **gift card**. Payment = **when and how** to collect: charge after job complete, or add **card on file** then charge at booking or after job. | `[S: t16]` |
| F18 | If "after job": worker marks the job **complete in the worker app** and is then taken to the **POS**: enter new card, charge card on file, or mark **cash, check**, or **send an invoice**. Recorded payment appears under **Transactions**. | `[S: t16]` |
| F19 | Services carry **checklists (checkboxes and questions)** shown in the worker app, and **required before and after photos** "by specifying how many should be uploaded at each stage"; global checklist/photo requirements exist in settings, service-specific ones add to them. | `[S: t06]` |
| F20 | Service-specific **customer questions** answered before booking; global questions can be turned off per service. | `[S: t06]` |
| F21 | Customer add-ons (visible online) and **company add-ons** (labor fees, equipment charges — only visible in CRM/worker app). Add-ons can carry **cost, duration (added to booking), inventory (part numbers, stock, min/max qty)**. Upsells can be added **after booking** in the CRM or **worker app POS before the job is marked complete**. | `[S: t07]` |
| F22 | Route jobs: worker swipes **start route**; route optimized; worker proceeds through each job with "the normal job workflow"; **before/after photos, complete checklists, message the client, finish all required steps**; route **closes automatically** when the final job is completed. Admin sees completed/remaining jobs and completion %. | `[S: t02]` |
| F23 | Recurring job: New → Recurring job; frequency daily/weekly/monthly; end never/date/count; recurring discount fixed $ or %; notes/tags/files; **auto-finds best available worker for each future job**; edits can be "committed across all future bookings" from the Recurring tab. | `[S: t14]` |
| F24 | Mobile businesses should pair service areas with **live tracking**: customers receive real-time updates on arrival. Zones carry **travel charge** (fixed $) and **custom tax rate**. CRM can override out-of-area bookings. | `[S: t11]` |
| F25 | Online booking: booking questions; intake form saved to profile; **customer assets**; terms & conditions acceptance; **cancellation policy** with feedback and optional reschedule prompt. | `[S: t05]` |
| F26 | Plans include: point of sale, **invoicing and quoting**, sales pipelines, **job photos**, timesheets, credit cards, gift cards, **team app**, recurring jobs, Google review booster, **automated SMS and email job updates**, calendar sync, **on-my-way tracking** (all on the $199 Start tier). | `[S: llms]` |
| F27 | 2026 features: **split a job or its services between team members**, recurring in the same flow, **schedule or reschedule jobs in the past**; **vehicle on every job**; dashboard "all photos"; live chat incl. group chats with the team; customers with multiple phones/people/addresses; ~200 emails rebuilt. | `[S: llms]` |
| F28 | MCP: CRM MCP (150+ actions, e.g. booking jobs) and Bookings MCP (customer-level); Darwin AI can do anything a business can do. | `[S: llms]` |
| F29 | fielddPAY: in-app POS, tipping, card on file, gift cards, Apple Pay; card-not-present (invoices, online, card on file) 2.9%. | `[S: llms]` |
| F30 | Transactions: card payments "charged on invoices through the customer portal and in the worker app POS". | `[S: t17]` |
| F31 | Timesheets: worker clock in/out/breaks in the worker app (separate from job time). | `[S: t03]` |
| F32 | Leads can be turned into a booking without leaving the dashboard. | `[S: t20]` |

Everything not in this table is `[I]` or `[D]`.

### 0.3 Glossary

| Term | Meaning in this spec |
|---|---|
| **Job** | The aggregate root. One record, one human-readable **job number**, that carries the quote, the appointment (schedule + field work), the invoice, payments view, photos, checklists, messages and audit. `[S: t04]` for the single-ID concept; structure `[D]`. |
| **Job number** | Per-business monotonically increasing integer shown as `#1042`. Same number printed on the quote ("Quote #1042") and invoice ("Invoice #1042"). `[D]` (concept `[S: t04]`) |
| **Quote** | A versioned, customer-facing **offer** document generated from the job's line items + terms. Lives *inside* the job. |
| **Invoice** | A versioned, customer-facing **request for payment** document generated from the job's line items. Lives inside the job. Auto-created when the job is booked. `[S: t04]` |
| **Line item** | Service, customer add-on, company add-on, custom item, fee, discount or adjustment on the job. |
| **Appointment / schedule slot** | The time window + assigned workers. **Owned by Agent 03** (Scheduling); the job holds a denormalized snapshot. |
| **Visit / field execution** | The worker-side performance of the job: on-my-way, arrival, start, pause, checklist responses, photos, completion. Owned by 04. |
| **Requirements** | The resolved set of checklist items, questions and photo counts for a job, computed from Agent 02's catalog rules at booking time and snapshotted. |
| **Document rendering** | An immutable rendered (HTML/PDF) snapshot of a quote or invoice version, with a content hash. |
| **Public link** | An unguessable, revocable token URL letting the customer view/accept/pay a document without logging in. |
| **POS** | Point-of-sale checkout screen in the worker app/CRM. Cart editing (upsell) is 04; tendering/charging is **Agent 05**. |
| **Office** | Any CRM user (owner/admin/manager/dispatcher) as opposed to a field worker. |

---

## 1. Summary

The Job module is the spine of the platform. Every piece of revenue work — whether it starts as a quote
an estimator writes on a driveway, an online booking from the customer's website, a recurring
subscription visit, a lead the office converts, or a standalone invoice for parts — becomes **one Job
record with one job number** that never changes as it moves from *quoted* to *scheduled* to *in
progress* to *completed* to *invoiced* to *paid* `[S: t04]`. The quote and the invoice are not separate
objects with separate numbers; they are **versioned documents rendered from the same job** and labelled
with the same number, so the office, the worker and the customer always talk about "#1042".

This module defines: the Job aggregate (line items, address, assets, assignments snapshot, notes, tags,
files, photos, checklist responses, messages link, payments projection, documents, audit log); the quote
workflow (draft → send → view → accept/book or decline/expire, with revisions); the invoice workflow
(auto-generated on booking, standalone invoices, issue/send, partial payments, reminders, void); the
**master job status state machine** reconciled with quote, schedule, field-work, invoice and payment
sub-states; and the **worker app job flow** (day list, on-my-way with ETA/live tracking, start,
checklists, required before/after photos, messaging, POS upsell, payment hand-off, complete; estimators
quoting on site) built **offline-first** with explicit sync-conflict rules and a resumable photo upload
pipeline. Payments themselves (charging, refunds, tips, tax order authority) belong to Agent 05,
scheduling/assignment to Agent 03, catalog/checklist/photo rule definitions to Agent 02, customers/assets
to Agent 07, notifications to Agent 08 and permissions to Agent 09 — section 9 defines those contracts.

---

## 2. Roles

| Role | What they do in this module | Source |
|---|---|---|
| **Owner / Operator** | Everything. Configures document styling, numbering, quote/invoice defaults, completion requirements enforcement. Sees cost & profit. | `[S: t03]` names "owner, operator, or an admin"; rest `[I]` |
| **Admin** | Same as owner except billing/plan and (configurable) destructive financial actions (void paid invoice, delete job with history). | `[I]` |
| **Manager / Dispatcher** | Creates/edits jobs, quotes, invoices; sends documents; reschedules (via 03); marks no-show/cancel; overrides completion requirements with reason; reopens completed jobs. | `[I]`; foreman/manager concept `[S: t03]` |
| **Foreman / Crew lead** | In the worker app: sees crew jobs, can start/complete on behalf of crew, edit line items/prices if permitted, approve upsells. | `[S: t03]` mentions foreman/manager permission tier; job-side powers `[I]` |
| **Field worker (technician)** | Sees *assigned* jobs; on-my-way, start, checklists, photos, message client, upsell via POS (if permitted), collect payment (if permitted), complete. | `[S: t02, t06, t07, t16]` |
| **Estimator** | Worker-app user who creates and sends quotes on site and can book the customer immediately. | `[S: t04]` |
| **Customer** | Receives quote/invoice by email/SMS; views, accepts (and books online), declines, pays, downloads PDF; receives on-my-way ETA + live tracking; messages the worker/office. | `[S: t04, t11]`; details `[I]` |
| **Customer's notify contacts** | Additional contacts selected on the job/quote who receive notifications (e.g. spouse, property manager, fleet manager). | `[S: t04]` "contact info to notify the customer"; multi-people per contact `[S: llms]` |
| **System** | Number allocation, invoice auto-generation, quote expiry, overdue detection, status derivation, route closure signal, reminders hand-off, AI/MCP actions. | `[S: t04, t02, llms]`; mechanics `[I]` |
| **AI assistant / MCP client** | Calls the same commands as office users within the permission scope of the connected user (CRM MCP) or customer scope (Bookings MCP: accept quote, book, pay). | `[S: llms]` existence; scoping `[D]` |

---

## 3. Screens

Conventions: "CRM" = desktop/tablet web app; "WA" = worker app (iOS/Android, phone-first). Layout
descriptions are `[I]` unless tagged — visual truth comes from the owner's video. Field lists give
*type · validation · default*.

### 3.1 CRM — "New" menu entry points

- Global **New** button (top/side nav) `[S: t01, t04, t13, t14, t15]` lists at least: Appointment `[S: t15]`,
  Quote `[S: t04]`, Invoice `[S: t04]`, Recurring job `[S: t14]`, Subscription `[S: t01]`, Subscription
  package `[S: t01]`, Worker `[S: t13]`, (Customer, Lead — `[S: t18, t20]` implied).
- Jobs-module entries: **Appointment**, **Quote**, **Invoice**, **Recurring job**. All four open the **same
  Job editor** in a different *mode* `[D]` — this is how we guarantee the single-ID model (the mode only
  changes which sections are expanded, which primary button is shown and which document is previewed).

| Mode | Primary button | Sections emphasised | Resulting job state |
|---|---|---|---|
| Appointment | **Book now** `[S: t15]` | Services, customer, address, schedule, options & payment | `scheduled` (+ invoice draft auto-created) |
| Quote | **Preview** → **Send quote** `[S: t04]` | Business header, customer, services, quote terms, book-online toggle, right panel | `quoted` |
| Invoice | **Preview** → **Send invoice** `[S: t04]` | Business header, customer, line items, due terms, payment options | `invoiced` (no field work) |
| Recurring job | **Book now** `[S: t14]` | + recurrence block (Agent 03) | series created; each occurrence is its own job |

### 3.2 CRM — Job editor (shared by all modes)

Two-column layout on desktop: **left = what & how much**, **right = who, where, when** — matches the
narrated layout in t04 ("On the right hand side … job address, preferred worker, customer assets, and
contact info") and t19 ("on the left you'll put in the service … on the right attach an existing
customer") `[S: t04, t19]`. On mobile/tablet portrait the right column stacks above the left `[D]`.

**Header bar**
- Job number chip `#1042` (assigned on first save; "Draft" before) `[D]`.
- Mode switcher (Appointment / Quote / Invoice) — allowed only before the first document is sent `[D]`.
- Master status pill + payment status pill (two separate pills, mirroring the jobs-tab "payment status" column `[S: t08]`).
- Overflow: Duplicate, Discard draft, View audit log, Print/PDF.

**Left column**

1. **Business information block** (quote/invoice modes) — read-only, prefilled from Settings → Business
   profile: logo, legal name, address, phone, email, website, tax/licence ID `[S: t04]` "business information
   is already filled in"; field list `[I]`. Link "Edit in settings".
2. **Services & items**
   - Row per line: item picker (search catalog: services, customer add-ons, company add-ons) `[S: t07, t15]`;
     "type a new one and save it for next time" creates a custom line with a "Save to catalog" checkbox `[S: t19]`.
   - Per line fields: name (text, 1–120 chars, required), description (text ≤ 2,000), qty (decimal ≥ 0.001,
     ≤ 9,999, default 1), unit price (money, ≥ 0 unless kind = discount/adjustment, default catalog price),
     cost (money ≥ 0, default catalog cost; visible only with `job.cost.view`) `[S: t06, t07, t19]`,
     duration minutes (int 0–1,440, default catalog duration; services/add-ons that are services) `[S: t06, t07]`,
     taxable (bool, default from catalog/zone), assigned worker(s) (optional per-line split `[S: llms]`),
     parent service (for add-ons; default = the service it was added under).
   - **+** button to add extra services/add-ons `[S: t15]`. Add-ons are filtered to those paired with the
     selected services (Agent 02's pairing rules `[S: t07]`); company add-ons shown with a "Company" badge
     and never rendered as selectable online `[S: t07]`.
   - Product add-ons show stock level and min/max quantity from inventory `[S: t07]`; qty validation
     against min/max is a **warning** in CRM (office may override), hard limit online `[D]`.
   - Drag handle to reorder lines (order is preserved on documents) `[D]`.
3. **Options** `[S: t16]`: Discount (attach existing code/promo or create one inline — Agent 06),
   Apply tax (toggle; rate from service-area zone tax `[S: t11]` or business default; Agent 02/05), Gift card
   (apply a gift card balance — tender, Agent 05/06), Travel fee (auto-added from zone `[S: t11]`, removable
   with permission `[D]`).
4. **Payment** `[S: t16]`: timing radio — *Charge after job is complete* (default `[I]`) / *Charge at booking*;
   card on file picker / "Add card" (Agent 05 component); deposit / prepayment / pre-authorization per
   Settings → Payments `[S: t04]` (Agent 05). In invoice mode: **due terms** (Due on receipt / Net 7 / Net 15 /
   Net 30 / Custom date) `[D]` and "Allow online card payment" toggle `[D]`.
5. **Totals panel** (sticky at bottom of left column): Subtotal, Discounts, Fees, Tax, **Total**, Deposit
   required, Paid to date, **Balance due**; with `job.cost.view`: Cost, Profit, Margin % `[S: t19]` "track
   profits automatically for every job"; layout `[I]`.
6. **Quote terms** (quote mode) `[S: t04]`: rich text (bold, italics, lists, links; ≤ 10,000 chars), default
   from Settings → Quotes; per-quote override. **Quote expiry** date (default today + N days, N from settings,
   default 30) `[D]`.
7. **Book-online button** toggle (quote mode) `[S: t04]`: "Let the customer book a time when they accept".
   Sub-options `[D]`: restrict bookable workers to preferred worker / any qualified worker; earliest bookable
   date; require deposit to confirm.
8. **Invoice notes / footer** (invoice mode): free text, default from settings `[D]`.
9. **Message to customer** (quote & invoice): short text included in the email/SMS body `[I]`.

**Right column**

1. **Customer** `[S: t04, t15, t19]`: search existing (name, phone, email, company) or **+ create** inline
   (first/last/company, phone(s), email, — Agent 07 component). Shows tags, balance owed, card on file
   badge, last job. Business customers with multiple people `[S: llms]`.
2. **Job address** `[S: t04, t15]`: pick from customer's saved addresses or enter new (autocomplete,
   geocoded; Agent 07 owns address book). Shows the resolved **service area/zone** (Agent 02) with
   out-of-area warning + override `[S: t11]`. Shop jobs: pick shop instead of address `[S: t11]`.
3. **Preferred worker** `[S: t04]` (quote mode) / **Assigned worker(s)** (appointment mode; Agent 03 picker
   with auto-find first available worker `[S: t15, t19]`).
4. **Schedule** (appointment mode — Agent 03 component): date/time, duration (sum of line durations
   `[S: t06, t07]`), "find first available" `[S: t15]`, route day for route services `[S: t02]`, repeat toggle
   `[S: t19]` → recurrence block, past dates allowed `[S: llms]`.
5. **Customer assets** `[S: t04, t19]`: multi-select from the customer's assets (vehicles, properties,
   equipment), **+ add asset** inline (Agent 07). A vehicle shows make/model/year/color/plate/photo `[S: llms]`.
6. **Notify contacts** `[S: t04]`: checkboxes for the customer's contacts/people with channel toggles
   (email/SMS) per contact; default = primary contact on both channels the customer has consented to `[D]`.
7. **Notes** `[S: t19]`: *Customer-visible note* and *Internal note* (two fields — `[D]`; internal notes are
   never rendered on documents).
8. **Tags** `[S: t19, t08]`: multi-select tag chips, create inline.
9. **Files** `[S: t14]`: attach files (PDF, images, docs; ≤ 25 MB each, ≤ 50 per job `[D]`); per-file
   "visible to customer" and "visible to workers" toggles `[D]`.
10. **Booking answers** (read-only when job came from online booking; editable by office) `[S: t05, t06]`.
11. **Lead source / booking source** (read-only; set by origin) `[S: t05]`.

**Footer actions by mode**
- Appointment: Save draft · **Book now** `[S: t15]`.
- Quote: Save draft · **Preview** · **Send** `[S: t04]` (· Copy link · Download PDF after first send).
- Invoice: Save draft · **Preview** · **Send** `[S: t04]` (· Record payment → Agent 05 · Copy payment link).

**States**
- *Loading*: skeleton for both columns; totals panel shows "—".
- *Empty*: no services → totals 0, Book/Send disabled with inline reason "Add at least one item".
- *Validation error*: field-level messages; a summary banner listing blockers (e.g. "Customer has no email
  or mobile number — the quote can't be sent. Add one, or use Copy link.").
- *Saving*: optimistic; autosave every 5 s of idle for drafts `[D]`; "Saved · 2s ago" indicator.
- *Conflict*: another user/worker changed the job → non-blocking banner "Updated by Dana (worker app) 30 s
  ago — Review changes" with a field diff; on save, server rejects stale version (see §10.8).
- *Success*: toast "Quote #1042 sent to jane@… and +1 512…" with Undo for 10 s (undo = recall
  before the notification dispatcher sends; §10.11) `[D]`.

### 3.3 CRM — Job card (job detail view)

The single place where the whole life of `#1042` is visible. Opened from calendar, jobs list, quotes list,
invoices list, customer profile, search `[S: t08, t15]`.

Layout `[I]`:
- **Header**: `#1042` · customer name · master status pill · payment pill · scheduled time · assigned workers
  avatars · primary action (context-sensitive: Send quote / Schedule / Send invoice / Record payment /
  Reopen) · overflow (Edit, Duplicate, Cancel job, Mark no-show, Void invoice, Delete draft, Print).
- **Lifecycle stepper** across the top: Quote ▸ Booked ▸ On the way ▸ In progress ▸ Completed ▸ Invoiced ▸ Paid —
  steps that didn't apply (e.g. no quote) are shown as skipped; each step shows timestamp + actor on hover `[D]`.
- **Tabs / sections**:
  1. *Overview* — line items with totals, address with map, assets, notes, tags, booking answers.
  2. *Quote* — current version preview, version list (v1, v2…), sent/viewed/accepted log, customer
     acceptance evidence (name, IP, timestamp, signature if captured) `[D]`.
  3. *Invoice* — current invoice preview, versions, due date, reminders sent, payments list (from Agent 05)
     with allocation, balance, refunds.
  4. *Field work* — on-my-way time + ETA sent, arrival, start/pause/resume/complete timestamps, actual vs
     estimated duration, worker GPS points (start/complete), checklist responses, questions, signature.
  5. *Photos* — grouped Before / After / Other, with counts vs required, uploader, time, worker; lightbox;
     "Share with customer" toggle per photo `[D]`; dashboard "all photos" feeds from here `[S: llms]`.
  6. *Messages* — the customer conversation thread scoped to this job (Agent 08 inbox) `[S: t02, llms]`.
  7. *Files*.
  8. *Activity / audit log* — every change with actor, source (CRM, worker app, portal, system, API/MCP,
     AI), before → after.
- **Right rail**: customer card (call/text/email), notify contacts, schedule card (reschedule → Agent 03),
  recurring series link `[S: t14]`, subscription link `[S: t01]`, route link `[S: t02]`, lead link `[S: t20]`.

### 3.4 CRM — Jobs tab sub-views owned (content) by 04

Agent 03 owns the calendar/list/map *views*; 04 owns the **status vocabulary, columns, filters and the
Quotes / Invoices lists** content `[S: t08]`.

**Jobs list columns** `[S: t08]`: job time, address, service(s), **payment status**, assigned workers — plus
`[D]`: job #, customer, master status, total, balance, tags, zone.
**Filters** `[S: t08]`: job status, tags, service areas, assigned staff, customers; `[D]`: date range,
payment status, origin, has-unsynced-field-data, requirements incomplete.

**Quotes list** ("track open quotes" `[S: t08]`): columns #, customer, created, sent, expires, total, status
(Draft/Sent/Viewed/Accepted/Booked/Declined/Expired/Withdrawn), last activity, owner. Quick filters: *Open*
(sent+viewed), *Expiring in 7 days*, *Accepted — needs scheduling*, *Declined*. Bulk: resend, extend expiry,
export CSV.

**Invoices list** ("view all invoices, each clearly marked with their status" `[S: t08]`): columns #, customer,
issued, due, total, paid, balance, status (Draft/Open/Viewed/Partially paid/Paid/Overdue/Void/Refunded),
reminders sent, last payment. Quick filters: *Unpaid*, *Overdue*, *Paid this week*. Totals row (sum balance).
Bulk: send reminders, export CSV, download PDFs (zip).

Search `[S: t08]`: typing `1042` or `#1042` or `INV-1042` or `Q-1042` resolves to the same job card `[D]`.

### 3.5 CRM — Document preview & send dialog

- **Preview** renders exactly what the customer will see (email body + attached/linked document) in a
  side-by-side: left = email/SMS preview with recipients; right = document (HTML) with "Download PDF" `[S: t04]`
  for "preview"; side-by-side `[D]`.
- **Send dialog** fields:
  - To: notify contacts (prefilled from right panel) `[S: t04]`; add ad-hoc email/phone (validated
    RFC 5322-lite / E.164).
  - Channel: Email ☑ SMS ☑ (SMS = short text + link) `[D]`.
  - Subject (email; default template from Settings → Communication → Email `[S: t04]`).
  - Message (prefilled from template; merge fields `{customer.first_name}`, `{job.number}`, `{document.total}`,
    `{document.link}`, `{quote.expires_on}`, `{invoice.due_on}`).
  - Attach PDF ☑ (default on for email) `[D]`.
  - "Send me a copy" ☐ `[D]`.
  - Schedule send (date/time) `[D]` — budget tier: hide.
- Errors: no deliverable channel; contact unsubscribed from SMS (STOP) → SMS disabled with tooltip;
  email hard-bounced previously → warning.

### 3.6 Customer-facing — Quote page (public link; hosted inside the booking portal, Agent 01)

Rendered on the business's booking subdomain (Agent 01) so the brand is consistent `[D]`.

- Business header (logo, name, contact) `[S: t04]`.
- "Quote #1042" · issued date · **valid until** date · version label if > v1 ("Updated 12 Nov") `[D]`.
- Prepared for: customer, job address, assets (e.g. "2019 Toyota RAV4 · Blue") `[S: t04]`.
- Line items (name, description, qty, price), optional items with checkboxes `[D — better than]`, subtotal,
  discounts, fees, tax, total, deposit due at booking (if any).
- **Quote terms** `[S: t04]` with "I accept the terms" checkbox (required before Accept) `[D]`.
- Actions: **Accept & book online** (if book-online enabled) `[S: t04]` / **Accept** (if not) / **Decline**
  (opens reason sheet) / **Ask a question** (opens message to business — Agent 08) / Download PDF.
- Accept flow: optional typed-name or drawn signature `[D]` → if book-online: slot picker (Agent 03
  availability, Agent 01 UI) → payment step per settings (deposit/card on file/pre-auth — Agent 05) →
  confirmation page showing "Booked: Thu 14 Nov, 9:00–11:00 · Job #1042" + add-to-calendar.
- States: *Expired* ("This quote expired on …. Request an updated quote" button → message + job event),
  *Superseded* (auto-redirect to latest version with banner), *Withdrawn* ("This quote is no longer
  available — contact …"), *Already accepted* (shows booking status), *Declined* (shows "You declined
  on … Changed your mind? Contact us"), *Link revoked/invalid* (generic 404 without leaking existence).
- Performance: server-rendered HTML, < 60 KB critical path, no client framework required to read; JS only
  for accept/sign/book `[D]` (brief performance bar).

### 3.7 Customer-facing — Invoice page (public link)

- Business header; "Invoice #1042"; issued, **due date**, status badge (Due / Overdue / Paid / Partially
  paid / Void).
- Bill to / service address / service date / worker (optional per style setting) / assets.
- Line items, subtotal, discounts, fees, tax, total, **payments received (date, method, amount)**, **balance due**.
- **Pay now** (card, Apple Pay `[S: llms]`; amount = balance, or partial if business allows partial online
  payments `[D]`; tip prompt if enabled — Agent 05) `[S: t17]` "payments charged on invoices through the
  customer portal".
- Photos section (only photos flagged "share with customer") `[D]`.
- Download PDF, receipt after payment.
- States: Paid (receipt view), Void ("This invoice was cancelled"), Superseded version → latest.

### 3.8 Settings → Communication → Email (document look + emails + reminders)

Location per `[S: t04]`. Agent 08 owns the email templates/sending; **04 owns the document style model**
rendered into those emails/PDFs. Fields `[I]/[D]`:

- *Document style*: logo (from brand settings), accent color (from brand), font (system set), header
  layout (logo left/centered), show business tax ID ☐, show worker name ☐, show service date ☐, show line
  descriptions ☑, show qty/unit price columns ☑, show tax breakdown by rate ☑, show photos on invoice ☐,
  show assets ☑, footer text (≤ 1,000 chars), payment instructions text (for check/bank transfer).
- *Quote email*: subject, body template, attach PDF default, SMS template (≤ 320 chars incl. link).
- *Invoice email*: subject, body, attach PDF default, SMS template.
- *Reminders* `[S: t04]`: Quote follow-up (off / after N days if not viewed / if viewed but not accepted),
  quote expiring (N days before), invoice due (N days before due), invoice overdue (every N days, max M
  times), receipt on payment ☑. Defaults `[D]`: quote follow-up 3 days; expiring 2 days before; invoice
  overdue at +1, +7, +14 days; max 3.
- Preview pane with sample data; "Send test to me".

### 3.9 Settings → Jobs (proposed; not seen in research) `[D]`

- **Numbering**: next job number (≥ current max + 1; can only increase), display prefixes (Quote "Q-", Invoice
  "INV-", Job "#") — all share the same integer; optional "Strict sequential invoice numbers" (separate gapless
  legal sequence assigned at issuance, printed alongside the job number) for jurisdictions that require it.
- **Quotes**: default expiry days (1–365, default 30), default terms, book-online default (on), require
  signature (off), allow optional items (on), auto-convert accepted quote without booking → status
  "Accepted — needs scheduling" + office task.
- **Invoices**: default due terms (Due on receipt), auto-send invoice on job completion (off / immediately /
  after N hours if unpaid), allow partial online payments (off), auto-generate invoice on booking (on —
  locked on; it is the Fieldd behaviour `[S: t04]`), late fee (budget tier: hidden).
- **Field workflow**: completion requirements enforcement (**Block** / **Warn & require reason** / **Off**) for
  photos and checklists separately; require customer signature at completion (off); auto-arrive by geofence
  (on, radius 150 m); on-my-way ETA source (GPS route / manual); live tracking link expiry; allow workers
  to see prices (on), edit prices (off), add company add-ons (on), apply discounts (off); require payment
  before completion (off); prompt clock-in on first job action when timesheets enabled (on) `[S: t03]`.
- **No-show & cancellation**: no-show fee (amount or % — charged by Agent 05 per cancellation policy owned
  by Agent 01 `[S: t05]`), require photo evidence for no-show (on), wait time before no-show allowed
  (default 15 min after arrival).

### 3.10 Worker app — Today (job list for the day)

`[S: t02]` for route mode and "normal job workflow"; layout `[I]`.

- Top: date switcher (◀ Today ▶, tap for calendar), **sync status chip** ("Synced" · "3 pending" · "Offline"),
  clock-in control if timesheets enabled `[S: t03]`.
- If the worker has a route today: **Route card** — "Anytime Thursday route · 6 stops · Start route" swipe
  `[S: t02]`; once started, jobs list re-orders to optimized sequence (Agent 03) with a progress bar
  "2 / 6 complete · 33%" `[S: t02]`.
- Job cards (sorted by start time, or route sequence): time window, customer name, address (one line),
  services summary, master status chip, payment badge (Paid / Card on file / Collect $X), requirement
  indicators (camera icon "0/4" · checklist icon "0/6"), distance/drive time from current
  location, assigned crew avatars, unsynced dot.
- Tabs: Today · Upcoming · Completed (last 7 days) · Quotes (estimators) `[D]`.
- Empty state: "No jobs today" + upcoming next job date.
- Pull-to-refresh; works fully offline from local store.
- **New** floating button (if permitted): New quote (estimator) `[S: t04]`, New job (if permission) `[S: t15]`.

### 3.11 Worker app — Job detail

- Header: `#1042`, status chip, time window, overflow (Call office, Report issue, View history).
- Customer block: name, **Call**, **Text**, **Message** (in-app thread, Agent 08) `[S: t02]`; notify contacts.
- Address block: address, gate code/access notes, **Navigate** (deep link to Apple/Google Maps/Waze).
- Assets: vehicle card(s) with photo, make/model/year/color/plate/VIN, tire size `[S: llms]`; home/equipment
  details.
- Services & add-ons with quantities and durations (prices shown only if `wa.prices.view`).
- Booking answers & customer notes `[S: t05, t06]`; internal notes (worker-visible).
- Files visible to workers.
- **Requirements panel**: Before photos x/N · Checklist m/M (required r/R) · Questions · After photos x/N ·
  Signature (if required) `[S: t06]`.
- History: previous jobs at this address/asset (last 3) with after-photos `[D]`.
- **Sticky primary action** (state-driven): `On my way` → `I've arrived` (optional) → `Start job` (swipe) →
  `Complete job`; secondary: Pause, Add items (POS), Take photo, Add note.

### 3.12 Worker app — On my way sheet

- Shows computed ETA ("18 min · arrive ~2:47 PM") from device location and routing `[I]`; editable
  (+5/+10/+15 or custom) `[D]`.
- Toggle "Send ETA & live tracking to customer" (default on if business has live tracking `[S: t11, llms]`).
- Recipients preview (notify contacts with SMS consent).
- Confirm → job `en_route`; starts location sharing; if offline: "Will send when back online" with
  staleness rule (§10.6).

### 3.13 Worker app — In-progress job

- Timer (elapsed since start, pauses excluded), estimated duration bar.
- Step list (configurable order, default): 1 Before photos → 2 Checklist & questions → 3 Add-ons/upsell →
  4 After photos → 5 Signature (opt.) → 6 Complete `[I]` (t02 lists photos, checklists, message, "finish all
  required steps").
- **Checklist screen**: items grouped by source (Global / per service name) `[S: t06]`; item types (owned by
  Agent 02): checkbox, yes/no, text, number (with unit/range), single/multi select, photo-required item,
  date; required marker; "N/A" option if item allows; autosave per answer; answered-by avatar for crews.
- **Photo capture**: in-app camera with stage selector (Before / After / Other) and per-service tag; burst
  allowed; shows "Before 2/4"; thumbnails with upload state badges (queued/uploading %/done/failed-retry);
  pick from gallery allowed only if setting permits (`[D]` anti-fraud: default allow, flag "from gallery").
- **Add items (POS cart)** `[S: t07, t16]`: catalog search (customer add-ons + company add-ons + services if
  permitted), qty, price (editable if `wa.prices.edit`), shows new total and added duration with warning if
  it overruns the next job (Agent 03 data); "Customer approved" checkbox / signature for upsells over a
  threshold `[D]`.
- **Pause** (reason: waiting for parts / customer request / weather / break / other) → `paused`; **Resume**.

### 3.14 Worker app — Complete job & payment hand-off

- **Completion check** (§10.4): lists unmet requirements; if enforcement = Block, Complete is disabled with
  "2 after photos missing"; if Warn, shows "Complete anyway" requiring a reason (and permission
  `wa.requirements.override`).
- Completion summary: services done, added items, total, paid to date, **balance due**, customer signature pad
  (optional), "Send photos to customer" toggle `[D]`.
- **Complete** → job `completed` → if balance > 0 and payment timing = after job → **POS** (Agent 05) `[S: t16]`
  with tenders: new card, card on file, cash, check, **send invoice** `[S: t16]`, (tap-to-pay / Apple Pay where
  supported `[S: llms]`), tip prompt (Agent 05). "Skip — collect later" (permission `wa.payment.skip`).
- After payment: receipt sent (Agent 08), "Next job" CTA with drive time; in route mode auto-advance to next stop
  `[S: t02]`; when the last route job completes, route closes automatically `[S: t02]` (Agent 03 consumes event).

### 3.15 Worker app — Estimator: New quote on site `[S: t04]`

- Condensed editor: customer (search/create), address (use current location → reverse geocode `[D]`), assets
  (add vehicle by VIN/plate scan `[S: llms]` lookup), services/add-ons, photos of the problem (attached to the
  quote, optionally shown to customer `[D]`), terms (default), expiry, book-online toggle.
- Actions: **Send** (SMS/email) · **Present on device** (hand phone to customer: review, sign, accept) ·
  **Book now** (customer present: pick slot from availability — Agent 03 — and confirm; collect deposit — Agent 05).
- Offline: quote saved as local draft with provisional number "Draft"; Send/Book disabled with "Needs
  connection" (Present & sign can work offline; acceptance syncs later — §10.7) `[D]`.

### 3.16 Worker app — Sync center

- List of pending items (commands, photos) with size and state; "Retry now"; per-item error with plain
  language ("Job #1042 was cancelled by the office while you were offline — your 4 photos and notes were
  saved to the job record").
- Storage usage, "photos waiting: 37 (210 MB)", Wi-Fi-only toggle for large uploads `[D]` (default off —
  reliability beats data cost for proof-of-work photos).
- Never offers "clear pending" without a double confirmation and export option.

### 3.17 Mobile behaviour (CRM)

- CRM job editor collapses to a single column with a sticky bottom bar (Total · primary button).
- Job card tabs become a segmented control + accordion.
- Document preview opens full-screen with a tab toggle Email ↔ Document.
- All tap targets ≥ 44 px; totals panel readable at 320 px width.

---

## 4. Workflows

Notation: **Writes** = records created/changed; **Emits** = domain events (catalog in §9.3); **Notifies** =
notification triggers handed to Agent 08 (which owns templates, channels, consent and the master
notification catalog). Event names follow the team convention used by 02/03/05/07/08: `job.job.*`, `job.line.*`, `job.visit.*`, `job.photo.*`, `job.checklist.*`, `quote.quote.*`, `invoice.invoice.*` (full catalog and aliases in §9.3).

### W1. Create a quote in the CRM (New → Quote) `[S: t04]`

1. Office clicks **New → Quote**. Editor opens in quote mode; business info prefilled `[S: t04]`.
   *Writes*: nothing yet (client-side draft) until the first meaningful field is entered, then
   `CreateJob{origin:'crm_quote', mode:'quote'}` → Job (status `draft`), job number allocated (§10.9).
   *Emits*: `job.job.created`.
2. Office selects/creates customer (Agent 07 `customer.customer.created` if new) `[S: t04]`.
3. Office adds services/add-ons; prices/costs/durations default from catalog (Agent 02) and are
   **snapshotted** onto the line (§5.1). *Emits*: `job.line.added` (batched per save).
4. Office sets quote terms (default from settings), expiry, **book-online** toggle `[S: t04]`.
5. Right panel: job address (zone resolved → travel fee/tax rate snapshot), preferred worker, assets,
   notify contacts `[S: t04]`.
6. **Preview** `[S: t04]` → server renders a *preview* (not persisted as a version; watermarked "Preview").
7. **Send** `[S: t04]`:
   - Validate (§5.8 quote send guards).
   - Freeze **QuoteVersion vN** (immutable snapshot of lines, totals, terms, expiry, customer/address/asset
     snapshot, style version). Render HTML + PDF, store with SHA-256 hash.
   - Create/refresh **PublicLink** (token) for the quote.
   - Quote state `draft → sent`; job master status → `quoted`.
   - *Emits*: `quote.quote.sent {jobId, quoteVersionId, recipients[], channels[], expiresOn, bookOnline}`.
   - *Notifies*: `quote_sent` to selected notify contacts (email with PDF + link, SMS with link) — Agent 08.
   - Agent 08 schedules quote follow-up/expiring reminders per settings `[S: t04]`.
8. Customer opens link → `quote.quote.viewed` (first view per version; subsequent views counted) → quote
   state `viewed`; office sees "Viewed 2×, last 3:14 PM" `[D]`.

### W2. Customer accepts and books online `[S: t04]`

1. Customer on quote page ticks "I accept the terms", optionally signs, taps **Accept & book online**.
2. `AcceptQuote{publicToken, quoteVersionId, acceptedOptionalLineIds[], signature?, acceptorName}`:
   - Guard: version is latest sent version, not expired, not withdrawn, job not cancelled (§6.2).
   - Writes: `QuoteAcceptance` (version, selected optional lines, name, IP, user agent, timestamp, signature
     image ref, terms hash). Optional lines not selected are marked `declined_optional`; selected ones become
     active job lines.
   - Quote state `accepted`; job master status → `approved` ("Accepted — needs scheduling").
   - *Emits*: `quote.quote.accepted`. *Notifies*: `quote_accepted` → office/preferred worker (internal) `[I]`.
3. If book-online enabled → customer continues to slot picker (Agent 01 UI; Agent 03 availability for the
   job's total duration, address/zone, preferred worker rule).
4. Customer selects a slot; payment step if deposit/card-on-file/pre-auth required (Agent 05).
5. Agent 03 `ScheduleJob{jobId, slot, workers}` (hold → confirm) → *Emits* `scheduling.job.scheduled`.
6. 04 consumes `scheduling.job.scheduled`:
   - Snapshot schedule onto job; master status `approved → scheduled`; quote state `accepted → booked`
     (terminal for the quote) `[S: t04]` "updates the job card automatically".
   - **Auto-generate invoice** (Invoice record in `draft`, number = job number) `[S: t04]` "at that point
     Field will also generate an invoice". *Emits*: `invoice.invoice.created`.
   - *Notifies*: `job_booked_confirmation` to customer, `job_assigned` to workers `[S: t15]` (Agent 08).
7. If the customer abandons after accepting but before booking → job stays `approved`; Agent 08 may run
   an "accepted-not-booked" follow-up; office sees it in **Quotes → Accepted — needs scheduling** `[D]`.
8. If book-online disabled → steps 3–6 are performed by the office (W5 Schedule) after the acceptance
   notification.

Idempotency: AcceptQuote keyed by `(quoteVersionId)` — a second accept returns the existing acceptance
(200, no duplicate events). Payment hold and schedule hold are coordinated by a booking saga (§10.3).

### W3. Customer declines a quote

1. Customer taps **Decline** → reason picker (Price too high / Chose another provider / Not needed now /
   Other + text) `[D]`.
2. `DeclineQuote` → quote `declined`, job master status `declined` (a closed-lost job; not deleted) `[D]`.
3. *Emits* `quote.quote.declined {reason}`; *Notifies* office `quote_declined`; Agent 08 may enrol customer in a
   "declined quote win-back" drip; Agent 07 lead/pipeline stage updates to Lost.
4. Office can **Revise** a declined quote (creates vN+1, back to `quoted` on send) — declines are not terminal
   for the job, only for that version.

### W4. Quote revision, withdrawal, expiry

- **Revise** (any time before acceptance; after acceptance only with `quote.revise_accepted` permission and it
  re-requires acceptance): editing a sent quote opens vN+1 as a *draft revision*; the customer still sees
  vN until vN+1 is sent. Sending vN+1 marks vN `superseded`; vN link redirects to vN+1 with "This quote was
  updated on …" banner. *Emits* `quote.quote.revised`, `quote.quote.sent`.
- **Withdraw**: office stops the offer → quote `withdrawn`, link shows withdrawn state; master status
  `cancelled` unless another action follows. *Emits* `quote.quote.withdrawn`; Agent 08 cancels reminders.
- **Expire**: system job at 00:05 business-local time (§10.10) moves `sent|viewed` quotes whose `expiresOn`
  (a local date, inclusive) has passed to `expired`; master status `expired`. *Emits* `quote.quote.expired`;
  *Notifies* office (digest) and optionally customer ("Your quote expired — request an update").
- **Extend expiry**: editing only the expiry date on a sent quote does **not** create a new version (it is
  metadata, not offer content) but is audit-logged and the customer link updates `[D]`. Reviving an expired
  quote = extend expiry (state `expired → sent`).
- **Office marks accepted on behalf** (phone/verbal acceptance): `AcceptQuote{actor:office, method:'verbal',
  note}` — acceptance evidence records the office user; same downstream as W2 step 2 `[D]`.

### W5. Create an appointment in the CRM (New → Appointment) `[S: t15, t19]`

1. New → Appointment; choose service(s) (or create new), **+** for extra services/add-ons `[S: t15]`.
2. Find/create customer, add job address `[S: t15]`.
3. Options & payment: discount, tax, gift card; payment timing; card on file `[S: t16]`.
4. Schedule: Agent 03 proposes first available time + worker (travel time, closest worker `[S: t19]`) or
   manual; past dates allowed `[S: llms]` (→ W14).
5. Assets, notes, tags, repeat `[S: t19]`.
6. **Book now** `[S: t15]`:
   - `CreateJob{origin:'crm_appointment'}` (if not yet saved) + `ScheduleJob` (Agent 03) in one transaction
     boundary via the booking saga (§10.3).
   - Requirements snapshot resolved from Agent 02 (`catalog.requirements.resolve`) → stored on job (§10.5).
   - Invoice auto-created in `draft` `[I — extends S: t04 to CRM bookings]`.
   - If payment timing = at booking and card on file → Agent 05 charges; 04 consumes `payments.payment.succeeded`
     → invoice `issued` + `paid` (§W12).
   - Master status `scheduled`. *Emits*: `job.job.created`, `invoice.invoice.created`, `job.job.status_changed`.
   - *Notifies* (via 08): assigned workers `job_assigned`; customer `job_booked_confirmation` and later
     reminders `[S: t15]` "notify your assigned team members, and send reminders directly to the customer".

### W6. Online booking from the booking portal (Agent 01) creates a job

1. Agent 01 portal collects services, add-ons, assets, booking answers, address, slot, payment method.
2. Portal calls `CreateJobFromBooking{bookingSessionId, idempotencyKey, ...}` (04) and Agent 03's slot
   confirmation inside the booking saga (§10.3).
3. Job created with `origin:'online_booking'`, `bookingSource` (Agent 01 source tracking `[S: t05]`), booking
   answers snapshot `[S: t05, t06]`, T&C acceptance evidence `[S: t05]`.
4. Invoice auto-created `draft`; prepayment/deposit/pre-auth per Settings → Payments (Agent 05) `[S: t04]`.
5. Master status `scheduled` (or `unscheduled` for request-only booking modes, if Agent 01 supports them) `[I]`.
6. Emits as W5. Agent 07 links/creates customer, assets; Agent 08 abandoned-booking flow is cancelled for
   that session.

### W7. Standalone invoice (New → Invoice) `[S: t04]`

1. New → Invoice. Editor in invoice mode; business info prefilled; customer; line items; due terms;
   notes/footer; payment options.
2. `CreateJob{origin:'crm_invoice', fieldWork:'none'}` — still a Job with a job number (single-ID model);
   no schedule, no requirements, workState `not_applicable` `[D]`.
3. Preview → **Send**: freeze InvoiceVersion v1, render, public link, invoice `draft → open (sent)`; master
   status `invoiced`. *Emits* `invoice.invoice.issued`, `invoice.invoice.sent`; *Notifies* `invoice_sent`; Agent 08
   schedules due/overdue reminders `[S: t04]`.
4. Optional: the office later adds a schedule to a standalone invoice (e.g. "parts invoice now, install
   next week") → job gains field work (`fieldWork:'required'`), master status follows field states while the
   invoice keeps its own state `[D]`.

### W8. Worker day: list, route start, on-my-way `[S: t02, t11, llms]`

1. Worker opens WA; local store already holds today's jobs (prefetched; §10.7). Clock-in prompt if
   timesheets enabled and setting on `[S: t03]` (Agent 09 owns timesheets — job flow only *prompts*).
2. Route day: worker swipes **Start route** `[S: t02]` → Agent 03 optimizes and emits
   `routing.route.started` + `routing.route.optimized {sequence[]}`; WA reorders list.
3. Worker taps **On my way** on a job:
   - WA computes ETA (device location + routing, or manual) and shows the sheet (§3.12).
   - Command `MarkEnRoute{jobId, etaAt, etaSource, location, sendToCustomer}`.
   - Guard: job `scheduled`, worker assigned (or foreman/crew), not already completed/cancelled; only one
     job per worker may be `en_route` at a time — starting en-route on job B while job A is en-route auto
     reverts A to `scheduled` with an audit note `[D]`.
   - Writes: `FieldVisit.enRouteAt`, `etaAt`; workState `en_route`; master status `en_route`.
   - *Emits* `job.job.en_route {jobId, workerId, etaAt, trackingSessionId}`.
   - *Notifies* `on_my_way` to notify contacts with ETA and live tracking link `[S: llms "on-my-way
     tracking", t11 "real-time updates on your arrival"]` — Agent 08 sends; tracking page served by Agent 01
     portal; location stream from WA via the tracking service (§10.6).
4. Location stream: every 15 s or 50 m (whichever first) while en route; ETA re-estimated server-side; if
   ETA slips > 10 min vs. the sent ETA, Agent 08 may send a "running late" update (setting) `[D]`.
5. Arrival: explicit **I've arrived** or geofence auto-arrive (radius setting) → workState `arrived`,
   `arrivedAt`; tracking link switches to "Your technician has arrived" and stops streaming `[D]`.

### W9. Start job, checklists, photos `[S: t02, t06]`

1. **Start job** (swipe) → `StartJob{jobId, startedAt(device), location}`; guard: `scheduled|en_route|arrived`
   (starting directly from scheduled is allowed: worker may be on site already). workState `in_progress`;
   master status `in_progress`; `startedAt` recorded. *Emits* `job.job.started`. Customer notification
   `job_started` optional (off by default) `[D]`. Location sharing stops.
2. Requirements view: before photos (N), checklist items and questions, after photos (N) `[S: t06]`.
3. **Before photos**: captured in app → photo pipeline (§10.12). Each photo: `stage:'before'`, optional
   `lineItemId` tag. *Emits* `job.photo.added` (on metadata sync) and `job.photo.uploaded` (bytes stored).
4. **Checklist & questions**: each answer `AnswerChecklistItem{jobId, itemId, value, answeredAt}` — appended
   to `ChecklistResponse` log (last write per item wins for display; full history kept). When all required
   items answered → `job.checklist.completed` (re-emitted if an answer is later cleared → `job.checklist.reopened`).
5. **Message the client** `[S: t02]`: opens the job-scoped conversation (Agent 08); messages carry `jobId`.
6. **Pause/Resume** (optional): `PauseJob{reason}` / `ResumeJob` → `paused` ↔ `in_progress`; paused time
   excluded from actual duration.

### W10. Upsell via POS before completion `[S: t07]`

1. Worker taps **Add items**; picks customer add-ons / company add-ons (and services if permitted) `[S: t07]`.
2. `AddLineItems{jobId, lines[], source:'pos_upsell', approvedByCustomer?}`:
   - Guard: job not completed (`[S: t07]` "before the job is marked complete"); after completion, only office
     can add lines (reopens invoice balance; §W13). Price edits require `wa.prices.edit`.
   - Writes: line items with `addedBy`, `source`, catalog snapshot; recompute totals; duration increases.
   - Inventory: product add-ons reserve stock (Agent 02 consumes `job.line.added`); decrement on completion.
   - If added duration overruns the worker's next job start → warning in WA + `job.job.duration_extended`
     for Agent 03 to flag the conflict to dispatch `[D]`.
   - If invoice already issued (e.g. prepaid at booking) → invoice revision vN+1 with new balance (§10.2).
   - *Emits* `job.line.added`, `job.job.totals_changed`.
3. Upsell attribution: `addedByWorkerId` used by Agent 08 reporting/leaderboard and Agent 09 commissions `[I]`.

### W11. Complete job `[S: t16, t02]`

1. Worker taps **Complete job**.
2. Completion gate (§10.4) evaluates requirements snapshot vs responses/photos (counting photos that are
   captured locally even if not yet uploaded — they're marked `pending_upload`, §10.12).
   - Block mode + unmet → cannot complete; Warn mode → "Complete anyway" with reason + permission.
3. Optional customer signature capture.
4. `CompleteJob{jobId, completedAt, location, signature?, overrideReason?}`:
   - workState `completed`; `completedAt`; actual duration computed.
   - If invoice is `draft` → it stays draft until payment/send (step 5) but its lines are **locked for workers**.
   - Master status → `completed` (or `paid` if already fully prepaid).
   - *Emits* `job.job.completed {jobId, lines, assets, workers, totals, balanceDue, photosShared[]}`.
   - Consumers: Agent 03 (route progress / auto-close route `[S: t02]`, next job), Agent 02 (inventory
     decrement), Agent 07 (asset last-service date `[S: llms]`, customer service history), Agent 08
     (completion message, review request `[S: llms "Google review booster"]`, leaderboard), Agent 06
     (subscription usage), Agent 09 (job time for pay/commission).
5. Payment hand-off `[S: t16]`: if balance > 0 and timing = after job → WA opens **POS** (Agent 05) with
   `{jobId, invoiceId, amountDue}`:
   - Card (new/on file/tap) or cash/check → Agent 05 records payment → `payments.payment.succeeded`
     → 04 issues invoice (if draft) + applies payment (§W12).
   - **Send invoice** `[S: t16]` → `IssueInvoice{jobId, send:true}` → invoice `open`, master `invoiced`.
   - Skip (permission) → job `completed`, invoice draft; office "Completed — awaiting payment" queue; optional
     auto-send after N hours (setting).
6. If the business uses "charge at booking"/card-on-file-after-job automatic charge: Agent 05 auto-charges on
   `job.job.completed` (if configured) — 04 does not charge.

### W12. Payment applied to invoice (any channel) `[S: t16, t17]`

1. Agent 05 emits `payments.payment.succeeded {paymentId, jobId, invoiceId?, amount, tip, method, allocations[]}`.
2. 04 handler (idempotent on `paymentId`):
   - If invoice `draft` → **issue** it first (freeze version; `issuedReason:'payment'`).
   - Apply allocation: `amountPaid += allocation` (tips are tracked separately and do not reduce balance;
     Agent 05 decides tip handling).
   - Recompute invoice state: `balance = 0 → paid`, `0 < paid < total → partially_paid`.
   - Recompute master status (§10.1).
   - *Emits* `invoice.invoice.payment_applied`, and `invoice.invoice.paid` when balance reaches 0.
   - Agent 08 sends receipt; cancels pending reminders on `invoice.invoice.paid`.
3. Overpayment (allocation > balance) → invoice `paid`, `creditDue = excess` flag → Agent 05 decides refund or
   customer credit; 04 shows "Overpaid by $X".

### W13. Post-completion edits, reopen, refunds

- **Office edits lines after completion**: allowed with `job.edit_completed`; creates invoice revision if
  issued; if invoice paid and new total > paid → state back to `partially_paid` and Agent 08 can send updated
  invoice; if new total < paid → `overpaid` flag → Agent 05 refund flow.
- If the invoice version was already **exported/locked** (accounting period close, Agent 05/08 export) →
  changes go to a **supplemental invoice** `#1042-2` (same job, same base number) rather than revising `[D]`.
- **Reopen job** (`job.reopen`): `completed → in_progress` (e.g. callback/rework same day); keeps original
  `completedAt` in history; emits `job.job.reopened`. Rework on another day = new linked job
  (`relatedJobId`, `relationType:'callback'`) `[D]`.
- **Refund** (Agent 05) → `payments.refund.succeeded` → 04 reduces `amountPaid` (net), invoice state →
  `partially_refunded`/`refunded` (§6.4); master status stays `paid` if net paid ≥ total, else reverts to
  `invoiced`/`partially_paid` as computed — except full refund of a cancelled job → `cancelled` with
  `refunded` payment pill.

### W14. Record a past job (backfill) `[S: llms "schedule or reschedule jobs in the past"]`

1. New → Appointment with a past date/time.
2. Prompt: "This time is in the past. Mark as completed?" `[D]` → **Yes**: job created with
   `workState:'completed'`, `completedAt = scheduledEnd`, requirements gate skipped (flag
   `backfilled:true`, audit reason), no customer notifications except optionally invoice/receipt.
   **No**: job stays `scheduled` in the past → appears in "Past due — not completed" queue.
3. Notifications for past-dated jobs are suppressed by default (no "your appointment is booked for
   yesterday") — contract with Agent 08: `job.job.created.payload.suppressCustomerNotifications = true`.

### W15. Cancel and no-show

- **Cancel (office)**: `CancelJob{reason, notifyCustomer, feePolicy}` from any pre-completion state; schedule
  released (Agent 03 consumes `job.job.cancelled`); invoice: draft → `void`; issued & unpaid → `void`;
  partially/fully paid → requires refund decision (Agent 05) before void, or keep as cancellation-fee invoice
  (lines replaced by a "Cancellation fee" line, revision) `[D]`.
- **Cancel (customer, portal)**: Agent 01 enforces cancellation policy and feedback + reschedule prompt
  `[S: t05]`; calls `CancelJob{actor:customer}` or Agent 03 reschedule instead.
- **No-show** (worker): available after `arrived` + wait time (setting) → `MarkNoShow{evidencePhotos[], note}` →
  master `no_show`; Agent 05 may charge no-show fee per policy; Agent 08 notifies customer + office;
  Agent 03 frees remaining slot time `[D]`.
- **Reschedule** is Agent 03's (`scheduling.job.rescheduled`); 04 updates snapshot; if the job was `en_route`
  it reverts to `scheduled` and the tracking link is invalidated `[D]`; quote/invoice unaffected (invoice
  service date updates on next render if not issued).

### W16. Recurring and subscription occurrences `[S: t14, t01]`

- Agent 03 owns the recurring series; Agent 06 owns subscriptions with auto-scheduling. Each occurrence is
  materialised (rolling horizon, Agent 03's call) by calling `CreateJob{origin:'recurring'|'subscription',
  seriesId|subscriptionId, occurrenceIndex, lines[], discounts[]}` → each occurrence is its **own job with its
  own number** linked by `seriesId` `[D]` (consistent with "show up in your calendar on every scheduled date"
  `[S: t14]`).
- "Commit across all future bookings" `[S: t14]`: Agent 03 issues `ApplySeriesChange{seriesId, fromDate,
  patch}` → 04 updates every future occurrence job in `scheduled` (or earlier) state that has **not been
  individually edited** (`detachedFromSeries=false`); individually edited occurrences get a conflict list for
  the office ("3 occurrences have custom changes — overwrite?") `[D]`.
- Subscription-covered lines: line `coveredBySubscriptionId`, price shown with "Included in plan" and a
  discount line making net 0 or the plan discount `[S: t01]` "20% off all services and one free add-on";
  invoice for a fully covered visit is auto-issued as `paid` with `$0` (no customer invoice email) `[D]`.

### W17. Lead → job `[S: t20]`

- Agent 07 lead conversion calls `CreateJob{origin:'lead_conversion', leadId, mode:'appointment'|'quote'}`;
  04 emits `job.job.created {leadId}`; Agent 07 moves lead to Won/Converted when the job reaches
  `scheduled` (appointment) or `approved` (quote accepted) `[D]`.

### W18. Estimator quote on site, customer books immediately `[S: t04]`

1. Estimator creates quote in WA (§3.15). Online → number allocated immediately; offline → local draft.
2. **Present on device**: customer reviews on the worker's phone, accepts terms, signs → `AcceptQuote
   {method:'in_person', deviceId, signature}` (queued if offline).
3. **Book now**: estimator picks a slot from availability (requires online; Agent 03); deposit via POS
   (Agent 05). Then same as W2 steps 5–6.
4. Or **Send**: SMS/email link (requires online) → W1 step 7 onward.

### W19. Crew / split jobs `[S: llms "split a job or its services between team members"]`

- Assignments (Agent 03) may be job-level (crew) or line-level (service X → worker A, service Y → worker B).
- Each assigned worker has their own `FieldVisit` record (en route/start/complete per worker) `[D]`; the
  job's workState is the **aggregate**: `en_route` if any worker en route and none started; `in_progress` if
  any started and not all assigned visits completed; `completed` when all required visits are complete, **or**
  when a foreman/lead completes the whole job (closing the others with `closedBy`).
- Requirements are evaluated per job (not per worker) unless a requirement is tagged to a line assigned to a
  specific worker; then that worker's completion is gated on it.
- On-my-way notifications: only the first worker's en-route triggers the customer ETA by default (setting:
  "lead worker only") to avoid duplicate texts `[D]`.

---

## 5. Business rules & math

### 5.1 Price snapshotting `[D]`

- When a catalog item is added to a job, the line stores a **snapshot**: name, description, unit price,
  cost, duration, taxable flag, tax category, item kind, catalog item id + catalog version. Later catalog
  changes (Agent 02 `catalog.service.updated`) **never** change existing job lines.
- A sent quote's prices are binding until expiry: booking from the quote uses the quote version's lines,
  not current catalog prices — even if the catalog price went up.
- Recurring/subscription occurrences take the series' line template (Agent 03/06) at materialisation time;
  a series price change applies only to occurrences materialised (or re-applied via "commit to future")
  afterwards.
- Office can "Refresh prices from catalog" on a draft job/quote (explicit action, shows diff).

### 5.2 Totals computation (order of operations)

Authority for tax order and tax rules is **Agent 05** (with zone tax rates from **Agent 02** `[S: t11]`). The
pipeline below is our proposed default; 04 implements it as a pure function `computeTotals(job)` shared by
CRM, WA (offline), portal and document rendering so all surfaces agree to the cent `[D]`.

All amounts integer cents (`int64`). Quantities are decimals with up to 3 fractional digits stored as
integer milli-units (`qtyMilli`). Percentages stored as basis points (`bps`, 1% = 100 bps).

```
for each line L (kind ∈ service, customer_addon, company_addon, custom_item, fee):
    L.gross = roundHalfUp(L.unitPriceCents * L.qtyMilli / 1000)

# 1. line-level discounts (recurring discount, subscription %, code tied to specific items)
for each line-level discount D on line L:
    D.amount = (D.type == 'percent') ? roundHalfUp(L.gross * D.bps / 10000) : min(D.fixedCents, L.gross_remaining)
    L.net = L.gross - Σ D.amount        # never below 0

# 2. order-level discounts (code on whole order, manual $ or % off)
eligibleBase = Σ L.net over lines eligible for that discount (Agent 06 decides eligibility)
orderDiscount = percent ? roundHalfUp(eligibleBase * bps / 10000) : min(fixedCents, eligibleBase)
allocate orderDiscount across eligible lines pro-rata by L.net using largest-remainder (§10.2.1)
L.netAfterOrder = L.net - L.allocatedOrderDiscount

# 3. fees (travel fee from zone [S: t11], other fees) are lines of kind 'fee'; discounts don't apply
#    to fees unless the discount explicitly includes fees.

# 4. tax
for each tax rate R applicable (zone/business rate; normally one rate):
    taxableBase_R = Σ L.netAfterOrder over lines with taxable && rate R
    tax_R = roundHalfUp(taxableBase_R * R.bps / 10000)       # invoice-level rounding per rate
allocate tax_R back to lines (largest remainder) for per-line reporting only

subtotal       = Σ L.gross (non-fee lines)
discountTotal  = Σ line discounts + orderDiscount
feeTotal       = Σ fee lines net
taxTotal       = Σ tax_R
total          = subtotal - discountTotal + feeTotal + taxTotal
```

Rules:
- `total ≥ 0` always; a discount can never make a line negative; excess discount is dropped (with UI
  warning "Discount exceeds eligible amount — capped at $X").
- Gift cards are **tenders** (payments), not discounts — they reduce balance, not total, and are not taxed
  around `[I; video t16 places gift card under "Options" next to discount/tax in the UI — verify]`.
- Tips are never part of `total`; tracked by Agent 05 on the payment.
- Rounding mode: half-up (away from zero for positives) on every multiplication; no intermediate floats.
- Tax-inclusive pricing (VAT/GST countries; fieldd operates in 37 countries `[S: llms]`) — if the business
  setting `pricesIncludeTax=true`: `tax_R = taxableBase_R - roundHalfUp(taxableBase_R * 10000 / (10000 + R.bps))`
  and `total = subtotal - discountTotal + feeTotal` (tax shown as "includes $X tax") `[D]`.
- **Deposit required** = per Agent 05 settings (fixed or % of total, rounded half-up), shown on quote and
  booking step; deposit is a payment against the same invoice (no separate deposit invoice number) `[D]`.
- **Balance due** = `total − Σ net allocations` (payments − refunds allocated to this invoice), excluding tips.
- **Cost & profit** `[S: t06, t07, t19]`: `costTotal = Σ roundHalfUp(L.unitCostCents × qty)`;
  `revenueNet = total − taxTotal`; `profit = revenueNet − costTotal`; `marginBps = profit × 10000 / revenueNet`
  (0 if revenueNet = 0). Payment processing fees (Agent 05) can optionally be included in cost for
  "true profit" `[D]`.
- **Duration**: `estimatedDuration = Σ service durations × qty (if qty-based) + Σ add-on durations`
  `[S: t06, t07]` "extra time will be added to the overall booking"; Agent 03 consumes it.

### 5.3 Job number rules `[D]` (concept `[S: t04, t08]`)

- Per business, monotonically increasing integer, default seed 1001, allocated atomically on first persist.
- Never reused, never changed after allocation. Discarded drafts keep their number (visible as "Discarded").
- Display: `#1042` (job), `Quote #1042` / `Q-1042`, `Invoice #1042` / `INV-1042` (prefixes configurable, the
  integer is shared). Supplemental invoices: `#1042-2`, `#1042-3`.
- Quote versions: `Quote #1042 (v2)` — version only shown when > 1.
- **Strict sequential invoice numbering** (optional setting, for tax jurisdictions requiring gapless invoice
  sequences): a separate `legalInvoiceNumber` allocated from a gapless sequence **at issuance** (not at draft
  creation), printed as "Invoice no. 2026-000123 (Job #1042)". Off by default; budget tier: available on all
  plans since it's compliance, not a premium feature.
- Offline-created jobs get a provisional `localRef` ("Draft · A7F2") until sync assigns the number. A document
  can never be sent with a provisional number.
- Search matches number with or without prefix/hash.

### 5.4 Quote rules

- Expiry: `expiresOn` local date (business tz), inclusive; default today + 30 days `[D]`; quote valid until
  23:59:59 local on that date. Accept attempts after that → `QUOTE_EXPIRED`.
- Acceptance is bound to a **specific version** and records the **terms hash**; changing terms requires a new
  version.
- Only the latest sent version can be accepted. Draft revisions are invisible to the customer.
- A quote with zero line items or total = 0 can be sent only if the business allows "$0 quotes" (default
  allowed — some businesses quote "free inspection") `[D]`.
- Book-online from a quote books the **quote's lines and duration**; customer cannot change services in the
  booking step except choosing offered optional lines `[D]`.
- Preferred worker on a quote `[S: t04]` is a *preference*: Agent 03 uses it as a hard filter if "restrict to
  preferred worker" is on, otherwise as a ranking boost.
- Accepted quote → the job's lines become the "contract" baseline; later changes are tracked as deltas
  (shown on the invoice as "Changes since quote: +$45 Pet hair removal") `[D — better than]`.

### 5.5 Invoice rules

- **Auto-generation**: an Invoice record is created (state `draft`) when the job first becomes `scheduled`
  `[S: t04]`, or at creation for standalone invoices `[S: t04]`. Exactly one *primary* invoice per job.
- While `draft`, the invoice is a **live view** of job lines (no snapshot). It is **issued** (snapshot frozen as
  InvoiceVersion v1) on the first of: sent to customer; any payment applied (incl. deposits at booking);
  job completed with auto-send; manual "Mark as issued".
- After issuance, line changes produce a new InvoiceVersion (vN+1); customer link always shows latest with a
  "Revised" note; payments carry over.
- **Due date**: `dueOn = issueDate + terms` (local date). Due on receipt → `dueOn = issueDate`.
- **Overdue**: computed, not stored as a primary state — `isOverdue = state ∈ {open, viewed, partially_paid}
  && today(businessTz) > dueOn`. The `overdue` flag transition emits `invoice.invoice.overdue` once (system sweep).
- **Partial payments**: unlimited; each payment allocated to this invoice by Agent 05; online partial payments
  only if setting on; minimum partial amount $1 or setting.
- **Void**: allowed when `netPaid = 0`; otherwise refund first (Agent 05) or convert to credit. Void invoice is
  kept (never deleted), watermarked "VOID".
- **Uncollectible / write-off** `[D]`: `MarkUncollectible{reason}` → state `written_off`, balance moved to bad
  debt reporting; can be reversed by a later payment.
- Invoices for **$0** (fully covered by subscription/gift/discount) are auto-issued and marked `paid` without
  sending unless the business opts in.
- Currency: single business currency (ISO 4217); multi-currency out of scope v1.

### 5.6 Field workflow rules

- Workers see only jobs where they are assigned (job- or line-level), plus crew jobs if foreman, plus all
  jobs if `wa.jobs.view_all` (Agent 09).
- One active `en_route` job per worker (§W8 step 3); multiple `in_progress` jobs per worker are allowed only
  if setting "allow concurrent jobs" (e.g. a shop worker with two cars) `[D]`; default off → starting a second
  job prompts "Pause #1041?".
- `StartJob` allowed from `scheduled`, `en_route`, `arrived`, `paused`(=resume) only; not before
  `scheduledStart − earlyStartWindow` (default 12 h, guards accidental taps on next week's job) unless confirmed.
- **Requirements** = union of global requirements (settings) and service-specific requirements for every
  service/add-on line on the job `[S: t06]`; photo counts per stage = `max(global, max over lines of
  service requirement)` by default; a service rule may be marked `additive` → summed `[D]` (see Q in §13).
- Requirement snapshot is taken at booking; adding a service later (upsell) **adds** its requirements; removing a
  service removes requirements not yet answered (answered ones are kept as history).
- Completion gate counts photos with local status ≥ `captured` (§10.12) — workers are never blocked by bad
  signal — but the job carries `evidencePendingUpload=true` until all bytes are on the server, and the office
  sees "Photos uploading (3)".
- **Actual duration** = `completedAt − startedAt − Σ pauses`. Displayed to office; feeds Agent 03 duration
  learning and Agent 09 performance.
- Job time ≠ timesheet time: the job flow can *prompt* clock-in, never clocks in automatically `[S: t03]`
  (timesheets are permissioned; workers may not have them).

### 5.7 Editing locks matrix `[D]`

| Job phase | Lines (office) | Lines (worker) | Customer | Address | Schedule (via 03) | Delete |
|---|---|---|---|---|---|---|
| draft | ✔ | ✔ (own drafts) | ✔ | ✔ | ✔ | ✔ (discard) |
| quoted (sent) | ✔ → new version | ✗ | ✔ → new version | ✔ → new version | n/a | ✗ (withdraw) |
| approved / scheduled | ✔ | ✔ add only (upsell perm.) | ✔ (warn: invoice) | ✔ (re-zone, re-price fees, warn) | ✔ | ✗ (cancel) |
| en_route / in_progress | ✔ (notify worker) | ✔ add/edit qty (perm.) | ✗ | ✗ (warn hard) | ✔ (warn) | ✗ |
| completed (unpaid) | ✔ `job.edit_completed` | ✗ | ✗ | ✗ | ✔ (date correction) | ✗ |
| paid | ✔ `job.edit_paid` → revision/supplemental | ✗ | ✗ | ✗ | date correction only | ✗ |
| cancelled / no_show | fee line only | ✗ | ✗ | ✗ | "Rebook" creates new job | ✗ |

### 5.8 Send guards (quote and invoice)

- Customer has at least one deliverable channel among selected recipients (email valid & not hard-bounced,
  or mobile with SMS consent) — otherwise "Copy link" only.
- At least one line item (unless $0 allowed).
- Job number allocated (online).
- Total ≥ 0; all lines have names; no line with qty 0.
- For quotes: expiry ≥ today; terms present if "require terms" setting.
- For invoices: if job has field work and is not completed, warn "Invoicing before completion" (allowed,
  common for prepayment) `[D]`.
- Rate limit: max 10 sends per document per hour (anti-spam).

### 5.9 Timezone & DST rules

- Every business has an IANA timezone; jobs store instants in UTC plus `jobTz` (the job location's tz — equals
  the business tz unless a multi-region business; derived from geocode) `[D]`.
- Date-only concepts (quote `expiresOn`, invoice `dueOn`, route "anytime Thursday" day `[S: t02]`) are stored as
  local dates `YYYY-MM-DD` + tz, never as midnight UTC instants.
- Sweeps (expiry, overdue) run per business at local 00:05, computed with the tz database (DST-safe; on
  spring-forward days 00:05 always exists; on fall-back, run once — dedupe by `(businessId, localDate)`).
- Worker app displays times in the job's tz with a tz badge if the device tz differs.

### 5.10 Money rounding & invariants (checked on every write; violation = reject + alert)

- `total = subtotal − discountTotal + feeTotal + taxTotal` (exclusive tax).
- `Σ line allocations of order discount = orderDiscount`; `Σ line tax allocations = taxTotal`.
- `amountPaid = Σ allocations(payments) − Σ allocations(refunds)` from Agent 05's ledger, never edited directly.
- `balanceDue = max(total − amountPaid, 0)`; `creditDue = max(amountPaid − total, 0)`.
- No negative line except kind `discount`/`adjustment`; adjustments require `job.price.adjust`.


### 5.11 Document branding rules (owner requirement 2026-11-13; Agent 11 owns the brand system)

- Quotes, invoices, receipts and their emails/SMS link pages are **tenant-branded surfaces**. 04 never
  hard-codes colors, fonts, radii or logos; the renderer takes a resolved `BrandTokens` object from Agent 11
  (`brand.tokens.resolve{businessId, surface:'document'|'document_email'|'document_page', mode}`) `[D]`.
- Placements used by 04: `logo.invoice` (PDF/print header), `logo.email` (email header), `logo.header` /
  `logo.mobileHeader` (public quote/invoice page), each with the business's size controls; light and dark
  variants — **PDF/print always renders the light palette and light logo** (paper is white; dark PDFs waste
  toner and fail accessibility in print); public pages follow the viewer's light/dark preference `[D]`.
- Custom uploaded fonts are embedded (subset to used glyphs) in PDFs; if a font file is missing/corrupt or
  lacks a glyph (e.g. customer name in another script), fall back per glyph to the system font stack and log a
  brand warning — a document must **never** fail to render because of branding.
- Copy overrides (Agent 11 text overrides) apply to every customer-facing label on documents: "Quote" →
  "Estimate", "Invoice" → "Bill", "Book online" → "Choose your time", "Balance due", "Terms" etc. Overrides
  are captured in the **document version snapshot** (style version id) so a re-download of an old version
  looks exactly as sent.
- Contrast guard: if a business palette yields text/background contrast < 4.5:1 on documents, the renderer
  substitutes the nearest compliant shade for text (not for brand blocks) and flags it in Settings preview.
- Every `QuoteVersion`/`InvoiceVersion` stores `brandVersionId`; brand changes never mutate past renderings
  (a "Re-render with current branding" action creates a new rendering, not a new version).

---

## 6. State machines

Design principle `[D]`: the job has **orthogonal sub-states** (quote, schedule, field work, invoice,
payment); the **master status** shown everywhere (jobs list filter `[S: t08]`, job card pill, worker app) is
**derived** from them by a precedence function (§10.1) and stored denormalised for indexing. Commands
validate against the relevant sub-machine, never against the master status alone. The **payment status**
pill (jobs list column `[S: t08]`) is a projection of Agent 05's `payments.job.balance_changed`.

### 6.1 Master job status (derived)

| Master status | Meaning (label shown) | Derived when (first matching row wins) |
|---|---|---|
| `cancelled` | Cancelled | job.cancelledAt set |
| `no_show` | No-show | workState = `no_show` |
| `paid` | Paid | (fieldWork = none OR workState = completed) AND invoice ∈ {paid} (or total 0) |
| `partially_paid` | Partially paid | (fieldWork none OR completed) AND invoice = partially_paid |
| `invoiced` | Invoiced / Awaiting payment | (fieldWork none OR completed) AND invoice ∈ {open, viewed} (overdue = badge) |
| `completed` | Completed — not invoiced | workState = completed AND invoice = draft |
| `paused` | Paused | workState = paused |
| `in_progress` | In progress | workState = in_progress |
| `arrived` | Arrived | workState = arrived |
| `en_route` | On the way | workState = en_route |
| `scheduled` | Scheduled (badge "Unassigned" if no worker) | scheduleState = scheduled |
| `approved` | Accepted — needs scheduling | quote = accepted AND scheduleState = unscheduled |
| `unscheduled` | Needs scheduling | fieldWork = required AND scheduleState = unscheduled AND no open quote |
| `quoted` | Quote sent | quote ∈ {sent, viewed} |
| `declined` | Quote declined | quote = declined (and nothing later) |
| `expired` | Quote expired | quote = expired |
| `written_off` | Written off | invoice = written_off |
| `draft` | Draft | otherwise |

Flags orthogonal to status (badges/filters): `overdue`, `unassigned`, `requirementsIncomplete`,
`evidencePendingUpload`, `unsyncedFieldData`, `backfilled`, `disputed` (Agent 05), `creditDue`, `archived`.

### 6.2 Quote sub-machine (per job; versions inside)

| State | Event / command | Guard | Next | Side effects |
|---|---|---|---|---|
| `none` | CreateJob(mode quote) / AddQuote | job not cancelled | `draft` | — |
| `draft` | SendQuote | §5.8 guards | `sent` | freeze vN, render, public link, `quote.quote.sent` |
| `sent` | first public view | version = latest | `viewed` | `quote.quote.viewed` |
| `sent`/`viewed` | ReviseQuote (edit) | — | same (draft revision exists) | revision draft vN+1 (invisible to customer) |
| `sent`/`viewed` | SendQuote(vN+1) | guards | `sent` | vN `superseded`, `quote.quote.revised` + `.sent` |
| `sent`/`viewed` | AcceptQuote | latest version, not expired, terms accepted | `accepted` | acceptance record, optional lines resolved, `quote.quote.accepted` |
| `sent`/`viewed` | DeclineQuote | latest version | `declined` | `quote.quote.declined`, reminders cancelled |
| `sent`/`viewed` | ExpirySweep | today > expiresOn | `expired` | `quote.quote.expired` |
| `sent`/`viewed`/`expired` | ExtendExpiry | new date ≥ today | `sent` (or keep viewed) | audit; `quote.quote.expiry_changed` |
| `sent`/`viewed`/`draft` | WithdrawQuote | perm `quote.withdraw` | `withdrawn` | link shows withdrawn, `quote.quote.withdrawn` |
| `declined`/`expired`/`withdrawn` | ReviseQuote + Send | — | `sent` | new version |
| `accepted` | `scheduling.job.scheduled` | — | `booked` | invoice auto-created `[S: t04]` |
| `accepted` | ReviseQuote | perm `quote.revise_accepted` | `sent` (re-acceptance) | old acceptance kept as history |
| `accepted`/`booked` | CancelJob | — | unchanged (job cancelled) | — |

Version sub-states: `draft → sent → superseded` or `sent → accepted|declined|expired|withdrawn`.

### 6.3 Field-work sub-machine (per job aggregate; per-worker `FieldVisit` uses the same table)

| State | Event / command | Guard | Next | Side effects |
|---|---|---|---|---|
| `not_applicable` | AddSchedule (standalone invoice gets work) | — | `not_started` | fieldWork = required |
| `not_started` | MarkEnRoute | assigned/crew; scheduled; not cancelled | `en_route` | ETA, tracking session, `job.job.en_route` (08: N-D01) |
| `en_route` | MarkArrived / geofence | — | `arrived` | stop tracking stream, `job.visit.arrived` |
| `en_route` | CancelEnRoute / en-route on other job / reschedule | — | `not_started` | invalidate tracking link, `job.visit.en_route_cancelled` |
| `not_started`/`en_route`/`arrived` | StartJob | within early-start window or confirmed; not cancelled | `in_progress` | startedAt, `job.job.started` |
| `in_progress` | PauseJob | — | `paused` | pause interval opened, `job.visit.paused` |
| `paused` | ResumeJob | — | `in_progress` | pause closed, `job.visit.resumed` |
| `in_progress`/`paused` | CompleteJob | completion gate passes or override w/ reason+perm | `completed` | completedAt, inventory, `job.job.completed` |
| `arrived` (+wait time) | MarkNoShow | evidence per setting | `no_show` | `job.job.no_show`, fee via 05 |
| `completed` | ReopenJob | perm `job.reopen`; invoice not written off | `in_progress` | `job.job.reopened` |
| `not_started` | RecordPastJob / backfill complete | scheduled end < now; perm | `completed` | backfilled flag |
| any non-terminal | CancelJob | perm | `cancelled` | schedule released (03), invoice void/fee |
| `no_show` | RevertNoShow | perm, within 24 h | `not_started` | fee reversal request to 05 |

### 6.4 Invoice sub-machine

| State | Event / command | Guard | Next | Side effects |
|---|---|---|---|---|
| `none` | job becomes scheduled / standalone create | — | `draft` | `invoice.invoice.created` |
| `draft` | SendInvoice / IssueInvoice / first payment / auto-send | lines ≥ 1 or $0 allowed | `open` | freeze v1, dueOn, `invoice.invoice.issued` (+ `.sent` if sent) |
| `open` | first public view | — | `viewed` | `invoice.invoice.viewed` |
| `open`/`viewed`/`partially_paid` | payment allocation | balance after > 0 | `partially_paid` | `invoice.invoice.payment_applied` |
| `open`/`viewed`/`partially_paid` | payment allocation | balance after = 0 | `paid` | `invoice.invoice.paid` (08 receipt, cancel reminders) |
| `paid`/`partially_paid` | refund allocation | net paid > 0 | `partially_refunded` (if paid before) / recompute | `invoice.invoice.refund_applied` |
| `paid`/`partially_paid` | refund allocation | net paid = 0 | `refunded` | — |
| `paid` | line edit increases total | perm `job.edit_paid`, not export-locked | `partially_paid` | revision vN+1, `invoice.invoice.revised` |
| any issued | line edit | export-locked | unchanged | supplemental invoice `#N-2` created in `draft` |
| `draft`/`open`/`viewed` | VoidInvoice | netPaid = 0 | `void` | `invoice.invoice.voided`, reminders cancelled |
| `open`/`viewed`/`partially_paid` | MarkUncollectible | perm | `written_off` | `invoice.invoice.written_off` |
| `written_off` | payment allocation | — | `partially_paid`/`paid` | recovery recorded |
| (derived) | overdue sweep | today > dueOn, balance > 0 | flag `overdue` | `invoice.invoice.overdue` once per dueOn |

`refunded`, `void` are terminal for that invoice; a cancelled-then-rebooked job uses **"Rebook"** which creates a
new job (new number, `relatedJobId`) — we never resurrect a void invoice.

### 6.5 Schedule sub-state (projection of Agent 03's JobSchedule)

| 03 event | 04 effect |
|---|---|
| `scheduling.job.scheduled` | scheduleState `scheduled`; snapshot start/end/localDate/tz/workers; create invoice draft if none; quote accepted→booked |
| `scheduling.job.rescheduled` | update snapshot; if workState `en_route` → back to `not_started` (tracking link invalidated) |
| `scheduling.job.unscheduled` | scheduleState `unscheduled`; if `en_route` → `not_started` |
| `scheduling.assignment.created/removed/changed` | update `assignedWorkerIds`/line assignment snapshot; WA job lists refresh; a worker removed mid-job keeps their recorded visit data (§11) |
| `routing.route.started` / `routing.route.optimized` | `routeSequence` snapshot for WA ordering |

### 6.6 Photo upload state (per photo; §10.12)

`captured → queued → uploading → uploaded → verified → processed` with `failed_retryable` (→ queued after backoff)
and `failed_permanent` (corrupt/unsupported; user notified, local file retained for manual export). `deleted`
is a soft state requiring permission once `verified`.

### 6.7 Offline command state (device outbox)

`pending → sending → acked` | `rejected_conflict` (server returned conflict outcome; data preserved, user
informed) | `rejected_invalid` (bug/validation; preserved for support export) | `superseded` (a later command
made it moot, e.g. MarkEnRoute after job already started — dropped silently but logged).

---

## 7. Data model

Stack-agnostic TypeScript-style interfaces. `Id` = opaque string (ULID/UUIDv7). `Cents` = integer (int64).
`LocalDate` = `YYYY-MM-DD`. `Instant` = UTC ISO-8601. Every entity has `businessId` (tenant key) and
`createdAt/updatedAt`; all queries are tenant-scoped.

```ts
// ---------- Aggregate root ----------
interface Job {
  id: Id;
  businessId: Id;
  number: number | null;              // null only while offline/provisional
  localRef?: string;                  // provisional ref from a device ("Draft · A7F2")
  version: number;                    // optimistic concurrency, +1 per committed change
  origin: 'crm_appointment' | 'crm_quote' | 'crm_invoice' | 'crm_recurring' | 'online_booking'
        | 'quote_booking' | 'lead_conversion' | 'recurring' | 'subscription' | 'worker_app'
        | 'phone_booking' | 'api' | 'mcp' | 'ai_assistant' | 'import' | 'rebook' | 'callback';
  bookingSource?: string;             // Agent 01 source tracking [S: t05]
  fieldWork: 'required' | 'none';     // 'none' for standalone invoices
  masterStatus: MasterStatus;         // derived, stored for indexing (§10.1)
  flags: JobFlag[];                   // overdue, unassigned, requirementsIncomplete, ...
  customerId: Id;                     // Agent 07
  billToContactId?: Id;               // Agent 07 contact/person
  notifyContacts: NotifyContact[];    // [S: t04]
  address: JobAddressSnapshot | null; // null for standalone invoices / shop jobs use shopId
  shopId?: Id;                        // Agent 02 shop [S: t11]
  zoneId?: Id;                        // Agent 02 zone resolved at save
  timezone: string;                   // IANA
  assetIds: Id[];                     // Agent 07 assets [S: t04, t19]
  assetSnapshots: AssetSnapshot[];    // what was printed on documents
  preferredWorkerId?: Id;             // [S: t04]
  schedule: JobScheduleSnapshot | null;  // projection of Agent 03 JobSchedule
  scheduleState: 'unscheduled' | 'scheduled';
  workState: WorkState;
  quoteState: QuoteState;
  invoiceState: InvoiceState;
  paymentStatus: PaymentStatusProjection; // from Agent 05
  lines: JobLineItem[];
  discounts: JobDiscountRef[];        // Agent 06 refs + manual discounts
  taxMode: 'apply' | 'exempt';        // "apply tax" toggle [S: t16, t19]
  priceBreakdownVersion?: number;     // Agent 05 PriceBreakdown version (authoritative totals)
  totals: JobTotals;                  // cached copy of current breakdown
  paymentTiming: 'after_job' | 'at_booking';   // [S: t16]
  cardOnFileId?: Id;                  // Agent 05 stored method ref
  customerNote?: string;              // visible on documents
  internalNote?: string;              // never customer-visible
  tagIds: Id[];                       // [S: t08, t19]
  bookingAnswers: BookingAnswer[];    // [S: t05, t06] snapshot
  termsAcceptance?: TermsAcceptance;  // online booking T&C [S: t05]
  requirements: RequirementsSnapshot | null;  // §10.5
  seriesId?: Id; occurrenceIndex?: number; detachedFromSeries?: boolean; // Agent 03 [S: t14]
  subscriptionId?: Id;                // Agent 06 [S: t01]
  routeId?: Id; routeSequence?: number; // Agent 03 [S: t02]
  leadId?: Id;                        // Agent 07 [S: t20]
  relatedJobId?: Id; relationType?: 'rebook' | 'callback' | 'split_from';
  conversationId?: Id;                // Agent 08 thread [S: t02]
  cancelledAt?: Instant; cancelReason?: string; cancelledBy?: ActorRef;
  completedAt?: Instant; startedAt?: Instant; actualDurationMin?: number;
  backfilled: boolean;                // [S: llms] past jobs
  discardedAt?: Instant;              // draft discarded (number retained)
  archivedAt?: Instant;
  createdBy: ActorRef;
}

type MasterStatus = 'draft' | 'quoted' | 'approved' | 'declined' | 'expired' | 'unscheduled' | 'scheduled'
  | 'en_route' | 'arrived' | 'in_progress' | 'paused' | 'completed' | 'invoiced' | 'partially_paid'
  | 'paid' | 'written_off' | 'cancelled' | 'no_show';
type WorkState = 'not_applicable' | 'not_started' | 'en_route' | 'arrived' | 'in_progress' | 'paused'
  | 'completed' | 'no_show' | 'cancelled';
type QuoteState = 'none' | 'draft' | 'sent' | 'viewed' | 'accepted' | 'booked' | 'declined' | 'expired' | 'withdrawn';
type InvoiceState = 'none' | 'draft' | 'open' | 'viewed' | 'partially_paid' | 'paid' | 'partially_refunded'
  | 'refunded' | 'void' | 'written_off';
type JobFlag = 'overdue' | 'unassigned' | 'requirementsIncomplete' | 'evidencePendingUpload'
  | 'unsyncedFieldData' | 'backfilled' | 'disputed' | 'creditDue' | 'outOfArea' | 'priceChangedSinceQuote';

interface ActorRef { kind: 'user' | 'worker' | 'customer' | 'system' | 'api_key' | 'mcp' | 'ai';
  id?: Id; deviceId?: Id; surface: 'crm_web' | 'crm_desktop' | 'crm_ios' | 'worker_app' | 'portal' | 'system' | 'api'; }

interface NotifyContact { contactId: Id; email: boolean; sms: boolean; push: boolean; role?: 'primary' | 'billing' | 'site' | 'other'; }

interface JobAddressSnapshot { addressId?: Id; line1: string; line2?: string; city: string; region?: string;
  postalCode?: string; country: string; lat: number; lng: number; accessNotes?: string; geocodeQuality: 'rooftop' | 'interpolated' | 'approximate'; }

interface AssetSnapshot { assetId: Id; kind: 'vehicle' | 'property' | 'equipment' | 'other'; label: string;
  details: Record<string, string>; photoUrl?: string; }   // e.g. make/model/year/color/plate [S: llms]

interface JobScheduleSnapshot {        // copy of Agent 03 data; never edited by 04
  start?: Instant; end?: Instant; localDate?: LocalDate; window?: 'exact' | 'arrival_window' | 'day_only';
  assignedWorkerIds: Id[]; leadWorkerId?: Id; lineAssignments: { lineId: Id; workerIds: Id[] }[];
  scheduleVersion: number; }

// ---------- Lines ----------
interface JobLineItem {
  id: Id;                             // stable; shared with Agent 05 breakdown line_id
  kind: 'service' | 'customer_addon' | 'company_addon' | 'custom_item' | 'fee' | 'discount' | 'adjustment';
  catalogItemId?: Id; catalogVersion?: number;   // Agent 02
  parentLineId?: Id;                  // add-on attached to service
  name: string; description?: string;
  qtyMilli: number;                   // 1000 = qty 1
  unitPriceCents: Cents; unitCostCents?: Cents;  // cost [S: t06, t07, t19]
  durationMin?: number;               // [S: t06, t07]
  taxable: boolean; taxCategory?: string;
  sku?: string; inventoryTracked?: boolean;     // product add-ons [S: t07]
  optional: boolean; optionalState?: 'offered' | 'selected' | 'declined';  // quote optional lines [D]
  coveredBySubscriptionId?: Id;
  source: 'quote' | 'booking' | 'crm' | 'pos_upsell' | 'series' | 'subscription' | 'zone_fee' | 'api';
  addedBy: ActorRef; addedAt: Instant;
  assignedWorkerIds?: Id[];           // split services [S: llms]
  sortOrder: number;
  removedAt?: Instant;                // soft remove (history); excluded from totals
}

interface JobDiscountRef { id: Id; kind: 'code' | 'promo' | 'manual' | 'recurring' | 'subscription';
  discountId?: Id; code?: string; type: 'percent' | 'fixed'; bps?: number; fixedCents?: Cents;
  scope: 'order' | 'lines'; lineIds?: Id[]; appliedBy: ActorRef; }

interface JobTotals { subtotal: Cents; discountTotal: Cents; feeTotal: Cents; taxTotal: Cents; total: Cents;
  depositRequired: Cents; amountPaid: Cents; balanceDue: Cents; creditDue: Cents; tipTotal: Cents;
  costTotal?: Cents; profit?: Cents; marginBps?: number; estimatedDurationMin: number; currency: string; }

interface PaymentStatusProjection { status: 'unpaid' | 'card_on_file' | 'authorized' | 'deposit_paid'
  | 'partially_paid' | 'paid' | 'invoice_sent' | 'refunded' | 'partially_refunded' | 'disputed' | 'failed';
  balanceDue: Cents; asOfLedgerSeq: number; }        // from payments.job.balance_changed

// ---------- Quote ----------
interface Quote {                     // 1:1 with Job (absent if never quoted)
  jobId: Id; state: QuoteState; currentVersionId?: Id; latestSentVersionId?: Id;
  expiresOn?: LocalDate; bookOnline: boolean;
  bookOnlineOptions?: { restrictToPreferredWorker: boolean; earliestDate?: LocalDate; requireDeposit: boolean };
  termsTemplateId?: Id; termsText: string; messageToCustomer?: string; requireSignature: boolean;
  viewCount: number; firstViewedAt?: Instant; lastViewedAt?: Instant;
  acceptance?: QuoteAcceptance; decline?: { reason: string; text?: string; at: Instant; versionId: Id };
}
interface QuoteVersion { id: Id; jobId: Id; versionNo: number; state: 'draft' | 'sent' | 'superseded'
  | 'accepted' | 'declined' | 'expired' | 'withdrawn';
  linesSnapshot: JobLineItem[]; totalsSnapshot: JobTotals; termsText: string; termsHash: string;
  customerSnapshot: object; addressSnapshot?: JobAddressSnapshot; assetSnapshots: AssetSnapshot[];
  expiresOn: LocalDate; brandVersionId: Id; renderingIds: Id[]; sentAt?: Instant; sentBy?: ActorRef;
  recipients: Recipient[]; }
interface QuoteAcceptance { versionId: Id; at: Instant; method: 'online' | 'in_person' | 'verbal' | 'api';
  acceptorName: string; signatureFileId?: Id; ip?: string; userAgent?: string; deviceId?: Id;
  termsHash: string; selectedOptionalLineIds: Id[]; recordedBy: ActorRef; }

// ---------- Invoice ----------
interface Invoice { id: Id; jobId: Id; kind: 'primary' | 'supplemental'; suffix?: number;  // #1042-2
  legalNumber?: string;               // strict sequential numbering option
  state: InvoiceState; currentVersionId?: Id; issuedAt?: Instant; issuedReason?: 'sent' | 'payment' | 'manual' | 'completion_auto';
  dueTerms: 'on_receipt' | 'net_7' | 'net_15' | 'net_30' | 'custom'; dueOn?: LocalDate;
  allowOnlinePayment: boolean; allowPartialOnline: boolean; footerText?: string;
  amountPaid: Cents; balanceDue: Cents; overdueSince?: LocalDate; exportLockedAt?: Instant;
  viewCount: number; voidedAt?: Instant; voidReason?: string; writtenOffAt?: Instant; }
interface InvoiceVersion { id: Id; invoiceId: Id; versionNo: number; linesSnapshot: JobLineItem[];
  totalsSnapshot: JobTotals; priceBreakdownVersion: number; brandVersionId: Id; renderingIds: Id[];
  createdAt: Instant; createdBy: ActorRef; reason: 'issue' | 'revision' | 'cancellation_fee'; }

// ---------- Documents ----------
interface DocumentRendering { id: Id; jobId: Id; docType: 'quote' | 'invoice' | 'receipt' | 'work_summary';
  versionId: Id; format: 'html' | 'pdf'; storageKey: string; sha256: string; bytes: number;
  locale: string; brandVersionId: Id; renderedAt: Instant; }
interface PublicLink { token: string /* ≥128-bit random, url-safe */; jobId: Id; docType: 'quote' | 'invoice' | 'tracking';
  versionPolicy: 'latest'; createdAt: Instant; expiresAt?: Instant; revokedAt?: Instant; lastUsedAt?: Instant;
  scopes: ('view' | 'accept' | 'decline' | 'pay' | 'book' | 'message')[]; }
interface Recipient { contactId?: Id; email?: string; phone?: string; channels: ('email' | 'sms')[]; messageIds: Id[]; }

// ---------- Field execution ----------
interface FieldVisit { id: Id; jobId: Id; workerId: Id; state: WorkState;
  enRouteAt?: Instant; etaAt?: Instant; etaSource?: 'gps_route' | 'manual'; trackingSessionId?: Id;
  arrivedAt?: Instant; arrivalMethod?: 'manual' | 'geofence';
  startedAt?: Instant; completedAt?: Instant; pauses: { from: Instant; to?: Instant; reason: string }[];
  startLocation?: GeoPoint; completeLocation?: GeoPoint; deviceId?: Id; closedBy?: ActorRef; }
interface GeoPoint { lat: number; lng: number; accuracyM: number; at: Instant; }

interface RequirementsSnapshot { resolvedAt: Instant; catalogVersions: Record<Id, number>;
  photoRules: { stage: 'before' | 'after'; minCount: number; sourceLineIds: Id[]; ruleIds: Id[] }[];
  items: RequirementItem[]; signatureRequired: boolean; }
interface RequirementItem { id: Id; templateItemId: Id; source: 'global' | 'service'; lineId?: Id;
  type: 'checkbox' | 'yes_no' | 'text' | 'number' | 'single_select' | 'multi_select' | 'date' | 'photo';
  label: string; options?: string[]; required: boolean; allowNA: boolean; minPhotos?: number;
  assignedWorkerIds?: Id[]; order: number; }   // item types defined by Agent 02
interface ChecklistResponse { id: Id /* client-generated */; jobId: Id; itemId: Id; value: unknown;
  na?: boolean; photoIds?: Id[]; answeredBy: ActorRef; deviceTime: Instant; serverTime: Instant; supersedes?: Id; }

interface JobPhoto { id: Id /* client-generated */; jobId: Id; stage: 'before' | 'after' | 'other' | 'no_show_evidence' | 'quote';
  lineItemId?: Id; checklistItemId?: Id; takenBy: ActorRef; capturedAt: Instant; capturedGeo?: GeoPoint;
  fromGallery: boolean; sha256: string; bytes: number; width: number; height: number; mime: string;
  uploadState: 'pending_upload' | 'uploaded' | 'verified' | 'processed' | 'failed_permanent';
  storageKey?: string; thumbKeys?: Record<'sm' | 'md' | 'lg', string>;
  sharedWithCustomer: boolean; caption?: string; deletedAt?: Instant; }

interface JobNote { id: Id; jobId: Id; visibility: 'internal' | 'worker' | 'customer'; body: string;
  author: ActorRef; createdAt: Instant; editedAt?: Instant; }   // append-mostly
interface JobFile { id: Id; jobId: Id; name: string; mime: string; bytes: number; sha256: string;
  storageKey: string; visibleToCustomer: boolean; visibleToWorkers: boolean; uploadedBy: ActorRef; }
interface CustomerSignature { id: Id; jobId: Id; purpose: 'quote_acceptance' | 'completion' | 'upsell_approval';
  signerName: string; fileId: Id; at: Instant; deviceId?: Id; }

// ---------- Audit & sync ----------
interface JobAuditEntry { seq: number /* per job, gapless */; jobId: Id; at: Instant; actor: ActorRef;
  command: string; commandId?: Id; changes: { path: string; before: unknown; after: unknown }[];
  reason?: string; eventIds: Id[]; }
interface DeviceCommand { commandId: Id; deviceId: Id; workerId: Id; jobId?: Id; type: string;
  baseVersion?: number; payload: unknown; deviceTime: Instant; deviceSeq: number;
  receivedAt?: Instant; outcome?: 'applied' | 'duplicate' | 'conflict_preserved' | 'superseded' | 'invalid';
  outcomeDetail?: string; }
interface NumberSequence { businessId: Id; kind: 'job' | 'legal_invoice'; next: number; }  // row-locked increment

// ---------- Settings ----------
interface JobSettings { businessId: Id; numbering: { nextJobNumber: number; prefixes: { job: string; quote: string; invoice: string };
    strictLegalInvoiceNumbers: boolean; legalFormat?: string };
  quotes: { defaultExpiryDays: number; defaultTermsTemplateId?: Id; bookOnlineDefault: boolean; requireSignature: boolean; allowOptionalLines: boolean; allowZeroTotal: boolean };
  invoices: { defaultDueTerms: Invoice['dueTerms']; autoSendOnCompletion: 'off' | 'immediate' | { afterHours: number }; allowPartialOnline: boolean; minPartialCents: Cents };
  field: { photoEnforcement: 'block' | 'warn' | 'off'; checklistEnforcement: 'block' | 'warn' | 'off';
    requireCompletionSignature: boolean; geofenceRadiusM: number; earlyStartWindowMin: number;
    workerPrices: { view: boolean; edit: boolean }; allowConcurrentJobs: boolean; galleryPhotosAllowed: boolean;
    trackingLinkTtlMin: number; onMyWayLeadWorkerOnly: boolean; noShowWaitMin: number; noShowEvidenceRequired: boolean;
    promptClockIn: boolean };
}
interface DocumentStyleSettings { businessId: Id; showTaxId: boolean; showWorkerName: boolean; showServiceDate: boolean;
  showLineDescriptions: boolean; showQtyPrice: boolean; showTaxBreakdown: boolean; showPhotosOnInvoice: boolean;
  showAssets: boolean; footerText?: string; paymentInstructions?: string; brandVersionId: Id /* Agent 11 */; }
```

Relations & keys:
- `Job (businessId, number)` unique where number not null. `Job.id` is the only FK other modules use.
- `Quote.jobId` PK=FK (1:1). `QuoteVersion (jobId, versionNo)` unique.
- `Invoice (jobId, kind='primary')` unique; supplemental `(jobId, suffix)` unique.
- `JobPhoto.id`, `ChecklistResponse.id`, `DeviceCommand.commandId` are **client-generated** (idempotency).
- `JobAuditEntry (jobId, seq)` PK; append-only; never updated.
- Indexes: `(businessId, masterStatus, schedule.start)`, `(businessId, customerId, createdAt)`,
  `(businessId, invoiceState, dueOn)`, `(businessId, quoteState, expiresOn)`, `(businessId, seriesId)`,
  assigned worker + date (via Agent 03's schedule table), full-text on number/customer/address.
- Retention: documents and audit kept ≥ 7 years by default (tax records) `[I]`; photos per plan setting;
  customer erasure (Agent 07 `customer.customer.erased`) pseudonymises job customer snapshots but keeps
  financial documents where law requires (§13 Q).

---

## 8. API surface

Command/query style, transport-agnostic (REST/RPC/MCP all map onto it). Every mutating command takes
`idempotencyKey` (or `commandId` from devices) and, where it edits an existing job, `expectedVersion`.
Errors use stable codes (§8.4). Auth column = permission key (Agent 09 owns the matrix; keys proposed in
§9.2). The same commands back the CRM web app, the **native desktop and iPhone staff apps** (owner
requirement 2026-11-13), the worker app, the portal (token-scoped), the public API and the CRM MCP / AI
assistant `[S: llms]` — no surface gets private logic `[D]`.

### 8.1 Commands — job & lines

| Command | Input | Output | Auth |
|---|---|---|---|
| `jobs.create` | `{mode:'appointment'\|'quote'\|'invoice', origin, customerId?, lines[], address?, assetIds[], notifyContacts[], notes, tagIds[], preferredWorkerId?, paymentTiming, discounts[], taxMode, idempotencyKey}` | `{jobId, number, version}` | `jobs.job.create` (quote mode: `quotes.quote.create`; invoice: `invoices.invoice.create`) |
| `jobs.createFromBooking` | `{bookingSessionId, draft, scheduleHoldId, paymentRefs[], answers[], termsAcceptance, idempotencyKey}` | `{jobId, number}` | service-to-service (Agent 01/03 saga) |
| `jobs.createOccurrence` | `{seriesId\|subscriptionId, occurrenceIndex, template, localDate}` | `{jobId, number}` | service (Agent 03/06) |
| `jobs.update` | `{jobId, expectedVersion, patch:{customerId?, address?, assetIds?, notifyContacts?, notes?, tagIds?, preferredWorkerId?, paymentTiming?, taxMode?}}` | `{version}` | `jobs.job.edit` (+ phase locks §5.7) |
| `jobs.lines.add` | `{jobId, expectedVersion, lines[], source, approvedByCustomer?}` | `{lineIds[], totals, version}` | `jobs.job.edit` or `jobs.addons.add` (WA upsell) |
| `jobs.lines.update` | `{jobId, expectedVersion, lineId, patch:{qtyMilli?, unitPriceCents?, unitCostCents?, name?, description?, taxable?, assignedWorkerIds?}}` | `{totals, version}` | `jobs.job.edit`; price change: `jobs.price.override` |
| `jobs.lines.remove` | `{jobId, expectedVersion, lineId, reason?}` | `{totals, version}` | `jobs.job.edit` |
| `jobs.lines.refreshPrices` | `{jobId, expectedVersion, lineIds?}` | `{diff[], totals}` | `jobs.job.edit` |
| `jobs.discounts.apply` / `.remove` | `{jobId, discountRef}` | `{totals}` | `payments.discount.apply` (Agent 05/06 key) |
| `jobs.cancel` | `{jobId, reason, notifyCustomer, feeDecision?:'policy'\|'waive'\|'custom', feeCents?}` | `{masterStatus, invoiceOutcome}` | `jobs.job.cancel` (customer via portal token) |
| `jobs.void` | `{jobId, sagaId}` | `{}` | service only — booking-saga compensation (Agent 03) |
| `jobs.discardDraft` | `{jobId}` | `{}` | `jobs.job.create` (own) / `jobs.job.delete` |
| `jobs.duplicate` | `{jobId, mode}` | `{jobId}` | `jobs.job.create` |
| `jobs.rebook` | `{jobId}` | `{jobId}` (new, `relatedJobId`) | `jobs.job.create` |
| `jobs.recordPast` | `{...create, completedAt, markCompleted:boolean}` | `{jobId}` | `jobs.job.backdate` |
| `jobs.reopen` | `{jobId, reason}` | `{masterStatus}` | `jobs.job.reopen` |
| `jobs.applySeriesChange` | `{seriesId, fromDate, patch, overwriteDetached:boolean}` | `{updatedJobIds[], conflicts[]}` | service (Agent 03) |
| `jobs.files.add` / `.remove` | `{jobId, fileUploadId, visibility}` | `{fileId}` | `jobs.job.edit` |
| `jobs.notes.add` | `{jobId, visibility, body}` | `{noteId}` | `jobs.job.view` (workers: worker/internal only) |

### 8.2 Commands — quotes & invoices

| Command | Input | Output | Auth |
|---|---|---|---|
| `quotes.preview` | `{jobId}` | `{html, emailPreview}` (not persisted) | `quotes.quote.create` |
| `quotes.send` | `{jobId, expectedVersion, recipients[], channels[], subject?, message?, attachPdf, sendAt?}` | `{versionId, publicUrl}` | `quotes.quote.send` |
| `quotes.revise` | `{jobId}` | `{draftVersionId}` | `quotes.quote.create` (+`quotes.quote.revise_accepted` if accepted) |
| `quotes.extendExpiry` | `{jobId, expiresOn}` | `{}` | `quotes.quote.send` |
| `quotes.withdraw` | `{jobId, reason}` | `{}` | `quotes.quote.withdraw` |
| `quotes.accept` | `{token \| jobId, versionId, acceptorName, signature?, selectedOptionalLineIds[], method}` | `{acceptanceId, next:'book'\|'done'}` | public token scope `accept`; office: `quotes.quote.mark_accepted` |
| `quotes.decline` | `{token \| jobId, versionId, reason, text?}` | `{}` | token scope `decline`; office: `quotes.quote.send` |
| `quotes.bookFromQuote` | `{token, versionId, slotHoldId, paymentRefs[]}` | `{jobId, schedule}` | token scope `book` → saga with Agent 03/05 |
| `invoices.preview` | `{jobId, invoiceId?}` | `{html, emailPreview}` | `invoices.invoice.view` |
| `invoices.issue` | `{jobId, invoiceId?, send:boolean, recipients[]?, dueTerms?}` | `{versionId, publicUrl}` | `invoices.invoice.send` |
| `invoices.resend` | `{invoiceId, recipients[]}` | `{}` | `invoices.invoice.send` |
| `invoices.void` | `{invoiceId, reason}` | `{}` | `invoices.invoice.void` |
| `invoices.markUncollectible` | `{invoiceId, reason}` | `{}` | `invoices.invoice.write_off` |
| `invoices.createSupplemental` | `{jobId, lines[]}` | `{invoiceId, suffix}` | `jobs.job.edit_paid` |
| `invoices.setDueTerms` | `{invoiceId, dueTerms, dueOn?}` | `{}` | `invoices.invoice.send` |
| `documents.download` | `{jobId, docType, versionId?, format:'pdf'}` | file stream (signed URL, 5 min TTL) | view perm or token |
| `documents.rerender` | `{renderingId}` | `{renderingId}` | `settings.communication.manage` |

### 8.3 Commands — field execution (worker app; also callable from CRM by office)

| Command | Input | Output | Auth |
|---|---|---|---|
| `field.enRoute` | `{jobId, commandId, deviceTime, etaAt, etaSource, location, notifyCustomer}` | `{trackingUrl?, notified:boolean}` | `jobs.workflow.run` (Agent 09 key) + assigned |
| `field.cancelEnRoute` | `{jobId, commandId}` | `{}` | same |
| `field.locationPing` | `{trackingSessionId, points[]}` (batched) | `{}` | device token; high-frequency path, not on event bus |
| `field.arrive` | `{jobId, commandId, method, location}` | `{}` | same |
| `field.start` | `{jobId, commandId, deviceTime, location, confirmEarly?}` | `{}` | same |
| `field.pause` / `field.resume` | `{jobId, commandId, reason?}` | `{}` | same |
| `field.answer` | `{jobId, responseId, itemId, value, na?, photoIds?}` | `{checklistComplete:boolean}` | same |
| `field.photos.init` | `{jobId, photoId, stage, lineItemId?, sha256, bytes, mime, capturedAt, geo?, fromGallery}` | `{uploadUrl \| alreadyHave:true, chunkSize}` | same |
| `field.photos.complete` | `{photoId, sha256}` | `{state}` | same |
| `field.photos.share` / `.delete` | `{photoId, shared}` | `{}` | `jobs.photos.manage` |
| `field.signature` | `{jobId, purpose, signerName, fileId}` | `{signatureId}` | same |
| `field.complete` | `{jobId, commandId, deviceTime, location, overrideReason?}` | `{masterStatus, balanceDue, posRequired:boolean}` | same; override: `jobs.requirements.override` |
| `field.noShow` | `{jobId, commandId, evidencePhotoIds[], note}` | `{}` | `jobs.job.no_show` |
| `sync.push` | `{deviceId, commands: DeviceCommand[]}` (ordered by deviceSeq) | `{results:[{commandId, outcome, detail, jobVersion}]}` | device session |
| `sync.pull` | `{deviceId, cursor, window:{from,to}}` | `{changes[], cursor, requirementTemplates, catalogSubset}` | device session |

### 8.4 Queries

| Query | Input | Output | Auth |
|---|---|---|---|
| `jobs.get` | `{jobId \| number}` | full Job view model (fields filtered by perms: cost/profit hidden without `settings.catalog.view_cost`) | `jobs.job.view` (workers: assigned only) |
| `jobs.list` | `{filters:{status[], paymentStatus[], tagIds[], zoneIds[], workerIds[], customerIds[], dateRange, origin[], flags[]}, sort, cursor}` | page of rows | `jobs.job.view` |
| `jobs.timeline` | `{jobId}` | audit entries + document events + messages summary | `settings.audit_log.view` for full; others see summarized |
| `quotes.list` | `{state[], expiringWithinDays?, ...}` | rows | `quotes.quote.view` |
| `invoices.list` | `{state[], overdue?, dueRange, ...}` | rows + totals | `invoices.invoice.view` |
| `worker.day` | `{workerId, localDate}` | ordered jobs with requirement progress, route info | self or `jobs.job.view` |
| `public.document` | `{token}` | branded view model (Agent 11 tokens) + allowed actions | token |
| `public.tracking` | `{token}` | worker first name/photo, vehicle, ETA, last position (coarse), state | token, TTL |
| `jobs.search` | `{q}` | jobs by number/prefix, customer, address, plate (via 07) | `jobs.job.view` |
| `jobs.totalsPreview` | `{lines, discounts, zoneId, taxMode}` | totals (delegates to `payments.quoteTotals`) | any authenticated |
| `invoices.reminderEligibility` | `{invoiceId \| jobId, reminderKind}` | `{eligible:boolean, reason}` | service (Agent 08) |

### 8.5 Error codes

`JOB_VERSION_CONFLICT` (409, includes current version + diff), `JOB_LOCKED_PHASE`, `JOB_CANCELLED`,
`QUOTE_EXPIRED`, `QUOTE_SUPERSEDED` (includes latest version link), `QUOTE_WITHDRAWN`, `QUOTE_ALREADY_ACCEPTED`
(200-equivalent for idempotent replays), `INVOICE_HAS_PAYMENTS` (void blocked), `INVOICE_EXPORT_LOCKED`,
`REQUIREMENTS_UNMET` (lists items), `NOT_ASSIGNED`, `ANOTHER_JOB_EN_ROUTE`, `EARLY_START_CONFIRM_REQUIRED`,
`NO_DELIVERABLE_CHANNEL`, `NUMBER_PROVISIONAL`, `PHOTO_HASH_MISMATCH`, `RATE_LIMITED`, `PERMISSION_DENIED`,
`TOKEN_INVALID` (404 shape, no existence leak).

---

## 9. Interfaces with other modules

### 9.1 Ownership boundaries (summary)

| Concern | Owner | 04's role |
|---|---|---|
| Job aggregate, number, master status, quote/invoice documents, field execution, photos, checklist responses, audit | **04** | owner |
| Time window, assignments, schedule blocks, routes, recurring series, availability, dispatch | **03** (`JobSchedule` keyed by `jobId`) | projection only; 03 never changes job status, 04 never changes time/worker (matches 03 §1 boundary) |
| Totals breakdown, tax order, payments, tips, deposits, refunds, A/R ledger, POS tendering, payment status | **05** (`PriceBreakdown`, ledger) | calls `payments.quoteTotals` / `payments.saveBreakdown`; projects balance; issues invoice documents |
| Catalog items, prices, durations, checklist/photo/question templates, add-on pairing, inventory, zones, travel fee, zone tax | **02** | snapshots onto lines and requirements |
| Customers, contacts/people, addresses, consent, assets, leads | **07** | references + snapshots on documents |
| Discount codes, subscriptions, gift cards | **06** | references; gift card = tender via 05 |
| Notification templates, channels, consent enforcement, reminders scheduling, inbox/chat, reviews, reports | **08** | emits triggers; supplies view-models |
| Roles, permission keys, worker profile, timesheets | **09** | checks permissions; prompts clock-in |
| Booking portal UI, customer accounts, public pages hosting, cancellation policy, tracking page host | **01** | provides document/tracking view-models & commands |
| Brand tokens, fonts, logos, copy overrides, light/dark, responsive design system, native app shells | **11** | renders documents with resolved tokens |

### 9.2 CONSUMES

| From | Event / API | Use in 04 |
|---|---|---|
| 01 | `jobs.createFromBooking` call (inside 03's commit saga); `portal.booking.confirmed` | create job with origin online_booking/quote_booking; booking answers, T&C evidence, source |
| 01 | `portal.cancellation.completed` / `portal.reschedule.completed` | reflect customer cancellations (cancel via `jobs.cancel{actor:customer}`) and reschedules (via 03) |
| 01 | `portal.invoice.payment_started` | show "Customer is paying…" lock on invoice edits for 10 min `[D]` |
| 02 | `catalog.requirements.resolve(serviceIds[], addonIds[], zoneId)` (sync API — **proposed to 02**) | build RequirementsSnapshot: global + service checklists, questions, photo min counts per stage `[S: t06]` |
| 02 | catalog item lookup with price/cost/duration/taxable/pairing/inventory | line defaults & snapshots `[S: t06, t07]` |
| 02 | `coverage.zone.resolve(address)` → zone, travel fee, tax rate | fee line + tax rate snapshot `[S: t11]` |
| 02 | `catalog.service.updated`, `coverage.tax_rate.changed`, `coverage.travel_charge.changed` | no change to snapshotted jobs; draft jobs show "Price/tax updated — refresh?" |
| 03 | `scheduling.job.scheduled` / `.rescheduled` / `.unscheduled` | schedule projection; invoice auto-create; quote → booked; en-route reset |
| 03 | `scheduling.assignment.created` / `.removed` / `.changed`, `scheduling.job.split` | assignment snapshot; WA list visibility; split lines `[S: llms]` |
| 03 | `routing.route.started` / `.optimized` / `.reoptimized` | WA route ordering `[S: t02]` |
| 03 | `jobs.createOccurrence`, `jobs.applySeriesChange`, `jobs.cancel` (skip), `jobs.void` (saga compensation) | recurring materialisation `[S: t14]` |
| 05 | `payments.quoteTotals` / `payments.saveBreakdown` (sync) | authoritative totals; 04's §5.2 algorithm is the shared reference implementation (pure library also used offline in WA) |
| 05 | `payments.payment.succeeded` / `.authorized` / `.reversed`, `payments.refund.succeeded`, `payments.tip.added` | invoice issuance on first payment, allocation, invoice state |
| 05 | `payments.job.balance_changed` | `paymentStatus` projection & jobs list column `[S: t08]` |
| 05 | `payments.dispute.opened` / `.updated` | `disputed` flag; evidence pack request (04 supplies job, photos, checklist, signatures, timestamps) |
| 06 | discount validation API, `subscription.subscription.cancelled/paused` | discount refs; future subscription occurrences cancelled via 03 |
| 07 | customer/contact/address/asset lookups; `customer.customer.merged` | re-point `customerId` on all jobs (documents keep historical snapshot) |
| 07 | `customer.consent.changed`, `customer.customer.erased`, `asset.asset.transferred` | send guards; pseudonymisation; asset snapshot unchanged on old jobs |
| 08 | `comms.message.delivered` / `.failed` (for document sends), `comms.conversation.message_received` (with jobId) | recipient delivery state on quote/invoice; job Messages tab badge |
| 08 | `invoices.reminderEligibility` call-back pattern | reminders only if still eligible |
| 09 | permission checks; `team.member.deactivated` | deactivated worker: unassign future jobs (03) and keep their historical visits; in-progress job flagged for office |
| 11 | `brand.tokens.resolve`, brand version ids, copy overrides | document & public page rendering (§5.11) |

### 9.3 PRODUCES (event catalog)

All events carry `{eventId, occurredAt, businessId, jobId, jobNumber, jobVersion, actor, source}`.
Payload-heavy events include the minimal fields listed; consumers fetch the rest via `jobs.get`.

| Event | Key payload | Main consumers |
|---|---|---|
| `job.job.created` | origin, mode, customerId, leadId?, seriesId?, suppressCustomerNotifications | 03, 07 (lead link), 08 (reporting), 10 |
| `job.job.updated` | changed fields (material: address, services, notes) | 08 (W-05 worker notice), 03 |
| `job.job.status_changed` | from, to, cause | 08 dashboards/reporting, 03 calendar colors, 10 |
| `job.line.added` / `job.line.updated` / `job.line.removed` | line snapshot, source, addedBy | 02 (inventory reserve), 05 (breakdown), 08 (upsell reporting) |
| `job.job.lines_changed` | durationDeltaMin, totals | 03 (duration conflicts; alias of 03's `job.lines.changed`) |
| `job.job.totals_changed` | totals | 05, 08 |
| `job.job.en_route` | workerId, etaAt, trackingUrl, notifyContacts | 08 (N-D01 on-my-way `[S: llms, t11]`), 03 (live ETA); alias of 03's `job.job.on_my_way` and 01's `job.worker.on_my_way` |
| `job.visit.arrived` / `.paused` / `.resumed` / `.en_route_cancelled` | workerId, at | 03, 08 (optional arrived message) |
| `job.job.started` | workerIds, startedAt | 03 (actual start, overrun), 08 (N-D03 optional), 09 (timesheet hint) |
| `job.job.completed` | completedAt, lines, assets, workers, totals, balanceDue, photosShared[], backfilled | 03 (route progress / auto-close `[S: t02]`), 02 (inventory decrement), 05 (auto-charge after job, revenue), 06 (subscription usage), 07 (asset service record, last service date `[S: llms]`), 08 (N-D04, review booster `[S: llms]`, leaderboard `[S: t09]`), 09 (job time) |
| `job.job.reopened` | reason | 03, 02 (inventory reversal), 08 |
| `job.job.cancelled` | reason, actor, feeCents (per policy), refundNeeded | 03 (release), 05 (fee/refund), 06, 07, 08 (cancellation notice) — alias of 05's `job.job.canceled` (spelling: we standardise on `cancelled`) |
| `job.job.no_show` | evidence ids, feeCents | 03, 05, 08 |
| `job.checklist.completed` / `.reopened` | itemsAnswered | 08 (reporting), office UI |
| `job.requirements.overridden` | unmet items, reason, by | 08 (owner alert), 09 (audit) |
| `job.photo.added` / `job.photo.uploaded` | photoId, stage, sharedWithCustomer | 08 (dashboard "all photos" `[S: llms]`, N-D04 wait-for-uploads) |
| `job.signature.captured` | purpose | 05 (dispute evidence) |
| `quote.quote.sent` / `.viewed` / `.revised` / `.accepted` / `.declined` / `.expired` / `.withdrawn` / `.expiry_changed` | versionId, recipients, expiresOn, bookOnline, total, reason | 08 (N-Q01, N-Q04, A-04, reminders), 07 (lead pipeline), 03 (`accepted` with bookOnline → book-from-quote entry), 01 |
| `invoice.invoice.created` | invoiceId (draft) | 05 |
| `invoice.invoice.issued` | versionId, total, dueOn, breakdownVersion | 05 (revenue recognition — **alias of 05's `job.invoice.finalized`**) |
| `invoice.invoice.sent` / `.viewed` | recipients | 08 (N-I01, reminder scheduling `[S: t04]`) |
| `invoice.invoice.payment_applied` / `.paid` / `.refund_applied` | amounts, balance | 08 (receipts, cancel reminders), 07 (customer balance) |
| `invoice.invoice.revised` | versionNo, delta | 05, 08 ("updated invoice") |
| `invoice.invoice.overdue` | dueOn, balance | 08 (overdue reminders), 07 |
| `invoice.invoice.voided` / `.written_off` | reason | 05, 08 |

### 9.4 Contracts with specific agents (as requested)

**Agent 02 — checklists & photo rules**
- 02 owns *templates*: global checklist items, service-specific items, question types, required flags, photo
  minimum counts per stage (before/after) `[S: t06]`, and whether a service's photo rule is additive.
- 04 owns *instances*: the RequirementsSnapshot on each job and all responses/photos.
- API needed from 02: `catalog.requirements.resolve({serviceIds, addonIds, zoneId}) → {items[], photoRules[], signatureRequired}`
  with `templateVersion` per item. Deterministic; cacheable on device for offline upsell (WA downloads the
  requirement templates for every service the worker can sell).
- Template edits never mutate open jobs' snapshots; office can "Refresh requirements" on a not-started job.
- Inventory: 02 reserves on `job.line.added` (product add-on), decrements on `job.job.completed`, releases on
  `job.job.cancelled`/`job.line.removed`, reverses on `job.job.reopened` then re-decrements on next completion.

**Agent 03 — schedule & assignment**
- 03 owns `JobSchedule`; creation of a booked job is **one commit saga run by 03** (hold → `jobs.createFromBooking`
  → confirm blocks → payment step by 05 → commit; compensation `jobs.void`). 04 returns `{jobId, number}`.
- 04 never writes time/worker; 03 never writes status. 03 relies on `job.job.en_route/started/completed/cancelled/no_show`
  and `job.job.lines_changed` (duration) — names aligned in §9.3.
- Route close `[S: t02]`: 03 auto-closes on the last stop's terminal event (completed / cancelled / no_show).
- Recurring "commit to all future" `[S: t14]`: 03 calls `jobs.applySeriesChange`; detached occurrences reported back.
- Backdated jobs `[S: llms]`: 03 marks `isBackdated` on `scheduling.job.scheduled`; 04 offers completion (W14).

**Agent 05 — payments on the job**
- 05 is the authority for totals (`PriceBreakdown`), tax order, deposits, pre-auth, tips, tenders and balance.
  04 keeps `JobLineItem.id` stable so 05's breakdown `line_id` maps 1:1.
- Invoice *document* lifecycle is 04's; the *ledger* is 05's. 04 emits `invoice.invoice.issued` (= 05's
  `job.invoice.finalized`) which 05 uses for revenue recognition; 05 emits `payments.payment.succeeded`
  and `payments.job.balance_changed`, which 04 uses for invoice state.
- POS hand-off from completion: 04 opens 05's POS with `{jobId, invoiceId, amountDue, allowSendInvoice:true}`;
  "Send invoice" tender delegates back to `invoices.issue{send:true}` `[S: t16]`.
- Cancellation/no-show fee amount is computed by policy (01's cancellation policy, 05 charges); 04 records the fee
  line/revision. 05's dispute evidence pack pulls 04's photos, checklist, signatures, GPS timestamps.

**Agent 07 — customers & assets**
- Jobs reference `customerId`, `contactId`s, `addressId`, `assetIds`; documents print snapshots.
- 04 emits `job.job.completed` with assets → 07 writes `asset.service_record.added` / last-service date `[S: llms]`.
- Merge: 07 emits `customer.customer.merged{from,to}` → 04 re-points jobs idempotently. Erasure: pseudonymise.
- Lead conversion uses `jobs.create{origin:'lead_conversion', leadId}`; 07 marks lead won on `job.job.status_changed`
  to `scheduled`/`approved`.

**Agent 08 — notifications**
- 04 emits; 08 decides channel/consent/template and owns the catalog (IDs N-Q01, N-Q04, N-I01, N-D01, N-D03, N-D04,
  A-04, W-05 already defined by 08 map to the events above).
- 04 supplies view-models `VS-QUOTE`, `VS-INVOICE`, `VS-JOB` (document link, totals, balance, ETA, tracking link,
  shared photos) and the reminder-eligibility query; 08 must re-check eligibility immediately before each
  reminder send (race with payments).
- Quiet-hours/backdated suppression flag `suppressCustomerNotifications` honoured by 08.
- Document style (look of quotes/invoices) lives in Settings → Communication → Email per `[S: t04]` — UI hosted in
  08's settings area, data model owned by 04 + brand tokens by 11.

**Agent 09 — who can do what** (proposed permission keys; 09 owns final matrix, key format `module.resource.action`)

| Key | Default: Owner/Admin | Manager | Foreman | Worker | Estimator |
|---|---|---|---|---|---|
| `jobs.job.view` (all) | ✔ | ✔ | crew | assigned | own + assigned |
| `jobs.job.create` | ✔ | ✔ | ✔ | ✗ | ✔ |
| `jobs.job.edit` | ✔ | ✔ | crew | ✗ | own quotes |
| `jobs.addons.add` (POS upsell) | ✔ | ✔ | ✔ | ✔ | ✔ |
| `jobs.price.override` | ✔ | ✔ | ✔ | ✗ | ✔ |
| `jobs.job.cancel` | ✔ | ✔ | ✗ | ✗ | ✗ |
| `jobs.job.no_show` | ✔ | ✔ | ✔ | ✔ | ✗ |
| `jobs.job.reopen` | ✔ | ✔ | ✔ | ✗ | ✗ |
| `jobs.job.edit_completed` / `edit_paid` | ✔ / ✔ | ✔ / ✗ | ✗ | ✗ | ✗ |
| `jobs.job.backdate` | ✔ | ✔ | ✗ | ✗ | ✗ |
| `jobs.workflow.run` (en route/start/complete) | ✔ | ✔ | ✔ | ✔ | ✔ |
| `jobs.requirements.override` | ✔ | ✔ | ✔ | ✗ | ✗ |
| `jobs.photos.manage` (delete/share) | ✔ | ✔ | ✔ | own, before verify | own |
| `quotes.quote.create` / `send` | ✔ | ✔ | ✔ | ✗ | ✔ |
| `quotes.quote.mark_accepted` / `withdraw` / `revise_accepted` | ✔ | ✔ | ✗ | ✗ | own |
| `invoices.invoice.view` / `send` | ✔ | ✔ | crew | assigned (send only from POS) | ✗ |
| `invoices.invoice.void` / `write_off` | ✔ | ✗ | ✗ | ✗ | ✗ |
| `settings.catalog.view_cost` (cost/profit on jobs) | ✔ | ✔ | ✗ | ✗ | ✗ |
| `wa.payment.skip` (complete without collecting) | ✔ | ✔ | ✔ | ✗ | ✗ |

Also from 09: timesheets — 04 shows a clock-in prompt when `promptClockIn` and the worker has timesheet permission
`[S: t03]`; 04 never creates timesheet entries.

**Agent 01 — booking portal**: hosts public quote/invoice/tracking pages on the business subdomain using 04's
`public.document` / `public.tracking` view-models; runs the book-from-quote slot step; enforces cancellation
policy and passes `jobs.cancel{actor:'customer'}`.

**Agent 11 — branding**: §5.11. 04 needs `brand.tokens.resolve` with surfaces `document`, `document_email`,
`document_page`, `tracking_page`, `worker_app`; logo placements `invoice`, `email`, `header`, `mobileHeader`;
font files with licences permitting PDF embedding; copy-override keys for every document label (04 provides the
key list: `doc.quote.title`, `doc.invoice.title`, `doc.balance_due`, `doc.book_online`, `doc.accept`,
`doc.decline`, `doc.terms`, `doc.valid_until`, `doc.due_on`, `doc.paid_stamp`, `doc.void_stamp`, …).

---

## 10. Algorithms / pseudo-code

### 10.1 Master status derivation

Pure function, run inside every job transaction after sub-states change; result persisted with the job and
`job.job.status_changed` emitted only if it changed.

```ts
function deriveMasterStatus(j: Job): MasterStatus {
  if (j.cancelledAt) return 'cancelled';
  if (j.workState === 'no_show') return 'no_show';
  const workDone = j.fieldWork === 'none' || j.workState === 'completed';
  if (workDone) {
    switch (j.invoiceState) {
      case 'paid': case 'partially_refunded': return netPaid(j) >= j.totals.total ? 'paid' : 'partially_paid';
      case 'refunded': return j.totals.total === 0 ? 'paid' : 'invoiced';     // money returned, still owed
      case 'partially_paid': return 'partially_paid';
      case 'open': case 'viewed': return 'invoiced';
      case 'written_off': return 'written_off';
      case 'void': return j.fieldWork === 'none' ? 'cancelled' : 'completed';
      default: /* draft|none */
        if (j.totals.total === 0) return 'paid';
        if (j.fieldWork === 'none') return 'draft';
        return 'completed';
    }
  }
  switch (j.workState) {
    case 'paused': return 'paused';
    case 'in_progress': return 'in_progress';
    case 'arrived': return 'arrived';
    case 'en_route': return 'en_route';
  }
  if (j.scheduleState === 'scheduled') return 'scheduled';
  if (j.quoteState === 'accepted') return 'approved';
  if (j.quoteState === 'sent' || j.quoteState === 'viewed') return 'quoted';
  if (j.quoteState === 'declined') return 'declined';
  if (j.quoteState === 'expired') return 'expired';
  if (j.quoteState === 'withdrawn') return 'cancelled';
  if (j.fieldWork === 'required' && hasBeenBooked(j)) return 'unscheduled';  // e.g. unscheduled by 03
  return 'draft';
}
// flags computed alongside: overdue, unassigned (scheduled && no workers), requirementsIncomplete, ...
```

### 10.2 Invoice sync with job lines

```ts
onJobLinesOrTotalsChanged(job):
  inv = primaryInvoice(job)
  if (!inv || inv.state === 'draft') return        // draft invoice is a live view; nothing to do
  if (inv.state in ['void','refunded'] ) -> reject edit (JOB_LOCKED_PHASE) unless creating rebook
  if (inv.exportLockedAt) {
    delta = diffLines(currentVersion(inv).linesSnapshot, job.lines)
    createSupplementalInvoice(job, delta.addedOrIncreased)      // #N-2 in draft; decreases → credit note via 05
    return
  }
  breakdown = payments.saveBreakdown(job.id, inputs(job), expectedVersion)   // Agent 05 authority
  v = new InvoiceVersion(inv, job.lines, breakdown, reason:'revision')
  inv.balanceDue = max(breakdown.total - inv.amountPaid, 0)
  inv.state = recomputeInvoiceState(inv)            // paid -> partially_paid if balance > 0
  emit invoice.invoice.revised { delta: breakdown.total - previousTotal }
  if (inv.amountPaid > breakdown.total) set flag creditDue; emit for 05 refund/credit decision
```

#### 10.2.1 Largest-remainder allocation (discounts and tax back to lines)

```ts
function allocate(totalCents: number, weights: number[]): number[] {
  const sumW = weights.reduce((a, b) => a + b, 0);
  if (sumW === 0) return weights.map(() => 0);
  const raw = weights.map(w => (totalCents * w) / sumW);       // use bigint/rational in impl
  const floors = raw.map(Math.floor);
  let remainder = totalCents - floors.reduce((a, b) => a + b, 0);
  const order = raw.map((r, i) => ({ i, frac: r - floors[i] }))
                   .sort((a, b) => b.frac - a.frac || a.i - b.i);   // deterministic tie-break by line order
  for (let k = 0; k < remainder; k++) floors[order[k].i] += 1;
  return floors;
}
```

### 10.3 Accept-and-book saga (quote → booked job), idempotent

```
bookFromQuote(token, versionId, slot, paymentInput, key):
  1. resolve token → job; assert scope 'book'; lock job row (SELECT … FOR UPDATE semantics or equivalent)
  2. if quote.acceptance exists for versionId → reuse; else AcceptQuote(...) (step validated: latest, not expired)
  3. hold = scheduling.hold.create(jobId, slot, durationMin, preferredWorkerRule)   // Agent 03, TTL 10 min
        on failure → return SLOT_UNAVAILABLE + fresh suggestions (job stays 'approved')
  4. if deposit/card-on-file/pre-auth required → Agent 05 payment step with idempotencyKey = key+':pay'
        on failure → scheduling.hold.release; return PAYMENT_FAILED (job stays 'approved')
  5. scheduling.booking.commit(holdId)  → emits scheduling.job.scheduled
        on failure after payment → 05 voids/refunds authorization (compensation); return BOOKING_FAILED
  6. 04 handler of scheduling.job.scheduled: quote → booked; create invoice draft; derive status
  7. respond with confirmation view-model
  Replays with the same key at any step return the stored outcome of that step.
```

### 10.4 Completion gate

```ts
function completionCheck(job, visit, settings): { ok: boolean; unmet: Unmet[]; mode: 'block'|'warn'|'off' } {
  const unmet: Unmet[] = [];
  for (const rule of job.requirements.photoRules) {
    const have = photosFor(job, rule.stage).filter(p => p.uploadState !== 'failed_permanent' && !p.deletedAt
                    && (!rule.sourceLineIds.length || !p.lineItemId || rule.sourceLineIds.includes(p.lineItemId))).length;
    if (have < rule.minCount) unmet.push({ kind: 'photo', stage: rule.stage, have, need: rule.minCount });
  }
  for (const item of job.requirements.items.filter(i => i.required && appliesToWorker(i, visit.workerId))) {
    const r = latestResponse(job, item.id);
    if (!r || (!r.na && isEmpty(r.value)) || (r.na && !item.allowNA)) unmet.push({ kind: 'checklist', itemId: item.id });
  }
  if (job.requirements.signatureRequired && !hasSignature(job, 'completion')) unmet.push({ kind: 'signature' });
  const mode = strictestMode(settings.field.photoEnforcement, settings.field.checklistEnforcement, unmet);
  return { ok: unmet.length === 0 || mode === 'off', unmet, mode };
}
// Server re-runs the check on CompleteJob; WA runs the same function offline (shared library).
// If the server sees unmet items that the device thought were met (e.g. photo later failed_permanent),
// completion is still applied (never bounce a worker's completion after the fact) but flagged
// requirementsIncomplete + job.requirements.overridden{reason:'post_sync_mismatch'} for the office.
```

### 10.5 Requirements resolution

```
resolveRequirements(job):
  lines = active service + addon lines
  tpl = catalog.requirements.resolve({serviceIds, addonIds, zoneId})        // Agent 02
  items = dedupe(tpl.globalItems ∪ tpl.serviceItems) by templateItemId     // global items appear once
  photoRules per stage:
     base = tpl.global.minCount[stage]
     svc  = tpl.services.map(s => s.minCount[stage])
     need = max(base, max(svc)) + Σ(svc where rule.additive)               // default: max; additive opt-in
  snapshot = { items, photoRules, signatureRequired: settings.requireCompletionSignature || tpl.signature }
  on upsell line added → union new items / recompute need (never decrease below already-satisfied)
  on line removed → drop unanswered items sourced only from that line
```

### 10.6 On-my-way: ETA, staleness and live tracking

```
enRoute(cmd):
  if job not in [scheduled] or workState not in [not_started] → if workState beyond en_route: outcome superseded
  serverLag = now - cmd.deviceTime (skew-corrected)
  eta = cmd.etaAt
  if cmd.etaSource == 'gps_route' and online: recompute with routing provider from cmd.location (cap ±50%)
  notify = cmd.notifyCustomer && settings.liveTracking
  if serverLag > 10 min → notify = false; record 'eta_not_sent_stale'                         // offline queue
  if eta < now → eta = now + max(5 min, remainingDriveTime(lastPing))                      // never send past ETA
  if job.flags.backfilled or schedule.start < now - 12h → notify = false
  if onMyWayLeadWorkerOnly and another visit already en_route → notify = false
  session = tracking.create(jobId, workerId, ttl = settings.trackingLinkTtlMin)
  emit job.job.en_route {etaAt: eta, trackingUrl: notify ? session.url : null}

tracking pings (every 15 s or 50 m; batched upload when signal is poor):
  store last N points (ring buffer, 24 h retention), recompute ETA every 60 s server-side,
  if |eta - lastNotifiedEta| > 10 min → emit schedule.eta.updated (Agent 08 "running late" per its catalog)
  stop on arrived/started/cancel/reschedule or TTL; public.tracking shows coarse position (≈100 m) only
  while en_route; after stop shows state text only.
```

### 10.7 Offline-first sync and conflict rules (worker app, estimator, native staff apps)

Device model: local database mirroring the worker's jobs in the window [today − 2 days, today + 14 days] plus
all non-terminal assigned jobs; catalog subset (sellable items + prices + requirement templates); customers and
assets for those jobs only (minimised PII; encrypted at rest; wiped on logout/deactivation).

Every user action = append a `DeviceCommand` to the outbox (with client-generated ids for any new entity) and
apply optimistically to the local store. Sync loop: `sync.push` in `deviceSeq` order (one in flight per job to
preserve order; different jobs in parallel), then `sync.pull` from cursor. Triggers: connectivity regained,
app foreground, every 60 s while active, background fetch, after each command when online.

Server application of a command (inside the job transaction):

```
apply(cmd):
  if seen(cmd.commandId) → return stored outcome ('duplicate')
  job = load(cmd.jobId)
  switch conflictClass(cmd.type):
    case APPEND_ONLY:      // notes, photos.init, checklist answers, signatures, location pings
      accept regardless of baseVersion unless job is hard-deleted; if job cancelled/reassigned → accept
      and mark 'conflict_preserved' (data attached to job audit; office notified once per job)
    case STATE_TRANSITION: // enRoute, arrive, start, pause, resume, complete, noShow
      if transition valid from current server state → apply (use skew-corrected deviceTime as event time,
         clamped to [lastStateChangeAt, serverNow])
      elif already in target or later state → 'superseded' (e.g. start after office completed)
      elif job cancelled → 'conflict_preserved' (store visit data; tell worker "Job was cancelled at 10:12")
      elif worker unassigned → if (cmd is complete|start) and work evidence exists → apply as
           'performed by unassigned worker' + office alert; else 'conflict_preserved'
    case LINE_EDIT:        // upsell add / qty change / price change
      add: apply if workState != completed-by-office and invoice not void; if invoice issued → revision
      qty/price update on a line also changed by office since baseVersion → office wins; device change
           recorded as rejected suggestion with diff (worker sees "Office changed price to $X")
      remove: allowed only for lines the device added and that are unchanged on server
    case PAYMENT_RECORD:   // cash/check only offline (card requires connectivity — Agent 05)
      forward to Agent 05 with idempotency key = commandId; never dropped
    case QUOTE_DRAFT:      // estimator offline quotes
      create job with number on sync; acceptance (in_person) applied if version content hash matches
  bump job.version; append audit with actor.deviceId and both times
```

Field-level policy summary:

| Data | Who wins on conflict |
|---|---|
| Schedule, assignment, address, customer | Office/server (03/07 authority) |
| Field state transitions | State machine order; later state wins; never regress |
| Checklist answers | Last answer by deviceTime per item, all answers kept in history |
| Photos, notes, signatures | Union (append-only) |
| Prices | Office; device price edits require permission and lose to concurrent office edits |
| Added lines | Union, unless job closed by office → preserved as suggested lines for office approval |

Clock skew: each sync request carries device clock; server estimates offset (median of last 5 round trips) and
stores `deviceTime`, `correctedTime`, `serverTime`. Reports use corrected time; audit shows all.

### 10.8 CRM concurrent edits (optimistic concurrency + field merge)

```
save(jobId, expectedVersion, patch):
  if job.version == expectedVersion → apply
  else:
    changedSince = auditFieldsChangedAfter(expectedVersion)
    if patch.fields ∩ changedSince == ∅ → auto-merge (apply patch to latest), return new version + "merged" notice
    else → 409 JOB_VERSION_CONFLICT with {theirs, yours, base} per conflicting field; UI shows 3-way choice
  Lines are merged per line id (two users editing different lines merge cleanly).
```

### 10.9 Job number allocation

```
allocateNumber(businessId):
  in its own short transaction: UPDATE number_sequence SET next = next + 1 WHERE businessId=? AND kind='job'
  RETURNING next - 1      // row-level lock; ~1 ms; gaps possible only if the outer txn fails → acceptable
                          // for job numbers (documented); legal invoice numbers use the gapless variant:
allocateLegalInvoiceNumber: inside the SAME transaction as invoice issuance (lock held until commit) → gapless.
Changing "next job number" in settings: only increase; validated ≥ max(existing)+1.
```

### 10.10 Sweeps (per business, local 00:05)

```
for business in businessesDueAt(localTime 00:05):     // computed with tz db; idempotent per (business, localDate)
  expire quotes: state in (sent, viewed) and expiresOn < today → expired (batch, events per quote)
  overdue invoices: state in (open, viewed, partially_paid) and dueOn < today and overdueSince is null → flag + event
  stale field states: en_route > 6 h → revert to not_started + office alert; in_progress > 18 h → office alert
  scheduled jobs whose end < today-1 and not started → flag "past due — not completed" (no auto-change)
  auto-send invoices: completed jobs with draft invoice older than autoSend.afterHours → issue + send
```

### 10.11 Send with undo window

Quote/invoice send writes the version and emits the event immediately, but tells 08 `deliverAfter = now + 10 s`.
"Undo" within the window calls `quotes.unsend` → 08 cancels the queued messages; version is marked `withdrawn_before_delivery`
and the previous state restored. After 10 s, Undo disappears (to correct, revise/resend). `[D]`

### 10.12 Photo capture & upload pipeline (reliability first)

```
capture(photo):
  write original JPEG/HEIC to app-private storage (fsync) → compute sha256 → create local JobPhoto (state captured)
  generate upload rendition: longest edge 2560 px, quality 0.8, keep EXIF time + GPS in metadata record,
     strip EXIF GPS from customer-facing renditions server-side
  enqueue metadata command field.photos.init (small, goes first so the office sees "photo pending")
uploader (background task; survives app restarts; OS background transfer APIs where available):
  for each queued photo (FIFO per job, before-photos first, max 2 concurrent, 1 on cellular-weak):
    init → server returns alreadyHave (dedupe by sha256 within job) or resumable upload URL + chunkSize (256 KB–4 MB adaptive)
    upload chunks with offset; on network error → exponential backoff (2 s … 5 min, jitter), resume at server offset
    complete → server verifies sha256; mismatch → restart once, then failed_permanent (keep local file)
    on verified: mark local uploaded; keep local original until server reports processed + 24 h, or storage pressure
  never delete an unverified photo; storage-full → warn worker before capture fails; allow export to device gallery
server processing: virus/format check, thumbnails (sm/md/lg), orientation fix, perceptual hash (duplicate warning),
  emit job.photo.uploaded; customer-facing variants only for sharedWithCustomer photos
```

---

## 11. Edge cases & failure modes

**Identity & numbering**
1. Two offices create jobs at the same millisecond → row-locked sequence; no duplicate numbers.
2. Owner sets next number lower than max → rejected with explanation.
3. Offline estimator creates 3 quotes → provisional refs; numbers assigned in sync order; any printed/handwritten
   provisional ref resolves via search (`localRef` indexed).
4. Search "1042" when both job #1042 and a phone number containing 1042 exist → number match ranked first.
5. Strict legal invoice numbering enabled mid-year → only invoices issued after enablement get legal numbers;
   earlier ones unchanged.

**Quotes**
6. Customer accepts v1 while office is editing an unsent v2 draft → acceptance of v1 valid; office's draft gets a
   conflict banner; sending v2 requires choosing "re-request acceptance".
7. Customer accepts after office sent v2 (old tab open) → `QUOTE_SUPERSEDED`, page reloads to v2.
8. Accept at 23:59:59 local on expiry day vs. sweep at 00:05 → valid; acceptance at 00:01 → expired (sweep not
   needed for the guard; guard compares dates).
9. Customer double-clicks Accept / network retry → idempotent on versionId.
10. Customer accepts, starts booking, slot taken by someone else → hold fails, job remains `approved`, fresh slots.
11. Customer accepts & pays deposit, booking commit fails → saga compensation voids/refunds; customer sees error
    with "We've saved your acceptance — pick another time".
12. Quote sent to an email that bounces → 08 `comms.message.failed` → job card banner "Quote not delivered".
13. Quote with book-online but no qualified worker exists for its services in the zone → booking step shows
    "Call us to schedule" + office alert, acceptance still recorded.
14. Preferred worker deactivated after quote sent → booking falls back to any qualified worker unless restricted;
    restricted → contact-us fallback + office alert.
15. Catalog price rises after quote sent → quote price honoured; job flagged `priceChangedSinceQuote` (info only).
16. Customer declines then calls to accept → office revises/resends or marks accepted (verbal) — both audited.
17. Quote for a customer later merged into another → token still works; job re-pointed; document shows original name.

**Scheduling interplay**
18. Office reschedules while worker is en route → WA push "Job moved"; en-route reverted; customer tracking link
    shows "Your appointment was rescheduled".
19. Office reassigns while worker has started (offline) → worker's start/complete preserved and applied as
    "performed by unassigned worker" with alert (03 rule: reassigning started jobs needs admin).
20. Recurring "commit to future" while some occurrences are in progress → only not-started occurrences updated.
21. Route reoptimized mid-day → WA order changes; current job unaffected.
22. Job scheduled in the past (backfill) → no customer notifications; requirement gate skipped only when
    "mark completed" chosen.
23. DST: job 01:30 on fall-back day → stored as instant; WA shows "1:30 AM (second occurrence)" disambiguation.

**Field work**
24. Worker taps On my way on the wrong job → "Cancel en-route" stops tracking and invalidates the link; if the ETA
    message was already delivered, 08 sends a short correction (template owned by 08); if still queued (undo
    window), the message is cancelled and the customer never sees it. Audit kept.
25. Worker has no GPS permission → manual ETA only; tracking link not offered.
26. Location permission revoked mid-route → tracking page shows "Live location unavailable", ETA from last estimate.
27. Worker starts job without tapping on-my-way → allowed; customer gets no ETA.
28. Two crew members start simultaneously → first start sets job startedAt; second creates its own visit.
29. Worker completes with photos still uploading → completion accepted; `evidencePendingUpload`; 08 waits ≤ 10 min
    before sending completion summary with photos, then sends without missing ones (link updates).
30. Photo storage full on device → capture blocked with clear message and "free space / upload now" actions;
    never silent loss.
31. App killed/phone dies mid-upload → resumes from server offset on next launch.
32. Same photo uploaded twice (retry after timeout) → sha256 dedupe.
33. Worker picks gallery photo from last week → flagged `fromGallery`, EXIF capturedAt shown; optional block setting.
34. Checklist template changed by office while job in progress → snapshot unchanged; office can push refresh only
    if no answers yet.
35. Upsell added while offline, office meanwhile marked job completed & invoice paid → line arrives as
    "suggested line" requiring office approval (creates revision/supplemental if approved).
36. Upsell pushes total above pre-authorized amount → Agent 05 handles re-auth/incremental auth; 04 shows
    "Payment authorization needs update".
37. Worker attempts to complete before scheduled start by days → confirm dialog; audit.
38. Job left `in_progress` overnight → sweep alert; actual duration excludes nothing automatically (office edits).
39. No-show tapped while customer actually present (mistake) → revert within 24 h with permission; fee reversal.
40. Customer refuses to sign → signature optional unless required; required → "Customer declined to sign" reason.
41. Worker device clock wrong by hours → skew correction; times clamped; audit shows raw device time.
42. Worker logs out with unsynced commands → logout blocked until synced, or export bundle + explicit confirmation.
43. Worker deactivated with pending offline data → device sync allowed for 72 h for pending commands only
    (read access revoked), then wiped.

**Invoices & payments**
44. Payment succeeds but 04 handler fails → event redelivered; idempotent by paymentId; reconciliation job compares
    05 ledger with invoice amountPaid nightly.
45. Two payments race to final balance (customer online + worker cash) → both applied; overpayment → creditDue,
    05 refund flow.
46. Invoice voided while customer is on the pay page → pay attempt fails with "invoice no longer payable" (05 checks
    `getPayableState` at confirm).
47. Line edit after invoice export-locked → supplemental invoice, never silent change.
48. Refund of a deposit on a cancelled job → master stays `cancelled`; payment pill `refunded`.
49. $0 invoice (subscription-covered) → auto paid; no email unless opted in.
50. Tax rate changes between quote and completion → quote price honoured if tax-inclusive? Rule: tax is
    recomputed at issuance using rate in force at **service date** (05 authority); quote shows "tax estimated".
51. Currency rounding: 3 × $3.335 line → 1000ths qty math → integer cents with deterministic rounding;
    totals identical in WA (offline) and server (shared library + server re-check; mismatch → server wins and
    WA shows updated total before payment).
52. Customer pays invoice v1 link after v2 issued → payment applies to the invoice (not the version); balance computed
    on current version.

**Documents & branding**
53. Business uploads font without embedding rights or corrupt file → fallback font, warning in settings.
54. Logo missing/too large → placeholder text logo; size controls clamp to placement bounds.
55. Copy override makes label empty → fallback to default label (empty labels not allowed on documents).
56. PDF render service down → send proceeds with HTML link only, PDF attached later or regenerated on demand;
    never block sending on PDF.
57. Customer forwards link to someone else → link grants only the scoped actions; payment by third party allowed;
    accept requires terms checkbox and name (recorded).
58. Token brute force → 128-bit tokens, rate-limited, uniform 404s.

**Permissions & multi-surface**
59. Worker without `settings.catalog.view_cost` must never receive cost fields — enforced server-side in view-models,
    and not synced to devices.
60. AI/MCP action attempts void/write-off → same permission checks as the connected user; destructive actions require
    explicit confirmation token (two-step) `[D]`.
61. Native desktop app and web CRM editing same job → same optimistic concurrency (§10.8).
62. Phone-call booking: staff building a job while the customer is on the line and the slot is taken by an online
    booking → 03 hold expires/conflict; editor keeps lines and suggests next slots without losing data.

---

## 12. Better than Fieldd

Concrete improvements (all `[D]`; Fieldd baseline from research where cited):

1. **One editor, four modes** — Appointment/Quote/Invoice/Recurring share one editor, so converting a quote to an
   appointment or invoice is a mode switch, not re-entry (extends the single-ID idea `[S: t04]` to the UI).
2. **Lifecycle stepper** on the job card shows quote → booked → en route → in progress → completed → invoiced → paid with
   timestamps/actors — instant "where is this job" answer.
3. **Separate master status and payment status** with a documented derivation function → no contradictory states
   ("Completed" yet "Paid" confusion); jobs list filters are exact.
4. **Quote optional line items** (good/better/best, optional add-ons) that the customer ticks at acceptance — upsell
   before the visit, priced and taxed correctly.
5. **Version history with diffs** for quotes and invoices; customers always land on the latest version; acceptance
   bound to a version and terms hash (dispute-proof).
6. **"Changes since quote"** shown on the invoice → fewer billing disputes after on-site upsells.
7. **Offline-first worker app** with explicit conflict rules, a visible sync center and zero silent data loss;
   completion never blocked by bad signal.
8. **Resumable, deduplicated photo uploads** with hash verification and "never delete until verified".
9. **Stale on-my-way protection**: no ETA texts sent from a queue 40 minutes late; ETA never in the past; no duplicate
   texts for crews.
10. **Completion gate modes** (Block / Warn+reason / Off) and per-requirement override audit, so owners can enforce
    before/after photos `[S: t06]` without stranding workers.
11. **Send undo window** (10 s) for quotes/invoices.
12. **Shared totals library** (CRM, worker app offline, portal, PDFs) → totals identical to the cent everywhere.
13. **Strict sequential legal invoice numbers** option for tax compliance while keeping the single job number.
14. **Supplemental invoices** after accounting lock instead of silently editing exported invoices.
15. **Fast public documents**: server-rendered, < 60 KB, readable without JS, branded via Agent 11 tokens with
    light/dark for pages and print-safe light PDFs.
16. **Dispute evidence pack** (photos, checklist, GPS start/complete, signatures, acceptance record) one click for
    Agent 05.
17. **Keyboard-first phone-booking flow** for the native desktop app: caller-ID lookup (Agent 07), `N` new job,
    type-ahead services, `Ctrl+Enter` book; target < 60 s from ring to confirmed booking.
18. **On-site package change** on iPhone/worker app: swap/add services or subscription package mid-job with live
    total, customer approval signature and automatic invoice revision.
19. **Accessibility**: WA primary actions are large swipe/tap targets usable with gloves; full screen-reader labels;
    colour never the only status signal (status text + icon).
20. **Past-job backfill** made explicit ("mark completed?") with notification suppression `[S: llms]`.

**Budget tier notes** (affordable starter vs. gated):

| Starter (cheapest plan) — must have | Can be gated to higher plans |
|---|---|
| Single job ID, quotes, invoices, book-online button `[S: t04]`, auto invoice on booking | Optional line items / good-better-best |
| Worker app job flow: on my way + ETA SMS, start, checklist, before/after photos, complete, POS hand-off | Live map tracking page with streaming location (ETA text only on starter) |
| Partial payments, due terms, basic reminders | Advanced reminder cadences, late fees, scheduled sends |
| Offline mode & reliable photo upload (reliability is not a premium feature) | Photo sharing galleries to customers, perceptual-duplicate detection |
| Brand logo/colors on documents (Agent 11 basics) | Custom fonts, per-placement logo sizing, full copy overrides |
| Strict legal invoice numbering (compliance) | Supplemental invoice/export locks (accounting integrations) |
| Customer signature on quote acceptance | Signature on completion & upsell approvals, dispute evidence pack |
| — | Native desktop app phone-booking console, multi-crew split visits |

Starter targets: solo operator can go from "New → Quote" to sent in < 90 s; worker app usable on a mid-range Android
over 3G; a job's offline data ≤ 200 KB excluding photos.

---

## 13. Open questions

1. **Recurring occurrence numbering**: does Fieldd give each recurring occurrence its own job number, or one series
   number with suffixes? (We chose own number + series link.)
2. **When exactly is the invoice auto-generated** for CRM-booked appointments (t04 only states it for accepted+booked
   quotes)? Is it visible/sendable before completion?
3. **Is the invoice number literally identical to the job/quote number** in Fieldd's UI (e.g. "Invoice #1042"), or
   does it show the job ID elsewhere? Any prefixes?
4. Does Fieldd let a customer **accept without booking** when book-online is on? What status does the job show then?
5. **Quote expiry**: does Fieldd have expiry dates / declines / revisions at all? None are mentioned in research.
6. **Photo count aggregation** when multiple services each require N before/after photos: max or sum?
7. Are completion requirements **hard-blocking** in Fieldd's worker app, or warnings?
8. Is there an **"arrived"** state or geofence in Fieldd, or only on-my-way → start → complete?
9. What does the **live tracking** page show customers (map, worker photo, vehicle)? Does it expire?
10. Does POS "send an invoice" `[S: t16]` mark the job "complete & invoiced" immediately and email the customer?
11. Gift card appears under "Options" with discount/tax `[S: t16]`: does Fieldd treat it as a discount (reducing
    taxable base) or as a payment? (We treat as tender — confirm with Agent 05/06.)
12. **Multi-visit jobs** (one job over several days): supported by Fieldd? We model one appointment per job in v1
    with per-worker visits; multi-day may need `visits[]`.
13. **Legal invoice requirements** for the owner's markets (gapless numbering, credit notes vs. negative invoices,
    tax ID display) — which countries will the platform launch in?
14. **Data retention vs. erasure**: how long to keep photos and documents; how to honour customer erasure while
    keeping tax records?
15. Should workers see **prices** by default (Fieldd hint: worker POS shows amounts `[S: t16]`)?
16. **Quote email/SMS reminder defaults** in Fieldd's Settings → Communication → Email `[S: t04]` — which reminder
    types exist?
17. Does Fieldd support **deposits on quotes** (require deposit at acceptance) or only through booking payment settings
    `[S: t04]`?
18. **Estimator role**: is it a distinct permission set in Fieldd, or just a worker with quote permissions?
19. Should **cancellation of a paid job** default to refund or credit? (Agent 05/01 policy.)
20. Native apps (owner 2026-11-13): will the iPhone staff app be the same binary as the worker app with role-based
    surfaces, or separate? Affects offline scope for office users.
21. Is there a **customer approval step for on-site upsells** in Fieldd, or does the worker just add them?
22. Event naming: team must confirm aliases — `job.job.en_route` vs 03's `job.job.on_my_way` / 01's
    `job.worker.on_my_way`; `invoice.invoice.issued` vs 05's `job.invoice.finalized`; `job.job.cancelled` vs 05's
    `job.job.canceled`; `job.job.lines_changed` vs 03's `job.lines.changed` (Agent 10 to rule).

---

## 14. Verify against video

Concrete checks for the owner's walkthrough (and any Fieldd CRM screen recording):

**Job identity**
- [ ] Where is the job number shown on the job card, quote, invoice, email subject and PDF? Same number on all three?
- [ ] Any prefixes (Q-, INV-, #)? Is numbering sequential per business? Starting value?
- [ ] Do recurring occurrences each have their own number?
- [ ] Does search find a job by quote/invoice number?

**New menu & editor**
- [ ] Exact items in the New menu and their order (Appointment, Quote, Invoice, Recurring job, …).
- [ ] Quote editor: business info block contents; left/right column split; field names "Quote terms", "Book online".
- [ ] Right panel order: job address, preferred worker, customer assets, notify contacts — labels and controls.
- [ ] Is there a quote expiry/valid-until field? A message-to-customer field?
- [ ] Line item columns (qty? cost? duration?) and how add-ons attach to services; company add-on badge.
- [ ] "Options and payment" section layout: discount, tax, gift card; payment timing options wording.
- [ ] Preview screen: side-by-side email and document? PDF download?
- [ ] Send dialog: channels (email/SMS), recipients, editable message.

**Customer-facing documents**
- [ ] Quote page/email: buttons (Accept, Decline, Book online), terms display, signature?
- [ ] What happens after Accept: booking flow steps, deposit step, confirmation page text.
- [ ] Invoice page: pay button, partial payment, tip prompt, receipts, status badges.
- [ ] Branding on documents: logo placement, colors, fonts; dark mode on pages?

**Statuses**
- [ ] Full list of job statuses in the jobs-tab status filter and their colors.
- [ ] Payment status column values in the jobs list `[S: t08]`.
- [ ] Quote statuses in the quotes list; invoice statuses in the invoices list `[S: t08]`.
- [ ] Is there a "needs scheduling"/accepted state, "no-show", "on the way", "in progress" visible to office?

**Worker app**
- [ ] Day list layout: card contents, sorting, route card with "Start route" swipe `[S: t02]`.
- [ ] Job detail: order of sections; call/text/message buttons; navigate button.
- [ ] On my way: ETA shown/editable? Customer text content? Live tracking page look.
- [ ] Start job interaction (swipe vs button); timer display.
- [ ] Checklist UI: item types, required markers, global vs service grouping `[S: t06]`.
- [ ] Photo capture: before/after counters "x of N", gallery allowed?, upload indicator.
- [ ] Can the worker complete with missing photos/checklist? Error/warning text.
- [ ] POS after completion: tenders listed (new card, card on file, cash, check, send invoice) `[S: t16]`; tip screen.
- [ ] Upsell: how add-ons are added in POS before completion `[S: t07]`; price editable?
- [ ] Estimator quote creation in the app `[S: t04]`: same fields as CRM? Present-on-device acceptance?
- [ ] Offline behaviour: any offline banner, queued actions, photo retry UI.
- [ ] Route progress % and auto-close after last job `[S: t02]`.

**Settings**
- [ ] Settings → Communication → Email: quote/invoice styling options and reminder settings `[S: t04]` — list every toggle.
- [ ] Settings → Payments (right below): deposits, prepayments, pre-authorization `[S: t04]`.
- [ ] Any job/workflow settings (photo enforcement, signature, numbering).

**Customer side (swash.co walkthrough)**
- [ ] Does the customer portal list past quotes/invoices/jobs under the account? Same job number visible?
- [ ] On-my-way/arrival notifications as received by the customer (SMS text, link, map).
- [ ] Completion message with photos? Review request timing?
