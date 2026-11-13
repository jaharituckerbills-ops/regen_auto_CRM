# 09 — Team, Permissions & Timesheets

> Agent 09 · Phase 1 "CRM brain" · 2026-11-12
> Scope: the business's people (team members / workers), tenancy & accounts, authentication & sessions,
> invites, RBAC (role presets + per-member overrides + field-level visibility), plan-tier entitlements,
> availability (weekly hours, time off, zone/shop, vehicles, capacity), Google Calendar sync,
> timesheets (clock in/out, breaks, approval, pay), compensation (effective-dated hourly rates),
> overtime, rounding, payroll export.

## Source legend

| Tag | File |
|---|---|
| `[S: T03]` | `research/transcripts/03_-d1cjZPEmoQ.txt` (+ `.description`) — Timesheets |
| `[S: T13]` | `research/transcripts/13_C2EsjjXfOOE.txt` (+ `.description`) — Add Team Member |
| `[S: T02]` | `research/transcripts/02_rMVRwp-anZM.txt` — Route Optimization (worker app routes) |
| `[S: T04]` | `research/transcripts/04_pASG8RTrhWc.txt` — Quotes & Invoices (estimators, preferred worker, worker app) |
| `[S: T07]` | `research/transcripts/07_uD5ND6GyoYA.txt` — Add-ons (worker app POS upsell) |
| `[S: T08]` | `research/transcripts/08_w3D1W6oOaNY.txt` — Jobs & Appointments (drag to staff member, filter by staff) |
| `[S: T09]` | `research/transcripts/09_xO4jahos3rE.txt` — CRM Tips (People tab, worker profiles, export, leaderboard) |
| `[S: T11]` | `research/transcripts/11_8sT9cFazZAA.txt` — Service Areas (add staff to zone, shops in People tab) |
| `[S: T14]` | `research/transcripts/14_5zqqoRbV1DU.txt` — Recurring job (best available worker) |
| `[S: T15]` | `research/transcripts/15_Dh1Ib3a4psU.txt` — Book a job (first available worker, notify team) |
| `[S: T16]` | `research/transcripts/16_G43PcI4hWOk.txt` — Payment methods (worker marks complete, POS) |
| `[S: T17]` | `research/transcripts/17_UezcRXW1BcE.txt` — Transactions (worker summary revenue) |
| `[S: T19]` | `research/transcripts/19_ZPf7JSlvIcw.txt` — Schedule a job (closest worker, no double bookings) |
| `[S: llms]` | `research/site/llms.txt` — pricing tiers, 2026 features, MCP |
| `[I]` | Inference / industry convention / proposal. Not confirmed by any source. |

Notation used in tables: **A** = all records in the business, **Z** = records in the member's assigned
service areas/shops, **C** = records of the member's crew(s), **O** = own records only (assigned to /
created by the member), **R** = read-only, **—** = no access. Money is always integer minor units (cents).

---

## 1. Summary

This module answers three questions for every business on the platform: **who works here**, **what are
they allowed to see and do**, and **how much time did they work and what are they owed for it**.

- A business (tenant) is created by its **owner**. The owner adds team members via **New → Worker**
  with name, phone, email, address and photo, assigns them to **service areas** (and shops), picks the
  **services they can be assigned to**, sets **mobile-app and CRM permissions**, and a **calendar
  color**; saving sends an **email invite** to the team app, and the member appears in the CRM after
  they log in `[S: T13]`.
- Members have **availability** (weekly hours, time off, max jobs/day, zone/shop, default vehicle) that
  the dispatch engine (Agent 03) reads to auto-assign jobs `[S: T13, T14, T15, T19]`, `[I]` for details.
- **Timesheets** are switched on in **Settings → Account → Workers**; each worker additionally needs a
  mobile-app permission ("view timesheets" = clock in/out; "edit timesheets" for foremen/managers)
  `[S: T03]`. Workers clock in (blue → green), take paid/unpaid breaks (orange), stop, review a summary
  with total hours, and save — the entry is submitted to the CRM, where an owner/operator/admin can
  add/edit punches and **approve / reject / mark paid**; the worker is notified `[S: T03]`.
- Pay is an **hourly rate with an effective date**; future-dated raises apply automatically `[S: T03]`.
  We add overtime rules, rounding, auto-close for forgotten clock-outs, geofencing, offline clock-in,
  and payroll export `[I]`.
- One **user** (a person with a login) can belong to **several businesses**; Pro-tier businesses can
  be grouped under a **multi-business hub** `[S: llms]`.

---

## 2. Roles

### 2.1 Principals (who can act)

| Principal | Description | Source |
|---|---|---|
| **User** | A human identity (one per person, global across businesses). Holds credentials. | `[I]` |
| **Membership** (a.k.a. Team member / Worker profile) | A user's seat inside one business, carrying role, permissions, zones, services, rates. A user can have many memberships. | `[I]`; "worker profile" `[S: T03, T13]` |
| **Customer** | End customer of the business. Separate identity pool from team users (see 9.1, Agent 01/07). Never holds a membership. | `[I]` |
| **System** | Scheduled jobs (rate activation notices, auto clock-out, invite expiry), dispatch engine. | `[I]` |
| **API / MCP client** | CRM MCP has "full business-level access"; Bookings MCP is "customer-level access only" `[S: llms]`. We model each connection as a scoped **service principal** owned by a membership. | `[S: llms]` + `[I]` |
| **AI assistant (in-app)** | Fieldd's Darwin can "do anything a business can do" `[S: llms]`. Ours always acts **as the signed-in membership** and is bound by that membership's permissions. | `[I]` |
| **Platform support (us)** | Break-glass impersonation, time-boxed, consented, fully audited. | `[I]` |

### 2.2 Role catalog (system presets)

Fieldd sources name these roles explicitly: **owner, operator, admin** (can enable timesheet
permissions), **foreman, manager** (should get "edit timesheets"), **worker** `[S: T03, T13]`, and
**estimators** (staff on site who create quotes in the worker app) `[S: T04]`. The exact capability of
each Fieldd role is not shown in any source; the definitions below are our proposal `[I]`.

| Role | Intent | Surfaces | Seat? |
|---|---|---|---|
| **Owner** | Legal/billing owner of the business. Exactly one per business. Can do everything, including plan/billing, ownership transfer, business deletion, and data export. | CRM + team app | Yes |
| **Operator** | Co-owner / general manager. Everything the owner can do **except** transfer ownership, delete the business, change the plan/payment card for the subscription. Named alongside owner/admin as able to manage permissions `[S: T03]`. | CRM + team app | Yes |
| **Admin** | Office administrator / dispatcher. Full operational access + most settings. Cannot manage plan/billing, cannot grant Operator/Owner, cannot see Operator/Owner compensation, cannot delete business. Can manage team permissions `[S: T03]`. | CRM + team app | Yes |
| **Manager** | Runs a territory or team. Operational access scoped to assigned zones/shops (configurable to "all"). Approves timesheets & time off of members in scope; edits timesheets `[S: T03]`. No settings except own-zone team availability. No refunds above a limit. | CRM + team app | Yes |
| **Foreman** | Field crew lead. Primarily team app. Sees crew's jobs, can reassign within crew, edits crew timesheets `[S: T03]`, can collect payments, limited CRM (calendar for crew). | Team app (+ limited CRM) | Yes |
| **Estimator** | Sales/estimating staff on site. Creates quotes, leads, customers; books jobs from accepted quotes `[S: T04]`. No dispatch of others, no payments beyond deposits, no timesheet edit. | Team app + limited CRM | Yes |
| **Worker** | Technician/cleaner/detailer. Own jobs only, job workflow, POS on own jobs `[S: T16]`, upsell add-ons `[S: T07]`, clock in/out if permitted `[S: T03]`. No CRM by default. | Team app | Yes |
| **Bookkeeper** *(our addition)* | External accountant. Read transactions, reports, timesheets; approve/pay/export payroll; no customer PII beyond names; no jobs editing. | CRM | Free seat (proposal) `[I]` |

Rules:
- Roles are **presets** (bundles of permission keys). Fieldd shows **per-worker permission toggles** for
  "mobile app" and "CRM" `[S: T03, T13]`, so every member can additionally carry **overrides**
  (grant/revoke individual keys) on top of the preset `[I]`.
- **Custom roles** (named presets created by the business) are a higher-tier feature (see 5.9) `[I]`.
- A membership has exactly **one role**; scope (zones/shops/crews) is separate from role.
- "Worker" in Fieldd's UI is the generic word for any team member ("New → Worker" `[S: T13]`); our UI
  says "Team member" in lists and keeps "Worker" as the default role name `[I]`.

### 2.3 Permission model

```
effective_permissions(membership) =
    role_preset(membership.roleId).keys
  ∪ membership.overrides.granted
  − membership.overrides.revoked
  ∩ plan_entitlements(business.plan)          // keys whose feature is not in plan are removed
  ∩ surface_mask(request.surface)             // 'crm' | 'team_app' | 'api' | 'mcp'
```

Each key has the form `module.resource.action` and an optional **scope** suffix (`:own | :crew | :zone |
:all`). Holding a wider scope implies narrower ones (`all ⊃ zone ⊃ crew ⊃ own`, with `crew` and `zone`
treated as independent unions — a manager with `:zone` and foreman with `:crew` see the union of both
sets if both are granted). `[I]`

Two **surfaces** are toggled independently on every member, mirroring Fieldd's "permissions for both the
mobile app and the Field CRM" `[S: T13]`:
- `surface.crm.access` — may sign into the web CRM at all.
- `surface.team_app.access` — may sign into the team (worker) app at all.

### 2.4 Full permission matrix (role presets)

Legend: A/Z/C/O/R/— as defined above; `A*` = all, with a monetary limit configurable in settings;
`opt` = off by default in the preset, commonly granted by override.

#### 2.4.1 Dashboard, schedule, jobs (Agents 03, 04)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `dashboard.view` (tiles incl. revenue) | A | A | A | Z | — | — | — | A (R) |
| `dashboard.team_activity.view` ("who is active right now" `[S: llms]`) | A | A | A | Z | C | — | — | — |
| `calendar.view` | A | A | A | Z | C | O | O | — |
| `calendar.view_unassigned` | A | A | A | Z | C | opt | opt | — |
| `jobs.view` | A | A | A | Z | C | O | O | R |
| `jobs.create` (book job / appointment) | A | A | A | Z | C | O | opt | — |
| `jobs.create_in_past` (`[S: llms]` "schedule … jobs in the past") | A | A | A | opt | — | — | — | — |
| `jobs.edit` (services, notes, address) | A | A | A | Z | C | O | O (notes, photos, checklist only) | — |
| `jobs.reschedule` (drag on calendar `[S: T08]`) | A | A | A | Z | C | O | — | — |
| `jobs.assign` (assign/reassign worker, split between members `[S: llms]`) | A | A | A | Z | C | — | — | — |
| `jobs.claim_unassigned` (self-assign open job) | — | — | — | — | opt | opt | opt | — |
| `jobs.cancel` | A | A | A | Z | C | O | — | — |
| `jobs.delete` (hard delete — draft only) | A | A | A | — | — | — | — | — |
| `jobs.workflow.run` (on-my-way, start, checklist, photos, complete `[S: T02, T16]`) | A | A | A | Z | C | O | O | — |
| `jobs.override_service_area` (book outside zone `[S: T11]`) | A | A | A | Z | — | — | — | — |
| `routes.view` | A | A | A | Z | C | — | O | — |
| `routes.start` (swipe start route `[S: T02]`) | A | A | A | Z | C | — | O | — |
| `routes.manage` | A | A | A | Z | — | — | — | — |
| `recurring.manage` (edit series, commit to all future `[S: T14]`) | A | A | A | Z | — | O | — | — |
| `quotes.create_send` (`[S: T04]` estimators) | A | A | A | Z | C | O | opt | — |
| `quotes.view` | A | A | A | Z | C | O | O | R |
| `invoices.create_send` | A | A | A | Z | C | O | opt | — |
| `invoices.view` | A | A | A | Z | C | O | O | A (R) |

