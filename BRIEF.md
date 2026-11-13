# Team Brief — Regen Auto CRM

## Goal
Produce an extremely detailed breakdown of the fieldd.co field-service CRM (how every section
looks, works, and deploys onto customers' live websites), then a build guideline, then the
first foundation pieces of our own platform — one that matches Fieldd's feature coverage and
beats it on speed, reliability and polish. **We are NOT building the full CRM yet.**

## Ground rules (every agent)
1. **Original product, not a clone.** Replicate workflows, feature coverage and information
   architecture. Do NOT copy Fieldd's name, logo, colors, marketing copy, illustrations or
   pixel-identical UI. Working product name: "the platform" (to be named later).
2. **Cite or flag every claim.** Tag facts `[S: <file>]` when sourced from research files, and
   `[I]` when inferred (industry convention / reasonable guess). Never present an inference as
   fact. Visual layout is mostly `[I]` until the owner's walkthrough video arrives.
3. **Video-verification hooks.** The owner will record a detailed screen video of the live
   Fieldd CRM. Every spec ends with a "Verify against video" checklist: concrete things to
   confirm (layouts, field names, button positions, states, empty states, flows).
4. **Better-than-Fieldd.** For each module, list concrete improvements (speed, UX, fewer
   clicks, offline, accessibility, reliability) — practical, not fluff.
5. **Quality bar.** Zero-crash mindset: enumerate edge cases, failure modes, validation rules,
   concurrency/race conditions, timezone/DST, money rounding, idempotency, permissions.
6. **Performance bar.** Booking widget is on customers' live sites: budget-conscious
   (tiny loader, no layout shift, fast TTI on mid-range mobile over 4G).

## Research sources (read-only)
- `research/transcripts/NN_<id>.txt` — full narration of the 20 FielddEDU tutorial videos.
- `research/transcripts/NN_<id>.description` — YouTube descriptions for each video.
- `research/site/llms.txt` — Fieldd's official current facts (pricing tiers, 2026 features,
  product areas, AI/MCP). Authoritative over third-party review sites.
- Video index:
  01 Subscriptions · 02 Route Optimization · 03 Timesheets · 04 Quotes & Invoices ·
  05 Online Booking Page · 06 Services & Prices · 07 Add-Ons · 08 Jobs & Appointments ·
  09 CRM Tips (transactions/people/discounts/gift cards/reporting/leaderboard) ·
  10 Marketing · 11 Service Areas · 12 Drip Campaigns · 13 Add Team Member ·
  14 Recurring Job · 15 Book a Job · 16 Payment Methods · 17 Transactions ·
  18 Add Customer · 19 Schedule a Job · 20 Add Lead
- fieldd.co product pages are behind a Cloudflare bot challenge — do NOT try to bypass it.
  Web search (third-party reviews: Capterra, GetApp, G2, SourceForge) is allowed for
  supplementary detail; tag those `[S: web <url>]` and treat as lower-confidence.

## Owner's context & goals (2026-11-12)
- Owner likes how Fieldd works and wants an **affordable** alternative — accessible to
  people just starting a service business, and for the owner's own use.
- Key reference: **swash.co** (a Fieldd customer). Its online scheduling runs as a
  **subdomain of the business's own website** — this subdomain-hosted booking experience is
  a must-have.
- The owner is recording a personal walkthrough video of swash.co's website and online
  scheduling flow. That video is the **visual source of truth** for the customer-facing
  side and the subdomain setup. The backend should behave like the Fieldd tutorials show.

## Target stack — NOT DECIDED
Owner does not want the hobby-tier defaults (Vercel + Supabase). They want more in-depth,
production-grade infrastructure for stability. Stack is to be chosen with the owner before
Stage 2. Do not assume a stack in specs; keep specs infrastructure-agnostic.

## Status
Phase 1 (now): build the "CRM brain" from the 20 transcripts + llms.txt — a module-by-module
breakdown plus a master map of how every part talks to every other part. Written so the
owner's upcoming walkthrough video can be layered on top (Verify-against-video checklists).
Phase 2 (after video): reconcile with the video, then build guideline, then code.

