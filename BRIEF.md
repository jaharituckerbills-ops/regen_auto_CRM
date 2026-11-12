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

## Status: PAUSED — waiting for the owner's walkthrough video.

## Output layout
- `docs/specs/` — module specs (Stage 1)
- `docs/reviews/` — stage reviews
- `docs/guideline/` — build guideline (Stage 2)
- `app/` — foundation code (Stage 3)