#### 2.4.2 Customers, leads, assets (Agent 07)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `customers.view` | A | A | A | Z | C (job-linked) | A | O (job-linked) | R (name, billing only) |
| `customers.create` | A | A | A | A | C | A | opt | — |
| `customers.edit` | A | A | A | Z | C (job-linked) | A | — (assets on own jobs: opt) | — |
| `customers.delete_merge` | A | A | A | — | — | — | — | — |
| `customers.import` (CSV) | A | A | A | — | — | — | — | — |
| `customers.export` (CSV) `[S: T09]` | A | A | opt | — | — | — | — | — |
| `customers.contact_info.view` (phone/email) | A | A | A | Z | C | A | O (time-boxed, see 2.5) | — |
| `leads.view` / `leads.manage` (lead board) | A | A | A | Z | — | A | — | — |
| `assets.manage` (vehicles, homes) | A | A | A | Z | C | O | O (add/update on own job) | — |

#### 2.4.3 Payments & money (Agents 05, 06)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `payments.collect` (POS: card, card-on-file, cash, check `[S: T16]`) | A | A | A | Z | C | O | O | — |
| `payments.charge_card_on_file` | A | A | A | Z | C | O (deposit only) | O (own job, job amount only) | — |
| `payments.record_offline` (cash/check) | A | A | A | Z | C | O | O | A |
| `payments.discount.apply` | A | A | A | Z | C (limit) | O (limit) | opt (limit) | — |
| `payments.price.override` (change line price) | A | A | A | Z | opt | O | — | — |
| `payments.addons.add` (upsell before complete `[S: T07]`) | A | A | A | Z | C | O | O | — |
| `payments.refund` | A | A | A* | Z* (default $0 = off) | — | — | — | — |
| `payments.void` (same-day) | A | A | A | Z | — | — | — | — |
| `transactions.view` (`[S: T09, T17]`) | A | A | A | Z | — | — | O (own collections only) | A |
| `transactions.export` | A | A | A | — | — | — | — | A |
| `payouts.view` (processor payouts `[S: T17]`) | A | A | opt | — | — | — | — | A |
| `disputes.manage` | A | A | A | — | — | — | — | R |
| `processor.settings` (bank account, payouts) | A | — | — | — | — | — | — | — |
| `subscriptions.sell` | A | A | A | Z | C | O | opt | — |
| `subscriptions.manage` (pause/cancel/edit packages) | A | A | A | — | — | — | — | — |
| `giftcards.issue` | A | A | A | opt | — | — | — | — |
| `discounts.manage` (codes) | A | A | A | — | — | — | — | — |
| `tips.view_own` | — | — | — | O | O | O | O | — |
| `tips.view_all` | A | A | A | Z | — | — | — | A |

#### 2.4.4 Reports, marketing, comms (Agent 08)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `reports.view` (revenue, services, customers `[S: T09]`) | A | A | A | Z | — | — | — | A |
| `reports.export` | A | A | A | — | — | — | — | A |
| `reports.labor_cost.view` (timesheet $ in reports) | A | A | opt | — | — | — | — | A |
| `leaderboard.view` (`[S: T09]`) | A | A | A | Z | C | A | opt (configurable "show team leaderboard in app") | — |
| `leaderboard.view_revenue_column` | A | A | A | Z | — | — | — | — |
| `marketing.campaigns.manage` (`[S: T10, T12]`) | A | A | A | — | — | — | — | — |
| `marketing.campaigns.view` | A | A | A | R | — | — | — | — |
| `reviews.view` / `reviews.reply` | A | A | A | Z | R(C) | — | R(O) | — |
| `inbox.customer_chat` (message client `[S: T02]`) | A | A | A | Z | C | O | O | — |
| `inbox.team_chat` (group chats `[S: llms]`) | A | A | A | A | A | A | A | — |

#### 2.4.5 Settings, catalog, coverage, integrations (Agents 01, 02)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `settings.business_profile` | A | A | A | — | — | — | — | — |
| `settings.catalog.manage` (services, prices, add-ons `[S: T06, T07]`) | A | A | A | — | — | — | — | — |
| `settings.catalog.view_cost` (service cost/profit `[S: T06, T19]`) | A | A | A | opt | — | — | — | A |
| `settings.coverage.manage` (zones, shops, travel fees `[S: T11]`) | A | A | A | — | — | — | — | — |
| `settings.online_booking.manage` (`[S: T05]`) | A | A | A | — | — | — | — | — |
| `settings.communication.manage` (email templates `[S: T04]`) | A | A | A | — | — | — | — | — |
| `settings.payments.manage` (deposits, pre-auth `[S: T04]`) | A | A | opt | — | — | — | — | — |
| `settings.toolkit.manage` (feature toggles e.g. route optimization `[S: T02]`) | A | A | A | — | — | — | — | — |
| `settings.integrations.manage` (calendar, accounting, analytics) | A | A | A | — | — | — | — | — |
| `settings.api_mcp.manage` (MCP connection `[S: llms]`, API keys) | A | A | opt | — | — | — | — | — |
| `settings.audit_log.view` | A | A | A | — | — | — | — | R |
| `settings.data_export.full` (account export) | A | — | — | — | — | — | — | — |
| `tenant.plan_billing.manage` | A | — | — | — | — | — | — | — |
| `tenant.ownership.transfer` | A | — | — | — | — | — | — | — |
| `tenant.delete` | A | — | — | — | — | — | — | — |
| `tenant.hub.manage` (multi-business hub `[S: llms]`) | A | opt | — | — | — | — | — | — |

#### 2.4.6 Team, permissions, timesheets (this module)

| Permission key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `team.view` (People tab, team list `[S: T09, T13]`) | A | A | A | Z | C | R (names only) | R (names/photos of crew) | R (names) |
| `team.create_invite` (New → Worker `[S: T13]`) | A | A | A | Z (roles ≤ Foreman) | — | — | — | — |
| `team.edit_profile` (name, phone, photo, color) | A | A | A | Z | — | — | O (own phone/photo/address) | — |
| `team.assign_zones_services` (`[S: T11, T13]`) | A | A | A | Z | — | — | — | — |
| `team.permissions.manage` (`[S: T03]` owner/operator/admin) | A | A | A (≤ Admin) | — | — | — | — | — |
| `team.roles.manage_custom` | A | A | — | — | — | — | — | — |
| `team.deactivate` | A | A | A (≤ Admin) | Z (≤ Foreman) | — | — | — | — |
| `team.export` (`[S: T09]`) | A | A | A | — | — | — | — | A |
| `team.availability.view` | A | A | A | Z | C | — | O | — |
| `team.availability.edit` | A | A | A | Z | C (opt) | O | O (if `availability.self_edit` setting on) | — |
| `team.timeoff.request` | O | O | O | O | O | O | O | — |
| `team.timeoff.approve` | A | A | A | Z | C (opt) | — | — | — |
| `team.calendar_sync.connect` (own Google Calendar `[S: T13]`) | O | O | O | O | O | O | O | — |
| `team.vehicles.assign` (`[S: llms]` vehicle on every job) | A | A | A | Z | C | — | — | — |
| `timesheets.settings.manage` (enable in Settings → Account → Workers `[S: T03]`) | A | A | A | — | — | — | — | — |
| `timesheets.clock` (Fieldd "view timesheets" = clock in/out `[S: T03]`) | O | O | O | O | O | O | O (when granted) | — |
| `timesheets.view_own` | O | O | O | O | O | O | O | — |
| `timesheets.edit_own` (Fieldd "edit timesheets" `[S: T03]`) | O | O | O | O | O | — | — | — |
| `timesheets.edit_others` (admin add/edit punch `[S: T03]`) | A | A | A | Z | C | — | — | — |
| `timesheets.view_others` | A | A | A | Z | C | — | — | A |
| `timesheets.approve_reject` (`[S: T03]`) | A | A | A | Z | C (opt) | — | — | A |
| `timesheets.mark_paid` (`[S: T03]`) | A | A | A | — | — | — | — | A |
| `timesheets.payroll_export` | A | A | A | — | — | — | — | A |
| `compensation.view_own` | O | O | O | O | O | O | O (setting: "show my rate in app") | — |
| `compensation.view_others` | A | A | A (excl. Owner/Operator) | opt (Z) | — | — | — | A |
| `compensation.edit` (rates, effective dates `[S: T03]`) | A | A | A (≤ Admin) | — | — | — | — | — |

Hard rules that override any preset or override (cannot be granted away) `[I]`:
1. **No privilege escalation**: a member can only grant keys (and scopes) they themselves hold, and only
   to members whose role rank is strictly lower (rank: Owner 100 > Operator 90 > Admin 80 > Manager 60 >
   Foreman 40 = Bookkeeper 40 > Estimator 30 > Worker 20). Custom roles take the rank of their base preset.
2. **No self-approval**: `timesheets.approve_reject` and `timesheets.mark_paid` never apply to one's own
   entries, except when the member is the Owner of a business with no other approver (solo mode).
3. **No self-rate-edit**: `compensation.edit` never applies to one's own rates, except the Owner.
4. **Owner is immutable** except via ownership transfer: cannot be deactivated, demoted, or have
   permissions revoked.
5. Exactly one Owner per business at all times.

### 2.5 Field-level visibility

Field masks apply on **every surface** (CRM, team app, API, MCP, exports, notifications, push previews,
search results, calendar sync payloads). Masked fields are **omitted server-side**, never hidden client-
side `[I]`.

