# 03 — Scheduling & Dispatch Engine

> Agent 03 · Phase 1 "CRM brain" · written 2026-11-12 · status: DRAFT v1 (pre-video)
> Owns: **when** and **by whom** work happens — availability math, the Jobs tab & calendar,
> booking a job from the CRM, auto-dispatch, the recurring engine, route optimization, travel time.

### Source legend

| Tag | Meaning |
|---|---|
| `[S: 02]` | `research/transcripts/02_rMVRwp-anZM.txt` (Route Optimization) |
| `[S: 08]` | `research/transcripts/08_w3D1W6oOaNY.txt` (Jobs & Appointments / scheduling tour) |
| `[S: 14]` | `research/transcripts/14_5zqqoRbV1DU.txt` (Recurring Job) |
| `[S: 15]` | `research/transcripts/15_Dh1Ib3a4psU.txt` (Book a Job) |
| `[S: 19]` | `research/transcripts/19_ZPf7JSlvIcw.txt` (Schedule a Job) |
| `[S: NN]` | any other `research/transcripts/NN_*.txt` (01 Subscriptions, 03 Timesheets, 04 Quotes & Invoices, 05 Booking Page, 06 Services, 07 Add-ons, 11 Service Areas, 13 Add Team Member, 16 Payment Methods …) |
| `[S: NN.desc]` | the matching `.description` file (chapter list) |
| `[S: llms]` | `research/site/llms.txt` (Fieldd official facts, 2026-10-23) |
| `[I]` | Inferred — industry convention or our design decision. **Not** a Fieldd fact. |

Anything visual (layout, column order, button placement) is `[I]` until the owner's walkthrough
video is reviewed — see §14.

### Ownership boundaries (read first)

| Concern | Owner | What 03 does with it |
|---|---|---|
| Job aggregate, job ID, statuses (quote→appointment→invoice), worker-app job workflow | **04** | 03 owns the **JobSchedule** sub-aggregate (time window, assignments, schedule blocks, route membership) keyed by `jobId`. 03 never changes job *status*; 04 never changes job *time/worker*. |
| Services, add-ons, durations, buffers-per-service, zones, shops, travel fees | **02** | 03 reads durations/zones/shops as snapshots at booking time. |
| Workers, working hours, time off, service/zone assignment, permissions, calendar sync | **09** | 03 reads availability and eligibility; 03 owns the *busy ledger* (what is booked). |
| Customer journey on booking site, holds UI, session | **01** | 01 calls 03's availability/hold/commit APIs. |
| Subscriptions with automatic scheduling | **06** | 06 asks 03 to create/pause/end a recurring series. |
| Notification templates, reminders, delivery | **08** | 03 emits schedule events; 08 decides who is told what. |
| Payment at booking (charge now / card on file) | **05** | 03's CRM booking flow calls 05; 03 holds slot during payment. |
| Customer profile, address book, assets/vehicles | **07** | 03 reads address geo + assets for display. |

---

## 1. Summary

The Scheduling & Dispatch Engine decides **when a job can happen and who does it**. Given the
services on a job (whose durations and add-on durations come from the catalog), the job's
location (mobile address or shop), the team's working hours, existing jobs, travel time between
mobile jobs, service-area membership and allowed services, it computes bookable time slots and
picks "the first available time for the first available worker" `[S: 15]`, guaranteeing **no
double bookings** `[S: 19]` even when the booking site and the CRM book the same minute. It owns
the Jobs tab and calendar (day/week/month/list/map views, drag-to-reschedule with automatic
notifications, filters, recurring/quotes/invoices sub-tabs, search) `[S: 08]`, the CRM "New →
Appointment" flow `[S: 15][S: 19]`, auto-dispatch to the best/closest worker `[S: 19][S: llms]`,
job splitting across team members and past scheduling `[S: llms]`, the recurring engine
(daily/weekly/monthly, end never/date/count, fixed or % discount) `[S: 14]`, and route
optimization for day-booked "route services" that are re-ordered when the worker swipes
"start route" `[S: 02]`.

## 2. Roles

| Role | What they do here | Source |
|---|---|---|
| **Owner / Admin** | Everything: book, reschedule, drag/drop, override availability/zones, schedule in the past, configure scheduling settings, dispatch strategy, holidays, route settings; track route progress. | `[S: 08][S: 15][S: 02][S: 11]` (override outside zone from CRM) |
| **Manager / Dispatcher** | Book & reschedule, assign/reassign, view all calendars, map "who is free". Override rights per RBAC (09). | `[I]` role granularity owned by 09 |
| **Foreman** | View crew's schedule, possibly reassign within crew; edit own timesheets. | `[S: 03]` (foreman edits timesheets) / scheduling rights `[I]` |
| **Worker** | Sees own jobs/route in worker app, sets own availability, syncs Google Calendar, taps "start route", proceeds through jobs. | `[S: 13][S: 02]` |
| **Customer** | Books via booking site (through Agent 01) — sees slots (or day-only for routes, e.g. "Anytime Thursday"); receives confirmations/reschedule notices. | `[S: 02][S: 05][S: 15]` |
| **System** | Auto-dispatch, recurring materialization, hold expiry, route optimization, auto-close, conflict detection, running-late detection. | `[S: 14][S: 02]`, others `[I]` |
| **AI agents (MCP / Darwin)** | Book/reschedule via the same commands with the same guards (CRM MCP = business scope; Bookings MCP = customer scope). | `[S: llms]` |

---

## 3. Screens

Visual layout is `[I]` unless tagged. Our UI must not copy Fieldd pixels/colors (BRIEF rule 1).

### 3.1 Jobs tab — list (`Jobs` in main nav)

**Purpose** `[S: 08]`: "every upcoming appointment along with key information like job time,
address, service, payment status, and assigned workers"; drag to reschedule; filters; recurring
jobs, open quotes, invoices with status.

**Layout regions** `[I]`
1. Header: title, date-range picker (default: today → +30 days), view switcher
   (`List · Day · Week · Month · Map`) `[S: 08]`, `New` button (opens §3.6), saved-view menu.
2. Sub-tab strip: `Upcoming` · `Unscheduled/Needs attention` · `Recurring` `[S: 08][S: 14]` ·
   `Quotes` (open quotes) `[S: 08]` · `Invoices` (with statuses) `[S: 08]` · `Past`.
3. Filter bar `[S: 08]`: Status · Tags · Service areas · Staff (assigned workers) · Customer.
   Plus `[I]`: Payment status · Service · Source (CRM/online/recurring/subscription/route) ·
   Unassigned only · Has conflict.
4. Table (virtualized).
5. Bulk-action bar (appears on selection) `[I]`: Reassign · Reschedule (shift by N days) ·
   Add tag · Export CSV · Notify.

**Columns (default order)** — first five sourced `[S: 08]`, rest `[I]`:

| # | Column | Type / render | Sort | Notes |
|---|---|---|---|---|
| 1 | Job time | local date + start–end in **job location TZ**; badge if TZ ≠ viewer TZ | yes (default asc) | Route jobs show "Anytime Thu 5 Mar" `[S: 02]` |
| 2 | Address | line1 + city; shop name if shop job | yes | click → map view centered |
| 3 | Service | primary service + "+N add-ons" | yes | |
| 4 | Payment status | pill: Unpaid / Card on file / Deposit / Paid / Partially paid / Refunded (owned by 05) | yes | |
| 5 | Assigned workers | avatar stack in worker calendar colors `[S: 13]`; "Unassigned" red pill | yes | split jobs show all workers |
| 6 | Job # | single job ID across quote/appointment/invoice `[S: 04]` | yes | |
| 7 | Customer | name, tap → profile | yes | |
| 8 | Status | job status (owned by 04) | yes | |
| 9 | Tags | chips | no | |
| 10 | Vehicle/asset | e.g. "2021 Tesla Model 3 · white" `[S: llms]` "vehicle on every job" | no | see Q-3 (§13) |
| 11 | Total | money (integer cents → formatted) | yes | |
| 12 | Conflict | ⚠ icon with reason tooltip | yes | `[I]` |

**Actions**: row click → job drawer (§3.4); drag row onto a calendar day (in split list+calendar
mode) `[I]`; kebab: Reschedule, Reassign, Duplicate, Skip occurrence (recurring), Cancel (04).

**States**
- Empty (no jobs in range): illustration-free text "No jobs between {from} and {to}" + `New appointment` CTA + "Show unscheduled jobs" link `[I]`.
- Empty due to filters: "No jobs match these filters" + `Clear filters`.
- Loading: skeleton rows (fixed row height → no layout shift).
- Error: inline banner "Couldn't load jobs — Retry"; keep last successful data visible, greyed.
- Stale/realtime: when another user changes a visible job, row updates in place with a 2-s highlight.

**Mobile**: table collapses into cards (time · customer · service · worker avatars · payment pill);
filters move into a bottom sheet; swipe-left on card → Reschedule / Reassign.

### 3.2 Calendar — Day / Week / Month views

**Sourced** `[S: 08]`: daily, weekly, monthly and list layouts; drag a job "to a new time or staff
member", schedule updates and "notify the necessary people automatically".
**New schedule (2026)** `[S: llms]`: "travel time for mobile jobs on the calendar, the vehicle on
every job, and a map that shows who is free".

**Day view (resource view)** `[I]`
- Columns = workers (ordered: by calendar order setting, then name); optional `Unassigned` column
  first; optional shop-bay columns when a shop filter is active.
- Rows = time axis in the **calendar display TZ** (default business TZ; switchable to viewer TZ).
  Grid granularity 15 min; zoom 15/30/60.
- Shaded = outside worker availability; hatched = time off / external busy (Google sync) `[S: 13]`.
- Job card: color = worker calendar color `[S: 13]` (or status color toggle); content: time,
  customer, service, vehicle/asset line `[S: llms]`, payment pill, tags, icons (recurring ↻,
  route ⤳, split ⫽, conflict ⚠, notes 🗒 — use icon set, not emoji, in product).
- **Travel blocks** `[S: llms]`: a thin striped block between consecutive mobile jobs showing
  "18 min drive" (estimate source + confidence on hover). Red if travel exceeds gap (= conflict).
- **Route containers** `[S: 02]`: an all-day bar at the top of the worker column: "Route · 7 stops ·
  43% complete"; click → Route overview (§3.10).
- "Now" line in display TZ; DST days show a 23 h / 25 h axis with the repeated/missing hour labeled.

**Week view**: columns = days; within a day either stacked by worker (lanes) or merged with worker
color stripes (toggle) `[I]`. **Month view**: per-day count chips + revenue total + capacity bar
(booked minutes / available minutes) `[I]`; click day → Day view.

**Drag-and-drop** (detailed flow §4.5)
- Drag vertically = new time; horizontally = new worker `[S: 08]`. Drop zones validate live:
  green = feasible, amber = feasible with warning (outside working hours, tight travel), red =
  hard conflict (double booking) — red drop requires override permission.
- Resize bottom edge = change duration (writes a duration override on the job, not on catalog).
- Keyboard alternative (accessibility): select card → `M` opens "Move to…" dialog (date, time,
  worker with live feasibility list).

**States**: loading skeleton per column; empty day "No jobs — {n} workers available" with "Book
first available" shortcut; error banner as §3.1.

**Mobile (CRM web on phone)**: Day view becomes single-worker agenda with worker picker chips;
long-press to drag; Week/Month as agenda list.

### 3.3 Map view & "Who is free"

`[S: 08]` map view "to visualize where your team is heading"; `[S: llms]` "a map that shows who is
free so jobs can be dispatched fast".

Layout `[I]`:
- Map (left/top 70%): job pins (numbered in sequence per worker, colored by worker); polyline of
  each worker's day in sequence; zone polygons toggle `[S: 11]`; shop markers.
- Worker list panel (right/bottom 30%): each worker → state chip **Free now · On job · Driving ·
  Off · On break**, last-known location age ("2 min ago"), current job, **next free window**
  ("free 13:40–15:00, 6 km from Unassigned job #1042").
- Unassigned jobs tray: drag a job onto a worker (pin or list row) → assignment flow with travel
  delta preview ("+12 min driving, arrives 14:05").
- Filters: date (default today), zone, staff, status.

Data: worker positions come from on-my-way / live-tracking pings (owned by 04/09) `[S: 11]`
("live tracking"); if no ping in 30 min, fall back to "last job location" and grey the marker.

States: no location permission → markers at last job address with "estimated" badge; no jobs →
show workers' start locations.

### 3.4 Job drawer — Schedule panel

Opens from list/calendar/search. 03 owns the **Schedule** section; other sections (lines, payment,
checklists, photos) belong to 02/04/05.

Fields:
| Field | Type | Validation | Default |
|---|---|---|---|
| Date | date (job location TZ) | required unless `unscheduled` | — |
| Start time | time | required for timed jobs; hidden for route jobs | — |
| Duration | minutes (computed, overridable) | 5–1440; multiple of 5 | Σ service + add-on durations `[S: 06][S: 07]` |
| End time | computed | read-only unless duration overridden | — |
| Assignment mode | `single` / `crew` / `split by service` `[S: llms]` | — | `single` |
| Worker(s) | multi-select with feasibility badge per worker | eligibility rules §5.3 (warn, override with permission) | auto-dispatch result |
| Route | read-only link for route services | — | — |
| Recurring | link to series + "this occurrence is modified" badge | — | — |
| Notify customer / Notify workers | toggles shown when time/worker changes | — | on / on `[S: 08]` |

