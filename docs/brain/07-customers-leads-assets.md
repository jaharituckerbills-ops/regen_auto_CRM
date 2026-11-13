# 07 — Customers, Leads & Assets

> Agent 07 · Phase 1 "CRM brain" · written 2026-11-12 · status: draft for Agent 10 synthesis and
> for reconciliation against the owner's walkthrough video.
> Product name is a placeholder: "the platform". Nothing here copies Fieldd naming, copy, or UI;
> we replicate workflows and coverage only.

## 0. Conventions, sources and scope

### 0.1 Source key
Citations use short tags that map to research files (all read-only):

| Tag | File | Relevance to this module |
|---|---|---|
| `T18` | `research/transcripts/18_PVo5YzUbOpI.txt` | Add Customer: CSV vs single, profile links all interactions, tags |
| `D18` | `research/transcripts/18_PVo5YzUbOpI.description` | Add Customer: "taxes, discounts, and notes" on the customer |
| `T20` | `research/transcripts/20_-TpNhaNTQkM.txt` | Add Lead: scratch vs existing customer, magic data, lead board, convert, postcard QR |
| `D20` | `research/transcripts/20_-TpNhaNTQkM.description` | Lead Board, follow-ups |
| `T09` | `research/transcripts/09_xO4jahos3rE.txt` | People tab, profile contents, import/export, filters, global search, customer types on discounts |
| `T10` | `research/transcripts/10_p7TFZDchFbk.txt` | Leadboard pipeline, every action logged, lead forms, real-time abandoned attempts, funnel step, magic data |
| `D10` | `research/transcripts/10_p7TFZDchFbk.description` | "tracking leads who dropped off your booking page" |
| `T05` | `research/transcripts/05_0qNwovgNTqM.txt` | Customer portal accounts, assets, intake form, portal visit tracking, leads tab, booking sources |
| `D05` | `research/transcripts/05_0qNwovgNTqM.description` | chapter list incl. "Customer Asset Management" |
| `T04` | `research/transcripts/04_pASG8RTrhWc.txt` | Quote side panel: job address, preferred worker, customer assets, contact info to notify |
| `T19` | `research/transcripts/19_ZPf7JSlvIcw.txt` | Schedule job: attach/create customer, customer assets, notes, job tags |
| `T15` | `research/transcripts/15_Dh1Ib3a4psU.txt` | Book job: find or create customer, job address |
| `T14` | `research/transcripts/14_5zqqoRbV1DU.txt` | Recurring job: add/create customer, select job address, notes/tags/files |
| `T08` | `research/transcripts/08_w3D1W6oOaNY.txt` | Jobs filters by tags/customers; sidebar search finds customers |
| `T12` | `research/transcripts/12_4BwGvptaW6s.txt` | Drip trigger "new customer"; abandoned-booking follow-up text |
| `T01` | `research/transcripts/01_mP9YP49qDiI.txt` | New subscription: choose existing customer or "+" new |
| `T11` | `research/transcripts/11_8sT9cFazZAA.txt` | Service areas; out-of-area override by manual CRM booking |
| `T13` | `research/transcripts/13_C2EsjjXfOOE.txt` | People tab also holds workers |
| `T16` | `research/transcripts/16_G43PcI4hWOk.txt` | Card on file |
| `llms` | `research/site/llms.txt` | Rebuilt customer profiles, business customers, vehicles tab, VIN/plate, CARFAX, tire sizes, garage, leads board layouts, sales pipelines, QR booking options |

Tags: `[S: X]` = sourced from file X. `[I]` = inference (industry convention / reasonable design
choice). `[I-legal]` = inference about law/regulation — **not legal advice**; must be reviewed by
counsel before launch. Visual layout is `[I]` unless stated otherwise.

Source-quality notes:
- The research corpus for this module is thin: the four primary videos (18, 20, 09, 10) total
  ~7 KB of narration. Most field-level detail below is `[I]` and is the main target of the
  owner's walkthrough video (§14).
- `D04` (quote video description) contains chapter markers that belong to the booking-page video
  (05); treat chapter timings in `D04` as mislabeled.
- Video 18's narration says "Today, use customer tags…"; probably a transcription artifact for
  "Use customer tags". No meaning attached.

### 0.2 Module boundary (what 07 owns vs. neighbours)
| Concern | Owner | 07's role |
|---|---|---|
| Customer record, people, phones/emails, addresses, tags, notes, customer types, custom fields, consent ledger | **07** | owns |
| Intake-form *definition* (questions configured under online scheduling settings) | 01 [S: T05] | 07 stores *answers* on the profile |
| Customer self-service account (login identity, password/OTP, sessions) | 01 | 07 owns the link between an account and a customer record, and the matching/merge rules |
| Booking attempts (session telemetry on the booking site) | 01 | 07 turns attempts into leads; owns the lead |
| Assets (vehicles, properties, equipment), garage, vehicle lookup, asset service history projection | **07** | owns |
| Jobs / appointments / quotes / invoices | 03/04 | reference `customerId`, `addressId`, `assetIds`, `notifyContactPointIds`; 07 projects history from their events |
| Cards on file, transactions, balances | 05 | 07 displays summaries on profile; never stores PAN |
| Subscriptions, gift cards, discount codes | 06 | 07 displays; 06 references `customerId`; discounts may target `customerTypeId` [S: T09] |
| Notifications, campaigns, drips, inbox, reviews, reports | 08 | 07 exposes segments + consent state; consumes STOP/unsubscribe/bounce events |
| Workers, roles, permissions | 09 | 07 declares permission keys; 09 owns the RBAC matrix |
| Leads, pipelines, stages, lead sources, lead activity log, enrichment | **07** | owns |
| Postcard campaigns (design/send) | 08 | 07 consumes QR-scan events and creates leads |
| QR code / short link generation | 01 | 07 consumes scan events with attribution |

### 0.3 Glossary (product vocabulary; final names TBD)
- **Customer** — the account the business serves; a household or a business. Fieldd's rebuilt
  model calls this a "contact" holding "multiple phone numbers, people, and addresses"
  [S: llms]. We use **Customer** in the UI and data model [I].
- **Person** — a human attached to a customer (primary, spouse, site contact, fleet manager,
  accounts payable) [S: llms "people … per contact"; roles are I].
- **Contact point** — one phone number or email address, optionally tied to a person.
- **Address** — a service or billing location belonging to a customer.
- **Asset** — "the items or locations that your services are performed on like cars, homes, or
  equipment" [S: T05]. **Vehicle** is a first-class asset subtype [S: llms].
- **Garage** — the set of a customer's vehicles [S: llms "multiple vehicles per customer in a garage"].
- **Lead** — a prospective piece of business tracked through a pipeline (lead board) until it
  becomes a booking (won) or is closed (lost) [S: T20, T10].
- **Booking attempt** — a visitor's in-progress or abandoned session on the booking site [S: T10].
- **Enrichment** — filling in missing contact data for a lead (Fieldd brands this "magic data")
  [S: T20, T10, T05]. We will not use that name.

---

## 1. Summary

This module is the platform's memory of *who* the business serves and *what* it services.
A **customer profile** is the single record every booking, quote, invoice, payment,
subscription, message and review links to, so reports never double-count and staff always see
the full history [S: T18]. Profiles hold service history, ratings, notes and account status
[S: T09], multiple people, phone numbers and addresses, and support business customers
[S: llms]. Customers arrive one at a time, by CSV import from a previous system [S: T18, T09],
inline while booking a job [S: T15, T19], or by self-signup on the booking site [S: T05]; all
paths converge through one matching/dedupe engine. **Assets** are the things work is done on;
for an auto-services brand the vehicle is the star: VIN/plate/make-model-year lookup, photos,
colors, tire sizes, mileage, and a per-vehicle service timeline [S: llms]. **Leads** are the
sales pipeline: created by hand, from address only with optional enrichment, from lead forms,
from postcard QR scans and — automatically and in real time — from people who started but did
not finish an online booking, labelled with how far they got [S: T20, T10]. A lead converts to
a booking without leaving the dashboard [S: T20]. Consent (SMS/email opt-in) is captured here as
an auditable ledger so Marketing (08) can only reach people who may lawfully be reached.

---

## 2. Roles

Role names follow the team convention; the RBAC matrix itself is owned by Agent 09. Permission
keys 07 declares are listed in §7.12. Owner/admin/worker distinctions are [S: T03, T13] for
existence of roles; the specific grants below are [I].

| Role | Customers | Assets | Leads | Import/Export | Merge/Delete | Enrichment |
|---|---|---|---|---|---|---|
| Owner | full | full | full | yes | yes, incl. erase | yes (pays) |
| Admin | full | full | full | yes | merge yes; erase only if granted | if granted |
| Manager | create/edit, no erase | full | full on all leads | export if granted | merge if granted | no (default) |
| Foreman | view all; edit customers on own crew's jobs | edit on own jobs | view/edit own + crew leads | no | no | no |
| Worker (team app) | view customers of jobs assigned to them, limited fields (name, service address, access notes, job contacts, assets on the job) during a window around the job | view + add photos/odometer/notes on job assets | create lead from field (door-knock/upsell), see only own | no | no | no |
| Estimator / sales (worker with sales permission) | view all, create | view | full on own; view all | no | no | if granted |
| Customer (booking site / customer app) | own profile: people, contact points, addresses, intake answers, consent toggles | own garage: add/edit/archive, photos | none (never sees a lead) | own data export (privacy request) | request deletion | no |
| System | projections, auto-leads, dedupe suggestions, consent from keywords | VIN decode, last-service projection | auto-create/advance/close leads | scheduled exports (gated) | auto-merge only when rule = exact verified match **and** setting on | provider calls |
| AI assistant (MCP / built-in assistant) | acts **as the authenticated user**, same permissions; Bookings-scope tokens = customer scope only [S: llms] | same | same | export blocked by default for AI scope [I] | merge requires human confirm [I] | blocked [I] |

Notes:
- llms.txt says Fieldd's CRM MCP has "full business-level access" while the Bookings MCP is
  "customer-level access only … keeps sensitive business data private" [S: llms]. Our API surface
  (§8) tags every command with the scope it is valid for so the same split is possible [I].
- Worker visibility window default: from job-assigned until 24 h after job completion [I].
  Gate codes / lockbox codes visible only from "on my way" until completion [I].

---

## 3. Screens

Layout statements are `[I]` unless cited. "Desktop" = ≥1024 px CRM; "Mobile" = CRM on phone
(responsive web) unless "team app" is stated.

### 3.1 People tab → Customers list
**Nav:** primary sidebar → People → Customers. Fieldd's People tab is "your centralized list of
customers and workers" [S: T09, T13]. We keep Customers and Workers as sibling sub-tabs (workers
UI owned by 09) [I]. Vehicles (assets) is its own tab [S: llms "a vehicle tab"]; Leads is its
own tab [S: T05 "leads tab", T20 "lead board"].

**Layout regions [I]:**
1. Header: title, count ("1,284 customers"), primary button **New customer** with split menu
   *Single* / *Import CSV* [S: T18 "upload a CSV … select single"], secondary **Export** (download
   icon — Fieldd uses a download icon "throughout" [S: T09]).
2. Toolbar: search box (scoped to customers), filter chips, saved views dropdown, column picker,
   density toggle.
3. Table (virtualized). 4. Bulk-action bar (appears on selection). 5. Right-side preview drawer
   (opens on row click; full profile on Enter/double-click/"Open").

**Default columns [I]:** checkbox · Name (display name + business badge) · Primary phone ·
Primary email · Primary address (city) · Service area · Tags (chips, max 3 + "+n") · Last
service date · Next job · Jobs completed · Lifetime revenue · Balance due · Status · Source ·
Created. Optional: customer type, owner/assigned rep, subscription status (from 06), vehicles
count, rating, consent badges (SMS/email marketing), portal account (yes/no), custom fields.

**Filters [S: T09 "filters and the global search"; specific filters I]:**
| Filter | Type | Notes |
|---|---|---|
| Tags | multi-select, any/all/none | tags group customers "across several different service areas" [S: T18] |
| Service area | multi-select | computed from primary address (§5.6) |
| Status | multi | active/inactive/blocked/archived |
| Customer kind | individual / business | [S: llms business customers] |
| Customer type | multi | used by discounts [S: T09] |
| Last service date | relative ("more than 90 days ago", "never") or absolute range | feeds "haven't booked in a while" promos [S: T10] |
| Next job | has / none / within N days | |
| Jobs completed | numeric range | |
| Lifetime revenue | money range | |
| Balance due | > 0 | from 05 |
| Has card on file | yes/no | from 05 [S: T16] |
| Subscription | active / past due / none | from 06 |
| Has portal account | yes/no | |
| Marketing consent | SMS granted / email granted / none / revoked | needed by 08 |
| Source | multi | import, CRM, booking site, lead conversion, QR, API/AI |
| Created date | range | |
| Assigned rep / preferred worker | multi | |
| Vehicle make/model/year | | joins to assets |
| Custom field | per definition type | |

**Bulk actions:** add/remove tags; set customer type; set status; assign rep; export selection;
add to marketing audience (hands a static segment to 08); merge (exactly 2–5 selected);
archive. Delete/erase is **not** a bulk action [I] (one at a time, owner only, typed
confirmation).

**States:**
- Empty (no customers ever): illustration-free panel [I, ground rule 1], three CTAs: *Add a
  customer*, *Import from CSV* ("switching from another CRM?" [S: T18]), *Share your booking
  link* (customers appear automatically) [I].
- Empty (filters exclude all): "No customers match" + *Clear filters*.
- Loading: skeleton rows (10); keep previous results visible while a new filter loads (no
  flash) [I].
- Error: inline banner with retry; table keeps last good page.
- Import running: sticky progress banner "Importing 3,402 rows… 41%" linking to import job.

**Mobile:** table collapses to cards (name, phone tap-to-call, city, last service, tags);
filters move to a bottom sheet; bulk select via long-press; search pinned top.

**Sorting:** any sortable column; default *Last activity desc* [I]. Stable secondary sort by
`id` to keep pagination deterministic.

### 3.2 Customer profile
**Route:** `/customers/:id`. Opened from list, global search [S: T08 search finds customers],
any job card, invoice, transaction, lead, chat thread.

**Header [I]:** avatar/initials (business icon for business customers) · display name ·
company name (business) · status pill · customer-type pill · tags (editable inline) · primary
phone (tap-to-call/text) · primary email · primary address (click → map) · badges: *Subscriber*
(06), *Card on file* (05), *Portal account*, *Marketing: SMS ✓/✗, Email ✓/✗*, *Do not service*
(blocked), *Tax exempt* [S: D18 "taxes"].
**Header actions:** New booking (pre-filled) · New quote · New invoice · New subscription
[S: T01] · Send message (opens 08 inbox) · Add lead · ⋯ menu (Edit, Merge with…, Export
customer data, Archive, Block, Erase personal data).