| Field / data | Visible to | Masking rule |
|---|---|---|
| Job price, line totals, invoice totals | `jobs.view` holders **and** `payments.collect` on that job | Workers see job total only when the job is theirs and POS is reachable (needed to collect `[S: T16]`). Business setting "Hide prices from workers" hides line prices but still shows "Amount due" at POS. `[I]` |
| Business revenue (dashboard, reports, worker summary `[S: T17]`) | `reports.view` / `dashboard.view` | Workers never see business-wide revenue. |
| Per-worker revenue (leaderboard `[S: T09]`, transactions worker summary `[S: T17]`) | `leaderboard.view_revenue_column` | Workers see their own revenue only if setting "Show my revenue in app" is on. |
| Service **cost** / profit margin `[S: T06, T19]` | `settings.catalog.view_cost` | Never sent to team app by default. |
| Customer phone / email | `customers.contact_info.view` | Worker: visible from (job start − 24h) to (job end + 72h), else masked (`•••-•••-1234`). In-app call/SMS via relay recommended (Agent 08). `[I]` |
| Customer address | job-linked | Worker: only addresses of assigned jobs; for unassigned "claimable" jobs show neighborhood/zip only until claimed. `[I]` |
| Customer lifetime value, notes tagged "office only" | Admin+ / Manager Z | Notes have visibility flag `office_only`. |
| Card on file | last4 + brand only, to anyone with `payments.charge_card_on_file` | PAN never stored by us (Agent 05). |
| Other members' pay rates | `compensation.view_others` | Admin cannot see Owner/Operator rates. |
| Own pay rate | `compensation.view_own` + setting | Default on. |
| Other members' timesheet hours | `timesheets.view_others` | Foreman: crew only. |
| Tips | own: `tips.view_own`; all: `tips.view_all` | |
| Team members' personal phone/address | `team.view` A/Z | Crew peers see name + photo + work phone (if set) only. |
| Clock-in GPS coordinates | `timesheets.view_others` | Retained 90 days then coarsened to 3 decimal places `[I]`. |
| Audit log | `settings.audit_log.view` | |

### 2.6 Team-app (mobile) permission toggles

The Fieldd worker profile has a **Permissions** section with a **Mobile app** group containing the
timesheet permissions `[S: T03]` and presumably others `[I]`. Our proposed toggle list (each maps to a key
above); grouping is what the profile UI shows:

| Group | Toggle (UI label) | Key(s) | Default for Worker |
|---|---|---|---|
| Access | Can use team app | `surface.team_app.access` | On |
| Jobs | See unassigned jobs / claim jobs | `calendar.view_unassigned`, `jobs.claim_unassigned` | Off |
| Jobs | Create jobs from app | `jobs.create:own` | Off |
| Jobs | Create & send quotes | `quotes.create_send:own` | Off (On for Estimator) |
| Jobs | Create & send invoices | `invoices.create_send:own` | Off |
| Jobs | See job prices | field mask flag | On |
| Customers | See customer contact info | `customers.contact_info.view:own` | On (time-boxed) |
| Customers | Message customers | `inbox.customer_chat:own` | On |
| Payments | Take payments (POS) | `payments.collect:own` | On |
| Payments | Charge card on file | `payments.charge_card_on_file:own` | On |
| Payments | Apply discounts (limit %) | `payments.discount.apply:own` | Off |
| Payments | Add add-ons / upsell | `payments.addons.add:own` | On |
| Timesheets | **View timesheets** (clock in/out) | `timesheets.clock`, `timesheets.view_own` | Off — requires company-level timesheets ON `[S: T03]` |
| Timesheets | **Edit timesheets** | `timesheets.edit_own` (+ `timesheets.edit_others:crew` for Foreman/Manager) | Off `[S: T03]` |
| Team | See team leaderboard | `leaderboard.view` | Off |
| Team | See my earnings (rate, tips, revenue) | `compensation.view_own`, `tips.view_own` | On |
| Availability | Edit my own availability | `team.availability.edit:own` | On (business setting may force approval) |

CRM group toggles: Access to CRM; then per-module (Calendar, Jobs, Customers, Leads, Transactions,
Reports, Marketing, Settings, Team) with scope pickers (Own / Crew / My zones / All). Exact Fieldd CRM
toggle names — unknown `[I]`, see Verify list.

---
## 3. Screens

General conventions for every screen in this module `[I]`:
- Desktop CRM: left nav, top bar with global search (members are searchable `[S: T08, T13]`), **New**
  menu (contains **Worker** `[S: T13]`).
- All forms: inline validation on blur, server re-validation on save, optimistic concurrency via
  `version` (409 → "This profile was changed by X at 10:42 — review changes").
- Loading = skeleton rows (no layout shift); error = inline banner with retry + support code;
  empty = illustration-free text + primary action.
- Mobile CRM (responsive web): tables collapse to cards; drawers become full-screen sheets.
- Every destructive action requires confirmation naming the consequences (e.g., "12 future jobs").

### 3.1 People tab → Team list

**Nav:** People tab (shared with customers `[S: T09]`) → sub-tab **Team**. Also reachable from
Settings → Account → Workers ("Manage team") `[I]`.

**Layout regions**
1. Header: title "Team", counters chip ("8 active · 2 invited · 3 deactivated"), seat usage ("10 of 10
   seats" with upgrade link when at cap), buttons **+ Add team member** (= New → Worker), **Export**
   (`team.export`, CSV `[S: T09]`).
2. Toolbar: search box (name, email, phone, employee ID; debounce 150 ms; client-side filter if ≤ 500
   members else server), filters: Role, Status (Active / Invited / Deactivated / Invite expired),
   Service area, Shop, Crew, Service capability, Timesheets enabled (Y/N), "Clocked in now".
3. Table columns: Avatar + calendar color swatch; Name; Role; Status pill; Service areas (chips, +n);
   Phone; Email; Today's status (Off / Scheduled n jobs / Clocked in 3h12 / On break / Time off);
   Last active; Actions (⋯: Edit, Resend invite, Copy invite link, Deactivate/Reactivate, View as).
4. Right drawer preview on row click (desktop) — summary of profile with "Open profile".

**States**
- Empty (only owner exists): "You're the only one here. Add your first team member" + primary button.
  Note: solo owners are themselves a worker for dispatch (owner membership has `isDispatchable=true` by
  default) `[I]`.
- Invited row: greyed avatar, pill "Invited · sent 2d ago", inline "Resend".
- Bounced invite: red pill "Email bounced — edit email".
- Deactivated rows hidden by default (Status filter).

**Mobile:** card list with avatar, name, role, status; tap → profile.

### 3.2 New → Worker (Add team member)

**Nav:** global **New** button → **Worker** `[S: T13]`. Fieldd's chapter list also mentions "Open the
Worker Settings" and "Tap 'Add New Worker'" `[S: T13 .description]` — i.e. a second entry point from
settings, possibly from the team app ("tap") `[I]`.

**Layout:** single scrolling form (desktop: two columns: left = identity, right = assignment), sticky
footer with **Save & send invite** (primary), **Save without inviting** (secondary, our addition `[I]`),
Cancel. Sections in order of Fieldd narration `[S: T13]`:

**A. Basic info** `[S: T13]` ("name, phone number, email, address, and even a photo")

| Field | Type | Validation | Default |
|---|---|---|---|
| First name | text | required, 1–60 chars, trimmed, no control chars | — |
| Last name | text | optional, ≤ 60 | — |
| Display name (on customer notifications) | text | ≤ 40; default "First L." | derived `[I]` |
| Email | email | required unless phone provided and invite channel = SMS `[I]`; RFC 5322 lite; lowercased; unique among **active memberships in this business** (409 "Already on your team") | — |
| Mobile phone | phone (E.164 with country picker defaulting to business country) | optional in Fieldd (unknown) `[I]`; required if SMS OTP login chosen | — |
| Address | address autocomplete (line1, line2, city, region, postal, country, lat/lng) | optional; used as **start/end location** for route & travel time (Agent 03) `[I]` | — |
| Photo | image upload (JPEG/PNG/HEIC/WebP ≤ 10 MB → server crops 1:1, 512px + 128px variants) | optional | initials avatar |
| Employee ID | text | optional, unique per business | — `[I]` |
| Timezone | select | default business timezone | business tz `[I]` |
| Language | select | for invite & app | business locale `[I]` |

**B. Role & access** `[S: T13]` "adjust their permissions for both the mobile app and the Field CRM";
chapter "Set Role and Permissions" `[S: T13 .description]`
- Role (select from presets + custom roles); helper text summarising what the role can do.
- Toggles: "Can use team app" (default on), "Can use CRM" (default from role).
- "Customize permissions" expander → the permission editor (3.4) inline.
- Validation: cannot pick role ≥ own rank; Owner not selectable (ownership transfer only).

**C. Service areas & shops** `[S: T13]` "assign them to a service area"; shops appear in worker profile
`[S: T11]`
- Multi-select of zones (from Agent 02) with map thumbnail; multi-select of shops.
- "Primary zone" radio (used as default for dispatch tie-breaks & reporting) `[I]`.
- Validation: at least one zone **or** shop if member is dispatchable and the business has > 1 zone
  (warning, not error, if single zone — auto-assign to it) `[I]`.

**D. Services** `[S: T13]` "customize the services that they can be assigned to"
- Tree of categories → services (Agent 02 catalog) with "All services" master toggle (default ON:
  new services added later are automatically allowed when "All services" is on) `[I]`.
- Add-ons are not filtered by worker (inherit from service) `[I]`.
- Route services `[S: T02]` listed like others.

**E. Calendar color** `[S: T13]`
- Swatch picker (palette of 24 accessible colors + custom hex). Auto-suggests the least-used palette
  color. Warn (not block) if same color as another active member; warn if contrast with white text
  < 4.5:1 (we auto-choose text color instead) `[I]`.

**F. Availability (optional on create)** `[I]` — "Use business hours" (default) or quick weekly grid.
Fieldd says availability is set **by the worker after login** `[S: T13]`; we allow both.

**G. Pay (optional on create)** `[I]` — hourly rate + effective date (defaults today). Visible only with
`compensation.edit` and only if timesheets are enabled or "Track pay rates" is on.

**H. Dispatch options** `[I]` — "Can be auto-assigned jobs" (default on), max jobs/day, default vehicle,
crew.

**Save behavior**
1. Validate; create `Membership(status='invited')` + `Invite` + optional `User` lookup by email/phone.
2. Send invite email (Agent 08 template `team.invite`) `[S: T13]`; optionally SMS.
3. Toast: "Invite sent to dana@… — they'll appear as active once they log in" `[S: T13]`.
4. Redirect to the new profile.

States: saving spinner on button (disabled, prevents double submit; request carries idempotency key);
error banner at top + field errors; seat-limit reached → modal "You've used 10 of 10 seats" with
upgrade / deactivate someone / save as draft (not invited, no seat) `[I]`.

### 3.3 Team member profile

**Nav:** People → Team → member, or global search `[S: T13]`. Fieldd: "view or edit a worker's
profile by opening the people tab … or using the search bar" `[S: T13]`; worker profiles show
"service history, ratings, notes, and account status" `[S: T09]`; a **Time sheets** area with
**Compensation** `[S: T03]`; a **Permissions** section with **Mobile app** subsection, reached by
scrolling `[S: T03]`.

**Header:** avatar, name, role pill, status pill, calendar color, today's status, quick actions
(Message, Call, Resend invite, ⋯ Deactivate / View as / Copy member ID). **Save** button at top
(Fieldd: "hit the save button at the top" `[S: T03]`) — we use per-section autosave-with-undo for
simple fields and explicit Save for permissions/compensation `[I]`.

**Tabs** (Fieldd may use one long page — verify `[I]`):
1. **Overview** — contact, address, employee ID, primary zone, account status, last login, app
   version, devices; ratings (avg + count) `[S: T09]`; notes (internal) `[S: T09]`.
2. **Jobs** — upcoming & past jobs (service history `[S: T09]`), filters, counts; "Reassign all
   future" action.
3. **Services & areas** — sections C, D from 3.2; crews; default vehicle.
4. **Availability** — 3.5.
5. **Permissions** — 3.4 (Mobile app / CRM groups) `[S: T03, T13]`.
6. **Time sheets** — **Compensation** card first `[S: T03]` (current rate, effective date, future rates
   list, **Edit rate** button), then this member's entries (same component as 3.9 filtered), pay-period
   totals.
7. **Performance** — jobs completed, revenue attributed (Agent 05/08), reviews, leaderboard rank
   `[S: T09]`; masked by permissions.
8. **Activity** — audit trail for this membership (role changes, permission changes, rate changes,
   punches edited by whom).
9. **Security** — sessions & devices (revoke), login methods, 2FA status, "Sign out everywhere".

### 3.4 Permissions editor

Location: profile → Permissions tab; also inline in New → Worker; also Settings → Team → Roles (3.7).

Layout: two groups — **Mobile app** and **CRM** `[S: T03, T13]`.
- Each group: master access toggle → list of capabilities grouped by module, each row: label, short
  description, toggle, optional **scope** dropdown (Own / Crew / My zones / All) and **limit** inputs
  (e.g., discount max %, refund max $).
- Rows that differ from the role preset show a dot "Customized" + "Reset to role default".
- Rows unavailable on plan: lock icon + "Available on Team plan".
- Rows dependent on business settings (e.g. timesheets disabled company-wide) show disabled with link
  "Turn on timesheets in Settings → Account → Workers" `[S: T03]` — Fieldd: "workers won't see any of
  the options at all unless they're enabled for them" `[S: T03]`.
- The two Fieldd timesheet combinations `[S: T03]`:
  - **View time sheets** only → clock in & out.
  - **View + Edit time sheets** → "enter their time, but then also edit it" (foreman / manager).
  We render these as a 3-state segmented control: *Off · Clock in/out · Clock in/out + edit* `[I]`,
  plus a separate "Approve crew timesheets" toggle for Foreman/Manager.
- Footer: **Preview as this member** (opens read-only "View as" session), **Save**.
- Validation: escalation guard (grayed rows the editor doesn't hold, tooltip "You can't grant what you
  don't have"); dependency auto-enable (granting `jobs.assign` requires `jobs.view`; granting
  `timesheets.edit_own` requires `timesheets.clock`) with toast listing auto-added keys.
- Save effects: new permission set takes effect on the member's **next request** (server checks every
  request; tokens carry only membership id + `permsVersion`) — cached permission sets invalidated
  immediately; push "Your permissions changed" silent push → app refetches `[I]`.

