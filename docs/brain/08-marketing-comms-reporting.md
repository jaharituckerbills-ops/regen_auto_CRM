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

---

## 6. State machines
All transitions are `[I]` designs. Each machine persists `state`, `state_changed_at` and a `version`
(optimistic concurrency). Every transition appends to an audit trail.

### 6.1 OutboundMessage
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `intent.created` | idempotency key not already present | `pending` | Row inserted; key unique index |
| `pending` | `eligibility.checked` | consent and suppression OK | `ready` | — |
| `pending` | `eligibility.checked` | blocked (no consent, opted out, suppressed, invalid endpoint, landline for SMS) | `suppressed` | `comms.message.suppressed{reason}`; fallback channel intent if the entry allows one |
| `ready` | `quiet_hours.checked` | outside allowed window | `deferred` | `not_before = next_allowed()`; timer |
| `ready` / `deferred` | `frequency.checked` | cap exceeded (marketing) | `deferred` (drip) / `suppressed` (broadcast) | Reason `frequency_cap` |
| `deferred` | `timer.fired` | still eligible (re-check consent) | `ready` | — |
| `ready` | `render.ok` | — | `rendered` | Body, subject and variables snapshot stored; `brand_theme_version` |
| `ready` | `render.failed` | required variable missing / template error | `failed` | `comms.message.failed{render}`; owner alert if systemic (>5 in 10 min) |
| `rendered` | `provider.accepted` | — | `sent` | `provider_message_id`; cost estimate; `comms.message.sent` |
| `rendered` | `provider.error` | retryable (5xx, timeout, 429) and attempts < 5 | `rendered` (retry with backoff) | Same provider idempotency key |
| `rendered` | `provider.error` | permanent (invalid number, unsubscribed at provider, content rejected) | `failed` | Fallback if allowed |
| `sent` | `dlr.delivered` / email `delivered` | — | `delivered` | Cost finalized (segments) |
| `sent` | `dlr.undelivered` / `bounce.hard` | — | `failed` | Suppression add (hard bounce, invalid number); fallback |
| `sent` | `bounce.soft` | — | `sent` (soft count++) | After 3 soft bounces in 7 days → suppression for 30 days |
| `delivered` | `email.complaint` | — | `delivered` (+flag) | Marketing revoke; global complaint suppression |
| `pending` … `deferred` | `subject.canceled` (job canceled, version superseded) | — | `canceled` | — |
| `sent` | no DLR within 72h | — | `sent_unconfirmed` | Treated as delivered for metrics with a flag |

Engagement events (`opened`, `clicked`, `replied`, `unsubscribed`) are stored as `MessageEvent`
rows. They are **not** states.

### 6.2 ScheduledNotification (durable timer for catalog entries)
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `schedule` | — | `scheduled` | `due_at`, `subject_version` |
| `scheduled` | `reschedule` (subject changed) | — | `scheduled` | `due_at` and `subject_version` updated in place (upsert by `(catalog_id, subject_id, recipient_id, slot)`) |
| `scheduled` | `cancel` | — | `canceled` | — |
| `scheduled` | `due` | `subject_version` equals the live version and guards pass | `dispatched` | Creates an OutboundMessage intent |
| `scheduled` | `due` | version mismatch or subject state invalid | `skipped` | Reason logged |
| `dispatched` | — | — | terminal | — |

### 6.3 Campaign
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `draft` | `schedule` | compliance checklist green and user has `marketing.campaign.send` | `scheduled` | `marketing.campaign.scheduled` |
| `draft` | `send_now` | same | `sending` | Audience snapshot |
| `scheduled` | `time.reached` | sender still verified | `sending` | Snapshot audience |
| `scheduled` | `time.reached` | sender not verified / budget exhausted | `blocked` | A-17 |
| `scheduled` | `cancel` | — | `canceled` | — |
| `scheduled` / `blocked` | `edit` | — | `draft` | — |
| `sending` | `pause` | — | `paused` | Stop dequeue |
| `paused` | `resume` | quiet-hours recomputed | `sending` | — |
| `sending` / `paused` | `cancel` | — | `canceled` | Remaining recipients become `canceled` |
| `sending` | `all_recipients_terminal` | — | `sent` | `marketing.campaign.completed` |
| `sending` | `failure_rate > 20%` over the first 200 sends or `complaint_rate > 0.3%` | — | `paused_by_system` | A-15 / A-17; manual review |
| `sent` | `archive` | — | `archived` | — |
| Postcard sub-states | vendor webhooks | — | `submitted → in_production → mailed → in_transit → delivered / returned` (per piece) | Costs posted |

### 6.4 DripCampaign and DripVersion
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `draft` | `launch` | label unique; ≥ 1 message step; senders verified for the channels used; compliance lint passes | `live` | Freeze `DripVersion` v1; `marketing.drip.launched` |
| `live` | `edit_publish` | lint passes | `live` | New version vN+1; migration choice (4.12 step 11) |
| `live` | `pause` | — | `paused` | Due steps held |
| `paused` | `resume` | — | `live` | Held steps processed (skip option) |
| `live` / `paused` | `archive` | — | `archived` | Active enrollments → `exited(drip_archived)` |
| `live` | system: sender suspended / budget cap | — | `paused_by_system` | A-15/A-17 |
| `paused_by_system` | issue resolved and `resume` | — | `live` | — |

### 6.5 Enrollment
| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `trigger.matched` | eligible, re-entry policy allows, no active enrollment in this drip | `active` | `marketing.enrollment.started`; `step_index = 0` |
| — | `trigger.matched` | not eligible | — (no row; counter `ineligible++`) | — |
| `active` | `step.message` | — | `executing` | Pipeline intent `drip:{enr}:{stepId}` |
| `executing` | `intent.accepted` (any terminal message outcome) | more steps remain | `active` (next step) | — |
| `active` | `step.delay` / `step.wait_until` | — | `waiting` | DurableTimer `due_at` |
| `waiting` | `timer.fired` | drip live | `active` | — |
| `waiting` | `timer.fired` | drip paused | `held` | — |
| `held` | `drip.resumed` | — | `active` (or skip-ahead) | — |
| `waiting` | `event` (for wait-until-event) | event matches | `active` | Cancel timer |
| any non-terminal | `exit_condition.met` | — | `exited` | Reason stored; `marketing.enrollment.exited`; cancel timers |
| any non-terminal | `contact.merged` | survivor not enrolled | (moved to survivor) | — |
| `active` | no more steps | — | `completed` | `marketing.enrollment.completed` |
| `executing` | worker crash | lease expired | `active` (re-run step; idempotent) | — |

### 6.6 Channel consent (projection per endpoint × purpose; ledger owned by Agent 07)
| State | Event | Next | Notes |
|---|---|---|---|
| `unknown` | transactional context (number given at booking) | `implied_txn` | Transactional only |
| `unknown` / `implied_txn` | checkbox / keyword / attested consent | `pending_confirm` (if double opt-in on) / `granted` | Evidence required |
| `pending_confirm` | reply YES within 72h | `granted` | N-M03 |
| `pending_confirm` | 72h elapsed | prior state | — |
| `granted` | STOP / unsubscribe / complaint / revoke | `revoked` | STOP revokes **all** SMS from that sender, including transactional |
| `implied_txn` | STOP | `revoked` | — |
| `revoked` | START / UNSTOP / YES / new written consent | `granted` (marketing) or `implied_txn` | Staff cannot do this without evidence |
| any | carrier: number deactivated / reassigned | `invalid` | Requires a new number |
| any (email) | hard bounce | `suppressed` | Clears if the customer re-verifies the address |

### 6.7 Sender registration (10DLC brand/campaign, toll-free, email domain)
| Entity | States |
|---|---|
| TenDlcBrand | `draft → submitted → (verified / unverified / vetting_pending → vetted) → suspended` |
| TenDlcCampaign | `draft → submitted → pending_mno_review → approved / rejected (reason) → suspended / expired` |
| TollFreeVerification | `draft → submitted → in_review → approved / rejected (resubmittable)` |
| PhoneNumber | `ordering → active → (pending_registration: SMS blocked or filtered) → sms_enabled → released` |
| EmailDomain | `pending_dns → verifying → verified / failed (after 72h) → degraded (DMARC/SPF break detected by daily check)` |
Sending gate: an SMS from a business number is allowed only when the number is `sms_enabled`
(campaign approved or TF approved).