**KPI strip [I]:** Lifetime revenue · Jobs completed · Avg ticket · Last service (date + "N days
ago") · Next job · Balance due · Avg rating (see open question Q3).

**Tabs [S: T09 "service history, ratings, notes, and account status"; layout I]:**
| Tab | Content | Owner of data |
|---|---|---|
| Overview | pinned notes, upcoming jobs, last 5 jobs, vehicles summary, open leads, open quotes, intake answers summary | mixed |
| Service history | every job (quote→appointment→invoice share one job ID [S: T04]) with date, services, asset(s), worker, status, total, rating; filter by asset/address/status | 03/04 data, 07 projection |
| Garage / Assets | vehicle cards (photo, color swatch, YMM, plate, last service, mileage), properties, equipment; *Add vehicle* | 07 |
| People & contact info | people list, each with phones/emails, roles, notification preferences; addresses list with labels, access notes, service area | 07 |
| Payments | cards on file (brand + last4 + expiry only), transactions, invoices, balance, gift cards | 05/06 |
| Subscriptions | active/past packages, next charge | 06 |
| Messages | SMS/email/push history, campaign membership | 08 |
| Notes & files | notes (pinned, internal vs. worker-visible), attachments, photos from jobs | 07 + 04 |
| Intake form | answers to the business's intake form, with "last updated by customer on …" [S: T05] | 07 stores; 01 defines |
| Leads | leads attached to this customer (open + closed) | 07 |
| Activity | immutable audit log: created, edited (field-level diff), merged, consent changes, imports | 07 |

**Account status** [S: T09] is shown in the header and editable in the ⋯ menu (§6.1).

**Inline editing:** click-to-edit on header fields; optimistic UI with version check (§10.9);
conflict toast "Updated by Sam 10 s ago — reload / keep mine".

**Mobile:** header compresses to name + quick actions row (call, text, book, navigate); tabs
become a horizontally scrolling segmented control; KPI strip becomes 2×3 grid.

**States:** loading skeleton; 404 "Customer not found or merged" → if merged, auto-redirect to
survivor with banner "This customer was merged into …" [I]; 403 for workers outside visibility
window ("You can see this customer while you have a job with them").

### 3.3 New / edit customer (single)
**Entry points:** People → New customer → Single [S: T18]; global "New" menu [I]; inline "+"
inside booking composer (03) [S: T15, T19], quote/invoice (04) [S: T04], subscription (06)
[S: T01], lead conversion (§4.20).
**Form (drawer on desktop, full screen on mobile) [fields I unless cited]:**

| Field | Type | Validation | Default |
|---|---|---|---|
| Customer kind | segmented: Individual / Business | required | Individual |
| First name | text ≤ 80 | one of first/last/company required | — |
| Last name | text ≤ 80 | | — |
| Company name (business) | text ≤ 160 | required if kind = business | — |
| Phone(s) | repeatable: number + label (mobile/home/work/other) + "primary" | parse to E.164 with business default country; reject if unparseable unless "save as entered" override (flagged `unverified_format`) | label mobile |
| Email(s) | repeatable: address + label | RFC 5322 practical subset; lowercase domain; warn on common typos (gmial.com) | — |
| SMS job updates | toggle per phone | transactional; on by default when a mobile number is given [I-legal, §5.9] | on |
| Marketing SMS consent | checkbox + method picker (verbal/paper/web) | **unchecked by default**; requires method; staff attests | off |
| Marketing email consent | checkbox + method | unchecked by default in strict-consent countries; configurable per business country (§5.9) | off |
| Address(es) | repeatable; autocomplete → structured fields; label (Home/Work/Shop/Site); access notes; gate code (secret) | geocode on save; warn if outside all service areas (allowed — CRM override [S: T11]) | — |
| Billing address | "same as service" toggle | | same |
| Tags | multi-select with create | ≤ 50 tags per customer | — |
| Customer type | select | | business default |
| Tax | "Tax exempt" toggle + certificate upload + exempt-until date | [S: D18 "taxes"] semantics owned by 05/02 | off |
| Default discount | select from 06 discounts | [S: D18 "discounts"] | none |
| Preferred worker(s) | multi-select (09) | [S: T04 "preferred worker" on quote] | — |
| Assigned rep | worker select | | creator if sales role |
| Source | select (lead sources list) | | "Manual entry" |
| Notes | rich text (internal) | ≤ 10 k chars | — |
| Custom fields | per settings | per type | — |
| Add a vehicle now | inline mini vehicle form (§3.10) | | collapsed |

**Live duplicate check:** as phone/email/name+address are typed, a debounced (300 ms) match
query shows "Possible match: Jane Doe · (512) 555-0101 · last service Mar 3 — *Use this
customer* / *Not the same*" (§10.2). Hard block only on identical **verified** email or phone
already on an active customer, with override for owner/admin ("Create anyway" logs reason) [I].
**Save actions:** *Save* · *Save & book* (opens 03 composer with customer) · *Save & add
another*. Side effects: §4.1.

### 3.4 People, contact points & addresses editor
Inside profile tab "People & contact info" [S: llms rebuilt profiles].
- People list: name, role chips (Primary, Billing, Site contact, Fleet manager, Spouse, Other),
  contact points beneath each. Exactly one Primary person; at most one Billing person [I].
- Contact point row: value (formatted), label, line type (mobile/landline/VoIP/unknown from
  carrier lookup if enabled — §5.9), status (valid, bounced, undeliverable, unverified),
  per-channel toggles: *job updates* (transactional) and *marketing*, consent history link.
- "Notify on jobs" per contact point: which contacts get confirmation/reminder/on-my-way
  messages; job-level override exists on the job (quote panel has "contact info to notify the
  customer" [S: T04]).
- Addresses list: label, formatted address, map pin (draggable to correct geocode), service
  area chip, access notes, gate code (masked •••• with reveal permission), parking/pets notes,
  property asset link (§3.9), primary/billing flags, "jobs at this address" count.
- Actions: add, edit, set primary, archive (never hard-delete an address used by a job — jobs
  keep a snapshot anyway, §5.5), move person to another customer (business re-orgs).

### 3.5 Import wizard (CSV)
**Entry:** People → New customer → Import CSV [S: T18]; also People → Import [S: T09]. Fieldd
claims "one click" [S: T18]; we keep a fast path (auto-map + one confirm) but never skip
validation [I].

| Step | Content | Notes |
|---|---|---|
| 1 Upload | drag-drop / file picker; accepts .csv, .tsv, .txt; .xlsx converted client-side (gated, see §12) ; max 50 MB / 200 k rows [I]; template download per source system (generic, Jobber-style, Housecall-style, Square customers export) | encoding sniff (UTF-8, UTF-8-BOM, UTF-16, Windows-1252), delimiter sniff |
| 2 Map columns | left: detected headers + 3 sample values; right: target field dropdown; auto-mapped via synonyms (§10.4); "ignore column"; "split full name"; multiple phone/email/address columns → multiple contact points; vehicle columns (VIN, year, make, model, plate, color) → assets; tags column with delimiter choice; custom field targets; "create new custom field from column" | save mapping as preset |
| 3 Options | dedupe strategy: *Skip existing* / *Update existing (fill blanks only)* / *Update existing (overwrite)* / *Always create new*; match keys (email, phone, external ID, name+address); default country for phones; tag all imported rows (auto-suggest "imported-2026-11-12"); **consent attestation** (see below); send welcome drip? default **off** (prevents blasting the whole list via "new customer" drip trigger [S: T12]) | |
| 4 Validate (dry run) | full validation server-side without writing; summary: N new, N updates, N duplicates within file, N errors, N warnings; sample of each; per-row error list downloadable | nothing written yet |
| 5 Import | runs as background job; progress; cancel (stops after current batch; already-committed batches stay, and are listed) | idempotent batches |
| 6 Results | counts; **partial failure report** CSV (original row + error column); undo import (within 7 days, removes created records that have no jobs since; reverts updates via stored before-images) [I] | |

**Consent attestation (step 3):** dropdown per channel: "These contacts have given **express
written consent** for marketing texts" / "…have opted in to marketing emails" / "Not sure /
transactional only" (default). If attested, every imported consent record stores
`method = import_attested`, the attesting user, timestamp, file hash, and the text of the
attestation [I-legal]. If a CSV column like `sms_opt_in` exists it can be mapped, still under
attestation.

### 3.6 Export dialog
Entry: download icon on list (exports current filter or selection) [S: T09 "export customer and
worker data", "download icon throughout"]. Options: format CSV (UTF-8 with BOM for Excel) [I];
columns: current view / all fields / custom pick; include people & contact points as (a) first
of each type in columns or (b) one row per contact point; include assets (separate file in a
zip) ; include consent status. Large exports (> 5 k rows) run async → notification + link that
expires in 24 h [I]. Every export logged (who, when, filter, row count) because exports are
the #1 data-exfiltration path [I]. Permission `customers.export`.
CSV-injection defence: prefix cells starting with `=`, `+`, `-`, `@`, tab, CR with `'` [I].

### 3.7 Duplicates & merge
- **Duplicates queue** (People → Customers → "Possible duplicates (12)"): pairs/clusters with
  match score and reasons ("same phone", "same email", "similar name + same address") [I].
  Actions: Merge, Not a duplicate (suppresses that pair forever), Snooze.
- **Merge screen:** 2–5 records side by side; choose survivor (default: oldest with most jobs);
  field-by-field radio for scalar fields; union for lists (people, contact points, addresses,
  tags, notes, assets) with de-duplication of identical contact points; preview of what moves
  (jobs, invoices, payments, subscriptions, cards, leads, messages, portal accounts). Merge is
  reversible for 30 days [I] (§10.3).
- Conflicts that block merge: two different active portal accounts with verified different
  emails → must pick one login, other becomes secondary login identity or is detached [I];
  two active subscriptions (allowed, warned); different tax-exempt states (pick).

### 3.8 Vehicles tab (Assets list)
**Nav:** primary sidebar → Vehicles [S: llms "a vehicle tab filterable by last service date"].
For non-auto businesses the tab label follows the business's asset type setting ("Properties",
"Equipment", "Assets") [I].
**Columns [I]:** photo thumb · Year Make Model Trim · Color swatch + name · Plate (region) ·
VIN (last 8, full on hover) · Owner (customer) · Last service date [S: llms] · Mileage (latest) ·
Services count · Next job · Tags · Status.
**Filters:** last service date (relative/absolute, "never") [S: llms]; make; model; year range;
body type; color; customer tags; service area of owner; has open lead; mileage range; tire size;
status (active/sold/archived).
**Search:** VIN (full or last 6+), plate (normalized), YMM text, owner name/phone.
**Bulk:** tag, export, "create campaign audience from owners" (→ 08), archive.
**Use case:** "every 2015–2019 F-150 not serviced in 120 days" → owners → SMS promo (consent
permitting) [I].
**Mobile:** card list with photo, YMM, plate; tap → vehicle profile.

### 3.9 Asset profile (vehicle detail)
**Header:** hero photo carousel (customer- and worker-uploaded) [S: llms "vehicle photos"];
YMM + trim; color swatch [S: llms "colors"]; plate + region; VIN with copy button and decode
status; owner link (customer) + co-owners/drivers (people of the customer); status.
**Sections [I unless cited]:**
- *Specs:* body type, engine, drivetrain, fuel, transmission, doors, GVWR class (decoded),
  **factory tire size front/rear + custom tire size override** [S: llms "custom factory tire sizes"],
  paint code (optional), interior color, size class for pricing (sedan/SUV/truck/van — used by
  catalog 02 for vehicle-size pricing, open question Q6).
- *Mileage:* latest odometer + unit; chart of readings over time; "estimated current mileage"
  from trend [I].
- *Service history:* every job that included this asset: date, services, add-ons, worker,
  odometer at service, before/after photos (from 04), notes, amount; filter by service
  [S: llms "service history per asset" via "vehicle history"].
- *Vehicle history report:* CARFAX (incl. CARFAX Canada) report entries in vehicle history
  [S: llms] — external paid/partner integration; ours is an optional connector (§12, Q8).
- *Recalls:* open recalls lookup (NHTSA public recall data is free in the US [I]).
- *Notes & photos:* condition notes (scratches, dents — pre-existing damage log protects the
  business in detailing disputes [I]).
- *Upcoming:* next scheduled jobs for this vehicle; "service due" reminders (e.g. oil change
  every 5,000 mi / 6 mo) — rule owned by 08 reminders, data from 07 [I].
- *Actions:* Book service for this vehicle; Transfer to another customer (sold); Mark sold /
  archive; Merge duplicate vehicles; Download history (PDF) for the owner.

Non-vehicle assets use the same shell with type-specific spec sections: **Property** (linked
address, property type, sq ft, stories, bedrooms/bathrooms, lot size, pool, pets, surfaces) and
**Equipment** (manufacturer, model, serial, install date, warranty until, location address)
[I; categories from S: T05 "cars, homes, or equipment"].

### 3.10 Add / edit vehicle (lookup)
Used from: profile Garage tab, booking composer (03), quote (04) [S: T04 "customer assets"],
job details [S: T19], booking site (customer adds own asset; UI by 01) [S: T05], team app.
**Lookup modes** [S: llms "vehicle lookup by make, model, and year, or by VIN and plate"]:
1. **Year → Make → Model → Trim** cascading selects with type-ahead; data from a vehicle
   catalog (free NHTSA vPIC make/model lists for US [I]; commercial catalog optional).
2. **VIN:** 17-char input, uppercase, auto-strip spaces/dashes; check-digit validation (§10.6);
   decode on 17th char; team app offers **camera barcode scan** (door-jamb / windshield VIN
   barcode, Code 39 / DataMatrix) [I].
3. **Plate:** plate + state/province → VIN → decode. Requires a paid plate-to-VIN data
   provider [I]; disabled in budget tier; show "Plate lookup not available on your plan — enter
   VIN or year/make/model" (Q7).
**Fields:** year (1900…current+1), make, model, trim (optional), color (name + swatch picker
with common automotive palette + custom hex) [S: llms colors], plate, plate region, VIN,
nickname ("Mom's Highlander"), odometer + unit, factory tire size(s) (auto-filled when decoder
provides, else picker), custom tire size, photos (up to 10, client-side resized to ≤ 2048 px,
EXIF GPS stripped [I]), notes, size class.
**Validation:** at least one of {VIN, year+make+model, nickname}; VIN check digit (warn, not
block, for pre-1981 / non-NA VINs); duplicate VIN in this business → "This VIN belongs to
<customer>. Transfer it?"; duplicate plate+region → warn.

### 3.11 Leads board
**Nav:** primary sidebar → Leads (Fieldd: "lead board"/"leadboard"/"leads tab"
[S: T20, T10, T05]). Fieldd calls it "your visual sales pipeline" [S: T10]; Start plan includes
"sales pipelines" [S: llms]; Oct-2026 update added "new layouts to view and sort leads" [S: llms].
**Layouts [I for exact set]:**
1. **Board (kanban)** — columns = stages; cards draggable between columns; column header shows
   count + summed estimated value; WIP collapse for Won/Lost.
2. **List/table** — sortable columns, inline stage change, bulk actions.
3. **Funnel / abandoned bookings** — booking-site attempts grouped by furthest step reached
   (§3.15) [S: T10 "exactly how far a customer made it through the booking flow"].
4. **Map** — pins for leads with addresses (door-knocking / postcard neighbourhoods) [I].
5. **Calendar of follow-ups** — leads by next follow-up date [I].
**Sorting:** newest, oldest, last activity, next follow-up, estimated value, stage age ("days in
stage"), score, source [I].
**Filters:** pipeline, stage, source (manual, address-only, lead form, booking site abandoned,
postcard QR, QR (other), referral, phone call, AI/API, import), owner, tags, created range,
follow-up due (overdue/today/this week), service interest, service area, has contact info
(phone/email/none), enriched (yes/no), consent status.
**Card content:** name (or address if address-only), source icon, interested services, vehicle
hint (e.g. "2018 Civic"), est. value, owner avatar, last activity relative time, follow-up due
badge (red when overdue), funnel step badge for abandoned attempts ("Reached: Time selection"),
"Live" pulsing dot while a booking attempt is still active [S: T10 "in real time"; visual I].
**Actions:** New lead, drag to stage, quick actions on card hover (call, text, email, log
activity, convert to booking, mark lost), bulk assign/tag/stage/lost, pipeline switcher,
board settings (stages).
**States:** empty board → explain the four lead sources and link to booking-site setup (01),
postcard campaigns (08), *New lead*. Board loading skeleton per column. Realtime updates via
subscription (§8.4): new abandoned-attempt leads slide in at top of "New" column.
**Mobile:** board becomes a stage selector + vertical list (no drag); swipe card right = advance
stage, left = mark lost (with undo) [I].

### 3.12 Lead detail drawer
Right drawer (desktop) / full screen (mobile).
- Header: lead title, stage dropdown, owner, est. value, source + source detail (campaign name,
  QR code name, booking-site attempt ID, form name), created/last activity, *Convert to booking*
  primary button [S: T20], ⋯ (merge, mark lost, delete, attach to customer).
- Contact block: attached customer (link) or lead-local contact snapshot (name, phones, emails,
  address) with *Create customer* / *Attach to existing* [S: T20]; enrichment status &
  provenance per field ("phone from <provider>, 2026-11-10, confidence medium") (§5.8).
- Interest: services/add-ons of interest (from catalog 02), vehicle/property hint, preferred
  dates, notes, quoted amount, related quote (04).
- Funnel panel (booking-site leads): steps reached with timestamps, cart (services/add-ons,
  price shown), selected slot (if any), device, referrer/UTM, booking source [S: T05 "booking
  source"], session count.
- **Activity timeline** — every action logged [S: T10 "Every lead action is logged here"]:
  created, field edits, stage moves, calls (logged/click-to-call), texts/emails (from 08),
  notes, follow-up set/done, enrichment requested/result, attempt progress, QR scans, quote
  sent/viewed/accepted, converted, lost/reopened. Composer at bottom: note / log call / log
  visit / set follow-up / send SMS / send email.
- Follow-up: date-time + assignee + reminder channel; overdue highlight.

### 3.13 New lead form
**Three modes** [S: T20]:
1. **From scratch** — first/last name, phone(s), email(s), address, services of interest,
   vehicle hint, source, owner, stage (default first open stage), est. value, follow-up date,
   notes, tags, consent checkboxes (same rules as customers).
2. **Attach to existing customer** — customer search; contact block becomes read-only link;
   choose which address/asset the lead concerns.
3. **Address only** — address autocomplete only; optional *Find contact info* button
   (enrichment, gated and permissioned §5.8). Lead is valid with no phone/email; it is
   "postal-only" until contact info is added.
Live duplicate check against customers **and** open leads (§10.2): "An open lead already exists
for this phone (stage: Contacted, owner: Ana)".

### 3.14 Convert lead → booking (inline)
"When it comes time to turn the lead into a booking you can do so all without leaving the …
dashboard" [S: T20]. Implementation [I]: *Convert to booking* opens Agent 03's booking composer
in a drawer over the lead board, pre-filled with: customer (existing, matched, or "new from
lead"), address, asset(s), services of interest, cart and selected slot from the abandoned
attempt (if still available, else first available [S: T15]), discount code from campaign
attribution (06), notes. On *Book now* the lead closes Won atomically with the job creation
(§4.20). Also *Convert to quote* (04) keeps the lead open in "Quoted" stage [I].

### 3.15 Abandoned bookings / funnel view
Layout 3 of the board. Columns or bar chart of funnel steps (step list owned by 01, e.g.
Landing → Service area check → Service selected → Add-ons → Asset/vehicle → Date/time → Contact
details → Payment → Confirm) [step names I]. Each attempt row: furthest step, time of last
activity, contact captured (yes/no), cart value, "Live" indicator, follow-up status (drip
enrolled? [S: T12 text "a couple of hours later"]). Fieldd says contact details are available
"with magic data" even if they dropped off before confirming [S: T10]; our lawful approach is
in §5.8/§5.10 — we show contact details **only if the visitor entered them** in our form after
a notice-at-collection, not via third-party de-anonymization.
Metrics strip: visits (portal visits tracked automatically [S: T05]), attempts, attempts with
contact, conversions, recovered (abandoned → later booked), recovery rate.

### 3.16 Settings → Customers, Assets & Leads
- **Customers:** customer types; tags (rename, merge, color, delete with reassignment);
  custom fields (type: text, number, date, select, multi-select, boolean, phone, URL; required
  on create? shown on booking site? worker-visible?); default country; display-name format;
  duplicate rules (which keys, auto-merge on/off); visibility window for workers; data retention
  (archive after N months inactive, erase on request flow).
- **Assets:** enabled asset types (vehicle/property/equipment/custom); asset label wording;
  required on booking? (01/03 contract); vehicle size classes; plate/VIN lookup connectors;
  vehicle history connector (CARFAX-like, gated); mileage unit default (mi/km).
- **Leads:** pipelines (multiple, e.g. "Residential", "Fleet"); stages per pipeline (name,
  order, type open/won/lost, probability %, "rotting after N days"); lead sources (system
  sources locked + custom); lost reasons; auto-assignment rule (round-robin among sales users,
  by service area, or unassigned); abandoned-attempt settings (inactivity minutes, create lead
  only if contact captured vs. always, auto-close on booking); enrichment (provider, monthly
  cap, purpose attestation).
- **Consent:** disclosure text versions per channel (shown on booking site, lead forms,
  imports); business country/region presets (US TCPA/CAN-SPAM, Canada CASL, UK/EU, AU) [I-legal].

### 3.17 Global search (customer/lead/asset provider)
Fieldd: "search tool in the sidebar. Whether it's a job number, invoice, staff, or customer"
[S: T08]; "filters and the global search" [S: T09]. 07 provides the **customers, leads and
assets search provider** to the global search (shell likely owned by Agent 10 / shared UI) [I].
Matches on: name (prefix, fuzzy), company, phone (any 4+ consecutive digits, ignoring
formatting), email (prefix/local part), address (street number + name), VIN (full / last 6+),
plate, tag, customer external ID. Results grouped by type with keyboard navigation; recent
items when empty. Latency target p95 < 150 ms server, < 300 ms perceived (§12).

### 3.18 Customer-facing surfaces (data contract; UI owned by 01)
Booking site account area [S: T05]: profile (name, phones, emails), addresses, **assets
(garage)** — "Once an asset is added, it's saved to their profile so they don't need to re-enter
the information when booking again" [S: T05]; intake form answers which they "can log in to
update … later" [S: T05]; communication preferences (consent toggles); rebook previous services
[S: T05] (reads 07 service history). 07 exposes customer-scope APIs (§8.3) and enforces field
whitelists (customer cannot edit tags, notes, status, customer type, tax exemption).

### 3.19 Team app (worker) views
- Job → Customer card: display name, job contacts (tap to call/text through masked relay if 08
  supports it [I]), service address + access notes, gate code (time-boxed), assets on this job
  with photo/color/plate (helps find the car in a parking lot [I]), customer notes flagged
  worker-visible, intake answers flagged worker-visible.
- Asset quick actions: record odometer, add photo, add condition note, scan VIN, add vehicle
  (customer brought a second car — "upsell on site").
- Create lead from field: "Neighbour asked for a quote" → address-only or scratch lead, owner =
  worker, source = "Field referral" [I].
- Offline: job-day customer/asset data cached on device; edits queued (§10.10).

---

## 4. Workflows

Notation: **W** = workflow; "→ event" = domain event emitted (catalog in §9); all writes in a
step are one transaction unless stated; every command carries an idempotency key (§10.8).

### W1. Create a customer (single) [S: T18 "select single and fill in the fields"]
1. User opens New customer → Single; form loads defaults (customer type, country, tags preset).
2. While typing, client calls `customers.findMatches` (debounced) and shows candidates (§10.2).
3. On Save: server normalizes (§10.1), re-runs matching inside the transaction (race with a
   simultaneous portal signup is possible), enforces hard-block rule for identical verified
   contact points.
4. Writes `Customer`, `Person` (primary), `ContactPoint`s, `Address`es (geocode async if
   provider slow: address saved with `geocodeStatus = pending`), `TagAssignment`s, `Note`,
   `ConsentEvent`s (one per granted channel+purpose, with method + actor), optional `Asset`s,
   `AuditEntry`.
5. → `customer.customer.created` (payload: id, kind, source, tags, serviceAreaId, consent
   summary, `createdVia = crm`), → `customer.consent.changed` per consent event,
   → `asset.asset.created` per vehicle.
6. Async: geocode → `customer.address.geocoded` → service area resolution (02 API) → update
   `serviceAreaId` projection.
7. Agent 08 drip trigger "new customer" [S: T12] fires off `customer.customer.created` unless
   payload `suppressAutomations = true` (imports default true).
8. UI: toast + navigate to profile (or to booking composer on *Save & book*).

### W2. Create customer inline (booking composer, quote, invoice, subscription)
Same command as W1 invoked by 03/04/06 UI with `createdVia = booking_composer | quote | invoice
| subscription` [S: T15, T19, T04, T01]. The host flow receives `customerId`, `addressId`,
`personId`, `contactPointIds` back and continues. If the host flow is abandoned, the customer
stays (it is real data the user typed) [I].

### W3. CSV import [S: T18, T09]
1. Upload: client computes SHA-256 of file; server stores file in object storage
   (`ImportJob.status = uploaded`). Same hash uploaded within 24 h → warn "This file was already
   imported on … (job #…)" [I].
2. Sniff encoding/delimiter, parse header + first 50 rows → `parsed_preview`.
3. Auto-map headers (§10.4); user adjusts; mapping saved on job (and optionally as preset).
4. Options + consent attestation saved.
5. Dry run (`ImportJob.status = validating`): stream all rows; per row: normalize, validate,
   resolve in-file duplicates (by match keys, first occurrence wins, later ones become
   "merged into row N" or errors per option), match against existing customers; compute action
   per row: create | update | skip | error; store per-row outcome. → `customer.import.validated`.
6. User reviews; clicks Import → `status = running`. Rows processed in batches of 500 in
   separate transactions; each batch keyed `importJobId:batchNo` so a worker crash/retry never
   double-creates (§10.8). Per-row before-image stored for updates (for undo).
7. Each created customer emits `customer.customer.created` with `createdVia = import`,
   `importJobId`, `suppressAutomations` per option. Event fan-out is batched (one
   `customer.import.batch_committed` event with IDs) to avoid flooding 08 [I].
8. Geocoding queued at low priority with provider rate limiting.
9. Completion: `status = completed | completed_with_errors | failed | cancelled`; results
   report CSV generated (original columns + `row_number`, `outcome`, `error_code`,
   `error_message`, `customer_id`). → `customer.import.completed`. Notification to importer (08).
10. Undo (≤ 7 days): deletes created customers that have no dependent records (jobs, payments,
    messages), reverts updated fields from before-images when current value still equals the
    imported value; reports what could not be undone. → `customer.import.reverted`.

### W4. Export [S: T09]
1. User chooses scope/columns; server checks `customers.export`; rate limit (e.g. 10/h/user) [I].
2. Small (≤ 5 k rows) → streamed download; large → `ExportJob`, file in object storage,
   signed URL expiring 24 h, notification.
3. Audit entry + → `customer.export.completed` (who, filter hash, row count) — owner can review
   in Activity [I].

### W5. Duplicate detection & merge
1. Detection runs: (a) synchronously on create/update (suggest), (b) nightly batch across
   business (§10.2), (c) after import. Pairs above threshold → `DuplicateCandidate`.
2. User opens merge screen, picks survivor and field winners.
3. Merge transaction (§10.3): re-point all child rows (people, contact points, addresses,
   assets, notes, tags, leads, portal links, consent ledger) to survivor; ask other modules to
   re-point their references via `customer.customer.merged` (03/04/05/06/08 must handle — they
   may also resolve `mergedIntoId` lazily on read); loser gets `status = merged`,
   `mergedIntoId = survivor`.
4. Consent merge rule: per contact point, consent ledgers are concatenated; effective state is
   computed from the latest event per (contact point, channel, purpose) — a revoke on either
   record is never overwritten by an older grant on the other (§5.9).
5. → `customer.customer.merged` {survivorId, mergedIds, movedCounts, undoToken}.
6. Unmerge within 30 days: restores loser from snapshot; child rows that were created after the
   merge stay with survivor; emits `customer.customer.unmerged`.

### W6. Self-service account signup on booking site → customer link [S: T05 "customers can create an account"]
Agent 01 owns identity (email/phone OTP or password). Flow:
1. Visitor signs up on booking site with name + email and/or phone; 01 verifies at least one
   channel (OTP/magic link) [I].
2. 01 emits `portal.account.registered` {accountId, businessId, email, emailVerified, phone,
   phoneVerified, name}.
3. 07 runs **account-to-customer matching** (§10.2, strict mode):
   - Exactly one active customer has a contact point equal to a **verified** account channel →
     link automatically (`PortalAccountLink.matchMethod = verified_email|verified_phone`); mark
     that contact point `verifiedAt = now` if it wasn't.
   - Multiple customers match (household shares a phone; duplicates exist) → link to the
     customer with most recent activity **only if** the match is on email; if match is on
     phone only and ambiguous → create link to a new customer and raise a `DuplicateCandidate`
     for staff review [I]. Never expose another customer's data across an ambiguous match.
   - Match only on **unverified** channel or on name/address → do **not** auto-link (account
     takeover risk: someone signs up with your phone number typed but not verified); create a
     new customer + duplicate suggestion.
   - No match → create customer (`createdVia = portal_signup`, source from booking-source
     attribution [S: T05]).
4. → `customer.account.linked` {customerId, accountId, matchMethod}; 01 now scopes the session
   to that customer.
5. Staff can later re-link/merge from the profile (§3.7); re-linking emits
   `customer.account.relinked`.

### W7. Guest booking on booking site (no account) → customer
1. 01 completes booking with contact payload; before creating the job it calls
   `customers.resolveForBooking` (customer-scope API, idempotent by booking attempt ID).
2. 07 matches (§10.2 strict mode). Because the guest did not verify anything, policy is:
   - Match on email **and** phone to same customer → reuse that customer, but **do not** reveal
     anything from the profile to the guest (no address autofill, no saved cards) [I].
   - Partial match → reuse customer *only for job linkage* if setting "link guest bookings to
     matching customers" = on (default on [I]); otherwise create and raise duplicate candidate.
   - Add any **new** contact points/addresses the guest typed to the matched customer, flagged
     `unverified`, never overwriting existing primary values.
3. Consent captured on the booking form (checkbox for marketing SMS/email, disclosure version)
   recorded as `ConsentEvent` with `method = web_form`, IP, UA, page URL, disclosure hash.
4. Assets entered on the booking site (vehicle YMM/plate) → matched against the customer's
   garage (§10.5) or created.
5. Return {customerId, addressId, assetIds, contactPointIds}; 03 creates the appointment;
   07 receives `booking.appointment.created` and closes any open abandoned-attempt lead (W16.6).

### W8. Edit contact info / addresses
- Changing a phone/email that has consent: consent does **not** transfer to the new value
  (consent belongs to the number/address, not the person) [I-legal]; UI warns "Marketing
  consent applies to the old number only".
- Changing primary address re-resolves service area → `customer.address.updated`; future jobs
  (03) get a notification event but their address snapshots are not mutated automatically —
  03 decides (prompt "Update 3 upcoming jobs to the new address?") [I].
- Deleting an address used by future jobs → blocked; archive instead.

### W9. Consent changes (incl. STOP)
1. Sources: staff toggle (requires method + note), customer portal toggle, booking/lead form
   checkbox, import attestation, inbound SMS keyword (STOP/UNSUBSCRIBE/CANCEL/END/QUIT/STOPALL
   → revoke; START/UNSTOP → re-grant) reported by 08, email unsubscribe link (08), hard bounce /
   carrier "unreachable" (08) → contact point status change (not consent).
2. 07 appends `ConsentEvent`, recomputes `ConsentState` projection, → `customer.consent.changed`
   {contactPointId, channel, purpose, newStatus, method, occurredAt}.
3. 08 must check consent at send time (not only at audience build time) via
   `consent.check` or its own projection fed by the event (§9).

### W10. Status changes, archive, block, erase
- **Inactive** (system or manual): no jobs in N months (setting) — informational only.
- **Blocked / Do not service**: booking site refuses online booking for matching contact
  points (01 calls `customers.resolveForBooking` which returns `blocked = true` → generic
  "Please contact us to book" message, no reason leaked) [I]; CRM booking shows red warning
  requiring override.
- **Archived**: hidden from default lists, still searchable with "include archived".
- **Erase (privacy request)**: owner only; preconditions: no unpaid balance, no active
  subscription, no future jobs (or cancel them). Personal fields replaced with tombstones
  (`name = "Erased customer #A1B2"`), contact points/addresses deleted, notes purged, assets
  detached (VIN kept only if business opts to keep anonymized service records), consent ledger
  **retained in minimized form** (hash of contact value + revoke records) so we can prove
  opt-out and keep suppressing [I-legal]; financial records kept with customer ID for
  tax/accounting retention (05). → `customer.customer.erased`.

### W11. Add vehicle (garage) [S: llms]
1. Choose lookup mode; VIN → normalize → check digit → decode via catalog provider (cache by
   VIN prefix (positions 1–8 + 10–11) for 90 days to cut cost) [I].
2. Duplicate VIN check (business scope). If owned by another customer → offer transfer (W13).
3. Write `Asset{kind: vehicle}` + `VehicleDetail` + photos (upload via signed URL; thumbnails
   generated async) → `asset.asset.created`, `asset.vehicle.decoded` (if decoded).
4. If size class derived (sedan/SUV/truck) → available to booking composer/pricing (02/03).

### W12. Job completion updates asset & customer history
1. 04 emits `job.job.completed` {jobId, customerId, addressId, assetIds, completedAt (UTC),
   businessLocalDate, services, totalCents, workerIds, odometerReadings[], photoIds[]}.
2. 07 projection (idempotent on jobId + version): update `CustomerStats.lastServiceAt` =
   max(existing, completedAt), `jobsCompleted++` (only if this jobId not yet counted),
   `AssetStats.lastServiceAt`, append `AssetServiceRecord`, append `OdometerReading`s
   (validate monotonic — §5.7), mark open leads attached to this customer with matching
   interest as "won by booking" if setting on.
3. Cancellations/undo of completion (`job.job.reopened`, `job.job.cancelled` after completion)
   → recompute stats from source records (not decrement blindly) (§10.7).
4. → `asset.service_record.added` (consumed by 08 for "service due" reminders).

### W13. Transfer asset (vehicle sold / moved between household and business)
1. From asset profile → Transfer → pick target customer (or create).
2. Asset keeps its service history; history entries remain linked to the **original job's
   customer** (jobs never move); the new owner sees service records before the transfer date
   only if setting "share prior history with new owner" = on (default **off** for privacy) [I].
3. → `asset.asset.transferred` {assetId, fromCustomerId, toCustomerId}.

### W14. Create lead [S: T20]
1. Mode scratch / existing customer / address-only.
2. Duplicate check against open leads + customers (§10.2). If open lead exists → offer "Add
   activity to existing lead".
3. Write `Lead` (stage = pipeline's first open stage), contact snapshot or `customerId`,
   `LeadActivity{type: created}`, consent events if captured, follow-up if set.
4. Auto-assign owner per rule. → `lead.lead.created` {leadId, source, customerId?, ownerId,
   hasPhone, hasEmail, consent summary}. 08 may enroll in a lead drip.

### W15. Address-only lead + enrichment [S: T20 "just give us an address and fieldd will gather the rest"]
1. Lead created with address only; status badge "Postal only".
2. Owner/admin clicks *Find contact info*. Preconditions (§5.8): enrichment connector enabled,
   monthly budget not exhausted, user has `leads.enrich`, user confirms permissible-purpose
   attestation (one-time per user + per request checkbox), and the lead's region is not on the
   blocked list.
3. `EnrichmentRequest` created (idempotent per lead+provider+address hash for 90 days — re-use
   cached result, no second charge) → provider call (async, timeout 20 s, retries ×2 on 5xx).
4. Result: candidate residents/owners with fields and per-field provenance + confidence. User
   picks which to apply. Applied fields are stored with `provenance = enrichment:<provider>` and
   **no marketing consent** (enriched numbers may only be contacted in ways that don't require
   prior consent — see §5.8).
5. → `lead.enrichment.completed` {leadId, fieldsFound, costCents}; LeadActivity logged; cost
   added to `EnrichmentUsage` (shown on billing page).
6. **Budget alternative (default in starter tier):** "Mail them" → create a postcard/door-hanger
   addressed to "Current resident" via 08 with a unique QR → scan creates/updates the lead with
   self-provided, consented contact info (W17). Also: "Look up public property owner name"
   from county assessor open data where the business has loaded it (name only, for mail
   personalization) [I].

### W16. Abandoned booking attempt → lead (real time) [S: T10, T05, T12]
1. 01 emits attempt telemetry: `booking.attempt.started` {attemptId, visitorId, sessionId,
   bookingSource, utm, referrer, device, startedAt}, then `booking.attempt.step_reached`
   {attemptId, step, stepIndex, at, cart?, slot?, serviceAreaResult?, assetHint?} for each
   step, `booking.attempt.contact_captured` {attemptId, name?, email?, phone?, address?,
   consent[]?, disclosureVersion} when the visitor has entered and *blurred/advanced past* a
   contact field (not keystroke-level) [I-legal §5.10], `booking.attempt.completed`
   {attemptId, appointmentId, customerId}, and (from 01's sweeper) `booking.attempt.expired`.
2. 07 upserts `BookingAttemptLead` keyed by `attemptId` (idempotent; events may arrive out of
   order — keep max(stepIndex), latest timestamps).
3. Lead creation policy (setting, default "when contact captured" [I]):
   - *Always*: an anonymous lead card exists from the first step ("Visitor from Google Ads,
     reached Services") — shown in Funnel layout only, not in Board columns, to avoid noise.
   - *When contact captured*: a Lead in stage "New" is created as soon as
     `contact_captured` arrives; card shows "Live" until inactivity threshold.
4. Matching: captured email/phone matched to customers (§10.2 strict) and open leads; if an
   open booking-site lead for the same person exists within 7 days → reuse it and append the
   new attempt (one lead per person, many attempts) [I].
5. Inactivity: when no step events for `abandonInactivityMinutes` (default 30) and not
   completed → `status = abandoned`, → `lead.attempt.abandoned` {leadId, attemptId,
   furthestStep, cartValueCents, hasConsentSms, hasConsentEmail}. 08 uses this for the
   "finish your booking" message "a couple of hours later" [S: T12] (subject to consent and
   transactional-vs-marketing classification, §5.9).
6. Completion: `booking.attempt.completed` or `booking.appointment.created` with same person →
   lead moves to Won with `wonReason = self_completed`, linked job; activity logged; 08 cancels
   pending recovery messages (08 listens to `lead.lead.won`).
7. Retention: anonymous attempts (no contact) purged after 30 days; aggregates kept for funnel
   analytics [I].

### W17. Postcard / printed QR scan → lead [S: T20 "once a customer scans the QR code on your postcard their information will show up here in the lead board"]
1. 08 (postcard campaign) prints per-recipient or per-campaign QR codes generated by 01
   (short link carrying `qrId`, `campaignId`, optional `recipientToken`). llms mentions "new QR
   code booking options" [S: llms].
2. Scan → booking site landing; 01 emits `portal.qr.scanned` {qrId, campaignId,
   recipientToken?, visitorId, at}.
3. If `recipientToken` resolves to a known mailing recipient (an address-only lead or a
   customer) → 07 logs `LeadActivity{type: qr_scanned}` on that lead (or creates a lead
   attached to the customer, source = postcard) and moves stage to "Engaged" if it was "New"
   [I]. Per-recipient QR is how "their information will show up" without any de-anonymization:
   we already know the address we mailed [I].
4. If campaign-level QR (no recipient) → a lead appears only once the visitor provides contact
   info (W16 path, with `source = postcard_qr`, `campaignId`).
5. If the scan proceeds to a completed booking → attribution recorded on the customer
   (`acquisitionCampaignId`) and lead Won.

### W18. Lead form submission (website "get a quote" form) [S: T10 "those who filled out your lead forms"]
1. Lead form (owned by 01 as an embeddable widget [I]) posts {formId, fields, consent,
   disclosureVersion, utm} → `leads.submitForm` (public, rate-limited, bot-protected:
   honeypot + time-to-submit + optional challenge).
2. 07 creates/merges lead (W14 rules), logs activity, auto-assigns, → `lead.lead.created`
   with `source = lead_form`. 08 sends auto-reply + staff alert.

### W19. Work the pipeline
1. Drag card to stage → `leads.moveStage` {leadId, toStageId, expectedVersion}. Guard:
   moving to a Won stage requires a linked job/quote or explicit "won without booking" reason;
   moving to Lost requires lost reason [I].
2. LeadActivity `stage_changed` {from, to, actor}; `stageEnteredAt` reset; → `lead.lead.stage_changed`.
3. Log call/visit/note; set follow-up → `lead.followup.scheduled`; at due time scheduler emits
   `lead.followup.due` → 08 notifies owner (push/email).
4. Rotting: stage with "rotting after N days" → card gets amber badge; nightly job emits
   `lead.lead.stale` for 08 digest [I].

### W20. Convert lead → booking [S: T20]
1. *Convert to booking* → 03 composer drawer pre-filled (§3.14).
2. If lead has no customer: on confirm, 07 `customers.createFromLead` runs **inside the same
   saga** as job creation: (a) resolve/create customer (match first), (b) 03 creates
   appointment with `leadId` in metadata, (c) 07 marks lead Won with `convertedJobId`.
   Saga with compensations: if (b) fails, customer creation stays (harmless) but lead stays
   open; if (c) fails it retries from the `booking.appointment.created` event carrying `leadId`
   (eventual consistency, idempotent) [I].
3. Lead contact snapshot fields are copied to the customer only where customer field empty;
   consent events move with their contact points (they are the same contact-point identity).
4. → `lead.lead.converted` {leadId, customerId, jobId, valueCents, sourceAttribution} and
   `lead.lead.won`. 08 reports conversion by source; booking source attribution [S: T05] is
   preserved on the job by 03.

### W21. Mark lost / reopen
Lost requires reason (settings list) + optional note; → `lead.lead.lost`. 08 may enroll in a
"win-back" drip if marketing consent. Reopen → previous open stage (or first) → `lead.lead.reopened`.

### W22. Merge leads
Same mechanics as W5 at lead level; activities concatenated chronologically; attempts union;
→ `lead.lead.merged`.

### W23. Bulk tagging / customer types
Up to 10 k selected IDs or "all matching filter" (server resolves filter snapshot at request
time to a frozen ID list; reports count to user before confirm) [I]. Async job if > 1 k.
→ one `customer.tags.bulk_changed` event with IDs (chunked ≤ 1 k per event).

### W24. Intake form answers [S: T05]
1. Customer answers intake form during booking or later in the account area (01 UI).
2. 01 calls `customers.saveIntakeResponse` {customerId, formVersionId, answers}; 07 stores a
   new `IntakeResponse` version (history kept; latest is current), → `customer.intake.updated`.
3. Staff can view/edit answers on profile (edit logged with actor = staff).
4. If form definition changes, old answers remain bound to their form version; profile shows
   unanswered new questions as "Not answered yet".

---

## 5. Business rules & math

### 5.1 Identity & normalization
- **Phone:** parse with libphonenumber-equivalent using (1) explicit `+` prefix, (2) address
  country, (3) business default country. Store `valueRaw` (as typed) and `valueE164`.
  Extensions stored separately (`ext`). Matching uses `valueE164` only. Short codes / invalid
  numbers stored with `formatStatus = invalid` and never used for matching or SMS [I].
- **Email:** trim; lowercase entire address for matching (`valueNormalized`), preserve display
  case; IDN domains to punycode for matching; **do not** strip Gmail dots/`+tags` for hard
  matching; do use a "relaxed" form (dots/plus stripped for gmail.com/googlemail.com) as a
  *weak* signal only [I].
- **Name:** store `firstName`, `lastName`, optional `middleName`, `suffix`, `preferredName`.
  `displayName` derived: business → `companyName`; individual → `preferredName || firstName`
  + `lastName`; fallback primary email local part → primary phone → "Unnamed customer" [I].
  Sort key: `lastName, firstName` (individual) / `companyName` (business), collation
  case/accent-insensitive.
- **Address:** structured (line1, line2, locality, region, postalCode, countryCode); normalized
  key = uppercase, punctuation stripped, USPS-style abbreviations (STREET→ST) for matching;
  geocode lat/lng with `geocodeQuality` (rooftop / range / centroid / failed). Unit/apartment is
  part of identity (two units at one building = two addresses) [I].
- **Plate:** uppercase, strip spaces/dashes/dots; key = `regionCode:plate` [I].
- **VIN:** uppercase, strip non-alphanumerics; I/O/Q invalid in 17-char VINs (§10.6).

### 5.2 Customer kinds & business customers [S: llms "better support for business customers"]
- `kind = business` enables: company name (display name), legal name, tax ID (encrypted), PO
  required flag, billing contact person (receives invoices), multiple sites (addresses) and
  site contacts, fleet garage (paged; may contain hundreds of vehicles), net terms *display*
  (terms enforcement owned by 04/05) [I].
- A person can belong to more than one customer (e.g. a fleet manager who also has a personal
  car) — modelled as two `Person` rows sharing contact points? **No**: contact points are
  unique per customer; the same phone may exist on two customers (household vs. business).
  Matching must therefore tolerate one phone → many customers (§10.2) [I].

### 5.3 Customer status rules
- `active` default. `inactive` auto-set when `lastServiceAt` older than setting (default
  365 days) and no future jobs/subscription; auto-reverts to active on next job [I].
- `blocked` manual only; blocks online booking (W10).
- `archived` manual or retention policy; excluded from segments by default.
- `merged` terminal (except unmerge window). `erased` terminal.
- "Account status" displayed in Fieldd profile [S: T09] — exact values unknown (Q2).

### 5.4 Derived metrics (projections; integer cents; business timezone for dates)
| Metric | Formula | Source |
|---|---|---|
| `jobsCompleted` | count distinct jobs with status `completed` (not cancelled/refunded-void) | 04 events |
| `lastServiceAt` | max(`completedAt`) over completed jobs; `lastServiceLocalDate` = date in business TZ at `completedAt` | 04 |
| `firstServiceAt` | min(`completedAt`) | 04 |
| `nextJobAt` | min(`scheduledStart`) over future non-cancelled jobs | 03 |
| `lifetimeRevenueCents` | Σ captured payments − Σ refunds − Σ chargebacks lost, excluding tips (tips shown separately) and gift-card *purchases* (revenue recognized on redemption) [I; final rule owned by 05/08 metrics] | 05 |
| `avgTicketCents` | `round_half_even(lifetimeRevenueCents / jobsCompleted)`; null if 0 jobs | derived |
| `balanceDueCents` | Σ open invoice balances | 04/05 |
| `daysSinceLastService` | `businessToday − lastServiceLocalDate` (calendar days; DST-safe because local dates) | derived |
| `avgRatingGiven` | mean of review stars given by this customer (1–5, one decimal) | 08 |
| Asset `lastServiceAt` | max(`completedAt`) over completed jobs whose `assetIds` include asset | 04 |
| Lead `estimatedValueCents` | manual, or Σ cart line totals from attempt/quote (pre-tax, post-discount) | 01/04 |
| Pipeline weighted value | Σ `estimatedValueCents × stage.probabilityPct / 100`, rounded half-even per lead then summed | derived |
| Lead age in stage | now − `stageEnteredAt` (hours, business TZ for "days" display) | derived |
| Funnel conversion (step k) | attempts reaching step k / attempts started, per date range in business TZ | 01 events |
| Recovery rate | abandoned leads later Won (any path) within 30 days / abandoned leads | derived |

Money: all amounts integer minor units with ISO currency; no floats; display formatting by
locale. Projections are recomputable from source events (§10.7).

### 5.5 Snapshots vs. references
- Jobs (03/04) store `customerId`, `addressId` **and** an address snapshot (formatted + lat/lng
  + access notes at booking time), plus `assetIds` and a light asset snapshot (YMM, color,
  plate) so historical invoices don't change when the customer edits data [I].
- Lead stores a contact snapshot until attached/converted; after attach, the customer record is
  the source of truth; snapshot kept read-only for audit.

### 5.6 Service area resolution
`serviceAreaId` for an address = Agent 02's `coverage.resolve(lat, lng)` (first match by 02's
priority rules). Customers are "across several different service areas" [S: T18]. Out-of-area
addresses allowed in CRM (override [S: T11]); flagged `outOfArea = true`. Re-resolved when 02
emits `catalog.service_area.updated` (batch, async).

### 5.7 Asset rules
- One VIN per business ⇒ unique (active assets). Archived/sold assets keep VIN but are excluded
  from uniqueness so a car can come back via transfer.
- Odometer readings must be non-decreasing per asset by `recordedAt`; a lower reading is stored
  with `flag = rollback_suspect` and excluded from "latest mileage" unless confirmed (odometer
  replaced / unit change) [I]. Unit conversions: 1 mi = 1.609344 km, store as entered + unit.
- Vehicle year range: 1900 … currentYear + 1 (model years run ahead).
- Photos: max 10 per asset in starter tier, 50 in higher tiers [I]; formats JPEG/PNG/HEIC→JPEG;
  EXIF stripped.
- Tire size format: ISO metric `^(P|LT|ST|T)?\d{3}/\d{2}(Z?R|D|B)\d{2}(\.\d)?` + optional load
  index/speed rating; flotation sizes (e.g. `33x12.50R20`) allowed by a second pattern;
  front/rear can differ (staggered) [I].
- Size class: from decoded body class (sedan/coupe/hatch → "car", SUV/crossover → "SUV",
  pickup → "truck", van → "van", ≥ class 3 GVWR → "heavy"); manual override. Used for pricing
  only if 02 supports vehicle-size pricing (Q6).

### 5.8 Contact enrichment ("magic data") — legal, privacy and cost constraints
Fieldd markets turning an address, and booking-site visitors who "dropped off before
confirming", into contact details [S: T20, T10, T05]. How Fieldd does this is not stated.
Typical industry mechanisms are reverse-address ("skip trace") data from data brokers, and
website-visitor identity resolution via third-party identity graphs [I]. **Both carry real
legal risk; we treat enrichment as an optional, gated, off-by-default connector.**

Legal constraints [I-legal — counsel review required]:
1. **US TCPA / FCC rules:** marketing calls or texts to mobile numbers using automated systems
   or prerecorded voice require **prior express written consent**; a number obtained from a
   data broker has no consent. So enriched numbers must **never** enter SMS marketing or
   automated-dialer flows. Manual, human-dialed calls may be permissible but remain subject to
   DNC rules. Statutory damages are per message, which makes this the largest legal risk in
   the module.
2. **National Do Not Call Registry + state DNC lists:** telemarketing calls to registered
   numbers require an established business relationship or consent. Enriched leads must be
   DNC-scrubbed before any call; scrubbing requires a registry subscription (cost) [I-legal].
3. **State "mini-TCPA" laws** (e.g. Florida, Oklahoma, others) tighten rules on automated
   texts and calling hours [I-legal].
4. **CAN-SPAM (US email):** consent not required, but sender identification, physical address
   and working unsubscribe are; **CASL (Canada)** requires consent (express or limited implied)
   for commercial electronic messages — cold email to an enriched address in Canada is not
   allowed [I-legal]; **UK/EU (PECR/GDPR)** require consent for marketing email/SMS to
   individuals and a lawful basis + transparency notice for processing enriched personal data;
   **Australia Spam Act** similar consent model. Fieldd operates in 37 countries [S: llms], so
   region matters.
5. **State privacy laws (e.g. CCPA/CPRA and other comprehensive state laws):** buying
   personal data is regulated through data-broker registration on the seller side and notice
   at collection / right-to-know / deletion on our customer's side; our customers (the
   businesses) become responsible for disclosures. The platform must support access/deletion
   requests for enriched data (W10) [I-legal].
6. **Website-visitor de-anonymization** (identifying people who never typed their details) can
   trigger wiretap/"pen register" style claims in some states and fails GDPR transparency
   outright. **We will not do it.** [I-legal]
7. **DPPA (US Driver's Privacy Protection Act):** personal info derived from DMV records
   (e.g. plate → owner name) is restricted to permissible uses; plate → vehicle specs (no
   owner data) is the only plate lookup we support [I-legal].

Data-source cost [I — estimates, verify with vendors before quoting]: reverse-address/skip-trace
APIs are typically priced per successful match, commonly in the range of a few cents to tens of
cents per record, with monthly minimums for API access; identity-graph visitor resolution is
typically a monthly subscription. For a starter business this is a recurring cost with weak
lawful use (no SMS marketing allowed on results).

**Design decisions:**
- Enrichment is a **connector** (bring-your-own provider key or platform-resold credits) behind
  plan gating, monthly spend cap, per-request confirmation of permissible purpose, and region
  allow-list (disabled by default for CA/EU/UK/AU businesses) [I].
- Enriched fields carry `provenance`, `obtainedAt`, `provider`, `confidence`, and are
  **excluded from SMS/email marketing segments** until the person provides consent through a
  first-party channel (form, booking, reply) [I]. 08 enforces at send time via consent check.
- Enriched data auto-expires (purged) after 90 days if the lead never converts [I].
- Every enrichment is an auditable `LeadActivity` with cost.

**Lawful budget alternative (default experience):**
1. *Address-only lead → "Mail a postcard / leave a door hanger"* with a personal QR code
   (W17). Postal mail to "Current resident" needs no consent; the scan converts an anonymous
   address into a consented contact when the resident books or fills the form [I].
2. *Neighbour marketing:* after a completed job, suggest "send postcards to the 20 nearest
   addresses" (addresses from open address datasets or user-entered) [I].
3. *Booking-site capture with consent* (W16): we only hold contact info the visitor typed,
   after a clear notice at collection, which is enough for the "finish your booking" follow-up
   in most regimes when framed as transactional/service and limited to one or two messages —
   still configurable by region (§5.9).
4. *Public property-owner name* (county assessor open data, where legally reusable) for mail
   personalization only [I].
5. *Referral capture* (08 referral codes [S: llms]) — the referrer shares, the referee
   self-identifies.

### 5.9 Consent model (SMS / email / voice / push / postal)
Consent is stored **per contact point × channel × purpose**:
- `channel ∈ {sms, email, voice, push, postal}`; `purpose ∈ {transactional, marketing}`.
- **Transactional** (booking confirmations, reminders, on-my-way, invoices, receipts —
  notification catalog owned by 08): default `granted_implied` when the contact point is
  provided in connection with a booking; customer can opt out of SMS transactional at any time
  (STOP), except legally required messages, which then fall back to email [I-legal].
- **Marketing** (campaigns, drips, promos, review requests? — classification of review
  requests owned by 08): default `not_set`; becomes `granted` only via an explicit action
  recorded with method + evidence. Checkbox never pre-checked; disclosure text versioned and
  hashed; for US SMS the disclosure must identify the business, message types, frequency,
  "msg & data rates may apply", STOP/HELP instructions, and consent-not-a-condition-of-purchase
  language [I-legal].
- **Effective state** = latest `ConsentEvent` by `occurredAt` for the tuple; ties broken by
  revoke-wins.
- **Revocation** via any reasonable means (reply "stop", email, verbal to staff) must be
  honoured; staff UI must make it one click [I-legal].
- **Imported consent** = `granted` with `method = import_attested` and lower `evidenceLevel`;
  08 may choose to require re-confirmation (double opt-in) for SMS in strict mode [I].
- **Region presets:** business country → defaults for email marketing (US: opt-out model
  allowed, Canada/EU/UK/AU: opt-in required) [I-legal].
- **Quiet hours / frequency caps** enforcement belongs to 08, but 07 stores the contact's
  timezone (derived from primary address) for 08 to use.
- Fields consumed by 08 (contract §9): `canSend(contactPointId, channel, purpose)`,
  `ConsentState` projection, `ContactPoint.status` (bounced/invalid), `customer.status`
  (archived/blocked/erased never targeted), `optOutAll` flag.

### 5.10 Abandoned-attempt capture rules (privacy-safe real time)
- Booking site captures contact fields only on **field blur / step advance**, never per
  keystroke, and only after a notice at collection visible on the contact step ("We'll use this
  to contact you about your booking. Privacy policy.") [I-legal].
- No session replay; no third-party identity resolution.
- Anonymous attempts keyed by first-party `visitorId` cookie (01 decides cookie/consent banner
  requirements per region) [I].
- A visitor can clear their in-progress data with "Start over" (deletes attempt contact
  snapshot) [I].

### 5.11 Lead rules
- One lead belongs to exactly one pipeline and one stage; stage `kind ∈ {open, won, lost}`;
  each pipeline must have ≥ 1 open, exactly 1 won, exactly 1 lost stage [I].
- Lead without any contact point and without address is invalid (except anonymous attempt
  records, which are not Leads until contact captured — §W16.3).
- Owner must be an active worker with lead permission; when 09 deactivates a worker →
  their open leads reassigned per rule or to "Unassigned" (W: `team.worker.deactivated`).
- Lead sources (system, locked): `manual`, `address_only`, `lead_form`, `booking_site`
  (abandoned), `postcard_qr`, `qr_other`, `referral`, `phone_call`, `walk_in`, `field_referral`,
  `import`, `api`, `ai_assistant`; custom sources allowed (e.g. "Home show 2026") [I].
- Default stages (template) [I]: New → Contacted → Quoted → Follow-up → Won / Lost.
  Abandoned-attempt leads enter "New" with source badge.
- Every mutation writes a `LeadActivity` [S: T10] (immutable; edits/corrections are new
  activities).

### 5.12 Tags
- Tag names unique per business per scope (case-insensitive); ≤ 40 chars; colors from a fixed
  accessible palette. Scopes: customer, lead, asset (job tags owned by 03/04 [S: T19, T08]);
  a unified tag library with scope flags avoids duplicates like "VIP" in 4 places [I].
- Deleting a tag used by discounts (06) or segments (08) → blocked until references removed, or
  confirm cascade; renaming propagates by ID.

### 5.13 Custom fields
Max 50 per entity type (customer, asset, lead) [I]; keys immutable after creation; type
changes only between compatible types; required custom fields enforced in CRM create only
(never retroactively on import/updates without value — imports get warnings).

---

## 6. State machines

All transitions write an `AuditEntry` (customers/assets) or `LeadActivity` (leads). Guards are
evaluated server-side inside the transaction with optimistic version check.

### 6.1 Customer status
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `create` | valid + not hard-blocked duplicate | active | `customer.customer.created` |
| active | `mark_inactive` (system nightly) | no job in N days, no future job, no active sub | inactive | `customer.customer.status_changed` |
| inactive | `job.job.created` / `job.job.completed` / manual | — | active | status_changed |
| active/inactive | `block` | role ≥ admin; reason required | blocked | status_changed; 01 refuses online booking; 08 suppresses marketing |
| blocked | `unblock` | role ≥ admin | active | status_changed |
| active/inactive/blocked | `archive` | no future jobs (else prompt to cancel via 03) | archived | excluded from default lists/segments |
| archived | `restore` | — | active | |
| any non-terminal | `merge_into(survivor)` | survivor not merged/erased; no blocking conflicts | merged | `customer.customer.merged`; references re-pointed |
| merged | `unmerge` | within 30 days; survivor not erased | prior state | `customer.customer.unmerged` |
| any non-terminal | `erase` | owner; balance = 0; no active subscription; no future jobs | erased | PII purge; `customer.customer.erased` |

### 6.2 Portal account link
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| unlinked | `portal.account.registered` | unique verified match | linked | `customer.account.linked` |
| unlinked | `portal.account.registered` | no safe match | linked (to new customer) | create customer; maybe `DuplicateCandidate` |
| linked | staff `relink` | role ≥ admin | linked (other customer) | `customer.account.relinked`; 01 refreshes session scope |
| linked | customer merged | — | linked (survivor) | link moved |
| linked | `portal.account.deleted` | — | unlinked | customer kept |

### 6.3 Consent (per contact point × channel × purpose)
| State | Event | Guard | Next |
|---|---|---|---|
| not_set | `grant` (form/verbal/paper/import_attested/keyword START) | method + evidence present | granted |
| not_set | contact used for booking (transactional only) | purpose = transactional | granted_implied |
| granted / granted_implied | `revoke` (STOP, unsubscribe link, staff, portal toggle) | — | revoked |
| revoked | `grant` | must be a fresh, explicit customer action (portal toggle, START keyword, new form consent) — staff cannot re-grant on verbal alone for SMS marketing [I-legal] | granted |
| any | contact point deleted | — | (ledger retained; state frozen) |

### 6.4 Lead lifecycle (status orthogonal to custom stages)
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | create (manual/form/QR/import/API) | has contact or address | open | `lead.lead.created` |
| — (attempt) | `booking.attempt.contact_captured` | policy allows | open (live) | `lead.lead.created` (source booking_site) |
| open (live) | step events | — | open (live) | update furthest step |
| open (live) | inactivity ≥ threshold | not completed | open (abandoned flag) | `lead.attempt.abandoned` |
| open | `moveStage(open stage)` | version ok | open | `lead.lead.stage_changed` |
| open | `convert` / `booking.appointment.created(leadId)` / self-completed attempt | job exists | won | `lead.lead.converted`, `lead.lead.won` |
| open | `markLost(reason)` | reason set | lost | `lead.lead.lost` |
| won / lost | `reopen` | role ≥ owner of lead or manager; won → reopen only if linked job cancelled | open | `lead.lead.reopened` |
| open/won/lost | `merge_into` | same business | merged | `lead.lead.merged` |
| open | `delete` (manual leads only, no activity beyond creation) | role ≥ manager | deleted (soft) | `lead.lead.deleted` |

### 6.5 Booking attempt (as seen by 07)
| State | Event | Next |
|---|---|---|
| — | `booking.attempt.started` | anonymous |
| anonymous | `step_reached` | anonymous (furthest step updated) |
| anonymous | `contact_captured` | identified (lead created/linked) |
| anonymous / identified | inactivity | abandoned |
| abandoned | new `step_reached` (same attempt resumed) | identified/anonymous (live again) |
| any | `completed` | completed (lead won) |
| anonymous (abandoned) | 30 days | purged |

### 6.6 Import job
| State | Event | Next |
|---|---|---|
| uploaded | parse ok | mapping |
| uploaded | parse fail (encoding/empty/too large) | failed |
| mapping | user confirms mapping+options | validating |
| validating | done | validated |
| validated | user confirms | running |
| running | all batches ok | completed |
| running | some rows errored | completed_with_errors |
| running | cancel | cancelled (committed batches remain) |
| running | infra failure (retries exhausted) | failed (resumable from last committed batch) |
| completed* | undo within 7 days | reverted / partially_reverted |

### 6.7 Enrichment request
| State | Event | Guard | Next |
|---|---|---|---|
| — | request | connector on, budget left, attestation, region allowed | pending |
| pending | provider OK | — | completed (cost recorded) |
| pending | provider no match | — | no_match (cost per provider terms) |
| pending | timeout/5xx after retries | — | failed (no cost) |
| completed | user applies fields | — | applied |
| completed/applied | 90 days and lead not converted | — | expired (enriched values purged) |

### 6.8 Asset status
| State | Event | Next |
|---|---|---|
| — | create | active |
| active | transfer | active (new owner) |
| active | mark sold / archive | archived |
| archived | restore / transfer back | active |
| active/archived | merge_into(other asset) | merged |

---

## 7. Data model

Stack-agnostic TypeScript-style interfaces. All entities carry `businessId` (tenant key; every
query filtered by it), `createdAt`, `updatedAt` (UTC ISO), `version: number` (optimistic
concurrency), `createdBy`, `updatedBy` (actor ref). IDs are opaque, sortable (ULID/UUIDv7) [I].
PII fields marked `/* PII */`; secrets `/* SECRET, encrypted at rest */`.

```ts
type ID = string;                 // ULID/UUIDv7
type Cents = number;              // integer minor units
type ISODate = string;            // YYYY-MM-DD (business-local)
type ISOTime = string;            // UTC timestamp
type ActorRef = { kind: 'user'|'customer'|'system'|'api'|'ai'; id: ID; onBehalfOf?: ID };

// ---------- 7.1 Customer ----------
interface Customer {
  id: ID; businessId: ID; version: number;
  kind: 'individual' | 'business';
  displayName: string;               // derived, stored for sort/search
  companyName?: string;              /* PII-lite */
  legalName?: string;
  taxId?: string;                    /* SECRET */
  status: 'active'|'inactive'|'blocked'|'archived'|'merged'|'erased';
  statusReason?: string;
  mergedIntoId?: ID;
  customerTypeId?: ID;               // discounts can target customer types [S: T09]
  primaryPersonId: ID;
  primaryAddressId?: ID;
  billingAddressId?: ID;
  billingPersonId?: ID;
  ownerUserId?: ID;                  // assigned rep
  preferredWorkerIds: ID[];          // [S: T04 preferred worker]
  taxExempt: boolean;                // [S: D18 taxes]
  taxExemptCertFileId?: ID;
  taxExemptUntil?: ISODate;
  defaultDiscountId?: ID;            // owned by 06 [S: D18 discounts]
  poRequired: boolean;               // business
  source: LeadSource;                // acquisition source
  sourceDetail?: string;
  acquisitionCampaignId?: ID;        // 08
  acquisitionBookingSource?: string; // 01 booking source [S: T05]
  createdVia: 'crm'|'import'|'portal_signup'|'portal_guest_booking'|'booking_composer'
            |'quote'|'invoice'|'subscription'|'lead_conversion'|'api'|'ai_assistant'|'team_app';
  importJobId?: ID;
  externalIds: { system: string; id: string }[];  // e.g. previous CRM id
  customFields: Record<string, unknown>;
  timezone?: string;                 // IANA, from primary address
  locale?: string;                   // preferred language for comms
  optOutAll: boolean;                // global marketing suppression
  doNotServiceReason?: string;
  lastActivityAt: ISOTime;           // any interaction (for default sort)
}

interface Person {
  id: ID; customerId: ID; businessId: ID; version: number;
  firstName?: string; lastName?: string; middleName?: string;   /* PII */
  preferredName?: string; suffix?: string;
  roles: ('primary'|'billing'|'site_contact'|'fleet_manager'|'spouse'|'driver'|'other')[];
  title?: string;                    // job title (business)
  isPrimary: boolean;
  notes?: string;
  archivedAt?: ISOTime;
}

interface ContactPoint {
  id: ID; customerId: ID; personId?: ID; businessId: ID; version: number;
  type: 'phone'|'email';
  valueRaw: string;                  /* PII */
  valueNormalized: string;           /* PII */ // E.164 or lowercased email
  valueHash: string;                 // HMAC(businessSalt, normalized) for erasure-safe suppression
  ext?: string;
  label: 'mobile'|'home'|'work'|'other'|'billing';
  lineType?: 'mobile'|'landline'|'voip'|'toll_free'|'unknown';
  formatStatus: 'valid'|'unverified_format'|'invalid';
  deliveryStatus: 'ok'|'soft_bounce'|'hard_bounce'|'carrier_unreachable'|'complaint';
  isPrimary: boolean;                // one primary per type per customer
  verifiedAt?: ISOTime;              // OTP/magic link (from 01) or reply
  notifyOnJobs: boolean;             // receives job notifications by default [S: T04 contact to notify]
  provenance: 'staff'|'customer'|'import'|'enrichment'|'lead_form'|'booking_site';
  provenanceDetail?: { provider?: string; obtainedAt?: ISOTime; confidence?: 'low'|'medium'|'high' };
  archivedAt?: ISOTime;
}

interface Address {
  id: ID; customerId: ID; businessId: ID; version: number;
  label?: string;                    // Home, Work, Shop, Site #3
  line1: string; line2?: string; locality: string; region?: string;
  postalCode?: string; countryCode: string;            /* PII */
  normalizedKey: string;
  lat?: number; lng?: number;
  geocodeStatus: 'pending'|'ok'|'failed'|'manual';
  geocodeQuality?: 'rooftop'|'range'|'centroid';
  serviceAreaId?: ID;                // from 02
  outOfArea: boolean;
  accessNotes?: string;              // worker-visible
  gateCode?: string;                 /* SECRET */
  parkingNotes?: string;
  isPrimary: boolean; isBilling: boolean;
  propertyAssetId?: ID;
  timezone?: string;
  archivedAt?: ISOTime;
}

interface CustomerType { id: ID; businessId: ID; name: string; color?: string; isDefault: boolean; }

interface Tag { id: ID; businessId: ID; name: string; nameKey: string; color: string;
  scopes: ('customer'|'lead'|'asset'|'job')[]; archivedAt?: ISOTime; }
interface TagAssignment { tagId: ID; entityType: 'customer'|'lead'|'asset'; entityId: ID;
  businessId: ID; assignedAt: ISOTime; assignedBy: ActorRef; }

interface Note {
  id: ID; businessId: ID; entityType: 'customer'|'lead'|'asset'; entityId: ID;
  body: string; pinned: boolean;
  visibility: 'internal'|'worker_visible'|'customer_visible';
  authorRef: ActorRef; editedAt?: ISOTime; deletedAt?: ISOTime;
  attachments: ID[];
}

interface IntakeResponse {           // [S: T05] answers saved to profile
  id: ID; customerId: ID; businessId: ID;
  formId: ID; formVersionId: ID;     // definition owned by 01
  answers: { questionId: ID; value: unknown; fileIds?: ID[] }[];
  submittedBy: ActorRef; submittedAt: ISOTime;
  isCurrent: boolean;                // latest per (customer, form)
}

interface CustomerStats {           // projection; rebuildable
  customerId: ID; businessId: ID;
  jobsCompleted: number; jobsCancelled: number;
  firstServiceAt?: ISOTime; lastServiceAt?: ISOTime; lastServiceLocalDate?: ISODate;
  nextJobAt?: ISOTime;
  lifetimeRevenueCents: Cents; tipsCents: Cents; avgTicketCents?: Cents;
  balanceDueCents: Cents; currency: string;
  avgRatingGiven?: number; ratingsCount: number;
  hasCardOnFile: boolean;            // from 05
  subscriptionStatus?: 'active'|'past_due'|'paused'|'none'; // from 06
  lastContactedAt?: ISOTime;         // from 08
  openLeadsCount: number; vehiclesCount: number;
  projectedFromEventSeq: Record<string, number>; // per source stream, for idempotency
}

// ---------- 7.2 Consent ----------
interface ConsentEvent {             // append-only ledger
  id: ID; businessId: ID;
  contactPointId?: ID;               // null after erasure
  contactValueHash: string;          // survives erasure for suppression
  channel: 'sms'|'email'|'voice'|'push'|'postal';
  purpose: 'transactional'|'marketing';
  action: 'grant'|'grant_implied'|'revoke';
  method: 'web_form'|'booking_form'|'lead_form'|'portal_toggle'|'staff_verbal'|'staff_paper'
        |'import_attested'|'sms_keyword'|'email_unsubscribe'|'api'|'system_erasure';
  evidence: {
    disclosureVersionId?: ID; disclosureTextHash?: string;
    ip?: string; userAgent?: string; pageUrl?: string;
    keyword?: string; inboundMessageId?: ID;
    importJobId?: ID; attestationText?: string;
    staffNote?: string; documentFileId?: ID;
  };
  evidenceLevel: 'strong'|'standard'|'weak';
  actor: ActorRef;
  occurredAt: ISOTime;               // when consent was given (may differ from recordedAt for paper)
  recordedAt: ISOTime;
}
interface ConsentState {             // projection
  contactPointId: ID; channel: ConsentEvent['channel']; purpose: ConsentEvent['purpose'];
  status: 'not_set'|'granted'|'granted_implied'|'revoked';
  lastEventId: ID; since: ISOTime;
}
interface ConsentDisclosureVersion { id: ID; businessId: ID; channel: string; purpose: string;
  text: string; textHash: string; locale: string; effectiveFrom: ISOTime; }

// ---------- 7.3 Portal link ----------
interface PortalAccountLink {
  accountId: ID;                     // owned by 01
  customerId: ID; businessId: ID;
  matchMethod: 'verified_email'|'verified_phone'|'created_new'|'staff_linked';
  linkedAt: ISOTime; linkedBy: ActorRef; active: boolean;
}

// ---------- 7.4 Assets ----------
interface Asset {
  id: ID; businessId: ID; customerId: ID; version: number;
  kind: 'vehicle'|'property'|'equipment'|'other';
  nickname?: string;
  status: 'active'|'archived'|'merged';
  archivedReason?: 'sold'|'scrapped'|'duplicate'|'other';
  mergedIntoId?: ID;
  addressId?: ID;                    // property: required; equipment: location
  photoIds: ID[]; coverPhotoId?: ID; // [S: llms photos]
  customFields: Record<string, unknown>;
  createdVia: 'crm'|'portal'|'booking_site'|'team_app'|'import'|'api';
  ownershipHistory: { customerId: ID; from: ISOTime; to?: ISOTime }[];
}

interface VehicleDetail {
  assetId: ID;
  vin?: string;                      // 17 chars normalized
  vinValid?: boolean;                // check digit result
  plate?: string; plateRegion?: string;      // e.g. 'US-TX', 'CA-ON'
  plateKey?: string;
  year?: number; make?: string; model?: string; trim?: string;
  makeId?: string; modelId?: string; // catalog ids
  bodyClass?: string; sizeClass?: 'car'|'suv'|'truck'|'van'|'heavy'|'motorcycle'|'other';
  sizeClassOverridden: boolean;
  colorName?: string; colorHex?: string; paintCode?: string;  // [S: llms colors]
  interiorColor?: string;
  engine?: string; fuelType?: string; drivetrain?: string; transmission?: string; doors?: number;
  factoryTireFront?: string; factoryTireRear?: string;         // [S: llms tire sizes]
  customTireFront?: string; customTireRear?: string;
  odometerUnit: 'mi'|'km';
  latestOdometer?: number; latestOdometerAt?: ISOTime;
  decodeSource?: 'nhtsa_vpic'|'commercial'|'manual';
  decodedAt?: ISOTime; decodeRaw?: unknown;
}

interface PropertyDetail { assetId: ID; propertyType?: 'house'|'apartment'|'condo'|'commercial'|'other';
  squareFeet?: number; stories?: number; bedrooms?: number; bathrooms?: number;
  lotSizeSqFt?: number; hasPool?: boolean; pets?: string; surfaces?: string[]; }

interface EquipmentDetail { assetId: ID; manufacturer?: string; model?: string;
  serialNumber?: string; installDate?: ISODate; warrantyUntil?: ISODate; }

interface OdometerReading { id: ID; assetId: ID; value: number; unit: 'mi'|'km';
  recordedAt: ISOTime; source: 'job'|'manual'|'portal'|'import'; jobId?: ID;
  flag?: 'rollback_suspect'|'confirmed_replacement'; recordedBy: ActorRef; }

interface AssetServiceRecord {      // projection from 04 job events
  assetId: ID; jobId: ID; businessId: ID; customerIdAtService: ID;
  completedAt: ISOTime; localDate: ISODate;
  serviceNames: string[]; addOnNames: string[];
  workerIds: ID[]; odometer?: number; photoIds: ID[]; totalCents?: Cents;
  notes?: string;
}

interface VehicleHistoryReport {     // optional connector (CARFAX-like) [S: llms]
  id: ID; assetId: ID; provider: string; region: 'US'|'CA'|string;
  fetchedAt: ISOTime; reportUrl?: string; summary?: unknown; costCents?: Cents;
}

// ---------- 7.5 Leads ----------
type LeadSource = 'manual'|'address_only'|'lead_form'|'booking_site'|'postcard_qr'|'qr_other'
  |'referral'|'phone_call'|'walk_in'|'field_referral'|'import'|'api'|'ai_assistant'|`custom:${string}`;

interface Pipeline { id: ID; businessId: ID; name: string; isDefault: boolean; order: number; archivedAt?: ISOTime; }
interface Stage { id: ID; pipelineId: ID; name: string; order: number;
  kind: 'open'|'won'|'lost'; probabilityPct: number; rotAfterDays?: number; color?: string; }

interface Lead {
  id: ID; businessId: ID; version: number;
  pipelineId: ID; stageId: ID; stageEnteredAt: ISOTime;
  status: 'open'|'won'|'lost'|'merged'|'deleted';
  title: string;                     // derived: name or address
  customerId?: ID;                   // attached existing customer [S: T20]
  contactSnapshot?: {                /* PII */
    firstName?: string; lastName?: string; companyName?: string;
    phones: { value: string; e164?: string; contactPointId?: ID }[];
    emails: { value: string; normalized?: string; contactPointId?: ID }[];
    address?: Omit<Address,'id'|'customerId'|'businessId'|'version'|'isPrimary'|'isBilling'|'outOfArea'|'normalizedKey'|'geocodeStatus'> & { normalizedKey?: string };
  };
  addressId?: ID; assetIds: ID[];
  assetHint?: { year?: number; make?: string; model?: string; plate?: string; text?: string };
  source: LeadSource; sourceDetail?: string;
  sourceRef?: { campaignId?: ID; qrId?: ID; recipientToken?: string; formId?: ID;
                attemptIds?: ID[]; referralCode?: string; utm?: Record<string,string>; bookingSource?: string };
  ownerUserId?: ID;
  interestServiceIds: ID[]; interestAddOnIds: ID[];
  estimatedValueCents?: Cents; currency: string;
  quoteJobId?: ID;                   // 04 quote on same job id [S: T04]
  convertedJobId?: ID; convertedAt?: ISOTime; wonReason?: 'converted'|'self_completed'|'manual';
  lostReasonId?: ID; lostNote?: string;
  nextFollowUpAt?: ISOTime; followUpAssigneeId?: ID;
  funnel?: { furthestStep: string; furthestStepIndex: number; lastStepAt: ISOTime;
             live: boolean; cartValueCents?: Cents; selectedSlot?: { start: ISOTime; workerId?: ID } };
  enrichmentStatus: 'none'|'pending'|'enriched'|'no_match'|'failed'|'expired';
  score?: number;                    // 0–100 [I]
  lastActivityAt: ISOTime;
  mergedIntoId?: ID;
}

interface LeadActivity {             // append-only [S: T10 every action logged]
  id: ID; leadId: ID; businessId: ID;
  type: 'created'|'field_changed'|'stage_changed'|'owner_changed'|'note'|'call_logged'
      |'visit_logged'|'sms_sent'|'sms_received'|'email_sent'|'email_received'
      |'followup_set'|'followup_done'|'attempt_step'|'attempt_abandoned'|'attempt_completed'
      |'qr_scanned'|'form_submitted'|'enrichment_requested'|'enrichment_result'
      |'quote_sent'|'quote_viewed'|'quote_accepted'|'converted'|'lost'|'reopened'
      |'merged'|'attached_to_customer'|'consent_changed';
  payload: Record<string, unknown>;
  actor: ActorRef; occurredAt: ISOTime;
}

interface LostReason { id: ID; businessId: ID; label: string; order: number; archivedAt?: ISOTime; }

interface BookingAttemptLink {      // 07's view of 01's attempt
  attemptId: ID; businessId: ID; leadId?: ID; visitorId: string;
  startedAt: ISOTime; lastEventAt: ISOTime;
  furthestStep: string; furthestStepIndex: number;
  state: 'anonymous'|'identified'|'abandoned'|'completed'|'purged';
  bookingSource?: string; utm?: Record<string,string>; referrer?: string; device?: string;
  cart?: { serviceIds: ID[]; addOnIds: ID[]; subtotalCents: Cents };
  appointmentId?: ID;
}

interface EnrichmentRequest {
  id: ID; businessId: ID; leadId: ID; provider: string;
  inputAddressHash: string;
  status: 'pending'|'completed'|'no_match'|'failed'|'applied'|'expired';
  requestedBy: ActorRef; purposeAttestation: string;
  costCents: Cents; requestedAt: ISOTime; completedAt?: ISOTime; expiresAt: ISOTime;
  candidates?: { name?: string; phones?: { value: string; lineType?: string; confidence: string }[];
                 emails?: { value: string; confidence: string }[]; }[];
}

// ---------- 7.6 Duplicates & merges ----------
interface DuplicateCandidate { id: ID; businessId: ID; entityType: 'customer'|'lead'|'asset';
  entityIds: ID[]; score: number; reasons: string[];
  status: 'open'|'merged'|'dismissed'|'snoozed'; detectedAt: ISOTime; }
interface MergeRecord { id: ID; businessId: ID; entityType: 'customer'|'lead'|'asset';
  survivorId: ID; mergedIds: ID[]; fieldDecisions: Record<string, ID>;
  snapshot: unknown;                 // before-images for unmerge
  movedRefs: Record<string, number>; actor: ActorRef; at: ISOTime; undoUntil: ISOTime; undoneAt?: ISOTime; }

// ---------- 7.7 Import / export ----------
interface ImportJob {
  id: ID; businessId: ID; entityType: 'customer'|'asset'|'lead';
  fileId: ID; fileHash: string; fileName: string; rowCount?: number;
  encoding?: string; delimiter?: string;
  mapping: { column: string; target: string | null; transform?: string }[];
  options: { dedupe: 'skip'|'update_fill_blanks'|'update_overwrite'|'create_always';
             matchKeys: ('email'|'phone'|'external_id'|'name_address')[];
             defaultCountry: string; addTagIds: ID[]; suppressAutomations: boolean;
             consentAttestation: { sms: 'none'|'express_written'; email: 'none'|'opt_in' }; };
  status: 'uploaded'|'mapping'|'validating'|'validated'|'running'|'completed'
        |'completed_with_errors'|'cancelled'|'failed'|'reverted'|'partially_reverted';
  counts: { created: number; updated: number; skipped: number; errors: number; warnings: number; duplicatesInFile: number };
  lastCommittedBatch: number; resultFileId?: ID; startedBy: ActorRef; undoUntil?: ISOTime;
}
interface ImportRowResult { importJobId: ID; rowNumber: number; outcome: 'created'|'updated'|'skipped'|'error';
  entityId?: ID; errorCode?: string; message?: string; beforeImage?: unknown; }
interface ExportJob { id: ID; businessId: ID; entityType: string; filterHash: string; columns: string[];
  rowCount?: number; status: 'running'|'ready'|'failed'|'expired'; fileId?: ID; expiresAt?: ISOTime; requestedBy: ActorRef; }

// ---------- 7.8 Saved views ----------
interface SavedView { id: ID; businessId: ID; entityType: 'customer'|'asset'|'lead';
  name: string; filter: FilterExpr; columns: string[]; sort: { field: string; dir: 'asc'|'desc' }[];
  layout?: 'board'|'list'|'funnel'|'map'|'calendar'; ownerUserId?: ID; shared: boolean; }
type FilterExpr = { and?: FilterExpr[]; or?: FilterExpr[]; not?: FilterExpr;
  field?: string; op?: 'eq'|'neq'|'in'|'nin'|'gt'|'gte'|'lt'|'lte'|'between'|'contains'|'exists'|'relative_days_gt'|'relative_days_lt';
  value?: unknown; };

// ---------- 7.9 Audit ----------
interface AuditEntry { id: ID; businessId: ID; entityType: string; entityId: ID;
  action: string; diff?: { field: string; before?: unknown; after?: unknown }[];
  actor: ActorRef; at: ISOTime; requestId: string; }
```

### 7.10 Keys, indexes and constraints [I]
- Unique: `(businessId, Tag.nameKey)`; `(businessId, VehicleDetail.vin)` where asset active;
  `(customerId, ContactPoint.valueNormalized, type)` where not archived; one primary contact
  point per (customer, type); one primary address per customer; `(businessId, Stage.pipelineId, order)`.
- Indexes: ContactPoint `(businessId, valueNormalized)` (matching); phone digits suffix index
  (last 4–10 digits) for search; trigram on `Customer.displayName`, `Address.line1`;
  `CustomerStats(businessId, lastServiceLocalDate)`; `AssetStats(businessId, lastServiceLocalDate)`;
  `Lead(businessId, stageId, lastActivityAt)`; `Lead(businessId, nextFollowUpAt)`;
  `BookingAttemptLink(businessId, attemptId)` unique.
- Multi-tenancy: every row has `businessId`; every query predicate includes it; cross-tenant
  matching never happens (a person who is a customer of two businesses on the platform is two
  unrelated records) [I].

### 7.11 Retention [I]
| Data | Retention |
|---|---|
| Anonymous booking attempts | 30 days |
| Enriched contact values (unconverted) | 90 days |
| Import source files | 30 days; results reports 90 days |
| Export files | 24 h link, file deleted 7 days |
| Merge snapshots | 30 days |
| Consent ledger | life of business account + 5 years (minimized after erasure) [I-legal] |
| Audit entries | 2 years starter, longer on higher tiers |

### 7.12 Permission keys declared by 07 (matrix owned by 09)
`customers.view`, `customers.view_all` (vs. assigned-jobs only), `customers.create`,
`customers.edit`, `customers.edit_sensitive` (tax ID, gate codes), `customers.view_gate_codes`,
`customers.block`, `customers.archive`, `customers.merge`, `customers.erase`,
`customers.import`, `customers.export`, `customers.consent_edit`, `customers.custom_fields_admin`,
`assets.view`, `assets.edit`, `assets.transfer`, `assets.history_reports` (paid lookups),
`leads.view_own`, `leads.view_all`, `leads.create`, `leads.edit`, `leads.assign`,
`leads.delete`, `leads.enrich`, `leads.pipeline_admin`, `settings.customers_admin`.

### 7.13 Addendum (2026-11-13) — fields required by other agents' specs
Added after reading 01–09 drafts; these extend the interfaces above.
```ts
interface Customer {                 // additions
  blockedWorkerIds: ID[];            // "do not send this worker" — 03 eligibility rule E4 consumes
                                     // via customer.worker_blocklist.updated [03 §9]
  brandingLocale?: string;           // which tenant copy-override locale this customer sees (01/08)
}
type LeadSource = /* … */ 'out_of_area';   // 01 "Ask us anyway" / 02 out-of-area capture
interface Lead {                     // additions
  outOfArea?: { pointRounded: { lat: number; lng: number }; nearestZoneId?: ID; distanceKm?: number };
  resumeToken?: string;              // 01's `/r/{resumeToken}` link restoring the session [01 §abandonment]
  holdExpiredAt?: ISOTime;           // 03 `scheduling.hold.expired` — strong-intent signal
}
```

---

## 8. API surface

Stack-agnostic command/query catalogue. Transport (REST/RPC/GraphQL) is a Stage-2 decision; the
same operations are exposed to the CRM UI, the team app, the booking site (customer scope), the
public API and AI assistants (MCP-style), mirroring Fieldd's CRM-scope vs. Bookings-scope split
[S: llms]. Conventions [I]:
- Every mutating command takes `idempotencyKey` (client-generated UUID; stored 24 h) and, for
  updates, `expectedVersion` (→ `409 VERSION_CONFLICT` with current record).
- Errors: `{ code, message, field?, details? }`; codes listed per command.
- Lists use cursor pagination (`cursor`, `limit ≤ 200`), stable sort with `id` tie-breaker.
- Scope column: **B** = business staff (permission key in brackets), **C** = authenticated
  customer (own records only), **P** = public/anonymous (rate-limited, bot-protected),
  **S** = internal service-to-service (other modules), **AI** = allowed for assistant tokens.

### 8.1 Customers
| Name | Input | Output | Scope | Errors |
|---|---|---|---|---|
| `customers.create` | CustomerDraft {kind, names, people[], contactPoints[], addresses[], tags[], customerTypeId, taxExempt, defaultDiscountId, preferredWorkerIds, source, notes, customFields, consents[], assets[]?, suppressAutomations?, createdVia} | Customer + ids of children | B[`customers.create`], S, AI | `DUPLICATE_VERIFIED_CONTACT` (with matchId), `VALIDATION`, `INVALID_PHONE`, `INVALID_EMAIL` |
| `customers.update` | id, expectedVersion, patch (field-level) | Customer | B[`customers.edit`], AI | `VERSION_CONFLICT`, `FORBIDDEN_FIELD` |
| `customers.get` | id, include[] (people, contactPoints, addresses, stats, assets, consents, intake, tags) | Customer aggregate; if merged → `{ mergedIntoId }` with 301-like semantic | B[`customers.view`], S, AI (masked secrets) | `NOT_FOUND`, `OUT_OF_VISIBILITY_WINDOW` |
| `customers.list` | filter: FilterExpr, sort, columns, cursor, limit, includeArchived | page of CustomerRow + total (approximate above 10 k) | B, AI | `FILTER_INVALID` |
| `customers.search` | q, types[] (customer, lead, asset), limit | grouped hits with highlight | B, AI | — |
| `customers.findMatches` | partial {names, phones, emails, address}, mode: 'suggest'\|'strict' | candidates [{customerId, score, reasons, displaySafe}] | B, S | — |
| `customers.resolveForBooking` (= 01 `MatchCustomer`/`UpsertCustomer`) | attemptId/sessionId, contact {name, phone, email}, address, assets[], consents[], intake?, accountId? | {customerId, personId, addressId, assetIds, contactPointIds, blocked: boolean, created: boolean} — never returns profile data to caller | S (01 only) | `BLOCKED_CUSTOMER` (soft; boolean), `VALIDATION` |
| `customers.addContactPoint` / `updateContactPoint` / `archiveContactPoint` / `setPrimary` | customerId, data | ContactPoint | B, C (own), AI | `DUPLICATE_ON_CUSTOMER`, `LAST_CONTACT_POINT` (warn) |
| `customers.addPerson` / `updatePerson` / `archivePerson` / `movePerson` | … | Person | B | `PRIMARY_REQUIRED` |
| `customers.addAddress` / `updateAddress` / `archiveAddress` | … | Address (geocodeStatus) | B, C (own), AI | `ADDRESS_IN_USE_FUTURE_JOBS` |
| `customers.setTags` / `customers.bulkTag` | ids or filter snapshot, add[], remove[] | {affected, jobId?} | B | `TOO_MANY_TAGS` |
| `customers.setStatus` | id, status, reason | Customer | B[`customers.block`/`archive`] | `HAS_FUTURE_JOBS` |
| `customers.setWorkerBlocklist` | id, workerIds[] | Customer | B[`customers.edit`] | — |
| `customers.merge` | survivorId, mergedIds[], fieldDecisions | MergeRecord | B[`customers.merge`]; AI only with human confirmation token | `MERGE_CONFLICT` (details), `ALREADY_MERGED` |
| `customers.unmerge` | mergeRecordId | Customer[] | B[`customers.merge`] | `UNDO_EXPIRED` |
| `customers.erase` | id, confirmationText | ErasureReceipt | B[`customers.erase`] (owner) | `HAS_BALANCE`, `HAS_ACTIVE_SUBSCRIPTION`, `HAS_FUTURE_JOBS` |
| `customers.exportPersonalData` | id | file link (JSON + CSV) | B, C (own) | — |
| `customers.duplicates.list` / `.dismiss` / `.snooze` | … | DuplicateCandidate[] | B | — |
| `customers.notes.add` / `.edit` / `.delete` / `.pin` | entityType, entityId, body, visibility | Note | B, worker (job-scoped) | — |
| `customers.saveIntakeResponse` | customerId, formVersionId, answers | IntakeResponse | C (own), S (01), B | `FORM_VERSION_UNKNOWN`, `VALIDATION` |
| `customers.getServiceHistory` | customerId, assetId?, cursor | rows | B, C (own; customer-safe fields), AI | — |
| `customers.consent.record` | contactPointId, channel, purpose, action, method, evidence | ConsentState | B[`customers.consent_edit`], C (own toggles), S (01/08) — **see conflict C1 §9.6** | `EVIDENCE_REQUIRED`, `REGRANT_NOT_ALLOWED` |
| `customers.consent.check` | contactPointId \| endpoint, channel, purpose | {allowed, status, reason} | S (08) | — |
| `customers.import.create` / `.setMapping` / `.validate` / `.run` / `.cancel` / `.undo` / `.get` | per §4 W3 | ImportJob | B[`customers.import`] | `FILE_TOO_LARGE`, `ENCODING_UNSUPPORTED`, `MAPPING_REQUIRED_FIELD`, `UNDO_EXPIRED` |
| `customers.export.create` / `.get` | entity, filter, columns, format | ExportJob / stream | B[`customers.export`] (AI blocked by default) | `RATE_LIMITED` |
| `customers.savedViews.*` | CRUD | SavedView | B | — |
| `customers.settings.*` | customer types, tags, custom fields, dedupe rules, retention | settings | B[`settings.customers_admin`] | `TAG_IN_USE` |

### 8.2 Assets
| Name | Input | Output | Scope |
|---|---|---|---|
| `assets.create` | customerId, kind, details (Vehicle/Property/Equipment), photos[], nickname | Asset | B[`assets.edit`], C (own garage), S (01, 03, 04), AI |
| `assets.update` / `assets.archive` / `assets.restore` | id, expectedVersion, patch / reason | Asset | B, C (own; cannot edit service records) |
| `assets.get` | id, include[] (history, odometer, photos, reports) | Asset aggregate | B, C (own), worker (job-scoped), AI |
| `assets.list` | filter (incl. lastServiceDate relative/absolute [S: llms]), sort, cursor | page | B, AI |
| `assets.vehicle.decodeVin` | vin | {valid, checkDigitOk, year, make, model, trim, bodyClass, sizeClass, engine, tireSizes?, source} | B, C, S, P (booking site; rate-limited) |
| `assets.vehicle.lookupPlate` | plate, region | {vin?, decode?} or `NOT_AVAILABLE_ON_PLAN` | B, C, S (gated) |
| `assets.vehicle.catalog` | year? make? model? | years / makes / models / trims lists (cached, CDN-able) | P, B, C |
| `assets.recordOdometer` | assetId, value, unit, jobId?, recordedAt | OdometerReading (+ flag) | B, worker, C |
| `assets.photos.uploadUrl` / `.attach` / `.remove` | assetId, contentType, size | signed URL / Asset | B, worker, C |
| `assets.transfer` | assetId, toCustomerId, shareHistory | Asset | B[`assets.transfer`] |
| `assets.merge` | survivorId, mergedIds | MergeRecord | B |
| `assets.history.fetchReport` | assetId, provider | VehicleHistoryReport (cost shown first; confirm token) | B[`assets.history_reports`] (gated) |
| `assets.recalls.get` | assetId | recall list | B, C |

### 8.3 Customer-scope API (booking site / customer app; consumed by 01)
`me.profile.get/update` (whitelisted fields: names, contact points (new ones unverified until
OTP), addresses, locale), `me.assets.*` (garage) [S: T05], `me.intake.get/save` [S: T05],
`me.consents.get/update` (preference centre), `me.serviceHistory.list` (for "rebook previous
services" [S: T05]), `me.dataExport.request`, `me.deletion.request` (creates a staff task; not
instant because of balances/jobs) [I]. All require an account session issued by 01 whose
`customerId` claim comes from `PortalAccountLink`.

### 8.4 Leads
| Name | Input | Output | Scope |
|---|---|---|---|
| `leads.create` | mode ('scratch'\|'existing_customer'\|'address_only'), contact/customerId/address, interests, assetHint, source, sourceRef, ownerUserId?, stageId?, estimatedValueCents?, followUp?, consents[] | Lead (+ duplicate warnings) | B[`leads.create`], worker (field referral), S (01, 08), AI |
| `leads.submitForm` (= public lead form) | formId, fields, consents, disclosureVersionId, utm, honeypot, renderedAt | {ok} (never reveals duplicates) | P (rate-limited per IP + per business; challenge on abuse) |
| `leads.ingestAttemptEvent` | attempt event envelope (01) | {leadId?} | S (01 only) |
| `leads.ingestQrScan` | qrId, campaignId, recipientToken?, visitorId | {leadId?} | S (01/08) |
| `leads.update` | id, expectedVersion, patch | Lead | B[`leads.edit`] (own unless `leads.view_all`) |
| `leads.moveStage` | id, toStageId, expectedVersion, lostReasonId?, note? | Lead | B |
| `leads.assign` | ids[], ownerUserId | {affected} | B[`leads.assign`] |
| `leads.logActivity` | id, type (note/call/visit/email/sms), body, occurredAt, durationSec? | LeadActivity | B, worker (own) |
| `leads.setFollowUp` / `leads.completeFollowUp` | id, at, assigneeId | Lead | B |
| `leads.attachToCustomer` | id, customerId | Lead | B |
| `leads.convert` | id, mode ('appointment'\|'quote'), bookingDraft (03/04 payload) | {customerId, jobId} | B (needs 03/04 booking permissions too) |
| `leads.markLost` / `leads.reopen` | id, reasonId, note | Lead | B |
| `leads.merge` | survivorId, mergedIds | MergeRecord | B |
| `leads.delete` | id | — | B[`leads.delete`] |
| `leads.enrich.request` / `.apply` / `.get` | leadId, provider, attestation / candidate selection | EnrichmentRequest | B[`leads.enrich`] (owner/admin), never AI |
| `leads.board` | pipelineId, layout, filter, sort | columns with cards + counts + summed values | B, AI |
| `leads.funnel` | dateRange, bookingSource? | step counts, conversion, recovery | B |
| `leads.pipelines.*` / `stages.*` / `lostReasons.*` / `sources.*` | CRUD + reorder | — | B[`leads.pipeline_admin`] |
| `leads.subscribe` (realtime) | pipelineId | stream of {lead.created/updated/moved} | B |

### 8.5 Internal read models offered to other modules (S scope)
- `customers.summary(customerId)` → {displayName, kind, status, primary contact (masked per
  caller permission), primaryAddress (geo), tags, customerTypeId, taxExempt (+cert ref),
  defaultDiscountId, blockedWorkerIds, preferredWorkerIds, hasPortalAccount, timezone, locale}
  — used by 03 job cards, 04 documents, 05 POS, 06 eligibility.
- `customers.segmentQuery(FilterExpr)` → streaming IDs + consent-eligible endpoints per channel
  (08 audience builder; 08 re-checks consent at send time).
- `customers.newCustomerCheck(customerId | {phone,email,address})` → {isNew, matchedIds} (06
  "new customer" discount anti-abuse [06 §5.7]).
- `assets.summary(assetIds[])` → {YMM, color, plate, sizeClass, photo thumb, tire sizes} for
  03 calendar "vehicle on every job" [S: llms], 04 job card, 02 pricing variation selection.

---

## 9. Interfaces with other modules

Event envelope (final shape owned by Agent 10) assumed: `{ eventId, type, businessId,
occurredAt, actor, entityId, version, payload, causationId, correlationId }`. Delivery
at-least-once; consumers idempotent on `eventId` and use `version` to drop stale updates [I].
PII in payloads is minimized: events carry IDs, consumers call `customers.summary` for details.

### 9.1 PRODUCES (07 → others)
| Event | Payload (beyond envelope) | Consumers |
|---|---|---|
| `customer.customer.created` | kind, source, createdVia, tags, customerTypeId, serviceAreaId, consentSummary, suppressAutomations, importJobId?, leadId? | 08 ("new customer" drip trigger [S: T12], reports), 06 (new-customer eligibility), 10 |
| `customer.customer.updated` | changedFields[] (names only) | 03/04 (cache), 08 |
| `customer.customer.status_changed` | from, to, reason | 01 (blocked → refuse online booking), 08 (suppress), 03 (warn on future jobs) |
| `customer.customer.merged` | survivorId, mergedIds, undoToken | **03, 04, 05, 06, 08, 01** must re-point or resolve `mergedIntoId` lazily |
| `customer.customer.unmerged` | restoredIds | same |
| `customer.customer.erased` | erasureReceiptId | 03 (cancel future), 04/05 (anonymize documents' PII snapshots where law allows), 06 (cancel subs — 06 transition 28), 08 (purge inbox PII, keep suppression hash), 01 (delete account) |
| `customer.address.added` / `.updated` / `.geocoded` / `.archived` | addressId, geoChanged, serviceAreaId, outOfArea | 03 (recompute travel for future jobs [03 §9]), 02 |
| `customer.contact_point.added` / `.updated` / `.verified` / `.archived` | contactPointId, type, label, verified | 08 (routing), 01 (OTP identifiers) |
| `customer.consent.changed` | contactPointId, endpointHash, channel, purpose, status, method | 08 — **see conflict C1** |
| `customer.tags.changed` / `customer.tags.bulk_changed` | ids, added[], removed[] | 08 (dynamic audiences), 06 (tag-targeted discounts if any) |
| `customer.worker_blocklist.updated` | blockedWorkerIds | 03 (eligibility E4) |
| `customer.account.linked` / `.relinked` | accountId, matchMethod | 01 (session scope), 08 ("has app installed") |
| `customer.intake.updated` | formVersionId, answeredQuestionIds | 04 (job intake complete? → 08 `job.intake.incomplete`) |
| `customer.import.validated` / `.completed` / `.reverted` / `.batch_committed` | counts, ids (chunked) | 08 (importer notification, optional drip enrollment), 10 |
| `customer.export.completed` | filterHash, rowCount | 08 (owner security digest) |
| `customer.privacy.request_completed` | type (export/erasure) | 08 (confirmation message) — name taken from 08's catalog |
| `asset.asset.created` / `.updated` / `.archived` / `.transferred` / `.merged` | assetId, kind, customerId, changedFields | 03 (job cards), 04, 02 (size-class pricing; 02 calls this `customer.asset.updated` — alias), 08 (audiences) |
| `asset.vehicle.decoded` | assetId, year, make, model, sizeClass | 02, 08 |
| `asset.service_record.added` | assetId, jobId, completedAt, odometer | 08 ("service due" reminders, vehicle audiences) |
| `asset.odometer.recorded` | assetId, value, unit, flag | 08 (mileage-based reminders) |
| `lead.lead.created` | source, sourceRef, ownerUserId, customerId?, hasPhone, hasEmail, consentSummary | 08 (staff alert, lead drips, reports), 09 (n/a) |
| `lead.lead.stage_changed` | fromStageId, toStageId | 08 (pipeline reports, stage-triggered drips) |
| `lead.lead.assigned` | ownerUserId | 08 (notify new owner) |
| `lead.attempt.abandoned` | attemptId, furthestStep, cartValueCents, resumeToken, consent flags | 08 ("finish your booking" drip [S: T12]) |
| `lead.lead.won` / `lead.lead.converted` | jobId, customerId, valueCents, source attribution | 08 (cancel recovery drips; conversion reports), 01 (session converted) |
| `lead.lead.lost` / `.reopened` / `.merged` / `.deleted` | reasonId | 08 (win-back drips) |
| `lead.followup.scheduled` / `lead.followup.due` | assigneeId, dueAt | 08 (reminder push/email to staff) |
| `lead.lead.stale` | daysInStage | 08 (digest) |
| `lead.enrichment.completed` | fieldsFound, costCents | 05 (usage billing if platform-resold credits), 08 (none) |

**Notification triggers fired by this module** (catalog + copy owned by 08): new lead assigned
to you; follow-up due/overdue; lead won by self-completed booking; import finished/failed;
export ready; duplicate queue weekly digest; consent revoked (owner digest — 08 A-21); customer
privacy request received/completed; enrichment budget 80 %/100 %; vehicle service due
(customer-facing, marketing vs. transactional classification by 08).

### 9.2 CONSUMES (others → 07)
| From | Event / call | 07 reaction |
|---|---|---|
| 01 | `portal.account.created` (01's name; my §4 W6 used `portal.account.registered` — **01's name is canonical**) | W6 match/link/create → `customer.account.linked` |
| 01 | `portal.session.started` / `portal.session.step_reached` / `portal.session.contact_captured` / `portal.session.payment_started` / `portal.session.abandoned` / `portal.booking.confirmed` (01's names; my §4 W16 `booking.attempt.*` are aliases — **01's names canonical**) | W16 upsert lead; `payment_started` → "high intent" flag; `abandoned` → `lead.attempt.abandoned`; `confirmed` → Won |
| 01 | `portal.visit.recorded` | funnel top-of-funnel counts only (no lead) [S: T05] |
| 01 | sync calls `MatchCustomer`, `UpsertCustomer`, `CreateLead{reason:'out_of_area'}` | served by `customers.findMatches(strict)`, `customers.resolveForBooking`, `leads.create(source:'out_of_area')` |
| 01 | QR scan (campaign/other QR) | `leads.ingestQrScan` |
| 02 | `coverage.location.out_of_area_attempted` (point rounded ~100 m, sessionId) | attach to session's lead (if contact captured) as `outOfArea`; no lead from the event alone |
| 02 | `catalog.service.archived` | leads' `interestServiceIds` show "(archived)"; no mutation |
| 02 | service-area changes | re-resolve `Address.serviceAreaId` in background |
| 03 | `scheduling.hold.expired` (sessionId) | lead `holdExpiredAt` (intent signal) |
| 03 | `scheduling.job.scheduled` / `.rescheduled` / `.unscheduled` | `CustomerStats.nextJobAt`, asset upcoming |
| 03/04 | `job.job.completed` (alias 04: `jobs.job.completed`), `job.job.reopened`, `job.job.cancelled`, `job.job.no_show` | W12 projections (lastServiceAt, jobsCompleted, asset service records, odometer); no-show count on profile |
| 04 | `jobs.job.created {leadId, origin:'lead_conversion'}` and quote accepted | per 04 W17: lead Won when job reaches `scheduled` (appointment) or quote `approved`; **see C3** |
| 04 | quote sent/viewed/declined | LeadActivity on linked lead; declined → 04 says lead → Lost (we propose → "Follow-up" stage, owner decides; **C3**) |
| 05 | `payments.card.saved` / removed / expiring (08 `payment.card.expiring`) | `CustomerStats.hasCardOnFile` badge [S: T16] |
| 05 | payment succeeded/refunded/dispute events | `lifetimeRevenueCents`, `balanceDueCents`; dispute → profile flag |
| 06 | `subscription(s).subscription.activated` / `.cancelled` / `.past_due` / `.paused` (alias pairs `subscription.*` vs `subscriptions.*`) | subscriber badge, filter, inactive-status guard |
| 06 | gift card issued/redeemed to customer | profile Payments tab list (read via 06 API) |
| 08 | `comms.consent.changed` (STOP, unsubscribe, keyword opt-in, complaints) | profile consent badges, filters (**C1**) |
| 08 | `comms.message.failed` (hard bounce / carrier unreachable) | `ContactPoint.deliveryStatus` |
| 08 | `comms.message.sent/delivered`, `comms.conversation.message_received` | `lastContactedAt`; LeadActivity `sms_*`/`email_*` on open leads of that customer/endpoint |
| 08 | `review.review.received` | `avgRatingGiven`, ratings count (Q3) |
| 08 | `marketing.postcard.scanned` {campaignId, recipientToken, address} | W17: log activity on recipient's lead/customer; create address-only lead with source `postcard_qr` if none |
| 08 | `customer.contact_point.verification_requested` (08 name) | none — 08 sends; 07 sets `verifiedAt` on `customers.contactPoint.verify` callback |
| 09 | `team.worker.deactivated` | reassign open leads; remove from `preferredWorkerIds`/`blockedWorkerIds` display (keep IDs for history) |
| 09 | role/permission changes | cache invalidation for visibility windows |

### 9.3 Per-agent contracts (summary)
- **01 Booking portal:** 01 owns identity, sessions, funnel step list, consent checkbox UI and
  disclosure rendering, resume links; 07 owns customer matching, link, profile/garage/intake
  storage, leads. 01 must send contact data only on blur/step advance after notice (§5.10) and
  must never display matched-profile data to an unverified guest (§W7). 07 returns
  `blocked: true` but 01 shows a neutral message.
- **02 Catalog:** reads `taxExempt`, asset size class/attributes for variation auto-selection;
  intake-form vs. per-service questions split (02 owns per-service questions; 01 owns intake
  definition; 07 stores intake answers) [02 §note on intake].
- **03 Scheduling:** jobs reference `customerId`, `addressId` (+ snapshot), `assetIds`
  (+ snapshot), `notifyContactPointIds`; 03 reads `blockedWorkerIds`, `preferredWorkerIds`;
  search by plate/VIN delegated to 07 [03 §search].
- **04 Job lifecycle:** quote/invoice side panel uses 07 customer picker, address book, asset
  picker with inline add [S: T04]; on completion emits asset IDs + odometer + photo IDs for
  history. Lead conversion calls 04 `CreateJob{origin:'lead_conversion', leadId}`.
- **05 Payments:** cards on file are 05 records keyed by `customerId`; 07 never stores PAN;
  merges trigger 05 to move processor customer objects (or keep a mapping table) — 05 decides.
- **06 Subscriptions/discounts:** subs keyed by `customerId` (+ `serviceAddressId`, `assetIds`);
  discounts may target `customerTypeId` [S: T09] and "new customer" via
  `customers.newCustomerCheck`; erasure cancels subs (06 transition 28).
- **08 Marketing/comms:** segments via `customers.segmentQuery` and 08's audience builder fields
  (tags, customer type, last service date, asset attributes, lead stage, abandoned step) —
  all exposed as filterable read models; consent checked at **send time**; 08 owns STOP handling.
- **09 Team:** owns RBAC; 07's keys in §7.12. Note 09 uses `customers.contact_info.view` with
  time-boxed worker visibility (job start − 24 h → job end + 72 h) — **adopt 09's window** in
  place of the 24 h-after default in §2 (C6).

### 9.4 Event-name alias table (for Agent 10 to normalize)
| Concept | 07 uses | Others use | Recommendation |
|---|---|---|---|
| customer created | `customer.customer.created` | 04/06: `customers.customer.created`; 08: same as 07 | singular domain `customer.*` |
| customer merged | `customer.customer.merged` | 03: `customer.merged` | `customer.customer.merged` |
| asset updated | `asset.asset.updated` | 02: `customer.asset.updated` | Agent 10 pick one; 07 OK with `customer.asset.*` |
| booking attempt | `booking.attempt.*` (§4) | 01: `portal.session.*` | **01's** |
| account created | `portal.account.registered` (§4) | 01: `portal.account.created`; 08: `customer.account.created` | `portal.account.created` (01) + `customer.account.linked` (07) |
| job completed | `job.job.completed` | 04: `jobs.job.completed` | singular `job.*` |
| subscription | `subscription.subscription.*` | 06: `subscriptions.subscription.*` | singular |
| consent changed | `customer.consent.changed` | 08: `comms.consent.changed` | see C1 |
| postcard scan | `portal.qr.scanned` (§4 W17) | 08: `marketing.postcard.scanned` | 08's for postcards; 01's for generic QR |

### 9.5 API keys & integrations register (07's third-party dependencies)
Per owner requirement 2026-11-13. "Platform" = our company's account/keys; "Business" = each
tenant brings its own key or buys credits through us. Storage for all secrets: a managed
secrets store / KMS-encrypted vault (stack TBD), never in the DB in plaintext, never in client
bundles; per-tenant keys encrypted with a per-tenant data key [I].

| Service (examples, vendor TBD) | Used for | Secret(s) | Owner | Storage | Rotation | Tier / cost notes |
|---|---|---|---|---|---|---|
| Geocoding + address autocomplete (e.g. Google Maps Platform, Mapbox, HERE, or self-hosted Pelias/Nominatim) | address autocomplete, lat/lng, timezone | server API key (geocode); separate browser key restricted by HTTP referrer for autocomplete | Platform (shared with 01/02/03 — single key family) | vault; browser key is public-but-restricted | 90 days server; browser key on abuse | per-request fees; cache geocodes per normalized address [I] |
| Phone parsing | E.164 normalization | none (open-source library) | — | — | library updates | free |
| Carrier / line-type lookup (e.g. Twilio Lookup) | mobile vs landline (SMS capability) | account SID + auth token (or API key pair) | Platform (shared with 08 SMS account) | vault | 90 days | per-lookup fee; run once per new number, cache 180 days; Scale+ [I] |
| Email validation (optional) | typo/MX check | API key | Platform | vault | 90 days | MX check can be done free in-house |
| VIN decode — NHTSA vPIC | VIN → YMM/specs (US/CA market vehicles) | none (public API) | Platform | — | — | free; rate-limit politely, cache by VIN pattern [I] |
| VIN decode / vehicle catalog — commercial (e.g. DataOne, ChromeData, CarAPI) | trims, factory tire sizes, colors, non-US VINs | API key | Platform (resold) | vault | 90 days | paid; higher tiers [I] |
| Plate → VIN lookup (commercial provider) | plate lookup [S: llms] | API key + account ID | Platform (resold) or Business BYO | vault | 90 days | paid per lookup; DPPA: vehicle data only; Scale+ |
| Vehicle history reports (CARFAX / CARFAX Canada partner program [S: llms]) | history in vehicle profile | partner credentials / OAuth client | Platform partnership required | vault | per partner terms | partner agreement needed (Q8); Pro tier |
| Recalls (NHTSA recalls API) | open recalls | none | Platform | — | — | free |
| Contact enrichment / skip-trace (vendor TBD) | address → contact ("magic data") | API key | **Business BYO** (default) or platform-resold credits | vault, per-tenant encrypted | business-managed; we prompt every 180 days | per-match fees; off by default; region-gated (§5.8) |
| DNC registry scrub (FTC SAN access via vendor) | before calling enriched leads | SAN + vendor API key | Business (registry access is per organization) | vault | annual SAN renewal | required only if enrichment + calling enabled [I-legal] |
| Object storage (photos, imports, exports) | files | access key / workload identity | Platform | vault / workload identity (preferred) | 90 days or keyless | shared platform service |
| Bot protection for public lead forms (e.g. Turnstile/hCaptcha) | spam control on `leads.submitForm` | site key (public) + secret key | Platform | vault (secret) | 180 days | free tiers available; shared with 01 |
| Barcode/VIN scan in team app | camera scanning | none (on-device library) or SDK license key | Platform | app config (license key is not secret-grade) | per license | prefer free on-device lib [I] |
| AI assistant/MCP access tokens to our API | customer/lead/asset commands | per-business OAuth tokens | Business | token store (hashed) | user-revocable; 90-day refresh | scopes per §8 |

### 9.6 Conflicts & decisions for Agent 10
- **C1 Consent ledger ownership.** 07 §5.9/§7.2 and 08 §5.6 both define an append-only consent
  ledger; 08's is keyed per business × endpoint (E.164/email) × purpose, 07's per contact point.
  **Recommendation:** one ledger, **owned by 08** (it handles STOP/HELP, unsubscribes, send-time
  checks), keyed by endpoint (consent belongs to the number/address, not to a customer row —
  survives merges and duplicate customers sharing a phone). 07 keeps: capture UIs (CRM add,
  import attestation, lead forms), `ContactPoint → endpoint` mapping, read projection for
  badges/filters; `customers.consent.record` becomes a thin proxy to 08's command, and
  `customer.consent.changed` is dropped in favour of `comms.consent.changed`.
- **C2 Attempt/session event names** → adopt 01's `portal.session.*`.
- **C3 Lead win timing.** 04: Won when job reaches `scheduled` / quote approved; declined quote
  → Lost. 07 proposes: Won at job creation as appointment (a booked, unscheduled job is still a
  win), and declined quote → stage "Follow-up" with an auto-task, not Lost (owners lose
  re-quotable deals otherwise). Needs one rule.
- **C4 Abandonment threshold:** 01 uses 30 min inactivity; 07 default 30 min — aligned; make it
  one tenant setting owned by 01, read by 07.
- **C5 Out-of-area capture:** 02 emits rounded point only; 01 calls `CreateLead{reason:'out_of_area'}`
  only when contact given — aligned with 07 (no lead from anonymous location).
- **C6 Worker visibility window:** adopt 09's (start − 24 h → end + 72 h).
- **C7 Intake form ownership:** 02 note says "Agent 01/07"; decision: 01 owns definition,
  07 owns answers (§0.2).

### 9.7 Tenant branding touchpoints (owner requirement 2026-11-13)
07 has no theming engine (owned by 01/Agent 10), but its customer-facing and staff surfaces must
consume the tenant theme tokens (light/dark palettes, radius, shadows, density, custom fonts,
custom icons, logos per placement, copy overrides):
- Customer-facing: account/profile, garage cards, vehicle detail, intake form, preference
  centre, lead form widget, postcard QR landing — all labels overridable ("Garage" → "My cars",
  "Assets" → "My properties") via copy keys `customers.*`, `assets.*`, `leads.form.*` [I].
- Asset kind icons and lead source icons are icon-slot tokens (tenant can upload SVGs).
- Staff CRM: list/board/profile honour the workspace theme (light/dark + custom palettes);
  stage colours and tag colours are validated for contrast against **both** light and dark
  palettes (≥ 4.5:1 text contrast) — auto-adjusted text colour on chips [I].
- Exports/PDFs (vehicle history PDF) use the tenant's document logo placement.

---

## 10. Algorithms / pseudo-code

All `[I]` (design), referenced from earlier sections by number.

### 10.1 Normalization
```text
normalizePhone(raw, ctx):
  s = trim(raw); ext = extractExtension(s)          // "x123", "ext. 123", ";ext=123"
  region = s.startsWith('+') ? null : (ctx.addressCountry ?? ctx.businessCountry)
  parsed = phoneLib.parse(s, region)
  if !parsed or !phoneLib.isPossible(parsed): return {raw, e164:null, formatStatus:'invalid', ext}
  status = phoneLib.isValid(parsed) ? 'valid' : 'unverified_format'
  return {raw, e164: phoneLib.format(parsed,'E164'), formatStatus: status, ext,
          lineTypeHint: phoneLib.getType(parsed)}   // FIXED_LINE / MOBILE / FIXED_LINE_OR_MOBILE

normalizeEmail(raw):
  s = NFKC(trim(raw)); [local, domain] = splitLast(s, '@')
  if !local or !domain or !domain.contains('.'): invalid
  domain = toASCII(lower(domain))                    // punycode
  strict = lower(local) + '@' + domain
  relaxed = domain in {gmail.com, googlemail.com}
            ? removeDots(lower(local).split('+')[0]) + '@gmail.com' : lower(local).split('+')[0] + '@' + domain
  typo = suggestDomain(domain)                       // gmial.com → gmail.com (warn only)
  return {strict, relaxed, typo}

addressKey(a): upper(stripPunct(abbr(a.line1) + '|' + (a.line2 ?? '') + '|' + a.postalCode5 + '|' + a.countryCode))
```

### 10.2 Matching & duplicate scoring
Used by live suggestions (mode `suggest`), booking/portal resolution (mode `strict`), imports
and the nightly batch.
```text
signals(candidate, input):
  +100 verified email equal (strict)            +90 verified phone equal (E.164)
  +70  unverified email equal (strict)          +60 unverified phone equal
  +35  relaxed email equal (not strict)         +40 same addressKey
  +25  same last name (Jaro-Winkler ≥ 0.92)     +15 same first name (JW ≥ 0.9 or nickname map Bob↔Robert)
  +30  same externalId(system)                  +20 same VIN on an asset (imports with vehicles)
  −40  phone shared by > 3 customers (household/office line) → treat phone as weak
  −100 pair previously dismissed ("not a duplicate")

blocking (to avoid O(n²)): candidates = union of
  index lookups on email.strict, email.relaxed, phone.e164, addressKey, (soundex(last), postal5)

thresholds:
  suggest:  score ≥ 60 show; ≥ 100 "Very likely the same customer"
  strict (booking/portal): reuse only if (verified email or phone equal) AND no conflicting
          verified value of same type on the candidate; else create new + DuplicateCandidate(score)
  import:   per matchKeys option; exact key equality only (no fuzzy auto-update)
  nightly:  score ≥ 80 → DuplicateCandidate; auto-merge only if setting on AND score ≥ 190
            (two verified identifiers equal) AND no conflicting financial/portal state
```

### 10.3 Merge
```text
merge(survivor S, losers L[], decisions):
  lock rows S ∪ L (ordered by id to avoid deadlocks)
  assert all status ∉ {merged, erased}; assert no blocking conflicts
  snapshot = serialize(S, L, children)               // for unmerge
  for each scalar field f: S.f = decisions[f] ?? S.f ?? firstNonNull(L.f)
  for child in people, addresses, assets, notes, tags, leads, intakeResponses, portalLinks:
      reparent child → S; dedupe identical (contact point equal normalized → keep oldest id,
      merge flags: verifiedAt=min, notifyOnJobs=or, isPrimary=S's)
  consent: endpoint-keyed ledger needs no move (C1); if contact-point-keyed, re-point events
  S.preferredWorkerIds ∪=; S.blockedWorkerIds ∪=; S.taxExempt = decision (required if differ)
  L.status = merged; L.mergedIntoId = S.id
  write MergeRecord(undoUntil = now + 30d); audit
  commit; emit customer.customer.merged {S, L}
  // other modules re-point asynchronously; reads of L resolve mergedIntoId (chain-compressed)
```

### 10.4 CSV header auto-mapping
```text
synonyms = {
  firstName: [first, first name, fname, given name], lastName: [last, surname, lname, family name],
  fullName: [name, customer, customer name, client, full name, contact],
  companyName: [company, business, organization, account name],
  phone: [phone, mobile, cell, telephone, tel, phone number, primary phone, mobile phone, home phone, work phone],
  email: [email, e-mail, email address, mail], line1: [address, street, address 1, address line 1, street address],
  line2: [address 2, apt, suite, unit], locality: [city, town, suburb], region: [state, province, region, county],
  postalCode: [zip, zip code, postcode, postal code], countryCode: [country],
  tags: [tags, labels, groups, segments], notes: [notes, comments, memo],
  externalId: [id, customer id, client id, external id], createdAt: [created, customer since, date added],
  vin: [vin, vehicle vin], plate: [plate, license plate, rego, registration], year: [year, vehicle year],
  make: [make, vehicle make], model: [model, vehicle model], color: [color, colour],
  smsConsent: [sms opt in, sms consent, text opt in, marketing sms], emailConsent: [email opt in, subscribed, accepts marketing]
}
score(header, field) = max over synonyms s of similarity(normalize(header), s)  // exact=1, token-set ratio
assign greedily by highest score ≥ 0.8; numbered duplicates ("Phone 2") map to extra contact points;
label inferred from header word (mobile/home/work)
sample-value sniffing overrides: column with ≥ 80 % values matching email regex → email, E.164-parsable → phone,
  17-char VIN pattern → vin
```
Row validation: required = (firstName ∨ lastName ∨ fullName ∨ companyName) ∧ (phone ∨ email ∨
address); full name split: last token = lastName, rest = firstName, honouring "Last, First".
Dates parsed with an explicit user-chosen format when ambiguous (01/02/2024) — never guessed
silently.

### 10.5 Asset matching (booking site / import / lead conversion)
```text
matchAsset(customer, hint):
  if hint.vin: return asset where vin == normalize(hint.vin) (business-wide; if other owner → transfer prompt for staff, new asset for customer flows + duplicate flag)
  if hint.plateKey: candidates = customer.assets where plateKey == hint.plateKey
  else candidates = customer.assets where year==hint.year and make≈hint.make and model≈hint.model
  if exactly 1 → reuse; if > 1 and hint.color → filter by color; still > 1 → ask (CRM) / create new (portal, with duplicate flag)
  else create
```

### 10.6 VIN check digit (North American, positions 1–17, check at 9)
```text
translit = A1 B2 C3 D4 E5 F6 G7 H8 J1 K2 L3 M4 N5 P7 R9 S2 T3 U4 V5 W6 X7 Y8 Z9, digits = themselves
weights  = [8,7,6,5,4,3,2,10,0,9,8,7,6,5,4,3,2]
valid(vin): len==17 and no I/O/Q
  sum = Σ translit(vin[i]) * weights[i]
  check = sum mod 11; expected = (check == 10) ? 'X' : str(check)
  return vin[8] == expected
// Non-NA VINs (EU/Asia) often don't use the check digit → warn only; pre-1981 VINs < 17 chars → allow with vinValid = null
```

### 10.7 Projection rebuild (stats robust to out-of-order / reopened jobs)
```text
on job event e (completed/reopened/cancelled/rescheduled) for customer c, assets A:
  if seen(e.eventId): return
  upsert JobFact(jobId, status, completedAt, totalCents, assetIds, version) if e.version > stored.version
  recompute CustomerStats(c) from JobFact rows (indexed, per customer ≤ thousands → cheap)
  for a in A ∪ previousAssetIds(job): recompute AssetStats(a)
// never increment/decrement counters in place; full recompute per affected entity keeps it correct
```

### 10.8 Idempotency
Every command: `(businessId, idempotencyKey)` → stored response for 24 h; replay returns stored
response; same key with a different payload hash → `409 IDEMPOTENCY_KEY_REUSED`. Import batches
use deterministic keys `import:{jobId}:{batchNo}`; booking resolution uses `session:{sessionId}`;
attempt ingestion keyed by `eventId`; enrichment keyed by `(leadId, provider, addressHash)` for 90 days.

### 10.9 Optimistic concurrency & field-level merge
Updates carry `expectedVersion`. On mismatch, server attempts a **field-level auto-merge**: if
the fields in the patch were not changed since `expectedVersion` (tracked via audit diff), apply
and return the new version; otherwise `409` with both values for the UI conflict toast (§3.2).

### 10.10 Offline (team app)
Device caches the day's jobs' customer cards + assets (encrypted at rest, purged at visibility
window end). Queued operations (odometer, photo, note, new asset, new lead) carry client UUIDs
and idempotency keys; on reconnect they replay in order; conflicts (asset edited by office)
resolved by field-level merge; new-asset duplicates resolved by §10.5 server-side with
"merged into existing asset" result shown to the worker.

### 10.11 Booking-session → lead upsert
```text
onSessionEvent(e):
  link = upsert BookingAttemptLink by e.sessionId
  if e.seq <= link.lastSeq and e.type != 'contact_captured': return      // stale
  link.furthestStepIndex = max(link.furthestStepIndex, e.stepIndex); link.lastEventAt = max(...)
  if e.type == 'contact_captured':
      person = normalize(e.contact)
      lead = findOpenLead(source='booking_site', endpoint ∈ person, createdAt > now-7d) ?? createLead(...)
      lead.sourceRef.attemptIds ∪= e.sessionId; lead.customerId ?= strictMatch(person)
  if lead: lead.funnel = {furthestStep, live:true, cart, slot}; activity('attempt_step')
  if e.type == 'abandoned': lead.funnel.live=false; emit lead.attempt.abandoned
  if e.type == 'booking_confirmed': win(lead, jobId, 'self_completed')
// sweeper safety net: every 5 min, live leads with lastEventAt < now − threshold → abandoned (if 01 event lost)
```

### 10.12 Search ranking
Query classification: all-digits ≥ 4 → phone-suffix + job number (03) + VIN-suffix; contains '@'
→ email prefix; 17 alnum → VIN exact; plate-like (2–8 alnum with letters+digits) → plate exact
first; else text → trigram on displayName/company/address line1 + tag names. Score = field
weight (exact phone 1.0, exact email 1.0, VIN/plate 1.0, name prefix 0.8, trigram × 0.6) ×
recency boost (lastActivityAt within 30 days ×1.2) × status (archived ×0.3). Results capped 8
per type; "See all" opens filtered list.

### 10.13 Lead auto-assignment
Rule options: none | round-robin among users with `leads.create` in an assignee pool (pointer
per pipeline, skip inactive/off-shift users per 09 availability) | by service area of lead
address (zone → pool) | to the worker who created it (field referral). Ties broken by fewest
open leads. Assignment is a separate transaction after creation so assignment failures never
lose a lead.

### 10.14 Lead score (optional, explainable) [I]
`score = 30·hasPhone + 15·hasEmail + 10·hasConsentSms + 20·reachedSlotStep + 15·reachedPaymentStep
+ 10·returningCustomer − 10·outOfArea − daysSinceLastActivity (cap 30)`, clamped 0–100; shown
with "why" chips. No ML in Phase 1.

---

## 11. Edge cases & failure modes

**Identity & matching**
1. Household shares one phone across 3 customers (parents, adult child) → phone becomes weak
   signal (§10.2); portal OTP on that phone → account links only if exactly one customer
   matches, otherwise staff review; no data leakage across customers.
2. Office landline shared by 40 business contacts → same; SMS not sent to landline lineType.
3. Recycled phone number: number previously belonged to another person who consented → new
   owner receives marketing. Mitigation: honour carrier "number reassigned" data where 08's SMS
   provider exposes it (FCC Reassigned Numbers Database in US) [I-legal]; any "wrong number"
   reply → revoke + flag contact point.
4. Guest books with someone else's email (typo) → strict mode: no auto-link to the real owner's
   profile; booking linked to a new customer; dupe candidate raised.
5. Account takeover attempt: attacker signs up with victim's phone unverified → no link
   (verified-only rule).
6. Same person is customer of two businesses on the platform → separate tenants; never matched.
7. Customer changes email; old email in portal identity → 01 must update login identity via
   verified change flow; 07 keeps old contact point archived for history.
8. Name with diacritics/apostrophes/hyphens ("Zoë O'Neil-Ávila") → NFC storage, accent-insensitive
   search and collation.
9. Single-name customers (mononyms) and businesses with no person → allowed (person optional for
   business with company-level contact points).
10. Emoji or RTL text in names/notes → stored as-is; exports UTF-8 with BOM.

**Imports**
11. Excel-mangled phone numbers (scientific notation `5.12555E+09`, leading zeros dropped for UK
    `07…`) → detect and error with fix hint; never silently "repair" to a wrong number.
12. Mixed encodings (Windows-1252 smart quotes) → detected; preview shows mojibake warning.
13. 200 k-row file → streaming parse, batched commit, progress; worker crash mid-import →
    resume from `lastCommittedBatch`.
14. Duplicate rows in file with conflicting values → first-wins per option, conflicts listed.
15. CSV injection in names (`=HYPERLINK(...)`) → stored as text; escaped on export.
16. Import triggering "new customer" drip to 5,000 people → default `suppressAutomations = true`.
17. Re-import of the same file → hash warning; with "update fill blanks" it's idempotent.
18. Import while the user edits a customer that the import updates → version conflicts handled
    per row (row result `skipped: concurrent_edit`) rather than overwriting fresh edits.
19. Undo after jobs were booked for imported customers → those customers are kept and listed.
20. Attested consent later disputed → evidence (file hash, attestation text, user, time) retained.

**Customers & addresses**
21. Geocoder down → address saved with `pending`; booking composer warns "location not
    verified; travel time estimated from postcode"; retried with backoff.
22. Geocode lands in wrong place (rural, new build) → draggable pin, `geocodeStatus = manual`.
23. Service area redrawn (02) → thousands of addresses re-resolved in background; filters show
    "recomputing" state briefly.
24. Delete customer with future jobs/subscription/balance → blocked with links to resolve.
25. Two staff edit the same customer simultaneously → field-level merge or conflict toast.
26. Customer blocked while having a portal session mid-booking → `resolveForBooking` returns
    blocked at commit; 01 shows neutral message; hold released.
27. Erasure request for a customer who has an unpaid invoice → partial: marketing data erased,
    suppression applied, financial data retained with legal basis noted; customer informed (08).
28. Merge chain (A→B, then B→C) → reads resolve to C (path compression); unmerge of A→B after
    B→C requires unmerging B→C first (blocked with explanation).
29. Merging customers with different currencies (multi-region business) → blocked [I].
30. Timezone: customer address in different TZ than business (border towns) → "last service
    date" uses business TZ for consistency in reports; customer-facing messages use customer TZ
    (08). DST: local dates never derived by adding 24 h.

**Assets**
31. Same VIN entered by two customers (car sold privately without telling the business) →
    duplicate VIN warning + transfer flow; booking site customer flow creates a new asset with
    `duplicateOf` flag rather than exposing the other owner.
32. VIN decoder returns nothing (grey import, motorcycle, trailer) → manual YMM; asset still valid.
33. Invalid check digit on a genuine non-NA VIN → warn, allow.
34. Plate lookup provider down or not on plan → graceful fallback message; never block adding.
35. Odometer rollback / cluster replacement / km↔mi unit mistakes → flagged, not hidden.
36. Huge fleet (500 vehicles) → garage paginated, search within garage, bulk import vehicles CSV.
37. Photo upload on weak 4G → resumable/chunked upload, client resize, retry queue; partial
    uploads never attached.
38. HEIC photos from iPhone → converted server-side; EXIF orientation applied, GPS stripped.
39. Asset deleted that is referenced by historical jobs → archive only; jobs keep snapshot.
40. Property asset whose address is edited → asset follows the address ID; snapshot on old jobs.

**Leads & attempts**
41. Session events arrive out of order or duplicated → seq/max logic (§10.11).
42. 01 never sends `abandoned` (crash) → sweeper marks abandoned.
43. Visitor completes booking on a different device later → matched by endpoint within 7 days →
    lead Won (`self_completed`), recovery drip cancelled.
44. Visitor abandons, returns via resume link, abandons again → same lead, attempts appended,
    funnel shows furthest step ever and latest attempt step.
45. Bot traffic floods booking site → no leads without contact capture; contact capture
    rate-limited and bot-scored by 01; leads flagged `suspected_bot` hidden by default.
46. Spam lead-form submissions with fake phones → invalid phones rejected; honeypot; duplicate
    throttling per IP/endpoint.
47. Lead owner deactivated → reassignment (§9.2).
48. Lead converted while another user is dragging it to Lost → version conflict; Won wins (job
    exists) and the drag is reverted with toast.
49. Conversion saga partial failure (job created, lead update failed) → reconciler listens to
    `jobs.job.created{leadId}` and completes; never two jobs (idempotency key `lead:{id}:convert`).
50. Pipeline stage deleted with leads in it → must choose destination stage.
51. Enrichment returns data for the wrong household (stale broker data) → per-field confidence,
    user must select; "Report wrong data" discards & logs.
52. Enrichment budget exhausted mid-batch → stop, partial results kept, owner notified.
53. Postcard QR scanned by a neighbour (postcard passed on) → token maps to the mailed address,
    but the scanner's typed details create/attach correctly; we never assume scanner = resident
    for consent purposes.
54. Out-of-area lead later becomes in-area (zone expanded) → saved filter "out-of-area leads now
    covered" (02 re-resolve) — great re-engagement list [I].

**Platform**
55. Export of 100 k customers → async, chunked; link expiry; audit; rate-limited.
56. Search under heavy load → degrade to prefix-only, never block page.
57. Event consumer lag (stats stale) → profile shows "updated x min ago" when projection lag
    > 60 s [I].
58. Permission change mid-session (worker loses access) → next request 403; cached data purged
    on device at next sync.
59. AI assistant mass-update ("tag all customers VIP") → bulk ops from AI require human
    confirmation step with count preview [I].

---

## 12. Better than Fieldd

Concrete improvements (Fieldd baseline mostly unknown in detail — compare during video review):
1. **Import dry-run + per-row error report + undo** — Fieldd advertises "one click" CSV [S: T18];
   we keep one-click auto-mapping but add a safe preview, downloadable failures and 7-day undo.
2. **Inline duplicate prevention while typing** (phone/email/address) instead of post-hoc cleanup;
   plus a duplicates queue with reversible merges (30-day unmerge).
3. **Consent as an auditable ledger** with evidence, region presets and send-time checks — turns
   a legal liability (TCPA) into a selling point for small businesses.
4. **Lawful lead capture by default:** first-party, notice-at-collection abandoned capture and
   per-recipient postcard QR instead of opaque data-broker "magic"; enrichment optional, gated,
   with cost visible before each call.
5. **Explainable lead score + funnel analytics** with recovery rate per booking source.
6. **Universal search that understands phone fragments, VIN/plate, emails** — type "0101" or the
   last 6 of a VIN and get the customer in < 300 ms.
7. **Vehicle-first garage:** free NHTSA VIN decode + recall check (no paid provider needed for the
   basics), camera VIN scan in the team app, colour swatches, staggered tire sizes, odometer trend
   with rollback detection, pre-existing damage log (detailing disputes).
8. **Per-asset service timeline shareable as a branded PDF** for the customer (resale value).
9. **Keyboard-first CRM:** `/` search, `n c` new customer, `n l` new lead, `j/k` navigate list,
   `e` edit, `m` merge [I].
10. **Offline team app** for customer cards/assets on job day with queued writes.
11. **Realtime lead board** (no refresh) with "Live" visitors and optimistic drag-and-drop.
12. **Accessibility:** WCAG 2.2 AA — board drag-and-drop has keyboard alternative (stage menu),
    screen-reader announcements on moves, colour never the only signal (tags/stages have text).
13. **Privacy tooling built-in:** one-click data export/erasure with receipts; retention policies.
14. **Responsive by design** (owner requirement): list → cards, board → stage list, profile tabs
    → segmented control; tablet gets two-pane (list + profile) layout [I].
15. **Native apps later** (owner requirement): API surface (§8) already covers the "staff on the
    phone builds a booking for the caller" flow — customer quick-create + vehicle lookup +
    convert in < 60 s; same commands power desktop/iPhone apps.
16. **Performance budgets:** customer list first page p95 < 400 ms server for 100 k customers;
    profile open < 500 ms; board with 500 cards virtualized; search p95 < 150 ms server [I].

### 12.1 Budget tier notes [I] (aligns with "affordable for starters")
| Capability | Starter (cheapest) | Mid | Top |
|---|---|---|---|
| Customers, people, multiple phones/emails/addresses, tags, notes, custom fields | ✓ (≤ 10 custom fields) | ✓ (50) | ✓ |
| CSV import/export, dedupe suggestions, merge/unmerge | ✓ | ✓ | ✓ + scheduled exports |
| Business customers, multi-site | basic | ✓ | ✓ |
| Consent ledger, preference centre | ✓ (non-optional — compliance) | ✓ | ✓ |
| Assets: vehicles via YMM + NHTSA VIN decode, photos (10/asset), recalls | ✓ | ✓ (50 photos) | ✓ |
| Camera VIN scan (team app) | ✓ | ✓ | ✓ |
| Plate → VIN lookup, commercial trims/tire data | — | ✓ (metered) | ✓ |
| Vehicle history reports (CARFAX-like) | — | — | ✓ (metered, partner) |
| Lead board: 1 pipeline, board + list layouts, manual/form/QR/abandoned leads | ✓ | multiple pipelines, funnel, map, calendar | + custom scoring weights |
| Abandoned-booking capture | ✓ | ✓ | ✓ |
| Enrichment connector | — | BYO key | BYO or resold credits |
| Line-type lookup | — | ✓ | ✓ |
| Duplicate auto-merge rules | — | ✓ | ✓ |
| Audit retention | 2 y | 3 y | 7 y |
Fieldd's own plans: Start $199/mo includes "sales pipelines"; Pro adds heatmaps etc. [S: llms].
Starter must include the pipeline and abandoned capture because they directly earn revenue.

---

## 13. Open questions

1. **Q1 Fieldd's customer model shape:** is a "contact" a household/company with nested people
   (our model) or one person with extra phones? [S: llms wording ambiguous]
2. **Q2 Account status values** shown on Fieldd profiles [S: T09] — active/inactive/blocked? Is
   "account status" about the portal account (registered/not) instead?
3. **Q3 "Ratings" on the profile** [S: T09]: ratings the customer gave (reviews), ratings workers
   give customers, or both? Same field is shown for workers.
4. **Q4 Magic data mechanics:** what does Fieldd actually do for address-only leads and booking
   drop-offs (vendor, cost to business, regions)? Owner's appetite for enrichment at all given
   §5.8 risks — recommend launch without it.
5. **Q5 Lead creation threshold:** does Fieldd show anonymous booking-page visitors as leads, or
   only once contact details exist? [S: T10 "visited your online booking page but didn't
   complete" suggests visitors appear]
6. **Q6 Vehicle-size pricing:** do prices vary by vehicle size class in Fieldd's auto templates,
   and is it driven by the selected asset? (02 dependency)
7. **Q7 Plate lookup provider & regions**, and whether owner wants it at launch.
8. **Q8 CARFAX partnership** feasibility for a new platform; alternatives (NMVTIS-based reports).
9. **Q9 Lead board stages:** fixed or user-defined in Fieldd? Which "new layouts" exist [S: llms]?
10. **Q10 Customer tax & discount fields on profile** [S: D18] — per-customer tax rate override
    or only exempt flag? Default discount auto-applied to every booking?
11. **Q11 Customer types** referenced by discounts [S: T09] — fixed list (residential/commercial)
    or custom?
12. **Q12 Who receives job notifications** for business customers with multiple people
    ("contact info to notify" [S: T04]) — per job or per customer default?
13. **Q13 Consent ownership** (C1) and **lead-won timing** (C3) — Agent 10 to decide.
14. **Q14 Intake form vs booking questions** storage when the same question appears in both.
15. **Q15 Data retention defaults** the owner is comfortable with (attempts 30 d, enrichment 90 d).
16. **Q16 Do workers see customer phone numbers** or only a masked relay? (09 says masked outside
    window.)

---

## 14. Verify against video

Owner's walkthrough (https://youtu.be/vyELvdWCdnc, customer side) plus any CRM footage. Check:

**Customer side (booking site / account)**
- [ ] Where account creation happens (before/after service selection; optional or forced) and
      what identifiers it asks for (email, phone, password vs. code).
- [ ] Whether a returning customer is recognised by phone/email and what is pre-filled.
- [ ] Asset step: label used (vehicle/home/equipment), fields asked (YMM? VIN? plate? colour?
      photo?), whether saved vehicles appear as selectable cards ("garage").
- [ ] Intake form placement and whether answers are editable later from the account area.
- [ ] Marketing consent checkbox presence, default state, exact disclosure wording location.
- [ ] Exact step at which phone/email is requested (determines abandoned-capture point).
- [ ] Any "finish your booking" message received after abandoning (timing, channel, link
      restores cart?).
- [ ] Account area sections: profile, addresses, vehicles, history/rebook, gift cards,
      subscriptions, payment methods, communication preferences.
- [ ] Postcard / QR landing behaviour (if shown): pre-filled address? personalised greeting?
- [ ] Branding applied to these screens (colours, fonts, logo, dark mode) — relevant to §9.7.

**CRM side (if captured)**
- [ ] People tab: sub-tabs, default columns, filter list, search behaviour, export icon position.
- [ ] Customer profile: header fields, tabs, where service history, ratings, notes, account
      status appear [S: T09]; tax/discount/notes fields [S: D18].
- [ ] Multiple phones/people/addresses UI and business-customer fields [S: llms].
- [ ] New customer: "Single" vs "CSV" choice [S: T18]; required fields; tag picker; "Next" button.
- [ ] CSV import screens: mapping UI, duplicate handling, error report.
- [ ] Vehicles tab: columns, "last service date" filter control, lookup by YMM/VIN/plate, photos,
      colours, tire sizes, CARFAX entry point [S: llms].
- [ ] Lead board: columns/stages, card fields, available layouts and sort options [S: llms],
      lead detail log [S: T10], funnel step display for booking attempts [S: T10].
- [ ] New lead: from scratch / existing customer / address-only paths [S: T20]; magic data button
      and what it returns (and any cost prompt).
- [ ] Convert lead → booking flow without leaving the dashboard [S: T20]: which form opens and
      what is pre-filled.
- [ ] Empty states, loading states, and error messages on all of the above.
- [ ] Mobile/tablet rendering of the People tab, profile and lead board.
