# 08 — Marketing, Communications & Reporting

> Agent 08 spec for "the platform" (working name). Phase 1 "CRM brain".
> Scope: every outbound message (system notifications, campaigns, drips), every conversation
> (unified inbox, live chat, team group chat, two-way SMS), reviews & referrals, and every number
> the owner looks at (dashboard tiles, reports, leaderboard, goals, forecasting).
> Owner of the **Master Notification Catalog** (Section 5.1) for the whole product.
> Stack-agnostic. Money is integer minor units (cents). All times stored UTC; displayed and
> bucketed in an explicit, named time zone (Section 5.10).

## 0. Reading guide

### 0.1 Citation keys
Every claim is tagged `[S: <key>]` (sourced) or `[I]` (inferred). Keys map to files:

| Key | File | Topic |
|---|---|---|
| T01 | `research/transcripts/01_mP9YP49qDiI.txt` | Subscriptions |
| T02 | `research/transcripts/02_rMVRwp-anZM.txt` | Route optimization |
| T03 | `research/transcripts/03_-d1cjZPEmoQ.txt` | Timesheets |
| T04 | `research/transcripts/04_pASG8RTrhWc.txt` | Quotes & invoices |
| T05 | `research/transcripts/05_0qNwovgNTqM.txt` | Online booking page |
| T06 | `research/transcripts/06_0atw7gNWDzg.txt` | Services & prices |
| T07 | `research/transcripts/07_uD5ND6GyoYA.txt` | Add-ons |
| T08 | `research/transcripts/08_w3D1W6oOaNY.txt` | Jobs & appointments |
| T09 | `research/transcripts/09_xO4jahos3rE.txt` | CRM tips: transactions, people, discounts, gift cards, reporting, leaderboard |
| T10 | `research/transcripts/10_p7TFZDchFbk.txt` | Marketing |
| T11 | `research/transcripts/11_8sT9cFazZAA.txt` | Service areas |
| T12 | `research/transcripts/12_4BwGvptaW6s.txt` | Drip campaigns (Engage) |
| T13 | `research/transcripts/13_C2EsjjXfOOE.txt` | Add team member |
| T14 | `research/transcripts/14_5zqqoRbV1DU.txt` | Recurring job |
| T15 | `research/transcripts/15_Dh1Ib3a4psU.txt` | Book a job |
| T16 | `research/transcripts/16_G43PcI4hWOk.txt` | Payment methods |
| T17 | `research/transcripts/17_UezcRXW1BcE.txt` | Transactions |
| T18 | `research/transcripts/18_PVo5YzUbOpI.txt` | Add customer |
| T19 | `research/transcripts/19_ZPf7JSlvIcw.txt` | Schedule a job |
| T20 | `research/transcripts/20_-TpNhaNTQkM.txt` | Add lead |
| Tnn.desc | `research/transcripts/NN_<id>.description` | YouTube description / chapter list for video NN |
| LLMS | `research/site/llms.txt` | Fieldd official facts (2026-10-23) |

`[I]` = inference (industry convention, regulation as commonly understood, or reasonable design
choice). **All regulatory statements are `[I]` and must be reviewed by counsel before launch.**
Vendor prices are `[I]` approximations from public list pricing as remembered — re-quote before
committing.

### 0.2 What the sources actually say (evidence inventory for this module)
This is the complete set of source facts this module rests on. Everything else is design.

| # | Fact | Source |
|---|---|---|
| E1 | Marketing suite is "fully integrated into your CRM"; launch **email, SMS, or postcard campaigns** "using customizable templates and targeting options" | [S: T10] |
| E2 | "All interactions and analytics from your marketing campaigns will be collected here" — no third-party setup | [S: T10] |
| E3 | Engage follows up with "leads who didn't finish booking" and sends "a promo to customers who haven't booked in a while" | [S: T10] |
| E4 | Engage = drip marketing suite; example: visitor abandons online booking → "send them a text a couple of hours later" | [S: T12] |
| E5 | Example: "curated holiday promotions to my customer base year round" via drip | [S: T12] |
| E6 | Path: "open the marketing tab, click new, and select drip" | [S: T12] |
| E7 | Templates exist "for holidays, announcements, reminders, and more"; "get a campaign going in less than a minute" | [S: T12] |
| E8 | Custom drip starts by "selecting a trigger"; example "select action and then choose new customer" | [S: T12] |
| E9 | Step: email with subject line "using dynamic fields to personalize it with the customer's name" | [S: T12] |
| E10 | Email editor has templates (e.g. "welcome email"); "automatically use my brand logo and colors" | [S: T12] |
| E11 | Step: "add a 3-day delay, then select SMS"; SMS "customized with their name and include my booking link" | [S: T12] |
| E12 | Drip editable after launch: "I can always come back and edit or add more steps to the drip at any time" | [S: T12] |
| E13 | Review step: "label the campaign and choose when the messages should send … I'll select ASAP"; then "launch" | [S: T12] |
| E14 | Metrics: "navigate back to the marketing tab and open the drip window … how many customers are currently in the drip, how many have completed it, and the business that I've generated from it" | [S: T12] |
| E15 | Engage triggers on "customer behavior"; chapters include "Real-time campaign performance" | [S: T12.desc] |
| E16 | Drag-to-reschedule: "Field will update your schedule and notify the necessary people automatically" | [S: T08] |
| E17 | Booking from CRM: "notify your assigned team members, and send reminders directly to the customer" | [S: T15] |
| E18 | "Field sends reminders and updates directly to the customer" once a customer is attached | [S: T19] |
| E19 | Quotes/invoices: "Settings, find communication, and click email … how your quotes and invoices look, as well as control over the emails that get sent to the customers when a quote or invoice is sent"; also "reminder settings" | [S: T04] |
| E20 | Quote has "contact info to notify the customer"; "book online button" in quote | [S: T04] |
| E21 | Timesheets: "worker will receive a notification in the worker app for them to either see the approved time sheets or see the rejected ones" | [S: T03] |
| E22 | Worker invite: "Field will automatically send an email inviting the worker to log in to their Field worker app" | [S: T13] |
| E23 | Route: worker can "message the client" inside job workflow | [S: T02] |
| E24 | Live tracking: "customers … receive real-time updates on your arrival … reduces calls or text while you're on the road" | [S: T11] |
| E25 | Cancellation: "collect feedback to understand why and choose whether or not to prompt them to reschedule" | [S: T05] |
| E26 | Portal visits auto-tracked; booking sources "tracked for you and can be viewed directly from your dashboard" | [S: T05] |
| E27 | Dashboard: "key reporting insights … revenue trends … top performing services and your best customers and workers … visual tiles that are easy to read and customize" | [S: T09] |
| E28 | "Inside the reporting tab, all of these graphs and charts can be downloaded"; download icon throughout; CSV | [S: T09] |
| E29 | Highlights "best performing services, workers, customers, and service areas" | [S: T09] |
| E30 | "Leaderboard … ranking performance based on bookings completed, reviews received, and revenue generated" | [S: T09] |
| E31 | People profiles show "service history, ratings, notes, and account status" | [S: T09] |
| E32 | Transactions: worker summary of revenue by team member; default current week; date picker by week | [S: T17] |
| E33 | Postcard QR scan → info shows up in lead board | [S: T20] |
| E34 | Plan Start includes "Google review booster, automated SMS and email job updates, SMS marketing, calendar sync, on-my-way tracking" | [S: LLMS] |
| E35 | Plan Scale adds "branded customer app, email campaigns, drip marketing, push marketing, referral marketing" | [S: LLMS] |
| E36 | Plan Pro adds "heatmaps, custom analytics, profile cloning, multi-business hub" | [S: LLMS] |
| E37 | New dashboard: "business goals with progress tracking, all reviews, all photos, team availability, notifications, and which customers and team members are active right now" | [S: LLMS] |
| E38 | Reporting: "cleaner visuals, custom views, forecasting, and an MVP view that shows the top team member" | [S: LLMS] |
| E39 | Marketing: "AI marketing builder for SMS, email, and more; referral codes tracked in one place that work online and in the customer app; new QR code booking options; AI gift card design" | [S: LLMS] |
| E40 | "Live chat: all conversations rebuilt in one place, plus group chats with the team" | [S: LLMS] |
| E41 | "Emails: nearly 200 emails rebuilt to be modern, clean, and easy to read" | [S: LLMS] |
| E42 | Darwin AI / CRM MCP "analyzing business data" | [S: LLMS] |
| E43 | Customers: "multiple phone numbers, people, and addresses per contact" | [S: LLMS] |
| E44 | Gift cards: customers can "buy and send gift cards" from portal | [S: T05] |
| E45 | Leadboard captures booking attempts in real time, "how far a customer made it through the booking flow"; magic data contact details | [S: T10] |

Things the sources do **not** show (all `[I]` below): exact list of automated notifications,
reminder timings, quiet hours, STOP handling, consent capture, email domain setup, campaign
analytics definitions, every metric formula, rollup design, inbox UI, review-request mechanics,
referral mechanics, postcard vendor.

### 0.3 Sub-modules inside 08
| Code | Sub-module | Plan gate (Fieldd reference) | Our proposed gate |
|---|---|---|---|
| 8A | Notification platform & Master Catalog (transactional + operational) | Start ("automated SMS and email job updates", "on-my-way tracking") [S: LLMS] | All tiers incl. starter |
| 8B | Campaigns: email / SMS / postcard / push broadcasts | SMS marketing = Start; email campaigns & push marketing = Scale [S: LLMS] | SMS + email on starter (email is cheap); postcards pay-per-piece; push needs customer app |
| 8C | Engage (drip / automation engine) | Scale [S: LLMS] | Starter gets 3 live drips; unlimited higher [I] |
| 8D | Compliance (consent, STOP/HELP, 10DLC, CAN-SPAM, quiet hours) | n/a (platform duty) | All tiers, non-optional |
| 8E | Communication: unified inbox, live chat, team group chats, two-way SMS | "Live chat … group chats with the team" (tier not stated) [S: LLMS] | All tiers |
| 8F | Reviews (review booster) & referrals | Review booster = Start; referral marketing = Scale [S: LLMS] | Reviews all tiers; referrals mid tier [I] |
| 8G | Dashboard, reporting, leaderboard, goals, forecasting, exports | Custom analytics, heatmaps = Pro [S: LLMS] | Core tiles all tiers; custom views mid; heatmaps/custom analytics top [I] |

---

## 1. Summary

The platform talks to three audiences — **customers/leads**, **team members**, and the
**business owner/admins** — and Agent 08 owns every one of those conversations plus the numbers the
owner reads. Concretely:

1. A **single outbound messaging pipeline** (one code path) used by every module: system
   notifications (booking confirmations, reminders, on-my-way, receipts, invoice/quote emails,
   timesheet decisions, worker invites…), marketing broadcasts, and drip steps. The pipeline
   enforces consent, quiet hours (in the *recipient's* time zone), frequency caps, idempotency,
   provider failover, and records delivery receipts. [I]
2. A **Master Notification Catalog** (Section 5.1) — the authoritative list of every automated
   SMS / email / push / in-app message in the product, its trigger event, recipient, timing,
   variables and editability. Other agents reference catalog IDs instead of defining messages. [S: BRIEF]
3. **Campaigns** (email, SMS, postcard, push) with templates, audience targeting and in-app
   analytics, so a small business never needs a separate email tool. [S: T10]
4. **Engage**, a durable **drip/automation engine** — triggers (actions like "new customer",
   abandoned booking, dates/holidays), steps (email, delay, SMS with booking link), launch, and
   metrics: *in drip*, *completed*, *revenue generated*. [S: T12]
5. **Compliance by construction** — TCPA consent records, STOP/HELP, 10DLC/toll-free registration,
   CAN-SPAM one-click unsubscribe, recipient-local quiet hours. Non-compliant sends are impossible,
   not just discouraged. [I]
6. A **unified inbox** (two-way SMS, email replies, web live chat, customer-app chat) and **team
   group chats**. [S: LLMS]
7. **Reviews & referrals** — post-job rating SMS ("Google review booster") and referral codes that
   work online and in the customer app. [S: LLMS]
8. **Dashboard & reporting** — customizable tiles (revenue trends, top services, best customers &
   workers, service areas, booking sources), downloadable charts/CSV, custom views, forecasting,
   MVP view, goals with progress, leaderboard; every metric precisely defined and served from
   incrementally refreshed rollups so it stays fast. [S: T09, LLMS]

---

## 2. Roles

Role names follow the team-wide set (owner/admin/manager/foreman/worker/customer/system); the
authoritative RBAC matrix belongs to Agent 09. The rows below are the permissions **this module
asks Agent 09 to define**. Defaults are `[I]`.

| Capability (permission key) | Owner | Admin | Manager | Foreman | Worker | Customer | System |
|---|---|---|---|---|---|---|---|
| `comms.notifications.configure` (toggle, timing, edit templates) | Y | Y | N | N | N | – | – |
| `comms.templates.edit` (email/SMS template library) | Y | Y | Y | N | N | – | – |
| `marketing.campaign.create` / edit drafts | Y | Y | Y | N | N | – | – |
| `marketing.campaign.send` (launch/schedule broadcast) | Y | Y | opt-in | N | N | – | – |
| `marketing.drip.manage` (create/launch/pause) | Y | Y | opt-in | N | N | – | – |
| `marketing.analytics.view` | Y | Y | Y | N | N | – | – |
| `compliance.sender.manage` (10DLC, numbers, email domain) | Y | Y | N | N | N | – | – |
| `compliance.consent.override` (record consent on behalf of customer, with attestation) | Y | Y | Y | N | N | – | – |
| `inbox.view_all` (all customer conversations) | Y | Y | Y | N | N | – | – |
| `inbox.view_assigned` (conversations of own jobs / assigned to me) | Y | Y | Y | Y | Y (if `mobile.message_client`) | – | – |
| `inbox.reply` | Y | Y | Y | Y | Y (own jobs) | own thread | – |
| `chat.team.create_group` | Y | Y | Y | Y | N | – | – |
| `chat.team.participate` | Y | Y | Y | Y | Y | – | – |
| `reviews.view` / `reviews.respond` | Y/Y | Y/Y | Y/N | N | own reviews view | – | – |
| `referrals.manage` | Y | Y | N | N | N | own code | – |
| `reports.view_financial` (revenue, payouts, margins) | Y | Y | opt-in | N | N | – | – |
| `reports.view_operational` (jobs, utilization, leaderboard) | Y | Y | Y | Y (own team) | own stats only | – | – |
| `reports.export` (CSV/PNG) | Y | Y | opt-in | N | N | – | – |
| `dashboard.customize` (personal layout) | Y | Y | Y | Y | N | – | – |
| `dashboard.customize_default` (business default layout) | Y | Y | N | N | N | – | – |
| `goals.manage` | Y | Y | Y | N | N | – | – |
| `leaderboard.view` | Y | Y | Y | Y | Y (if enabled) | – | – |
| Receive operational alerts (A-series) | Y | Y | configurable | configurable | – | – | – |
| Send pipeline / timers / rollups | – | – | – | – | – | – | Y |

Recipient roles in the catalog: **Customer** (the contact person on the job / account),
**Gift recipient**, **Lead** (prospect without a booking), **Assigned worker(s)**, **Worker
(self)**, **Approvers** (owner/admin/manager with relevant permission), **Owner**.

Notes:
- Workers messaging clients from the worker app [S: T02] go **through the business number** so the
  worker's personal phone is never exposed (masked messaging) [I].
- Customers interact only via message channels, preference center, rating page, live-chat widget
  and customer app; they never see analytics.

---

## 3. Screens

Navigation labels are `[I]` unless cited. Fieldd shows a "marketing tab" with a "drip window"
[S: T12], "Settings → Communication → Email" [S: T04], a "dashboard" with tiles and a "reporting
tab" [S: T09], a "leaderboard" [S: T09], and "Live chat" [S: LLMS]. Our IA:

```
Dashboard                 (home; tiles, goals, reviews, photos, team availability, active now)
Reports                   (Overview · Revenue · Jobs · Services · Customers · Team/Leaderboard ·
                           Areas · Sources · Marketing · Reviews · Custom views)
Inbox                     (Customers · Team chats · Unassigned · Mine)
Marketing                 (Campaigns · Engage (drips) · Templates · Reviews · Referrals · Audiences · Analytics)
Settings → Communication  (Notifications · Email · SMS · Push · Sender & compliance · Quiet hours · Brand kit link)
```

Global "New" menu (Fieldd uses New → X pattern [S: T01, T04, T12, T13, T14, T15]) gains:
`New → Campaign`, `New → Drip` [S: T12], `New → Message`, `New → Goal` [I].

### 3.1 Dashboard (home)
Location: first nav item; default landing for owner/admin/manager. [S: T09 "Head back to your dashboard"]

Layout regions (desktop ≥1200px, 12-column grid) [I]:
1. **Header bar**: business name, date-range picker (Today / This week (default, matches T17's
   current-week default [S: T17]) / This month / Last 30 days / Quarter / YTD / Custom), compare-to
   toggle (previous period / same period last year), location/zone filter (multi-business hub on
   top tier [S: LLMS]), "Customize" button, "as of hh:mm" freshness stamp.
2. **Goals strip** (top): up to 4 goal cards with progress bar, % complete, pace indicator
   (ahead/on track/behind), days left. [S: LLMS "business goals with progress tracking"]
3. **KPI row**: 4–6 stat tiles (Revenue, Jobs completed, Average ticket, New customers, Rebook
   rate, Avg rating), each with delta vs comparison period and sparkline.
4. **Tile grid**: draggable/resizable tiles from the Tile Catalog (Section 5.11). Default set:
   Revenue trend, Upcoming schedule load, Top services, Best customers, Top workers (MVP),
   Service areas, Booking sources [S: T05, T09], Recent reviews [S: LLMS], Recent photos
   [S: LLMS], Team availability [S: LLMS], Notifications feed [S: LLMS], Active now [S: LLMS].
5. **Right rail (collapsible)**: "Active now" (customers on booking site / customer app,
   team members clocked in / on the app) and notifications feed. [S: LLMS]

Tile anatomy: title, info "i" (opens metric definition from Section 5.10 — every number is
explainable), period label, chart, footer link "View report", kebab menu (Download PNG, Download
CSV, Remove, Resize, Duplicate with different filter). [S: T09 download icon] [I details]

States:
- Loading: skeletons sized to final tile dimensions (no layout shift); tiles load independently.
- Empty (new business): each tile shows a helpful zero-state ("No completed jobs yet — book your
  first job") with CTA; goals strip shows "Set your first goal".
- Partial/stale: if a rollup is older than its SLA, show "Updating…" chip and the as-of time.
- Error: tile-level error with retry; never a page-level crash.
- Permissions: financial tiles hidden (not zeroed) for roles without `reports.view_financial`.

Mobile (<768px): single column, KPI row becomes horizontal swipe carousel, tiles collapse to
summary + "expand", customize mode disabled (layout edits on desktop/tablet; mobile uses the same
saved order). Worker app shows a personal mini-dashboard: my jobs this week, my rating, my rank
(if leaderboard enabled), my hours (from timesheets). [I]

Customize mode: drag handles appear; "Add tile" opens catalog drawer with search and categories;
each tile has per-tile settings (metric, grouping, filter, chart type, period override). Layout
saved per user; "Save as business default" with `dashboard.customize_default`. Reset to default.
[S: T09 "easy to read and customize"] [I details]

### 3.2 Reports (reporting tab)
[S: T09 "Inside the reporting tab, all of these graphs and charts can be downloaded"]

Layout: left sub-nav of report pages; top filter bar (date range, compare, group-by
day/week/month, filters: worker, service, category, service area/zone, shop, booking source,
customer tag, job tag, job type (mobile/shop/route), payment method); main canvas with chart +
data table; "Save view" (custom views [S: LLMS]) and "Download" (CSV of table, PNG/SVG of chart,
multi-sheet XLSX optional) [I].

Report pages (each lists metrics from Section 5.10):
| Page | Contents |
|---|---|
| Overview | KPI grid, revenue trend, jobs trend, forecast band [S: LLMS forecasting] |
| Revenue | Net sales by day/week/month; breakdown by service/add-on/travel fee/cancellation fee; discounts; refunds; tax collected; tips; cash vs accrual toggle; payment-method split |
| Jobs | Booked, completed, canceled, no-show, rescheduled counts; lead time; duration accuracy (scheduled vs actual) |
| Services | Top services (revenue, count, margin using cost [S: T06, T07]); add-on attach rate |
| Customers | New vs returning; best customers [S: T09]; LTV; retention cohorts; rebook rate; churned |
| Team | Revenue by worker [S: T17], jobs completed, rating, utilization, hours (from timesheets), Leaderboard and MVP view [S: T09, LLMS] |
| Service areas | Jobs & revenue by zone/shop; travel fees collected; heatmap (top tier) [S: T09, T11, LLMS] |
| Sources | Booking sources [S: T05]; funnel: visits → started → completed; abandoned recovery [S: T10] |
| Marketing | Campaign & drip performance, attributed revenue, cost, ROI [S: T10, T12] |
| Reviews | Review count, average, distribution, by worker, response rate [I] |
| Subscriptions | MRR, active subs, churn, failed payments (data from Agent 06) [I] |
| Custom views | Saved combinations of the above; pinnable to dashboard [S: LLMS custom views] |

MVP view: a card at top of Team page showing the top team member for the period with their key
stats and rank deltas. [S: LLMS "MVP view that shows the top team member"]

States: empty (no data in range → show range suggestions), large range (>2 years at day grain →
auto-switch to week/month grain with notice), export in progress (toast + notification when
ready for big exports).

Mobile: chart above, table becomes card list; downloads deliver via share-sheet.

### 3.3 Leaderboard
[S: T09 "ranking performance based on bookings completed, reviews received, and revenue generated"]

Location: Reports → Team → Leaderboard tab; optional dashboard tile; optional worker-app tab.
Layout: period picker; metric switch (Bookings completed | Reviews received | Revenue generated |
Composite [I]); podium for top 3; ranked table (rank, avatar, name, metric value, delta rank vs
previous period, secondary stats: avg rating, jobs, revenue). Toggle "Show to team" (exposes
worker-app view that hides revenue if owner chooses) [I]. Eligibility filter: active workers with
≥1 completed job in period (configurable minimum) [I].

### 3.4 Goals
[S: LLMS "business goals with progress tracking"]

Goal editor (drawer): Name; Metric (from goal-eligible list: net sales, jobs completed, new
customers, reviews received, average rating, rebook rate, subscription count, MRR); Scope (whole
business / worker / zone / service); Period (week, month, quarter, year, custom; recurring
yes/no); Target (number or money); Visibility (owners only / managers / whole team); Notify on
milestones (50/75/100%) yes/no. Validation: target > 0; money as cents; percent 0–100; period
end > start; max 20 active goals [I].
Goal card: progress bar, value/target, pace marker (expected linear progress for elapsed time),
projected end value (Section 10.13).

### 3.5 Marketing home
Location: "marketing tab" [S: T12]. Tabs: **Campaigns**, **Engage** (the "drip window" [S: T12]),
**Templates**, **Reviews**, **Referrals**, **Audiences**, **Analytics** [I tab names].

Campaigns list: columns name, channel icon(s), status (Draft/Scheduled/Sending/Sent/Paused/
Canceled/Failed), audience size, sent date, delivered %, open % (email), click %, replies (SMS),
bookings, revenue attributed, cost. Filters: channel, status, date. Row actions: open, duplicate,
pause/cancel (if scheduled/sending), archive. Empty state: template gallery with "Start from a
template" and "Build with AI" [S: LLMS "AI marketing builder"].

Engage list ("drip window"): columns label [S: T12 "label the campaign"], trigger, status
(Draft/Live/Paused/Archived), **In drip**, **Completed**, **Revenue generated** [S: T12], exits,
conversion %, last edited. Row click → drip detail (3.8).

Global header: monthly usage meter (SMS segments, emails, postcards vs plan allowance and
budget cap), sender status badge (e.g. "SMS: registration pending") — prevents surprises [I].

### 3.6 New campaign wizard (email / SMS / postcard / push)
[S: T10 "email, SMS, or postcard campaigns … customizable templates and targeting options"]
Push campaigns require the branded customer app [S: LLMS push marketing on Scale].

Steps (stepper, can jump back; autosave draft every 2s idle) [I]:
1. **Channel**: Email | SMS | Postcard | Push | Multi-channel (email + SMS fallback).
2. **Audience** (3.7): saved audience or build inline; live count with breakdown "eligible /
   no consent / unsubscribed / no address / quiet-hour deferred".
3. **Content**:
   - Email: subject (required, ≤150 chars, warn >60), preheader (≤150), from name (default
     business name), reply-to (default inbox routing address), body via email editor (3.9).
   - SMS: body (3.10), with automatic business-name prefix and "Reply STOP to opt out" footer
     appended where required (first message to a recipient in 30 days, or always for marketing,
     configurable minimum) [I], segment counter, cost estimate, MMS image (optional, ≤ 500 KB
     after compression, jpg/png/gif) [I].
   - Postcard: size (4x6, 6x9, 6x11), front/back designer, QR code to booking page/short link
     [S: T20 postcard QR → lead board; LLMS QR booking options], return address, mail class.
   - Push: title (≤50), body (≤150), deep link (booking, service, promo), image optional.
   - Variables picker (Section 5.3) and offer picker (attach discount code from Agent 06;
     creates tracked link) [I].
4. **Schedule**: Send now (ASAP) | Schedule at date/time (business TZ shown) | "Send at
   recipient's local time" | Smart window (e.g., 10:00–12:00 local) [I]; quiet hours shown and
   enforced; throttle (e.g., max N/minute — needed for 10DLC throughput) auto-computed.
5. **Review**: summary, compliance checklist (all must be green to launch): sender verified;
   unsubscribe present (email); STOP language (SMS); physical address in footer (email);
   consent coverage; estimated cost vs budget cap; test-send button (to self, renders with a
   chosen sample customer). Launch button requires `marketing.campaign.send`.

Validation errors block "Launch" but never lose content.

### 3.7 Audience builder
Rule builder with AND/OR groups [I]. Fields (all sourced from other modules' read models):
- Customer: tags [S: T18], created date, source [S: T05], zone/service area [S: T11], city/ZIP,
  customer type (residential/business [S: LLMS]), has app installed, has subscription (06),
  lifetime spend, # jobs, last job date ("haven't booked in a while" [S: T10]), next job date,
  last service type, asset attributes (vehicle make/year, last service date [S: LLMS vehicles]),
  average rating given, referral status.
- Lead: lead stage (07), abandoned booking step reached [S: T10], lead source (postcard QR,
  form) [S: T20].
- Engagement: opened/clicked campaign X, received drip Y, replied, unsubscribed.
- Consent (implicit filter, cannot be removed): channel-eligible only.
Live count updates debounced 400ms; "Preview 20 recipients"; save as named Audience (dynamic —
re-evaluated at send time) or Snapshot (frozen list) [I]. CSV upload of ad-hoc list allowed only
for email/postcard; for SMS each uploaded number must carry a consent attestation (Section 5.6).

### 3.8 Engage (drip) builder — `Marketing → New → Drip` [S: T12]
Step 0 — **Template gallery**: categories Holidays, Announcements, Reminders, Welcome, Win-back,
Abandoned booking, Review follow-up, Rebook reminder, Subscription upsell [S: T12 lists holidays,
announcements, reminders "and more"; rest I]. "Start from scratch" and "Build with AI" [S: LLMS].
Each template preconfigures trigger + steps + copy; "get a campaign going in less than a minute"
[S: T12].

Step 1 — **Trigger** [S: T12 "selecting a trigger … select action and then choose new customer"]:
| Trigger type | Options |
|---|---|
| Action (event) | New customer [S: T12]; Abandoned booking [S: T12, T10]; New lead; Job completed; Job canceled; Quote sent but not accepted; Invoice overdue; First booking; Subscription started/canceled; Review received (rating ≥ / ≤ n); Tag added; Customer app installed; Referral signed up [I except cited] |
| Date | Fixed calendar date (holiday picker incl. moveable holidays) [S: T12 holidays]; relative date field (N days before/after: customer birthday, last job date, asset last service date, subscription renewal date) [I] |
| Segment entry | When a customer starts matching an audience (e.g., "no booking in 90 days") [S: T10 "haven't booked in a while"] [I mechanism] |
| Manual / API | Add contacts manually, from list, or via API/MCP [I] |
Trigger filters: optional audience conditions (e.g., only zone = Austin). Enrollment settings:
re-entry policy, max concurrent, exit conditions (Section 5.8).

Step 2 — **Steps canvas** (vertical flow) [S: T12]:
- `Email` — subject with dynamic fields, body via brand-aware editor [S: T12].
- `SMS` — body with dynamic fields + booking link [S: T12].
- `Delay` — N minutes/hours/days [S: T12 "3-day delay"]; option "then wait until next send
  window".
- `Push` (customer app) [I, LLMS push marketing].
- `Wait until` — date/time or event ("until they book or 7 days") [I].
- `Branch (If/Else)` — condition on contact or engagement [I — better-than].
- `Action` — add/remove tag, create task, notify team member, update lead stage, issue discount
  code [I].
- `Exit` — explicit end.
Each step card shows estimated send time for a sample contact, channel cost estimate, and
compliance warnings inline.

Step 3 — **Review & launch** [S: T12]: Label (required, unique per business, ≤80 chars) [S: T12];
"When should messages send": ASAP [S: T12] | within business send window | specific local time of
day; apply to existing matching contacts? (for segment/date triggers: "Also enroll the N contacts
who already match" — default No) [I]; goal (exit when contact books a job — default ON for
promotional drips) [I]; Launch.

Drip detail page: header with status and Pause/Resume/Archive; **metric cards: In drip,
Completed, Revenue generated** [S: T12] plus Exited (by reason), Conversion rate, Sent/Delivered/
Clicked per step; per-step funnel; enrollment list (searchable, with per-contact timeline and
"Remove from drip"); version history (E12 edits) [S: T12].

### 3.9 Brand-aware email editor
[S: T12 "Field will automatically use my brand logo and colors"; E10]
Block-based editor (no raw HTML required): blocks = header (logo), heading, text, image, button
(booking link / custom URL), service card (pulls name, image, price from catalog — Agent 02),
coupon block (code from Agent 06), review stars, divider, spacer, social links, footer (locked:
business address + unsubscribe link + preference link). Theme derived from Brand Kit (logo,
primary/secondary colors, font family mapped to email-safe stacks, button radius) — Brand Kit is
the same source used by the booking portal style settings (Agent 01, [S: T05 style settings]).
Template starters: Welcome [S: T12], Promotion, Holiday, Announcement, Reminder, Newsletter,
Review request, Win-back [I]. Preview desktop/mobile/dark mode; send test; accessibility checker
(alt text, contrast ratio ≥ 4.5:1, link text). Output: responsive table-based HTML + plain-text
alternative generated automatically. Autosave; undo/redo.

### 3.10 SMS composer
Body textarea with: variable chips, booking-link insert (short link on business's branded
domain) [S: T12 booking link], emoji picker with warning ("switches to Unicode — 70 chars per
segment"), live **segment counter** "143/160 · 1 segment · GSM-7" (Section 10.9), estimated cost
for audience, opt-out footer preview, MMS attach. Inline linting: shortened public URL
shorteners (bit.ly etc.) blocked (carriers filter them) [I]; ALL CAPS / spam-trigger warnings.

### 3.11 Settings → Communication
[S: T04 "Settings, find communication, and click email"]

Sub-pages:
1. **Notifications** (the catalog UI): grouped by audience (Customer · Team · Owner/Admin) then
   by area (Booking, Day-of-service, Quotes & invoices, Payments, Subscriptions, Gift cards,
   Account, Timesheets, Team). Row = catalog entry: name, channels with toggles (Email / SMS /
   Push / In-app), timing control where applicable (e.g., reminder offsets), "Edit template"
   per channel, "Send test", "Reset to default". Locked entries (legally required or
   security) show a lock icon with reason.
2. **Email**: appearance of quotes & invoices and their emails [S: T04]; sender name;
   reply-to; footer address; logo; custom sending domain setup (Section 4.15).
3. **SMS**: business number(s), registration status (10DLC brand/campaign or toll-free
   verification), keyword auto-replies (STOP/HELP/START text), signature, "message prefix with
   business name" toggle, MMS enable.
4. **Push**: customer app / worker app push settings (requires app) [I].
5. **Quiet hours & limits**: default window per class (Section 5.5), per-day frequency cap,
   holiday blackout dates.
6. **Consent & privacy**: consent checkbox text used on booking site (versioned), double
   opt-in toggle for SMS marketing, preference-center branding.
7. **Usage & billing**: SMS segments / emails / postcards consumed, cost, monthly budget cap
   with alert thresholds (50/80/100%) and hard stop option.

### 3.12 Inbox (unified communication)
[S: LLMS "all conversations rebuilt in one place, plus group chats with the team"]

Three-pane layout (desktop) [I]:
- **Left**: folders — Unassigned, Mine, All open, Snoozed, Closed; Team chats (group + direct);
  channel filter chips (SMS, Email, Chat, App); search (full-text across messages, names, phones).
- **Middle**: conversation list — avatar, name (or phone if unknown), last message preview,
  channel icon, unread badge, SLA timer ("waiting 12m"), assignee avatar, job chip if linked.
- **Right**: thread — merged timeline across channels for this contact (SMS, email, chat,
  automated notifications shown as muted system bubbles so staff see what the customer
  already received), composer with channel switcher (defaults to channel of last inbound),
  templates/snippets ("/" commands), attach photo/file, insert booking link, insert payment
  link (Agent 05), internal note (yellow, never sent), assign, snooze, close.
- **Context panel** (toggle): customer card (Agent 07), upcoming/last jobs (03/04), open
  invoices (04), consent status per channel with source, tags.

States: empty inbox ("All caught up"), no SMS number yet (CTA to set up sender), consent
missing (composer disabled for marketing intent, allowed for reply-within-conversation since
customer initiated [I — counsel]), delivery failure bubble with reason and retry.
Mobile/worker app: list + thread only; workers see only threads for their assigned jobs
[S: T02 "message the client"].

Team chats: create group (name, members), direct messages, @mentions, job-linked threads
("Job #1234 chat"), read receipts, push to worker app. Customers never see team chats. [S: LLMS]

### 3.13 Reviews
Location: Marketing → Reviews and dashboard "all reviews" tile [S: LLMS].
Review list: rating, comment, customer, job, worker(s), date, channel (internal rating / Google
click-through), response status. Filters: rating, worker, date, has comment. Actions: reply
privately (via inbox), mark resolved, feature on booking site (with customer consent) [I].
Review booster settings [S: LLMS "Google review booster"]: enable; Google Business Profile
review URL (or connect profile); send delay after job completion (default 2h) [I]; channel
(SMS default, email fallback); minimum days between requests per customer (default 90) [I];
"ask everyone for public review" (default ON, compliant mode — Section 5.9); reminder once after
48h if no rating [I].

Customer-facing rating page (hosted on the business's booking subdomain, Agent 01): 1–5 stars,
optional comment, tip prompt link if tips enabled (Agent 05) [I], then "Share on Google" button.

### 3.14 Referrals
[S: LLMS "referral codes tracked in one place that work online and in the customer app"]
Program settings: reward for referrer (account credit / discount code / gift card from Agent 06),
reward for friend (discount on first booking), qualifying event (friend's first job completed and
paid — default), caps (max rewards per referrer per year), code format (FIRSTNAME + 4 chars).
Referral list: referrer, friend, code, status (Invited/Signed up/Booked/Qualified/Rewarded/
Rejected), dates, reward issued. Share surfaces: customer app, post-job email, preference
center. Fraud rules in Section 5.9.

### 3.15 Notification center (in-app bell)
For CRM users and worker app [S: LLMS dashboard "notifications"]. Items from A-series and
W-series catalog entries with `in_app` channel; grouped by day; unread count; mark all read;
deep links; per-user mute by category. Retention 90 days [I].

### 3.16 Customer preference center & unsubscribe pages
Hosted on the business's booking subdomain (Agent 01). One-click unsubscribe landing (no login)
via signed token; preference center lists categories (Appointment updates — cannot disable
while a job is upcoming, only switch channel; Receipts & invoices — email required; Promotions
by email; Promotions by SMS; Review requests; Newsletter). Shows business name/address. Fully
accessible, works without JavaScript. [I]

### 3.17 Sender & compliance onboarding wizard
Steps: (1) Business identity (legal name, EIN or "I'm a sole proprietor", address, website,
contact) — mostly reused from business profile; (2) Choose SMS route (Toll-free verified
recommended for starter / 10DLC local number) with plain-language explanation and timeline;
(3) Use-case & sample messages (auto-generated from catalog + business's templates); (4) Opt-in
description & screenshot URL (auto-points to booking page consent step); (5) Submit; status
tracker. Email: (1) use platform shared domain immediately; (2) optional custom domain with DNS
records (SPF include, DKIM CNAMEs, DMARC, return-path) and live verification. Until SMS
registration is approved, system messages fall back to email and the UI says so. [I]

---

## 4. Workflows

Conventions: "Pipeline" = the Send Pipeline (Section 10.1). Every send writes an
`OutboundMessage` row before calling a provider. Event names per Section 9.

### 4.1 Generic transactional notification (every catalog entry)
1. Source module emits domain event (e.g., `booking.appointment.created`) via the transactional
   outbox (event is committed atomically with the business change — Agent 10 contract).
2. Notification Router consumes the event; looks up catalog entries whose `trigger_event`
   matches; for each entry loads the business's `NotificationSetting` (enabled? channels?
   timing offsets? template version?).
3. If entry is **immediate**: creates a `NotificationIntent` with idempotency key
   `{catalogId}:{subjectId}:{eventId}:{recipientId}:{channel}` and hands to Pipeline.
   If **scheduled** (reminders, review requests, dunning): writes/updates a `ScheduledNotification`
   (durable timer) with `due_at` and `subject_version` (Section 10.3).
4. Pipeline per recipient/channel: resolve endpoint → consent/suppression check → quiet-hours
   check (may defer) → frequency cap (marketing only) → render template with a frozen variable
   snapshot → persist `OutboundMessage(status=queued)` → provider send (with provider-side
   idempotency key where supported) → status `sent` → async delivery webhook → `delivered` /
   `failed` / `bounced`.
5. Fallback: if SMS fails permanently (invalid number, landline, carrier block) and catalog entry
   allows `fallback: email`, enqueue email variant once. [I]
6. Emits `comms.message.sent` / `comms.message.delivered` / `comms.message.failed`; appears on
   the customer timeline (Agent 07) and the job activity log (Agent 04).

### 4.2 Booking confirmation (online or CRM)
[S: T15 "add the job to your calendar, notify your assigned team members, and send reminders
directly to the customer"; T19 "sends reminders and updates directly to the customer"]
1. `booking.appointment.created` (payload: job_id, source=online|crm|app|api|recurring,
   customer contact ids, start/end UTC, service address tz, workers, notify_customer flag).
2. If source = `crm` and the booking form's "Notify customer" toggle is OFF → skip customer
   notifications (still schedule nothing) [I — CRM toggle requested from Agent 03].
3. Customer: N-B01 Booking confirmation (email + SMS) immediately; includes date/time in
   **service-address tz**, services, address, worker first name (if assigned & business allows),
   total/deposit info, manage link (reschedule/cancel per policy, Agent 01), add-to-calendar ICS.
4. Assigned workers: W-02 Job assigned (push + in-app; SMS fallback if no app installed).
5. Owner/admin: A-01 New online booking (in-app + optional email/push) only for source=online.
6. Schedule reminders: N-B04 (default 24h before) and N-B05 (default 2h before; off by default
   for starter) as `ScheduledNotification`s keyed to job_id + subject_version.
7. If recurring series (Agent 03): confirmation summarizes the series ("every 2 weeks on Thu,
   next 3 dates…"); each generated occurrence does **not** send its own confirmation by default
   (prevents spam), only reminders [I].
8. If job time is < reminder offset away (e.g., booked for 1h from now) → skip reminders whose
   due_at is in the past; do not send immediately (confirmation already covers it).

### 4.3 Reschedule (including drag-to-reschedule) and reassignment
[S: T08 "drag … Field will update your schedule and notify the necessary people automatically"]
1. `booking.appointment.rescheduled` {job_id, old_start, new_start, old_workers, new_workers,
   actor, subject_version, notify_customer (default true), reason}.
2. **Debounce**: changes to the same job within a 2-minute window coalesce; only the final state
   is notified (drag-drop users often adjust twice) [I]. Implementation: notification intent with
   `not_before = now + 120s`, replaced on each new version; sent with the latest snapshot.
3. If only the worker changed (same time): customer gets N-B07 "Your pro has changed" only if the
   business shows worker names (setting) — else nothing; old worker gets W-03 unassigned; new
   worker gets W-02 assigned.
4. If time changed: customer N-B06 Rescheduled (old → new time, manage link); workers W-04.
5. Reminders: recompute `due_at`; bump `subject_version`; old timers become no-ops (version
   mismatch) (Section 10.3).
6. Past-dated reschedule (allowed by Fieldd [S: LLMS "schedule or reschedule jobs in the past"]):
   **never** notify customer for jobs whose new start is in the past [I].
7. If the reschedule was initiated by the customer via manage link → customer gets
   "Reschedule confirmed" variant; business gets A-02b.

### 4.4 Cancellation
1. `booking.appointment.canceled` {actor: customer|business|system, reason, fee_cents,
   refund_cents, feedback (from cancellation flow [S: T05])}.
2. Customer: N-B08 (canceled by business) or N-B09 (cancellation confirmation, includes fee/
   refund and, if enabled, "Rebook" CTA — [S: T05 "prompt them to reschedule"]).
3. Workers: W-04 canceled. Owner: A-02 (if customer-initiated).
4. Cancel all pending `ScheduledNotification`s for job (reminders, on-my-way not yet sent,
   review request).
5. Drips with exit condition "job canceled" or enrollments tied to this job evaluate exit.
6. Cancellation of a whole recurring series sends **one** message, not one per occurrence [I].

### 4.5 Day of service: on-my-way, ETA, arrived, complete
[S: LLMS "on-my-way tracking"; T11 "live tracking … real-time updates on your arrival"]
1. Worker swipes "On my way" in worker app (or route start [S: T02]) → `job.job.en_route`
   {job_id, worker_id, eta_utc (from routing provider), tracking_token}.
2. Customer N-D01 On my way: SMS (primary) with ETA in local time ("arriving ~10:40") and live
   tracking link (`/t/{token}` on booking subdomain, expires 2h after job completion).
   Quiet-hours class: **service-critical** (bypass) (Section 5.5).
3. ETA drift: Agent 03/04 emit `schedule.eta.updated`; if new ETA later than promised by
   ≥ 15 min (configurable) → N-D02 Running late (max 2 per job) [I].
4. Route jobs [S: T02]: when route starts, customers on that route get N-D04 "Your service is
   today, you're stop #k, estimated window …" (optional) [I]; each subsequent stop gets N-D01
   when the worker departs the previous stop.
5. `job.job.started` → optional N-D03 "Arrived/started" (off by default).
6. `job.job.completed` → N-D05 Job complete summary (email; optional SMS) with before/after
   photos if enabled [S: T06 photos] [I message] and payment link if balance due; schedule
   review request (4.7); stop tracking link.

### 4.6 Quote & invoice messaging
[S: T04 "control over the emails that get sent … when a quote or invoice is sent"; "reminder
settings"; "contact info to notify the customer"; "book online button"]
1. `quote.quote.sent` {job_id (same ID as job [S: T04]), doc_url, recipients (contacts chosen
   on quote), channel prefs} → N-Q01 Quote email (+ SMS link optional) with View & Accept and
   Book online buttons.
2. Schedule N-Q02 quote reminders (default +3d, +7d; stop on viewed? no — stop on
   accepted/declined/expired) and N-Q03 expiring-soon (2 days before expiry, if expiry set).
3. `quote.quote.viewed` → A-04v (optional owner ping "Quote viewed") [I].
4. `quote.quote.accepted` → N-Q04 acceptance confirmation to customer; A-04 to owner; cancel
   quote reminders; if booked, flow 4.2 takes over.
5. `invoice.invoice.sent` → N-I01 Invoice email/SMS with Pay link (Agent 05). Schedule
   N-I02 reminders per settings (defaults: on due date, +3d, +7d, +14d overdue; max 4).
6. `invoice.invoice.paid` / `payment.payment.captured` → N-P01 Receipt; cancel invoice reminders.
7. Reminder rendering always fetches **current** balance at send time (partial payments) —
   never a stale amount (variable snapshot taken at render, not at schedule) [I].

### 4.7 Review request (Google review booster)
[S: LLMS "Google review booster"; T09 profiles carry "ratings"; T09 leaderboard "reviews received"]
1. `job.job.completed` → if booster enabled and customer not asked within the cooldown (90d
   default) and job not flagged "do not request" → schedule N-R01 at completed_at + delay (2h).
2. At due time re-check: job still completed (not reopened/canceled), customer not opted out
   of review requests, no open dispute/complaint on job (Agent 05) [I].
3. Send SMS with rating link (`/r/{token}`); fallback email.
4. Customer rates 1–5 (+ comment) → `review.review.received` (rating attached to job, to each
   worker on job, to customer profile ratings [S: T09]).
5. Compliant mode (default): thank-you page shows "Share on Google" to **all** raters and a
   private-feedback box; owner alerted for rating ≤ 3 (A-08). "Gated" routing (only 4–5 see
   Google link) is **not offered** (Section 5.9 rationale).
6. Track click on Google link (`review.public_link.clicked`); cannot confirm a Google review was
   actually posted unless Google Business Profile API is connected — if connected, poll new
   reviews and fuzzy-match reviewer name to customer (`review.public_review.matched`) [I].
7. No rating after 48h → one reminder N-R02; then expire.

### 4.8 Worker invite & team notifications
[S: T13 "automatically send an email inviting the worker to log in"; "After they log in, you'll
see them appear in your CRM"]
1. `team.worker.invited` {worker_id, email, phone, invite_token, expires_at (7d [I])} → W-01
   invite email (+ SMS with app download link if phone present and worker consented by being
   added by employer — transactional/operational class).
2. Resend action from worker profile regenerates token (old token invalid).
3. `team.worker.activated` → A-11 "Alex joined your team" to owner.
4. Job assignment → W-02; changes → W-03/W-04/W-05; daily agenda W-06 (6:00 local, optional).

### 4.9 Timesheet notifications
[S: T03 "worker will receive a notification in the worker app … approved … or … rejected";
statuses paid/approved/rejected; time sheet "sent over to the CRM for approval"]
1. `timesheet.timesheet.submitted` → A-09 to approvers (in-app, digest optional).
2. `timesheet.timesheet.approved` → W-07 push + in-app (worker app).
3. `timesheet.timesheet.rejected` {reason} → W-08 push + in-app with reason and "Edit" deep link
   (only if worker has edit permission [S: T03]).
4. `timesheet.timesheet.paid` → W-09 (off by default) [I].
5. `timesheet.punch.open_too_long` (clocked in > 12h or past midnight local) → W-13 reminder to
   clock out; A-19 to approvers next morning [I].

### 4.10 Broadcast campaign (email / SMS / push)
[S: T10]
1. User completes wizard (3.6); `marketing.campaign.scheduled`.
2. At send time T: **snapshot audience** (dynamic audience evaluated once at T; record
   `CampaignRecipient` rows with eligibility verdicts). Contacts added after T are not included.
3. Partition recipients by channel and by recipient-local send window (if "local time" mode).
4. Throttle per sender capacity (10DLC throughput tier / toll-free rate / ESP rate) — token
   bucket per sender (Section 10.8).
5. Each recipient → Pipeline with idempotency key `campaign:{id}:{recipient}:{channel}`.
6. Progress visible live (sent/total); Pause stops dequeuing (in-flight completes); Cancel marks
   remaining `canceled`.
7. Analytics events accumulate (delivered, opened, clicked, replied, unsubscribed, bounced,
   complained, booked, revenue) → campaign rollups.
8. Completion → `marketing.campaign.completed`; owner in-app summary after 24h ("2,140 sent ·
   38 bookings · $4,210 attributed").

### 4.11 Postcard campaign
[S: T10 postcard campaigns; T20 "once a customer scans the QR code on your postcard their
information will show up here in the lead board"]
1. Audience: customers/leads with verified postal address, or **address list** (e.g., neighbors
   around recent jobs — "neighbor marketing" radius around completed jobs in last N days) [I].
2. Address verification (CASS/NCOA via print vendor) → undeliverable excluded with reason.
3. Design front/back; QR code encodes a **per-recipient** tracked short link
   (`/q/{token}` → booking page with lead context) so a scan identifies the household [I].
4. Proof approval (PDF proof rendered by vendor) — required before order; cost shown per piece
   + postage; charge business's platform balance/card before submission (Agent 05 billing).
5. Submit to print API; statuses: `submitted → in_production → mailed → in_transit →
   delivered (estimated) / returned`.
6. Scan → `marketing.postcard.scanned` → Agent 07 creates/updates lead with source
   `postcard:{campaignId}` and address [S: T20]; booking within attribution window credits the
   campaign.

### 4.12 Engage drip: create → launch → run
[S: T12]
1. `Marketing → New → Drip`; pick template or scratch.
2. Choose trigger (e.g., Action → New customer) and filters.
3. Add steps: Email (subject with `{{customer.first_name}}`), Delay 3 days, SMS with
   `{{links.booking}}` [S: T12].
4. Review: label, send timing (ASAP) [S: T12]; exit goal; re-entry policy.
5. Launch → `DripCampaign.status = live`, `DripVersion v1` frozen (immutable snapshot of steps).
   `marketing.drip.launched`.
6. Trigger event arrives (e.g., `customer.customer.created`) → Enrollment Service evaluates
   eligibility (Section 10.4) → creates `Enrollment(state=active, version=v1, step=0)` with
   unique constraint `(drip_id, contact_id, enrollment_key)`.
7. Step executor runs step 0 (email) via Pipeline (idempotency key `drip:{enrollment}:{step}`),
   then step 1 (delay) writes durable timer `due_at = now + 3d` (adjusted to send window when
   next step is a message), state `waiting`.
8. Timer fires → step 2 (SMS) → Pipeline; no more steps → `completed`;
   `marketing.enrollment.completed`.
9. At any time, exit conditions are evaluated on relevant events (booked a job, unsubscribed,
   deleted, drip paused/archived) → `exited(reason)`.
10. Metrics: In drip = enrollments in {active, waiting}; Completed = completed; Revenue generated
    = attributed revenue (Section 5.7) [S: T12].
11. Editing a live drip [S: T12 "edit or add more steps … at any time"]: publish creates v2. New
    enrollments use v2. In-flight enrollments: default **continue on their version**; owner may
    choose "Move in-flight contacts to new version" — mapping by stable step IDs; contacts on a
    deleted step jump to the next surviving step; contacts past the end complete [I].

### 4.13 Abandoned booking recovery (Engage template)
[S: T12 "if a customer visits my online booking page but doesn't complete the booking process, I
can send them a text a couple of hours later"; T10 "follow up with leads who didn't finish
booking"; "how far a customer made it through the booking flow"]
1. Agent 01 emits `booking.session.contact_captured` when the visitor enters phone/email (with the
   consent checkbox state) and `booking.session.abandoned` after 30 min idle without
   `booking.appointment.created` [I thresholds].
2. Drip trigger "Abandoned booking" fires; eligibility requires **marketing SMS consent** captured
   in that session (the booking-site consent checkbox, Section 5.6) for SMS steps; email steps
   require only that the address was provided in a commercial relationship context (CAN-SPAM
   allows, with unsubscribe) [I — counsel].
3. Step 1: Delay 2h ("a couple of hours later" [S: T12]) → SMS "Hi {{first_name}}, still want
   your {{session.service_name}} on {{session.selected_date}}? Finish here: {{links.resume_booking}}".
   Resume link restores the cart (Agent 01 stores session state 7 days).
4. Exit when `booking.appointment.created` for that contact (any source) → exit reason
   `goal_reached`; attribution credits the drip if booked via the link or within window.
5. Re-entry: once per 14 days per contact [I].

### 4.14 Inbound SMS handling (two-way)
1. Provider webhook → verify signature → persist raw inbound (idempotent on provider message SID).
2. Resolve business by `To` number. Unknown number → drop + alert platform ops.
3. **Keyword check first** (Section 10.6): STOP-family → opt-out; START-family → opt-in;
   HELP-family → auto-reply help text. Keyword-only messages still appear in inbox as system
   events.
4. Resolve contact(s) by `From` within business (E.164 match across all phone numbers of all
   contacts/people [S: LLMS multiple phone numbers & people]). 0 matches → create "Unknown
   sender" conversation + lead suggestion (Agent 07). >1 match → thread attaches to all matched
   contacts' timelines but one conversation keyed by phone (Section 10.7).
5. Append to conversation; if conversation closed → reopen; route assignment (last assignee, or
   assigned worker of the customer's active/next job if within ±4h window and worker has
   messaging permission, else Unassigned) [I].
6. Intent detection (cheap rules, optional AI): "C"/"confirm" reply to reminder → emit
   `comms.reply.confirmation_received` (Agent 03 may mark job confirmed); "R"/"reschedule" →
   reply with manage link [I].
7. Notify: push/in-app to assignee or watchers (A-10); unanswered SLA alert after X minutes
   during business hours [I].
8. Auto-reply outside business hours (optional) — once per 12h per conversation [I].

### 4.15 Sender setup: 10DLC / toll-free / email domain
1. Business opens wizard (3.17). Platform acts as ISV/reseller: creates the business as a
   **brand** under the platform's provider account (secondary customer profile) [I].
2. **Route A — Toll-free verification (recommended default for starter)**: buy TF number,
   submit verification (business info, use case, opt-in flow, sample messages, est. volume).
   Until verified: TF messaging blocked or heavily filtered → system SMS fall back to email.
3. **Route B — 10DLC**: brand registration (Standard brand with EIN; or **Sole Proprietor**
   brand for businesses without EIN — limited to 1 campaign / 1 number / low throughput), brand
   vetting (optional, increases throughput), campaign registration (use case "Mixed" or
   "Customer care + marketing" with sample messages, opt-in description, HELP/STOP), number
   assignment. Typical approval: days to weeks [I].
4. Status webhooks → `compliance.sender.status_changed` → A-15 to owner; UI badges.
5. Email: day 1 on platform shared sending domain (`mail.<platform>.com`, From =
   "Business Name <business-slug@mail.platform>" with Reply-To routed to inbox); optional custom
   domain: generate DKIM keys/CNAMEs, SPF include, return-path CNAME, DMARC recommendation;
   verify DNS every 10 min for 72h; on success switch sending.
6. Warm-up: new custom domains get a daily volume ramp for marketing (transactional unaffected)
   [I].

### 4.16 Unsubscribe / opt-out (email)
1. Every marketing email has `List-Unsubscribe` (mailto + https) and
   `List-Unsubscribe-Post: List-Unsubscribe=One-Click` headers plus a visible footer link [I].
2. One-click POST → suppress marketing email for that address within the business immediately
   (`comms.consent.changed`), regardless of login. Honor ≤ 10 business days is the legal maximum
   — we do it instantly [I].
3. Footer link → preference center (3.16) with "Unsubscribe from all marketing" pre-focused.
4. Transactional emails carry a preference link but no marketing unsubscribe effect on
   transactional delivery.
5. Spam complaint (feedback loop) → treat as unsubscribe from marketing + flag contact.
6. Hard bounce → suppress address platform-wide for that business; soft bounce ×3 in 7d → suppress 30d.

### 4.17 Live chat (booking site / customer app)
[S: LLMS "Live chat: all conversations rebuilt in one place"]
1. Widget (lazy-loaded after booking page interactive — must not hurt widget performance budget,
   BRIEF performance bar) opens; visitor optionally provides name + email/phone (required if
   business offline).
2. Message → conversation with channel `webchat`, linked to booking session id (Agent 01) so
   staff see what the visitor was booking (cart, step).
3. Staff reply in inbox; visitor sees typing indicators; if visitor leaves, transcript + reply
   are delivered by email/SMS (SMS only if consent captured) [I].
4. Business offline hours → "Leave a message"; auto-acknowledgment with expected response time.
5. Logged-in customers (customer account / branded app) get persistent threads tied to their
   profile; app push for staff replies.

### 4.18 Team group chat
[S: LLMS "group chats with the team"]
1. Create group: name, members (workers/managers), optional link to job/route/zone.
2. Messages fan out to members via worker-app push and in-app; respects each member's
   do-not-disturb (off-shift hours from Agent 09 availability) except @urgent by managers [I].
3. Auto-created job threads: when a job has ≥2 assigned workers (split jobs [S: LLMS "split a job
   or its services between team members"]) an optional "Job #id crew" thread is created [I].
4. Membership changes when a worker is deactivated (`team.worker.deactivated` → remove from all
   groups; history retained).

### 4.19 Referral flow
[S: LLMS referral codes]
1. Customer gets code (auto on first completed job or on demand in app/preference center).
2. Friend uses link `/ref/{code}` or enters code at checkout (Agent 01 validates via 08 API;
   Agent 06 applies the friend discount).
3. `booking.appointment.created` with referral_code → Referral status `booked`.
4. Qualifying event (friend's job completed + paid, not refunded within 14d) → `qualified` →
   request Agent 06 to issue reward (credit/discount/gift card) → `rewarded`;
   N-M05 "You earned a reward" to referrer.
5. Fraud checks before qualifying (Section 5.9).

### 4.20 Dashboard, reports, exports, goals
1. Page load requests tiles in parallel; each tile query hits rollup tables keyed by
   (business, bucket) with filters (Section 10.10); response includes `as_of`.
2. Customize: drag/resize → save layout JSON (debounced) → per-user `DashboardLayout`.
3. Download CSV/PNG [S: T09]: small (< 50k rows) streamed synchronously; larger → `ExportJob`
   queued, notification when ready, link valid 7 days, signed URL.
4. Scheduled report email (e.g., Monday 7:00 weekly summary) — A-14 [I].
5. Goals: create → progress recomputed on rollup refresh → milestone notifications (A-13) once
   per milestone per goal period (idempotent key `goal:{id}:{period}:{milestone}`).

### 4.21 Subscription & dunning messages (Agent 06 drives timing; 08 sends)
1. `subscription.subscription.activated` → N-S01 welcome/summary.
2. `subscription.renewal.upcoming` (default 3 days before annual/commitment renewals; monthly
   optional) → N-S02.
3. `subscription.renewal.charged` → N-S03 receipt.
4. `subscription.payment.failed` {attempt n, next_retry_at, update_card_url} → N-S04 (each
   attempt), A-12 owner on final failure.
5. `subscription.subscription.canceled|paused|commitment_ending` → N-S05/N-S06/N-S07.
6. `payment.card.expiring` (30 days before card expiry, card on file used by active
   subscription or upcoming job) → N-P04.

---

## 5. Business rules & math

### 5.1 MASTER NOTIFICATION CATALOG (authoritative)

**How to read.** Every automated message in the product has exactly one catalog ID. Other agents
reference these IDs; they never send messages directly. Columns:

- **Trigger** — consumed event (Section 9) or timer.
- **To** — recipient role. `Cust` = job/account contact(s) selected for notifications (one or
  more people per contact [S: LLMS]); `Payer` = person who paid; `Gift` = gift-card recipient;
  `Lead`; `AsgW` = assigned worker(s); `Wkr` = the worker concerned; `Appr` = users with approval
  permission; `Own` = owner + admins (+ managers if opted in).
- **Ch** — default channels: `E` email, `S` SMS, `P` push (customer app / worker app), `I` in-app
  (CRM bell / worker app feed). Bracketed = optional, off by default. `→E` = fallback to email
  if SMS impossible.
- **Timing** — default; business can change where "Edit" includes `T`.
- **QH** — quiet-hours class (5.5): `CRIT` bypass; `TXN` hold 21:00–08:00 recipient-local
  unless within 3h of the event it concerns; `MKT` only 09:00–20:00 local (stricter than federal
  8–21 to satisfy strict states) [I]; `TEAM` respects worker off-shift/DND.
- **Opt** — opt-out class (5.6): `LOCK` cannot be disabled (security/legal); `TXN` customer may
  switch channel, SMS STOP honored, email continues; `MKT` requires marketing consent;
  `OPS` internal user preference.
- **Edit** — business can: `On` toggle on/off, `C` change channels, `T` change timing, `X` edit
  text (per channel; variables only from the entry's variable set), `—` nothing.
- **VS** — variable set (5.3).
- **Tier** — `All` / `Mid` / `Top` (our proposal; Fieldd references in Src).
- **Src** — evidence.

#### 5.1.1 Customer — booking & schedule

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| N-B01 | Booking confirmation | `booking.appointment.created` (notify_customer=true; not for generated recurring occurrences) | Cust | E+S | Immediate | TXN | TXN | On,C,X | VS-JOB | All | [S: T15, T19] |
| N-B02 | Booking request received (awaiting approval) | `booking.appointment.requested` | Cust | E+S | Immediate | TXN | TXN | On,C,X | VS-JOB | All | [I] (if Agent 01/03 support approval mode) |
| N-B03 | Booking approved / declined | `booking.appointment.approved` / `.declined` | Cust | E+S | Immediate | TXN | TXN | On,C,X | VS-JOB | All | [I] |
| N-B04 | Appointment reminder #1 | Timer: `start − 24h` (range 1h–7d) | Cust | S+E | Scheduled | TXN | TXN | On,C,T,X | VS-JOB | All | [S: T15 "send reminders"] |
| N-B05 | Appointment reminder #2 | Timer: `start − 2h` (range 15m–12h) | Cust | [S] | Scheduled | TXN* | TXN | On,C,T,X | VS-JOB | All | [I] |
| N-B06 | Appointment rescheduled | `booking.appointment.rescheduled` (time changed; new start in future) | Cust | E+S | Debounce 2 min | TXN* | TXN | On,C,X | VS-JOB + `old.*` | All | [S: T08] |
| N-B07 | Your pro changed | `booking.appointment.reassigned` (time same) | Cust | [S] | Debounce 2 min | TXN | TXN | On,C,X | VS-JOB | All | [I] |
| N-B08 | Appointment canceled (by business) | `booking.appointment.canceled` actor≠customer | Cust | E+S | Immediate | TXN* | TXN | On,C,X | VS-JOB + `cancel.*` | All | [S: T08 implied] |
| N-B09 | Cancellation confirmed (+ rebook CTA) | `booking.appointment.canceled` actor=customer | Cust | E | Immediate | TXN | TXN | On,X | VS-JOB + `cancel.*` | All | [S: T05] |
| N-B10 | Recurring plan confirmed / changed | `recurring.series.created` / `.updated` | Cust | E | Immediate | TXN | TXN | On,X | VS-SERIES | All | [S: T14] [I message] |
| N-B11 | Recurring plan ended | `recurring.series.ended` | Cust | [E] | Immediate | TXN | TXN | On,X | VS-SERIES | All | [I] |
| N-B12 | Pre-visit info needed (intake/booking questions incomplete) | Timer `start − 48h` if `job.intake.incomplete` | Cust | E+S | Scheduled | TXN | TXN | On,T,X | VS-JOB | All | [S: T05 intake form] [I msg] |
| N-B13 | Route day notice ("anytime Thursday — you're stop #k") | `route.route.started` | Cust | [S] | Immediate | TXN | TXN | On,X | VS-JOB + `route.*` | All | [S: T02] [I msg] |
| N-B14 | Reschedule request received (customer asked, business must confirm) | `booking.reschedule.requested` | Cust | E | Immediate | TXN | TXN | On,X | VS-JOB | All | [I] |

`TXN*` = treated as CRIT if the job starts within the next 12 hours (a same-day cancel or change
must arrive even late evening) [I].

#### 5.1.2 Customer — day of service

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| N-D01 | On my way + ETA + live tracking link | `job.job.en_route` | Cust | S (+P if app) →E | Immediate | CRIT | TXN | On,C,X | VS-JOB + `eta.*`, `links.tracking` | All | [S: LLMS on-my-way tracking; T11 live tracking] |
| N-D02 | Running late (new ETA) | `schedule.eta.updated` with delay ≥ 15 min vs last promised (max 2/job) | Cust | S (+P) | Immediate | CRIT | TXN | On,T(threshold),X | VS-JOB + `eta.*` | All | [I] |
| N-D03 | Arrived / service started | `job.job.started` | Cust | [S/P] | Immediate | CRIT | TXN | On,C,X | VS-JOB | All | [I] |
| N-D04 | Job complete summary (+ photos, balance due / pay link) | `job.job.completed` | Cust | E [+S] | Immediate (photos: wait ≤ 10 min for uploads) | TXN | TXN | On,C,X | VS-JOB + `photos.*`, `balance.*` | All | [S: LLMS "automated SMS and email job updates"; T06 photos] [I content] |

#### 5.1.3 Customer — quotes, invoices, payments

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| N-Q01 | Quote sent (View/Accept + Book online) | `quote.quote.sent` | Contacts chosen on quote | E [+S link] | Immediate | TXN | TXN | C,X (design via Settings→Communication→Email) | VS-QUOTE | All | [S: T04] |
| N-Q02 | Quote reminder | Timer `sent + 3d`, `+7d` (0–5 reminders) until accepted/declined/expired | same | E [+S] | Scheduled | TXN | TXN | On,C,T,X | VS-QUOTE | All | [S: T04 "reminder settings"] |
| N-Q03 | Quote expiring soon | Timer `expires_at − 2d` | same | E | Scheduled | TXN | TXN | On,T,X | VS-QUOTE | All | [I] |
| N-Q04 | Quote accepted — confirmation | `quote.quote.accepted` | Cust | E | Immediate | TXN | TXN | On,X | VS-QUOTE | All | [I] |
| N-I01 | Invoice sent (Pay link) | `invoice.invoice.sent` | Bill-to contact(s) | E [+S link] | Immediate | TXN | TXN | C,X | VS-INVOICE | All | [S: T04] |
| N-I02 | Invoice reminder (due / overdue) | Timer: `due_at`, `+3d`, `+7d`, `+14d` (max 6) while balance > 0 | same | E [+S] | Scheduled | TXN | TXN | On,C,T,X | VS-INVOICE | All | [S: T04 "reminder settings"] |
| N-P01 | Payment receipt | `payment.payment.captured` (incl. POS, card on file, invoice pay) | Payer | E [+S link] | Immediate | TXN | LOCK(email when email known) | X | VS-PAYMENT | All | [S: T16 "payment is recorded"] [I msg] |
| N-P02 | Deposit / pre-authorization notice | `payment.preauth.created` / `payment.deposit.captured` | Payer | E | Immediate | TXN | TXN | On,X | VS-PAYMENT | All | [S: T04 deposits, prepayments, pre-authorization] [I msg] |
| N-P03 | Payment failed (card on file declined) | `payment.payment.failed` (non-subscription) | Payer | E+S | Immediate | TXN | TXN | On,X | VS-PAYMENT + `links.update_card` | All | [I] |
| N-P04 | Card expiring soon | `payment.card.expiring` (30d prior, only if card is in use) | Card owner | E | Scheduled 10:00 local | TXN | TXN | On,X | VS-PAYMENT | All | [I] |
| N-P05 | Refund issued | `payment.refund.created` | Payer | E | Immediate | TXN | LOCK(email) | X | VS-PAYMENT + `refund.*` | All | [S: T09 transactions] [I msg] |

#### 5.1.4 Customer — subscriptions & gift cards (timing owned by Agent 06)

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| N-S01 | Subscription activated | `subscription.subscription.activated` | Subscriber | E | Immediate | TXN | TXN | On,X | VS-SUB | All | [S: T01] [I msg] |
| N-S02 | Renewal upcoming | `subscription.renewal.upcoming` | Subscriber | E | Agent 06 decides (default 3d before; required for annual/commitment) | TXN | TXN (LOCK for auto-renew notice where law requires) | T,X | VS-SUB | All | [S: T01 commitment options] [I] |
| N-S03 | Renewal charged (receipt) | `subscription.renewal.charged` | Subscriber | E | Immediate | TXN | LOCK(email) | X | VS-SUB + VS-PAYMENT | All | [I] |
| N-S04 | Subscription payment failed (dunning step n) | `subscription.payment.failed` | Subscriber | E+S | Immediate per attempt | TXN | TXN | X | VS-SUB + `dunning.*` | All | [I] |
| N-S05 | Subscription canceled | `subscription.subscription.canceled` | Subscriber | E | Immediate | TXN | TXN | On,X | VS-SUB | All | [I] |
| N-S06 | Subscription paused / resumed | `subscription.subscription.paused` / `.resumed` | Subscriber | E | Immediate | TXN | TXN | On,X | VS-SUB | All | [I] |
| N-S07 | Commitment period ending | `subscription.commitment.ending` | Subscriber | E | Agent 06 (default 14d before) | TXN | TXN | On,T,X | VS-SUB | All | [S: T01 commitment options] [I] |
| N-G01 | Gift card delivered | `giftcard.giftcard.delivery_due` (purchaser's chosen date; default now) | Gift | E (or S if purchaser gave phone) | At chosen date 09:00 recipient-local | TXN | TXN | X (design via AI gift card design [S: LLMS]) | VS-GIFT | All | [S: T05 "buy and send gift cards"] |
| N-G02 | Gift card purchase receipt | `giftcard.giftcard.purchased` | Payer | E | Immediate | TXN | LOCK(email) | X | VS-GIFT + VS-PAYMENT | All | [I] |
| N-G03 | Gift card balance update | `giftcard.giftcard.redeemed` | Holder | [E] | Immediate | TXN | TXN | On,X | VS-GIFT | All | [I] |

#### 5.1.5 Customer — account, reviews, referrals, compliance, inbox

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| N-A01 | Welcome / account created | `customer.account.created` | Cust | E | Immediate | TXN | TXN | On,X | VS-CUST | All | [S: T05 "create an account"] [I msg] |
| N-A02 | Login code / magic link | `auth.challenge.created` (Agent 01) | Requester | S or E | Immediate | CRIT | LOCK | — (branding only) | VS-AUTH | All | [I] |
| N-A03 | Email / phone verification | `customer.contact_point.verification_requested` | Requester | E or S | Immediate | CRIT | LOCK | — | VS-AUTH | All | [I] |
| N-A04 | Privacy request confirmation (export/deletion) | `customer.privacy.request_completed` | Cust | E | Immediate | TXN | LOCK | — | VS-CUST | All | [I] |
| N-R01 | Review request (rating link) | Timer: `job.completed_at + 2h` (5.9) | Cust | S →E | Scheduled | TXN | TXN (own category "Review requests") | On,C,T,X | VS-JOB + `links.review` | All | [S: LLMS "Google review booster"] |
| N-R02 | Review request reminder | Timer: `N-R01.sent + 48h` if no rating | Cust | S →E | Scheduled | TXN | TXN | On,T,X | same | All | [I] |
| N-M01 | Campaign message (email/SMS/push/postcard) | Campaign send (4.10) | Audience | per campaign | per campaign | MKT | MKT | full | VS-MKT | SMS All; email All*; push Mid | [S: T10; LLMS tiers] |
| N-M02 | Drip step message | Drip step (4.12) | Enrollee | per step | per step | MKT (or TXN for "transactional" drips like onboarding info, flagged by owner and reviewed) | MKT | full | VS-MKT + trigger context | Mid (starter: 3 drips) | [S: T12] |
| N-M03 | SMS marketing double opt-in ("Reply YES") | `comms.consent.pending_confirmation` | Contact | S | Immediate | CRIT | LOCK | X (must keep required elements) | VS-COMPLIANCE | All | [I] |
| N-M04 | STOP / START / HELP auto-replies | Inbound keyword (10.6) | Sender | S | Immediate | CRIT | LOCK | X (business name + help contact; required elements locked) | VS-COMPLIANCE | All | [I] |
| N-M05 | Referral reward earned | `referral.referral.rewarded` | Referrer | E [+S if MKT consent] | Immediate | TXN | TXN | On,X | VS-REF | Mid | [S: LLMS referral codes] [I msg] |
| N-M06 | Referral code welcome (your code) | `referral.code.issued` | Cust | E | Immediate | MKT | MKT | On,X | VS-REF | Mid | [I] |
| N-C01 | Staff message (manual reply / new outbound) | User action in inbox | Contact | channel chosen | Immediate | none (human-initiated; UI warns 21:00–08:00) | conversational (STOP honored) | n/a | VS-CONV | All | [S: LLMS live chat; T02 "message the client"] |
| N-C02 | Chat transcript / missed reply | Visitor left webchat with unread staff reply | Visitor | E (S if consent) | +5 min after leave | TXN | TXN | On,X | VS-CONV | All | [I] |
| N-C03 | After-hours auto-reply | Inbound outside business hours | Sender | same channel | Immediate, ≤1 per 12h per conversation | CRIT | TXN | On,X | VS-CONV | All | [I] |

`*` email campaigns are Scale-tier at Fieldd [S: LLMS]; we propose all tiers because email cost is
negligible (Section 12).

#### 5.1.6 Team (worker app / CRM users)

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| W-01 | Worker invite | `team.worker.invited` / `.invite_resent` | Wkr | E [+S] | Immediate | CRIT | LOCK | X (intro line only) | VS-TEAM | All | [S: T13] |
| W-02 | New job assigned | `dispatch.job.assigned` (incl. at booking) | AsgW | P+I (S if no app) | Immediate; if job > 7d away, batch into W-06 | TEAM | OPS | On,C | VS-JOB(team) | All | [S: T15 "notify your assigned team members"] |
| W-03 | Removed from job | `dispatch.job.unassigned` | Old worker | P+I | Debounce 2 min | TEAM | OPS | On,C | VS-JOB(team) | All | [S: T08] |
| W-04 | Job rescheduled / canceled | `booking.appointment.rescheduled` / `.canceled` | AsgW | P+I | Debounce 2 min; CRIT if within 12h | TEAM* | OPS | On,C | VS-JOB(team) + `old.*` | All | [S: T08 "notify the necessary people"] |
| W-05 | Job details changed (address, services, add-ons, notes) | `job.job.updated` (material fields) | AsgW | P+I | Debounce 5 min | TEAM | OPS | On | VS-JOB(team) + `changes[]` | All | [S: T07 add-ons added after booking] [I] |
| W-06 | Daily agenda / tomorrow's route | Timer 18:00 day before or 06:00 day of (worker-local) | Each worker with jobs | P+I [+E] | Scheduled | TEAM | OPS | On,T | VS-AGENDA | All | [S: T02 route] [I] |
| W-07 | Timesheet approved | `timesheet.timesheet.approved` | Wkr | P+I | Immediate | TEAM | OPS | On | VS-TIMESHEET | All | [S: T03] |
| W-08 | Timesheet rejected (+reason) | `timesheet.timesheet.rejected` | Wkr | P+I | Immediate | TEAM | OPS(locked on) | — | VS-TIMESHEET | All | [S: T03] |
| W-09 | Timesheet marked paid | `timesheet.timesheet.paid` | Wkr | [P+I] | Immediate | TEAM | OPS | On | VS-TIMESHEET | All | [S: T03 status paid] [I notify] |
| W-10 | Team chat message / @mention | `comms.team_message.created` | Group members | P+I | Immediate (batched 30s per thread) | TEAM (mentions by manager with "urgent" bypass) | OPS | per-user mute | VS-CHAT | All | [S: LLMS group chats] |
| W-11 | Customer message on my job | `comms.conversation.message_received` routed to worker | AsgW | P+I | Immediate | TEAM | OPS | per-user | VS-CONV | All | [S: T02 "message the client"] [I] |
| W-12 | New rating on my job | `review.review.received` | AsgW | [P+I] | Immediate | TEAM | OPS | On | VS-REVIEW | All | [I] |
| W-13 | Clock-out reminder | `timesheet.punch.open_too_long` | Wkr | P | Immediate | CRIT | OPS | On,T | VS-TIMESHEET | All | [S: T03 clock in/out] [I] |
| W-14 | Open job available to claim | `dispatch.job.offered` | Eligible workers | P+I | Immediate | TEAM | OPS | On | VS-JOB(team) | Mid | [I] (if Agent 03 supports offers) |
| W-15 | Pay rate change effective | `team.pay_rate.effective` | Wkr | [I] | On effective date 08:00 | TEAM | OPS | On | VS-TEAM | All | [S: T03 future rates] [I notify] |
| W-16 | Leaderboard / goal achievement | `reporting.goal.milestone_reached` (team-visible goals) | Team | [P+I] | Immediate | TEAM | OPS | On | VS-GOAL | Mid | [S: T09 "celebrate top contributors"] [I] |

#### 5.1.7 Owner / admin (operational alerts)

| ID | Name | Trigger | To | Ch | Timing | QH | Opt | Edit | VS | Tier | Src |
|---|---|---|---|---|---|---|---|---|---|---|---|
| A-01 | New online booking | `booking.appointment.created` source∈{online, app, api} | Own | I [+P,+E] | Immediate | OPS | OPS | On,C | VS-JOB | All | [I] |
| A-02 | Customer canceled / rescheduled | `booking.appointment.canceled` / `.rescheduled` actor=customer | Own | I [+P,+E] | Immediate | OPS | OPS | On,C | VS-JOB | All | [S: T05 cancellation feedback] [I] |
| A-03 | New lead / abandoned booking captured | `lead.lead.created`, `booking.session.abandoned` | Own | I [+P] | Immediate (abandoned: digest hourly) | OPS | OPS | On,C | VS-LEAD | All | [S: T10, T20] |
| A-04 | Quote accepted / declined (A-04v viewed) | `quote.quote.accepted` / `.declined` / `.viewed` | Own + quote creator | I [+P,+E] | Immediate | OPS | OPS | On,C | VS-QUOTE | All | [S: T04] [I] |
| A-05 | Payment received (digest) / failed | `payment.payment.captured` / `.failed` | Own | I (daily digest E optional) | Immediate / 19:00 digest | OPS | OPS | On,C,T | VS-PAYMENT | All | [I] |
| A-06 | Dispute / chargeback opened | `payment.dispute.opened` | Own | I+E+P | Immediate | CRIT | LOCK | — | VS-PAYMENT + `dispute.*` | All | [S: T09 "dispute management"] |
| A-07 | Payout sent / failed | `payment.payout.paid` / `.failed` | Own | I [+E] (failed: E locked) | Immediate | OPS | OPS | On | VS-PAYOUT | All | [S: T17 payout history] |
| A-08 | New review (low-rating alert ≤3★) | `review.review.received` | Own | I [+P,+E for ≤3★] | Immediate | OPS | OPS | On,C,T(threshold) | VS-REVIEW | All | [I] |
| A-09 | Timesheet submitted for approval | `timesheet.timesheet.submitted` | Appr | I [+E digest] | Immediate | OPS | OPS | On,C | VS-TIMESHEET | All | [S: T03 "sent over to the CRM for approval"] |
| A-10 | New inbound message | `comms.conversation.message_received` | Assignee / watchers / Own if unassigned | I+P | Immediate | OPS | OPS | per-user | VS-CONV | All | [S: LLMS live chat] |
| A-11 | Worker accepted invite | `team.worker.activated` | Own | I | Immediate | OPS | OPS | On | VS-TEAM | All | [S: T13 "you'll see them appear in your CRM"] |
| A-12 | Subscription dunning exhausted | `subscription.dunning.exhausted` | Own | I+E | Immediate | OPS | OPS | On | VS-SUB | All | [I] |
| A-13 | Goal milestone reached / missed | `reporting.goal.milestone_reached` / `.period_closed` | Goal watchers | I [+P] | Immediate | OPS | OPS | On | VS-GOAL | All | [S: LLMS goals] [I] |
| A-14 | Weekly / monthly summary report | Timer Monday 07:00 business-local | Own | E | Scheduled | OPS | OPS | On,T | VS-SUMMARY | All | [I] |
| A-15 | Sender / compliance status & deliverability alerts | `compliance.sender.status_changed`, bounce/complaint thresholds | Own | I+E | Immediate | CRIT | LOCK | — | VS-COMPLIANCE | All | [I] |
| A-16 | Low inventory | `catalog.addon.stock_low` | Own | I [+E] | Immediate, max 1/item/day | OPS | OPS | On | VS-CATALOG | All | [S: T07 "stock levels, and min or max quantities"] [I notify] |
| A-17 | Campaign/drip finished, paused by system, or budget threshold | `marketing.campaign.completed` / `.failed`, `comms.budget.threshold_reached` | Own | I+E | Immediate | OPS | OPS | On | VS-MKT | All | [I] |
| A-18 | Job needs assignment (auto-dispatch found nobody) | `dispatch.job.unassignable` | Own + dispatchers | I+P | Immediate | CRIT if job < 24h | OPS | On,C | VS-JOB | All | [S: LLMS "Auto-dispatches every booking"] [I] |
| A-19 | Missing clock-out | `timesheet.punch.open_too_long` | Appr | I | Next 07:00 local | OPS | OPS | On | VS-TIMESHEET | All | [I] |
| A-20 | Export ready | `reporting.export.ready` | Requester | I [+E] | Immediate | OPS | OPS | — | VS-EXPORT | All | [S: T09 downloads] [I] |
| A-21 | Customer opted out of SMS/email | `comms.consent.changed` (opt-out) | Own | [I] | Daily digest | OPS | OPS | On | VS-COMPLIANCE | All | [I] |

**Catalog count:** 14 booking + 4 day-of + 11 quotes/invoices/payments + 10 subscriptions/gift +
13 account/reviews/marketing/inbox + 16 team + 21 owner = **89 entries** (Fieldd claims "nearly
200 emails" [S: LLMS]; their count likely includes per-variant templates — e.g., each entry ×
channel × business type. Our 89 entries × channels ≈ 170 templates) [I].

**Catalog governance rules**
1. New message types require a catalog entry (ID, all columns) reviewed by Agent 08; emitting
   modules publish events, never call providers.
2. Entries are versioned. A business's edited template is pinned to the default version it was
   forked from; when defaults change, show "Update available" diff, never overwrite.
3. Locked entries cannot be disabled; their editable text excludes required elements (codes,
   legal wording, STOP language).
4. Every entry declares `fallback` behavior and `dedupe_window` (e.g., N-D02 max 2/job).

### 5.2 Message classification (drives consent, quiet hours, unsubscribe)
| Class | Definition | Examples | Consent needed (US) [I — counsel] | Unsubscribe effect |
|---|---|---|---|---|
| Security | Authentication / account safety | N-A02, N-A03 | Prior express consent implied by request | Cannot opt out (but SMS STOP still blocks SMS → email used) |
| Transactional | Relates to an existing transaction the recipient initiated | Confirmations, reminders, on-my-way, receipts, invoices, quote follow-ups, dunning | Prior express consent (giving number in the transaction context); no marketing content allowed in body | Customer may change channel; SMS STOP blocks all SMS from that sender |
| Conversational | Human 1:1 replies in a thread the customer started or expects | Inbox replies | Customer-initiated thread; STOP honored | STOP blocks SMS |
| Marketing | Promotes goods/services | Campaigns, drips, referral promos, abandoned booking nudges, rebook promos | SMS: **prior express written consent** (checkbox/keyword, signed via E-SIGN). Email: CAN-SPAM opt-out model (US); CASL/GDPR opt-in elsewhere | Category unsubscribe; global marketing unsubscribe |
| Operational (team) | Employer → worker | W-series | Employment relationship; worker can mute non-critical | Per-user preferences |
| Operational (owner) | Platform → business users | A-series | Account relationship | Per-user preferences, except LOCK |

**Mixed-content rule:** a transactional message must not contain promotional content (e.g.,
"Book again and get 20% off") unless the recipient has marketing consent for that channel. The
template linter flags promo keywords and discount variables in transactional templates and,
if present, the renderer strips the promo block for non-consented recipients ("conditional
promo block") [I].

### 5.3 Template variables (dynamic fields)
[S: T12 "dynamic fields to personalize it with the customer's name"; booking link]

Syntax: `{{namespace.field}}` with filters `{{job.start | date:"EEE MMM d, h:mm a"}}`,
`{{customer.first_name | default:"there"}}`, `{{money | currency}}`. Rendering is
sandboxed (logic-less: conditionals `{{#if}}`, loops over arrays only; no code execution) [I].
All dates render in the **recipient display time zone** (5.5.1) with the zone abbreviation when
it differs from business tz.

| Set | Variables |
|---|---|
| VS-BIZ (always) | `business.name`, `business.phone`, `business.email`, `business.address`, `business.website`, `business.logo_url`, `business.brand_color`, `links.booking` (booking portal / subdomain, Agent 01), `links.app_download`, `links.preferences`, `links.unsubscribe` (email), `business.review_url` |
| VS-CUST | `customer.first_name`, `customer.last_name`, `customer.full_name`, `customer.company_name` (business customers [S: LLMS]), `customer.email`, `customer.phone` (masked), `customer.account_url`, `customer.credit_balance`, `customer.tags[]` |
| VS-JOB | VS-BIZ + VS-CUST + `job.id` (single job ID [S: T04]), `job.number`, `job.start` (datetime), `job.end`, `job.date`, `job.time_window` (route jobs: "Anytime Thursday" text [S: T02]), `job.duration_minutes`, `job.address` (one-line), `job.address_notes`, `job.services[]` {name, qty, price}, `job.addons[]`, `job.total`, `job.deposit_paid`, `job.balance_due`, `job.workers[]` {first_name, photo_url}, `job.vehicle` (make/model/color [S: LLMS vehicle on every job]), `job.assets[]` [S: T05], `job.notes_for_customer`, `links.manage` (reschedule/cancel), `links.add_to_calendar`, `links.pay`, `links.intake` |
| `eta.*` | `eta.time`, `eta.minutes`, `eta.window_start`, `eta.window_end`, `links.tracking`, `worker.first_name`, `worker.photo_url`, `worker.vehicle_desc` |
| `old.*` | `old.start`, `old.date`, `old.worker_first_name` |
| `cancel.*` | `cancel.reason`, `cancel.fee`, `cancel.refund`, `links.rebook` |
| `route.*` | `route.stop_number`, `route.total_stops`, `route.window` |
| `photos.*` | `photos.before[]`, `photos.after[]`, `links.gallery` |
| VS-SERIES | VS-JOB + `series.frequency_text`, `series.next_dates[]` (max 3), `series.discount_text` [S: T14], `series.end_text` |
| VS-QUOTE | VS-BIZ + VS-CUST + `quote.id` (= job id), `quote.total`, `quote.expires_at`, `quote.terms`, `links.quote_view`, `links.quote_book` [S: T04 "book online button"] |
| VS-INVOICE | VS-BIZ + VS-CUST + `invoice.id`, `invoice.total`, `invoice.amount_paid`, `invoice.balance_due`, `invoice.due_date`, `invoice.days_overdue`, `links.pay`, `links.invoice_pdf` |
| VS-PAYMENT | `payment.amount`, `payment.tip`, `payment.tax`, `payment.method_display` ("Visa ••4242"), `payment.date`, `links.receipt`, `refund.amount`, `refund.reason`, `dispute.amount`, `dispute.reason`, `dispute.respond_by`, `links.update_card` |
| VS-SUB | `subscription.package_name`, `subscription.price`, `subscription.cycle`, `subscription.next_charge_date`, `subscription.commitment_end`, `subscription.included_text` [S: T01], `dunning.attempt`, `dunning.next_retry`, `links.manage_subscription` |
| VS-GIFT | `gift.amount`, `gift.balance`, `gift.code` (masked except in N-G01), `gift.message`, `gift.from_name`, `gift.design_url`, `links.redeem` |
| VS-AUTH | `auth.code`, `auth.link`, `auth.expires_minutes` |
| VS-REVIEW | `review.rating`, `review.comment`, `links.review` (internal rating page), `business.review_url` |
| VS-REF | `referral.code`, `referral.link`, `referral.friend_offer_text`, `referral.reward_text`, `referral.friend_first_name` |
| VS-MKT | VS-BIZ + VS-CUST + `last_job.date`, `last_job.service`, `next_job.date`, `offer.code`, `offer.text`, `offer.expires_at`, `links.offer` (tracked, pre-applies code), `session.service_name`, `session.selected_date`, `links.resume_booking` (abandoned), `asset.make`, `asset.model`, `asset.last_service_date` |
| VS-TEAM | `worker.first_name`, `invite.link`, `invite.expires_at`, `links.app_store`, `links.play_store`, `pay_rate.amount`, `pay_rate.effective_date` |
| VS-TIMESHEET | `timesheet.period`, `timesheet.date`, `timesheet.total_hours`, `timesheet.status`, `timesheet.reject_reason`, `links.timesheet` |
| VS-CHAT / VS-CONV | `sender.name`, `message.preview` (≤ 80 chars, no PII in lock-screen push when privacy mode on), `links.thread` |
| VS-AGENDA | `agenda.date`, `agenda.jobs[]` {time, customer_first_name, address_short, services}, `agenda.first_start`, `links.route` |
| VS-LEAD, VS-GOAL, VS-SUMMARY, VS-EXPORT, VS-COMPLIANCE, VS-CATALOG, VS-PAYOUT | Defined in the API schema per entry (lead name/source/step reached; goal name/value/target/pct; summary KPIs; export name/link; sender status/reason; item/stock; payout amount/arrival date) |

Rendering rules [I]:
1. Missing variable → `default` filter value if provided, else the renderer **blocks** the send
   for required variables (declared per entry) and logs `render_failed`; for optional variables it
   renders empty and collapses surrounding punctuation ("Hi ," → "Hi,").
2. Names are trimmed, title-cased only if all-caps/all-lowercase input; never alter mixed case.
3. Money: integer cents → `Intl` currency format of business currency; no fractional cents.
4. Links in SMS always use the business's branded short domain (e.g., `book.example.com/s/Ab3x`)
   — public shorteners blocked [I].
5. SMS template length check runs against **worst case** expansion (e.g., `job.services[]` long
   names) — UI shows "up to 3 segments".
6. Snapshot: variables are resolved at **render time** (send time), not at schedule time, so
   reminders show current job time/balance. The rendered body is stored on the OutboundMessage
   for audit.

### 5.4 Timing defaults & dedupe
| Item | Default | Allowed range | Rule |
|---|---|---|---|
| Reminder #1 | 24h before start | 1h–7d | Skip if due_at < now at scheduling time |
| Reminder #2 | 2h before (off) | 15m–12h | Must be < Reminder #1 |
| Day-before reminder anchoring | Optional "send at 18:00 the day before" instead of exact −24h | 07:00–20:00 | Avoids 06:00 sends for 06:00 jobs |
| Reschedule debounce | 120s | 0–600s | Coalesce per job |
| Worker change debounce | 120s | — | — |
| Review request delay | 2h after complete | 0–72h | Cooldown per customer 90d (0–365) |
| Quote reminders | +3d, +7d | 0–5 reminders, 1–30d | Stop on accept/decline/expire/void |
| Invoice reminders | due, +3d, +7d, +14d | 0–6 reminders | Stop when balance = 0 or void/written-off |
| Gift card delivery | purchaser chosen date 09:00 recipient-local | — | Not before purchase capture |
| Abandoned booking (template) | +2h [S: T12 "a couple of hours later"] | 15m–7d | Only with consent |
| Running late threshold | 15 min | 5–60 | Max 2 per job |
| Job-complete photo wait | ≤10 min or until worker marks photos done | — | — |
| Per-recipient duplicate guard | Same catalog ID + same subject + same rendered body hash within 10 min → drop | — | Defense in depth beyond idempotency keys |

### 5.5 Quiet hours & time zones
#### 5.5.1 Recipient time zone resolution (in order) [I]
1. Explicit preference on the contact (`contact.timezone`, set by customer or staff).
2. For job-related messages: time zone of the **job's service address** (geocoded; Agent 02/03
   store `tz` on address). For shop jobs: shop tz.
3. Contact's primary address tz.
4. Phone number area code → tz (NANP mapping; multi-tz area codes use the **most restrictive**
   window across candidate zones — e.g., the later start and earlier end).
5. Business tz.
Workers: worker profile tz (Agent 09) else business tz.
Display time zone in messages = job location tz for job times (the time the customer will
experience), not recipient tz; add zone abbreviation when recipient tz ≠ job tz.

#### 5.5.2 Windows (defaults; business can **narrow**, never widen beyond legal bounds)
| Class | Default allowed window (recipient local) | Business-adjustable | Legal floor [I — counsel] |
|---|---|---|---|
| CRIT | 24h | No | n/a |
| TXN | 08:00–21:00; outside → defer to 08:00, **unless** the subject event is < 3h away (e.g., 07:30 job reminder may go at 06:00? → no: reminder offsets are recomputed: a reminder due 21:00–08:00 for a job starting before 11:00 is moved to 20:00 the evening before) | Narrow | TCPA time-of-day rule targets solicitations; we apply anyway for courtesy |
| MKT | 09:00–20:00 Mon–Sat; Sunday 12:00–18:00 (default off) ; holiday blackout list | Narrow only | Federal: 08:00–21:00 local for telephone solicitations; some states stricter (e.g., FL 08:00–20:00 and frequency limit 3 per 24h per subject; OK similar) |
| TEAM | Worker's shift hours ± 1h (from availability), else 07:00–21:00 | Per-worker | n/a |

Algorithm (Section 10.2) computes `next_allowed(t, tz, window)` DST-safely using wall-clock
arithmetic in the IANA zone (never adding 3600s offsets).

**Reminder pull-forward rule:** if a reminder's computed due time lands in quiet hours, move it
**earlier** to the end of the previous allowed window (e.g., 20:59 the day before), not later —
a reminder that arrives after the appointment started is useless. If no earlier slot exists
after booking time → skip [I].

**Frequency caps (marketing only)** [I]:
- Per contact per business: ≤ 1 marketing SMS / 24h, ≤ 4 / 7 days; ≤ 2 marketing emails / 24h.
- Across drips + campaigns combined; excess messages are **deferred** to the next allowed day
  (drips) or **skipped** (broadcast; recorded as `suppressed_frequency`).
- Owner can raise per-week cap to 7 SMS but not the per-24h cap (state law alignment).

### 5.6 Consent model (TCPA / CTIA / CAN-SPAM / international)
All statements `[I — counsel review required]`.

#### 5.6.1 Records
Consent is stored **per business × channel endpoint (E.164 phone / normalized email) × purpose**
(transactional, marketing) as an **append-only ledger**; current state is a projection.

Each `ConsentEvent` captures: endpoint, purpose, action (grant/revoke/confirm), method
(`web_checkbox`, `keyword_reply`, `verbal_attested_by_staff`, `paper_form`, `import_attested`,
`customer_app_toggle`, `preference_center`, `one_click_unsubscribe`, `carrier_stop`,
`complaint`, `hard_bounce`), disclosure text **version id** and full text hash, page URL, IP,
user agent, actor (customer / staff user id / system), timestamp, evidence pointer (screenshot
template of that consent screen version, form image, call note).

#### 5.6.2 Capture points (contracts with other agents)
| Where | Owner agent | Captures |
|---|---|---|
| Booking site contact step | 01 | Transactional SMS consent implicit with phone + **separate unchecked checkbox** for marketing texts with TCPA disclosure ("By checking, you agree to receive recurring automated marketing texts from {{business}} at this number. Consent not required to purchase. Msg & data rates may apply. Reply STOP to cancel, HELP for help. Terms/Privacy links."). Email marketing checkbox (pre-checked allowed in US for email only where lawful; default unchecked for CA/EU visitors) |
| Customer app onboarding / settings | 01 | Same + push permission state |
| CRM manual customer add / import | 07 | Staff attestation per channel ("Customer gave written consent on [date] via [method]") — required to mark marketing SMS consent; CSV import column `sms_marketing_consent` requires batch attestation + retained source file |
| Keyword opt-in (e.g., text "JOIN" to the business number; QR "text to join") | 08 | Double opt-in confirmation N-M03 |
| Postcard QR landing | 01/08 | Same checkbox as booking site |
| Lead forms | 07 | Same checkbox |
| Workers | 09 | Operational messages by employment; personal phone used only for W-series |

#### 5.6.3 Revocation
- STOP-family keywords (10.6) on SMS → revoke **all SMS** from that sender number to that phone
  (carrier/CTIA expectation), send one confirmation (N-M04), mark `sms_all=revoked`.
  Transactional fallback switches to email/push automatically and the job page shows
  "SMS opted out — notifications by email".
- Revocation by "any reasonable means" (FCC 2024 order, effective 2025): free-text like "please
  stop texting me", "unsubscribe", "remove me" is detected by an NLP-lite rule set + optional AI
  classifier; ambiguous messages create an inbox task "Possible opt-out request" and the
  marketing consent is **paused** until staff resolves [I].
- Honor immediately (target < 1 minute); legal maxima (e.g., 10 business days) are irrelevant
  because we never queue after revocation — the Pipeline checks consent at send time.
- Email: one-click unsubscribe / footer / reply "unsubscribe" → marketing email revoked.
- Re-subscribe: START/UNSTOP/YES keywords or explicit customer action (preference center with
  confirmation). Staff cannot re-subscribe an SMS opt-out on the customer's behalf without new
  written consent evidence (requires upload) [I].

#### 5.6.4 Scope across businesses
Consent and suppression are **per business** (each business is a separate sender/brand).
Exception: platform-level **global suppression** for hard-bounced emails, spam complaints, and
numbers flagged by carriers as reassigned/disconnected (Reassigned Numbers Database check for
phones whose consent is > 12 months old before marketing sends — paid lookup, top tier) [I].

#### 5.6.5 International
Fieldd operates in 37 countries [S: LLMS]. Policy pack per business country [I]:
- **Canada (CASL)**: express or implied consent (implied: existing business relationship within
  2 years of purchase); unsubscribe within 10 business days; sender identification. Email
  marketing defaults to opt-in checkbox.
- **UK/EU (PECR/GDPR)**: opt-in for marketing (soft opt-in for existing customers, similar
  services, with opt-out at collection); DPA, data retention, right to erasure (Agent 07).
- **Australia (Spam Act)**: consent + unsubscribe within 5 business days, sender ID.
- Alphanumeric sender IDs where supported (UK/EU/AU) cheaper than numbers; no two-way though.
MVP: US + Canada; others behind feature flag.

#### 5.6.6 SMS content requirements (CTIA / carrier) [I]
- Identify business in first message of a conversation/marketing message ("Acme Detailing:").
- Opt-out language on marketing messages and at least the first message in a program.
- HELP reply must include business name + contact (phone/email) + opt-out instruction.
- No SHAFT content (sex, hate, alcohol, firearms, tobacco/cannabis) without age gating — template
  linter blocks keywords for categories the business did not register.
- No public URL shorteners; branded short domain must be registered in the campaign.
- Embedded phone numbers must match registered.

### 5.7 Attribution model ("revenue generated")
[S: T12 "the business that I've generated from it"; T10 analytics] — formula is `[I]`.

**Unit of attribution:** a **Job** (single job ID [S: T04]) booked by a contact.

**Touches:** a touch is a marketing message (campaign or drip step) that reached the contact:
- `click` touch: contact clicked a tracked link in the message (link token resolves contact +
  message) — strongest.
- `code` touch: job used an offer code issued by that campaign/drip (via Agent 06 redemption
  event carrying `source_campaign_id`).
- `delivered` touch: message delivered (SMS delivery receipt; email delivered, NOT opened —
  opens are unreliable due to privacy proxies).
- `scan` touch: postcard QR scan.

**Windows (defaults, business-configurable 1–30 days):** click 7d, code 30d, delivered SMS 3d,
delivered email 3d, push 3d, scan 30d, postcard mailed (no scan) 14d.

**Rule: last eligible touch, with precedence** — for a job created at time `t_booked` by contact
`c`:
1. Candidate touches = touches for `c` with `t_touch ≤ t_booked` and within the channel window.
2. If the booking session carries a link token or code → that touch wins (direct attribution).
3. Else precedence `code > click > scan > delivered`; ties broken by most recent `t_touch`.
4. Exactly **one** campaign/drip gets credit per job (no double counting in totals). A secondary
   "influenced" count (all campaigns with a candidate touch) is shown separately and never summed.
5. Transactional notifications are never touches (a reminder doesn't "generate" revenue).
6. If the job was booked **before** the drip enrollment started → not attributable.
7. Recurring series: only the **first** job of a series created from the touch is attributed;
   subsequent auto-generated occurrences are not (option "attribute series value for 90 days"
   shown as separate metric) [I].

**Attributed revenue value (cents):** the job's **Net Sales** (5.10.3 M-REV-NET) recognized on
completion, i.e., `services + add-ons + travel fees − discounts − refunds`, excluding tax and tips.
Until completion, the job contributes to "Pipeline (booked) value"; after completion it moves to
"Revenue generated". Canceled jobs contribute 0 (remove from pipeline). Refunds after completion
reduce attributed revenue retroactively (rollups handle via negative contributions, 10.10).

**Drip metrics (exact):**
- *In drip* = count of enrollments with state ∈ {active, waiting, executing} at query time.
- *Completed* = enrollments that reached the end of the last step (state `completed`),
  cumulative since launch (filterable by date of completion).
- *Exited* = enrollments that left early, by reason (`goal_reached`, `unsubscribed`,
  `condition_failed`, `manual`, `contact_deleted`, `drip_archived`).
- *Revenue generated* = Σ attributed revenue of jobs attributed to the drip, completed within the
  selected date range (default all time).
- *Conversion rate* = enrollments with ≥1 attributed job / enrollments started (cohort by
  enrollment start date).
- *Cost* = Σ provider cost of messages sent by the drip (SMS segments × rate + email + postcard).
- *ROI* = (Revenue generated − Cost) / Cost (shown only when Cost > 0).

**Campaign metrics (exact):** recipients (snapshot), suppressed (by reason), sent, delivered,
delivery rate = delivered/sent, bounced (hard/soft), open rate (email) = unique opens /
delivered — labeled "approximate" (Apple MPP inflates), click rate = unique clickers / delivered,
CTOR = unique clickers / unique openers, reply rate (SMS) = recipients who replied within 72h /
delivered, unsubscribe rate = unsubscribes attributed to this message / delivered, complaint
rate = complaints / delivered, bookings = attributed jobs, revenue = attributed revenue,
cost, ROI.

### 5.8 Drip enrollment rules
[S: T12 trigger/steps/launch; behavior details I]
| Rule | Default | Options |
|---|---|---|
| Eligibility | Contact matches trigger + filters + has at least one channel the drip uses with valid consent (else enrolled but steps for unavailable channels are skipped with reason; if no step can ever send → not enrolled, counted as `ineligible`) | — |
| Re-entry | **Once ever** for Action/new-customer triggers; **once per occurrence** for Date triggers (e.g., each year's holiday); **once per 14 days** for abandoned booking | never / after completion / after exit / cooldown N days |
| Concurrency | A contact can be in max 1 active enrollment per drip | — |
| Cross-drip | No limit, but frequency caps (5.5) apply across all drips/campaigns | Priority (1–5) used to decide which message gets the slot when capped |
| Exit conditions | Unsubscribed from all channels the drip uses; contact deleted/merged (enrollment moves to survivor if survivor not already enrolled); drip paused→**hold** (not exit); drip archived → exit; goal reached (default for promo drips: "booked a job"); trigger condition no longer true (for segment-entry triggers, option "exit when no longer matching") | Custom: tag added, lead converted, invoice paid, etc. |
| Pause semantics | Paused drip: timers keep counting; due steps are **held**; on resume, held steps execute with send-window rules; option "skip messages that became due while paused" | — |
| Late steps | If a step is due > 72h late (outage/pause), its message is skipped unless step flagged "always send" [I] | — |
| Send timing | ASAP [S: T12]: send as soon as step is due subject to quiet hours; Window: next occurrence of window; Specific time: e.g., 10:00 recipient-local on due day or next day | — |
| Existing contacts at launch | Segment/date triggers: option to enroll existing matches (default off); Action triggers: only future events | — |
| Test mode | "Send test enrollment to me" runs steps with delays compressed (1 day → 1 minute) to staff only | — |
| Limits | Max 20 steps per drip; max delay 365d; max 50 live drips (top tier), 3 on starter [I] | — |

### 5.9 Reviews & referrals rules
**Review booster** [S: LLMS] [rules I]:
1. One request per completed job; cooldown 90d per customer (configurable); never for jobs with
   total = 0 internal jobs (warranty/redo) unless enabled.
2. Rating stored on the job, attributed to every worker on the job (split jobs: each worker
   receives the rating; leaderboard "reviews received" counts per worker).
3. **No review gating**: every rater sees the public review option. Rationale: Google's review
   policies prohibit selectively soliciting positive reviews, and the US FTC's 2024 Consumer
   Reviews and Testimonials rule targets review suppression; gating risks the business's Google
   profile [I — counsel]. Private feedback is offered **in addition**, not instead.
4. Incentives for reviews (discounts for reviews) are blocked in templates (linter) [I].
5. Low-rating (≤3★) creates an inbox task and A-08 alert; customer response SLA metric.
6. Ratings from customers are also stored as "rating given" on the customer profile
   [S: T09 profiles show "ratings"].

**Referrals** [S: LLMS] [rules I]:
1. One code per customer per business; case-insensitive; Crockford base32 suffix (no O/0/I/1).
2. Friend eligibility: new customer (no prior completed job, no matching phone/email/address
   among existing customers).
3. Self-referral block: friend sharing phone, email, payment card fingerprint (Agent 05),
   device id, or service address with referrer → `rejected:self_referral`.
4. Qualification: friend's first job completed and paid, net sales ≥ minimum (configurable),
   not refunded within 14d → reward issued; reward reversal if refunded later (credit clawback
   only if unused) [I].
5. Caps: max rewards per referrer per year (default 10); program budget cap.
6. Rewards issued via Agent 06 commands (credit ledger / discount code / gift card); 08 never
   writes money balances.

### 5.10 Metric dictionary (every number the owner sees)
[S: T09 tiles/leaderboard; T17 worker summary; LLMS reporting] — formulas are `[I]` unless noted.

#### 5.10.1 Global conventions
1. **Time zone.** All bucketing uses `Business.timezone` (IANA). A "day" is local midnight to the
   next local midnight, so DST days have 23 or 25 hours. "Today" means the business-local date.
   In a multi-business hub [S: LLMS], each business is bucketed in its own zone and labeled. Its
   numbers are never summed across currencies (matches Agent 05's rule).
2. **Week** starts on Monday by default (business setting). The default range is the
   current week, matching Transactions [S: T17].
3. **Two money bases, always labeled:**
   - **Accrual (default for Dashboard and Reports):** revenue is recognized on the job's
     revenue-recognition date. That is Agent 05's ledger `effective_date` for revenue accounts
     4000–4030, posted when Agent 04 finalizes the invoice, which in practice is at job completion.
   - **Cash (default for Transactions views):** money is counted on the payment or refund
     `effective_date` (business-local), as Agent 05's RevenueAttribution does.
   The two bases never mix inside one tile. The basis badge ("Accrual" or "Cash") appears in the tile
   footer and in CSV headers.
4. **Source of truth.** Money metrics read Agent 05's ledger (through rollups). Job counts read
   Agent 04/03 job state events. Ratings read 08's reviews. Hours read Agent 09's timesheets.
   08 never recomputes money from job line items when a ledger fact exists.
5. **Exclusions everywhere:** test-mode jobs and payments, deleted/voided documents, jobs
   whose business has `is_demo = true`, and quote-only jobs that were never booked.
6. **Money** is integer minor units. Sums are exact. Averages and ratios round half-up only for
   display (5.14).

#### 5.10.2 Money metrics
| ID | Name (UI) | Formula | Basis/date | Includes | Excludes |
|---|---|---|---|---|---|
| M-GROSS | Gross sales | Σ credits to revenue accounts 4000 (services), 4010 (add-ons), 4015 (products), 4020 (travel fees) | Accrual: revenue effective_date | Customer add-ons and company add-ons [S: T07], travel fees [S: T11] | Tax, tips, gift card sales, deposits not yet earned |
| M-DISC | Discounts | Σ debits to 4900 (contra) | Accrual | Discount codes, recurring and subscription discounts [S: T14, T01], manual discounts | — |
| M-REFUND | Refunds (revenue portion) | Σ debits to 4950, revenue portion only | Accrual view: posted on the **refund date** (no restatement, matching the ledger). "Restated" view: allocated back to the original job's recognition date (optional toggle) | — | Refunded tax, refunded tips |
| M-FEE-INC | Cancellation & no-show fees | Σ credits to 4030 | Accrual | Fees [S: T05 cancellation policy] | — |
| **M-NET** | **Revenue (Net sales)** — the default "Revenue" everywhere | `M-GROSS − M-DISC − M-REFUND + M-FEE-INC` | Accrual | Gift card *redemptions* count, because the service was delivered | Tax, tips, gift card *sales* (a liability), processing fees, subscription prepayments not yet consumed |
| M-COLLECTED | Collected revenue | Σ succeeded payments' `amount − tip − tax` − Σ succeeded refunds' revenue portion | Cash: payment/refund date | Card, cash, check, other tenders [S: T16], invoice payments | Gift card and store-credit tenders (non-cash), tips, tax |
| M-CASHIN | Total money in | Σ all succeeded tenders, including tips, tax and gift card sales | Cash | Everything received | Refunds shown as a separate negative line |
| M-TAX | Tax collected | Σ credits to 2200 − refund reversals | Accrual (cash toggle) | Zone tax [S: T11] | — |
| M-TIPS | Tips | Σ tip portions of succeeded payments − refunded tips | Cash | — | — |
| M-PROC-FEE | Processing fees | Σ processor fee per payment (Agent 05 `fee_minor`) | Cash | — | Estimated fees (never shown) |
| M-NET-DEPOSIT | Net deposits | M-CASHIN − refunds − M-PROC-FEE (card tenders only) | Cash | — | Cash/check |
| M-COST | Cost of services | Σ line `cost_snapshot × qty` from Agent 02 cost fields [S: T06, T07] on recognized lines | Accrual | — | Labor (separate: M-LABOR) |
| M-LABOR | Labor cost | Σ approved timesheet hours × effective rate [S: T03] | Timesheet date | Approved and paid entries | Rejected or pending entries |
| M-MARGIN | Gross margin | `M-NET − M-COST` (and % = margin / M-NET) | Accrual | — | Labor, unless "include labor" is toggled |
| M-AOV | Average ticket | `M-NET / M-JOBS-DONE` over the same period | Accrual | $0 jobs are counted unless the "exclude $0 jobs" toggle is on | — |
| M-MRR | Monthly recurring revenue | Σ active subscriptions' normalized monthly price (weekly × 52/12; yearly ÷ 12; a prepaid commitment is spread over its term) from Agent 06 | Snapshot at period end | Active and past-due subscriptions | Canceled or paused |
| M-GC-SOLD | Gift cards sold | Σ gift card sale amounts (liability) | Cash | — | Not part of revenue |
| M-AR | Outstanding invoices | Σ open invoice balances (Agent 04/05) | Snapshot | — | Voided or written-off invoices |

#### 5.10.3 Job & booking metrics
| ID | Name | Formula | Date basis |
|---|---|---|---|
| M-BOOKINGS | Bookings | Count of booking events: job created in a booked state (not a draft quote). A recurring series counts as 1 booking; each materialized occurrence counts in M-JOBS-SCHED | `booked_at` local date |
| M-JOBS-SCHED | Jobs scheduled | Jobs whose scheduled start falls in the period and whose status is not canceled | Scheduled start (job tz converted to business date) |
| M-JOBS-DONE | Jobs completed ("bookings completed" [S: T09]) | Jobs whose latest transition into `completed` falls in the period. A job reopened and completed again counts once, on its last completion date. Jobs canceled after completion are excluded | `completed_at` |
| M-CANCEL | Cancellations / rate | Count of jobs canceled with a scheduled start in the period; rate = canceled ÷ (completed + canceled + no-show) for the same scheduled-start window. Split by actor: customer, business, system | Scheduled start |
| M-NOSHOW | No-shows / rate | Same as M-CANCEL, using status `no_show` (Agent 03/04) | Scheduled start |
| M-RESCHED | Reschedules | Count of `rescheduled` events whose time changed, by actor | Event date |
| M-LEAD-TIME | Booking lead time | Median of `(scheduled_start − booked_at)` in hours, over jobs booked in the period | `booked_at` |
| M-DUR-ACC | Duration accuracy | Median of `actual_minutes ÷ scheduled_minutes` over completed jobs. Actual = started → completed from Agent 04 | `completed_at` |
| M-ON-TIME | On-time arrival | Share of jobs whose arrival (start) is ≤ scheduled start + grace (10 min) | `completed_at` |
| M-UTIL | Utilization (business) | Σ scheduled minutes of completed jobs ÷ Σ available minutes across active workers (Agent 09 availability minus time off) | Day |
| M-SOURCE | Booking sources [S: T05] | M-BOOKINGS grouped by `source` (online portal, customer app, CRM, worker app, API/MCP, recurring, quote acceptance) and by `attribution.channel` (UTM, referral, postcard, short link [S: T06]) from Agent 01 | `booked_at` |
| M-FUNNEL | Booking funnel | Portal visits (unique visitor-days, Agent 01 `portal.visit.recorded`) → sessions started → contact captured → booked. Step conversion = next ÷ previous | Event date |
| M-ABANDON | Abandoned sessions / recovered | Abandoned = sessions that captured contact details and then reached `abandoned`. Recovered = abandoned sessions whose contact booked within 7 days (any source). Rate = recovered ÷ abandoned | Abandon date |

#### 5.10.4 Customer metrics
| ID | Name | Formula |
|---|---|---|
| M-CUST-NEW | New customers | Customers whose **first completed job** falls in the period. Using created date instead would count imports [S: T18] as new |
| M-CUST-CREATED | Customers added | Customers created in the period, any source |
| M-CUST-RET | Returning customers | Customers with a completed job in the period **and** a completed job before the period start |
| M-CUST-ACTIVE | Active customers | Customers with ≥1 completed job in the trailing 365 days at period end |
| M-REBOOK | Rebook rate | Cohort = jobs completed in the period. A job is "rebooked" if the same customer had another non-canceled job with `booked_at ∈ (completed_at − 0, completed_at + N days]`, **or** already held a future job at completion time (recurring or subscription). Rate = rebooked ÷ cohort. N defaults to 60. A cohort less than N days old is labeled "maturing" |
| M-LTV | Customer lifetime value | Σ M-NET for the customer, all time |
| M-BEST-CUST | Best customers [S: T09] | Rank by M-NET in the period. Tie-break: more completed jobs, then most recent completion, then name. Optional switch to rank by job count |
| M-CHURN-RISK | Lapsed customers | Active customers whose days since last completed job exceed 1.5 × their median interval between jobs (minimum 2 prior jobs), or exceed 120 days for one-time customers. Feeds the "haven't booked in a while" audiences [S: T10] |
| M-RATING-GIVEN | Rating given | Average of ratings this customer gave [S: T09 profile ratings] |

#### 5.10.5 Service, area, team metrics
| ID | Name | Formula |
|---|---|---|
| M-SVC-REV | Top services [S: T09] | Per service: Σ net line revenue. Each line's revenue = line gross − allocated discount − allocated refund (allocations from Agent 05, largest remainder). Also shows quantity, jobs containing the service, margin (line cost), and share of M-NET |
| M-ADDON-ATTACH | Add-on attach rate | Completed jobs with ≥1 add-on ÷ M-JOBS-DONE. Add-on revenue per job = add-on net ÷ jobs done [S: T07] |
| M-AREA | Service areas [S: T09, T11] | Per zone or shop: M-JOBS-DONE, M-NET, travel fees (4020), average travel minutes. The zone is the **snapshot** on the job at booking time, so later redrawing a zone does not rewrite history. Jobs outside any zone (manual override [S: T11]) go to "Outside areas" |
| M-W-REV | Revenue generated (worker) [S: T09, T17] | Accrual: for each completed job, line-level worker assignment if the job is split by service [S: LLMS], otherwise job M-NET × worker `share_bp` (Agent 09/05 shares; default equal). Largest-remainder cents go to the lead worker. Cash view = Agent 05 RevenueAttribution rows |
| M-W-JOBS | Bookings completed (worker) [S: T09] | Count of completed jobs where the worker was assigned at completion. A split job gives **1 full count to each worker** (display) and a fractional `share_bp` count (option for fairness) |
| M-W-REVIEWS | Reviews received (worker) [S: T09] | Count of ratings received in the period on jobs the worker was on, plus average rating. For **ranking**, use the Bayesian average `(C·m + Σr) ÷ (C + n)`, where m = business average over the trailing 365 days and C = 5. This stops a worker with a single 5★ rating from ranking first |
| M-W-UTIL | Utilization (worker) | Scheduled minutes of the worker's completed jobs ÷ the worker's available minutes |
| M-W-HOURS | Hours worked | Σ approved timesheet hours, net of unpaid breaks [S: T03] |
| M-W-REV-HR | Revenue per labor hour | M-W-REV ÷ M-W-HOURS (shown only when hours > 0) |
| M-W-UPSELL | Upsell revenue | Σ net of lines added by the worker after booking (`addedByWorkerId`, Agent 04) [S: T07 "upsells … in the worker app POS"] |
| M-W-TIPS | Tips | Agent 05 tip distribution rows |

#### 5.10.6 Leaderboard, MVP, reviews, presence
- **Leaderboard metrics** [S: T09]: bookings completed (M-W-JOBS), reviews received
  (M-W-REVIEWS, count; average and Bayesian average shown alongside), revenue generated
  (M-W-REV). We add **Composite** [I]: the mean of each worker's percentile rank across the three
  metrics, with configurable weights.
- **Eligibility:** workers active at any point in the period with ≥ 1 completed job (minimum
  configurable). Deactivated workers keep their history but are hidden from the current
  period after deactivation (Agent 09 contract).
- **Ranking:** descending by value. Ties share a rank (competition ranking "1, 1, 3"). Display order
  among ties uses a secondary metric (revenue → jobs → name).
- **MVP view** [S: LLMS]: rank 1 on Composite (default) or on the selected metric, with a
  minimum of 5 completed jobs in the period. If nobody qualifies, the view says "Not enough
  activity yet".
- **Business review metrics:** count, average, star distribution, share of 5★, response rate
  (low-rating tasks resolved ÷ low ratings), public-link click rate (Google clicks ÷ ratings).
- **Active now** [S: LLMS]: customers = distinct contacts or anonymous visitors with a booking-site
  or customer-app heartbeat in the last 5 minutes (from Agent 01). Team = workers with a
  worker-app heartbeat in the last 5 minutes **or** currently clocked in (Agent 09).
- **Team availability** [S: LLMS]: from Agent 09/03, today's free/busy per worker with next
  free slot.

#### 5.10.7 Marketing metrics
Defined in 5.7 (campaign and drip metrics, attribution). In addition:
- **SMS cost** = Σ segments × (provider rate + carrier fee).
- **Email cost** = emails × rate.
- **Postcard cost** = pieces × (print + postage).
Shown per campaign, per drip and per month. **List growth** = net change in marketing-consented
contacts per channel. **Opt-out rate** = opt-outs ÷ delivered marketing messages.

### 5.11 Dashboard tile catalog
[S: T09 "visual tiles that are easy to read and customize"; LLMS new dashboard] — tile set and
sizes `[I]`.

| Tile ID | Title | Viz | Metric(s) | Default size (cols×rows) | Default on | Tier |
|---|---|---|---|---|---|---|
| T-REV-TREND | Revenue trend [S: T09] | Line or column with comparison line | M-NET by day/week/month | 8×3 | Yes | All |
| T-KPI | KPI row | Stat tiles with sparkline and delta | M-NET, M-JOBS-DONE, M-AOV, M-CUST-NEW, M-REBOOK, avg rating | 12×1 | Yes | All |
| T-GOALS | Goals [S: LLMS] | Progress bars with pace marker | Goal metrics | 12×1 | Yes | All |
| T-UPCOMING | Upcoming schedule load | Column chart, next 14 days | M-JOBS-SCHED and booked value | 6×2 | Yes | All |
| T-TOP-SVC | Top services [S: T09] | Horizontal bar | M-SVC-REV top 5 | 4×2 | Yes | All |
| T-BEST-CUST | Best customers [S: T09] | Ranked list | M-BEST-CUST top 5 | 4×2 | Yes | All |
| T-TOP-WORKERS | Top team members / MVP [S: T09, LLMS] | Ranked list with podium | Leaderboard metric | 4×2 | Yes | All |
| T-AREAS | Service areas [S: T09] | Bar list or map choropleth (top tier) | M-AREA | 4×2 | Yes | All (map: Top) |
| T-SOURCES | Booking sources [S: T05] | Donut, ≤ 6 slices + "Other" | M-SOURCE | 4×2 | Yes | All |
| T-FUNNEL | Booking funnel | Funnel bars | M-FUNNEL, M-ABANDON | 6×2 | No | All |
| T-REVIEWS | All reviews [S: LLMS] | Feed and average | Latest reviews, M-W-REVIEWS business-wide | 4×3 | Yes | All |
| T-PHOTOS | All photos [S: LLMS] | Grid of latest job photos (Agent 04) | — | 4×2 | Yes | All |
| T-TEAM-AVAIL | Team availability [S: LLMS] | Timeline strips | 5.10.6 | 6×2 | Yes | All |
| T-NOTIFS | Notifications [S: LLMS] | Feed | A-series in-app items | 4×3 | Yes | All |
| T-ACTIVE | Active now [S: LLMS] | Counters and avatars | 5.10.6 | 4×1 | Yes | All |
| T-FORECAST | Forecast [S: LLMS] | Line with confidence band | 10.12 | 8×3 | No | Mid |
| T-CASH | Money in / payouts | Stat and bar | M-COLLECTED, M-TIPS, M-PROC-FEE, next payout (Agent 05) | 4×2 | No | All |
| T-AR | Unpaid invoices | Stat and aging buckets | M-AR (0–30 / 31–60 / 61–90 / 90+) | 4×2 | No | All |
| T-SUBS | Subscriptions | Stat and line | M-MRR, active subscriptions, churn | 4×2 | No | All |
| T-MKT | Marketing results | Table | Top campaigns and drips by attributed revenue | 6×2 | No | Mid |
| T-LEADS | Leads pipeline | Stage bars | Agent 07 lead stages, abandoned count | 4×2 | No | All |
| T-UTIL | Utilization | Gauge per worker | M-UTIL, M-W-UTIL | 4×2 | No | Mid |
| T-HEATMAP | Job heatmap [S: LLMS Pro "heatmaps"] | Map density | Completed jobs by location | 8×4 | No | Top |
| T-CUSTOM | Custom metric [S: LLMS Pro "custom analytics"] | Any | User-defined metric over the semantic layer (10.10.5) | any | No | Top |
| T-MARGIN | Profit | Stat and bar | M-MARGIN, M-COST, M-LABOR | 4×2 | No | Mid |

Tile rules:
- A tile's period follows the dashboard range unless the tile overrides it (the override is shown
  as a chip).
- Each tile has at most 1 query and is served from rollups (10.10).
- Every tile exposes "Download CSV" and "Download PNG" [S: T09] and an "i" popover with the
  metric definition and basis.

### 5.12 Messaging providers & budget (cost model)
All prices are approximate public list prices from memory **[I — re-quote before committing]**. The
design is provider-agnostic (adapter per provider, 7.7 `ProviderAccount`).

| Need | Primary (budget) | Alternates | Approximate cost basis | Notes |
|---|---|---|---|---|
| SMS/MMS US/CA (two-way, 10DLC and toll-free) | Telnyx | Twilio, Bandwidth, Plivo, AWS End User Messaging | Telnyx ≈ $0.004 per segment; Twilio ≈ $0.008 per segment; plus carrier pass-through ≈ $0.002–0.005 per segment on registered 10DLC; numbers ≈ $1–2 per month; MMS ≈ 2–4× SMS | Choose a provider with an ISV/reseller 10DLC API (subaccounts per business) |
| 10DLC registration | via SMS provider (TCR) | — | Brand ≈ $4 one-time; secondary vetting ≈ $40 (optional); campaign vetting ≈ $15 one-time; campaign ≈ $1.50–$10 per month | Sole-proprietor brand for businesses without an EIN (1 campaign, 1 number, low throughput) |
| Toll-free verification | via SMS provider | — | Verification fee ≈ $0 at most providers; TF number ≈ $2 per month; per-message rates similar to 10DLC | Recommended starter default: no monthly campaign fee, faster approval |
| Transactional email | Amazon SES | Postmark (best deliverability for transactional), Resend, Mailgun, SendGrid | SES ≈ $0.10 per 1,000; Postmark ≈ $15 per 10k | Separate IP pools or streams for transactional and marketing |
| Marketing email | Amazon SES (separate configuration set and IP pool) | Mailgun, SendGrid | Same as above | One-click unsubscribe headers required for bulk senders by Gmail and Yahoo (since 2024) [I] |
| Push | FCM (Android and web), APNs (iOS) direct | OneSignal, Expo (if the app stack chooses it) | Free | Branded apps need per-tenant credentials (Agent 01/11) |
| Postcards | Lob | PostGrid, Click2Mail | ≈ $0.60–$1.10 per 4×6 including postage, by mail class and volume | Address verification (CASS/NCOA) included |
| Link shortening and tracking | Self-hosted on tenant domain (Agent 01) | — | ~0 | Public shorteners are filtered by carriers |
| Reviews (public) | Google Business Profile API | — | Free (OAuth per business) | Optional review fetch and reply |
| Phone number intelligence | Provider lookup (line type, carrier) | — | ≈ $0.005 per lookup | Run once per new number, cached, so landlines are not texted |
| Reassigned Numbers Database | FCC RND | — | Subscription tiers | Top tier, for consent older than 12 months |
| Optional AI copy / intent | Platform LLM provider | — | Per token | Used by AI marketing builder [S: LLMS] and opt-out intent detection |

**Per-business monthly cost formula:**
`SMS = Σ_messages segments × (rate + carrier_fee) + numbers × number_fee + campaign_fee`.

**Worked example for a starter business** (100 jobs per month) [I]:
- Message mix: N-B01 + N-B04 + N-D01 + N-R01 ≈ 4 SMS per job, × 1.3 average segments = 520
  segments.
- Cost at Telnyx: 520 × ($0.004 + $0.003) ≈ **$3.64**.
- Fixed costs: TF number $2 → about **$6 per month total**.
- Email: ≈ 600 transactional emails ≈ **$0.06**.
- Result: a starter plan can include about 500 SMS segments and 5,000 emails and stay profitable.

Budget controls:
- Per-business monthly cap with alerts at 50%, 80% and 100% (A-17).
- At 100%: **marketing pauses**, transactional continues. Owner can choose "pause SMS
  transactional too, fall back to email".
- Overage is billed at pass-through plus margin (platform billing outside this module).

**Failover realities** [I]:
- **Email** can fail over between providers. Domain authentication must be set up for both
  (dual DKIM).
- **SMS cannot fail over transparently.** A number and its 10DLC campaign live on one provider. The
  fallback for a transactional SMS outage is email or push, not a second SMS provider.
- Platform-level SMS redundancy would need a second registered number per business on a second
  provider. That costs double the fees, so it is offered only on the top tier.

### 5.13 Tenant branding of every outbound surface (contract with Agent 11)
[Owner requirement 2026-11-13: emails and SMS links match tenant branding] — mechanics `[I]`.
1. **Brand source.** All rendering reads Agent 11's `BrandTheme` (versioned):
   - light and dark palettes (full token sets);
   - logo variants per placement, including an **email-header logo** and a **dark-mode logo**,
     each with sizing controls;
   - custom fonts (heading and body);
   - radius, shadow and spacing tokens;
   - icon set;
   - copy overrides.
   Each `OutboundMessage` stores `brand_theme_version` so the email a customer received can be
   reproduced exactly.
2. **Email adaptation.** Email clients are limited, so the renderer maps tokens:
   - **Colors:** palette → inline styles. Dark mode → `color-scheme` meta and
     `@media (prefers-color-scheme: dark)` overrides for clients that support it (Apple Mail,
     iOS, some Outlook versions). Gmail forces its own inversion, so logos ship with
     transparent-safe variants.
   - **Fonts:** uploaded custom fonts → `@font-face` (works in Apple Mail and iOS) with a mapped
     fallback stack for Gmail and Outlook. Agent 11 stores a "closest web-safe fallback" per font.
   - **Shape and depth:** radius → button border-radius (VML fallback for Outlook). Shadows are
     dropped in email; flat elevation is used instead.
   - **Icons:** SVG icons are rasterized to PNG at 2× (Gmail strips SVG).
   - **Accessibility:** the contrast checker runs on the **email-adapted** palette. If a tenant
     palette fails 4.5:1 for button text, the renderer picks black or white text automatically
     and warns in the editor.
3. **Copy overrides.** Every customer-facing label in system templates (button text such as "Manage
   booking" and "Pay now", headings, footers) is a key in Agent 11's copy-override registry.
   Template bodies the business edits are their own copy. Default copy resolves through overrides
   at render time.
4. **SMS links on the tenant's domain.** Every SMS or email link (booking, manage, pay, tracking,
   review, preference, unsubscribe, resume booking, referral, short links) is generated on the
   tenant's **branded host**:
   - the custom domain (e.g. `book.acme.com`, Agent 01) when it is active;
   - otherwise the tenant's platform subdomain.
   Link format is `https://{host}/s/{token}` (short) or a route path. If the custom domain
   detaches (`portal.domain.detached`), new links use the subdomain. Old links keep working
   through the platform redirect (Agent 01 keeps the legacy host alive).
   Each SMS carries the **business name prefix** from `BrandTheme.display_name` (or an SMS-specific
   short name, ≤ 20 characters).
5. **Landing pages** for those links (tracking, rating, preference center, unsubscribe, chat, pay)
   render with the tenant theme. They honor the visitor's light or dark preference with a toggle
   (Agent 11 requirement) and are responsive (phone first).
6. **Push.** Tenant-branded app (top tier): uses the tenant app icon and name. Otherwise the
   platform app uses the business name in the title.
7. **Postcards.** The designer starts from the brand theme (CMYK conversion warning for colors out
   of print gamut; minimum logo resolution 300 dpi at print size).
8. **Platform branding.** "Powered by" footers in emails and landing pages can be removed on the
   tier Agent 01/11 defines (Fieldd: Pro [S: LLMS]).
9. **Staff-facing messages** (W- and A-series emails) use the business theme too (the business's CRM
   workspace is branded per owner requirement). Platform-account emails (billing for our own
   subscription) use platform branding.

### 5.14 Money, rounding and numeric display in reports
1. All stored values are integer minor units (`bigint`). Rollups store sums only, never
   averages.
2. Averages and ratios are computed at query time from integer sums and counts. Display rounding
   is half-up at the final step only:
   - money to cents;
   - percentages to 1 decimal place;
   - ratings to 1 decimal place (2 in CSV).
3. Splits (worker shares, discount and refund allocation to lines) use **largest remainder**. The
   allocations always sum exactly to the source amount. The remainder goes to the lead worker or
   the largest line (consistent with Agent 05).
4. CSV exports:
   - money as decimal strings with the currency code column (`12.50`, `USD`), never float;
   - dates in ISO 8601 business-local with an offset;
   - a header comment row with the basis (accrual/cash), time zone and generation time.
5. Percent change versus the previous period: if the previous value is 0, show "—" (never ∞ or
   NaN). For negative bases, show the absolute change.

### 5.15 Ownership reconciliation (with specs already written)
- **Consent ledger:** Agent 07 owns the **system of record** (07 §"consent ledger", events
  `customer.consent.changed`). Agent 08:
  - **captures** keyword, unsubscribe, complaint and bounce evidence and writes it **through
    07's command** `customer.consent.record` (schema = 5.6.1 `ConsentEvent`);
  - **enforces** consent at send time from a local read projection `SendEligibility`, updated
    from `customer.consent.changed` and written synchronously on opt-out, so a STOP takes
    effect even before the projection catches up;
  - owns the **email/phone suppression list** (bounces, complaints, carrier blocks).
- **Templates for quotes and invoices:** Agent 04 owns the document style model. Agent 08 owns the
  email and SMS templates that carry them [S: T04] (matches 04 §"Settings → Communication → Email").
- **Abandoned-booking capture:** Agents 01/07 own sessions and leads. 08 owns the drip that
  follows up.
- **Masked customer↔worker messaging:** Agent 09 requests a relay (09 contact-visibility rule);
  08 provides it via the business number (4.14, 7.5).