Actions: `Find next available` (re-runs search from now), `Reassign`, `Unschedule` (keeps job,
removes time), `Skip occurrence`, `Move to route day`.

### 3.5 Reschedule confirm & conflict panel

Shown on drop/save when change is material (time or worker). `[I]` (the notification itself is
`[S: 08]`).

```
Move Job #1042 — Deluxe Detail (2h 30m)
  From: Thu 5 Mar 09:00–11:30 · Alex
  To:   Thu 5 Mar 13:00–15:30 · Sam        Travel: +14 min from #1039 ✓
  [x] Notify customer (SMS + email)  [x] Notify Sam  [x] Notify Alex (removed)
  Optional message to customer: [__________]
  [Cancel]                               [Move job]
```
Conflict variant lists each violation (type, severity, detail) with actions: `Pick suggested
time` (top 3 alternatives), `Override & move` (permission `scheduling.override`), `Cancel`.

### 3.6 New → Appointment (CRM booking form)

Entry: global `New` menu → `Appointment` `[S: 15]`; `New` → `Recurring job` opens the same form
with Recurring pre-enabled `[S: 14]`; also from customer profile, lead board "convert" `[S: 20]`,
calendar empty-slot click (pre-fills date/time/worker) `[I]`.

**Two-column layout** — sourced `[S: 19]`: "on the left you'll put in the service … on the right
attach an existing customer or create a new customer profile". Field order inside columns
follows the narration order of `[S: 15]` / `[S: 19]` / `[S: 16]` / `[S: 14]`:

**Left column — What**
1. **Service** — searchable dropdown of published services; "type in a new one and save it for
   next time" `[S: 19]` / "create a new one" `[S: 15]` → inline mini-form (§3.6.1).
2. **Add-ons / extra services** — `+` button "choose from the available options" `[S: 15]`;
   lists customer add-ons and company add-ons (company add-ons only in CRM/worker app `[S: 07]`).
   Quantity stepper where product/quantity-based `[S: 07]`.
3. **Line cost** — "attach the cost of each service to track profits" `[S: 19]` (prefilled from
   catalog cost `[S: 06]`).
4. **Options & payment** section `[S: 16]`:
   - Options: attach/create **discount**, apply **tax**, add **gift card** `[S: 16][S: 19]`.
   - Payment: **charge after job is complete** or **add a card on file** → then **charge at
     booking** or **after job** `[S: 16]`. Deposits / pre-authorization if enabled `[S: 04]`.
     (All payment UI and rules owned by 05; 03 only sequences it in Book now.)

**Right column — Who / Where / When**
5. **Customer** — search existing (name/phone/email/plate) or "enter the new one's details
   below" `[S: 15]` (fields owned by 07: first, last, phone, email).
6. **Job address** — select from customer's addresses or enter new (autocomplete + geocode);
   for shop services → shop selector `[S: 11]`. Zone badge shows matched service area, or warning
   "Outside service areas — booking allowed from CRM" `[S: 11]`.
7. **Scheduling** — "Field will automatically show you the first available time for the first
   available worker. You can pick one of those options or manually enter a time" `[S: 15]`;
   "pick a date and time or let field find the next open time slot and available worker"
   `[S: 19]`:
   - Suggested times: top 3–5 chips `Today 14:00 · Sam (6 km)`, `Tomorrow 09:00 · Alex`, … with
     "More times" → full availability grid (date strip × time chips, worker filter).
   - Manual: date + time + worker picker (each worker shows feasibility badge ✓ / ⚠ reason).
   - Assignment mode: single / crew (N workers) / split services `[S: llms]`.
   - Route services: date only ("Anytime Thursday") `[S: 02]`; worker = route owner.
   - Past date allowed (with permission) `[S: llms]` → banner "Scheduling in the past —
     notifications off by default".
8. **Recurring** toggle — "set recurring frequency to on" `[S: 14]` → frequency (daily / weekly /
   monthly) + interval `[I]`, end (never / on date / after N jobs), recurring discount (fixed $ or
   %) `[S: 14]`; preview of next 6 dates with conflict markers `[I]`.
9. **Additional details** — customer **assets**, **notes**, **job tags** `[S: 19]`, **files**
   `[S: 14]`.
10. **Notify** toggles `[I]`: confirmation to customer (default on), notify assigned workers
    (default on) — defaults per `[S: 15]` side effects.
11. **Book now** primary button `[S: 15][S: 14]`. Secondary: `Save as quote` (04) `[I]`.

**Sticky summary** (right rail / bottom on mobile) `[I]`: subtotal, discounts, tax, total
(integer cents, owned by 05/06), duration, worker, time.

#### 3.6.1 Inline service creation
Fields: name (required, 1–120 chars), price (≥ 0, cents), **duration (required, minutes, 5–1440,
default 60)** `[I]`, category (default "General"/last used), mobile/shop flag, cost (optional).
Choice: `Save to catalog` (creates published service via 02 — "save it for next time" `[S: 19]`)
or `Use once` (one-off line item, not in catalog). Duration is required because availability
cannot be computed without it `[S: 06]`.

**Validation summary (Book now disabled until):** ≥1 line with duration > 0; customer selected
or valid new-customer fields; address geocoded (or shop chosen) — CRM may book un-geocoded with
warning (travel not computed); a time chosen **or** "Leave unscheduled" chosen; if recurring on:
valid frequency + end; if charge-now: payment method valid (05).

**States**: suggestions loading → skeleton chips (form remains usable); "No availability in next
60 days for these services" → offer manual entry + "Show workers who could do it with override";
submit in flight → button spinner, form locked, idempotency key reused on retry; success → toast
"Job #1042 booked for Thu 5 Mar 09:00 with Sam" + `View` / `Book another`; failure (slot taken) →
inline "That time was just taken" + refreshed suggestions, all other inputs kept.

**Mobile**: single column in order 1→11; suggested times as horizontal chips.

### 3.7 Recurring tab (Jobs → Recurring)

`[S: 14]`: "find the booking under the recurring tab in the jobs window … changes … committed
across all future bookings". `[S: 08]` "manage your recurring jobs".

Columns `[I]`: Customer · Services · Frequency ("Every 2 weeks on Tue") · Next date · Worker
(series default) · Ends ("Never" / "on 1 Jun 2027" / "after 12 jobs, 5 left") · Discount · Status
(Active/Paused/Ended) · Source (CRM / online / subscription `[S: 01]`).
Row → Series detail: rule editor, occurrences list (past ✓, upcoming, skipped, modified),
exceptions, `Pause`, `Resume`, `End series`, `Skip next`, edit with scope dialog (§4.8).

### 3.8 Quotes & Invoices sub-tabs

`[S: 08]` "track open quotes, and view all invoices, each clearly marked with their status".
Documents and statuses are owned by **04**; 03 only provides the list chrome inside the Jobs
window (shared filter bar, search, job-ID linking) and a "Book" shortcut from an accepted quote
(quote's book-online button lets customers self-schedule `[S: 04]` → through Agent 01's flow).

### 3.9 Global search (sidebar)

`[S: 08]`: "search tool in the sidebar … job number, invoice, staff, or customer". `[S: 15]`
"use the search function to find it quickly". 03 contributes the **jobs** result provider:
match on job # (exact/prefix), customer name/phone, address, plate/VIN (via 07), invoice # (04).
Result row: job #, date/time, customer, worker, status; `Enter` opens job drawer. Target: results
in < 150 ms p95 for tenants ≤ 100k jobs `[I]`. Cross-module search UX owned by Agent 10/shell.

### 3.10 Route overview (admin)

`[S: 02]`: "open the route from the calendar. Inside the route overview, you'll see completed jobs
and remaining jobs clearly listed. You can click on any job to view its details. You'll also see a
completion percentage at a glance."

Layout `[I]`: header (worker, date, status: Planned / In progress / Completed / Closed-incomplete,
**completion %** ring, started at, est. finish); map with ordered stops; two lists **Remaining**
(in optimized order, with ETA) and **Completed** (with actual times); actions: `Add stop`,
`Remove stop`, `Re-optimize` (admin), `Lock order`, `Move stop to another worker/day`.

### 3.11 Worker app — Route (UI shared with 04)

`[S: 02]`: worker "opens the day's route and taps start route" (swipe) → route optimized →
"guides the worker to the first stop" → each job uses normal workflow (before/after photos,
checklists, message client) → "Once the final job is completed, the route closes automatically".
03 owns: route list for the day, **Swipe to start route** control, optimized sequence, "Next stop"
card with navigation deep link, mid-route re-optimize prompt when stops added/removed. Job steps
UI owned by 04.

States: route with 0 stops → "No stops today"; offline at start → optimize locally with straight-
line estimates and re-sync (§10.6); provider failure → fallback order (§11).

### 3.12 Scheduling settings (Settings → Scheduling) `[I]` unless noted

| Setting | Type | Default | Notes |
|---|---|---|---|
| Business time zone | IANA tz | from signup city `[S: 11]` (default zone from city) | |
| Slot granularity | 5/10/15/20/30/60 min | 30 (online), 15 (CRM) | |
| Online lead time (min notice) | minutes | 120 | CRM ignores |
| Same-day cutoff | local time or off | off | |
| Booking horizon (max advance) | days | 60 | CRM ignores (max 730) |
| Default buffer between jobs | minutes | 0 | added per worker after each job |
| Travel-time mode | off / estimate / road network / traffic-aware | road network | cost tiering §10.5 |
| Count travel from home/start for first job | bool | false | |
| Dispatch strategy | Earliest / Closest / Balanced / Preferred-first | Earliest then Closest | `[S: 15][S: 19]` |
| Allow customers to choose worker | bool | false | 01 |
| Slot display | exact times / arrival windows (e.g. 2 h) | exact `[S: 06]` ("without relying on arrival windows") | |
| Compact scheduling (only offer slots adjacent to existing jobs when day already has jobs) | bool | false | improvement |
| Hold TTL (online) | minutes | 10 | |
| Allow scheduling in the past | role permission (09) | owners/admins | `[S: llms]` |
| Reschedule notifications default | customer / workers toggles | on/on | `[S: 08]` |
| Holidays calendar | list + country preset | none | §5.7 |
| Recurring horizon | days | 90 | §5.6 |
| Route: max stops per worker-day | int | 12 | |
| Route: end location | return to start / end anywhere | end anywhere | |
| Route: auto-close grace | minutes after availability end | 120 | `[S: 02]` auto-close |
| Routes text (customer-facing wording "Anytime Thursday") | text | — | lives in Settings → Customer hub → Style → Text → Routes text `[S: 02]`; owned by 01 |
| Route optimization toolkit toggle | bool | off | "activated in your toolkit" `[S: 02]` |

---

## 4. Workflows

### 4.1 Customer books online (timed service) — via Agent 01
1. 01 calls `scheduling.availability.search` with lines, location, date range, channel=`online`.
2. 03 returns days → slots (no worker identity unless "choose worker" enabled).
3. Customer picks slot → 01 calls `scheduling.hold.create` (idempotency key = booking session +
   slot) → 03 runs the commit-time feasibility check (§10.3) **and** inserts a `hold` ScheduleBlock
   for the chosen worker(s) (exclusive). TTL 10 min. Emits `scheduling.hold.created`.
4. Customer completes details/payment (01/05). If payment step needs > TTL (3-D Secure), 01 calls
   `scheduling.hold.extend` (max total 20 min).
5. 01 calls `scheduling.booking.commit(holdId, jobDraft, idempotencyKey)` → in one transaction:
   job created via 04 (`jobs.createFromBooking`), hold block converted to `job` block,
   assignments written, `scheduling.job.scheduled` + `scheduling.assignment.created` emitted via
   outbox. 08 sends confirmation + schedules reminders.
6. If hold expired but slot still feasible → commit re-acquires transparently. If not feasible →
   `409 SLOT_TAKEN` with 3 nearest alternatives; 01 shows them.

### 4.2 Customer books a route service online `[S: 02]`
1. Availability returns **days** ("Anytime Thursday" text from 01 settings) where at least one
   eligible worker has a route container with capacity (stops & minutes) — §10.7.
2. Hold = **capacity hold** on a specific worker's route-day (counts 1 stop + duration minutes).
3. Commit → job gets `scheduleKind = 'route_day'`, `routeId`, no start time; appended to the
   route's unordered stop set. Order is decided at "start route" `[S: 02]` (or a preview order
   nightly, §10.6).

### 4.3 Book a job from CRM (New → Appointment) `[S: 15][S: 19]`
1. Open form; generate client `bookingIdempotencyKey` (UUIDv7) once per form instance.
2. Pick service(s)/add-ons → client computes provisional duration; server is authoritative.
3. Pick/create customer → pick/create address (geocode → zone match) `[S: 15]`.
4. As soon as lines + location are known, client calls `scheduling.suggestions.get` (debounced
   300 ms): returns "first available time for the first available worker" plus alternates
   `[S: 15]`, ranked per dispatch strategy, each with an explanation.
5. Admin picks a suggestion or enters manual date/time/worker. Manual entries are validated live
   (`scheduling.slot.check`) → ✓ / ⚠ (soft: outside hours, outside zone, not skilled) / ✖ (hard:
   overlaps a job). Soft violations need `scheduling.override` permission; hard ones need
   `scheduling.override_overlap` (off by default for everyone, can be granted).