### 3.5 Availability editor

Location: profile → Availability; team app → Me → Availability `[S: T13]` ("set their availability").

Regions:
1. **Weekly hours**: 7 rows (Mon–Sun, week start per business setting), each with on/off and one or
   more time ranges (e.g., 08:00–12:00, 13:00–17:00); copy-to-all; "Same as business hours" toggle.
   Validation: ranges within 00:00–24:00 (end may be 24:00), no overlap, min length 15 min; ranges
   crossing midnight entered as "22:00 → 06:00 (+1 day)" stored as start + duration `[I]`.
2. **Location by day** (optional `[I]`): per weekday choose zone(s) or shop (e.g., Mon/Wed shop A,
   Tue/Thu mobile in North zone). Default: all assigned zones/shops.
3. **Capacity**: max jobs/day (int 0–50, blank = unlimited), max booked hours/day (optional), buffer
   between jobs override (default from Agent 03 setting).
4. **Date overrides**: list of specific dates with custom hours or "unavailable" (e.g., Saturday
   extra shift).
5. **Time off**: list (date range, all-day or partial, type: vacation/sick/unpaid/other, status pill:
   Requested/Approved/Denied), **Request time off** (worker) / **Add time off** (admin, auto-approved).
   Conflicts panel: "3 jobs scheduled in this period" → actions Reassign / Leave / Cancel request.
6. **Calendar sync**: "Connect Google Calendar" `[S: T13]` (+ Outlook/ICS `[I]`); shows status,
   which calendars block availability ("busy" events subtract availability), whether jobs are pushed
   to Google.
7. **Default vehicle** `[S: llms]` "the vehicle on every job" — select fleet vehicle; per-day override.

Preview strip: next 14 days computed availability (what dispatch sees) — green = available, grey = off,
striped = time off, orange = external calendar busy.

### 3.6 Settings → Account → Workers

Fieldd: "settings down at the bottom lefthand corner under account, select workers and you'll see the
time sheet setting which you can turn on … save" `[S: T03]`.

Sections (Fieldd shows only the timesheet toggle; the rest is ours `[I]`):

**A. Timesheets**
| Setting | Type | Default |
|---|---|---|
| Enable timesheets | toggle | Off `[S: T03]` |
| Pay period | Weekly / Biweekly / Semi-monthly (1–15, 16–EOM) / Monthly | Weekly `[I]` |
| Pay period anchor date | date (for weekly/biweekly) | next Monday |
| Workweek starts | weekday + time (e.g., Mon 00:00) | Mon 00:00 |
| Payroll timezone | tz | business tz |
| Workday boundary (for shifts crossing midnight) | time | 04:00 `[I]` |
| Rounding | None / nearest 1 / 5 / 6 / 15 min (applied to in & out separately) | None |
| Overtime rule | None / Weekly > 40h / Daily > 8h + Weekly > 40h / California / Custom | Weekly > 40h (US) / None (other countries) |
| OT multiplier / Double-time multiplier & thresholds | decimals | 1.5 / 2.0 / 12h daily |
| Paid breaks count as hours worked | toggle | On (US law: short breaks are paid) |
| Max paid break minutes per shift (excess becomes unpaid) | int | 30 |
| Auto-deduct unpaid meal break if shift > X h and no break recorded | toggle + minutes + threshold | Off |
| Forgotten clock-out: remind after | minutes after scheduled end / last job completed | 30 |
| Forgotten clock-out: auto clock-out at | max shift length (h) | 12 |
| Allow clock-in only when scheduled / within X min of first job | toggle + minutes | Off |
| Geofence | Off / Record location / Warn / Block | Record location (if permission granted) |
| Geofence radius | meters (50–2000) | 300 |
| Allowed clock-in locations | shops, job sites of the day, anywhere in assigned zones | shops + job sites |
| Require photo on clock-in (buddy-punch prevention) | toggle | Off |
| Workers may add manual entries (missed punches) | toggle (requires `timesheets.edit_own`) | Off |
| Auto-approve entries that match schedule ±X min and have no edits | toggle | Off |
| Lock period after "Paid" | always on (immutable) | — |
| Track time per job (job timers from job workflow) | toggle | On (labor costing) |

**B. Team defaults**: default role for new members; default permissions template; default calendar
colors; invite expiry days (7); require 2FA for CRM users (toggle); availability self-edit (Off /
On / On with approval); "Show prices to workers"; "Show my earnings in app".

**C. Compensation defaults**: default hourly rate; currency (business currency; read-only).

**D. Payroll export**: format (CSV / Gusto / QuickBooks / ADP — later), employee ID mapping.

Save: explicit Save button `[S: T03]`; turning timesheets **off** while members are clocked in →
confirm modal: "3 people are clocked in. Stop their clocks now (entries will be submitted) or keep them
running until they stop?" `[I]`.

### 3.7 Settings → Team → Roles & permissions *(our addition `[I]`)*

- List of roles (presets locked; "Duplicate" to create custom), member count per role.
- Role editor = permission editor (3.4) without member-specific overrides; "Apply to N members" with diff
  preview ("+2 permissions, −1 permission for 6 members").
- Matrix view (roles × keys) read-only export (CSV/PDF) for compliance.

### 3.8 Settings → Account → Plan & seats *(owner only)*

- Current plan, seats used / included, extra seat price, next invoice; upgrade/downgrade.
- Seat accounting explanation (5.9). Downgrade blocked if active members > new cap → wizard to
  deactivate or convert to free roles.
- Multi-business hub (Pro): "Businesses in this hub" (3.14).

### 3.9 Timesheets — admin entry screen

Fieldd: "on the entry screen, you'll see the different statuses are denoted by different icons. You
have a paid, approved, and rejected"; "admin level edits … edit a punch, or add a new one"; "go to add
down at the bottom" `[S: T03]`.

**Nav:** Team → Timesheets (or People → Timesheets) `[I]`; also worker profile → Time sheets.

**Layout**
1. Header: period picker (current pay period default; ‹ › arrows; custom range), totals strip:
   Total hours · Regular · OT · Double · Paid breaks · Unpaid breaks · Gross pay (masked by
   `compensation.view_others`) · Entries pending approval (n).
2. Filters: member, role, crew, zone/shop, status (Open/In progress, Submitted, Approved, Rejected,
   Paid, Voided), flags (Auto-closed, Edited, Outside geofence, Overlaps job gap, Missing break,
   Offline-synced).
3. Group-by toggle: by member (default; expandable rows per day) or by day.
4. Entry row: status icon (Submitted = hollow circle/clock; Approved = check; Rejected = x; Paid =
   currency icon; In progress = pulsing dot) `[S: T03]` + text label (accessibility — never icon
   only `[I]`), date, member, start–end (with "+1" for next day), breaks (paid/unpaid), total hours,
   rate, pay, flags, jobs worked (count, link), actions.
5. Bulk bar (on selection): **Approve**, **Reject** (reason required), **Mark paid**, **Export**.
6. Footer button **Add** (Fieldd: "go to add down at the bottom" `[S: T03]`) → Add entry modal (3.10).

**States:** empty ("No time recorded for this period" + Add); timesheets disabled → banner with link
to settings; loading skeleton.

**Mobile CRM:** list of cards by member with swipe actions Approve/Reject.

### 3.10 Add / edit entry modal (admin punch editor)