## Team (Phase 1) — ten agents
| # | Agent | Owns | Main videos |
|---|---|---|---|
| 01 | Booking Portal & Subdomain | customer journey on the booking site, subdomain/custom-domain hosting, website buttons, embed, QR, short links, customer accounts | 05 (+01,02,06,07,11 customer side) |
| 02 | Catalog & Coverage | services, categories, pricing, durations, add-ons (customer + company), inventory, service areas/zones, shops, travel fees, zone tax | 06, 07, 11 |
| 03 | Scheduling & Dispatch Engine | availability math, calendar/jobs tab, booking from CRM, auto-dispatch, recurring engine, route optimization, travel time | 02, 08, 14, 15, 19 |
| 04 | Job Lifecycle & Field Workflow | single job ID across quote→appointment→invoice, statuses, worker-app job flow (on-my-way, checklists, photos, complete), quotes & invoices documents | 04 (+02,06,16) |
| 05 | Payments & Money Movement | payment options, timing, card on file, deposits/pre-auth, POS, tips, tax order, refunds, transactions, payouts, disputes, processor integration | 16, 17 (+04,09) |
| 06 | Subscriptions, Discounts & Gift Cards | subscription packages, billing cycles, commitments, auto-scheduling, dunning; discount codes/promos; gift card ledger | 01, 09 |
| 07 | Customers, Leads & Assets | customer profiles, contacts/addresses, tags, CSV import/export, dedupe, assets/vehicles, leads, lead board, abandoned-booking capture, enrichment | 18, 20 (+09,10) |
| 08 | Marketing, Comms & Reporting | notification catalog (all SMS/email/push triggers), campaigns, drip engine, inbox/chat, reviews, referrals, dashboard tiles, reports, leaderboard, metrics formulas | 10, 12 (+09) |
| 09 | Team, Permissions & Timesheets | workers, invites, RBAC matrix, service/zone assignment, availability, calendar sync, timesheets, pay rates | 03, 13 |
| 10 | Systems Integrator — "the brain" | runs AFTER 01–09: unified entity graph, event catalog, end-to-end flows, cross-module contracts, conflicts, master index | all |

| 11 | Branding, Theming & Responsive Design System | tenant brand customization on every surface, light/dark themes, design tokens, fonts/logos/icons upload, responsive desktop/tablet/phone, native-app surfaces | all + owner requirements below |

## Owner requirements added 2026-11-13 (apply to every agent)
- **Deep tenant branding.** Every business must be able to make the booking site, customer
  portal/app, emails, SMS links, quotes/invoices — and its own CRM workspace — match its brand:
  - Default light and dark themes, **plus fully editable color palettes for both modes**
    (not just one accent color); end-customer and staff can switch light/dark.
  - Adjustable **shadows** (elevation), corner radius, spacing density.
  - **Custom fonts** uploaded by the business (headings/body), with sizes/weights.
  - **Custom icons** (choose icon set or upload SVGs for categories/services/nav).
  - **Logo uploads with sizing controls** per placement (header, mobile header, emails,
    invoices, favicon/app icon), light- and dark-mode logo variants.
  - Text/copy overrides for every customer-facing label.
- **Responsive:** must work properly on desktop, tablet and phone.
- **Native apps (later phase):** a real desktop app and iPhone app so staff can adjust a
  client's package on site, or take a phone call and build a service/booking for the client live.
- **API keys & integrations register:** list every third-party service, the keys/secrets it
  needs, where they're stored, who owns them (platform vs. each business), and rotation.
- **Later phases (do not decide now):** security review, timeout/slow-load risk review, then
  choosing production-grade infrastructure "leagues better and safer than Supabase".
- **Video:** the owner's walkthrough (customer side of a Fieldd-powered business site) is at
  https://youtu.be/vyELvdWCdnc but is not yet readable from this environment; it will be
  analyzed once the owner uploads the file. Keep "Verify against video" checklists.

Notification ownership: Agent 08 owns the master notification catalog; other agents list the
triggers their module fires and reference 08.

## Output layout
- `docs/brain/NN-<name>.md` — one file per agent (01–09)
- `docs/brain/00-CRM-BRAIN.md` — Agent 10's master synthesis
- `docs/reviews/` — stage reviews
- Agents never run git; the lead commits.

## Module file template (agents 01–09)
1. **Summary** — what the module is for, in plain language (5–10 lines)
2. **Roles** — who touches it (owner/admin/manager/foreman/worker/customer/system)
3. **Screens** — per screen: where it lives in nav, layout regions, every field (type,
   validation, default), actions, states (empty/loading/error/success), mobile behavior
4. **Workflows** — numbered steps with side effects (records written, notifications, status changes)
5. **Business rules & math** — formulas, order of operations, rounding (money = integer cents)
6. **State machines** — tables: state, event, guard, next state, side effects
7. **Data model** — entities with fields, types, keys, relations (stack-agnostic, TypeScript-style interfaces)
8. **API surface** — endpoints/commands/queries this module exposes (name, input, output, auth)
9. **Interfaces with other modules** — CONSUMES (data/events from module X), PRODUCES (events/data for X),
   with event names in `domain.entity.verb` form (e.g. `booking.appointment.created`)
10. **Algorithms / pseudo-code** — for any non-trivial logic
11. **Edge cases & failure modes** — exhaustive
12. **Better than Fieldd** — concrete improvements, plus "budget tier" notes (what a starter
    business needs vs. what can be gated to higher plans)
13. **Open questions**
14. **Verify against video** — concrete checklist for the owner's walkthrough video