6. Optional: split / crew assignment `[S: llms]`; recurring toggle `[S: 14]`; assets/notes/tags/
   files `[S: 19]`; options & payment `[S: 16]`.
7. **Book now** → `scheduling.booking.createAppointment(cmd, idempotencyKey)`:
   a. Create customer/address if new (07) — inside the saga, idempotent by key.
   b. Run commit transaction (§10.3) → job (04) + JobSchedule + blocks + assignments.
   c. If recurring on → create RecurringSeries anchored on this job as occurrence #1 (§4.7).
   d. Payment instruction to 05 (charge now / store card / after job) — if a charge-now fails,
      job stays booked with payment status "Failed" and a red banner (decision: do not unbook;
      see Q-9) `[I]`.
   e. Emit `scheduling.job.scheduled`, `scheduling.assignment.created` (per worker),
      `scheduling.series.created` (if recurring).
   f. 08 (consumer) "notify your assigned team members, and send reminders directly to the
      customer" `[S: 15]`; calendar updates in real time for all viewers `[S: 15]`.
8. Success toast; job visible in calendar `[S: 15]`.

### 4.4 Auto-dispatch (any channel)
1. Trigger: online commit without worker preference; CRM "first available"; recurring
   materialization `[S: 14]`; unassigned job sweep; worker deactivated / time-off conflicts.
2. Build JobSpec → eligible workers (hard filters) → feasibility per worker (§10.1) → score
   (§10.2) → pick best; persist `DispatchDecision` (inputs, candidates, scores, reason).
3. If no candidate: job saved as `unassigned` (online: slot would not have been offered, so this
   occurs only for recurring/sweeps) → `scheduling.dispatch.failed` → 08 alerts admins; job shows
   in `Needs attention`.

### 4.5 Drag-to-reschedule (calendar) `[S: 08]`
1. `dragstart`: client fetches (or has cached) feasibility map for the visible range for this job
   (`scheduling.slot.checkMany`, computed per 15-min cell × visible worker) → colors drop zones.
2. `drop`: client applies **optimistic** move (card moves, "saving…" shimmer), opens confirm
   dialog (§3.5) if material; else (same worker, < 5 min change) sends immediately.
3. Client sends `scheduling.job.reschedule({jobId, expectedVersion, newStart, newWorkers,
   notify, idempotencyKey})`.
4. Server: version check → commit transaction (§10.3) with this job's own blocks excluded from
   conflict search → update blocks → bump `version` → emit `scheduling.job.rescheduled` (and
   `scheduling.assignment.changed` if workers changed) with `notify` flags.
5. Response OK → client confirms (shimmer off, 10-s **Undo** toast which issues the inverse
   reschedule with a new idempotency key).
6. Response `409 VERSION_CONFLICT` (someone else changed the job) → client reverts with animation,
   shows "Job #1042 was changed by Maria 20 s ago" + fresh state + `Try again here`.
   Response `409 SLOT_CONFLICT` → revert + conflict panel with alternatives/override.
7. 08 notifies customer + affected workers `[S: 08]` (coalesced, §5.9). Reminders re-scheduled
   by 08 on `scheduling.job.rescheduled`.

### 4.6 Reassign / split / crew
- **Reassign**: change worker only; same as 4.5 with time fixed. Removed worker gets
  `scheduling.assignment.removed`, new worker `scheduling.assignment.created`.
- **Split by service** `[S: llms]`: in job drawer choose "Split services" → each service line row
  gets its own worker and (optionally) start time; default = parallel at job start. Each
  assignment owns its own blocks. Job window = [min start, max end].
- **Crew** `[I]`: N workers on the whole job, same window. Duration per worker = job duration
  (default) or divided (if service marked `crewDivisible`, §5.2).
- Worker-app started jobs (status `in_progress` from 04) can be reassigned only by admin with
  confirmation "Sam has already started this job".

### 4.7 Create a recurring job `[S: 14]`
1. New → Recurring job (or Recurring toggle in appointment form).
2. Services → customer → address → Scheduling: frequency on, daily/weekly/monthly, end never /
   date / after N jobs, recurring discount fixed $ / % `[S: 14]`; extra info (notes, tags, files)
   `[S: 14]`; Book now.
3. Server: create `RecurringSeries` (rule in **local time of job location**), book occurrence #1
   as a normal job (with chosen or auto-dispatched worker), then materialize occurrences up to the
   horizon (§10.4), auto-dispatching each — "field will automatically find the best available
   worker for each future job" `[S: 14]` — with continuity preference for the series worker.
4. Beyond the horizon, occurrences are **projected** (virtual, read-only ghost cards) so "this job
   will now show up in your calendar on every scheduled date" `[S: 14]` without unbounded rows.
5. Daily job materializes newly-entered horizon dates (§10.4).

### 4.8 Edit recurring — scope
Dialog `[I]`: **This job only** · **This and all future jobs** · (`All jobs` only for
non-time fields like notes/tags). `[S: 14]` confirms "committed across all future bookings".
- This only → occurrence becomes `modified` (detached fields recorded), series unchanged.
- This and future → series split: old series `endsAfterOccurrenceKey = previous`; new series
  created from this date with new rule (preserves history & reporting) — or in-place update when
  only non-schedule fields change. Materialized future occurrences are re-evaluated: unchanged
  and not individually modified → updated; individually modified → kept, flagged "kept your
  edit" in summary dialog.
- Preview diff before commit: "12 future jobs will move from Tue 09:00 to Wed 10:00; 2 conflicts
  (Wed 18 Mar: Sam busy → will assign Alex)".

### 4.9 Skip / pause / end
- Skip occurrence → exception `skip`; job (if materialized) cancelled via 04 with reason
  `recurring_skip` (no cancellation fee) → `scheduling.occurrence.skipped`.
- Pause series (date range or indefinite) → occurrences inside become `paused` (not materialized;
  materialized ones cancelled with reason `series_paused`).
- End series (from date) → future materialized occurrences cancelled; customer notified once
  (08) with summary, not per job.

### 4.10 Route day (worker app) `[S: 02]`
1. Route service bookings land in the worker's `Route` container for that date (§4.2 / CRM).
2. Night before (configurable) `[I]`: preview optimization so admins/customers can get an ETA
   window ("between 10:00 and 12:00") — optional, improvement.
3. Worker taps/swipes **Start route** → `routing.route.start(routeId, startLocation,
   idempotencyKey)` → optimize (§10.6) → `routing.route.started` + `routing.route.optimized`
   (sequence + ETAs) → 08 may send "on the way / ETA" messages.
4. App guides to stop 1 (deep link to Maps/Waze/Google). Each stop runs the normal job flow (04).
5. On each job completion (04 event `job.job.completed`) → route progress recomputed →
   `routing.route.progressed`; admin sees % `[S: 02]`.
6. Last non-terminal stop terminal → route `completed` automatically `[S: 02]` →
   `routing.route.completed`.
7. If day ends with open stops → auto-close at availability end + grace → `closed_incomplete`;
   open stops become `needs_reschedule` → admins alerted.

### 4.11 Schedule / reschedule in the past `[S: llms]`
1. Permission `scheduling.past` (09). Booking site never allows it.
2. Availability & overlap checks run but only **warn** (the past is history; double-entry of
   historical jobs must not be blocked) — except overlap with an identical customer+service+time
   (likely duplicate) → hard confirm.
3. Notifications default **off** (toggle visible); reminders never scheduled for past times.
4. Job status chosen by admin (04): `scheduled` (to be completed later), or directly `completed`
   (for back-entry).
5. Event `scheduling.job.scheduled` carries `isBackdated=true` so 08 suppresses, 09 timesheets
   don't auto-create, reports count it by scheduled date.