Fieldd add flow: select date (today), start 8, end 3, optionally add break time ("worked through …
didn't take a break"), "set the hourly rate for this punch"; then the entry shows as **entered and
submitted** with approve / reject / pay options `[S: T03]`.

Fields:
| Field | Type | Validation | Default |
|---|---|---|---|
| Member | select (members with timesheets permission) | required | current filter |
| Date | date | required; not in a Paid period (unless correction, see 5.6); not > today + 0 (future punches disallowed) `[I]` | today `[S: T03]` |
| Start time | time | required | — |
| End time | time | required; if ≤ start → treated as next day with explicit "+1 day" chip (confirm) | — |
| Breaks | repeatable rows: start, end (or duration), Paid/Unpaid | each inside shift; no overlap | none |
| Hourly rate for this punch | money | ≥ 0, ≤ 10,000.00; default = effective rate on that date `[S: T03]` | effective rate |
| Job links (optional) | multi-select of member's jobs that day | — | auto-suggest |
| Note / reason for edit | text | **required when editing** an existing entry (audit) `[I]` | — |

Live preview: total hours, paid/unpaid break minutes, OT impact for the week, pay.
Save → status `submitted` (Fieldd: "entered and submitted" `[S: T03]`); entry flagged `manual`.
Edit of an approved entry → returns to `submitted` and requires re-approval (unless editor has approve
permission and ticks "Approve after save") `[I]`. Paid entries are read-only (see 5.6).

### 3.11 Pay period / payroll export

- Period summary per member: regular, OT, DT, paid break, unpaid break, gross pay, tips (from Agent 05),
  entries count, all approved? (warning if not).
- **Export**: CSV columns (5.8), provider formats later. Option "Mark exported entries as paid".
- "Close period" → locks edits for approved entries in that period (soft lock; admins can reopen with
  reason) `[I]`.

### 3.12 Team app (worker mobile)

**3.12.1 Login & invite**
- Invite email deep link → app store if not installed → app opens with invite token prefilled
  (deferred deep link) → verify via email OTP (or phone OTP) → set name/photo if missing → land on Today.
- Sign-in options: email + OTP, phone + SMS OTP, email + password, passkey `[I]`; SSO later.
- If user has > 1 business membership: business switcher (avatar of business, unread counts) `[I]`.

**3.12.2 Today screen with clock widget** `[S: T03]`
- Clock card **at the top** when timesheets enabled and member has `timesheets.clock`
  ("they will see the clock in button at the top") `[S: T03]`.
- States & colors `[S: T03]`:
  - **Idle**: blue **Start** button ("indicated by a blue start button").
  - **Working**: timer counting up; card boundary **green**; buttons **Pause** (break) and **Stop**.
  - **On break**: tap Pause → sheet "Paid break / Unpaid break" `[S: T03]`; timer **restarts** to show
    break duration; boundary **orange**; button **Resume**.
  - Resume → back to green, work timer continues from the accumulated total `[S: T03]`.
- Below the clock: today's jobs/route (Agent 03/04).
- Accessibility: state also conveyed by text label ("Working · 3:12:04") and haptics; colors meet
  contrast; we use our own palette (not Fieldd's) — only the semantic state mapping is replicated `[I]`.

**3.12.3 Stop → Time track summary** `[S: T03]`
- "Time track summary": shift start, shift end, each break (type, start, end, duration), and **total
  hours** at the bottom; **Save** → "sent over to the CRM for approval" `[S: T03]`.
- Ours adds `[I]`: optional note; jobs worked list; warnings (no break on > 6h shift; outside geofence);
  "Edit" (only with `timesheets.edit_own`) to correct times before submit; **Discard** is not allowed
  (accidental-tap protection) — instead "This was a mistake" → submits with flag `void_requested`.
- If the worker closes the summary without saving, the entry stays `stopped` (unsaved) and a banner on
  Today says "Submit yesterday's time"; auto-submit after 24h `[I]`.

**3.12.4 My timesheets**
- List by pay period with status icons (Submitted/Approved/Rejected/Paid) `[S: T03]`, totals, earnings
  (if allowed). Rejected entries show the reason and "Fix & resubmit" (with edit permission) or "Contact
  manager".
- Foreman/manager with edit permission: **Crew** tab listing crew entries with edit/approve.

**3.12.5 Notifications** `[S: T03]` — push on approved / rejected (and paid `[I]`), deep-linking to the
entry.

**3.12.6 Me** — profile (photo, phone, address), availability (3.5 mobile), time off requests,
calendar sync `[S: T13]`, security (sessions), switch business, sign out.

### 3.13 Multi-business hub (Pro) `[S: llms]` "multi-business hub"

Exact Fieldd behavior unknown `[I]`. Proposal:
- Hub = group of businesses owned/operated by the same owner (franchise or multi-brand).
- Hub dashboard: consolidated KPIs across businesses (Agent 08 provides metrics per business), team
  directory across businesses, switcher.
- Cross-business actions: **clone profile** (`[S: llms]` "profile cloning" — interpretation: clone a
  business's settings/catalog to a new business, or clone a worker profile to another business `[I]`),
  invite a member into several businesses at once, shared roles templates.
- Data isolation: each business remains a separate tenant; hub only aggregates read models and
  orchestrates commands per tenant.

### 3.14 Auth & account screens

- Sign in (web): email or phone → method chooser (OTP code / password / passkey) → 2FA if enabled →
  business chooser (if > 1) → CRM.
- Sign up (new business): email → OTP → name, business name, city (Fieldd creates default service area
  from signup city `[S: T11]`) → industry template (Agent 02) → done; creates User + Business + Owner
  membership.
- Forgot password; change email (verify both old & new); change phone (OTP to new).
- Account settings (user-level, not business-level): name, avatar, language, login methods, 2FA,
  sessions across all businesses, delete my account (blocked while owning a business).

### 3.15 "View as" preview *(our addition `[I]`)*

Admin opens a read-only rendering of CRM/team app with the member's effective permissions and masks
(no writes allowed, banner "Viewing as Dana — read only"). Audited.

---
## 4. Workflows

Each workflow lists side effects: **W** = records written, **E** = events emitted (section 9),
**N** = notifications (catalog owned by Agent 08), **S** = status changes.

### 4.1 Sign up a new business (owner bootstrap)
1. Visitor enters email (or phone) → OTP sent → verified. W: `User(status='active')`, `AuthIdentity`.
2. Enters business name, city/address, timezone (auto from city), currency (from country), industry.
3. System creates `Business(plan='trial')`, `Membership(role='owner', status='active',
   isDispatchable=true)`, default `TimesheetSettings(enabled=false)`, default availability = business
   hours (Mon–Fri 08:00–17:00 `[I]`), asks Agent 02 to create a default service area from the signup city
   `[S: T11]` and assigns the owner to it.
4. E: `tenant.business.created`, `team.member.activated`. N: welcome email (Agent 08).
5. Idempotency: signup keyed by verified user + business name hash for 10 min (double-click safe).

### 4.2 Add a team member (New → Worker) `[S: T13]`
1. Admin opens New → Worker; fills basic info, role/permissions, zones/shops, services, color (3.2).
2. Client sends `team.createMember` with `Idempotency-Key`.
3. Server: permission check `team.create_invite` (+ scope: managers only into their zones, roles ≤
   Foreman); escalation guard on permissions; seat check (5.9).
4. Lookup `User` by normalized email/phone:
   - Exists → membership linked to that user (they will see a new business on next login).
   - Not exists → `User(status='pending')` placeholder created (no credentials).
5. W: `Membership(status='invited')`, `MemberZone[]`, `MemberShop[]`, `MemberService[]` (or
   `allServices=true`), `MemberPermissionOverride[]`, optional `CompensationRate`, `AvailabilityTemplate`
   (business hours copy), `Invite(tokenHash, expiresAt=+7d)`.
6. E: `team.member.invited`, `team.invite.sent`. N: invite email `[S: T13]` (+ SMS if phone & chosen).
7. Agent 03 receives `team.member.invited`: member **not dispatchable until active** unless business
   setting "Allow assigning invited members" is on (default ON — owners often schedule before the
   worker installs the app) `[I]`. Open question Q3.
8. UI: member appears in Team list as "Invited". Fieldd says "After they log in, you'll see them appear
   in your CRM" `[S: T13]` — we show the invited row immediately (better visibility) `[I]`.

### 4.3 Accept invite
1. Member taps the link: `https://<app-domain>/invite/<token>` (deep link to team app on mobile).
2. Server validates token (hash lookup, not expired, not revoked, membership still `invited`).
3. If the user has no credential → choose login method: email OTP (default — proves inbox ownership
   because the link went there), set password optional, add phone optional.
4. If the user already has an account under a **different** email than invited → "Sign in to accept":
   after sign in, the invite is bound to the signed-in user only if the invited email is verified on that
   account or the user verifies it now (prevents token forwarding takeover) `[I]`.
5. W: `Membership.status='active'`, `activatedAt`, `Invite.status='accepted'`, `User.status='active'`.
6. E: `team.invite.accepted`, `team.member.activated`. N: "Dana joined your team" to inviter (push/in-app).
7. App: onboarding — photo, confirm availability `[S: T13]`, enable notifications, location permission
   (only if geofence/on-my-way enabled, with rationale), connect Google Calendar (optional) `[S: T13]`.

### 4.4 Resend / revoke / expire invite
- Resend: new token, old token revoked (only latest valid). Rate limit 5/day/member. E:
  `team.invite.sent`.
- Revoke: membership → `removed` (never activated), seat freed. E: `team.invite.revoked`.
- Expiry job (hourly): invites past `expiresAt` → `expired`; membership stays `invited` with "Invite
  expired" pill; seat still reserved (5.9). E: `team.invite.expired`. N: to inviter (digest).
- Bounce (Agent 08 `notification.delivery.failed` for invite email) → `Invite.deliveryStatus='bounced'`,
  red pill in list. E: `team.invite.bounced`.
- "Copy invite link" (admins) for sending via WhatsApp etc. — token shown once; audited.

### 4.5 Edit member profile, zones, services, color
1. Edit any section; Save with `version`.
2. W: membership fields, zone/shop/service links. Audit row with diff.
3. E: `team.member.updated` (payload has changed field names), and specifically
   `team.member.zones_changed`, `team.member.services_changed` (Agent 03 re-evaluates future
   assignments).
4. Removing a zone/service that the member has **future jobs** requiring → confirm dialog listing
   affected jobs with options: keep (allowed, flagged "assignment no longer eligible"), auto-reassign
   (Agent 03), unassign. Default: keep + flag `[I]`.
5. Calendar color change → Agent 03 calendar re-renders; Google events pushed by us get colorId updated
   lazily `[I]`.

### 4.6 Change role / permissions
1. Admin edits role or overrides (3.4). Escalation guard; Owner role not assignable.
2. W: `Membership.roleId`, overrides, `permsVersion++`. Audit.
3. E: `team.member.role_changed` / `team.member.permissions_changed`.
4. Effective immediately: next API call re-evaluates. Revoked `surface.crm.access` → active CRM sessions
   for that membership get 403 `membership_surface_revoked` → client redirects to "You no longer have CRM
   access in <Business>". Sessions themselves aren't killed (user may have other businesses).
5. N: "Your role in <Business> changed to Foreman" (in-app + email) `[I]`.

### 4.7 Deactivate a member (offboarding)
Precondition: not the Owner; actor rank > target rank; actor holds `team.deactivate` in scope.

1. Actor clicks Deactivate → **impact wizard** computes (server query `team.previewDeactivation`):
   - Future jobs assigned (count, list) — from Agent 03/04.
   - Future routes (days) — Agent 03.
   - Recurring series where member is preferred/fixed worker — Agent 03.
   - Open quotes where member is preferred worker `[S: T04]` / estimator owner.
   - Open clock session (is clocked in now?), unsubmitted/submitted/approved-unpaid timesheet entries.
   - Pending time-off requests they must approve (if manager) / approvals they own.
   - Leads assigned (Agent 07), conversations assigned (Agent 08).
   - Future-dated compensation rates.
2. Actor chooses:
   - Future jobs: **Auto-reassign** (Agent 03 picks best eligible worker per job; those without a match
     go to Unassigned) / **Move all to Unassigned** / **Reassign all to <member>** (eligibility checked
     per job, ineligible ones listed).
   - Customer notification on reassignment: "Notify customers of new technician" (default off — the
     worker name change isn't usually notified) `[I]`.
   - Effective: **now** or **at end of date X** (scheduled deactivation, e.g. last day Friday).
3. On effective time (transaction + outbox):
   - If clocked in → auto clock-out at effective time, entry `submitted` with flag `auto_closed_deactivation`.
   - W: `Membership.status='deactivated'`, `deactivatedAt`, `deactivatedBy`, `reason` (Quit / Let go /
     Seasonal / Other, free text).
   - Revoke all sessions & refresh tokens **for this membership's business context**; push tokens
     unregistered for this business; if user has no other active memberships, revoke all sessions.
   - Disconnect calendar sync for this business; delete events **we created** in their external
     calendar for future jobs (best effort) `[I]`.
   - Remove from crews, team chat groups (Agent 08), dispatch pools.
   - Future-dated compensation rates: kept but inert.
   - Seat freed.
4. Emitted: `team.member.deactivated` with `{reassignmentPlan}` → Agent 03 executes reassignment and
   emits its own `dispatch.job.reassigned` per job; Agent 04 notifies affected workers (new assignee gets
   "New job assigned"); Agent 08 removes from chats & leaderboard current period (historical kept).
5. Unaffected: historical jobs, revenue attribution, reviews, ratings, timesheets (still approvable &
   payable — payroll must still pay them), tips owed (Agent 05).
6. N: to the member: "Your access to <Business> has ended" email (optional toggle, default on) `[I]`.

### 4.8 Reactivate
1. Status `deactivated` → `active` (requires seat). Restores role, overrides, zones, services, rates.
   Does **not** restore job assignments. Calendar sync must be reconnected.
2. E: `team.member.reactivated`. N: email "You've been re-added to <Business>".

### 4.9 Remove / anonymize (GDPR/CCPA erasure) `[I]`
- Only for deactivated members after a retention check: timesheets and payroll records must be retained
  (US: payroll records 3 years, time cards 2 years under FLSA recordkeeping — legal to confirm `[I]`).
- Anonymize: name → "Former team member #1234", contact cleared, photo deleted, GPS points deleted,
  keep hours/pay/job links. E: `team.member.anonymized`.

### 4.10 Transfer ownership
1. Owner selects an active member (Operator or Admin preferred) → confirm with re-auth (OTP/passkey).
2. Target receives email + in-app request; must accept within 72 h.
3. On accept: target role → Owner; previous owner → Operator (or chosen role). Billing contact moves
   with it (SaaS billing payment method requires the new owner to re-enter card) `[I]`.
4. E: `tenant.ownership.transferred`. Audit (high severity).

### 4.11 Multi-business membership & hub
- Same user invited by another business → second membership; no new account. Sign-in shows chooser;
  CRM top-left business switcher; team app switcher. Each request carries `X-Business-Id`
  (or business-scoped subdomain path) and server verifies membership.
- Timesheets are **per membership** (a person working for two businesses has two separate clocks; the
  app shows which business is clocked in and prevents clocking into two businesses simultaneously unless
  setting "allow concurrent" — default prevent with warning `[I]`).
- Hub (Pro): owner creates hub, adds businesses where they are Owner (or Operator with consent of the
  owner). Hub roles: Hub owner, Hub admin (manage businesses), Hub viewer (reporting). `[I]`

### 4.12 Sign in / sessions
- Email OTP: 6-digit code, 10 min TTL, max 5 attempts per code, max 5 codes/hour/identifier, constant-
  time compare, generic responses ("If an account exists we sent a code") — but for CRM UX we may reveal
  existence after OTP proof only.
- SMS OTP: same, with per-country fraud controls (SMS pumping protection: block premium ranges, velocity
  per IP/ASN, CAPTCHA after 3 sends).
- Password: optional; min 10 chars, breached-password check (k-anonymity API), Argon2id.
- Passkeys (WebAuthn) `[I]`: recommended for owners/admins.
- 2FA: TOTP or passkey; business setting can require for CRM users.
- SSO (later): Google / Microsoft OIDC, SAML for Pro.
- Sessions: web = httpOnly SameSite=Lax secure cookie, 12 h idle / 30 d absolute (remember device);
  mobile = short-lived access token (15 min) + rotating refresh token (90 d, reuse detection → revoke
  family). Device record per install (platform, model, app version, push token).
- New device sign-in → security email to user. E: `auth.session.created`.

### 4.13 Set availability (worker self-service or admin)
1. Worker edits weekly hours in app `[S: T13]`; if setting = "with approval", change stored as
   `pending` and current template remains until approved.
2. W: new `AvailabilityTemplate` version with `effectiveFrom` (default next day 00:00 local; changes to
   *today* are allowed by admins only) `[I]`.
3. Conflict check: future jobs falling outside the new hours → listed; worker cannot save a change that
   orphans existing jobs without manager approval (pending) `[I]`.
4. E: `team.availability.changed` `{memberId, effectiveFrom, affectedRange}` → Agent 03 invalidates
   availability cache & booking-page slots (Agent 01).

### 4.14 Time off
1. Worker requests (date range, partial-day times, type, note).
2. W: `TimeOff(status='requested')`. E: `team.timeoff.requested`. N: approvers (manager in scope/admins).
3. Approver approves/denies (reason). On approve: E `team.timeoff.approved` → Agent 03 removes capacity;
   conflict list shown to approver with "Reassign jobs" action (Agent 03 bulk reassign).
4. Admin-created time off is auto-approved.
5. Cancel by worker before start (approved → cancelled restores availability). E: `team.timeoff.cancelled`.

### 4.15 Google Calendar sync `[S: T13]` "sync with Google Calendar to stay organized both inside and
outside"
1. Member taps Connect → OAuth (scopes: calendar events read/write on selected calendars; least
   privilege `calendar.events` + `calendar.readonly` for free/busy) `[I]`.
2. Choose: (a) **Push my jobs to Google** (creates a dedicated calendar "Work – <Business>" — recommended
   to avoid polluting primary), (b) **Block my availability with busy events** from selected calendars.
3. W: `CalendarConnection`. E: `team.calendar.connected`.
4. Push: on `dispatch.job.assigned/rescheduled/unassigned/cancelled` for this member → upsert/delete
   Google event (idempotent via stored `externalEventId`, extendedProperties.private.jobId). Event body
   respects field masks (no prices; customer name + address + service; link to app).
5. Pull: Google push notifications (watch channel) or 5-min polling fallback → busy blocks stored as
   `ExternalBusyBlock` → `team.availability.changed`.
6. Failures (token revoked, 401) → status `error`, N to member + admin banner; E `team.calendar.sync_failed`.

### 4.16 Enable timesheets (company) `[S: T03]`
1. Owner/operator/admin → Settings → Account → Workers → toggle Timesheets On → Save.
2. W: `TimesheetSettings.enabled=true`, `enabledAt`. E: `timesheet.settings.changed`.
3. Nothing changes for workers until per-member permission is granted `[S: T03]` ("workers won't see any
   of the options at all unless they're enabled for them"). Our UX: after enabling, prompt "Give clock-in
   access to: [x] all workers [ ] choose…" (bulk grant) `[I]`.

### 4.17 Grant per-worker timesheet permission `[S: T03]`
Worker profile → Permissions → Mobile app → "View time sheets" (clock) and optionally "Edit time sheets"
(foreman/manager) → Save. E: `team.member.permissions_changed`. App shows clock card on next refresh
(silent push triggers refresh).

### 4.18 Clock in → break → resume → stop → submit (worker app) `[S: T03]`
1. **Start (blue)**: worker taps Start. App creates `ClockEvent{type:'clock_in', clientEventId(UUIDv7),
   deviceTime, monotonicMs, location?, photo?}` and shows green timer immediately (optimistic).
2. Server (online or upon sync): validate permission, timesheets enabled, membership active, no other
   open entry for this membership (or for this user in another business, per setting), geofence policy.
   W: `TimeEntry(status='in_progress', startAt)`, `TimeSegment(kind='work', startAt)`.
   E: `timesheet.entry.started`. Dashboard "active now" `[S: llms]` updates.
3. **Pause**: sheet "Paid or unpaid break?" `[S: T03]` → `ClockEvent{type:'break_start', breakPaid}`.
   W: close work segment, open `TimeSegment(kind='break', paid=bool)`. E: `timesheet.break.started`.
   UI orange, break timer restarts at 0 `[S: T03]`.
4. **Resume**: `ClockEvent{type:'break_end'}` → close break, open work segment. E: `timesheet.break.ended`.
   UI green, work timer shows accumulated work time `[S: T03]`.
5. **Stop**: `ClockEvent{type:'clock_out'}` (if on break, break is closed at the same instant).
   W: `TimeEntry.status='stopped'`, `endAt`, computed totals. App shows Time track summary `[S: T03]`.
6. **Save**: `timesheet.submitEntry{entryId, note?, edits?}` → status `submitted` → "sent over to the
   CRM for approval" `[S: T03]`. E: `timesheet.entry.submitted`. N: approvers (batched digest, not per
   entry — e.g. one push at 6 pm "7 timesheets to review") `[I]`.
7. If auto-approve rule matches (setting) → immediately `approved` with `approvedBy='system'`.

### 4.19 Offline clock-in
1. App has no connectivity → events written to local durable queue (SQLite) with clientEventId,
   deviceTime (wall clock), `bootId` + monotonic elapsed, last known server-time offset.
2. Timer UI runs from monotonic clock (immune to the user changing device time).
3. On reconnect: events flushed in order; server computes `trustedTime = deviceTime + offset` where
   offset = last successful sync's (serverTime − deviceTime) if same bootId, else compare monotonic deltas
   against the first online event. If |drift| > 5 min, flag entry `clock_skew`.
4. Server dedupes by clientEventId (idempotent). Events older than 7 days rejected → user told to ask a
   manager to enter manually `[I]`.
5. Entry flag `offline_synced`. Geofence evaluated with the stored location.

### 4.20 Forgotten clock-out
1. Reminder push at `max(scheduled end, last job completed) + remindAfter` (default 30 min) `[I]`:
   "Still working? Tap to clock out." with actions *Clock out now* / *Clock out at 5:12 pm (last job
   finished)* / *Still working*.
2. Second reminder at +2 h.
3. At `startAt + maxShiftHours` (default 12 h): system auto clock-out **at the max-shift boundary**
   (not at last job time — we don't guess pay downward), status `submitted`, flag `auto_closed`, never
   auto-approved; approver sees suggested end time (last job completed / last app activity) as a one-click
   correction `[I]`.
4. E: `timesheet.entry.auto_closed`. N: worker + approvers.

### 4.21 Admin adds a missing entry `[S: T03]`
1. Timesheets → **Add** (bottom) → member, date (today), start 08:00, end 15:00, breaks none, hourly rate
   for this punch → Save.
2. W: `TimeEntry(source='manual_admin', status='submitted')`, segments, `rateOverrideCents`.
3. E: `timesheet.entry.created`, `timesheet.entry.submitted`. Audit `created_by`.
4. Then Approve / Reject / Pay available `[S: T03]`.

### 4.22 Admin edits a punch `[S: T03]`
1. Open entry → Edit → change times / breaks / rate → reason required.
2. Guard: not `paid`; no overlap with member's other entries; end > start; total ≤ 24 h.
3. W: segments replaced (old version kept in `TimeEntryRevision`), totals recomputed, status: if was
   `approved` → `submitted` (re-approval) unless editor ticks "approve". Flag `edited`.
4. E: `timesheet.entry.edited`. N: worker "Your timesheet for Tue was edited by Sam" `[I]`.

### 4.23 Approve / reject / pay `[S: T03]`
- Approve (single/bulk): W status `approved`, `approvedBy/At`. E `timesheet.entry.approved`. N worker
  push `[S: T03]`.
- Reject: reason required (≤ 500 chars). W status `rejected`. E `timesheet.entry.rejected`. N worker push
  `[S: T03]`. Worker with edit permission can fix & resubmit → `submitted`; otherwise manager edits.
- Mark paid: "Once the pay period has ended and you've paid your worker … update the status to paid"
  `[S: T03]`. Only from `approved` (we block paying unapproved — Fieldd's video shows Pay as an option
  next to approve/reject on a submitted entry; ours: "Approve & mark paid" combined action) `[I]`.
  W status `paid`, `paidAt`, `payrollRunId?`. E `timesheet.entry.paid`. N worker (optional) `[I]`.
- Bulk operations are atomic per entry, not per batch (partial success reported: "18 approved, 2 skipped
  — already paid").

### 4.24 Set compensation rate / future raise `[S: T03]`
1. Profile → Time sheets → Compensation → **Edit rate** → enter rate + **effective date** (defaults to
   today) → Add → list shows current rate & effective date `[S: T03]`.
2. Future raise: Edit rate → new rate (e.g., $25/h) → effective date (e.g., Oct 20) → **Add** → list
   shows two rates: "current and active" and "effective in the future" `[S: T03]` → **Save** at top.
3. W: `CompensationRate{effectiveDate (local date), rateCents, createdBy}`. E:
   `timesheet.compensation.scheduled`.
4. No cron required to "apply" the raise: the effective rate for any hour is looked up by date (5.4);
   a daily job at 00:05 business tz emits `timesheet.compensation.activated` for rates whose date is today
   (for notifications/audit) `[I]`.
5. Source discrepancy: narrator sets **October 20** and then says "Once that time comes by, **October
   19th**, the rate will change" `[S: T03]`. Either a misspeak or a UTC/local date off-by-one in Fieldd's
   display. We store `effectiveDate` as a **date-only value in payroll timezone** (never a UTC timestamp)
   to make this impossible. Verify in video.
6. Editing/deleting a rate whose date ≤ any `paid` entry's date is blocked; ≤ any `approved` entry →
   warn "Recalculate pay for 14 approved entries?" (recalculation sets them back to `submitted`? No —
   pay recalculated in place with audit, status unchanged) `[I]`.

### 4.25 Payroll export
1. Admin selects pay period → Export → format.
2. Server builds file from **approved** (optionally + paid) entries only; warns about submitted/
   rejected/in-progress ones.
3. W: `PayrollExport{periodStart, periodEnd, entryIds, totals, fileRef, format}`. Option to mark entries
   `paid` with `payrollExportId`.
4. E: `timesheet.payroll.exported`.

### 4.26 Job time tracking linkage `[I]`
When "Track time per job" is on, Agent 04's job workflow events (`job.work.started`, `job.work.completed`)
are attached to the open TimeEntry as `JobTimeLink{jobId, startAt, endAt}`. If the worker starts a job
while **not** clocked in → prompt "Clock in now?" (one tap). Labor cost per job = Σ linked minutes ×
effective rate → sent to Agent 05/08 for profit reports (complements service cost `[S: T06, T19]`).

---
## 5. Business rules & math

### 5.1 Time representation (applies to everything below) `[I]`
- Instants (`startAt`, `endAt`, segment bounds) stored in **UTC** with millisecond precision; durations
  computed as `endAt − startAt` on instants → DST-safe (a 22:00–06:00 shift on the fall-back night is
  9 h, on spring-forward night 7 h — both correct by real elapsed time).
- Calendar concepts (workday, workweek, pay period, rate effective date, time-off dates, availability
  weekly hours) are evaluated in the **payroll timezone** (business setting; default business tz).
- Weekly availability ranges are wall-clock local times in the **member's** timezone (default business
  tz) and are materialized per date; on DST days a 01:00–03:00 range is materialized as its real instants
  (spring-forward gap → shortened; fall-back → the first occurrence wins) `[I]`.
- Durations shown as `h:mm`; decimal hours shown to 2 dp for payroll (`7:45` = `7.75`).

### 5.2 Timesheet entry totals
For an entry with segments `S`:
```
workMs        = Σ duration(s) for s.kind='work'
paidBreakMs   = Σ duration(s) for s.kind='break' and s.paid
unpaidBreakMs = Σ duration(s) for s.kind='break' and not s.paid

// cap on paid breaks (setting maxPaidBreakMin per shift); excess converts to unpaid
excessPaid    = max(0, paidBreakMs − maxPaidBreakMin*60000)
paidBreakMs  -= excessPaid ; unpaidBreakMs += excessPaid

// auto-deduct meal (setting): if unpaidBreakMs == 0 and (workMs+paidBreakMs) > thresholdMs
//   then autoDeductMs = deductMin*60000 ; record as synthetic unpaid break flagged 'auto_deducted'

payableMs     = workMs + (paidBreaksCountAsWorked ? paidBreakMs : 0) − autoDeductMs
spanMs        = endAt − startAt  (= workMs + paidBreakMs + unpaidBreakMs, invariant checked)
```
Fieldd's summary shows "total hours" calculated at the bottom `[S: T03]` — we show **Total (payable)**
plus a breakdown line "Shift 8:00 am–4:30 pm · 8h 30m · −30m unpaid break" `[I]`.

### 5.3 Rounding `[I]`
- Applied to the **entry's start and end instants only** (not to breaks), in payroll timezone, to the
  nearest `n` minutes, ties rounding **half up**; stored `rawStartAt/rawEndAt` + `startAt/endAt`.
- Neutral rounding only (nearest). No "always in employer's favor" option (legal risk under FLSA
  29 CFR 785.48 `[I]`).
- If rounding would make end ≤ start → no rounding for that entry.
- Rounding never applied to manual admin entries (admin types the exact times).

### 5.4 Effective rate lookup
```
rateFor(membershipId, localDate) =
  CompensationRate where membershipId and effectiveDate <= localDate and not deleted
  order by effectiveDate desc, createdAt desc limit 1
```
- `localDate` = the **workday** of the hour in question (5.5).
- No rate on file → rate = null → pay shows "—" and the entry raises flag `missing_rate`; approval
  allowed, export blocked until a rate exists (or rate override on the entry) `[I]`.
- Per-entry override (`rateOverrideCents`) wins (Fieldd "set the hourly rate for this punch" `[S: T03]`).
- An entry whose workday is before a new rate's date keeps the old rate even if approved after.
- If a single entry spans two workdays with different rates (only possible with workday boundary
  splitting disabled) → whole entry uses the rate of its **start workday** `[I]`.

### 5.5 Shifts crossing midnight `[I]`
- An entry belongs to the **workday** in which it **starts**, where workday boundary = setting (default
  04:00): a shift starting Tue 22:00 belongs to Tuesday; a shift starting Wed 02:00 belongs to Tuesday
  (before 04:00 boundary).
- **Daily** OT thresholds use the workday attribution (whole entry counted to its workday).
- **Weekly** OT uses actual hours falling in each workweek: an entry crossing the workweek boundary is
  **split at the boundary instant** for weekly accounting (FLSA workweek is a fixed 168-h period).
- Max entry span 24 h (hard), soft warning > 14 h.

### 5.6 Overtime engine `[I]` (Fieldd sources do not mention overtime)
Rule sets (business setting; jurisdiction presets; all thresholds editable):

| Preset | Daily OT | Daily DT | Weekly OT | 7th-day rule |
|---|---|---|---|---|
| None | — | — | — | — |
| US federal (FLSA) | — | — | > 40 h → 1.5× | — |
| Daily+weekly | > 8 h → 1.5× | — | > 40 h → 1.5× | — |
| California | > 8 h → 1.5× | > 12 h → 2× | > 40 h → 1.5× | 7th consecutive workday in workweek: first 8 h 1.5×, > 8 h 2× |
| Custom | any | any | any | toggle |

Per-member flag `otExempt` (salaried/contractors) → all hours regular.

Allocation algorithm in 10.4. Key rules:
- Hours already counted as daily OT/DT are **not** counted again toward weekly OT (no pyramiding).
- Paid breaks count as hours worked when `paidBreaksCountAsWorked` (default on).
- OT premium uses the **rate in effect for those hours**; the FLSA "regular rate" blended across
  multiple rates/bonuses is **out of scope for v1** — flagged in export (column `blendedRateRequired`
  when a member had > 1 rate in the workweek) `[I]`.
- OT is computed over **approved + submitted** entries for preview, and frozen at approval: an entry's
  stored OT split may change when an earlier entry in the same week is edited → all later entries in that
  workweek are recomputed (only if not `paid`). Paid weeks are immutable; corrections go into a new
  `adjustment` entry in the current period.

### 5.7 Pay calculation (integer cents)
```
for each pay bucket b in {regular, ot, dt} of an entry:
    minutes_b   = floor(ms_b / 60000)    // whole minutes; seconds truncated per bucket
    multiplier  = {1, otMult, dtMult}[b]  // decimals stored as integer basis points (15000 = 1.5×)
    centsExact  = minutes_b * rateCents * multiplierBp / (60 * 10000)
    pay_b       = roundHalfUp(centsExact)      // round once per bucket per entry
entryPay = Σ pay_b
periodPay = Σ entryPay (no re-rounding)
```
- Rounding once per bucket per entry keeps line items reconcilable on exports; we document it.
- Seconds: total seconds per bucket are truncated to whole minutes **after** summing segments (not per
  segment) to avoid losing up to a minute per break.
- Currency = business currency; rates stored as cents/hour (`rateCents`, integer). Max $10,000/h.
- Tips are **not** part of timesheet pay; they're added in payroll export from Agent 05 ledger.

### 5.8 Payroll export columns (CSV v1) `[I]`
`period_start, period_end, employee_id, member_id, name, regular_hours, ot_hours, dt_hours,
paid_break_hours, unpaid_break_hours, rate_regular, gross_regular, gross_ot, gross_dt, gross_total,
tips_card, tips_cash_reported, entries_count, unapproved_entries_count, flags, blended_rate_required,
currency`. Hours 2 dp; money as decimal string from cents (`1234` → `12.34`), never floats.

### 5.9 Plan entitlements & seats

**Fieldd (authoritative, `[S: llms]`):**
| Plan | Price | Included users | Relevant to this module |
|---|---|---|---|
| Start | $199/mo | 10 users | timesheets, team app, calendar sync, on-my-way tracking |
| Scale | $299/mo | 30 users | everything in Start (+ marketing items) |
| Pro | $999/mo | 30+ users | **branded team app**, **profile cloning**, **multi-business hub**, custom analytics |
Unknown: what counts as a "user" (assumed every active team member incl. workers `[I]`), extra-seat
price, behavior at cap.

**Our proposal** (cheaper starter tier; final pricing is an owner decision `[I]`):

| Capability | Solo ($0–19) | Starter (~$49) | Team (~$129) | Pro (~$299) |
|---|---|---|---|---|
| Included seats | 1 (owner) | 3 | 10 | 30 (+ $8/extra) |
| Extra seats | — | $10/seat | $8/seat | $6/seat |
| Role presets (all 8) | owner only | ✓ | ✓ | ✓ |
| Per-member permission overrides | — | ✓ | ✓ | ✓ |
| Custom roles | — | — | ✓ | ✓ |
| Zone/crew-scoped managers | — | — | ✓ | ✓ |
| Timesheets (clock, breaks, approve, pay, rates, future raises) | ✓ (self) | ✓ | ✓ | ✓ |
| Rounding, auto clock-out, offline | ✓ | ✓ | ✓ | ✓ |
| Overtime engine (presets) | — | Weekly only | All | All |
| Geofence + clock-in photo | — | — | ✓ | ✓ |
| Payroll CSV export | ✓ | ✓ | ✓ | ✓ |
| Payroll provider integrations | — | — | ✓ | ✓ |
| Job-level labor costing | — | — | ✓ | ✓ |
| Google Calendar sync | ✓ | ✓ | ✓ | ✓ |
| Time-off approvals | — | ✓ | ✓ | ✓ |
| 2FA / passkeys | ✓ | ✓ | ✓ | ✓ |
| Require 2FA policy, SSO (Google/Microsoft) | — | — | ✓ | ✓ |
| SAML SSO | — | — | — | ✓ |
| Audit log retention | 30 d | 90 d | 1 y | 3 y |
| Multi-business hub, profile cloning | — | — | — | ✓ |
| Branded team app | — | — | — | add-on |
| API/MCP access | read-only | ✓ | ✓ | ✓ |

**Seat accounting** `[I]`:
- Counts: memberships with status `active` or `invited` (pending invites reserve a seat so the cap
  cannot be exceeded when they accept). Bookkeeper role and deactivated members do not count.
- At cap: create is blocked with upgrade prompt **unless** plan allows overage billing (Team/Pro:
  auto-add seat, prorated, owner notified). Accepting an invite never fails due to seats (seat already
  reserved).
- Downgrade: if seats used > new included and overage not allowed → wizard to deactivate; downgrade
  effective at period end; until then all remain active.
- Entitlement removal (downgrade) never deletes data: e.g., custom roles become read-only, members keep
  the custom role's effective keys frozen until changed; geofence settings retained but inactive.

### 5.10 Permission evaluation rules
- Default deny. Every command declares `requiredKey` + scope resolver (how to find the resource's
  zone/crew/owner).
- Scope resolution for jobs: `own` = job.assignees ∋ member OR job.createdBy = member (for estimators:
  quotes they own); `crew` = assignees ∩ crew members ≠ ∅; `zone` = job.zoneId ∈ member.zones OR
  job.shopId ∈ member.shops. Unassigned jobs in zone count for `zone`.
- List queries apply scope as a filter predicate (never post-filter in memory beyond 1 page).
- Field masks applied by a serializer layer keyed by `(resourceType, effectivePermissions)`.
- Permission checks also run on **events delivered to clients** (websocket / push payloads).
- Deactivated / invited (not accepted) memberships have zero permissions.
- Business `status='suspended'` (non-payment) → read-only for all; team app keeps clock-in/out and job
  workflow working for 14 days grace (don't strand workers mid-job) `[I]`.

### 5.11 Availability rules (contract with Agent 03)
A member is **available** at instant *t* for a job with services *X* at location *L* iff `[I]`:
1. Membership `active` (or `invited` and setting allows), `isDispatchable=true`.
2. `allServices` OR X ⊆ member.services `[S: T13]`.
3. L within one of member's zones (mobile jobs) or job.shop ∈ member.shops (shop jobs) `[S: T11, T13]`,
   honoring per-weekday location rules (3.5). (Agent 02 resolves L → zone.)
4. *t* within materialized weekly hours or a positive date override, and not within: approved time off,
   negative date override, external calendar busy block, existing job (+ buffers/travel — Agent 03).
5. Jobs that day < maxJobsPerDay (cancelled jobs don't count; routes count each stop as a job unless
   setting "route counts as 1") `[I]`.
6. Business hours do not constrain members (member hours do), except for booking-page offering
   (Agent 01/03 intersect with business booking hours).
Pending time-off requests do **not** block availability, but the dispatcher sees a warning `[I]`.

### 5.12 Calendar color rules
- Unique among active members preferred; collisions allowed with warning.
- Stored as hex `#RRGGBB`; text color auto (black/white by WCAG contrast); dark-mode variant computed
  (`[S: llms]` dark mode exists) by lightness adjustment `[I]`.

### 5.13 Validation summary (members)
- Email unique per business among non-removed memberships; phone unique per business (warning only, as
  couples may share) `[I]`.
- Name: strip zero-width & control chars; max lengths; emoji allowed in display name? → no (SMS
  encoding cost) `[I]`.
- Photo: strip EXIF (GPS privacy).
- Max memberships per business: plan cap; hard technical cap 5,000.

---

## 6. State machines

### 6.1 Membership

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `createMember` | perms, seat available | `invited` | Invite sent; E `team.member.invited` |
| — | `createMember(sendInvite=false)` | perms | `draft` | no seat used `[I]`; not dispatchable |
| `draft` | `sendInvite` | seat | `invited` | E `team.invite.sent` |
| `invited` | `acceptInvite` | token valid | `active` | E `team.member.activated` |
| `invited` | `revokeInvite` | perms | `removed` | seat freed; E `team.invite.revoked` |
| `active` | `deactivate(now)` | not owner; rank | `deactivated` | 4.7 effects; E `team.member.deactivated` |
| `active` | `deactivate(at=X)` | same | `active` (+`scheduledDeactivationAt`) | system job at X → `deactivated` |
| `active` | `businessSuspended` | — | `active` (business read-only) | — |
| `deactivated` | `reactivate` | seat | `active` | E `team.member.reactivated` |
| `deactivated` | `anonymize` | retention ok | `anonymized` | PII purged |
| any | `transferOwnership` | — | (role change only) | E `tenant.ownership.transferred` |

### 6.2 Invite

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `pending` | email delivered | — | `pending` (deliveryStatus=`delivered`) | — |
| `pending` | bounce | — | `pending` (deliveryStatus=`bounced`) | E `team.invite.bounced` |
| `pending` | accept | not expired | `accepted` | membership active |
| `pending` | resend | rate limit | `superseded` (old) + new `pending` | E `team.invite.sent` |
| `pending` | revoke | perms | `revoked` | — |
| `pending` | expiry job | now > expiresAt | `expired` | E `team.invite.expired` |
| `expired` | resend | — | new `pending` | — |

### 6.3 Clock (worker app UI state, per membership) `[S: T03]`

| State | Color | Event | Next | Writes |
|---|---|---|---|---|
| `idle` | blue (Start) | tap Start | `working` | `clock_in` event, TimeEntry `in_progress` |
| `working` | green | tap Pause + choose paid/unpaid | `on_break` | `break_start` |
| `working` | green | tap Stop | `summary` | `clock_out` |
| `on_break` | orange | tap Resume | `working` | `break_end` |
| `on_break` | orange | tap Stop | `summary` | `break_end` + `clock_out` (same instant) |
| `summary` | — | Save | `idle` | `submit` → entry `submitted` |
| `summary` | — | Edit (perm) | `summary` | revised segments |
| `summary` | — | dismiss/app killed | `idle` (pending-submit banner) | entry `stopped` |
| `working`/`on_break` | — | auto clock-out (max shift) | `idle` | entry `submitted` + `auto_closed` |
| `working`/`on_break` | — | permission revoked / deactivated | `idle` | auto clock-out at that instant, flag |

### 6.4 Time entry (server)

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | clock_in | timesheets enabled, perm, no open entry | `in_progress` | E `timesheet.entry.started` |
| — | admin add | `timesheets.edit_others` | `submitted` `[S: T03]` | E `timesheet.entry.created`, `.submitted` |
| — | worker manual add | `timesheets.edit_own` + setting | `submitted` | flag `manual_worker` |
| `in_progress` | clock_out | — | `stopped` | totals computed |
| `in_progress` | auto clock-out | max shift reached | `submitted` | flag `auto_closed`; E `.auto_closed` |
| `stopped` | submit | owner of entry | `submitted` | E `.submitted` |
| `stopped` | 24 h elapsed | — | `submitted` | flag `auto_submitted` |
| `submitted` | approve | approver ≠ self | `approved` | E `.approved`, N worker `[S: T03]` |
| `submitted` | auto-approve rule | rule match, no flags | `approved` | approvedBy=`system` |
| `submitted` | reject(reason) | approver ≠ self | `rejected` | E `.rejected`, N worker `[S: T03]` |
| `submitted` | edit | edit perm | `submitted` | revision; E `.edited` |
| `rejected` | edit + resubmit | edit perm | `submitted` | E `.edited`, `.submitted` |
| `rejected` | void | admin | `voided` | excluded from totals |
| `approved` | edit | edit perm | `submitted` (or `approved` if editor approves) | E `.edited` |
| `approved` | mark paid | `timesheets.mark_paid` | `paid` `[S: T03]` | E `.paid` |
| `approved` | unapprove | approver | `submitted` | E `.unapproved` |
| `paid` | any edit | — | (blocked) | create `adjustment` entry instead |
| `paid` | unmark paid | Owner only, reason | `approved` | audit high severity `[I]` |
| `submitted`/`approved` | void | admin, reason | `voided` | E `.voided` |

### 6.5 Time-off request

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | request | own | `requested` | N approvers; E `team.timeoff.requested` |
| — | admin add | perm | `approved` | E `team.timeoff.approved` |
| `requested` | approve | approver ≠ self | `approved` | Agent 03 recompute; N worker |
| `requested` | deny(reason) | approver | `denied` | N worker |
| `requested`/`approved` | cancel | own (before start) or admin | `cancelled` | availability restored |
| `approved` | period ends | — | `approved` (historical) | — |

### 6.6 Compensation rate

| State | Event | Next | Notes |
|---|---|---|---|
| — | add (date > today) | `scheduled` | shown as "effective in the future" `[S: T03]` |
| — | add (date ≤ today) | `active` (previous active → `superseded`) | |
| `scheduled` | date reached (derived) | `active` | E `timesheet.compensation.activated` |
| `scheduled` | delete | `deleted` | allowed freely |
| `active` | newer rate active | `superseded` | |
| `active`/`superseded` | delete | `deleted` | blocked if any `paid` entry used it |
State is **derived** from `effectiveDate` vs today — not stored — to avoid cron drift `[I]`.

### 6.7 User account

| State | Event | Next |
|---|---|---|
| `pending` (placeholder from invite) | first verified login | `active` |
| `active` | 10 failed logins / 15 min | `locked` (30 min) |
| `active` | user deletes account (no owned business) | `deleted` (memberships anonymized) |
| `active` | platform abuse action | `disabled` |

### 6.8 Calendar connection

`connecting → active → (error | revoked_by_user | disconnected)`; `error → active` on successful
re-auth; watch channels renewed before expiry (Google channels expire) `[I]`.

---