### 6.8 Conversation
| State | Event | Next | Side effects |
|---|---|---|---|
| — | first inbound or outbound | `open` | Assignment rule |
| `open` | staff reply | `open` (waiting_on = customer) | SLA timer stop |
| `open` | close | `closed` | — |
| `open` | snooze(until) | `snoozed` | Timer |
| `snoozed` | timer or new inbound | `open` | Notify assignee |
| `closed` | new inbound | `open` | Reassign to last assignee if still active, else unassigned |
| any | contact merged | (thread merged into survivor's conversation per endpoint) | — |

### 6.9 ReviewRequest and Review
| State | Event | Next |
|---|---|---|
| `scheduled` | due and guards OK | `sent` |
| `scheduled` | job reopened / canceled / opted out / cooldown | `skipped` |
| `sent` | rating submitted | `rated` |
| `sent` | +48h, no rating | `reminded` (N-R02) |
| `reminded` | rating | `rated` |
| `sent` / `reminded` | +7 days | `expired` |
| `rated` | Google link clicked | `rated_public_clicked` |
| `rated` | rating ≤ threshold | (+ task `low_rating_open` → `resolved`) |
Review entity: `active → hidden (owner hides from site display; still counted) → deleted
(customer privacy erasure only)`.

### 6.10 Referral
`invited → signed_up → booked → qualified → rewarded`. Side branches:
`rejected(self_referral / existing_customer / cap)`; `reversed` (refund within the reversal window).

### 6.11 Goal
| State | Event | Next |
|---|---|---|
| `active` | progress ≥ target | `achieved` (A-13 once) |
| `active` | period end, progress < target | `missed` |
| `achieved` / `missed` | recurring goal and new period begins | new period instance `active` |
| any | archive | `archived` |

### 6.12 ExportJob
`queued → running → ready (signed URL, 7 days) → expired`. Errors go `running → failed` (retry up to 2,
then A-20 with error). Cancel: `queued/running → canceled`.

### 6.13 Rollup partition (reporting freshness)
`fresh → dirty (fact arrived for the partition) → refreshing → fresh`. A full rebuild runs on
`stale_rebuild` (tz change, metric definition version bump): `fresh → rebuilding → fresh`.
Queries during a rebuild serve the previous version (double-buffered).

---

## 7. Data model
TypeScript-style, stack-agnostic. Common fields on every entity: `id: string` (ULID),
`business_id: string` (tenant key; every query is tenant-scoped), `created_at`, `updated_at`
(UTC ISO), `version: number`. Money: `MinorUnits = number` (integer). `Tz = string` (IANA).
`[I]` throughout.

### 7.1 Notification catalog & settings
```ts
type Channel = 'email' | 'sms' | 'push' | 'in_app' | 'postcard';
type QuietClass = 'CRIT' | 'TXN' | 'MKT' | 'TEAM' | 'OPS';
type OptClass = 'LOCK' | 'TXN' | 'MKT' | 'OPS' | 'CONV';
type RecipientRole = 'customer' | 'payer' | 'gift_recipient' | 'lead' | 'assigned_workers'
  | 'worker_self' | 'approvers' | 'owners' | 'requester' | 'audience' | 'enrollee';

interface NotificationDefinition {          // platform-level, versioned; seeded from 5.1
  id: string;                               // 'N-B01'
  def_version: number;
  name: string;
  audience: 'customer' | 'team' | 'owner';
  area: string;                             // 'booking', 'day_of', 'quotes', ...
  trigger: { event?: string; timer?: { anchor: string; offset_minutes: number; range: [number, number] } };
  recipient_role: RecipientRole;
  default_channels: Channel[];
  optional_channels: Channel[];
  fallback?: Channel[];                     // ordered
  quiet_class: QuietClass;
  critical_if_within_hours?: number;        // TXN* rule
  opt_class: OptClass;
  editable: { toggle: boolean; channels: boolean; timing: boolean; text: boolean };
  variable_set: string[];                   // ['VS-JOB','eta']
  required_variables: string[];
  dedupe: { window_seconds: number; max_per_subject?: number };
  debounce_seconds?: number;
  tier: 'all' | 'mid' | 'top';
  default_templates: Record<Channel, string /* TemplateVersion id */>;
}

interface NotificationSetting {             // per business override
  business_id: string; definition_id: string;
  enabled: boolean;
  channels: Channel[];
  timing?: { offsets_minutes?: number[]; send_at_local?: string /* 'HH:mm' */; threshold?: number };
  template_overrides: Partial<Record<Channel, string /* TemplateVersion id */>>;
  forked_from_def_version?: number;         // for "update available" diff
  updated_by: string;
}

interface UserNotificationPreference {      // staff (A/W series)
  user_id: string; definition_id: string; channels: Channel[]; muted: boolean;
  dnd?: { start_local: string; end_local: string }; digest?: 'none' | 'hourly' | 'daily';
}
```

### 7.2 Templates & rendering
```ts
interface Template {
  id: string; business_id: string | null;   // null = platform default
  kind: 'system' | 'campaign' | 'drip_step' | 'snippet' | 'gallery';
  channel: Channel; name: string; category?: string; // 'holiday','welcome',...
  current_version_id: string; archived: boolean;
}
interface TemplateVersion {
  id: string; template_id: string; number: number;
  subject?: string; preheader?: string;     // email
  blocks?: EmailBlock[];                    // email editor JSON (source of truth)
  html_compiled?: string; text_compiled?: string;
  sms_body?: string; media_urls?: string[]; // sms/mms
  push?: { title: string; body: string; deep_link?: string; image_url?: string };
  postcard?: { size: '4x6' | '6x9' | '6x11'; front: DesignDoc; back: DesignDoc };
  variables_used: string[];
  lint: { level: 'ok' | 'warn' | 'error'; issues: LintIssue[] };
  created_by: string; created_at: string;
}
type EmailBlock =
  | { type: 'header'; logo_placement: 'email_header' }
  | { type: 'heading' | 'text'; content: RichText }
  | { type: 'image'; asset_id: string; alt: string; link?: string }
  | { type: 'button'; label: string; href: LinkSpec }
  | { type: 'service_card'; service_id: string }
  | { type: 'coupon'; discount_code_id: string }
  | { type: 'divider' | 'spacer'; size?: number }
  | { type: 'social' }
  | { type: 'conditional'; when: 'has_marketing_consent' | Expr; children: EmailBlock[] }
  | { type: 'footer'; locked: true };
type LinkSpec = { kind: 'booking' | 'service' | 'offer' | 'manage' | 'pay' | 'review' | 'custom';
  ref?: string; url?: string; track: boolean };
```

### 7.3 Messages, events, scheduling
```ts
interface NotificationIntent {               // transient; persisted as OutboundMessage
  idempotency_key: string; definition_id?: string; campaign_id?: string;
  drip_id?: string; enrollment_id?: string; step_id?: string;
  subject: { type: 'job' | 'quote' | 'invoice' | 'payment' | 'subscription' | 'giftcard'
    | 'timesheet' | 'worker' | 'conversation' | 'review' | 'referral' | 'goal' | 'export' | 'lead';
    id: string; version?: number };
  recipient: { kind: 'contact' | 'person' | 'user' | 'raw'; id?: string; endpoint?: string };
  channel: Channel; purpose: 'security' | 'transactional' | 'conversational' | 'marketing' | 'operational';
  not_before?: string; context: Record<string, unknown>; // event payload refs
}

interface OutboundMessage {
  id: string; business_id: string; idempotency_key: string;           // UNIQUE(business_id, idempotency_key)
  definition_id?: string; campaign_id?: string; drip_id?: string; enrollment_id?: string;
  step_id?: string; conversation_id?: string;
  subject_type: string; subject_id: string; subject_version?: number;
  recipient_contact_id?: string; recipient_person_id?: string; recipient_user_id?: string;
  channel: Channel; endpoint: string;          // E.164 / normalized email / device token id / address id
  purpose: NotificationIntent['purpose'];
  state: 'pending' | 'ready' | 'deferred' | 'rendered' | 'sent' | 'delivered' | 'sent_unconfirmed'
    | 'failed' | 'suppressed' | 'canceled';
  state_reason?: string;
  not_before?: string; recipient_tz: Tz;
  template_version_id?: string; brand_theme_version?: string;
  rendered: { subject?: string; body_text?: string; body_html_ref?: string; media?: string[] };
  sender: { provider_account_id: string; from: string; number_id?: string; email_domain_id?: string };
  provider_message_id?: string; attempts: number; last_error?: string;
  segments?: number; encoding?: 'GSM7' | 'UCS2';
  cost_estimate_minor?: MinorUnits; cost_final_minor?: MinorUnits; cost_currency: string;
  sent_at?: string; delivered_at?: string; failed_at?: string;
  fallback_of?: string;                     // message id this one replaced
  link_tokens: string[];                    // ShortLink ids embedded
}

interface MessageEvent {                    // append-only, high volume (partition by month)
  id: string; business_id: string; message_id: string;
  type: 'queued' | 'sent' | 'delivered' | 'undelivered' | 'bounced_hard' | 'bounced_soft'
    | 'complained' | 'opened' | 'clicked' | 'replied' | 'unsubscribed' | 'failed';
  at: string; provider_payload_ref?: string; link_id?: string; ip_hash?: string;
  is_machine_open?: boolean;                // privacy-proxy heuristic
}

interface ScheduledNotification {
  id: string; business_id: string; definition_id: string;
  subject_type: string; subject_id: string; subject_version: number;
  recipient_role: RecipientRole; slot: string;   // e.g. 'reminder_1'
  due_at: string; state: 'scheduled' | 'dispatched' | 'skipped' | 'canceled';
  // UNIQUE(business_id, definition_id, subject_id, slot)
}

interface DurableTimer {                    // shared by ScheduledNotification, drips, deferred msgs, goals
  id: string; business_id: string; kind: 'notification' | 'drip_step' | 'deferred_message'
    | 'campaign_start' | 'review_request' | 'conversation_snooze' | 'report_schedule';
  ref_id: string; due_at: string;           // indexed (due_at, state)
  state: 'pending' | 'leased' | 'done' | 'canceled';
  lease_owner?: string; lease_expires_at?: string; attempts: number;
}

interface ShortLink {
  id: string; business_id: string; token: string;     // 8–10 chars, base62, UNIQUE per host
  host: string;                                        // tenant branded host at creation
  target_url: string; kind: LinkSpec['kind'] | 'tracking' | 'resume_booking' | 'referral' | 'postcard_qr';
  message_id?: string; contact_id?: string; campaign_id?: string; drip_id?: string;
  expires_at?: string; click_count: number; first_clicked_at?: string;
}
```

### 7.4 Consent, suppression, senders
```ts
interface ConsentEvent {                    // written via Agent 07 ledger command (5.15)
  id: string; business_id: string; contact_id?: string; person_id?: string;
  endpoint_type: 'phone' | 'email' | 'push_device' | 'postal';
  endpoint: string; purpose: 'transactional' | 'marketing' | 'review_requests' | 'all_sms';
  action: 'grant' | 'revoke' | 'confirm' | 'pause';
  method: 'web_checkbox' | 'keyword_reply' | 'verbal_attested' | 'paper_form' | 'import_attested'
    | 'app_toggle' | 'preference_center' | 'one_click_unsubscribe' | 'carrier_stop'
    | 'complaint' | 'hard_bounce' | 'free_text_detected' | 'staff_on_request';
  disclosure_version_id?: string; disclosure_text_hash?: string;
  page_url?: string; ip?: string; user_agent?: string; evidence_ref?: string;
  actor: { type: 'customer' | 'staff' | 'system' | 'carrier'; id?: string };
  occurred_at: string; source_message_id?: string;
}
interface SendEligibility {                 // projection used by the pipeline (fast path)
  business_id: string; endpoint: string; endpoint_type: string;
  sms_all: 'ok' | 'revoked' | 'invalid'; sms_marketing: 'none' | 'pending' | 'granted' | 'revoked';
  email_marketing: 'none' | 'granted' | 'revoked'; review_requests: 'ok' | 'revoked';
  line_type?: 'mobile' | 'landline' | 'voip' | 'unknown'; tz_hint?: Tz; updated_at: string;
}
interface SuppressionEntry {
  id: string; scope: 'business' | 'platform'; business_id?: string;
  endpoint: string; endpoint_type: 'phone' | 'email';
  reason: 'hard_bounce' | 'soft_bounce_threshold' | 'complaint' | 'carrier_block' | 'invalid'
    | 'reassigned' | 'manual'; until?: string; created_at: string;
}
interface ProviderAccount {                 // platform-owned credentials (see 9.6 API key register)
  id: string; provider: 'telnyx' | 'twilio' | 'bandwidth' | 'plivo' | 'ses' | 'postmark' | 'mailgun'
    | 'sendgrid' | 'resend' | 'fcm' | 'apns' | 'lob' | 'postgrid' | 'gbp';
  scope: 'platform' | 'business';           // business = BYO credentials (top tier)
  business_id?: string; secret_ref: string;  // pointer into secrets manager, never the secret
  subaccount_id?: string; status: 'active' | 'disabled'; priority: number;
}
interface PhoneNumber {
  id: string; business_id: string; e164: string; provider_account_id: string;
  type: 'local_10dlc' | 'toll_free' | 'short_code' | 'alphanumeric';
  state: 'ordering' | 'active' | 'pending_registration' | 'sms_enabled' | 'released';
  campaign_id?: string; tf_verification_id?: string; mms: boolean; voice_forward_to?: string;
}
interface TenDlcBrand { id: string; business_id: string; tcr_brand_id?: string;
  entity_type: 'private_profit' | 'public_profit' | 'non_profit' | 'sole_proprietor';
  ein_last4?: string; legal_name: string; state: string; vetting_score?: number; rejection_reason?: string; }
interface TenDlcCampaign { id: string; brand_id: string; tcr_campaign_id?: string;
  use_case: 'mixed' | 'customer_care' | 'marketing' | 'account_notification' | 'low_volume_mixed';
  sample_messages: string[]; opt_in_description: string; opt_in_screenshot_url?: string;
  help_text: string; stop_text: string; state: string; throughput_mps?: number; monthly_fee_minor?: MinorUnits; }
interface TollFreeVerification { id: string; number_id: string; state: string; submitted_payload_ref: string; rejection_reason?: string; }
interface EmailDomain { id: string; business_id: string; domain: string; // e.g. mail.acme.com
  dkim_records: DnsRecord[]; spf_include: string; return_path: DnsRecord; dmarc_recommended: string;
  state: 'pending_dns' | 'verifying' | 'verified' | 'failed' | 'degraded'; last_checked_at: string; }
interface QuietHoursPolicy { business_id: string;
  windows: Record<'TXN' | 'MKT' | 'TEAM', { days: number[]; start: string; end: string }[]>;
  blackout_dates: string[]; marketing_caps: { sms_per_24h: 1; sms_per_7d: number; email_per_24h: number }; }
interface CommsBudget { business_id: string; month: string; cap_minor?: MinorUnits;
  spent_minor: MinorUnits; alerts_sent: number[]; hard_stop_marketing: true; hard_stop_txn_sms: boolean; }
```

### 7.5 Conversations
```ts
interface Conversation {
  id: string; business_id: string;
  kind: 'customer' | 'team_group' | 'team_direct' | 'job_crew';
  // customer threads are keyed by (business, endpoint) for SMS, or by contact for webchat/app
  primary_endpoint?: string; contact_ids: string[]; lead_id?: string;
  linked_job_ids: string[]; title?: string; member_user_ids?: string[];
  state: 'open' | 'snoozed' | 'closed'; snoozed_until?: string;
  assignee_user_id?: string; waiting_on: 'business' | 'customer' | 'none';
  last_message_at: string; last_inbound_at?: string; unread_by: Record<string, number>;
}
interface ConversationMessage {
  id: string; conversation_id: string; business_id: string;
  direction: 'inbound' | 'outbound' | 'internal_note' | 'system';
  channel: 'sms' | 'mms' | 'email' | 'webchat' | 'app' | 'team';
  author: { type: 'contact' | 'user' | 'system' | 'visitor'; id?: string; display_name: string };
  body_text: string; attachments: { asset_id: string; mime: string; bytes: number }[];
  outbound_message_id?: string; provider_message_id?: string; // UNIQUE for inbound dedupe
  job_id?: string; mentions?: string[]; created_at: string; edited_at?: string; deleted_at?: string;
  intent?: 'confirm' | 'reschedule' | 'cancel' | 'opt_out_possible' | 'stop' | 'help' | 'start';
}
interface WebchatSession { id: string; business_id: string; conversation_id: string;
  visitor_id: string; booking_session_id?: string; contact_capture?: { name?: string; email?: string; phone?: string };
  last_seen_at: string; }
```

### 7.6 Marketing: audiences, campaigns, drips, attribution
```ts
interface Audience { id: string; business_id: string; name: string; kind: 'dynamic' | 'snapshot';
  rule?: RuleGroup; snapshot_contact_ids_ref?: string; last_count?: number; last_counted_at?: string; }
type RuleGroup = { op: 'and' | 'or'; rules: (Rule | RuleGroup)[] };
type Rule = { field: string /* from semantic field registry */; cmp: 'eq' | 'neq' | 'in' | 'gt' | 'lt'
  | 'between' | 'within_last_days' | 'not_within_last_days' | 'exists' | 'not_exists'; value?: unknown };

interface Campaign {
  id: string; business_id: string; name: string; channels: Channel[];
  state: 'draft' | 'scheduled' | 'blocked' | 'sending' | 'paused' | 'paused_by_system' | 'sent' | 'canceled' | 'archived';
  audience_id?: string; audience_rule_inline?: RuleGroup;
  template_versions: Partial<Record<Channel, string>>; offer?: { discount_code_id: string };
  schedule: { mode: 'now' | 'at' | 'recipient_local' | 'window'; at?: string; local_time?: string;
    window?: { start: string; end: string } };
  throttle_per_minute?: number; created_by: string; launched_by?: string; launched_at?: string;
  stats_ref: string;                        // CampaignStats rollup row
}
interface CampaignRecipient { campaign_id: string; contact_id: string; channel: Channel;
  verdict: 'eligible' | 'no_consent' | 'unsubscribed' | 'suppressed' | 'no_endpoint' | 'frequency_cap' | 'invalid';
  message_id?: string; }               // PK(campaign_id, contact_id, channel)
interface PostcardOrder { id: string; campaign_id: string; contact_id?: string; address_id: string;
  vendor_id?: string; state: string; qr_link_id: string; cost_minor: MinorUnits; expected_delivery?: string; }

interface DripCampaign { id: string; business_id: string; label: string; // UNIQUE(business_id, label)
  state: 'draft' | 'live' | 'paused' | 'paused_by_system' | 'archived';
  current_version_id: string; template_gallery_id?: string; priority: 1 | 2 | 3 | 4 | 5;
  created_by: string; launched_at?: string; }
interface DripVersion { id: string; drip_id: string; number: number; frozen_at?: string;
  trigger: DripTrigger; filters?: RuleGroup;
  reentry: { mode: 'never' | 'after_completion' | 'after_exit' | 'cooldown'; cooldown_days?: number };
  exit_conditions: ExitCondition[]; goal?: ExitCondition;
  send_timing: { mode: 'asap' | 'window' | 'local_time'; window?: { start: string; end: string }; local_time?: string };
  enroll_existing: boolean; steps: DripStep[]; }
type DripTrigger =
  | { type: 'event'; event: string; where?: Expr }      // e.g. customer.customer.created
  | { type: 'date_fixed'; month: number; day: number; local_time: string; recurring_yearly: boolean; holiday_key?: string }
  | { type: 'date_relative'; field: string; offset_days: number; local_time: string }
  | { type: 'segment_entry'; audience_id: string; exit_on_leave: boolean }
  | { type: 'manual' | 'api' };
type DripStep = { id: string /* stable across versions */ } & (
  | { type: 'email'; template_version_id: string }
  | { type: 'sms'; template_version_id: string }
  | { type: 'push'; template_version_id: string }
  | { type: 'delay'; minutes: number; align_to_send_window: boolean }
  | { type: 'wait_until'; at?: { field: string; offset_minutes: number }; event?: string; timeout_minutes: number }
  | { type: 'branch'; condition: Expr; then_step_id: string; else_step_id: string }
  | { type: 'action'; action: 'add_tag' | 'remove_tag' | 'create_task' | 'notify_user' | 'set_lead_stage' | 'issue_code'; params: Record<string, unknown> }
  | { type: 'exit' });
type ExitCondition = { type: 'event'; event: string; where?: Expr } | { type: 'rule'; rule: RuleGroup };

interface Enrollment { id: string; business_id: string; drip_id: string; version_id: string;
  contact_id: string; enrollment_key: string;         // UNIQUE(drip_id, contact_id, enrollment_key)
  state: 'active' | 'executing' | 'waiting' | 'held' | 'completed' | 'exited';
  step_id?: string; step_entered_at: string; exit_reason?: string; trigger_event_id?: string;
  trigger_context: Record<string, unknown>;           // e.g. booking session id, service name
  started_at: string; ended_at?: string; lease?: { owner: string; expires_at: string }; }
interface EnrollmentStepRun { enrollment_id: string; step_id: string; run_seq: number;
  outcome: 'sent' | 'skipped' | 'suppressed' | 'failed' | 'action_ok'; message_id?: string; at: string; }

interface Touch { id: string; business_id: string; contact_id: string;
  source_type: 'campaign' | 'drip'; source_id: string; step_id?: string; message_id?: string;
  kind: 'click' | 'code' | 'scan' | 'delivered' | 'mailed'; at: string; }
interface Attribution { job_id: string; business_id: string;          // PK(job_id)
  source_type: 'campaign' | 'drip' | 'referral' | 'none'; source_id?: string; touch_id?: string;
  rule: 'direct_token' | 'code' | 'click' | 'scan' | 'delivered'; booked_at: string;
  pipeline_value_minor: MinorUnits; recognized_value_minor: MinorUnits; // from rollups
  influenced_source_ids: string[]; model_version: number; }
```

### 7.7 Reviews, referrals
```ts
interface ReviewRequest { id: string; business_id: string; job_id: string; contact_id: string;
  state: 'scheduled' | 'sent' | 'reminded' | 'rated' | 'rated_public_clicked' | 'expired' | 'skipped';
  token: string; sent_message_id?: string; }
interface Review { id: string; business_id: string; job_id: string; contact_id: string;
  worker_ids: string[]; rating: 1 | 2 | 3 | 4 | 5; comment?: string; source: 'internal' | 'google_matched';
  public_link_clicked_at?: string; visibility: 'active' | 'hidden'; response?: string;
  low_rating_task_state?: 'open' | 'resolved'; created_at: string; }
interface ReferralProgram { business_id: string; enabled: boolean;
  referrer_reward: RewardSpec; friend_reward: RewardSpec; min_qualifying_net_minor: MinorUnits;
  reversal_window_days: number; cap_per_referrer_per_year: number; budget_cap_minor?: MinorUnits; }
type RewardSpec = { kind: 'credit' | 'discount_code' | 'gift_card'; amount_minor?: MinorUnits; percent?: number };
interface ReferralCode { id: string; business_id: string; contact_id: string; code: string; // UNIQUE ci
  created_at: string; disabled: boolean; }
interface Referral { id: string; business_id: string; code_id: string; friend_contact_id?: string;
  first_job_id?: string; state: 'invited' | 'signed_up' | 'booked' | 'qualified' | 'rewarded'
    | 'rejected' | 'reversed'; reject_reason?: string; reward_ref?: string; }
```

### 7.8 Reporting
```ts
// Fact contributions: every source event produces signed rows; rollups = SUM(contributions).
interface FactContribution {
  business_id: string; source_event_id: string; line_no: number;      // PK — idempotent
  metric_family: 'money' | 'job' | 'customer' | 'review' | 'marketing' | 'hours' | 'funnel';
  local_date: string;                       // business-local YYYY-MM-DD at business tz version
  basis: 'accrual' | 'cash' | 'event';
  dims: { worker_id?: string; service_id?: string; addon_id?: string; zone_id?: string; shop_id?: string;
    source?: string; customer_id?: string; job_type?: string; campaign_id?: string; drip_id?: string;
    payment_method?: string; currency: string };
  measures: Record<string, number>;         // e.g. { gross_minor: 12000, disc_minor: -1000, jobs_done: 1 }
  definition_version: number; ingested_at: string;
}
interface RollupDaily {                     // one table per grain group; PK(business_id, local_date, dim tuple, basis)
  business_id: string; local_date: string; basis: string; dim_key: string; // hashed dims
  dims: Record<string, string>; measures: Record<string, number>; refreshed_at: string; }
interface RollupPartitionState { business_id: string; month: string; state: 'fresh' | 'dirty' | 'refreshing' | 'rebuilding';
  watermark_event_seq: number; definition_version: number; }
interface DashboardLayout { id: string; business_id: string; owner: { type: 'user' | 'business_default'; id: string };
  tiles: { tile_id: string; instance_id: string; x: number; y: number; w: number; h: number;
    settings: { metric?: string; group_by?: string; filters?: RuleGroup; period_override?: string; chart?: string } }[];
  range_default: string; updated_at: string; }
interface SavedReportView { id: string; business_id: string; name: string; page: string;
  filters: Record<string, unknown>; group_by: string; columns: string[]; chart: string;
  shared_with: 'me' | 'admins' | 'all_staff'; created_by: string; }
interface Goal { id: string; business_id: string; name: string; metric_id: string; // from goal-eligible metrics
  scope: { type: 'business' | 'worker' | 'zone' | 'service'; id?: string };
  period: { type: 'week' | 'month' | 'quarter' | 'year' | 'custom'; start: string; end: string; recurring: boolean };
  target: number; unit: 'minor' | 'count' | 'percent' | 'rating';
  visibility: 'owners' | 'managers' | 'team'; milestones_notified: number[];
  state: 'active' | 'achieved' | 'missed' | 'archived'; }
interface GoalProgressSnapshot { goal_id: string; period_start: string; as_of: string; value: number;
  expected_value: number; projected_value: number; }
interface ExportJob { id: string; business_id: string; requested_by: string; kind: 'csv' | 'xlsx' | 'png' | 'svg';
  query_ref: string; state: 'queued' | 'running' | 'ready' | 'failed' | 'expired' | 'canceled';
  file_ref?: string; row_count?: number; expires_at?: string; }
interface ScheduledReport { id: string; business_id: string; view_id: string; cron_local: string;
  recipients_user_ids: string[]; format: 'email_summary' | 'csv'; last_sent_at?: string; }
interface PresenceHeartbeat { business_id: string; actor_type: 'customer' | 'visitor' | 'worker' | 'user';
  actor_id: string; surface: 'portal' | 'customer_app' | 'worker_app' | 'crm'; last_seen_at: string; } // TTL 10 min
interface InAppNotification { id: string; business_id: string; user_id: string; definition_id: string;
  title: string; body: string; deep_link: string; read_at?: string; created_at: string; } // TTL 90 d
```

### 7.9 Key indexes & constraints (logical)
- `OutboundMessage(business_id, idempotency_key)` UNIQUE. `(business_id, recipient_contact_id,
  created_at)` for the timeline. `(provider_message_id)` UNIQUE for DLR lookup.
- `DurableTimer(state, due_at)` partial index on pending rows.
- `Enrollment(drip_id, contact_id, enrollment_key)` UNIQUE. Partial UNIQUE on
  `(drip_id, contact_id) WHERE state IN (active, executing, waiting, held)`.
- `ConversationMessage(provider_message_id)` UNIQUE for inbound dedupe.
- `ShortLink(host, token)` UNIQUE.
- `FactContribution(business_id, source_event_id, line_no)` PK.
- `RollupDaily` PK on `(business_id, basis, local_date, dim_key)`, partitioned by month.
- Data retention [I]:
  - MessageEvent: raw events 25 months, aggregated forever.
  - Rendered bodies: 25 months, then hashed.
  - Consent evidence: forever (or the legally required period after relationship end, at least 5 years).

---

## 8. API surface
These are commands and queries, not wire format. Auth = permission key from Section 2 (Agent 09 RBAC).
All are tenant-scoped. All mutations accept `Idempotency-Key`. Commands marked **MCP** are
candidates for the CRM MCP / Darwin action set [S: LLMS "CRM MCP: 150+ actions … analyzing business
data"]. Commands marked **BMCP** are candidates for the customer-level Bookings MCP. `[I]` throughout.

### 8.1 Internal (module-to-module) — the only way other modules send messages
| Name | Input | Output | Auth |
|---|---|---|---|
| `comms.notify` | `{definition_id, subject{type,id,version}, recipients?: override[], context, not_before?, suppress_customer?: boolean}` | `{intent_ids[]}` | service token |
| `comms.schedule` | `{definition_id, subject, slot, due_at, subject_version}` | `{scheduled_id}` (upsert) | service |
| `comms.cancelScheduled` | `{subject{type,id}, definition_ids?: string[]}` | `{canceled: n}` | service |
| `comms.sendDirect` | `{conversation_id?, endpoint, channel, body, purpose:'conversational'|'security', attachments?}` | `{message_id}` | service (auth codes from 01; worker relay) |
| `comms.eligibility` | `{business_id, endpoint, channel, purpose}` | `{allowed, reason?, next_allowed_at?}` | service (01 shows "SMS opted out" states; 07 badges) |
| `links.create` | `{kind, target, contact_id?, message_id?, expires_at?}` | `{url, token}` | service (01 owns resolution route) |
| `reviews.recordRating` | `{token, rating, comment?}` | `{review_id, public_url?}` | public (token) |
| `referrals.validateCode` | `{code, friend: {email?, phone?, address?}}` | `{valid, friend_offer, reason?}` | public (rate-limited) / service |
| `reporting.ingest` | (event bus subscription — not callable) | — | — |

### 8.2 Notifications settings
| Name | Input | Output | Auth |
|---|---|---|---|
| `notifications.catalog.list` | `{audience?, area?}` | definitions + business settings merged | `comms.notifications.configure` (read: manager) |
| `notifications.setting.update` | `{definition_id, enabled?, channels?, timing?}` | setting | `comms.notifications.configure` |
| `notifications.template.fork` / `.update` / `.reset` | `{definition_id, channel, version payload}` | TemplateVersion + lint | `comms.notifications.configure` |
| `notifications.template.preview` | `{template_version_id, sample_subject_id?}` | rendered output (desktop/mobile/dark, SMS segments) | same |
| `notifications.test.send` | `{definition_id, channel, to_self: true}` | message id | same |
| `notifications.userPrefs.get/update` | per-user | — | self |
| `notifications.inApp.list/markRead` | `{cursor}` | items | self |

### 8.3 Marketing
| Name | Input | Output | Auth | MCP |
|---|---|---|---|---|
| `audiences.count` | `{rule}` | `{eligible_by_channel, excluded_by_reason}` | `marketing.campaign.create` | MCP |
| `audiences.preview` | `{rule, limit:20}` | contacts | same | — |
| `audiences.save` / `.list` / `.delete` | — | — | same | MCP |
| `audiences.fields` | — | semantic field registry (fields from 07/02/03/05/06) | same | MCP |
| `campaigns.create/update` | Campaign draft | Campaign | `marketing.campaign.create` | MCP (draft only) |
| `campaigns.lint` | `{campaign_id}` | checklist results + cost estimate | same | MCP |
| `campaigns.testSend` | `{campaign_id, to_user_id, sample_contact_id}` | — | same | — |
| `campaigns.launch` / `.schedule` | `{campaign_id, at?}` | state | `marketing.campaign.send` (**MCP launches require human confirmation in UI**) | MCP (confirm) |
| `campaigns.pause/resume/cancel/duplicate/archive` | `{campaign_id}` | state | `marketing.campaign.send` | MCP |
| `campaigns.stats` | `{campaign_id}` | 5.7 metrics | `marketing.analytics.view` | MCP |
| `postcards.proof` / `postcards.order` | `{campaign_id}` | proof URL / order ids, total cost | `marketing.campaign.send` + billing | — |
| `drips.templates.list` | `{category?}` | gallery | `marketing.drip.manage` | MCP |
| `drips.create/update` (draft) | DripVersion draft | Drip | same | MCP |
| `drips.launch` | `{drip_id, enroll_existing?}` | state, version | same (confirm for MCP) | MCP (confirm) |
| `drips.publishEdit` | `{drip_id, version draft, migrate_in_flight: boolean}` | new version | same | — |
| `drips.pause/resume/archive` | — | state | same | MCP |
| `drips.metrics` | `{drip_id, range?}` | in drip, completed, exited by reason, revenue generated, per-step funnel | `marketing.analytics.view` | MCP |
| `drips.enrollments.list` | `{drip_id, state?, q?}` | enrollments + timeline | same | — |
| `drips.enrollments.add` (manual) / `.remove` | `{drip_id, contact_ids}` | results | `marketing.drip.manage` | MCP |
| `drips.testRun` | `{drip_id, compress: true}` | test enrollment for self | same | — |
| `ai.marketing.draft` | `{goal prompt, channel(s), audience hint}` | Draft campaign/drip (never launched automatically) [S: LLMS AI marketing builder] | `marketing.campaign.create` | MCP |
| `templates.*` | CRUD + gallery | — | `comms.templates.edit` | — |
| `referrals.program.get/update` | ReferralProgram | — | `referrals.manage` | — |
| `referrals.list` | filters | referrals | `referrals.manage` | MCP |
| `referrals.myCode` | — | code + link + stats | customer (portal/app) | BMCP |
| `reviews.list` / `.respond` / `.hide` | filters / text | — | `reviews.view` / `reviews.respond` | MCP |
| `reviews.settings.update` | booster settings, google URL / GBP connect | — | `comms.notifications.configure` | — |

### 8.4 Compliance & senders
| Name | Input | Output | Auth |
|---|---|---|---|
| `senders.status` | — | numbers, registrations, email domains, gates | `compliance.sender.manage` (read: all staff badge) |
| `senders.sms.start` | `{route:'toll_free'|'10dlc', identity}` | registration records | same |
| `senders.sms.updateSubmission` / `.resubmit` | — | — | same |
| `senders.email.addDomain` / `.verify` / `.remove` | `{domain}` | DNS records / state | same |
| `consent.record` (staff attestation) | `{contact_id, endpoint, purpose, method, evidence_ref}` | ConsentEvent (via 07) | `compliance.consent.override` |
| `consent.history` | `{contact_id}` | events | `inbox.view_all` or 07 profile perm |
| `preferences.get/update` | signed token | categories | public (token) / customer session |
| `unsubscribe.oneClick` | POST signed token | 200 | public |
| `quietHours.get/update` | QuietHoursPolicy (narrowing only) | — | `comms.notifications.configure` |
| `budget.get/update` | cap, alerts | — | owner/admin |
| `usage.get` | `{month}` | segments, emails, postcards, cost | owner/admin |

### 8.5 Inbox & chat
| Name | Input | Output | Auth |
|---|---|---|---|
| `inbox.conversations.list` | `{folder, channel?, assignee?, q?, cursor}` | conversations | `inbox.view_all` / `.view_assigned` (server filters) |
| `inbox.conversation.get` | `{id, cursor}` | messages incl. system notification bubbles | same |
| `inbox.message.send` | `{conversation_id, channel, body, attachments?, template_id?}` | message (pipeline: conversational) | `inbox.reply` |
| `inbox.note.add` | `{conversation_id, body, mentions}` | note | `inbox.reply` |
| `inbox.conversation.assign/snooze/close/reopen` | — | state | `inbox.reply` |
| `inbox.newConversation` | `{contact_id or phone/email, channel, body}` | conversation | `inbox.reply` (eligibility check; marketing intent blocked without consent) |
| `chat.team.createGroup/addMembers/removeMember/leave` | — | conversation | `chat.team.create_group` / participate |
| `chat.team.send` | `{conversation_id, body, mentions, urgent?}` | message | `chat.team.participate` |
| `webchat.start/send/poll` (public widget; WebSocket/SSE) | visitor token | messages | public (rate-limited, captcha on abuse) |
| `webhooks.sms.inbound` / `.status`, `webhooks.email.events` / `.inbound`, `webhooks.postcard.events` | provider payloads | 200 | provider signature verification |

### 8.6 Reporting
| Name | Input | Output | Auth | MCP |
|---|---|---|---|---|
| `reports.metric.query` | `{metric_ids[], range, basis?, group_by?, filters?, compare?}` | series + totals + `as_of` + definition versions | `reports.view_*` per metric | MCP (Darwin "analyzing business data") |
| `reports.metric.define` | `{metric_id}` | human definition (5.10) | any staff | MCP |
| `dashboard.layout.get/save/resetDefault/saveAsDefault` | layout | — | `dashboard.customize(_default)` | — |
| `dashboard.tile.data` | `{instance_id}` | tile payload | per metric | — |
| `reports.views.save/list/delete` | SavedReportView | — | `reports.view_*` | MCP |
| `reports.export` | `{query or view_id, format}` | `{export_id}` or stream | `reports.export` | MCP |
| `reports.export.get` | `{export_id}` | status, signed URL | requester | — |
| `reports.schedule.create/update/delete` | ScheduledReport | — | `reports.export` | — |
| `leaderboard.get` | `{metric, range, scope?}` | ranked list, MVP | `leaderboard.view` (revenue column requires financial perm) | MCP |
| `goals.create/update/archive/list` | Goal | progress | `goals.manage` / view by visibility | MCP |
| `forecast.get` | `{metric:'M-NET'|'M-JOBS-SCHED', horizon_days: 30|60|90}` | expected + band + components | `reports.view_financial` | MCP |
| `presence.heartbeat` | `{surface}` | — | any session (01 portal, 09 apps) | — |
| `presence.activeNow` | — | counts + lists | dashboard viewers | — |

Rate limits [I]: metric queries 10/s per business. Exports: 5 concurrent per business. Public
endpoints (webchat, rating, unsubscribe, code validation): IP + token limits.

---

## 9. Interfaces with other modules

### 9.1 Canonical event names and aliases (for Agent 10 reconciliation)
The catalog (5.1) uses **canonical names**. Specs 01–07 and 09 were written in parallel and
emit slightly different names. The table maps canonical names to the names found in each spec.
Agent 10 should choose one, and 08 subscribes to whatever Agent 10 ratifies. Until then, the
Notification Router subscribes to **all aliases** and de-duplicates by `event_id`.

| Canonical (08) | Emitted as (found in specs) | Producer |
|---|---|---|
| `booking.appointment.created` | `scheduling.job.scheduled`, `jobs.job.created`, `booking.job.created`, `portal.booking.confirmed`, `booking.appointment.created` (07) | 03 (system of record) / 04 / 01 |
| `booking.appointment.rescheduled` | `scheduling.job.rescheduled`, `schedule.job.rescheduled`, `portal.reschedule.completed` | 03 / 01 |
| `booking.appointment.reassigned` | `scheduling.assignment.changed`, `.created`, `.removed`, `dispatch.job.reassigned` | 03 / 09 |
| `booking.appointment.canceled` | `jobs.job.cancelled`, `job.job.cancelled`, `booking.job.cancelled`, `portal.cancellation.completed` | 04 / 01 |
| `dispatch.job.unassignable` | `scheduling.dispatch.failed`, `scheduling.job.at_risk` | 03 |
| `recurring.series.created/updated/ended` | `scheduling.series.created/updated/ended/paused/resumed`, `scheduling.occurrence.skipped` | 03 |
| `route.route.started` | `routing.route.started`, `schedule.route.started` | 03 / 04 |
| `job.job.en_route` | `job.job.on_my_way`, `job.worker.on_my_way` | 04 (03, 01 consume) |
| `schedule.eta.updated` | `routing.route.reoptimized`, `routing.route.progressed`, `team.location.pinged` (derived) | 03 |
| `job.job.started` | `jobs.visit.started`, `job.job.started`, `job.work.started` | 04 / 09 |
| `job.job.completed` | `jobs.job.completed`, `job.job.completed`, `job.work.completed` | 04 |
| `job.job.reopened` | `jobs.job.reopened`, `job.job.reopened` | 04 |
| `job.job.no_show` | `job.job.no_show` | 03/04 |
| `job.job.updated` (material) | `jobs.lineitem.added`, `job.line.added/removed/qty_changed`, `job.lines.changed`, `jobs.job.totals_changed` | 04 |
| `job.photo.added` | `jobs.photo.added`, `jobs.photo.uploaded` | 04 |
| `job.intake.incomplete` | `customer.intake.updated` (absence-derived) | 07 / 01 |
| `quote.quote.sent/viewed/accepted/declined/expired/withdrawn` | `jobs.quote.sent/viewed/accepted/declined/expired/withdrawn`, `jobs.quote.revised` | 04 |
| `invoice.invoice.sent/overdue/paid` | `jobs.invoice.sent/issued/overdue/paid/payment_applied` | 04 |
| `payment.payment.captured` | `payments.payment.succeeded`, `payments.charge.succeeded`, `payments.cash.collected` | 05 |
| `payment.payment.failed` | `payments.charge.failed`, `payments.authorization.failed`, `payment.deposit.failed` | 05 |
| `payment.preauth.created` | `payments.authorization.placed` | 05 |
| `payment.refund.created` | `payments.refund.succeeded` (send on success, not on creation) | 05 |
| `payment.dispute.opened` | `payments.dispute.opened`, `charge.dispute.created` | 05 |
| `payment.payout.paid/failed` | `payments.payout.paid/failed` | 05 |
| `payment.card.expiring` | (not yet in 05) — **request** | 05 |
| `payment.tip.added` | `payments.tip.added` | 05 |
| `subscription.subscription.activated` | `subscriptions.subscription.activated`, `portal.subscription.purchased` | 06 |
| `subscription.renewal.upcoming` | (not yet in 06) — **request**; related `subscription.price_change.notice` | 06 |
| `subscription.renewal.charged` | `subscriptions.subscription.renewed` | 06 |
| `subscription.payment.failed` | `subscriptions.subscription.payment_failed`, `subscription.subscription.past_due` | 06 |
| `subscription.dunning.exhausted` | (derive from 06 dunning terminal state) — **request** | 06 |
| `subscription.subscription.canceled/paused/resumed` | `subscriptions.subscription.cancel_scheduled`, `subscription.subscription.cancelled/paused`, `subscriptions.subscription.resumed` | 06 |
| `subscription.commitment.ending` | `subscriptions.commitment.ended/renewed` (need a *pre*-end event) — **request** | 06 |
| `giftcard.giftcard.purchased` | `portal.giftcard.purchased`, `giftcards.card.issued` | 01 / 06 |
| `giftcard.giftcard.delivery_due` | (06 issues with `deliver_at`; 08 schedules its own timer) | 06 |
| `giftcard.giftcard.redeemed` | `giftcards.card.redeemed` | 06 |
| `customer.customer.created` | `customer.customer.created`, `customers.customer.created` | 07 |
| `customer.customer.merged/erased` | `customer.customer.merged/unmerged/erased` | 07 |
| `customer.account.created` | `portal.account.created`, `portal.account.registered` | 01 |
| `customer.consent.changed` | `customer.consent.changed` | 07 (ledger), triggered by 08 commands |
| `customer.tag.added/removed` | `customer.tags.bulk_changed` (+ per-customer update) | 07 |
| `lead.lead.created/stage_changed/converted` | `lead.lead.*` | 07 |
| `booking.session.contact_captured` | `portal.session.contact_captured`, `booking.attempt.contact_captured` | 01 / 07 |
| `booking.session.abandoned` | `portal.session.abandoned`, `lead.attempt.abandoned`, `booking.attempt.expired`, `booking.session.abandoned` (03) | 01 / 07 |
| `portal.visit.recorded` | `portal.visit.recorded`, `portal.session.started/step_reached` | 01 |
| `marketing.postcard.scanned` (08 produces) | consumed by 07 as `portal.qr.scanned` | 08 → 07 |
| `catalog.addon.stock_low` | `inventory.stock.low`, `inventory.stock.depleted` | 02 |
| `catalog.service.published` | `catalog.service.published`, `catalog.revision.published` | 02 |
| `team.worker.invited` | `team.member.invited`, `team.invite.sent` | 09 |
| `team.worker.activated` | `team.member.activated`, `team.invite.accepted` | 09 |
| `team.worker.deactivated` | `team.member.deactivated`, `team.worker.deactivated` | 09 |
| `timesheet.timesheet.submitted/approved/rejected/paid` | `timesheet.entry.submitted/approved/rejected/paid` | 09 |
| `timesheet.punch.open_too_long` | `timesheet.entry.auto_closed` (post-hoc); need a pre-close reminder event — **request** | 09 |
| `team.pay_rate.effective` | `timesheet.compensation.activated` | 09 |
| `team.availability.changed` | `team.availability.changed/updated`, `team.timeoff.*` | 09 |

### 9.2 CONSUMES (by module)
| From | What | Why (08 feature) |
|---|---|---|
| **01 Booking portal** | Events: visits, sessions (started/step/contact_captured/abandoned), account created, booking confirmed, cancellation feedback [S: T05], referral used, gift-card purchase, domain activated/detached. Data: branded host per tenant, consent-checkbox disclosure versions, booking-session cart (for resume links), auth challenges | Funnel metrics, abandoned drips, link host, consent evidence, N-A02 codes |
| **02 Catalog & coverage** | Services/add-ons (name, image, price, cost [S: T06, T07]), categories, zones/shops with tz and geometry versions, travel fees, stock-low events | Email service cards, reporting dims (service, zone), M-COST, A-16 |
| **03 Scheduling & dispatch** | Job scheduled/rescheduled/assignment/canceled, series lifecycle, route started/progress/ETA, dispatch failed, availability (with 09) | Catalog N-B*, N-D*, W-02..06, A-18, utilization, forecast inputs (future schedule, series) |
| **04 Job lifecycle** | Job status changes, on-my-way, started, completed, reopened, no-show, line changes (`addedByWorkerId`), photos, quotes & invoices lifecycle, document URLs, job-scoped conversation link | Day-of messages, quotes/invoices messaging, review requests, upsell metrics, photos tile |
| **05 Payments** | Ledger postings (revenue/contra/tax/tips by effective_date), payments, refunds, disputes, payouts, RevenueAttribution, processing fees, pay links, card-expiry | All money metrics (5.10.2), receipts, A-05/06/07, cash tiles |
| **06 Subscriptions, discounts, gift cards** | Subscription lifecycle and dunning schedule, MRR inputs, discount redemptions with `source_campaign_id`, gift card issue/delivery/redeem, reward issuance results | N-S*, N-G*, code-touch attribution, M-MRR, referral rewards |
| **07 Customers & leads** | Customer/people/contact points (multiple phones/emails [S: LLMS]), addresses with tz, tags, types, consent ledger state, merges/erasures, leads and stages, assets/vehicles (last service date) | Recipient resolution, audiences, timelines, consent enforcement, drip triggers, privacy erasure |
| **09 Team** | Members, roles/permissions (RBAC keys from §2), invite tokens, worker tz, shift/availability/DND, timesheet events, pay rates, presence, split shares (`share_bp`) | W-series, A-09/11/19, leaderboard eligibility, M-W-*, team chat membership |
| **11 Branding** | `BrandTheme` (palettes light/dark, logos per placement incl. email + dark variant, fonts + fallbacks, radius/shadow, icons, copy overrides), theme version events `branding.theme.published` | 5.13 rendering of every email, landing page, SMS name prefix |
| **10 Integrator** | Ratified event catalog & envelope (event_id, occurred_at, business_id, causation_id), outbox guarantees, tenant registry | Router subscriptions & dedupe |

### 9.3 PRODUCES
| Event | Payload (key fields) | Consumers |
|---|---|---|
| `comms.message.sent` / `.delivered` / `.failed` / `.suppressed` | message_id, definition_id/campaign/drip, subject, recipient, channel, reason | 07 (timeline), 04 (job activity "Reminder sent"), 09 (`notification.delivery.failed` alias for invite bounce → Invite.deliveryStatus) |
| `comms.consent.changed` → via 07 `customer.consent.record` | ConsentEvent | 07 (ledger), 01 (preference UI) |
| `comms.suppression.added` | endpoint, reason | 07 (badge "email bouncing"), 01 (ask for new email at login) |
| `comms.conversation.message_received` / `.message_sent` | conversation_id, contact_ids, job_id?, channel | 07 (last-contacted, lead activity auto-advance), 04 (job thread), 09 (worker notifications) |
| `comms.reply.confirmation_received` / `.reschedule_requested` | job_id, contact_id | 03/04 (mark confirmed; reschedule flow) |
| `comms.link.clicked` | link kind, contact, message | 07 (lead engagement), 01 (attribution handoff via token) |
| `marketing.campaign.scheduled/launched/completed/failed` | campaign_id, counts | 10 audit; owner notifications |
| `marketing.drip.launched/paused/archived` | drip_id, version | 10 audit |
| `marketing.enrollment.started/completed/exited` | enrollment_id, drip_id, contact_id, reason | 07 (customer timeline "In drip: Welcome") |
| `marketing.touch.recorded` | Touch | internal attribution; 07 timeline |
| `marketing.attribution.assigned` | job_id, source, rule | 05/06 (optional campaign ROI), reporting |
| `marketing.postcard.scanned` | campaign_id, link token, address_id?, contact_id? | 07 (create/update lead, source=postcard [S: T20]) |
| `review.request.sent` / `review.review.received` / `review.public_link.clicked` | job_id, worker_ids, rating | 04 (job card), 07 (profile ratings [S: T09]), 09 (worker profile ratings) |
| `referral.code.issued` / `referral.referral.qualified` / `.reversed` | referrer, friend, job | 06 (issue/claw back reward via `rewards.issue` command), 07 |
| `reporting.goal.milestone_reached` / `.period_closed` | goal, value | notifications (A-13/W-16) |
| `reporting.export.ready` | export_id | notifications (A-20) |
| `compliance.sender.status_changed` | business, entity, state | 01 (hide "text me" options until enabled), owner alerts |

### 9.4 Contracts we REQUIRE from each agent (acceptance criteria)
1. **All producers:** emit through a transactional outbox with a stable `event_id` and `occurred_at`.
   Payloads carry `business_id`, ids (not only names) and **versions**: `job.version`,
   `schedule_version`. 08 never parses UI text.
2. **01:**
   - Consent checkbox with versioned disclosure text. The `contact_captured` event includes the
     consent state and disclosure version.
   - Hosts the preference, unsubscribe, rating, tracking and webchat routes on the tenant host.
   - Resolves `/s/{token}` short links via 08 `links.resolve`.
   - Booking sessions persist 7 days for resume links.
   - Lazy-loads the webchat widget so it stays within the widget performance budget.
3. **03:** `notify_customer` flag on create/reschedule (CRM toggle). Debounce-friendly versioning.
   ETA source.
4. **04:** `suppressCustomerNotifications` on past-dated jobs (already in 04, aligned). Job state
   machine names (`completed`, `reopened`, `no_show`) are stable. `addedByWorkerId` on lines.
5. **05:**
   - Ledger `effective_date` in business-local date.
   - Revenue split by account classes 4000–4030, 4900, 4950 and tax/tip accounts.
   - `payment.card.expiring` event.
   - `source_campaign_id` passthrough on payments that used a tracked offer.
6. **06:**
   - Renewal-upcoming, commitment-ending and dunning-exhausted events.
   - `discounts.redemption.committed` carries `issued_by_source {campaign|drip|referral, id}`.
   - Command `rewards.issue(referral_id, spec)`, idempotent on referral_id.
7. **07:**
   - `customer.consent.record` command that accepts 08's ConsentEvent.
   - Contact-point lookup by E.164 across people.
   - Merge events with survivor id.
   - Address tz.
   - Field registry for audiences.
   - Erasure event: 08 deletes rendered message bodies and conversation content for that
     contact and keeps hashed audit rows.
8. **09:**
   - RBAC keys listed in §2.
   - Worker tz and DND/shift windows.
   - `share_bp` for split jobs.
   - Presence heartbeat source.
   - Invite token lifecycle (`team.invite.*`).
   - Deactivation event (remove from chats and from the current-period leaderboard).
9. **11:** `BrandTheme` API with email-specific logo/placement sizing, dark logo, font fallback
   mapping, copy-override keys, and theme versioning.

### 9.5 What other agents should NOT do
- Do not call SMS/email/push providers directly or store templates. Always call `comms.notify` with a
  catalog ID (new IDs are requested from 08).
- Do not compute dashboard metrics in their own modules. Use `reports.metric.query`. Exception:
  Agent 05's Transactions views read the ledger directly, and their totals must equal
  M-COLLECTED (reconciliation test).

### 9.6 API keys & integrations register (owner requirement 2026-11-13)
Storage principle [I]:
- All secrets live in a **secrets manager / KMS-encrypted vault** (infra TBD, stack-agnostic).
- The database stores only `secret_ref` pointers.
- Secrets are never sent to browsers or mobile apps. Mobile apps receive only public config such as
  FCM sender IDs.
- Access is least-privilege per service, with an audit log on every read.

| # | Service | Purpose (08) | Keys / secrets | Owner | Stored where | Rotation |
|---|---|---|---|---|---|---|
| K1 | Telnyx (primary SMS) | SMS/MMS send and receive, number ordering, 10DLC/TF registration (ISV) | API key v2 (bearer); webhook **public key** for Ed25519 signature verification; messaging profile IDs (non-secret); per-business **managed sub-account** IDs | Platform | Vault (`sms/telnyx/api_key`); public key in config | API key every 90 days, dual-key overlap; webhook key on provider rotation |
| K2 | Twilio (alternate SMS / optional BYO top tier) | Same | Account SID (non-secret), **Auth Token** or **API Key SID + Secret** (preferred); webhook signature uses the Auth Token; sub-account SIDs per business | Platform (BYO: business) | Vault; BYO under `business/{id}/sms/twilio` | API keys every 90 days; Auth Token rotation via secondary token |
| K3 | Bandwidth / Plivo (optional alternates) | SMS | Bandwidth: API client ID/secret (OAuth), account ID, application ID, webhook basic-auth credentials. Plivo: Auth ID + Auth Token | Platform | Vault | 90 days |
| K4 | The Campaign Registry (via provider) | 10DLC brand/campaign | Normally none of ours (provider proxies). If going direct: TCR API key + secret | Platform | Vault | Yearly / per TCR policy |
| K5 | Amazon SES (primary email) | Transactional + marketing email, inbound reply parsing (SES receiving → storage → webhook), event notifications (SNS/EventBridge) | IAM role (preferred, no static keys) or access key ID + secret; SNS topic subscription confirmation; **DKIM private keys** if using BYODKIM (else SES-managed Easy DKIM) | Platform | IAM role on workload identity; BYODKIM keys in KMS | Static keys 90 days (avoid with roles); DKIM key rotation yearly with dual selectors |
| K6 | Postmark (alternate transactional email) | Higher-deliverability transactional stream | Server API token (per stream/server), Account API token (domain management) — separate; webhook basic-auth credentials | Platform | Vault | 180 days; account token restricted to ops |
| K7 | Mailgun / SendGrid / Resend (alternates) | Email | API key (scoped: send-only), webhook signing key | Platform | Vault | 90 days |
| K8 | Firebase Cloud Messaging | Push to Android/web (worker app + customer app) | Service account JSON (private key) per Firebase project; for **tenant-branded apps** a project per tenant app or one project with multiple apps | Platform (branded app: platform-managed per tenant) | Vault (`push/fcm/{project}`) | Service account keys 90 days or Workload Identity Federation (no key) |
| K9 | Apple Push Notification service | Push to iOS | **APNs auth key (.p8)** + Key ID + Team ID (token-based; one key works across apps of the same team). Tenant-branded apps published under a **tenant's own Apple developer account** need that tenant's .p8 | Platform; Business for own-account branded apps | Vault (`push/apns/{team}`) | No expiry; rotate yearly or on staff change (Apple allows 2 active keys) |
| K10 | Lob (primary postcards) | Print & mail, address verification | Live + test secret API keys; webhook signing secret | Platform | Vault | 180 days |
| K11 | PostGrid / Click2Mail (alternates) | Same | API key; webhook secret | Platform | Vault | 180 days |
| K12 | Google Business Profile API | Read reviews, reply, deep link to review form | Platform OAuth **client ID + client secret**; per-business **OAuth refresh token** (scope business.manage) | Platform app; Business grants token | Vault (`business/{id}/gbp/refresh_token`, envelope-encrypted) | Client secret yearly; refresh tokens revocable by business; re-consent on error |
| K13 | Phone lookup (line type/carrier) | Avoid texting landlines; tz hint | Provider API key (Telnyx/Twilio Lookup reuse K1/K2) | Platform | Vault | with K1/K2 |
| K14 | FCC Reassigned Numbers Database | Consent hygiene (top tier) | RND account credentials / API token | Platform | Vault | Per RND policy (annual) |
| K15 | LLM provider (AI marketing builder, opt-out intent, Darwin) | Drafting copy, classification | API key (server-side only), org/project id | Platform | Vault | 90 days; spend limits |
| K16 | Platform internal signing keys | Unsubscribe/preference/rating/tracking tokens, short-link tokens, webchat visitor tokens, ICS links | HMAC keys (≥ 256-bit) with `kid`; Ed25519 for signed URLs to exports | Platform | KMS | 90 days with `kid` overlap (old keys verify-only for token lifetime, e.g. unsubscribe tokens: never-expiring links → keep verify keys for 10 years or use DB-backed tokens) |
| K17 | Webhook ingress secrets (all providers above) | Verify inbound webhooks | Per-provider signature secret / public key | Platform | Vault | With each provider |
| K18 | Export storage | Signed URL generation for CSV/PNG exports | Object-store credentials (role-based) | Platform | Workload identity | Roles, no static keys |
| K19 | Analytics pixels (business-provided, via 01) | Not 08's — listed for completeness | Business's GA4/Meta IDs (non-secret) | Business | 01 config | n/a |
| K20 | BYO sender credentials (top-tier option) | Business uses own Twilio/SendGrid | Business-provided API keys | Business | Vault namespace `business/{id}/byo/*`, envelope-encrypted with per-tenant DEK | Business-managed; we alert on auth failures and expiry; never displayed after save (last 4 only) |

Non-secret identifiers that still need management: phone numbers, 10DLC brand/campaign IDs, SES
configuration-set names, Lob template IDs, FCM sender IDs, APNs bundle IDs. These are stored in
DB config, not the vault.
Operational rules:
- On a secret-read failure, the pipeline fails closed: the message stays `rendered`, then retries,
  then alerts ops. It never falls back to an unsigned or unauthenticated send.
- Webhooks without a valid signature are rejected with 401 and counted.

---

## 10. Algorithms / pseudo-code
All `[I]`. Pseudo-code is language-neutral.

### 10.1 Send pipeline
```
function process(intent):
  # 1. idempotent insert
  msg = insert OutboundMessage(state='pending', key=intent.idempotency_key) ON CONFLICT DO NOTHING RETURNING *
  if msg is null: return existing(intent.idempotency_key)          # duplicate event / retry

  def = catalog(intent.definition_id)        # may be null for campaign/drip/conversational
  # 2. recipient endpoint
  ep = resolveEndpoint(intent.recipient, intent.channel)  # 10.16
  if ep is null: return fallbackOrSuppress(msg, 'no_endpoint')
  # 3. eligibility (fast projection + synchronous opt-out table)
  elig = SendEligibility[business, ep]
  if suppressed(ep) or !allowed(elig, intent.purpose, intent.channel):
      return fallbackOrSuppress(msg, reason)
  if intent.channel=='sms' and elig.line_type=='landline': return fallbackOrSuppress(msg,'landline')
  if intent.channel=='sms' and !sender(business).sms_enabled: return fallbackOrSuppress(msg,'sender_not_registered')
  # 4. quiet hours
  tz = recipientTz(intent, ep)                                 # 5.5.1
  cls = quietClass(def, intent)                                # incl. TXN* promotion to CRIT
  t = max(now, intent.not_before)
  allowed_at = nextAllowed(t, tz, policy(business, cls))       # 10.2
  if allowed_at > now: msg.state='deferred'; timer(msg, allowed_at); return
  # 5. frequency cap (marketing)
  if intent.purpose=='marketing' and capExceeded(ep):
      if intent.drip_id: defer to nextCapSlot(ep) else suppress('frequency_cap'); return
  # 6. budget
  if overBudget(business, intent.purpose, channel): suppress or fallback per policy; return
  # 7. render (resolve variables NOW)
  ctx = loadVariables(def.variable_set, intent.subject)        # current state of job/invoice
  if subjectInvalid(def, ctx, intent.subject.version): cancel(msg,'subject_changed'); return
  r = render(templateFor(def|campaign|step, business, channel), ctx, brandTheme(business))
  if r.error: fail(msg,'render'); return
  if intent.purpose!='marketing': r = stripPromoBlocks(r, elig)          # 5.2 mixed-content rule
  links = mintShortLinks(r, msg)                                        # tenant host
  store(msg, r, links, segments(r), costEstimate)
  # 8. provider send with retry
  for attempt in 1..5:
     res = provider(channel).send(r, from=sender, idempotency=msg.id)
     if res.ok: msg.state='sent'; emit comms.message.sent; return
     if res.permanent: fail(msg, res.code); fallbackIfAllowed(msg); return
     sleep(backoff(attempt))   # exponential 2^n s + jitter; via timer, not thread sleep
  msg.state='failed'; alertOpsIfSystemic()

function fallbackOrSuppress(msg, reason):
  msg.state='suppressed'; msg.state_reason=reason; emit comms.message.suppressed
  for ch in def.fallback where ch != msg.channel and not alreadySentVariant(msg.subject, def, ch):
     enqueue intent(channel=ch, key=msg.key+':fb:'+ch); break
```
Concurrency: workers lease messages (`state=ready`, `lease_expires_at`). A provider call is made
only by the leaseholder. If a crash happens after the provider accepted the message but before the
DB update, the message is re-sent under the same provider idempotency key where the provider
supports one (Postmark/SES lack this). Otherwise the per-recipient duplicate guard (5.4) catches
the repeat: body hash plus 10-minute window. That guard is best effort, so accept rare duplicates
over losing messages.

### 10.2 Quiet hours — `nextAllowed(t, tz, policy)` (DST-safe)
```
function nextAllowed(t_utc, tz, windows, blackout_dates):
  if windows == ALWAYS: return t_utc
  local = toZoned(t_utc, tz)                     # wall clock
  for d in 0..14:                                # search horizon
     day = local.date + d
     if day in blackout_dates: continue
     for w in windows where day.weekday in w.days (sorted by start):
        start = zonedDateTime(day, w.start, tz, disambiguation='later')   # nonexistent 02:30 → 03:00+
        end   = zonedDateTime(day, w.end,   tz, disambiguation='earlier')
        candidate = (d==0) ? max(t_utc, start) : start
        if candidate < end: return candidate
  return null   # no window in 14 days → suppress('no_send_window')
```
- Windows are defined in wall-clock time, so a 08:00 start stays 08:00 on DST-change days.
- Ambiguous times (fall back): use the later instant for starts and the earlier instant for ends.
  This shortens the window and never lengthens it.
- Multi-tz area codes use the intersection of windows (5.5.1 step 4).

**Reminder pull-forward** (5.5.2):
```
due = start - offset
if !inWindow(due): prev = previousAllowedEnd(due) - 1min
   due = (prev >= booked_at + 5min) ? prev : SKIP
```

### 10.3 Reminder scheduling with superseding versions
```
on booking.appointment.created/rescheduled/reassigned(job, version):
  for slot in enabledReminderSlots(business):           # reminder_1, reminder_2, pre_visit, review...
     due = computeDue(slot, job)                         # 10.2 pull-forward
     if due == SKIP or due <= now: cancel(slot); continue
     upsert ScheduledNotification(definition, job.id, slot) set due_at=due, subject_version=version
on booking.appointment.canceled(job): cancel all slots for job
on timer due(sn):
  job = load(sn.subject_id)
  if job.version_for_schedule != sn.subject_version or job.status in (canceled, completed): skip
  comms.notify(sn.definition_id, subject={job, version}, key=sn.id+':'+sn.subject_version)
```
Debounce for change notices (N-B06, W-03/W-04):
- Upsert `pending_change(job)` with `not_before = now + 120s` and `latest_version`.
- When the timer fires, send if `latest_version` equals the current job version. Otherwise the newer
  change has re-armed the timer.
- If the net effect returned to the original time and workers, send nothing. Compare
  `first_seen_state` with `final_state`.

### 10.4 Drip engine (durable timers, enrollment, exits, idempotency)
**Components:**
- **Trigger Matcher** (event consumer).
- **Scheduler** (date and segment triggers).
- **Step Executor** (worker pool).
- **Timer Service** (shared `DurableTimer` table, polled every 5s with leased batches; at higher
  scale, a time-bucketed queue).
These map onto any workflow runtime (a Temporal-style engine, or a DB-backed queue). Choosing one is
deferred to the stack decision. The guarantees below are required regardless.

Guarantees:
- **At-least-once execution**, with **effectively-once sends** via idempotency keys
  `drip:{enrollment_id}:{step_id}:{run_seq}`. `run_seq` increments only for loops (none in v1),
  so retries reuse the same key.
- **Durable timers** survive deploys and crashes. A timer is `pending → leased(60s) → done`. An
  expired lease returns the timer to pending.
- **Ordering per enrollment.** Only one executor may hold an enrollment lease, so steps never run in
  parallel for the same contact.

```
on event E:                                  # Trigger Matcher
  for drip in liveDripsWithTrigger(E.type, E.business_id):   # indexed by (business, trigger event)
     v = drip.current_version
     if !matches(v.trigger.where, E) or !matchesRule(v.filters, contactOf(E)): continue
     key = enrollmentKey(v, E)               # 'once' | E.occurrence_id | floor(now/cooldown)
     if !reentryAllows(drip, contact, v.reentry): continue
     if !anyStepSendable(v, contact): count ineligible; continue
     insert Enrollment(... state='active', step_id=v.steps[0].id) ON CONFLICT DO NOTHING
     if inserted: enqueue execute(enrollment)
  for enr in activeEnrollmentsAffectedBy(E):  # exit conditions & wait_until events (indexed by contact)
     if exitConditionMet(enr, E): exit(enr, reason) ; continue
     if enr.state=='waiting' and enr.wait_event==E.type: resume(enr)

execute(enr):                                # Step Executor (leased)
  lock enr (lease); if enr.state in terminal: return
  drip = load(enr.drip_id); if drip.state in (paused, paused_by_system): enr.state='held'; return
  step = version(enr).step(enr.step_id)
  if now - enr.step_due_at > 72h and !step.always_send and isMessage(step): record skipped_late; advance(enr); return
  switch step.type:
   case email|sms|push:
      if !eligible(contact, step.channel, 'marketing'): record skipped; advance(enr); return
      comms.process(intent(channel, purpose='marketing' unless drip.transactional,
              key='drip:'+enr.id+':'+step.id+':0', not_before=sendTiming(drip, contact)))
      record EnrollmentStepRun(sent|suppressed); advance(enr)
   case delay:
      due = now + step.minutes
      if step.align_to_send_window and nextIsMessage: due = nextAllowed(due, tz, sendWindow)
      timer(enr, due); enr.state='waiting'
   case wait_until: timer(enr, computeAt or now+timeout); enr.wait_event = step.event; enr.state='waiting'
   case branch: enr.step_id = eval(step.condition, contact) ? then : else; enqueue execute(enr)
   case action: doAction idempotently (key enr.id+step.id); advance(enr)
   case exit: complete(enr)

advance(enr): next = nextStep(enr); if none: complete(enr) else enr.step_id=next; enqueue execute(enr)
```
Date triggers:
- A nightly scheduler, run per business at 00:10 local, finds contacts whose date field plus
  offset equals today.
- It enqueues enrollments with the `enrollment_key = YYYY` (yearly) or the date.
- Sends are spread with send timing, e.g. 10:00 local.
- Fixed holiday dates use a holiday table per country, including movable holidays (Thanksgiving,
  Easter, Mother's Day), computed yearly.

Segment-entry triggers:
- Incremental: on any contact change event, re-evaluate segment membership for the segments that
  reference the changed fields (field→segment index).
- Time-relative rules ("no booking in 90 days") are evaluated by a nightly sweep.
- Membership transitions emit `segment.entered` and `segment.left` internally.

Version migration (4.12 step 11):
- In-flight enrollments map `step_id` to the new version.
- If the step was deleted, move to the next surviving step in the old order that exists in the new
  version.
- If none remains, the enrollment completes.

### 10.5 Attribution assignment
```
on booking.appointment.created(job):
  c = job.customer_id; t = job.booked_at
  if job.session.link_token → touch = touchOf(token); rule='direct_token'
  elif job.discount_redemption.issued_by_source → touch = codeTouch; rule='code'
  else:
     cands = touches(c) where t - touch.at <= window(touch.kind) and touch.at <= t
             and (touch.source is drip → enrollment.started_at <= t)
     touch = argmax over cands by (precedence(kind), touch.at)
  if job.series_id and not firstOfSeries(job): attribution none (series_parent=…)
  upsert Attribution(job_id, …, model_version=1)          # idempotent on job_id
on job canceled → Attribution.pipeline_value=0 (kept for audit)
recognized value comes from rollups joined on job_id (accrual M-NET per job incl. later refunds)
```
Model changes bump `model_version`. Historical attributions are recomputed in a backfill job, and
the UI labels the model in use.

### 10.6 Inbound keyword handling (STOP/HELP/START)
```
normalize(body): trim, strip punctuation/emoji, uppercase, collapse spaces
STOP_SET  = {STOP, STOPALL, UNSUBSCRIBE, CANCEL, END, QUIT, REVOKE, OPTOUT, OPT OUT, STOP ALL,
             ARRET (fr-CA), ALTO/PARAR (es)}
START_SET = {START, UNSTOP, YES, SUBSCRIBE, RESUME}      # YES only when a double-opt-in is pending or after STOP
HELP_SET  = {HELP, INFO, AIDE, AYUDA}
if norm in STOP_SET (exact match, or first word in STOP_SET and ≤ 3 words):
    write revoke(all_sms) synchronously to SendEligibility + ledger via 07; cancel queued SMS to that endpoint
    send N-M04 STOP confirmation once (no further messages)
elif norm in START_SET and endpoint revoked: grant (transactional restored; marketing only if prior written consent
    existed and the reply is to the business's number → treat START as renewed express consent for marketing? NO:
    restore only the purposes that were granted before STOP; record method=keyword_reply)
    send START confirmation
elif norm in HELP_SET: send HELP response (business name, phone/email, "Reply STOP to opt out")
else: pass to conversation; run free-text opt-out detector:
    regex on phrases ("stop texting", "remove me", "don't text", "unsubscribe", "wrong number")
    → pause marketing consent + inbox task "Possible opt-out"; "wrong number" → mark endpoint invalid for that contact
```
Carrier-level STOP (some providers auto-handle STOP and block further sends) is mirrored. A provider
error "recipient opted out" during a send also triggers a revoke.

### 10.7 Inbound routing & conversation threading
```
on inbound sms(from, to, body, sid):
  dedupe on sid
  biz = businessByNumber(to)
  convo = conversation(biz, endpoint=from) or create
  contacts = contactsByPhone(biz, from)               # may be 0..n
  convo.contact_ids ∪= contacts
  job = activeJobFor(contacts, window ±4h of now) or nextJob(contacts, 48h)
  msg = append(convo, inbound, job_id=job?.id)
  assignee = convo.assignee if active(convo.assignee)
           else (job and job.workers.length==1 and perm(worker,'inbox.view_assigned')) ? worker
           else null (Unassigned)
  notify assignee or owners (A-10, W-11)
```
Masked worker relay:
- A worker reply from the worker app is sent from the business number. The customer never sees the
  worker's number.
- A worker "Call" uses the business number via a voice proxy (future; voice is out of scope for v1).

Email replies:
- Reply-To is `reply+{conversation_token}@inbound.{platform}`. The inbound parser strips quoted
  history (heuristics plus `In-Reply-To` headers) and appends to the conversation.
- Unknown tokens go to the business's unassigned folder.

### 10.8 Throttling (sender capacity)
- A token bucket per sender (number, TF number, email domain/IP pool, postcard vendor).
- Capacity comes from the registration: 10DLC throughput per campaign/brand score; TF verified rate;
  ESP rate.
- Campaign sends draw tokens. Transactional sends have **priority lanes**: CRIT > TXN > CONV > MKT,
  with marketing limited to 70% of capacity so a broadcast never delays on-my-way texts.
- Example: a business with a 4 msg/s cap sending to 2,000 → ~12 minutes. The UI shows the estimated
  finish time.

### 10.9 SMS segment & cost calculation
```
gsm7_basic = GSM 03.38 basic set; gsm7_ext = { ^ { } \ [ ~ ] | € }  (each counts 2 septets)
if all chars in gsm7_basic ∪ gsm7_ext:
   units = Σ (char in ext ? 2 : 1)
   segments = units <= 160 ? 1 : ceil(units / 153)
   encoding = GSM7
else:
   units = UTF-16 code units (emoji = 2)
   segments = units <= 70 ? 1 : ceil(units / 67)
   encoding = UCS2
cost = segments * (rate(provider, country, encoding) + carrierFee(dest carrier, registration))
```
Smart-character normalization (optional, default on):
- Curly quotes → straight, en/em dashes → "-", ellipsis → "...", non-breaking space → space.
- This keeps the message in GSM-7 and roughly halves cost.
- Applied at render, never to user-visible names unless the setting is on.

### 10.10 Reporting rollups (fast dashboards, incremental refresh)
**Goal** [I]:
- p95 tile response < 300 ms.
- Dashboard of 12 tiles < 1.5 s on 4G.
- Freshness: money and jobs within 60 s of the source event; marketing within 5 min.

**10.10.1 Pipeline**
```
source events (outbox) → Fact Mapper (pure function per event type, versioned)
   → FactContribution rows (signed measures, keyed by source_event_id+line_no → idempotent)
   → mark RollupPartitionState(business, month) dirty
Rollup Refresher (every 10–30 s per dirty partition, or on read if older than SLA):
   for each dirty (business, month): recompute affected days only:
     DELETE FROM rollup WHERE business, local_date IN affected_days
     INSERT … SELECT business, local_date, basis, dims…, SUM(measures) FROM FactContribution
            WHERE business AND local_date IN affected_days GROUP BY …
   (or an incremental UPSERT adding deltas: rollup.measure += contribution, exactly-once guaranteed by
    "insert contribution then apply delta" in one transaction with a processed-marker)
```
- **Late-arriving / retroactive events.** Example: a refund posted today against a job completed last
  month. Accrual "posted" view: contributes on the refund date. Restated view: contribution dated
  to the original recognition date, which marks last month's partition dirty.
- **Corrections.** If a source event is voided, a compensating contribution with negated measures is
  inserted. Rows are never updated in place.

**10.10.2 Grains** (separate rollup tables; each tile maps to the smallest table that answers it):
| Rollup | Grain (dims) | Serves |
|---|---|---|
| R1 money_daily | business, date, basis | Revenue trend, KPIs |
| R2 money_by_worker_daily | + worker | Leaderboard revenue, worker summary |
| R3 money_by_service_daily | + service/add-on | Top services, attach rate |
| R4 money_by_zone_daily | + zone/shop | Service areas |
| R5 jobs_daily | business, date, status-transition, source, job_type, zone, worker | Jobs, sources, cancellations |
| R6 customer_daily | business, date (new, returning, active counts) + customer_ltv table (per customer running totals) | Customer KPIs, best customers |
| R7 reviews_daily | business, date, worker, rating bucket | Reviews, leaderboard |
| R8 marketing_daily | business, date, campaign/drip, step, channel (sent, delivered, clicks, unsub, cost, attributed bookings/revenue) | Marketing |
| R9 funnel_daily | business, date, source (visits, started, captured, booked, abandoned, recovered) | Funnel |
| R10 hours_daily | business, date, worker (hours, labor cost) | Utilization, labor |
Month and year totals are computed by summing days (≤ 366 rows per series), which is cheap. For
multi-year ranges a monthly rollup R*_monthly is maintained by the same refresher.

**10.10.3 Top-N and ranking queries** (best customers, top services): served from R3/R6 with
`ORDER BY sum DESC LIMIT N`, using the index on `(business, local_date)`. For "all time" queries,
use the customer_ltv running-total table.

**10.10.4 Time-zone change.** If a business changes `timezone`, `local_date` shifts. Bump the business
tz version, rebuild all partitions from FactContribution (`occurred_at` is kept UTC), and
double-buffer the rollups. The UI shows "Recalculating reports…" (typically minutes).

**10.10.5 Semantic layer (custom analytics, top tier).** A registry maps metric IDs to
`{rollup, measure expression, allowed dims, basis}`. Custom metrics are formulas over registered
measures (e.g. `M-NET / M-W-HOURS`), validated with no division by zero. Custom metrics are never
SQL typed by the user, which prevents tenant-escape and slow queries.

**10.10.6 Caching.**
- Tile responses are cached by `(business, tile config hash, range, as_of watermark)`. The cache
  is invalidated when the partition watermark advances.
- Browser: ETag / 304.
- "Today" tiles poll every 60 s when the tab is visible; otherwise there is no polling. Server-sent
  "rollup advanced" pings are an option.

**10.10.7 Reconciliation (zero-drift).**
- Nightly per business: compare R1 cash M-COLLECTED against Agent 05 ledger totals per day, and
  R5 job counts against the job store.
- On mismatch: rebuild the affected days and log a `reporting.reconciliation.mismatch` metric.
- Alert ops if more than 0.1% of businesses mismatch.

### 10.11 Leaderboard & MVP
```
rows = for w in eligibleWorkers(range): {w, jobs: M-W-JOBS, reviews_n, reviews_bayes, revenue: M-W-REV}
if metric == 'composite':
   for m in metrics: pct_m(w) = percentRank(rows, m)       # 0..1, ties averaged
   score(w) = Σ weight_m * pct_m(w) / Σ weight_m
sort desc by metric, then revenue, jobs, name
rank = competition ranking (1,1,3)
mvp = rows[0] if rows[0].jobs >= 5 else null
delta_rank = rank in previous equivalent period − current rank
```
Workers who can see the leaderboard but lack the financial permission get the revenue column
hidden, not zeroed.

### 10.12 Forecasting (explainable, cheap)
[S: LLMS "forecasting"] — method `[I]`. Forecast for the next H days (30/60/90) of M-NET (and of job
counts), as a sum of components:
1. **Committed.** Σ expected value of jobs already scheduled in the horizon (status scheduled or
   confirmed; job total excluding tax and tip). Each job is multiplied by (1 − historical
   cancellation-plus-no-show rate for its source and lead-time bucket).
2. **Recurring not yet materialized.** For each active series (Agent 03), project occurrences in
   the horizon that are not yet generated. Each is multiplied by the series' historical completion
   rate (default 0.92).
3. **Subscriptions.** Expected renewal charges (Agent 06) are shown separately on the cash basis.
   On the accrual basis they are already reflected via auto-scheduled jobs, so they are not double
   counted.
4. **Not-yet-booked demand.** For each future day d:
   `baseline(d) = mean over last 8 same-weekdays of (net from jobs booked < lead_time(d) days before service)`.
   Seasonality index = last year's same-week ÷ last year's average week, if ≥ 1 year of history
   (clamped 0.5–2.0).
   The pickup curve is the share of a day's final revenue typically booked by k days before:
   `unbooked(d) = baseline_final(d) × (1 − pickup(k_d))`.
5. **Band.** ±1.28 σ of the residuals of the same method backtested over the past 12 weeks (an 80%
   interval). If there are fewer than 8 weeks of history, show only the committed and recurring
   components, labeled "Not enough history for a full forecast".

Output per day: `{committed, recurring, projected_unbooked, low, high}`. The tile shows the
components stacked, so the owner sees why a number is what it is.

### 10.13 Goal progress & pacing
```
value = metric(goal.metric_id, scope, period.start → min(now, period.end))
elapsed = businessDaysOrCalendarDays(period.start, now) / total(period)     # calendar default
expected = target * elapsed                           # linear; option "seasonal pacing" uses last year's shape
projected = value + forecastRemaining(goal.metric_id, now → period.end)   # 10.12 for money/jobs; linear run-rate otherwise
status = value >= target ? achieved : (projected >= target ? on_track : (value >= expected*0.9 ? at_risk : behind))
milestones: for m in (50,75,100) if pct >= m and m not in notified: notify A-13 (key goal:id:period:m)
```
For "lower is better" metrics (e.g. cancellation rate), the comparisons are inverted.

### 10.14 Audience evaluation at send time
- Compile the RuleGroup into a query over the contact read-model, which is a denormalized
  `contact_marketing_view` maintained by consuming events from 07/03/04/05/06. It has columns
  last_job_at, next_job_at, lifetime_net, jobs_count, tags[], zone_id, has_subscription,
  app_installed, consent flags, asset summaries.
- Always AND in the implicit consent filter.
- For counts above 50k, return an approximate count (HyperLogLog sketch) while typing and an exact
  count at launch.

### 10.15 Resolving recipients for a job (multiple people per contact)
- Each job has `notify_contacts[]` (Agent 03/04: chosen people and endpoints; "contact info to notify
  the customer" [S: T04]). If none are chosen, use the contact's primary person, primary mobile and
  primary email.
- Business customers [S: LLMS] can have a site contact (receives day-of messages) separate from a
  billing contact (receives invoices and receipts). The mapping is per catalog area: day_of → site
  contact; quotes/invoices/payments → billing contact; booking → both.
- Duplicate endpoints across people (a shared family phone) are deduped per message. The first
  person's name is used in variables.

### 10.16 Endpoint resolution
- `resolveEndpoint(recipient, channel)` picks the endpoint flagged for the channel.
- SMS: mobile line type preferred. A number never looked up is looked up once (async; first message
  proceeds).
- Email: the verified address is preferred.
- Push: all active device tokens of the user or contact. Push "delivered" means at least one
  device acknowledged. Tokens invalidated by FCM/APNs are deleted.

---

## 11. Edge cases & failure modes
All `[I]`. Each item gives the expected behavior.

### 11.1 Notifications & scheduling
1. **Job booked less than the reminder offset before start:** skip the reminder (no instant
   "reminder" after the confirmation).
2. **Job booked in the past** (allowed by Fieldd [S: LLMS]): no customer messages
   (`suppressCustomerNotifications`). Workers are notified only if the job is still open.
3. **Drag-reschedule bounced back and forth within the debounce:** net no-change → no message.
4. **Reschedule across a DST change:** the time renders in the job tz with the correct offset.
   The reminder `due_at` is recomputed from wall-clock time.
5. **Job in another time zone than the business** (mobile crew crossing a tz line; shop in another
   state): message times use the job tz plus its abbreviation. Reports bucket by business tz.
6. **Recurring series of 52 occurrences created at once:** one series confirmation only. Reminders
   are scheduled lazily as occurrences materialize (Agent 03), not 52 timers upfront.
7. **Series edited "all future":** cancel or reschedule timers per occurrence through events, and
   send one "plan changed" message (N-B10), not N messages.
8. **Customer has no mobile, only a landline:** SMS suppressed (landline), email fallback.
9. **Customer has no email and no phone:** nothing can be sent. The job shows a warning chip
   "Customer can't be notified". The owner can add contact info.
10. **Same person is both the customer and a worker** (employee booking a service): both message
    sets are sent. Endpoints are deduped only within the same message.
11. **On-my-way pressed twice, or for two jobs at once:** dedupe per job (once per 30 min unless the
    ETA changes by ≥ 15 min). For a route with 2 jobs at the same address, one message.
12. **ETA unavailable** (routing provider down): send without ETA ("on the way now"). Never
    block the on-my-way text.
13. **Job completed offline in the worker app and synced 6 hours later:**
    - The job-complete message uses the actual `completed_at`.
    - The review request is due at `completed_at + 2h`, which has already passed, so it sends now
      if within 24h, else it is skipped.
    - No "on my way" is sent after completion.
14. **Job reopened after completion** (Agent 04): the pending review request is canceled. A rating
    already given is kept.
15. **Cancellation with a fee, refund processing async:** N-B09 says "A refund of $X is being
    processed". A separate N-P05 is sent on refund success.
16. **Invoice partially paid between scheduling and sending a reminder:** the reminder renders the
    current balance. If the balance is 0, the reminder is skipped.
17. **Invoice voided, or job deleted:** subject validity check at render time → cancel.
18. **Quote revised after send** (`jobs.quote.revised`): reminders point to the latest version
    link. The old link redirects.
19. **Worker invite to an email already used by another business's worker:** the invite is still
    sent (identity is per user; tenancy membership is handled by 09).
20. **Invite bounces:** `comms.message.failed` maps to Agent 09 `Invite.deliveryStatus='bounced'`.
    Owner sees an "Invite bounced — fix email" chip.
21. **Timesheet approved and then changed to rejected within a minute:** debounce of 60s on W-07/W-08.
    Only the final state is sent.
22. **Worker deactivated while timers are pending for them** (daily agenda): skip. They are also
    removed from team chats.
23. **Business disables a notification while messages are deferred** (quiet hours): re-check at
    dispatch → canceled.
24. **Business edits a template while a reminder is scheduled:** the template is resolved at send
    time, so the latest version is used.
25. **Template renders over 10 SMS segments:** hard cap. Truncate optional sections (service list →
    "+2 more"). Block publishing if the required content exceeds 6 segments.
26. **Unicode customer name** (e.g. "José", "Nguyễn"):
    - "é" is in the GSM-7 basic set, so it stays GSM-7.
    - "ễ" is not in GSM-7 and forces UCS-2 (cost doubles).
    - Names are never transliterated. Cost is shown to the business.
27. **Very long business names in the SMS prefix:** use the SMS short name (≤ 20 chars).

### 11.2 Compliance
28. **STOP sent while a broadcast is mid-flight:** synchronous revoke plus cancellation of queued
    SMS to that endpoint. The in-flight provider call may still deliver one message (unavoidable).
    Audit shows the ordering.
29. **STOP to one business's number:** affects only that business (separate brand). Another business
    using the platform is unaffected.
30. **STOP and then an upcoming appointment:** all SMS halts. The job page shows "SMS opted out".
    Transactional messages go by email/push. If no channel remains → warning to staff on the job.
31. **Customer texts "Stop" inside a sentence** ("Can you stop by at 3?"): not an opt-out, because
    it is not a keyword-only message. The free-text detector marks it low confidence and does
    nothing.
32. **Customer texts "STOP" and then books online with the SMS checkbox ticked:** new written consent
    → marketing re-granted and SMS restored. The provider-level block is cleared via the provider
    API or by the customer texting START. Show "Reply START to receive texts" guidance on the
    confirmation page.
33. **Shared family phone; one person opts out:** the opt-out is per endpoint, so it applies to
    everyone using that number.
34. **Number reassigned to a new owner** (old customer's number): carrier deactivation data or RND
    → mark invalid. "Wrong number" replies → invalidate that endpoint for the contact.
35. **Imported list without consent attestation:** SMS marketing ineligible. The audience count shows
    "no consent: N".
36. **Staff attests verbal consent for SMS marketing:** allowed only as "express consent" for
    transactional purposes. Marketing needs written evidence (upload or web/keyword). The UI explains.
37. **Quiet hours with an unknown recipient tz:** area-code fallback. For multi-tz area codes, use
    the restrictive intersection. Unknown country code → business tz with the strictest window.
38. **Holiday blackout** (e.g. Dec 25): marketing deferred to the next allowed day. A drip step due
    on the blackout day shifts. Campaigns scheduled on blackout days are blocked at scheduling with
    a warning.
39. **10DLC campaign rejected:**
    - SMS stays disabled.
    - All transactional messages fall back to email, and marketing SMS steps are skipped.
    - The wizard shows the rejection reason and a resubmit option.
    - A-15 is sent.
40. **10DLC suspended for complaints:** drips and campaigns auto-pause (`paused_by_system`),
    transactional falls back to email, and the owner gets a remediation checklist.
41. **Email domain DNS removed later** (DKIM fails): the daily check marks it `degraded`. Sending
    switches back to the platform shared domain automatically and the owner is alerted.
42. **Gmail/Yahoo bulk sender thresholds** (complaints > 0.3%): auto-pause marketing for that
    business, with a review.
43. **Marketing email to a Canadian or EU recipient without opt-in:** blocked by the policy pack
    based on the recipient country (address/phone country).
44. **Customer requests data erasure** (07 `customer.customer.erased`):
    - Delete rendered bodies, conversation contents and attachments; anonymize the conversation.
    - Keep the consent ledger revocation and suppression of the hashed endpoint, so the person is
      never contacted again (legal basis: legitimate interest / record keeping).
    - Rollups keep aggregates. Customer-level rows are anonymized as "Deleted customer".
45. **Review incentive language in a template** ("leave a 5-star review for 10% off"): linter
    error, cannot publish.

### 11.3 Drips & campaigns
46. **"New customer" drip launched, then a CSV import of 3,000 customers:** 07 marks import-created
    customers with `createdVia=import`. Drip triggers exclude imports by default (07 already
    defaults "send welcome drip? off"), so there is no accidental blast. Option to include them, with
    a confirmation showing the count and cost.
47. **Contact merged during an enrollment:** move it to the survivor, or exit as a duplicate if the
    survivor is already enrolled. Never send twice.
48. **Contact enrolled, then deleted:** exit and cancel timers.
49. **Drip edited to remove the step a contact is waiting on:** migration rule (10.4).
50. **Drip paused for 2 weeks with steps due:** held. On resume, steps more than 72h late are skipped
    unless flagged "always send". Delays after resume are recomputed from the resume time.
51. **Timer service outage for 3 hours:**
    - On recovery, process the backlog in `due_at` order with a rate limit.
    - Quiet hours are re-checked.
    - The 72h late-skip prevents stale promos.
    - Transactional reminders whose subject time has passed are skipped (subject validity).
52. **Duplicate trigger events** (at-least-once bus): idempotent enrollment key → no duplicate.
53. **Abandoned booking followed by booking via a phone call 10 minutes later:** exit on
    `booking.appointment.created` for any source. The SMS is not sent if the booking arrives
    before the 2h delay.
54. **Abandoned session with only an email captured:** SMS steps skipped; email steps proceed.
55. **Holiday drip for a business in the Southern Hemisphere or another country:** the holiday table
    is per business country. Templates are flagged by region.
56. **Campaign scheduled at 09:00 "recipient local time" spanning 4 US time zones:** 4 waves. The
    campaign stays `sending` until the last wave.
57. **Audience changes between scheduling and send:** a dynamic audience is evaluated at send time
    (the UI says so). A snapshot audience is frozen.
58. **Budget cap reached mid-campaign:** pause with "Budget reached" → owner can raise the cap and
    resume.
59. **Two campaigns plus a drip all eligible for the same contact on the same day:** the frequency
    cap allows 1 SMS. Drip priority, then earliest scheduled, wins. Others are deferred or skipped.
60. **Postcard returned undeliverable:** the address is marked `undeliverable` (07). It is excluded
    from future postcards.
61. **Postcard QR scanned by someone other than the addressee** (neighbor): a lead is created with the
    address context and flagged "scan, not verified identity". The household match is hinted, not
    assumed.
62. **Short link clicked by a link-preview bot or email security scanner:**
    - Bot user agents are ignored, as are clicks within 2 seconds of delivery from known scanner
      IP ranges. A HEAD request is not a click.
    - These clicks are excluded from metrics and attribution, but the redirect still works.

### 11.4 Inbox & chat
63. **Inbound SMS from an unknown number:** new "Unknown" conversation with a lead suggestion. If the
    body contains a name or address, the AI may propose one (optional).
64. **Same phone belongs to 2 different customers:** one conversation listing both. The staff
    composer shows a "Which customer?" chip for linking a job.
65. **MMS image from a customer:** stored, virus-scanned, max 5 MB. HEIC is converted to JPEG for
    preview.
66. **Customer replies to an automated reminder with "C" or "Yes":** confirmation intent (emits an
    event). A thumbs-up "Liked …" iMessage tapback text is detected and treated as acknowledgment.
67. **Customer replies after hours:** at most one auto-reply per 12h. An SLA timer runs only within
    business hours.
68. **Worker leaves the company while assigned to conversations:** reassign them to Unassigned and
    notify the owner.
69. **Webchat spam or bot:** rate limits, honeypot field and a challenge after 3 messages/10s.
    Messages with links from anonymous visitors are held for review.
70. **Two staff replying simultaneously:** both send (no lock), with a "collision" indicator shown when
    another user is typing in the same thread.
71. **Email reply with a giant quoted history or signature:** the parser strips it. Raw source is
    available on demand.
72. **Provider delivers an inbound message twice:** dedupe by provider SID.

### 11.5 Reporting
73. **Refund posted for a job in a closed period:** posted view → refund-date bucket. Restated view →
    original period changes. Goals already closed are not re-opened (snapshot kept).
74. **Job reopened and re-completed in a later month:** the job count moves to the latest completion
    date. Revenue follows the ledger (Agent 05 reversal and repost, if any).
75. **Split job with 3 workers and $100.00 net:** shares of 3333/3333/3334 bp → $33.33/$33.33/$33.34.
    The remainder goes to the lead worker. Each worker gets 1 job count.
76. **Worker removed from a job after completion** (correction): the attribution contribution is
    reversed and re-added for the new assignment through compensating rows.
77. **Business with 0 data:** every tile shows its designed empty state. Ratios show "—".
78. **Comparison with a previous period of different length** (Feb vs Jan): compare equal-length
    windows aligned by day index. "Same period last year" aligns by weekday when the grain is week.
79. **Business timezone changed:** full rebuild (10.10.4). Exports during the rebuild are marked with
    the as-of time.
80. **Currency change** (rare; new business setup): blocked once any money exists. A multi-business
    hub sums only within a currency.
81. **Huge export** (2M message events): async, gzip CSV, split files of 1M rows, and an expiry
    notice.
82. **Leaderboard with worker privacy:** workers in the team view see ranks and jobs. Revenue is
    hidden unless the owner opts in.
83. **Clock skew between worker devices:** reporting uses server-received timestamps for ordering.
    Device times are used only for the offline-completion `completed_at`, clamped to ≤ server
    receive time.
84. **Forecast with insufficient history:** components only (10.12).
85. **Rollup drift detected nightly:** auto-rebuild of the affected days, with an ops alert.
86. **Daylight-saving day:** day buckets of 23/25 hours. Hourly charts show the duplicated 01:00
    hour twice with offsets.

### 11.6 Providers & infrastructure
87. **SMS provider outage:**
    - Transactional falls back to email/push after 2 failed attempts or 5 minutes, whichever is
      first.
    - Marketing is held, with a 72h skip rule for drips.
    - Status banner in the CRM.
88. **Email provider outage:** fail over to the secondary ESP (dual DKIM). Marketing waits.
89. **DLR webhooks delayed or missing:** `sent_unconfirmed` after 72h.
90. **Webhook replay or forgery:** signature check plus timestamp tolerance (5 min) plus event-id
    dedupe.
91. **Secret rotation mid-traffic:** dual keys accepted during the overlap.
92. **Provider rejects content** (carrier spam filter, error 30007-style):
    - Mark the message failed with the reason "Carrier filtered".
    - After 3 or more filtered sends of the same template, auto-pause that template's campaign and
      show content advice (remove link shorteners, ALL CAPS, etc.).
93. **Cost estimate differs from the actual cost** (segments recalculated by the provider): the
    actual cost from the provider wins in billing. Estimates are labeled "est."

---

## 12. Better than Fieldd

### 12.1 Concrete improvements (all `[I]`; contrasted with what sources show)
1. **One explainable catalog.**
   - Every automated message is listed in one settings screen with trigger, timing, channels,
     quiet-hours class and a "Send test" button.
   - Fieldd mentions automated updates and settings scattered around Settings → Communication →
     Email [S: T04].
   - Owners can answer "what does my customer receive and when?" in one place.
   - Includes a **customer journey preview**: pick a sample job and see the full message timeline.
2. **Reminders that can't arrive late or at night.** Pull-forward quiet-hours logic, job-tz
   rendering and DST-safe math (10.2).
3. **Drag-reschedule without spam.** A 2-minute debounce and net-change detection (Fieldd notifies
   "automatically" [S: T08], which can mean several texts if the user adjusts twice).
4. **Drip engine with branches, goals and safe edits.**
   - If/else, wait-until-event and exit on booking are included.
   - Versioned edits with an explicit in-flight migration choice. Fieldd shows linear steps
     [S: T12].
   - A test run with compressed delays.
5. **Honest "revenue generated".**
   - The attribution rule is visible in the UI ("Last touch: click 7d / code 30d / delivered 3d").
   - Pipeline (booked) value is separate from realized value.
   - Refunds are subtracted and there is no double counting across drips.
   - Fieldd shows a "business generated" number with no definition [S: T12].
6. **Compliance by construction.**
   - Consent ledger with evidence, keyword handling and free-text opt-out detection.
   - One-click unsubscribe headers, a toll-free starter path and a guided 10DLC wizard.
   - Owners cannot accidentally break TCPA. Imports default to no marketing consent.
7. **Ungated review booster.** Policy-safe by default. It protects the business's Google profile
   while still catching unhappy customers privately (5.9).
8. **Cost transparency.** Live segment counter, per-campaign cost and a monthly budget cap that pauses
   marketing but never on-my-way texts. GSM-7 normalization roughly halves SMS cost.
9. **Unified inbox with context.**
   - Automated messages appear inline in the thread, so staff see what the customer already got.
   - Job, invoice and consent context sit in a side panel.
   - Masked worker messaging protects staff numbers.
   - "C to confirm" replies update the job.
10. **Every number is defined.**
    - An "i" popover on every tile shows its formula, basis (accrual/cash), time zone and as-of
      time.
    - Cash and accrual are never silently mixed.
    - CSV headers include the basis.
11. **Fast dashboards.**
    - Pre-aggregated, incrementally refreshed rollups with p95 < 300 ms per tile.
    - Tiles load independently with skeletons and no layout shift.
    - Nightly reconciliation against the payments ledger means the dashboard never disagrees with
      Transactions.
12. **Explainable forecast.** Committed + recurring + projected components with a confidence band,
    instead of a black-box line.
13. **Fair leaderboard.**
    - The Bayesian rating average stops a single 5★ review from winning.
    - Split-job crediting is defined.
    - An optional composite score.
    - Team view can hide revenue.
14. **Brand-perfect messages** (owner requirement). Every email, landing page and SMS link is on the
    tenant's domain and theme, including dark-mode logo variants and font fallbacks (5.13).
15. **Accessibility.**
    - Emails pass contrast checks (with auto-fix), and alt text is enforced.
    - Landing pages work without JavaScript (unsubscribe, preferences).
    - Inbox is keyboard-navigable with screen-reader labels.
    - Charts have data-table equivalents (also used for CSV).
16. **Reliability.**
    - Idempotent sends, durable timers and an outbox-based event intake.
    - Provider failover for email, and channel fallback for SMS outages.
    - Status banners instead of silent failures.
17. **AI with guardrails.** The AI marketing builder [S: LLMS] produces drafts only. The same lint and
    compliance checks apply. A launch from MCP/Darwin requires human confirmation.
18. **Offline-tolerant worker flows.** Messages triggered by offline actions use actual event times,
    and stale ones are suppressed (11.1 #13).

### 12.2 Budget tier notes (starter vs. higher plans) — proposal `[I]`
| Capability | Starter (new solo business) | Mid | Top | Rationale |
|---|---|---|---|---|
| Full transactional catalog (confirmations, reminders, on-my-way, receipts, invoices, quotes) | ✔ | ✔ | ✔ | Core value; Fieldd includes this in Start [S: LLMS] |
| SMS sender | Toll-free verified number (no monthly campaign fee) | TF or local 10DLC | Multiple numbers, local presence per zone | Cheapest compliant path first |
| Included usage | ~500 SMS segments, 5k emails/mo | ~2,500 segments, 25k emails | ~10k segments, 100k emails | Pass-through overage plus small margin |
| Email campaigns | ✔ (unlike Fieldd Start) | ✔ | ✔ | Email is nearly free; grows small businesses |
| SMS campaigns | ✔ | ✔ | ✔ | Fieldd Start includes SMS marketing [S: LLMS] |
| Postcards | Pay per piece | Pay per piece | Neighbor-marketing automation | Variable cost |
| Engage drips | 3 live drips, templates | Unlimited, branches | + API/MCP triggers, priority | Fieldd gates drips at Scale [S: LLMS] |
| Push marketing | — (needs app) | ✔ with branded app | ✔ | Depends on customer app |
| Review booster | ✔ | ✔ + GBP review sync | ✔ + reply from CRM | Fieldd Start [S: LLMS] |
| Referrals | — | ✔ | ✔ | Fieldd Scale [S: LLMS] |
| Inbox, two-way SMS, webchat, team chat | ✔ | ✔ | ✔ + SLA rules, routing | Communication is core |
| Dashboard and core reports, CSV | ✔ | ✔ | ✔ | Fieldd shows these broadly [S: T09] |
| Goals | 2 active | 10 | 20 | — |
| Custom report views, forecasting, MVP | MVP ✔; views 3 | ✔ | ✔ | — |
| Heatmaps, custom metrics, multi-business hub | — | — | ✔ | Fieldd Pro [S: LLMS] |
| Scheduled report emails | Weekly summary | ✔ | ✔ | — |
| BYO provider credentials, RND checks, SMS redundancy | — | — | ✔ | Cost/complexity |
| Remove "Powered by" | — | — | ✔ (or per Agent 01/11) | Fieldd Pro [S: LLMS] |

---

## 13. Open questions
1. **Default revenue basis.** Should the Dashboard "Revenue" default to **accrual (net sales on
   completion)** or **cash (collected)**? Fieldd's Transactions is payment-based [S: T17], but its
   dashboard definition is unknown. This affects every tile and goal.
2. **SMS sender strategy for starter.** Toll-free verification for every new business (cheap and fast;
   some carriers filter TF marketing more strictly), or local 10DLC numbers (better recognition;
   monthly fees and Sole Proprietor limits)? Do we let businesses port in their existing number
   (two-way texting on their current business line)?
3. **Who pays for messaging:** included allowances plus pass-through overage, or BYO provider
   accounts? What margin? What hard cap behavior is acceptable for transactional SMS when a budget
   runs out?
4. **Review booster policy.** Confirm the owner accepts the **ungated** default (no rating-based
   routing to Google), given competitors may gate. Also: should we pursue Google Business Profile
   API verification for review sync and replies?
5. **Attribution windows and model.** Accept last-touch with click 7d / code 30d / delivered 3d? Should
   recurring-series value be attributed (first job only vs 90-day value)?
6. **Fieldd's actual notification defaults:** which messages exist, their wording, reminder offsets,
   and whether the business can edit SMS text for each. This needs the owner's CRM video and
   Settings → Communication screens.
7. **Online booking approval mode:** does Fieldd support "request" bookings that need approval
   (N-B02/N-B03)? Coordinate with Agents 01/03.
8. **Worker-facing leaderboard:** should workers see it by default, and should revenue be visible to
   them?
9. **International scope for v1:** US and Canada only, or also UK/AU (alphanumeric sender IDs,
   different consent regimes)? Fieldd claims 37 countries [S: LLMS].
10. **Postcard vendor and payment flow:** charge the business per order on their card, or deduct
    from a prepaid balance? Is minimum order size acceptable?
11. **Voice:** do we need call tracking or masked calling (worker → customer via business number) in
    v1? It is out of scope here.
12. **Consent at CRM booking:** when staff book by phone [S: T15], may they tick "customer agreed to
    texts" for transactional SMS (yes) and for marketing (we propose no without written evidence)?
    Confirm this UX with the owner.
13. **Event-name ratification** (9.1): Agent 10 to pick canonical names. Requested new events
    from 05/06/09: `payment.card.expiring`, `subscription.renewal.upcoming`,
    `subscription.dunning.exhausted`, a commitment pre-end event, and
    `timesheet.punch.open_too_long`.
14. **AI features at launch:** AI marketing builder and AI opt-out intent classification. Are
    LLM costs per business acceptable on the starter tier?
15. **Data retention periods** for message bodies and conversations (we propose 25 months), and
    whether businesses can export all conversations.

---

## 14. Verify against video
Check each item in the owner's Fieldd CRM walkthrough (CRM side) and the swash.co walkthrough
(customer side). Record findings next to each item.

### 14.1 Notifications & settings
- [ ] Exact Settings path and sub-pages under **Settings → Communication** (Email, SMS, Push?) [S: T04]. List every toggle shown.
- [ ] Full list of automated customer notifications Fieldd offers, with names, channels and default on/off. Map each to catalog IDs (5.1) and add any missing.
- [ ] Reminder timing options: how many reminders, offsets (24h? 2h?), day-before at fixed time?
- [ ] Can the business edit SMS text per notification? Which variables (dynamic fields) are offered, and what is the variable picker UI?
- [ ] Quote and invoice email look and "reminder settings" screen [S: T04]: fields and defaults.
- [ ] Drag-to-reschedule [S: T08]: is there a confirm dialog with a "notify customer" checkbox, or does it notify silently? Who gets notified?
- [ ] Booking from CRM [S: T15]: is there a "notify customer" toggle? Which messages fire?
- [ ] On-my-way: worker app button label, ETA shown to the customer, tracking page look (swash.co side).
- [ ] Job-complete message: does it include photos? A receipt? A review request?
- [ ] Review booster: settings screen, delay, message text, rating page look, whether it gates low ratings.
- [ ] Worker invite email content and app-download flow [S: T13].
- [ ] Timesheet approve/reject push wording in the worker app [S: T03].
- [ ] In-app notification bell: what appears, grouping, read states (new dashboard "notifications" [S: LLMS]).
- [ ] Opt-out UI: how a customer unsubscribes; preference page look; STOP auto-reply wording.
- [ ] SMS sender setup: does Fieldd show 10DLC/toll-free registration screens, or a number assignment?

### 14.2 Marketing & Engage
- [ ] Marketing tab layout: tabs/sections (Campaigns, Engage/drip window, Templates, Postcards, Referrals?) [S: T10, T12].
- [ ] `New → Drip` flow screens [S: T12]: template gallery categories and count, trigger types list (all "action" options; date options), step types offered (email, delay, SMS, anything else: push, branch?).
- [ ] Delay units available (minutes/hours/days?) and max.
- [ ] Review screen: the "label" field, the send-timing options besides ASAP [S: T12], and any audience or "apply to existing" options.
- [ ] Drip metrics window: exact labels for "in drip", "completed", "revenue generated" [S: T12]. Is a definition or window shown? Is there a per-step breakdown?
- [ ] Editing a live drip: warnings shown, and what happens to contacts in flight.
- [ ] Email editor [S: T12]: block types, template list, how brand logo and colors are applied, mobile preview, test send.
- [ ] SMS composer: character/segment counter? Booking link insertion? Opt-out text auto-added?
- [ ] Campaign wizard for email/SMS/postcard [S: T10]: targeting options (tags, last booked, service area, etc.), scheduling, cost display.
- [ ] Postcard designer and ordering flow: sizes, pricing, QR generation, proof [S: T20].
- [ ] Campaign analytics view: which metrics are shown (sent, opened, clicked, booked, revenue?) [S: T10].
- [ ] AI marketing builder entry point and output [S: LLMS].
- [ ] Referral settings and referral tracking screen [S: LLMS].
- [ ] Abandoned booking follow-up: is it a drip template or a separate automation? Default delay? [S: T12]

### 14.3 Communication
- [ ] Live chat / conversations screen layout [S: LLMS]: list, thread, channels shown, assignment, search.
- [ ] Team group chats: creation, membership, job-linked threads [S: LLMS].
- [ ] Worker app "message the client" [S: T02]: does it reveal numbers? Does it use the business number?
- [ ] Two-way SMS: do customer replies to reminders land in the inbox? Any auto-confirm keywords?
- [ ] Booking-site live-chat widget on swash.co: position, offline behavior, contact capture.

### 14.4 Dashboard & reporting
- [ ] Dashboard tile list and default layout [S: T09, LLMS]: goals, all reviews, all photos, team availability, notifications, active now. Record each tile's chart type and period selector.
- [ ] Customize flow: add/remove/reorder/resize tiles? Per-user or per-business?
- [ ] Goals: creation form fields, metrics available, progress display [S: LLMS].
- [ ] Reporting tab: list of report pages, filters, group-by options, custom views save flow [S: T09, LLMS].
- [ ] Download behavior: CSV vs image, filename, columns [S: T09].
- [ ] Revenue definition hints: does the tile say gross/net, or include tips/tax? Compare a known job's numbers between the dashboard and Transactions to infer the basis.
- [ ] Forecasting view: what is forecast and how it is shown [S: LLMS].
- [ ] MVP view and leaderboard: metrics, period options, tie display, whether workers can see it [S: T09, LLMS].
- [ ] Service-area performance view and heatmap (Pro) [S: T09, LLMS].
- [ ] Booking sources view on the dashboard [S: T05]: source names and how they are captured.
- [ ] Transactions → worker summary week picker [S: T17]: confirms default week and week start day.
- [ ] Data freshness: does a new booking appear instantly on the dashboard? Is any "as of" time shown?

### 14.5 Customer side (swash.co video)
- [ ] Consent checkboxes on the booking flow (SMS and email), with wording and default state.
- [ ] Confirmation email and SMS received after booking: sender name/number, branding, links on the business's subdomain or Fieldd domain.
- [ ] Reminder and on-my-way messages as received, with timing relative to the appointment.
- [ ] Link domains in messages (branded subdomain vs platform domain); look of the tracking, rating, pay and manage pages.
- [ ] Unsubscribe link in marketing emails and the preference page look.