### 4.12 Worker availability changes (from 09)
On `team.availability.updated` / `team.timeoff.created` / `team.calendar_sync.busy_updated`:
recompute affected future windows; for each existing job now outside availability → create
`ScheduleConflict(kind=availability)` (never auto-move customers' jobs silently) → admin sees
`Needs attention` with one-click "Auto-reassign all" (runs dispatch, shows preview).
On `team.worker.deactivated` → all future assignments become conflicts; bulk reassign wizard.

### 4.13 Job overruns / running late `[I]`
On 04's `job.job.started` / heartbeat: if `now > scheduledEnd` and job not complete, or projected
arrival at next job > next start + tolerance (10 min) → `scheduling.job.at_risk` (next job) → 08
can notify customer "running ~20 min late" (template owned by 08), admin sees red badge.

---
## 5. Business rules & math

### 5.1 Time model (TZ & DST) — applies everywhere
1. **Instants** (job start/end, block ranges, holds) are stored as UTC instants with half-open
   ranges `[start, end)` `[I]`.
2. **Wall-clock rules** (worker working hours, recurring rules, lead-time cutoffs, holidays) are
   stored as local date/time **plus an IANA time zone** — never as UTC offsets `[I]`.
3. Each job has a `locationTz`: shop's tz for shop jobs; geocoded address tz for mobile jobs;
   fallback business tz. Customer-facing times are shown in `locationTz` with tz abbreviation
   when it differs from the viewer's tz `[I]`.
4. Worker availability is evaluated in the **worker's** tz (normally = business tz). A worker
   whose availability tz ≠ job tz is handled naturally because both convert to instants.
5. **DST gap** (spring forward, e.g. 02:00–02:59 doesn't exist): a wall-clock time inside the gap
   maps to the instant just after the gap (02:30 → 03:00 new offset) for availability windows; a
   generated slot start inside the gap is **dropped**; a recurring occurrence at a gap time
   shifts forward by the gap length (02:30 → 03:30) and is flagged `dstAdjusted` `[I]`.
6. **DST overlap** (fall back, 01:00–01:59 occurs twice): slot generation iterates **instants**,
   so both 01:30 instances can appear, labeled with offset ("01:30 CDT", "01:30 CST"); recurring
   occurrences at an ambiguous time use the **earlier** offset `[I]`.
7. Durations are elapsed minutes (a 120-min job starting 00:30 on fall-back night ends 01:30
   second instance, wall-clock shows 01:30). Calendar renders 25-h / 23-h days.
8. Day boundaries: "a day" for availability, routes, horizons = local date in the relevant tz.
9. tz database updates are deployed with the platform; stored instants are re-validated nightly
   against stored wall-clock intent for future recurring occurrences (rare political tz changes).

### 5.2 Job duration
```
lineMinutes(service)      = service.durationMin × qty                    [S: 06]
lineMinutes(addOn)        = addOn.durationMin × qty   (service add-ons only; products = 0)  [S: 07]
workMinutes               = Σ lineMinutes over all lines on the job (or on the assignment's lines when split)
occupancyMinutes(worker)  = bufferBefore + workMinutes_for_worker + bufferAfter
bufferBefore/After        = max(service-level buffer of any line, tenant default buffer)    [I]
```
- Crew mode: `workMinutes_for_worker = workMinutes` unless every line is `crewDivisible`, then
  `ceilTo5( workMinutes / crewSize )` but ≥ max single line `minCrewDuration` `[I]`.
- Split-by-service: each worker's `workMinutes` = Σ of its lines; parallel assignments at same
  address do not add travel between them.
- Admin duration override on a job wins over computed; stored as `durationOverrideMin`.
- Durations are **snapshotted** on the job at booking. Later catalog edits do not move existing
  jobs `[I]`; a "Recalculate duration" action exists.
- Adding an add-on after booking `[S: 07]` (CRM or worker POS) extends duration → triggers
  conflict check against the next block; if conflict → `scheduling.conflict.detected`
  (soft: job still saved — the work is happening).
- Rounding: durations rounded **up** to 5 minutes. Slot `end = start + workMinutes` (buffers are
  not shown to customers).

### 5.3 Worker eligibility (hard filters, in order — cheapest first)
| # | Rule | Source | CRM override? |
|---|---|---|---|
| E1 | Worker active, accepted invite, has "can receive jobs" | `[S: 13]` (invite → appear in CRM → receive assignments) | no |
| E2 | Worker allowed to perform **every** service on the job (or on its split lines) | `[S: 13]` "customize the services that they can be assigned to" | yes (`scheduling.override`) |
| E3 | Mobile job: job location inside a zone the worker is assigned to. Shop job: worker assigned to that shop | `[S: 11][S: 13]` | yes — "override it by manually booking … through the CRM" `[S: 11]` |
| E4 | Customer has not blocked this worker (`doNotSend` list on customer, 07) | `[I]` | yes |
| E5 | For online channel: worker visible for online booking | `[I]` | n/a |

### 5.4 Feasibility (per worker, per candidate start `t`)
Given occupancy `O = [t − bufferBefore, t + work + bufferAfter)`:
- **F1 Availability**: `O ⊆ AvailableIntervals(worker, day)` where
  `Available = WorkingHours ∪ ExtraAvailability − TimeOff − ExternalBusy − Breaks` (all from 09).
  CRM may override (soft).
- **F2 No overlap**: `O ∩ ExclusiveBlocks(worker) = ∅` where exclusive blocks = other jobs' occupancy
  + active holds (+ time off if `timeOffIsHard`). **Hard** — "no double bookings" `[S: 19]`.
- **F3 Travel (mobile only)** `[S: 19]` "factors in the travel time": let `P` = latest block ending
  ≤ `O.start` on that local day with a location (or worker start location at shift start if
  "count travel from home" is on), `N` = earliest block starting ≥ `O.end`.
  `P.end + travel(P.loc → loc, departAt=P.end) ≤ O.start` and
  `O.end + travel(loc → N.loc, departAt=O.end) ≤ N.start`. Same address ⇒ travel 0.
  Shop jobs: travel 0 (worker is at shop).
- **F4 Lead time / horizon** (online only): `t ≥ now + leadTime`; `localDate(t) ≤ today + horizon`;
  same-day cutoff respected; holidays closed.
- **F5 Shop capacity**: shop jobs need a free **bay/resource unit** over `[t, t+work)` (capacity
  from 02; default = number of workers assigned to shop) `[I]`.
- **F6 Crew/split**: all required workers feasible for their own occupancy simultaneously.

### 5.5 Travel time
`travel(a→b, departAt)` returns `{minutes, source, confidence}`. Minutes **rounded up to 5**
and multiplied by a tenant-configurable safety factor (default 1.1) `[I]`. Tiered providers
(§10.5): exact cache → straight-line estimate (haversine × circuity 1.35 ÷ zone speed, + 3 min
parking) → road network (self-hosted OSRM/Valhalla-class) → traffic-aware commercial (paid tier).
The **same** estimate must be used at search and at commit for a given (a,b,departBucket) —
achieved by cache read-through with 15-min depart buckets, so a slot shown is not rejected at
commit because of estimator noise. Travel blocks on the calendar `[S: llms]` show these values.

### 5.6 Recurrence rules `[S: 14]` + `[I]` details
- Frequencies: **daily, weekly, monthly** `[S: 14]`; interval N (every N units) `[I]`; weekly
  may pick multiple weekdays `[I]`; monthly mode = `dayOfMonth` (clamped to last day: 31 → 30/28/29)
  or `nthWeekday` (e.g. 2nd Tue; "5th" = "last") `[I]`.
- End: **never**, **on date** (inclusive, local), **after N jobs** `[S: 14]`. Count semantics:
  N counts **delivered or scheduled** occurrences; skipped occurrences do **not** consume the count
  (so customer still gets N jobs) — tenant setting `skipsConsumeCount` default false `[I]`.
- Time of day: series stores local start time; each occurrence = that wall time in `locationTz`.
- Materialization horizon: rolling **90 days** (setting 14–365) and always ≥ next 2 occurrences
  `[I]`; occurrences beyond horizon projected virtually.
- Per-occurrence assignment: auto-dispatch per occurrence `[S: 14]`, with **continuity bonus** for
  `series.preferredWorkerId` and the previous occurrence's worker. Admin can pin a worker to the
  series (`pinWorker=true`) — then if pinned worker infeasible, occurrence is created `unassigned`
  + conflict rather than silently swapped.
- Time flexibility: `series.flexWindowMin` (default 0): if the exact time is infeasible for all
  workers, dispatcher may move the occurrence within ±flex on the same local day; else unassigned.
- Recurring discount `[S: 14]`: `fixed` (cents) or `percent` (basis points) **per job**:
  `discountCents = fixed ? min(fixedCents, subtotalCents) : roundHalfUp(subtotalCents × bp / 10000)`;
  never below 0 total. Applied to subtotal before tax (tax order owned by 05). Snapshotted per
  occurrence at materialization so price-list changes apply only to newly materialized jobs unless
  admin re-prices future occurrences (§4.8 scope).

### 5.7 Holidays & closures `[I]`
Tenant `HolidayCalendar` (manual dates + optional country preset). Per date: `closed` or custom
hours. Online: closed days never offered. CRM: warn. Recurring policy per series (default from
tenant): `skip` · `moveToNextOpenDay` · `moveToPrevOpenDay` · `keepAndFlag`. Moves keep time of day,
re-run dispatch, and produce `scheduling.occurrence.moved` with reason `holiday`.

### 5.8 Route services `[S: 02]`
- Service flagged `schedulingMode = 'route'` (set in service editor "Select route" `[S: 02]`; owned
  by 02). Customer selects a **day**, not a time `[S: 02]`.
- Capacity per worker-day route: `stops ≤ maxStops` and
  `Σ work + estimatedTravel ≤ availableMinutes(worker, day) − timedJobsMinutes` (timed jobs the
  same worker has that day are fixed anchors; route stops fill around them) `[I]`.
- Estimated travel for capacity uses the current cheapest-insertion tour estimate (§10.7).
- Mixed days: a worker may have timed jobs and a route on the same day; optimizer treats timed
  jobs as hard time windows (§10.6).
- Progress `[S: 02]`: `pct = floor(100 × done / (total − cancelled))`, `done` = completed +
  skipped-by-admin; 0 stops → 100% and route auto-completed.

### 5.9 Notification rules emitted by 03 (catalog owned by 08)
| Trigger | Default recipients | Notes |
|---|---|---|
| Job booked (CRM) | assigned workers; customer confirmation + reminders `[S: 15]` | suppress if backdated |
| Job booked (online) | customer, assigned workers, admins (new-booking alert) | `[I]` |
| Rescheduled (time) | customer + assigned workers `[S: 08]` | coalesce: multiple changes to same job within **2 min** → one notification with final state `[I]` |
| Reassigned | removed worker, new worker; customer only if worker visible to customer | `[I]` |
| Unassigned/dispatch failed | admins/managers | `[I]` |
| Recurring series created/updated/ended | customer summary (one message) | `[I]` |
| Occurrence skipped / moved (holiday) | customer + worker | `[I]` |
| Route started/optimized | customers on route: ETA window (optional) | `[I]` |
| Running late | next customer (opt-in) | `[I]` |
Reschedules within **15 min of now** or in the past never notify customer automatically `[I]`.

### 5.10 Money touches
03 computes no prices except the recurring-discount spec applied per occurrence (§5.6). All
amounts integer cents; percentages in basis points; rounding half-up at the line level by the
pricing engine (05/06).

---

## 6. State machines

### 6.1 SlotHold
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `hold.create` | feasible (§10.3) & session hold count < limit | `active` | insert exclusive `hold` block(s); `scheduling.hold.created` |
| active | `hold.extend` | total TTL ≤ 20 min | active | `expiresAt` updated |
| active | `booking.commit` | same session, idempotency key | `consumed` | block kind hold→job; `scheduling.job.scheduled` |
| active | `hold.release` (customer changed slot / left) | — | `released` | delete blocks; `scheduling.hold.released` |
| active | clock ≥ expiresAt (sweeper every 30 s; also lazily ignored in queries) | — | `expired` | delete blocks; `scheduling.hold.expired` (01 may show "time expired") |
| expired | `booking.commit` | slot still feasible | `consumed` | re-acquire within commit transaction |
| expired | `booking.commit` | infeasible | expired | 409 `SLOT_TAKEN` + alternatives |

Queries treat holds with `expiresAt ≤ now` as non-existent even before the sweeper runs.

### 6.2 JobSchedule (scheduling sub-state; job **status** is 04's)
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `unscheduled` | schedule (time + workers) | feasibility / override | `scheduled` | blocks; `scheduling.job.scheduled` |
| `unscheduled` | schedule (time, no worker) | — | `unassigned` | `scheduling.job.scheduled` (workers=[]) |
| `unassigned` | assign | feasible | `scheduled` | `scheduling.assignment.created` |
| `unassigned` | dispatch fails | — | unassigned | `scheduling.dispatch.failed` |
| `scheduled` | reschedule | version match, feasible/override | scheduled | `scheduling.job.rescheduled` |
| `scheduled` | reassign / split | feasible | scheduled | `scheduling.assignment.changed` |
| `scheduled` | worker deactivated / availability removed / overrun cascade | — | `conflicted` | `scheduling.conflict.detected` |
| `conflicted` | resolve (move/reassign/accept) | — | scheduled | `scheduling.conflict.resolved` |
| `scheduled` | unschedule | job not started | `unscheduled` | delete blocks; `scheduling.job.unscheduled` |
| any non-terminal | 04 `job.job.cancelled` | — | `released` | delete blocks; route stop removed |
| `scheduled` | 04 `job.job.started` | — | `in_progress` | record actualStart; block end extends live if overrun |
| `in_progress` | 04 `job.job.completed` | — | `done` | block trimmed to actualEnd (frees remaining time for same-day bookings) `[I]` |
| `scheduled` (route_day) | route started | — | `sequenced` | sequence + ETA written |

### 6.3 RecurringSeries
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `draft` | create (Book now) | valid rule | `active` | occurrence #1 job; materialize horizon; `scheduling.series.created` |
| active | pause(from,to?) | — | `paused` | cancel materialized in range; `scheduling.series.paused` |
| paused | resume / pause end reached | — | active | materialize; `scheduling.series.resumed` |
| active | end condition reached (date/count) | — | `ended` | `scheduling.series.ended` |
| active/paused | end(fromDate) | — | `ended` | cancel future; notify once |
| active | edit "this and future" (schedule fields) | — | `ended` (split) + new series `active` | `scheduling.series.updated` with `splitFrom` |
| active | 06 `subscription.subscription.cancelled` | series.source=subscription | `ended` | per subscription policy (finish already-paid occurrences) |
| active | 06 `subscription.subscription.past_due` | policy=pause | `paused` | `[I]` (dunning owned by 06) |

### 6.4 Occurrence
| State | Event | Next | Notes |
|---|---|---|---|
| `projected` (virtual) | enters horizon | `materialized` | job created via 04, dispatched |
| projected/materialized | skip | `skipped` | job cancelled reason `recurring_skip` |
| materialized | edited "this only" | `modified` | detached fields list |
| materialized/modified | job completed (04) | `fulfilled` | counts toward "after N" |
| materialized | job cancelled by customer (04) | `cancelled` | counts? → setting (default no) |
| projected | holiday policy move | `projected` (moved) | `movedFrom` recorded |
| materialized | dispatch fails | `materialized` + `unassigned` | conflict listed |

### 6.5 Route (per worker per local date)
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | first route stop booked | — | `planned` | `routing.route.created` |
| planned | nightly preview / admin re-optimize | ≥2 stops | planned | preview sequence (not binding) |
| planned | `route.start` (worker swipe) | worker is owner; date = today local (or admin allows early) | `in_progress` | optimize; `routing.route.started`, `routing.route.optimized` |
| in_progress | stop added/removed | — | in_progress | re-optimize remaining (prefix fixed) unless order locked; `routing.route.reoptimized` |
| in_progress | stop terminal (04 event) | remaining > 0 | in_progress | `routing.route.progressed` |
| in_progress | last stop terminal | — | `completed` | auto-close `[S: 02]`; `routing.route.completed` |
| planned/in_progress | availability end + grace passed | open stops | `closed_incomplete` | stops → `needs_reschedule`; `routing.route.auto_closed` |
| planned | all stops removed | — | `empty` (deleted) | — |

### 6.6 ScheduleConflict
`open → resolved | dismissed (accepted risk) | superseded (job cancelled)`; kinds:
`overlap`, `availability`, `travel`, `zone`, `skill`, `capacity`, `holiday`, `overrun`.

---

## 7. Data model

Stack-agnostic TypeScript-style. All entities carry `tenantId` (multi-tenant isolation) and
`createdAt/updatedAt` (instants). IDs are opaque strings (UUIDv7 recommended for index locality)
`[I]`.

```ts
type Id = string;
type Instant = string;        // RFC3339 UTC, e.g. "2026-03-05T15:00:00Z"
type LocalDate = string;      // "2026-03-05"
type LocalTime = string;      // "09:00"
type IanaTz = string;         // "America/Chicago"
type Cents = number;          // integer
type BasisPoints = number;    // integer, 1500 = 15%
interface GeoPoint { lat: number; lng: number; }

// ---------- Inputs owned by other modules (read models 03 keeps in sync) ----------
interface WorkerScheduleProfile {            // projection of 09's Worker
  workerId: Id; tenantId: Id;
  active: boolean; receivesJobs: boolean; onlineBookable: boolean;
  tz: IanaTz;
  calendarColor: string;                     // [S: 13]
  zoneIds: Id[]; shopIds: Id[];              // [S: 11][S: 13]
  serviceIds: Id[] | 'all';                  // [S: 13]
  startLocation?: GeoPoint;                  // home/depot for first-job travel
  defaultBufferMin?: number;
  maxJobsPerDay?: number;                    // [I]
  version: number;                           // bumps on any change → cache invalidation
}
interface AvailabilityRule {                 // owned by 09 (weekly template)
  workerId: Id; weekday: 0|1|2|3|4|5|6; start: LocalTime; end: LocalTime;  // end may be < start = overnight
  tz: IanaTz; effectiveFrom: LocalDate; effectiveTo?: LocalDate;
}
interface AvailabilityOverride { workerId: Id; date: LocalDate; intervals: {start: LocalTime; end: LocalTime}[]; tz: IanaTz; } // [] = day off
interface TimeOff { id: Id; workerId: Id; range: [Instant, Instant]; hard: boolean; reason?: string; }
interface ExternalBusy { workerId: Id; range: [Instant, Instant]; source: 'google'|'ics'; externalId: string; } // calendar sync [S: 13]

// ---------- Owned by 03 ----------
interface SchedulingSettings {
  tenantId: Id; businessTz: IanaTz;
  slotGranularityMin: { online: number; crm: number };
  leadTimeMin: number; sameDayCutoff?: LocalTime; horizonDays: number;
  defaultBufferMin: number;
  travelMode: 'off'|'estimate'|'road'|'traffic';
  travelSafetyFactor: number;               // 1.1
  countTravelFromStart: boolean;
  dispatchStrategy: 'earliest'|'closest'|'balanced'|'preferred_first';
  dispatchWeights?: Partial<DispatchWeights>;
  slotDisplay: { mode: 'exact'|'window'; windowMin?: number };
  compactScheduling: boolean;
  holdTtlSec: number; holdMaxTotalSec: number;
  recurringHorizonDays: number; skipsConsumeCount: boolean;
  holidayPolicyDefault: HolidayPolicy;
  route: { enabled: boolean; maxStopsPerDay: number; endMode: 'return_to_start'|'open';
           autoCloseGraceMin: number; previewNightBefore: boolean };
  notifyDefaults: { customerOnReschedule: boolean; workersOnReschedule: boolean };
  version: number;
}

interface JobSchedule {                     // 1:1 with 04's Job
  jobId: Id; tenantId: Id;
  state: 'unscheduled'|'unassigned'|'scheduled'|'conflicted'|'in_progress'|'sequenced'|'done'|'released';
  kind: 'timed'|'route_day'|'all_day';
  locationKind: 'mobile'|'shop';
  location?: GeoPoint; addressId?: Id; shopId?: Id; zoneId?: Id | null;  // null = outside zones (override)
  locationTz: IanaTz;
  start?: Instant; end?: Instant;            // timed: required; route_day: undefined
  localDate?: LocalDate;                     // for route_day/all_day
  durationMin: number;                       // snapshot (computed or override)
  durationOverrideMin?: number;
  durationBreakdown: { lineId: Id; minutes: number }[];
  assignmentMode: 'single'|'crew'|'split';
  routeId?: Id; routeSequence?: number; eta?: Instant;
  seriesId?: Id; occurrenceKey?: string;     // e.g. "2026-03-05"
  isBackdated: boolean;
  overrides: OverrideRecord[];               // which guards were overridden, by whom
  actualStart?: Instant; actualEnd?: Instant;
  version: number;                           // optimistic concurrency
  lastDispatchDecisionId?: Id;
}
interface OverrideRecord { guard: 'availability'|'zone'|'skill'|'overlap'|'lead_time'|'holiday'|'past'; byUserId: Id; at: Instant; note?: string; }

interface JobAssignment {
  id: Id; tenantId: Id; jobId: Id; workerId: Id;
  role: 'lead'|'crew'|'split';
  lineIds: Id[] | 'all';                     // split by service [S: llms]
  start: Instant; end: Instant;              // timed; for route jobs = route day bounds
  status: 'active'|'removed';
  notifiedAt?: Instant;
}

interface ScheduleBlock {                   // THE busy ledger; source of truth for "no double booking"
  id: Id; tenantId: Id;
  ownerType: 'worker'|'resource';            // resource = shop bay
  ownerId: Id;
  kind: 'job'|'hold'|'route_capacity'|'personal'|'break';
  range: [Instant, Instant];                 // occupancy incl. buffers (half-open)
  exclusive: boolean;                        // participates in exclusion constraint
  jobId?: Id; assignmentId?: Id; holdId?: Id;
  location?: GeoPoint;                       // for travel computation
  localDate: LocalDate;                      // in owner tz — lock & index key
  active: boolean;
}
// INVARIANT (DB-enforced): for active & exclusive blocks with same (tenantId, ownerType, ownerId),
// ranges never overlap. Postgres-class: EXCLUDE USING gist (owner WITH =, range WITH &&) WHERE (active AND exclusive).
// Other stacks: per-owner-day serialized write + uniqueness on discretized 5-min cells. (§10.3)

interface SlotHold {
  id: Id; tenantId: Id;
  channel: 'online'|'crm'|'mcp_bookings'|'mcp_crm';
  sessionId: string;                         // booking session (01) or user id
  idempotencyKey: string;
  jobSpecHash: string;                       // lines+location hash; commit must match
  workerIds: Id[]; resourceId?: Id; routeId?: Id;
  start?: Instant; end?: Instant; localDate?: LocalDate;
  state: 'active'|'consumed'|'released'|'expired';
  expiresAt: Instant; createdAt: Instant; totalExtendedSec: number;
}

interface IdempotencyRecord { tenantId: Id; key: string; command: string; requestHash: string;
  status: 'in_progress'|'succeeded'|'failed'; response?: unknown; expiresAt: Instant; } // 24 h

interface DispatchDecision {
  id: Id; tenantId: Id; jobId: Id; trigger: 'online'|'crm_suggest'|'recurring'|'sweep'|'manual_auto';
  strategy: string; weights: DispatchWeights;
  candidates: { workerId: Id; feasible: boolean; rejectReasons?: string[];
                start?: Instant; addedTravelMin?: number; score?: number;
                breakdown?: Record<string, number> }[];
  chosenWorkerIds: Id[]; at: Instant;
}
interface DispatchWeights { travel: number; earliness: number; gap: number; load: number;
  preferred: number; continuity: number; rating: number; }

interface ScheduleConflict { id: Id; tenantId: Id; jobId: Id; workerId?: Id;
  kind: 'overlap'|'availability'|'travel'|'zone'|'skill'|'capacity'|'holiday'|'overrun';
  severity: 'hard'|'soft'; detail: string; state: 'open'|'resolved'|'dismissed'|'superseded';
  detectedAt: Instant; resolvedBy?: Id; }

interface RecurringSeries {
  id: Id; tenantId: Id; customerId: Id; addressId?: Id; shopId?: Id;
  source: 'crm'|'online'|'subscription'|'mcp';
  subscriptionId?: Id;                       // [S: 01] auto-scheduling packages (06)
  lineTemplate: { serviceId: Id; addOnIds: {id: Id; qty: number}[]; qty: number }[];
  rule: RecurrenceRule;
  startTimeLocal?: LocalTime;                // undefined for route services (day only)
  locationTz: IanaTz;
  anchorDate: LocalDate;                     // occurrence #1
  end: { type: 'never' } | { type: 'until'; date: LocalDate } | { type: 'count'; count: number }; // [S: 14]
  discount?: { type: 'fixed'; cents: Cents } | { type: 'percent'; bp: BasisPoints };             // [S: 14]
  preferredWorkerId?: Id; pinWorker: boolean; flexWindowMin: number;
  holidayPolicy: HolidayPolicy;
  notes?: string; tagIds: Id[]; fileIds: Id[]; assetIds: Id[];   // [S: 14][S: 19]
  state: 'draft'|'active'|'paused'|'ended';
  pause?: { from: LocalDate; to?: LocalDate };
  materializedThrough: LocalDate;
  splitFromSeriesId?: Id; version: number;
}
interface RecurrenceRule {
  freq: 'daily'|'weekly'|'monthly';          // [S: 14]
  interval: number;                          // ≥1 [I]
  byWeekday?: (0|1|2|3|4|5|6)[];             // weekly
  monthlyMode?: 'day_of_month'|'nth_weekday';
  byMonthDay?: number;                       // 1..31 (clamped)
  bySetPos?: 1|2|3|4|-1; byWeekdayOfMonth?: 0|1|2|3|4|5|6;
}
type HolidayPolicy = 'skip'|'next_open_day'|'prev_open_day'|'keep_and_flag';
interface RecurrenceException {
  seriesId: Id; occurrenceKey: LocalDate;    // original date = stable identity
  type: 'skip'|'move'|'modify'|'pause';
  newStart?: Instant; newWorkerIds?: Id[]; detachedFields?: string[]; reason?: string;
}
interface Occurrence {                       // row exists only once materialized or excepted
  seriesId: Id; occurrenceKey: LocalDate; jobId?: Id;
  state: 'materialized'|'modified'|'skipped'|'cancelled'|'fulfilled';
  dstAdjusted?: boolean; movedFrom?: LocalDate; sequenceNo: number;
}

interface HolidayCalendar { tenantId: Id; entries: { date: LocalDate; name: string; closed: boolean;
  hours?: {start: LocalTime; end: LocalTime}[] }[]; countryPreset?: string; }

interface Route {
  id: Id; tenantId: Id; workerId: Id; localDate: LocalDate; tz: IanaTz;
  state: 'planned'|'in_progress'|'completed'|'closed_incomplete';
  stopJobIds: Id[];                          // unordered set until sequenced
  sequence?: Id[];                           // optimized order
  orderLocked: boolean;
  startLocation?: GeoPoint; endLocation?: GeoPoint;
  startedAt?: Instant; completedAt?: Instant;
  optimization?: { algorithm: 'exact_dp'|'nn_2opt_oropt'|'fallback_angle';
                   provider: string; totalTravelMin: number; totalKm: number;
                   computedAt: Instant; matrixCostCents: number; };
  progress: { total: number; completed: number; skipped: number; cancelled: number; pct: number };
  version: number;
}

interface TravelEstimate {                   // cache
  key: string;                               // geohash7(a)|geohash7(b)|dowHourBucket|provider
  minutes: number; meters: number; source: 'same'|'haversine'|'road'|'traffic';
  computedAt: Instant; ttlSec: number;
}

interface CalendarViewPreference { userId: Id; view: 'list'|'day'|'week'|'month'|'map';
  filters: JobFilter; workerOrder?: Id[]; displayTz: 'business'|'viewer'; zoom: 15|30|60; }
interface JobFilter { statuses?: string[]; tagIds?: Id[]; zoneIds?: Id[]; workerIds?: Id[];   // [S: 08]
  customerIds?: Id[]; paymentStatuses?: string[]; serviceIds?: Id[]; sources?: string[];
  unassignedOnly?: boolean; hasConflict?: boolean; from?: LocalDate; to?: LocalDate; q?: string; }
interface SavedView { id: Id; tenantId: Id; ownerUserId?: Id; name: string; shared: boolean; pref: CalendarViewPreference; }
```

**Key indexes** `[I]`: `ScheduleBlock(tenantId, ownerType, ownerId, localDate)`; GiST/interval
index on `range`; `JobSchedule(tenantId, start)`, `(tenantId, state)`, `(tenantId, routeId)`;
`JobAssignment(tenantId, workerId, start)`; `SlotHold(state, expiresAt)`;
`Occurrence(seriesId, occurrenceKey)` unique; `Route(tenantId, workerId, localDate)` unique;
`IdempotencyRecord(tenantId, key)` unique.

---
## 8. API surface

Transport-agnostic commands/queries (REST/RPC/GraphQL/MCP all map onto these). Every **command**
accepts `idempotencyKey` (required for create/commit/reschedule/start) and returns the same
response on replay within 24 h; a replay with a different request hash → `409 IDEMPOTENCY_MISMATCH`.
Every mutating command on an existing job takes `expectedVersion`. Auth scopes below map to 09's
RBAC; `public:booking` = booking-site/Bookings-MCP scope (customer-level, no business data
`[S: llms]`).

### 8.1 Availability & holds
| Name | Input | Output | Auth |
|---|---|---|---|
| `scheduling.availability.search` | `{ lines: LineSpec[], location: {addressGeo?, shopId?}, from: LocalDate, to: LocalDate (≤ 62 days), channel, preferredWorkerId?, crewSize?, tz? }` | `{ locationTz, days: [{ date, closed?, slots: [{start, end, label, workerCount, token}] } \| { date, routeDay: true, label: "Anytime Thursday", capacityLeft }], nextAvailable?: {start}, computedAt, ttlSec }` (slot `token` = signed {start, specHash, candidateWorkers} — opaque to client) | `public:booking` (no worker ids unless setting) / `scheduling.view` |
| `scheduling.availability.firstAvailable` | same as search minus range (`searchDays` ≤ 60) | `{ start, end, workerIds, explanation }` | same |
| `scheduling.suggestions.get` (CRM) | `{ lines, location, after?: Instant, count (≤10), strategy?, assignmentMode, crewSize? }` | `[{ start, end, workerIds, score, addedTravelMin, explanation: string[] }]` | `scheduling.book` |
| `scheduling.slot.check` | `{ jobSpec \| jobId, start, workerIds }` | `{ feasible, violations: [{guard, severity, detail, overridable}] }` | `scheduling.book` |
| `scheduling.slot.checkMany` | `{ jobId, range: [Instant, Instant], workerIds, cellMin }` | compact bitmap per worker (feasible / soft / hard) for drag-hover | `scheduling.reschedule` |
| `scheduling.hold.create` | `{ slotToken \| {start, workerIds}, jobSpec, sessionId, idempotencyKey }` | `{ holdId, expiresAt, start, end }` or `409 SLOT_TAKEN {alternatives[3]}` | `public:booking` / `scheduling.book` |
| `scheduling.hold.extend` | `{ holdId, sessionId }` | `{ expiresAt }` / `410 HOLD_EXPIRED` | same |
| `scheduling.hold.release` | `{ holdId, sessionId }` | `{ ok }` | same |

### 8.2 Booking & job scheduling
| Name | Input | Output | Auth |
|---|---|---|---|
| `scheduling.booking.commit` (online/MCP) | `{ holdId, jobDraft (lines, customer ref, address, answers…), idempotencyKey }` | `{ jobId, jobNumber, start, end, workerIds? }` / `409 SLOT_TAKEN` | `public:booking` |
| `scheduling.booking.createAppointment` (CRM) | `{ lines (incl. inline-created), customer (id \| new), address (id \| new) \| shopId, schedule: {mode: 'suggested'\|'manual'\|'unscheduled'\|'route_day', start?, localDate?, workerIds?, assignmentMode, splits?: {lineIds, workerId, start?}[] }, overrides: Guard[], recurring?: SeriesInput, options (discount/tax/giftcard refs), payment instruction, assets, notes, tags, files, notify: {customer, workers}, idempotencyKey }` | `{ jobId, jobNumber, seriesId?, schedule, warnings[] }` | `scheduling.book` (+ `scheduling.override`, `scheduling.past` as needed) |
| `scheduling.job.reschedule` | `{ jobId, expectedVersion, start? \| localDate?, workerIds?, durationOverrideMin?, scope?: 'this'\|'this_and_future', notify, message?, overrides, idempotencyKey }` | `{ schedule, version, warnings }` / `409 VERSION_CONFLICT {current}` / `409 SLOT_CONFLICT {violations, alternatives}` | `scheduling.reschedule` |
| `scheduling.job.assign` / `.unassign` / `.reassign` | `{ jobId, expectedVersion, workerIds, lineIds?, notify, idempotencyKey }` | `{ schedule, version }` | `dispatch.assign` |
| `scheduling.job.split` | `{ jobId, expectedVersion, splits: {lineIds, workerId, start?}[] }` | `{ schedule }` | `dispatch.assign` |
| `scheduling.job.unschedule` | `{ jobId, expectedVersion, notify }` | `{ schedule }` | `scheduling.reschedule` |
| `scheduling.job.autoDispatch` | `{ jobId, strategy? , keepTime: boolean }` | `{ decision }` | `dispatch.assign` |
| `scheduling.job.bulkShift` | `{ jobIds[], shiftDays \| toDate, keepWorkers, notify, dryRun }` | `{ results[], conflicts[] }` | `scheduling.reschedule` |
| `scheduling.conflicts.list` / `.resolve` / `.dismiss` | filters / `{conflictId, action}` | list / ok | `scheduling.view` / `scheduling.reschedule` |
| `scheduling.dispatch.explain` | `{ jobId }` | last `DispatchDecision` humanized | `scheduling.view` |

### 8.3 Calendar / jobs tab queries
| Name | Input | Output | Auth |
|---|---|---|---|
| `scheduling.calendar.range` | `{ from: Instant, to: Instant (≤ 42 d), filters: JobFilter, include: ('travel'\|'availability'\|'routes'\|'holds'\|'projected')[] }` | `{ jobs: CalendarJob[], travelBlocks[], availability: {workerId, intervals}[], routes[], projected[] , cursor }` | `scheduling.view` (workers: own only unless `scheduling.view_all`) |
| `scheduling.jobs.list` | `{ filters, sort, page: {cursor, size ≤ 200}, tab: 'upcoming'\|'needs_attention'\|'past'\|'recurring' }` | `{ rows: JobRow[], cursor, totalApprox }` | `scheduling.view` |
| `scheduling.map.whoIsFree` | `{ at: Instant (default now), zoneId?, near?: GeoPoint }` | `[{ workerId, state, lastLocation, lastPingAt, currentJobId?, nextFree: {from, to}, distanceKm? }]` | `scheduling.view_all` |
| `scheduling.search.jobs` (provider for global search `[S: 08]`) | `{ q, limit ≤ 20 }` | `[{ jobId, jobNumber, start, customerName, workerNames, status }]` | `scheduling.view` |
| `scheduling.subscribe` (realtime) | `{ range, filters }` | stream of `{type, jobId, version, patch}` | `scheduling.view` |
| `scheduling.export.csv` | `{ filters }` | file (async) | `scheduling.export` |

### 8.4 Recurring
| Name | Input | Output | Auth |
|---|---|---|---|
| `scheduling.series.create` | `SeriesInput {customerId, address, lineTemplate, rule, startTimeLocal, anchorDate, end, discount, preferredWorkerId?, pinWorker, flexWindowMin, holidayPolicy, notes, tags, files, source, subscriptionId?}` + idempotencyKey | `{ seriesId, occurrence1JobId, materialized: n, conflicts[] }` | `scheduling.book` / system (06) |
| `scheduling.series.preview` | rule + anchor + end + count ≤ 52 | `[{date, start, feasibleWorkers, holiday?, dstAdjusted?}]` | `scheduling.book` |
| `scheduling.series.update` | `{ seriesId, expectedVersion, fromOccurrenceKey, scope: 'this'\|'this_and_future'\|'all_non_schedule', patch, dryRun }` | `{ diff: {moved[], repriced[], keptModified[], conflicts[]}, newSeriesId? }` | `scheduling.reschedule` |
| `scheduling.series.skip` / `.unskip` | `{ seriesId, occurrenceKey, notify }` | ok | `scheduling.reschedule` |
| `scheduling.series.pause` / `.resume` / `.end` | `{ seriesId, from, to?, notify }` | ok | `scheduling.reschedule` / system (06) |
| `scheduling.series.list` / `.get` | filters / id | rows / detail incl. occurrences | `scheduling.view` |

### 8.5 Routes
| Name | Input | Output | Auth |
|---|---|---|---|
| `routing.route.get` | `{ routeId }` or `{ workerId, localDate }` | Route + stops + progress `[S: 02]` | worker (own) / `routes.view` |
| `routing.route.start` | `{ routeId, startLocation?, idempotencyKey }` | `{ sequence, etas, totalTravelMin }` | route owner worker / admin |
| `routing.route.optimize` (admin re-run / preview) | `{ routeId, lockPrefix?: number }` | same | `routes.manage` |
| `routing.route.reorder` (manual) | `{ routeId, expectedVersion, sequence, lock: boolean }` | route | `routes.manage` / worker if allowed |
| `routing.route.addStop` / `.removeStop` / `.moveStop` | `{ routeId, jobId, toRouteId? }` | route | `routes.manage` |
| `routing.route.close` | `{ routeId, reason }` | route | `routes.manage` / system |

### 8.6 Settings
`scheduling.settings.get/update`, `scheduling.holidays.get/update`, `scheduling.views.save/list`
— `settings.scheduling` permission.

### 8.7 Error codes
`SLOT_TAKEN`, `VERSION_CONFLICT`, `SLOT_CONFLICT`, `HOLD_EXPIRED`, `HOLD_LIMIT`,
`OUTSIDE_SERVICE_AREA`, `NO_ELIGIBLE_WORKER`, `NOT_SKILLED`, `OUTSIDE_AVAILABILITY`,
`LEAD_TIME`, `BEYOND_HORIZON`, `HOLIDAY_CLOSED`, `PAST_NOT_ALLOWED`, `RANGE_TOO_LARGE`,
`GEOCODE_FAILED`, `ROUTE_ALREADY_STARTED`, `ROUTE_CAPACITY`, `IDEMPOTENCY_MISMATCH`,
`RATE_LIMITED`. Each violation object: `{code, guard, severity, overridable, detail, workerId?}`.

---

## 9. Interfaces with other modules

Event envelope `[I]`: `{ eventId (UUIDv7), name, tenantId, occurredAt, actor {type, id},
aggregate {type, id, version}, payload, causationId, correlationId }`. Delivered at-least-once via
transactional outbox; consumers must be idempotent on `eventId`. Ordering guaranteed per
aggregate (`jobId`, `seriesId`, `routeId`).

### 9.1 CONSUMES

| From | Event / data | 03 reaction |
|---|---|---|
| **01** Booking Portal | *Sync calls*: `availability.search`, `firstAvailable`, `hold.create/extend/release`, `booking.commit` | Contract: 01 never computes availability; always shows times in `locationTz`; renders route days with "routes text" `[S: 02]`; must release holds on slot change/abandon; uses one idempotency key per commit attempt chain. Latency SLO for 01: search p95 ≤ 300 ms (14 days, ≤ 30 workers), hold p95 ≤ 250 ms. |
| 01 | `booking.session.abandoned` | release any active hold for session |
| **02** Catalog & Coverage | Read API `catalog.getDurations(lineSpecs)` → `{lineId, minutes, bufferBefore, bufferAfter, crewDivisible, schedulingMode: 'timed'\|'route', locationMode: 'mobile'\|'shop'\|'both'}` | snapshot at booking |
| 02 | `coverage.zone.resolve(geo)` → `{zoneId \| null, travelFeeRef}`; `coverage.shop.get(shopId)` → `{geo, tz, capacityUnits, hours}` | eligibility E3, F5 |
| 02 | `catalog.service.updated`, `catalog.addon.updated` | invalidate slot cache; existing jobs unchanged |
| 02 | `coverage.zone.updated`, `coverage.zone.deleted`, `coverage.shop.updated` | invalidate; future jobs in deleted zone → `zone` conflicts (soft) |
| **04** Job Lifecycle | *Sync*: `jobs.createFromBooking(draft, schedule)` inside 03's commit saga; `jobs.cancel(jobId, reason)` for skips/pauses | 04 returns `jobId`, `jobNumber` |
| 04 | `job.job.started` (actualStart), `job.job.on_my_way`, `job.job.completed` (actualEnd), `job.job.cancelled`, `job.job.no_show`, `job.lines.changed` | block adjust/trim/release; route progress & auto-close `[S: 02]`; duration change conflicts `[S: 07]`; overrun detection |
| 04 | `quote.quote.accepted` with `bookOnline` | none directly — customer books through 01 `[S: 04]`; CRM "Book from quote" opens §3.6 prefilled |
| **05** Payments | `payment.authorization.failed` / `payment.deposit.failed` during online booking | release hold (01 drives) |
| **06** Subscriptions | `subscription.subscription.activated` (package has automatic scheduling `[S: 01]`, frequency, services, start date) | `series.create(source='subscription')`; occurrence #1 time: from booking (online) or first-available |
| 06 | `subscription.subscription.paused` / `.resumed` / `.cancelled` / `.package_changed` | pause/resume/end/update-this-and-future series |
| **07** Customers | `customer.address.updated` (geo changed), `customer.merged`, `customer.worker_blocklist.updated` | recompute travel for future jobs at that address (warn on new conflicts); re-point series; eligibility E4 |
| 07 | Read: assets/vehicles for job cards ("vehicle on every job" `[S: llms]`) | display only |
| **08** Comms | Read: notification preference per customer (SMS/email opt-out) — not needed by 03 | — |
| **09** Team | Read: `WorkerScheduleProfile`, `AvailabilityRule`, overrides, `TimeOff`, `ExternalBusy`, permissions | eligibility & F1 |
| 09 | `team.worker.created/updated/deactivated`, `team.worker.zones_changed`, `team.worker.services_changed`, `team.availability.updated`, `team.timeoff.created/updated/deleted`, `team.calendar_sync.busy_updated` | invalidate caches; detect conflicts (§4.12) |
| 09 | `team.location.pinged` (live tracking `[S: 11]`) | map who-is-free; running-late |

### 9.2 PRODUCES

| Event | Key payload | Main consumers |
|---|---|---|
| `scheduling.hold.created` / `.released` / `.expired` | holdId, sessionId, start, end | 01 (UI), 07 (abandoned-booking capture: expired holds are a strong lead signal `[S: 10]`) |
| `scheduling.job.scheduled` | jobId, start/end or localDate, workerIds, locationTz, kind, channel, isBackdated, notify flags | 04 (status appointment), **08** (confirmation, worker notice, reminder scheduling `[S: 15]`), 09 (worker calendar sync out), 10 |
| `scheduling.job.rescheduled` | jobId, old {start,end,workerIds}, new {…}, reason, actor, notify {customer, workers}, message | **08** (reschedule notices `[S: 08]`, re-plan reminders), 09 (calendar sync), 05 (pre-auth expiry re-plan if auth tied to date) |
| `scheduling.job.unscheduled` | jobId, old window | 08, 09 |
| `scheduling.assignment.created` / `.removed` / `.changed` | jobId, workerId(s), lineIds, start/end | 08 (worker push/SMS), 09 (calendar sync, timesheet context), 04 (worker app job list) |
| `scheduling.dispatch.decided` | jobId, decisionId, chosen, strategy | 10 / reporting |
| `scheduling.dispatch.failed` | jobId, reasons per worker | 08 (admin alert) |
| `scheduling.conflict.detected` / `.resolved` | conflict object | 08 (admin alert), UI |
| `scheduling.job.at_risk` | jobId, projectedDelayMin | 08 (running-late notice) |
| `scheduling.series.created` / `.updated` / `.paused` / `.resumed` / `.ended` | seriesId, rule, source, subscriptionId | 06 (sync status), 08 (customer summary) |
| `scheduling.occurrence.materialized` / `.skipped` / `.moved` / `.unassignable` | seriesId, occurrenceKey, jobId | 06 (usage counting for packages), 08 |
| `routing.route.created` / `.started` / `.optimized` / `.reoptimized` / `.progressed` / `.completed` / `.auto_closed` | routeId, workerId, date, sequence, etas, pct | 08 (ETA messages), 04 (worker app next stop), 09 (timesheet hint: route started), reporting |
| `scheduling.settings.updated` | changed keys | 01 (cache bust of booking widget config) |

### 9.3 Contract summary per agent (what each must provide / may rely on)
- **01 ↔ 03**: 01 sends `LineSpec[]` exactly as the customer configured (serviceId, addOn ids+qty,
  answers irrelevant to scheduling), plus `addressGeo` (01 geocodes via shared geocoder) or
  `shopId`. 03 guarantees: any slot returned was feasible at `computedAt`; a hold either succeeds
  or returns alternatives; a consumed hold → job is booked (exactly once per idempotency key).
  01 must not cache slots longer than `ttlSec` (default 60 s).
- **02 → 03**: durations in minutes (integer, multiple of 5), `schedulingMode`, `locationMode`,
  buffers, `crewDivisible`, zone polygons & shop capacity. Zone resolution is 02's function.
- **03 ↔ 04**: 04 owns `Job` & status; 03 owns `JobSchedule`. Creation is a single local
  transaction when both live in one database (recommended), otherwise a saga with 03's commit as
  the pivot and compensation `jobs.void(jobId)`. 04 must emit start/complete/cancel events and
  `job.lines.changed` with new line durations.
- **06 → 03**: auto-scheduling packages call `series.create` with `{subscriptionId, frequency,
  services, preferred day/time?}`; 03 returns `seriesId`; 06 owns billing, 03 owns dates.
  Frequency vocabulary must align (06 "booking frequency" `[S: 01]` ↔ `RecurrenceRule`).
- **03 → 08**: every schedule-changing event carries `notify` flags + `actor` + old/new values,
  enough for 08 to render without calling back. 08 owns templates, channels, quiet hours,
  reminder timing; 08 must cancel & re-create reminders on reschedule/unschedule/cancel.
- **09 → 03**: worker eligibility & availability read models with `version` numbers; permission
  checks via 09's policy function `can(user, action, resource)`. Permissions 03 needs defined:
  `scheduling.view`, `scheduling.view_all`, `scheduling.book`, `scheduling.reschedule`,
  `scheduling.override`, `scheduling.override_overlap`, `scheduling.past`, `dispatch.assign`,
  `routes.view`, `routes.manage`, `scheduling.export`, `settings.scheduling`.

---

## 10. Algorithms / pseudo-code

### 10.1 Slot generation

```pseudo
// Shared types
JobSpec := {
  lines, workMinutes, bufferBefore, bufferAfter,
  locationKind: mobile|shop, geo?, shopId?, zoneId?, locationTz,
  schedulingMode: timed|route, assignmentMode: single|crew, crewSize: int (1 = single),
  requiredServiceIds
}

function searchAvailability(req): AvailabilityResult
  s        := settings(req.tenantId)
  spec     := buildJobSpec(req.lines, req.location)               // 02 durations, §5.2
  if spec.schedulingMode == route: return searchRouteDays(req, spec)     // §10.7
  window   := clampWindow(req.from, req.to, s, req.channel, spec.locationTz)
             // online: start ≥ now+leadTime, end ≤ today+horizon, ≤ 62 days; CRM: no lead/horizon
  workers  := eligibleWorkers(spec, req.channel, req.preferredWorkerId)   // E1..E5 (§5.3)
  if workers is empty: return {days: [], reason: NO_ELIGIBLE_WORKER}
  gran     := s.slotGranularityMin[req.channel]
  result   := []
  for each localDate d in window (ascending, in spec.locationTz):
      if holidayClosed(d) and req.channel is online: result.add(d, closed=true); continue
      slotMap := Map<Instant, Candidate[]>()
      for each w in workers:
          day      := workerDay(w, d)            // cached per (w, d, versionVector) — §10.1.1
          for each gap in day.freeIntervals:     // availability minus exclusive blocks
              // earliest occupancy start in gap, considering travel from previous anchor
              prev := day.anchorBefore(gap.start)        // block or start-of-shift location
              next := day.anchorAfter(gap.end)
              tPrevTravel := travelMin(prev.loc, spec.geo, prev.end)   // 0 for shop / same addr
              tNextTravelUB := travelMinUpperBound(spec.geo, next.loc)
              lo := max(gap.start + tPrevTravel, window.earliest) + spec.bufferBefore
              hi := gap.end - spec.bufferAfter - spec.workMinutes      // latest start ignoring next travel
              t  := alignUp(lo, gran, spec.locationTz)                   // align in local wall time
              while t <= hi:
                  occEnd := t + spec.workMinutes + spec.bufferAfter
                  if next exists:
                      // cheap check first, exact only if near the boundary
                      if occEnd + tNextTravelUB > next.start:
                          if occEnd + travelMin(spec.geo, next.loc, occEnd) > next.start: break  // later t only worse
                  if spec.locationKind == shop and not shopUnitFree(spec.shopId, t, occEnd): t += gran; continue
                  if not dstValid(t, spec.locationTz): t += gran; continue          // §5.1 gap
                  slotMap[t].add(Candidate(w, addedTravel(prev, next, spec.geo), t))
                  t += gran
      // crew: need crewSize distinct workers at the same t
      for (t, cands) in slotMap sorted by t:
          if len(cands) < spec.crewSize: continue
          if s.compactScheduling and dayHasJobs(d) and not adjacentToExisting(cands, t): continue
          best := rankCandidates(cands, spec, s)                 // §10.2
          result.add(d, Slot(start=t, end=t+spec.workMinutes,
                             workerCount=len(cands), token=sign({t, specHash, best.ids})))
      if req.mode == firstAvailable and result.hasAnySlot(): break
  return result with ttlSec=60
```

#### 10.1.1 `workerDay(w, d)` — cached free-interval index
```pseudo
function workerDay(w, d):
  key := (w.id, d, w.version, availabilityVersion(w), ledgerVersion(w, d), holidayVersion)
  if cache.has(key): return cache[key]
  avail := expandRules(w.availabilityRules, d, w.tz)          // wall-clock → instants (DST-aware),
                                                             // overnight rules split across dates
  avail := applyOverride(avail, w.overrides[d])
  avail := subtract(avail, w.timeOff ∩ d, w.externalBusy ∩ d, w.breaks ∩ d)
  if holidayClosed(d) and holiday applies to workers: avail := ∅      // CRM override path ignores
  blocks := ledger.blocks(ownerType=worker, ownerId=w.id, localDate ∈ {d-1, d, d+1}) // active, incl. live holds
  free := subtract(avail, union(b.range for b in blocks if b.exclusive and not expired(b)))
  anchors := sorted(blocks with location) ∪ {startOfShift(w.startLocation) if s.countTravelFromStart}
  return cache[key] := {freeIntervals: free, anchors}
```
Invalidation: `ledgerVersion(w, d)` increments in the same transaction that writes any block for
(w, d) — reads are always consistent with the ledger snapshot they came from.

Complexity: O(W × D × (slots per day)) with travel lookups mostly cache hits; for 30 workers ×
14 days × 32 slots ≈ 13k candidate checks → well under 50 ms CPU `[I]`.

#### 10.1.2 `travelMin` pruning
```pseudo
function travelMin(a, b, departAt):
  if a == null or b == null: return 0                     // un-geocoded: warn upstream
  if distanceMeters(a, b) < 75: return 0                   // same building
  key := (geohash7(a), geohash7(b), bucket15(departAt, dowHour))
  if cache has key: return cache[key]
  lb := haversine(a,b) / MAX_SPEED_ZONE                    // lower bound (e.g. 100 km/h)
  ub := haversine(a,b) × 1.6 / MIN_SPEED_ZONE + 5           // upper bound
  // callers needing only "does it fit?" use lb/ub to avoid provider calls
  v := provider(settings.travelMode).duration(a, b, departAt)    // §10.5
  v := ceilTo5(v × settings.travelSafetyFactor)
  cache[key] := v; return v
```

### 10.2 Dispatch scoring (best / closest worker)

Hard filters first (§5.3/§5.4). Then **lower score = better**. All terms normalized to 0..1.

```pseudo
DEFAULT_WEIGHTS by strategy:
  earliest        : earliness 1.0, travel 0.3, gap 0.1, load 0.1, preferred 0.5, continuity 0.4, rating 0.05
  closest         : earliness 0.2, travel 1.0, gap 0.2, load 0.1, preferred 0.5, continuity 0.4, rating 0.05  // [S: 19] "finds the closest worker"
  balanced        : earliness 0.3, travel 0.5, gap 0.2, load 1.0, preferred 0.5, continuity 0.4, rating 0.05
  preferred_first : earliness 0.3, travel 0.4, gap 0.1, load 0.1, preferred 3.0, continuity 1.0, rating 0.05

function score(c: Candidate, spec, ctx, W):
  addedTravel := travel(prev→job) + travel(job→next) − travel(prev→next)     // insertion cost, minutes
  earliness   := (c.start − ctx.searchStart) / ctx.searchSpan
  gapPenalty  := fragmentation(c)  // leftover gaps < min service duration before/after → wasted minutes / 120
  loadRatio   := bookedMinutes(c.worker, day) / availableMinutes(c.worker, day)
  preferred   := 1 if c.worker == spec.preferredWorkerId (customer/quote preferred worker [S: 04]) else 0
  continuity  := 1 if c.worker served this customer last (or series worker) else 0
  rating      := 1 − normalizedRating(c.worker)                          // optional, from 08 reviews
  return W.travel   * min(addedTravel / 60, 1)
       + W.earliness* earliness
       + W.gap      * min(gapPenalty, 1)
       + W.load     * loadRatio
       − W.preferred* preferred
       − W.continuity*continuity
       + W.rating   * rating

function rankCandidates(cands, spec, s):
  W := merge(DEFAULT_WEIGHTS[s.dispatchStrategy], s.dispatchWeights)
  sorted := sort cands by (score asc, workerId asc)        // deterministic tie-break
  return first spec.crewSize of sorted, with breakdown for explanation
```

"First available time for the first available worker" `[S: 15]` = iterate slots in time order
(earliness dominates) and take the best-scored worker at the earliest slot. CRM suggestions
return a **diverse** set: earliest overall; closest-today; preferred/continuity worker's
earliest; earliest on the next 2 different days `[I]`.

**Explanation strings** (shown in UI): "Earliest opening", "+8 min drive from previous job
(#1039, 4.2 km)", "Served this customer last time", "Lightest day (35% booked)".

**Unassigned sweep** (every 10 min, and on availability changes): for each `unassigned` future
job, run dispatch with `keepTime=true`; on success assign + notify; never moves time automatically.

**Reassignment on worker loss**: run dispatch per affected job (keepTime=true); present as a
preview list (admin confirms in one click) — never auto-applies to customer-visible changes unless
setting `autoReassignOnTimeOff=true` `[I]`.

### 10.3 Commit transaction — "no double bookings" `[S: 19]`

Concurrency defenses, layered:
1. **Holds** reduce contention for online flows (customer sees time reserved while paying).
2. **Per worker-day serialization**: commit acquires transaction-scoped locks on keys
   `(tenantId, workerId, localDate)` for every affected worker/date (+ `(tenantId, shopId,
   localDate)` for shop capacity), **sorted** lexicographically to avoid deadlocks. Travel
   feasibility depends on neighbors, so the exclusion constraint alone is not sufficient — the
   lock makes "read neighbors → validate → insert" atomic.
3. **DB exclusion constraint** on exclusive ScheduleBlocks (last line of defense against bugs or
   lock misuse). On stores without exclusion constraints: insert one row per 5-min cell into a
   `(owner, cellStart)` unique index (≤ 288 rows/day/worker) — same guarantee.
4. **Idempotency keys** make client/network retries safe (book once).
5. **Optimistic versions** on JobSchedule for edits (drag/drop races between admins).

```pseudo
function commitSchedule(cmd):                 // used by hold.create, booking.commit, createAppointment, reschedule
  rec := idempotency.begin(cmd.tenantId, cmd.key, hash(cmd))
  if rec.status == succeeded: return rec.response
  if rec.status == in_progress: return 409 RETRY_LATER       // concurrent duplicate
  BEGIN TRANSACTION (READ COMMITTED is enough with explicit locks)
    if cmd.jobId: js := lockRow(JobSchedule, cmd.jobId)
                  if js.version != cmd.expectedVersion: ROLLBACK; return 409 VERSION_CONFLICT(js)
    keys := sorted({(w, localDate(t)) for each target assignment} ∪ shopKeys)
    for k in keys: acquireXactLock(k)                  // advisory lock / SELECT … FOR UPDATE on WorkerDay row
    for each assignment a in cmd.targets:
        neighbors := ledger.blocks(a.worker, dates around a) excluding cmd.jobId's own blocks
                     and excluding cmd.holdId's blocks (we are consuming it), and expired holds
        v := validate(a, neighbors, spec, cmd.channel)       // F1..F6 with SAME travel cache
        hard := v.filter(severity=hard and not (overridden and actorMay(override_overlap)))
        soft := v.filter(severity=soft and not overridden)
        if hard or (soft and cmd.channel is online): ROLLBACK; return 409 SLOT_CONFLICT(v, alternatives(cmd))
        if soft and not actorMay(scheduling.override): ROLLBACK; return 403 OVERRIDE_REQUIRED(v)
    if cmd.holdId: markConsumed(cmd.holdId)
    deleteBlocks(jobId=cmd.jobId)                          // reschedule: replace atomically
    insertBlocks(cmd.targets)                              // exclusion constraint verifies
    upsert JobSchedule (+version) ; upsert JobAssignments
    if new job: jobs.createFromBooking(...)                // 04, same transaction when co-located
    bumpLedgerVersion(keys)
    outbox.append(events…)                                 // scheduling.job.scheduled / rescheduled / assignment.*
  COMMIT                                                   // on exclusion violation → map to 409 SLOT_TAKEN
  idempotency.succeed(rec, response)
  return response
```
Lock hold time target < 30 ms; travel lookups needed inside the transaction are pre-warmed into
cache before BEGIN (validation then hits cache only; on a miss inside the lock, use the
upper-bound estimate — conservative — rather than calling a remote provider while holding a lock).

**Hold spam protection**: max 1 active hold per booking session, 5 hold creations per session
per 10 min, 30 per IP per 10 min; holds never extend beyond 20 min total `[I]`.

**Alternatives** on conflict: run `searchAvailability` from the requested time ±1 day, return
3 nearest by |Δt| (same worker first if CRM).

### 10.4 Recurrence expansion & materialization

```pseudo
function occurrences(series, fromDate, toDate): List<OccurrenceInstance>   // pure, deterministic
  rule := series.rule; out := []; n := 0           // n counts toward 'count' end
  d := series.anchorDate
  iterate candidate dates c generated by rule from anchorDate:
     daily:   c = anchor + k*interval days
     weekly:  for each week w = anchor-week + k*interval: for each wd in byWeekday (sorted): c = date(w, wd), c ≥ anchor
     monthly: m = anchor-month + k*interval;
              c = (mode == day_of_month) ? date(m, min(byMonthDay, daysIn(m)))
                                         : nthWeekdayOf(m, bySetPos, byWeekdayOfMonth)  // -1 = last
     if series.end.type == until and c > series.end.date: stop
     key := c                                        // occurrenceKey = ORIGINAL date (stable identity)
     ex  := exceptions[key]
     if ex.type == skip: if not settings.skipsConsumeCount: continue else n++; continue
     if series.pause covers c: continue                 // paused dates don't count
     n++
     if series.end.type == count and n > series.end.count: stop
     date := c; start := null
     if ex.type == move: start := ex.newStart
     else:
        if holidayClosed(c): 
            switch series.holidayPolicy:
              skip          -> record skippedByHoliday(key); n--; continue     // doesn't consume
              next_open_day -> date := nextOpenDay(c)     // never past the next regular occurrence; if collision → keep_and_flag
              prev_open_day -> date := prevOpenDay(c)     // never before today/previous occurrence
              keep_and_flag -> flag
        if series.startTimeLocal: start := toInstant(date, series.startTimeLocal, series.locationTz,
                                                     gap=shiftForward, overlap=earlier)  // §5.1
     if key < fromDate (by resulting date) : continue
     if resulting date > toDate: stop
     out.add({key, date, start, sequenceNo: n, flags})
  return out
// Guard: iteration limit 5,000 candidates per call; byMonthDay 31 clamps (never skips months).

job materializeAll():                           // runs hourly; each series processed idempotently
  for each series in active series where materializedThrough < today(series.tz) + horizon:
     lock(series)
     target := today + horizonDays; also ensure ≥ 2 future materialized occurrences
     for inst in occurrences(series, series.materializedThrough + 1, target):
        if Occurrence(series.id, inst.key) exists: continue          // unique (seriesId, key) → idempotent
        spec := buildJobSpec(series.lineTemplate, series.location)   // current durations (snapshot now)
        price := pricing(series.lineTemplate, series.discount)       // 02/05/06 engines; discount per job [S: 14]
        chosen := dispatchForOccurrence(series, inst, spec)
        jobId := jobs.createFromBooking(source='recurring', seriesId, key, lines, price)
        if chosen: commitSchedule(assign chosen, start=inst.start or routeDay)
        else:      schedule unassigned at inst.start; conflict(kind='availability'); emit scheduling.occurrence.unassignable
        insert Occurrence(series.id, inst.key, jobId, state=materialized)
        emit scheduling.occurrence.materialized
     series.materializedThrough := target
     if end reached: series.state := ended; emit scheduling.series.ended

function dispatchForOccurrence(series, inst, spec):
  if series.pinWorker: return feasible(series.preferredWorkerId, inst) ? it : null
  cands := feasibleWorkers(spec, inst.start)                     // §10.1 single-time check
  if cands empty and series.flexWindowMin > 0:
       cands := searchWithin(inst.start ± flex, same local day)  // returns (worker, t)
  return rankCandidates(cands, spec, W with continuity weight ×2)  // keep same worker when possible
```
Projection for calendar beyond horizon: `occurrences(series, horizonEnd+1, viewEnd)` computed on
read (pure function, no dispatch) and rendered as ghost cards "Projected · worker TBD".

**Edit this-and-future (schedule fields)**:
```pseudo
function splitSeries(series, fromKey, patch):
  old := series; new := clone(series) with patch; new.anchorDate := patch.anchorDate ?? fromKey
  if old.end.type == count: remaining := old.end.count − fulfilledOrScheduledBefore(old, fromKey)
                            old.end := {count: consumed}; new.end := {count: remaining}
  else old.end := {until: dayBefore(fromKey)}
  for occ in materialized(old, key ≥ fromKey):
      if occ.state == modified: keep (re-parent to new series, flag keptModified)
      else: reschedule/reprice job to match new rule's instance (or cancel if no matching instance)
  materialize(new)
  return diff
```

### 10.5 Travel-time / distance-matrix provider strategy (budget-aware)

| Tier | Provider class | Cost model | Use |
|---|---|---|---|
| T0 | Same-location shortcut | free | same address/building |
| T1 | Straight-line estimate: haversine × circuity (1.35 default, per-zone learnable) ÷ zone avg speed (urban 30 km/h, suburban 45, rural 60) + 3 min | free, in-process | slot search pruning, offline worker app, fallback |
| T2 | Self-hosted open-source road router on OpenStreetMap (OSRM / Valhalla / GraphHopper-class), regional extracts | fixed infra (one small VM per region), unlimited queries | default for availability & route optimization (Starter plan) |
| T3 | Commercial traffic-aware matrix API (Google Routes / Mapbox / HERE / TomTom-class) | per-element pricing | optional higher tier: route optimization at "start route" only, ≤ 1 call/route/start, n ≤ 25 stops; never for slot search |

Rules `[I]`:
- Provider chosen per call: `min(tenantPlanTier, settings.travelMode)`; fall back down the tiers on
  error/timeout (T3 800 ms → T2 300 ms → T1).
- Cache: `TravelEstimate` keyed by geohash-7 pair (~150 m cells) + depart bucket (dow × hour for
  T3; none for T2/T1). TTL T2 30 days, T3 7 days. Expected hit rate > 90% for repeat customers.
- Learning: compare scheduled travel vs actual (on-my-way → arrived timestamps from 04) per zone;
  adjust circuity/speed per zone weekly (bounded ±30%).
- Matrix for route optimization: (n+2)² elements; with T3 at 25 stops = 729 elements/start;
  budget guard: per-tenant monthly element cap, then degrade to T2 automatically and log.
- Vendor terms (caching/storage limits of commercial APIs) must be checked at stack selection —
  some prohibit long-term caching (flag for Phase 2) `[I]`.

### 10.6 Route optimization (single worker-day; "start route") `[S: 02]`

Problem: open-path TSP (or closed if `endMode=return_to_start`) from `start` (worker GPS at swipe,
else start location) over stops `S` (n typically 3–20), with optional fixed **timed anchors**
(timed jobs the worker has that day) as time windows, optional soft customer windows, and a fixed
prefix (stops already completed / in progress).

```pseudo
function optimizeRoute(route, startLoc, now):
  fixed  := stops in route with status in {completed, in_progress} in actual order    // prefix
  free   := remaining stops (not locked) ; anchors := timed jobs of worker today after now
  if route.orderLocked: return route.sequence (recompute ETAs only)
  nodes  := [startLoc] + free + anchors + (endLoc if closed)
  M      := matrix(nodes)   // provider tier §10.5; on failure → T1 matrix
  service[i] := durationMin of stop i (+ buffers)
  if len(free) + len(anchors) <= 9 and no soft windows:
      order := heldKarp(M, nodes, open = (endMode == open))      // exact, O(n² 2ⁿ) ≤ ~46k states
  else:
      order := nearestNeighbor(M, startIdx)
      order := twoOpt(order, M, timeBudget=150ms)                 // reverse segments while improving
      order := orOpt(order, M, segLen ∈ {1,2,3}, timeBudget=150ms)
      // multi-start: repeat from 3 alternative seeds (farthest-first, sweep-angle, random) within 600 ms total; keep best
  // time-window feasibility (anchors are hard, customer windows soft)
  cost(order) := Σ travel + Σ waiting + PENALTY_LATE × Σ lateness(soft) + ∞ × anchorViolation
  if anchorViolation(order): order := insertAnchorsAtFeasiblePositions(order)  // cheapest feasible insertion; if impossible, flag conflict 'travel'
  etas := simulate(order, departAt=now, M, service)
  return {sequence: fixed + order, etas, totalTravelMin, totalKm, algorithm}

function twoOpt(order, M):
  improved := true
  while improved and withinBudget():
     improved := false
     for i in 1..n-2: for k in i+1..n-1:
        Δ := M[o[i-1]][o[k]] + M[o[i]][o[k+1]] − M[o[i-1]][o[i]] − M[o[k]][o[k+1]]
        // asymmetric matrices: recompute reversed segment cost exactly instead of Δ shortcut
        if Δ < −ε: reverse(o[i..k]); improved := true
  return order
```
Notes:
- Real road matrices are asymmetric (one-way streets); 2-opt reversal must re-sum segment cost
  for asymmetric M (n ≤ 25 so O(n³) per pass is fine).
- Determinism: same inputs → same output (seeded RNG with routeId) so admin and worker app agree.
- Start-route is idempotent: second swipe returns the existing sequence (`ROUTE_ALREADY_STARTED`
  as info, not error).
- Re-optimization after changes keeps the fixed prefix and the **current next stop** if the worker
  is already driving to it (`on_my_way`), to avoid U-turns.
- Offline (worker app): compute T1 matrix + NN + 2-opt locally (pure JS, < 50 ms for 20 stops),
  submit result with `computedOffline=true`; server accepts unless the route changed (version) →
  re-optimizes and pushes.
- Sequential job flow `[S: 02]`: worker app shows only "Next stop" as primary; others listed;
  worker may skip ahead (allowed, logged) `[I]`.
- Auto-close `[S: 02]`: on every stop terminal event → if no non-terminal stops → `completed`.

### 10.7 Route-day availability & assignment across workers (VRP-lite)

```pseudo
function searchRouteDays(req, spec):
  for each localDate d in window:
     best := null
     for each eligible worker w with availability on d:
        r := route(w, d) or empty
        if r.state in {completed, closed_incomplete, in_progress}: continue  // can't add after start online
        if len(r.stops) + 1 > maxStops: continue
        ins := cheapestInsertion(r.previewTour, spec.geo)          // T1/T2 estimates
        minutes := r.workMinutes + spec.workMinutes + r.travelMin + ins.addedTravel
        if minutes > availableMinutes(w, d) − timedMinutes(w, d): continue
        cand := (w, ins.addedTravel, load)
        best := argmin(score using 'closest' weights (cluster compactness))
     if best: day d offered with label "Anytime {weekday}" (text from 01 settings [S: 02])
  return days
```
Capacity holds for route days are `route_capacity` ScheduleBlocks (non-exclusive; counted) plus a
per-(worker, date) lock at commit to re-check `maxStops` & minutes.

Optional nightly **balance** (admin setting, default off) `[I]`: for tomorrow's routes not
started and stops whose worker isn't customer-visible, run a sweep-clustering + cheapest-insertion
VRP across workers in the same zone to reduce total travel; moves require ≥ 10% saving and emit
`scheduling.assignment.changed` (no customer notice since time unchanged & worker hidden).

### 10.8 Calendar conflict detection (continuous)
```pseudo
on any ledger change for (worker, date): 
  blocks := ordered blocks(worker, date)
  for consecutive (a, b): 
     if a.range ∩ b.range ≠ ∅: conflict(overlap, hard)          // only possible via override
     if travel(a.loc, b.loc) > b.start − a.end: conflict(travel, soft) ; render travel block red
  for each job block outside availability: conflict(availability, soft)
  upsert ScheduleConflict rows (dedupe by (jobId, kind)); auto-resolve rows no longer true
```

### 10.9 Optimistic UI reconciliation (client)
```pseudo
state: serverSnapshot (by jobId → {schedule, version}), pendingOps (queue)
onDrop(job, target):
  op := {id: uuid(), jobId, expectedVersion: serverSnapshot[job].version, target, key: uuid()}
  applyLocal(op); pendingOps.push(op); send(op)
onResponse(op, ok, data):
  if ok: serverSnapshot[op.jobId] := data; pendingOps.remove(op); showUndo(op)
  else if VERSION_CONFLICT: serverSnapshot[op.jobId] := data.current; rollback(op); toast("changed by …")
  else if SLOT_CONFLICT: rollback(op); openConflictPanel(data)
  else (network): retry with SAME op.key (idempotent) ×3 with backoff, then rollback + offline badge
onRealtimePatch(p):
  if pendingOps has op for p.jobId: defer until op settles (then reapply p if p.version > snapshot)
  else serverSnapshot[p.jobId] := p; rerender
```

---
