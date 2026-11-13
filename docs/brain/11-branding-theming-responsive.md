# 11 — Branding, Theming & Responsive Design System

> Agent 11 · Phase 1 "CRM brain" · Status: draft v1 (pre-video) · Date: 2026-11-13
> Scope: how every business makes **every surface** look like its own brand — public booking site
> on its subdomain, customer account/portal, branded customer app, emails, SMS landing pages,
> quotes/invoices/receipts (HTML + PDF), on-my-way tracking page, embed widget/popup, QR materials,
> and the business's **own CRM workspace and team app** — plus the platform-wide responsive and
> accessibility system (desktop / tablet / phone, native iPhone + desktop apps).
> Infrastructure-agnostic per BRIEF: no vendor, framework or cloud is assumed. Where a mechanism has
> several common implementations, options are listed and the decision is deferred to Stage 2.

## 0. Conventions used in this file

**Source tags**

| Tag | Meaning |
|---|---|
| `[S: T01]` … `[S: T20]` | `research/transcripts/NN_<id>.txt` for video NN (e.g. `T05` = `research/transcripts/05_0qNwovgNTqM.txt`) |
| `[S: T05d]` | the matching `.description` file (chapter list) |
| `[S: llms]` | `research/site/llms.txt` (Fieldd's own facts, updated 2026-10-23; authoritative) |
| `[S: BRIEF]` | `BRIEF.md`, incl. "Owner requirements added 2026-11-13" |
| `[S: 01]` … `[S: 09]` | sibling specs in `docs/brain/` (cross-reference, not a Fieldd fact) |
| `[S: web <url>]` | third-party web source (lower confidence) |
| `[I]` | inference: industry convention, standards knowledge or reasoned guess — **not** a confirmed Fieldd fact |
| `[D]` | design decision for *our* platform (not a claim about Fieldd at all) |

**What Fieldd demonstrably offers (the entire factual baseline — everything else in this file is ours)**

| # | Fact | Source |
|---|---|---|
| F1 | Settings → Online scheduling → **Style**: "customize the online portal to look and feel like your company. You can adjust **colors, fonts, the map, and even the text** used to describe elements on the page." | [S: T05] |
| F2 | "If you want to keep it simple, you can customize the elements you care about, and the rest are all ready to go." (i.e., sensible defaults; partial customization) | [S: T05] |
| F3 | Text overrides exist at a granular key level: "settings, **customer hub, style, text**, then select **routes text**" | [S: T02] |
| F4 | "Any updates you make in any of these sections will be reflected immediately on your customer portal" | [S: T05] |
| F5 | Quote/invoice look is configured at "settings, find communication, and click email. There you'll have access to **how your quotes and invoices look**" | [S: T04] |
| F6 | Email editor: "Field will automatically use my **brand logo and colors** to create a custom email" | [S: T12] |
| F7 | "**Dark mode**: switch between light and dark mode from the side menu." (Oct 2026 update — CRM side) | [S: llms] |
| F8 | Branded customer app (iOS and Android) on Scale; branded team app on Pro; "no fieldd branding" on Pro | [S: llms] |
| F9 | "AI setup: fieldd brings over a business's services, pricing, and **branding from its website**" | [S: llms] |
| F10 | "Emails: nearly 200 emails rebuilt to be modern, clean, and easy to read." | [S: llms] |
| F11 | Workers get a unique **calendar color** | [S: T13] |
| F12 | Separate permissions for "the mobile app and the Field CRM" | [S: T13] |
| F13 | "new QR code booking options"; QR for print materials | [S: llms][S: T05] |

Nothing in the research shows: dark mode for the *public* portal, separately editable dark palettes,
font uploads, logo variants per placement, icon customization, shadow/radius/density controls, theme
versioning, custom CSS, or copy localization. These are all **ours** `[D]` and form most of this spec.
Fieldd's exact Style screen layout is unknown — see §14.

**Terminology**

- **Business / tenant** — a service company using the platform.
- **Brand Kit** — the business's complete brand definition: theme tokens (both modes), fonts, logos,
  icons, copy overrides, optional custom CSS. One per business (multi-brand later, §13).
- **Brand release** — an immutable, numbered, published snapshot of the Brand Kit. Everything that
  renders for customers reads a release, never the draft.
- **Draft** — the single mutable working copy being edited; autosaved; visible only in previews.
- **Token** — a named design value (`color.action.primary.bg`, `radius.md`, `shadow.3`). Three layers:
  **brand** (few inputs the business picks) → **semantic** (meaning, per mode) → **component**.
- **Mode** — `light` or `dark`. **Mode preference** — `light | dark | system` chosen by a viewer.
- **Surface** — a rendering target with its own constraints (§3.0 table): `booking`, `account`,
  `customer_app`, `email`, `sms_page`, `document`, `tracking`, `embed`, `qr`, `crm`, `team_app`.
- **Placement** — a specific slot a logo/icon appears in (desktop header, email header, favicon …).
- **Compiled artifact** — derived output of a release for one surface/platform (CSS file, email CSS
  block, iOS/Android token bundle, PDF stylesheet, favicon set …), content-hashed and cache-immutable.
- **Copy key** — stable identifier for a customer-facing string, e.g. `booking.datetime.title`.
- Breakpoint names: `xs` `sm` `md` `lg` `xl` `2xl` (§5.10). "Phone" = `xs`/`sm`, "tablet" = `md`/`lg`
  (portrait/landscape), "desktop" = `lg`+ with fine pointer.

**Ownership boundaries (who owns what — so this file does not redefine other modules)**

| Concern | Owner | This module's role |
|---|---|---|
| Portal config (flow settings, domains/TLS, embeds, QR codes, short links, booking questions, terms), portal page structure, tracking-page behaviour | 01 | 11 supplies the **theme, logos, fonts, icons and copy-override engine**; 01's `PortalStyle` (`[S: 01]` §7) is **replaced by a reference** to the Brand release (`brandReleaseId`) + 01-only map settings. 01 keeps ownership of the *portal copy-key catalog* (which strings exist); 11 owns storage, validation, locale fallback and the editor. **Conflict flagged for Agent 10** (§9.4). |
| Service/category names, images, display order; category/service **icon field** | 02 | 11 owns icon *assets & sets*; 02 stores `iconRef` on category/service (or 11 stores the mapping — §9.4 decision) |
| Worker calendar colors | 09 (`[S: 09]` §5.12) | 11 supplies the contrast/dark-variant function and categorical palette tokens |
| Quote/invoice/receipt **layout options** (columns, show tax ID, footer text) and immutable document snapshots | 04 (`[S: 04]` §3.8) | 11 supplies document stylesheet, logo-for-documents, fonts, PDF render constraints; 04 snapshots `brandReleaseId` per rendered document |
| Email/SMS templates, sending, brand-aware email editor, notification catalog | 08 (`[S: 08]` §3.9 "Brand Kit") | 11 **is** the "Brand Kit" 08 references: provides compiled email theme, email-safe font stacks, raster logos, dark-mode email rules |
| RBAC engine and matrix | 09 | 11 registers new permission keys (§2.2) |
| Payment element appearance (processor-hosted card fields) | 05 | 11 provides an `appearance` token subset mapped to the processor's appearance API (§10.12) |
| Native app shells, stores, push | this file defines token/asset delivery; app distribution and push credentials are shared with 08/09; final app architecture is Stage 2 |
| **Theme engine, token schema, palettes (light+dark), contrast checking, fonts, logos & image pipeline, icon sets & uploads, copy-override engine & locales, custom CSS, theme delivery/caching, responsive & accessibility system, appearance preferences, branding settings screens** | **11** | Owner |

---

## 1. Summary

Every business on the platform should be able to make everything its customers and staff see feel
like *its* brand — not "a SaaS page with your logo on it". Fieldd lets a business change colors,
fonts, the map and the wording on its booking portal [S: T05], style quotes and invoices [S: T04],
reuse logo and colors in emails [S: T12], and staff can switch the CRM to dark mode [S: llms]. We go
much deeper: a business enters a few brand colors (or imports them from its website), and the
platform derives complete, **accessible** light **and** dark palettes it can then edit token by
token; it uploads its own fonts and logos (with light/dark variants and per-placement sizing), picks
or uploads icons, rewrites any customer-facing sentence per language, and tunes shadows, corner
radius, density and motion. Changes are drafted, previewed live across booking site / app / email /
invoice / CRM side by side, checked for contrast, then published as a versioned release that can be
rolled back in one click. The same token source compiles to CSS variables (edge-cached per tenant,
no flash of the wrong theme), inline email CSS, PDF stylesheets and iOS/Android/desktop token
bundles. A single responsive and accessibility system makes the booking flow, CRM (calendar, jobs,
job card, settings) and team app work properly on phone, tablet (incl. split-view on site) and desktop.

---

## 2. Roles

### 2.1 Who touches branding

| Role | What they do here | Source |
|---|---|---|
| Owner | Everything: edit draft, publish, rollback, upload fonts (accepts licence terms), custom CSS (tier-gated), delete assets, set who may publish | F1 [S: T05]; detail [D] |
| Operator / Admin | Edit draft, upload logos/icons/fonts, edit copy; **publish only if granted** `settings.brand.publish` (default: Admin yes, Operator yes) | [D] |
| Manager | View brand settings; may edit **copy** for their locale if granted (common: a manager fixes a typo) — cannot publish by default | [D] |
| Marketing role / external designer | Invited as limited member with `settings.brand.edit_draft` only — can build the theme, cannot publish; or receives a **share-preview link** (read-only, expiring) | [D] |
| Worker / Foreman / Estimator / Bookkeeper | No brand settings access. Personal **appearance** only (light/dark/system, text size, density, reduced motion) on CRM and team app | F7 [S: llms]; detail [D] |
| Customer (end client) | Sees the brand everywhere; toggles **light/dark/system** on booking site, account, app; their choice persists in a cookie / account profile | [S: BRIEF]; detail [D] |
| Hub owner (multi-business, Pro) | Creates a **brand template** and pushes it to child businesses with locks (e.g. franchise logo/colors locked, local copy editable) | multi-business hub [S: llms]; detail [D] |
| Platform support (our staff) | Read-only view of a tenant's Brand Kit + release history for support; can **force-disable** custom CSS or quarantine an asset (security), audited | [D] |
| System | Derives palettes, runs contrast audit, processes fonts/images/SVG, compiles artifacts, purges caches, rebuilds app bundles, schedules publish | [D] |

### 2.2 Permission keys (registered with Agent 09's RBAC matrix, form `module.resource.action`)

| Key | Owner | Operator | Admin | Manager | Foreman | Estimator | Worker | Bookkeeper |
|---|---|---|---|---|---|---|---|---|
| `settings.brand.view` | A | A | A | opt | — | — | — | — |
| `settings.brand.edit_draft` (colors, type, shape, logos, icons) | A | A | A | — | — | — | — | — |
| `settings.brand.copy.edit` (copy overrides, all locales) | A | A | A | opt | — | — | — | — |
| `settings.brand.publish` (publish, schedule, rollback) | A | A | opt (default on) | — | — | — | — | — |
| `settings.brand.fonts.upload` (implies licence attestation) | A | opt | opt | — | — | — | — | — |
| `settings.brand.custom_css.edit` (tier-gated `pro`) | A | opt | — | — | — | — | — | — |
| `settings.brand.preview.share` (create external preview links) | A | A | A | — | — | — | — | — |
| `settings.brand.assets.delete` (hard delete from library) | A | A | opt | — | — | — | — | — |
| `settings.brand.crm_workspace.edit` (staff-facing CRM/team-app branding) | A | A | opt | — | — | — | — | — |
| `self.appearance.edit` (own light/dark/system, text size, density, motion) | A | A | A | A | A | A | A | A |

Legend as in [S: 09] §2.4 (A = allowed, opt = toggle in permissions editor, — = never). Plan
entitlements mask keys (e.g. `custom_css` absent below `pro`). Surface mask: brand editing is
**CRM web + desktop app + iPad app**; on iPhone the brand screens are view + copy-edit + publish
only (no font upload/CSS editing on a phone) [D].

---

## 3. Screens

### 3.0 Surface matrix — what the Brand Kit controls on every surface

| Surface | Where | Theme modes | Logo placement(s) | Fonts | Icons | Copy overrides | Custom CSS (pro) | Render tech |
|---|---|---|---|---|---|---|---|---|
| `booking` public booking site | business subdomain / platform host [S: 01] | light, dark, system (customer toggle); business picks default | `web_header_desktop`, `web_header_mobile`, `favicon`, `og_image`, `loading_mark` | heading + body | category/service/nav/step icons | all `booking.*` keys | yes (scoped) | SSR HTML + per-tenant CSS vars |
| `account` customer portal (logged-in) | same host `/account` [S: 01] | same as booking | same as booking | same | nav/asset icons | `account.*` | yes | same |
| `customer_app` branded iOS/Android app | stores (team/pro tier) [S: llms] | light, dark, system (OS) | `app_icon`, `splash`, `app_header` | heading + body bundled/downloaded | same mapping | `app.*` + shared | no | native; tokens via JSON bundle (§10.11) |
| `email` transactional + marketing | inbox | light baseline + dark-mode hints | `email_header` (raster) | email-safe stacks (+ web font where supported) | raster icons only | `email.*` subjects/bodies via 08 | no | table HTML, inlined CSS (§10.9) |
| `sms_page` SMS landing pages (confirm, reschedule, pay, review) | business host short links [S: 08] | light/dark/system | `web_header_mobile` | same as booking | status icons | `sms_page.*` | yes (shares booking CSS) | SSR, ultra-light |
| `document` quotes/invoices/receipts HTML + PDF | `/q/`, `/i/` pages + PDF [S: 04] | **HTML: light/dark; PDF: always light** | `document_header`, `document_footer_mark` | heading + body **embedded** in PDF if licence allows | none (or line-item category icons, opt-in) | `document.*` labels ("Invoice", "Balance due") | no (layout via 04 options only) | HTML + print CSS → headless PDF |
| `tracking` on-my-way page | `/t/{token}` [S: 01] | light/dark/system | `web_header_mobile`, `map_pin_avatar` | body | status icons | `tracking.*` | yes | SSR + live updates |
| `embed` inline iframe + popup | business's other website [S: 01] | follows **host page** option (`light`, `dark`, `system`, or `match-parent` via postMessage) | `web_header_mobile` (optional, often hidden in embed) | same as booking | same | same | yes | iframe of booking site with `?embed=1` |
| `qr` QR codes & print sheets | downloads [S: 01][S: T05] | n/a (print = light) | `qr_center` (raster/SVG mono) | heading for print sheet | none | `qr.*` (call-to-action text) | no | SVG/PDF generation |
| `crm` business's own CRM workspace | web app, desktop app | light, dark, system per staff | `crm_sidebar`, `crm_sidebar_collapsed`, `crm_login` | optional: body font in CRM (default: platform UI font) | nav icon set choice | staff-side terms (e.g. rename "Jobs" → "Appointments") `crm.*` | no | SPA/SSR + same token engine, separate **CRM theme** |
| `team_app` worker app | iPhone/Android/iPad; branded binary on pro [S: llms] | light, dark, system per worker | `team_app_header`, `app_icon` (branded binary only), `splash` | platform UI font default; brand font optional | same as CRM | `team_app.*` | no | native |

Two theme scopes per business [D]:
1. **Public theme** — booking, account, customer app, sms_page, tracking, embed, document, email, qr.
2. **Workspace theme** — CRM + team app. Defaults to the platform's neutral UI with the business's
   logo and a brand accent; the business may extend it (full palette both modes) but **status
   semantic colors** (job states, success/danger) and worker calendar colors stay governed by
   platform rules for usability (they can be re-tuned, not removed) [D].

### 3.1 Navigation location

`Settings → Brand & appearance` (top-level settings group, next to "Online booking" which is 01's,
and "Communication" which is 08's) [D]. Fieldd places style under "Online scheduling → Style"
[S: T05] / "Customer hub → Style → Text" [S: T02] and document look under "Communication → Email"
[S: T04]. We centralize in one place and leave **deep links** in the old-style places: "Online
booking → Style" opens `Brand & appearance` filtered to the booking surface; "Communication →
Email → Document look" opens the Documents surface tab — so users coming from Fieldd find it where
they expect `[D]`.

Sub-navigation (left column, desktop):

```
Brand & appearance
 ├─ Overview            (health card, quick setup, publish status)
 ├─ Colors              (brand colors → palettes light & dark → semantic tokens)
 ├─ Typography          (fonts, scale, weights, line heights)
 ├─ Logos & images      (logo variants, placements, favicon/app icon, hero, OG image)
 ├─ Icons               (icon set, custom uploads, mappings)
 ├─ Shape & depth       (radius, shadows/elevation, density, borders, motion)
 ├─ Copy & languages    (text overrides, locales)
 ├─ Surfaces            (per-surface options & overrides: booking, app, email, documents,
 │                       tracking, embed, QR, CRM workspace, team app)
 ├─ Advanced            (custom CSS [pro], token import/export, brand template [hub])
 └─ Versions            (release history, scheduled publishes, rollback)
```

### 3.2 Global editor layout (all Brand sub-screens)

**Desktop (`xl` ≥ 1280 px):** three regions.

| Region | Width | Content |
|---|---|---|
| A. Sub-nav | 232 px fixed (collapsible to 56 px icon rail) | items above; each shows a dot when the draft differs from the live release |
| B. Controls | 400–480 px, scrollable | the section's controls in collapsible groups; sticky group header; inline validation; contrast badges |
| C. Preview | remaining (min 560 px) | live preview iframe(s) (§3.11) with toolbar: surface tabs · device (phone/tablet/desktop) · mode (light/dark/both side-by-side) · locale · zoom (fit/100%) · "Inspect copy" toggle · "Open full preview" |
| Top bar (sticky, 56 px) | full width | breadcrumb · **Draft status** ("Draft · saved 2 s ago" / "Saving…" / "Offline — changes kept on this device") · **Audit chip** ("2 contrast issues") · `Discard draft` · `Preview link` · **`Publish`** (primary; disabled without permission, tooltip explains) |

**Laptop / tablet landscape (`lg` 1024–1279):** Sub-nav collapses to icon rail; Controls 380 px;
Preview fills the rest.

**Tablet portrait (`md` 768–1023):** two regions: Controls full width; Preview becomes a **right
drawer** (60% width) toggled by a `Preview` button in the top bar, or pinned as bottom half
(split handle, persisted). Sub-nav becomes a segmented dropdown at top.

**Phone (`xs`/`sm` < 768):** Controls only, one section per screen (drill-down stack); a floating
`Preview` pill (bottom-right, above safe-area) opens a full-screen sheet with surface/device/mode
switchers; `Publish` lives in the top bar overflow `⋯` and in the Overview screen. Color pickers
open as bottom sheets; font upload and custom CSS show "Use a computer or tablet for this" with a
"Send me a link" action (email the deep link) [D].

**States (all sub-screens):**
- Loading: skeleton of controls + preview placeholder with the current release's background color (no white flash in dark mode).
- Empty (new business): Overview shows "Make it yours" quick-setup (§3.3) — defaults are already fully usable (F2 [S: T05]).
- Error loading draft: "Couldn't load your brand settings" + Retry; the live site is unaffected (stated explicitly).
- Conflict (two editors): optimistic concurrency on draft `revision`; second saver sees "Alex changed Colors 30 s ago — Reload their changes / Keep mine (overwrite)" with per-section merge when sections differ (§10.13).
- Read-only (no edit permission): controls disabled with a lock icon and "Ask an owner for edit access".
- Plan-locked control: shown with a small `Pro` badge, clickable → upgrade sheet; **never hidden** so users learn it exists.

### 3.3 Overview

Regions: (1) **Brand health card** — score badges: Contrast (AA pass count / total pairs), Fonts
(loaded, licence acknowledged), Logos (placements filled / required), Copy (overrides count, missing
translations), Performance (theme CSS size, font payload KB, logo KB; budget bars), Last published
(version, who, when). Each badge links to the fix.
(2) **Quick setup** (first-run or "Re-run setup"): 4 steps in a card stepper:
1. "Import from your website" — URL field (prefilled with business website) → `Import` → shows
   detected logo(s), colors (top 6 by area-weighted prominence), fonts (CSS font-family detected,
   matched to Google Fonts or flagged "upload your font file"), favicon. Each detected item has
   checkbox "Use". Mirrors Fieldd's AI setup capability F9 [S: llms]; mechanism §10.14 [D].
2. "Pick your colors" — 1 required (Primary) + optional Secondary, Accent, Neutral tint.
3. "Upload your logo" — one drop zone; we auto-create dark variant suggestion (§10.6).
4. "Choose fonts" — curated pairings (8 cards with live sample text) or "Upload my own".
Finish → preview opens in "both modes" view → `Publish`.
(3) **Surface cards** — one per surface (thumbnail of the draft rendered in the preview service,
light+dark halves), each with "Customize" deep link and status ("Uses defaults" / "Customized").
(4) **Recent activity** — last 10 brand audit events (who changed what).

### 3.4 Colors

Three tabs inside Colors: **Brand colors** (simple) · **Palettes** (advanced) · **Semantic tokens** (expert).
Plus a mode switch at the top of the Controls column: `Editing: Light ▸ Dark ▸ Both linked`.

**Tab 1 — Brand colors** (what 90% of users touch)

| Control | Type | Validation | Default |
|---|---|---|---|
| Primary color | color field: swatch + hex input + eyedropper (browser EyeDropper API where supported) + recent colors | `#RRGGBB` or `#RGB` (normalized to 6), also accepts `rgb()`, `hsl()`, `oklch()` text → converted; no alpha | platform default primary (a neutral-friendly blue-teal, final value Stage 2) |
| Secondary color | same | optional | derived: primary hue +150° (OKLCH), lower chroma |
| Accent color (highlights, badges, promotions) | same | optional | none (accent = primary) |
| Neutral tint | slider "Cool ← Neutral → Warm" (−1…+1) + optional seed color | — | 0 (pure neutral, chroma 0.005 brand-hue tint) |
| Success / Warning / Danger / Info seeds | collapsed "Status colors" group, 4 color fields + "Reset" | contrast-audited | platform defaults (green/amber/red/blue families) |
| Map pin color | color field (feeds 01's map) | — | = primary |
| Link style | segmented: `Primary color` · `Text color + underline` | — | Primary (falls back to underline automatically if link contrast < 4.5:1 vs body and < 3:1 vs surrounding text, per WCAG F73 practice [I]) |
| Dark mode palette strategy | radio: `Auto-derive from light (recommended)` · `Customize dark separately` | — | Auto-derive |
| Default mode for customers | radio: `Follow device (system)` · `Light` · `Dark` | — | System |
| Allow customers to switch mode | toggle | — | On |

Below each brand color: a **ramp strip** (11 swatches 50…950, §5.2) with the input color marked
(anchor) and AA badges for the most-used pairings. Hovering a swatch shows hex, OKLCH, and contrast
vs white/black/canvas.

**Contrast panel** (sticky at bottom of the Controls column): list of failing pairs, e.g.
"Button text on Primary — 3.2:1 (needs 4.5:1) · Light mode" with `Fix` (applies suggested color,
shows before/after swatches and ΔE) and `Ignore for now` (allowed only for non-text pairs < AA; text
pairs below AA **block publish** for public surfaces unless owner explicitly overrides with a typed
confirmation — §5.4). "Fix all" applies all minimal fixes.

**Tab 2 — Palettes**: one row per ramp (Primary, Secondary, Accent, Neutral, Success, Warning, Danger,
Info) × 11 steps, **per mode**. Each swatch is editable (click → picker). Edited swatches show a pin
icon (locked against re-derivation). Buttons: `Regenerate ramp` (keeps pinned steps), `Reset ramp`.
Controls: "Ramp curve" (Balanced / Vivid / Muted → chroma multiplier 1.0 / 1.15 / 0.75), "Hue shift
toward dark" (0–20°, default 6° — warm shades get slightly redder as they darken, a common
perceptual tweak [I]).

**Tab 3 — Semantic tokens**: table grouped by category (Backgrounds, Text, Borders, Actions, Status,
Data/calendar, Overlays, Focus). Columns: Token name (human label + code name on hover, e.g.
"Page background · `color.bg.canvas`"), Light value, Dark value, Source (`→ neutral.50` alias chip
or a raw hex), Contrast (against its declared pair), Used on (count of components; click → highlights
in preview). Each value cell: alias picker (choose a palette step — preferred, stays coherent when
brand colors change) or raw color (warning "won't follow brand changes"). Filter: "Only failing",
"Only customized". Search box.

**Mobile behavior:** tabs become a segmented control; palette table scrolls horizontally with the
ramp name column sticky; color picker is a bottom sheet with a large hue/chroma plane and hex input.

### 3.5 Typography

| Control | Type | Validation | Default |
|---|---|---|---|
| Heading font | font picker (search; tabs: `Recommended` · `Google Fonts` · `Uploaded` · `System`) | must have ≥ 1 weight ≥ 600 or a variable `wght` axis; warning if no Latin-Ext when business locale needs it | platform default (system UI stack for speed) |
| Body font | same picker | must include regular (400) or variable; warning if x-height very small (readability) | system UI stack |
| Mono / numerals | `Use body font with tabular figures` (default, uses `font-variant-numeric: tabular-nums` if font supports `tnum`) · or pick a font | — | body + tnum |
| Weights used | per role: Heading weight (500–800), Body regular (300–500), Body strong (500–800), Button label (400–700) | snapped to nearest available weight; preview shows actual rendered weight | 700 / 400 / 600 / 600 |
| Base size | slider 14–18 px (body text) | inputs on touch devices never < 16 px regardless (iOS zoom-on-focus) [I] | 16 |
| Scale ratio | select: 1.125 (Major Second) · 1.2 (Minor Third) · 1.25 (Major Third) · 1.333 (Perfect Fourth) · custom 1.05–1.5 | — | 1.2 |
| Fluid headings | toggle: headings scale between phone and desktop sizes with `clamp()` | — | On |
| Line height | Body (1.3–1.8), Headings (1.0–1.4) | — | 1.5 / 1.2 |
| Letter spacing | Headings (−0.03–0.05 em), Uppercase labels (0–0.12 em) | — | −0.01 / 0.04 |
| Heading case | `As typed` · `Uppercase` · `Title Case (display only)` | uppercase limited to ≤ 3 heading levels | As typed |
| Font loading | `Swap (show fallback, then your font)` · `Optional (use your font only if it loads fast)` | — | Swap (body), Swap (heading) |
| Fallback match | auto (computed metrics) with readout "Layout shift risk: Low" and manual `size-adjust` override (80–125%) | — | auto |

Font picker card shows: family name, sample in the business's name and "Book your service", weight
chips, file size estimate (Latin subset), licence badge (`OFL` / `Uploaded — licence acknowledged`),
`Preview on site`.

**Upload font dialog**: drop zone (WOFF2 / WOFF / TTF / OTF, ≤ 10 MB each, up to 24 files per family);
detected family/style/weight per file (editable mapping table: file → weight 100–900, style
normal/italic); **licence acknowledgement** checkbox (required): "I have a licence that allows this
font to be used on websites, in apps, in emails and embedded in PDFs for my business" + optional
licence note/URL + optional licence file upload; we display the font's own embedding flag
(`OS/2 fsType`) and **block PDF embedding** automatically if it is "restricted" (§5.6). Progress per
file: Uploading → Validating → Converting → Subsetting → Ready / Rejected (reason).

**Type preview block** (Controls column bottom): specimen with H1–H6, body, small, button, price
(`$1,249.00` with tabular figures), in both modes.

### 3.6 Logos & images

**Logo library** (top): grid of uploaded logo files (SVG/PNG/WebP/JPEG accepted; SVG preferred) with
tags: `Primary`, `Mark only (icon)`, `Wordmark`, `On dark` (light-colored version), `Monochrome`.
Upload: drop zone; SVG ≤ 512 KB, raster ≤ 10 MB, min 512 px on shortest side for raster (warn < 1024
for app icon use). After upload: auto-trim toggle (removes transparent/solid margins), detected
background ("Transparent ✓" / "Has white background — Remove background?" one-click for flat-color
backgrounds only — no AI cutout in v1), dominant colors ("Use as brand colors?").

**Variants matrix**: rows = mode (Light background, Dark background); columns = form (Full logo,
Mark only). Each cell: pick from library, or `Auto-generate` for the dark cell (options: invert
lightness of dark-on-light single-color logos; recolor to white; place on a brand-colored "badge"
plate) — preview on both canvases with contrast score of logo vs background (3:1 recommended for
graphical objects [I]).

**Placements table** — every placement, one row:

| Placement | Source variant | Size control | Padding / safe area | Background | Preview | Default |
|---|---|---|---|---|---|---|
| Booking header — desktop (`web_header_desktop`) | Full logo (mode-aware) | **max height** 24–96 px slider, max width 160–480 px | inner padding 0–24 px | transparent / header color | header mock | 40 px h, 240 max w |
| Booking header — mobile (`web_header_mobile`) | Full or Mark (choice) | 20–56 px h | 0–16 | — | phone mock | 32 px, Full if aspect ≤ 4:1 else Mark |
| Customer account sidebar | Full | 24–64 | — | — | — | 32 |
| Email header (`email_header`) | Full (raster PNG generated) | width 80–400 px (email images sized by width), alignment left/center | 0–32 | transparent or brand plate (recommended for dark-mode robustness) | email mock light+dark+"Gmail invert" | 160 w, center |
| Documents (`document_header`) | Full | height 24–120 px (printed at 300 dpi raster or vector) | 0–24 | — | A4/Letter mock | 48 h, left |
| Document footer mark | Mark | 12–32 | — | — | — | off |
| Favicon (`favicon`) | Mark | n/a — generated 16/32/48 ICO, 180 apple-touch, SVG with dark media query | safe area 0–20% | transparent or plate color (+ corner radius) | browser-tab mock light/dark | Mark, 10% pad, transparent |
| App icon (`app_icon`) — customer app / branded team app | Mark | n/a — generated iOS 1024 (no alpha), Android adaptive 108 dp fg + bg | safe zone indicator (Android 66/108 visible) | **required solid** color or gradient | iOS/Android home-screen mocks | Mark on primary |
| Splash / launch (`splash`) | Mark or Full | logo width 20–60% of short side | — | color token (bg per mode) | phone mocks light/dark | Mark 30% on canvas |
| PWA / web app manifest icons | from app icon | 192, 512, maskable 512 | maskable safe zone 80% circle | — | — | auto |
| Social share (`og_image`) | composited: logo + headline + brand color, or upload 1200×630 | — | — | — | link-preview mock | auto composite |
| CRM sidebar (`crm_sidebar`, `crm_sidebar_collapsed`) | Full / Mark | 20–40 px | — | — | CRM mock | 28 px |
| CRM login page | Full | 32–80 | — | — | — | 48 |
| Team app header | Mark | 20–32 | — | — | — | 24 |
| QR center (`qr_center`) | Mark, flattened mono or color | ≤ 22% of QR width (error correction H allows ~30% loss) [I] | white quiet plate | — | QR mock with scan test | off |
| Loading mark (`loading_mark`) | Mark | 24–64 | — | — | — | off (spinner) |
| Tracking map pin avatar | Mark | n/a (32 px circle) | — | plate = primary | map mock | Mark on primary |

"Apply suggested sizes" button computes per placement from logo aspect ratio (wide wordmarks get a
smaller max-height so visual weight stays balanced — §10.6 optical sizing).

**Images**: Hero/cover image for booking landing (≤ 10 MB, focal point picker, overlay scrim color
+ opacity token to keep headline contrast ≥ 4.5:1 — audit samples the image under the text box),
background pattern (optional), email banner defaults, default service image placeholder.

### 3.7 Icons

| Control | Type | Default |
|---|---|---|
| Icon set | cards: platform sets (e.g. 3–4 permissively licensed open-source sets, final list Stage 2 — candidates: Lucide (ISC), Phosphor (MIT), Tabler (MIT), Material Symbols (Apache-2.0) [I]); each card shows 12 sample semantic icons | platform default set |
| Style | where the set supports it: Outline · Filled · Duotone; stroke width 1.25–2.25 (stroke sets) | Outline, 1.75 |
| Icon color | `Inherit text color` · `Primary` · `Accent` · per-context override | Inherit (`currentColor`) |
| Icon size scale | S/M/L (16/20/24 base) | M |
| Custom icons library | upload SVG (≤ 50 KB, single-color recommended), or PNG for multi-color illustrations (≤ 500 KB, rendered as `<img>`, not recolorable) | — |
| Mappings | table tabs: **Categories**, **Services**, **Add-ons**, **Navigation** (booking + account + app), **Booking steps**, **Status** (read-only defaults, overridable for non-safety icons) | auto: keyword match of service names to platform icon tags (e.g. "interior detail" → `car-seat`) with "Accept all suggestions" |

Mapping row: Item (name from 02), current icon, picker (search across chosen set + custom), `Reset`.
Uploaded SVG flow shows **sanitization report** ("Removed: 1 script element, 2 event attributes,
1 external reference") and a recolor preview (`currentColor` conversion) in both modes. Rejected
files show why (e.g. "Contains embedded raster image larger than 256 KB").

### 3.8 Shape & depth

| Group | Control | Type | Range / options | Default |
|---|---|---|---|---|
| Corner radius | Base radius | slider + presets (Sharp 0 · Subtle 4 · Rounded 8 · Soft 12 · Round 16) | 0–24 px | 8 |
| | Buttons | `Follow base` · `Pill` · custom px | 0–32 / pill | Follow base |
| | Inputs, Cards, Dialogs, Chips, Images | per-component override (collapsed "Advanced") | 0–32 | Follow base (scaled: cards ×1.5, chips pill) |
| Shadows (elevation) | Style | `None (flat, use borders)` · `Soft` · `Crisp` · `Hard offset (retro)` | — | Soft |
| | Intensity | slider 0–200 % | — | 100 % |
| | Shadow tint | `Neutral` · `Brand-tinted` | — | Neutral |
| | Elevation per component | Card, Dropdown/Popover, Dialog, Sticky summary/bottom sheet, Header (on scroll), Toast | level 0–5 each | 1, 3, 4, 3, 1 (on scroll), 4 |
| | Dark mode depth | `Lighter surfaces + subtle shadow (recommended)` · `Shadows only` · `Borders only` | — | recommended |
| Borders | Width | 0 / 1 / 1.5 / 2 px | — | 1 |
| | Border emphasis | Subtle · Default · Strong (maps to neutral steps) | — | Default |
| Density | Spacing density | `Compact` · `Comfortable` · `Spacious` (separate for **public** and **workspace**) | multiplier 0.875 / 1 / 1.125 | public: Comfortable; workspace: Comfortable (staff can override personally) |
| Motion | Motion level | `Full` · `Reduced` · `None` | — | Full (always yields to OS `prefers-reduced-motion`) |
| | Speed | slider 0.75×–1.5× | — | 1× |
| Focus ring | Style | `Brand color ring` · `High-visibility (double ring)` | width 2–4 px, offset 2 px | Brand ring, auto-switches to double ring if brand ring fails 3:1 against any adjacent background |
| Buttons | Primary button style | `Filled` · `Outline` · `Soft (tinted)`; label case; icon position | — | Filled |

Preview shows a **component gallery** (buttons all states, inputs, card, dialog, slot picker,
bottom sheet, toast, calendar event chip) in both modes.

### 3.9 Copy & languages

Layout: left = key tree (grouped by surface → screen: `Booking › Date & time`, `Emails › Booking
confirmation`, `Documents › Invoice`, `CRM › Navigation` …) with counts of overridden keys; right =
editor list. Top: locale switcher (business default locale + enabled locales), search (matches key,
default text and override), filters (`Overridden`, `Missing translation`, `Too long`, `Has
placeholders`, `Locked`).

Each key row:
- Label (human), code key (copy button), **Default text** (platform, current locale, read-only).
- **Your text** field (single-line or multi-line by key kind), placeholder chips (`{weekday}`,
  `{business.name}`, `{count}`) insertable by click; ICU plural/select editor for keys that need it
  ("1 add-on" / "{count} add-ons").
- Max length meter (key-specific; e.g. button label 32 chars, step title 60), overflow warning from
  preview measurement ("Truncates on phone at 360 px").
- `Reset to default`, `Where is this used?` (highlights in preview), history (last 5 values).
- Locked keys (legal/compliance: SMS "Reply STOP to opt out", unsubscribe footer, card-authorization
  consent, cancellation-fee acknowledgement wording minimums) show 🔒 and either are non-editable or
  editable only around a **required core phrase** (validator ensures it's still present) [D].
- **Inspect copy mode** in the Preview pane: click any text in the preview → editor scrolls to its key
  (powered by `data-copy-key` attributes emitted only in preview builds).
- Bulk: Export locale as CSV/XLIFF/JSON; Import (validates placeholders, lengths; shows diff before
  apply). "Translate missing with AI" (draft suggestions, never auto-published; marked `machine`
  until a human accepts) [D].

Locales: `Add language` (BCP-47 picker: e.g. `en-US`, `en-GB`, `es-US`, `fr-CA`), default locale,
per-surface availability, customer locale detection order (§5.8). RTL languages (ar, he) supported
by the layout system (logical CSS properties) but flagged "beta" until audited [D].

Fieldd parity: "Routes text" override [S: T02] is the key `booking.datetime.route_slot_label`
(default "Anytime {weekday}"), which 01 also documents [S: 01] §5.9.

### 3.10 Surfaces (per-surface options)

One sub-tab per surface; each shows only options that differ from the shared Brand Kit:

| Surface tab | Options (all [D]) |
|---|---|
| Booking site & account | Default mode & toggle visibility (header icon button / footer link / hidden); header style (logo left · centered; solid · transparent over hero; sticky on scroll); header background token; footer style; "Powered by" badge (on unless plan allows removal [S: llms]); hero on/off; service card style (image top · image left · compact list); price emphasis; step indicator style (bar · dots · numbered); map style (01-owned; shown here as link) |
| Customer app | App display name (≤ 30 chars; home-screen label ≤ 12 recommended), icon & splash (from Logos), tab bar items & icons, default mode follows OS; "Requires new app build" badges on fields that cannot update over the air (§5.13) |
| Emails | Email width (560–680, default 600), header style (logo on canvas · logo on brand plate · full-width banner), body background, card style, button shape (radius capped for Outlook VML fallback), font stack mapping (web font → email-safe fallback chosen automatically, editable), footer color, dark-mode behaviour: `Adaptive (recommended)` · `Force light look` (best-effort; some clients ignore) |
| Documents (quotes/invoices/receipts) | Accent usage (header bar · thin rule · none), table zebra on/off, heading font (brand or "classic serif" option for formal invoices), paper size default (Letter / A4 by business country), margins (narrow/normal/wide), page numbers, "Print-friendly colors" toggle (limits large color fills), PDF/A archival toggle (open question §13). Column/field visibility remains in 04's Document style settings (deep link) [S: 04] |
| Tracking page | Map theme follows mode; worker card style; ETA emphasis color; show business logo vs worker photo first |
| Embed & popup | Mode in embed: `Same as booking site` · `Light` · `Dark` · `Match host page` (loader reads host `prefers-color-scheme` or a `data-mode` attribute); transparent background toggle (lets the iframe blend into the host page); popup button colors/label/position/radius (01 owns the snippet [S: 01] §4.1.8; tokens come from here); max width |
| QR & print | QR foreground (must stay dark-on-light, contrast ≥ 7:1 enforced for scan reliability [D]), module style (square · rounded dots — warn about scanner compatibility), finder-pattern color, logo center on/off, print sheet templates (flyer, door hanger, business card, yard sign) using heading font + brand colors |
| CRM workspace | `Use platform look + my logo & accent` (default) · `Full brand theme`; sidebar color (neutral / brand-tinted / brand solid), status color tuning (job statuses — re-tune only, AA enforced), worker calendar palette (categorical, 16 colors, AA-checked, dark variants — consumed by 09), staff default mode, staff allowed to change mode (always yes — accessibility), CRM font (platform UI font / brand body font) |
| Team app | Same as CRM workspace; branded team app binary (pro) icon/splash/name |

### 3.11 Live preview pane (behaviour spec)

- Renders **real surface templates** (the same renderer as production) fed by a **draft compile**
  (§10.4) and **fixture data**: business's real catalog (services/categories/prices from 02), a
  sample customer "Alex Sample", sample job/invoice with 3 lines + tax + tip, sample worker. Toggle
  "Use a real recent booking" (staff with permission; PII masked) [D].
- Surface tabs: `Booking` (pages: landing, service, add-ons, date/time, details, payment, confirmation)
  · `Account` · `App` (iPhone frame, Android frame) · `Email` (template picker: confirmation,
  reminder, quote, invoice, receipt, marketing sample; client simulation: Default, Apple Mail dark,
  Gmail-app dark (partial invert simulation), Outlook desktop (no radius/web fonts)) · `Invoice`
  (HTML view + `Render PDF` button producing a real PDF) · `Tracking` · `Embed` (inside a neutral
  sample host page with toggle "host page dark") · `QR` · `CRM` (dashboard, calendar, job card) ·
  `Team app`.
- Device: Phone 390×844, Tablet 820×1180 (portrait) / 1180×820 (landscape), Desktop 1440×900; custom
  width drag handle (320–1920) to test reflow.
- Mode: Light · Dark · **Side by side** (two iframes) · System (follows editor machine).
- Updates within ≤ 150 ms of a control change (CSS variables swapped via `postMessage` without
  reloading the iframe; only font/logo/structure changes reload) [D].
- "Open full preview" → new tab with signed preview URL `https://{host}/__preview/{token}` (expires
  24 h; `noindex`; banner "Preview of unpublished changes"). **Share preview link** (permission
  `settings.brand.preview.share`): 1–7 days, optional passcode, revocable.
- Accessibility overlay toggle: shows contrast failures inline (red outline + ratio label), focus
  order numbers, and touch-target size failures (< 44 px on touch device frames).

### 3.12 Publish dialog

Triggered by `Publish`. Content:
1. **Summary of changes** (semantic diff, grouped): "Colors: primary #1F6FEB → #0B5BD3; 14 derived
   tokens changed · Typography: body font Inter → Source Sans 3 · Logos: email header size 160 → 200 ·
   Copy: 6 keys (en-US), 2 keys (es-US)".
2. **Audit results**: Contrast (pass / warnings / blocking), Fonts (licence acknowledged ✓), Assets
   (all processed ✓), Performance budgets (e.g. "Fonts 148 KB > 120 KB budget — page may load slower
   on mobile" — warning, not blocking), Custom CSS guardrail check (pass/fail), Missing translations.
3. **Affected surfaces** with checkboxes? — **No** [D]: a release always applies to all surfaces
   (atomic); per-surface staged rollout is out of scope v1 (avoids mixed-brand states).
4. **Native apps note**: "Colors, fonts, copy will update in the app on next launch. App icon/name
   changes need a new app version (submitted separately)."
5. When: `Publish now` · `Schedule` (date/time in business timezone; e.g. rebrand launch at midnight).
6. Release note (optional, ≤ 200 chars).
7. Confirm button `Publish to all surfaces`. On success: toast "Live in a few seconds" with link to
   the live site; Versions shows v(n+1).
Blocking failures disable confirm with reasons and "Fix" links. Owner override for contrast blocks:
typed confirmation "PUBLISH WITH CONTRAST ISSUES" + logged (§5.4).

### 3.13 Versions

Table: Version · Status (`Live`, `Scheduled`, `Superseded`, `Rolled back from`) · Published by ·
At · Note · Changes (diff link) · Actions (`Preview` (renders that release), `Compare with live`,
`Restore` → creates new draft from that version **or** `Roll back now` → publishes it as v(n+1)
immediately (permission `publish`)). Scheduled release row: `Edit time`, `Cancel`. Named versions
("Summer 2027 look") are pinned and never pruned (§5.14).

### 3.14 Advanced

- **Custom CSS** (tier `pro`) — code editor with syntax highlighting, linting against the allowlist
  (§5.7), part-selector autocomplete (`[data-part="booking-step-title"]` …), size meter (≤ 20 KB),
  scope selector (Booking & account · Tracking · SMS pages · Embed — default all public web), guardrail
  check results, `Disable custom CSS` kill switch (instant, no publish needed — emergency path).
- **Token import/export** — export Brand Kit as JSON (our schema §7.2 / W3C Design Tokens format) for
  designers (Figma Tokens/variables workflows); import validates schema, shows diff, writes draft only.
- **Brand template** (hub, pro) — "Save as template", "Push to businesses" with lock checkboxes per
  section (Colors, Fonts, Logos, Icons, Copy keys, Shape) [D].
- **Reset Brand Kit to platform defaults** (draft only; confirm).

### 3.15 Appearance controls for viewers

**Customer (booking site / account / tracking / SMS pages):** header icon button (sun/moon/monitor)
opening a 3-option menu `Light · Dark · Match device` (`aria-haspopup="menu"`, labelled "Appearance");
choice saved to first-party cookie `pl_mode` (1 year, `SameSite=Lax`, host-only) and, if signed in,
to the customer's profile (syncs to app). Hidden if business disabled the toggle (§3.4). In the
customer app: Account → Appearance (same 3 options; default OS).

**Staff (CRM, desktop app, team app):** avatar menu / side menu → `Appearance` (Fieldd puts dark-mode
switching in the side menu F7 [S: llms]): Mode (Light/Dark/System), Text size (90–150 %, also
honours browser zoom and iOS Dynamic Type), Density (Compact/Comfortable/Spacious — CRM tables and
calendar), Reduce motion (follow device / always), High-contrast mode (forces AAA-ish token set,
thicker focus rings, underlined links), Color-vision assist for calendars (adds patterns/icons to
status colors). Stored per user (not per business) so it follows the person across businesses
(multi-business hub) [D]; keyboard shortcut `Shift+D` toggles light/dark in the CRM [D].

### 3.16 Responsive layout specifications (per product area)

Breakpoints and rules are in §5.10; this section fixes the **layout** of each major screen.

#### 3.16.1 Booking flow (public) — owner of content: 01 [S: 01] §3.0/3.A

| Region | Phone (`xs`/`sm`) | Tablet portrait (`md`) | Tablet landscape / desktop (`lg`+) |
|---|---|---|---|
| Header | 56 px; logo (mobile placement) left, account + mode icons right; hides on scroll down, reveals on up | 64 px; full logo | 72 px; full logo; optional nav links (Services, Gift cards, Sign in) |
| Progress | thin bar + "Step 3 of 7 · Add-ons" text under header | same | left-aligned step list inside content column |
| Content | single column, 16 px side gutters, full-width cards | single column max 640 px centered | content column 640–720 px + right **summary rail** 320–360 px (sticky) |
| Service list | 1 column cards (image left 72 px or top 16:9 by setting) | 2-column grid | 2–3-column grid (container query: card min 280 px) |
| Date/time | horizontal date strip (7 days, swipe) + slot chips grid (3 per row, 44 px tall) | date strip + 4 per row | month calendar (left) + slot list (right) inside content column |
| Summary/cart | bottom sheet: collapsed bar (44 px+ tap area) "2 items · $189 · Continue" over safe-area inset; expands to 80% height | same as phone (bottom bar) | right rail, always visible |
| Primary action | full-width sticky footer button (min 48 px high) | inline at bottom + sticky | in rail + inline |
| Forms | labels above fields, 1 column; `inputmode`/`autocomplete` set; 16 px+ input text | 2 columns for short pairs (first/last name) | 2 columns |
| Dialogs | full-screen sheets | centered modal max 560 px | centered modal |
| Map (address) | 160 px tall static map, tap to expand full-screen | 240 px | 280 px inline |

Embed (`?embed=1`): header/footer hidden by default, page height reported to loader; popup uses
phone layout under 640 px of iframe width irrespective of device (container-query driven) [D].

#### 3.16.2 CRM shell (workspace)

| | Phone | Tablet portrait | Tablet landscape | Desktop |
|---|---|---|---|---|
| Primary nav | **bottom tab bar** (5: Home, Schedule, Jobs, Customers, More) + floating `+ New` button (bottom-right, above tab bar) | icon **rail** 72 px left (labels under icons) | rail 72 px; expandable to 248 px overlay | **sidebar** 248 px, collapsible to 72 px rail (persisted per user) |
| Top bar | 56 px: page title, search icon (opens full-screen search), notifications | 64 px: search field, notifications, avatar | same | same + global `+ New` split button |
| Content | single pane, drill-down stack (back arrow) | master–detail where meaningful (list ⅓ / detail ⅔) | master–detail (list 360 px / detail) | multi-pane (list / detail drawer / side panels) |
| Overlays | full-screen sheets | sheets/drawers 480 px | drawers 480–640 px | drawers 480–720 px, dialogs |

Keyboard (desktop): `/` search, `g s` schedule, `g j` jobs, `n j` new job, `?` shortcut help; all
shortcuts listed in help and disableable (WCAG 2.1.4) [D].

#### 3.16.3 Calendar / schedule (03 owns behaviour [S: 03])

| Device | Default view | Layout |
|---|---|---|
| Phone | **Agenda (day list)** for the selected worker filter | Date strip (swipe weeks) on top; worker filter chips (horizontal scroll); jobs as cards in time order with travel-time separators [S: llms "travel time for mobile jobs on the calendar"]; tap → job page; long-press → quick actions sheet; "Day timeline" toggle shows a single-column time grid (one worker at a time, swipe between workers) |
| Tablet portrait | Day **resource view**, 3 workers visible, horizontal scroll for more | Time axis left (sticky), worker columns min 200 px; unassigned jobs as a collapsible bottom tray (drag up into columns) |
| Tablet landscape | Day resource view, 4–6 workers visible + **map split** toggle (calendar 60% / map 40%) [S: llms "map that shows who is free"] | Drag-and-drop with touch (long-press 300 ms to pick up, auto-scroll at edges, haptic tick on snap where supported) |
| Desktop | Week / Day / Resource / Timeline (horizontal, workers as rows) / Map | Left mini-calendar + worker filters (240 px, collapsible); main grid; right drawer for job card (480–640 px) so the calendar stays visible |

Event chip tokens: background = worker color tint (09) or status tint (setting), 3 px left border =
worker color, text uses on-color computed for AA; status icon + text (never color alone);
density-aware heights; minimum chip height 24 px (shows title only) with full info in tooltip/
popover and accessible name "Exterior detail, 9:00 to 10:30, Sam, Confirmed".

#### 3.16.4 Jobs table (04 owns data)

| Device | Rendering |
|---|---|
| Desktop | Data table: sticky header, sticky first column (job #/customer), column chooser, resizable columns, density row height (32/40/48), bulk select, inline status menu; virtualized rows (≥ 200 rows) with `aria-rowcount`/`aria-rowindex` |
| Tablet landscape | Same table with ≤ 6 default columns; others in row-expand panel; master–detail: tapping a row opens detail on the right (table narrows to key columns) |
| Tablet portrait | Card list (2 lines per row: customer · service / date · status · total) + detail drawer |
| Phone | Card list; filters as a bottom sheet; sort as a menu; swipe actions (Call, Message, Mark complete) **each also available in the row's `⋯` menu** (swipe is never the only path); pull-to-refresh |

#### 3.16.5 Job card / job detail

| Device | Layout |
|---|---|
| Desktop | Drawer (from calendar/table) or full page: two columns — main (customer, address+map, services & add-ons, checklist, photos, notes, timeline) / side (status, assignment, schedule, payment summary, actions) |
| Tablet landscape | Full page two columns 60/40; sticky action bar top-right |
| Tablet portrait | Single column with sticky **section tabs** (Details · Services · Checklist · Photos · Payment · Activity) |
| Phone | Single column; sticky **bottom action bar** with the next lifecycle action (On my way → Start → Complete → Collect payment, 04's state machine) as a full-width 52 px button; secondary actions in `⋯`; call/text/navigate as large icon buttons under the customer name |

#### 3.16.6 Settings (all modules)

Desktop: settings left nav (two-level, 248 px) + content (max 880 px) + optional right help/preview
panel. Tablet: master–detail (nav 280 px). Phone: drill-down list → page; long forms get a sticky
`Save` bar; unsaved-changes guard on back navigation.

#### 3.16.7 Team app (worker) — phone-first [S: 09 §3.12][S: 04]

| Device | Layout |
|---|---|
| iPhone / Android phone | Bottom tabs: Today · Schedule · Messages · Timesheet · More. Today = route-ordered job list with next job card hero; job screen as §3.16.5 phone; camera/photo grid full-bleed; checklist items with 48 px tap rows; one-handed reach: primary actions in bottom third |
| iPad / Android tablet | **Split view**: left 320–360 px day list (jobs, timesheet chip), right job detail; landscape adds a third collapsible pane for **map/route** or **customer history**; works in iPadOS Split View / Slide Over (layout responds to window width, not device type) |
| On-site customer-facing mode | See §3.17 |

### 3.17 Branded on-site & phone-call screens (native apps + CRM)

Owner requirement: staff can **adjust a client's package on site** and **take a phone call and build
a service/booking live** [S: BRIEF]. Data/behaviour belongs to 02/03/05/06; the screens' theming,
layout and "customer-facing mode" belong here [D].

**A. "Adjust package" on site (iPad landscape, iPhone, desktop app)**
- Entry: Job detail → `Adjust services` / Customer → Subscription → `Change package`.
- Layout (tablet landscape): left 55% = catalog browser (categories as large tiles with brand icons,
  services as cards with price/duration, add-on chips); right 45% = **live order summary** (lines,
  discounts, tax, total, change vs. original "+$45.00"), subscription impact ("Next visit $129 → $149
  from Dec 2"), `Review with customer` button.
- **Customer-facing mode** (`Review with customer`): screen re-themes from the *workspace theme* to
  the *public theme* (customer sees the business's brand, not the staff UI), hides internal fields
  (cost, margin, internal notes, other jobs), enlarges type (+2 scale steps), shows a clean summary,
  terms text (copy keys, locked legal parts), signature/consent pad and `Approve` button sized ≥ 56 px;
  exit requires staff action (Face ID / passcode / long-press 2 s on a staff-only corner affordance)
  so the customer cannot wander into the CRM [D].
- Phone: the same as a stacked flow (catalog → summary sheet → customer-facing review full-screen).

**B. "Phone booking builder" (desktop/web CRM + desktop app, also iPad)**
- Entry: global `+ New → Booking (phone call)`, or incoming-call pop (if telephony integration
  exists — 08) → opens builder with caller matched to a customer (07).
- Layout (desktop ≥ 1280): three columns — (1) **Customer** (search/create, addresses, vehicles/assets
  — 07), (2) **Build** (catalog search with keyboard: type "int det" → "Interior detail", add-ons,
  quantity, price override with permission), (3) **When & confirm** (availability from 03 with
  next-available suggestions, worker choice, payment option: send payment link / take card now via 05,
  summary + `Book`). Keyboard-first: `Tab` order follows columns; `Ctrl/⌘+Enter` books; every
  field reachable without mouse.
- Laptop/tablet landscape: columns 2+3 merge into tabs; phone: stepper.
- Read-back panel: one-click "Read back to customer" script generated from copy keys (e.g. "So
  that's an Interior Detail on Thursday 9 am at 12 Oak St, total $189") — copy-overridable [D].
- Sends the customer a **branded confirmation** (public theme) immediately; the staff never sees the
  public theme in the builder except in the "Customer will see" preview card.

---

## 4. Workflows

All workflows are `[D]` unless tagged. Event names follow `domain.entity.verb`; this module's domain
prefix is `brand.` (and `appearance.` for viewer preferences).

### 4.1 New business — default Brand Kit seeded
1. Consume `tenant.business.created` (09).
2. Create `BrandKit` with `draft` = platform default ThemeDocument (§7.2), default locale from the
   business country, no logos (text logo fallback = business name in heading font), default icon set.
3. Compile and publish **release v1** automatically ("Platform defaults") so every surface renders
   immediately — Fieldd's "the rest are all ready to go" F2 [S: T05].
4. Emit `brand.release.published {version:1, auto:true}`.
5. Fallback text logo: business name rendered in heading font, truncated at 28 chars with full name
   in `aria-label`; favicon/app icon auto-generated as monogram (first letter(s) on primary color).

### 4.2 Quick setup / import from website (F9 [S: llms])
1. Owner enters URL → `brand.import.requested`.
2. Fetcher (server-side, platform-owned headless browser, respects robots.txt, 15 s budget, 5 MB
   page limit, no auth, blocks private IP ranges to prevent SSRF) loads the page at 1440 px and 390 px.
3. Extract: `<link rel=icon>`, `apple-touch-icon`, `og:image`, `<img>`/inline `<svg>` inside `header`
   or with `logo` in class/id/alt (score by position top-left, size, filename) → up to 5 logo candidates;
   CSS computed colors of buttons/links/headers weighted by pixel area (screenshot k-means in OKLab,
   k=8, drop near-white/near-black unless dominant) → color candidates with roles guessed (most
   saturated on CTA = primary); computed `font-family` of `h1`/`body` + `@font-face` src URLs → match
   against Google Fonts catalog by family name; unmatched → "Upload your font (we found *BrandSans*)".
   We **never** auto-download a business's commercial font files from its site (licence) — the owner
   must upload them with attestation.
4. Show candidates (§3.3 step 1); selections write to **draft only**; record provenance
   (`source:'import', url`). Emit `brand.import.completed`.
5. Failure modes: site blocks bots / Cloudflare challenge → "We couldn't read your site — upload your
   logo and pick colors manually" (we do not bypass challenges; same rule as BRIEF for fieldd.co).

### 4.3 Edit draft (any control)
1. Control change → client applies optimistic update to the preview (CSS var swap) immediately.
2. Debounced (800 ms idle, or on blur) `PatchBrandDraft {revision, ops[]}` (JSON-Patch-like ops on the
   ThemeDocument) → server validates each op (schema, ranges), applies, increments `revision`, runs
   **incremental derivation** (e.g. new primary → regenerate primary ramp except pinned steps →
   re-resolve semantic aliases) and **incremental audit** (contrast pairs touching changed tokens).
3. Response returns new `revision`, derived token diffs, audit deltas; UI updates badges.
4. Offline (CRM PWA/desktop app): ops queue locally with base revision; on reconnect, replay; if base
   revision is stale → rebase per section (§10.13) or conflict prompt.
5. Writes `BrandAuditEvent` (who, section, summary); emits `brand.draft.updated` (internal; not consumed
   by public surfaces).

### 4.4 Upload a logo / image
1. Client requests upload slot `CreateAssetUpload {kind:'logo', filename, bytes, mime}` → returns
   pre-authorized upload URL to **quarantine** storage (not publicly readable), expires 15 min.
2. Client uploads; calls `CompleteAssetUpload`.
3. Pipeline (async, idempotent by content SHA-256): sniff real type by magic bytes (ignore extension);
   reject mismatches; malware scan; for SVG → sanitize (§10.7) → re-serialize → store sanitized only;
   for rasters → decode with hardened decoder in a sandbox, strip metadata (EXIF incl. GPS), convert
   CMYK → sRGB, detect alpha, auto-trim (if chosen), compute bounding box, dominant colors, perceived
   luminance (for auto dark-variant decisions); generate master PNG (max 4096 px) + preview.
4. Status `ready` → `brand.asset.ready`; UI shows variant suggestions. Rejected → `brand.asset.rejected
   {reason}` with human message.
5. Placement derivatives (WebP/AVIF/PNG at 1×/2×/3×, email PNG, favicon set, app icons) are generated
   **at publish compile time** for the assets referenced by the release (cheap, deterministic, cached
   by `(assetHash, placementSpecHash)`).

### 4.5 Upload a custom font
1. Owner (permission `settings.brand.fonts.upload`) ticks licence attestation → `CreateFontUpload`.
2. Upload each file to quarantine; `CompleteFontUpload`.
3. Pipeline per file: magic-byte check (`wOF2`, `wOFF`, `\0\1\0\0`/`true` TTF, `OTTO` CFF); run through
   an **OpenType sanitizer** (the same class of tool browsers use, e.g. OTS [I]) — reject on failure;
   parse `name` (family, subfamily, full name, version, licence URL/description IDs 13/14), `OS/2`
   (`usWeightClass`, `fsSelection` italic bit, `fsType` embedding permissions, `sxHeight`, `sCapHeight`,
   typo ascender/descender/lineGap, `xAvgCharWidth`), `hhea`, `head.unitsPerEm`, `fvar` axes; cmap
   coverage per Unicode block.
4. Convert to WOFF2 (Brotli). Generate **subsets** by `unicode-range` buckets (latin, latin-ext,
   cyrillic, cyrillic-ext, greek, vietnamese; others kept whole if present) — each ≤ budget; drop
   hinting for web subsets (smaller) but keep a full hinted original for PDF embedding [I].
5. Compute fallback metric overrides vs. candidate local fallbacks (Arial/Helvetica for sans,
   Times New Roman/Georgia for serif, by `OS/2` panose/serif class or heuristic) (§10.8).
6. Group files into a `FontFamily` (by `name` IDs 16/1) with faces; detect duplicates by hash.
7. Ready → `brand.font.ready`; rejected → `brand.font.rejected {reason}` (e.g. "File is corrupt",
   "Font forbids embedding (restricted licence) — it can still be used on web pages but not inside
   PDFs; documents will use the fallback font", "Too large after conversion (> 600 KB per subset)").

### 4.6 Choose a Google Font
1. Picker queries our **mirrored catalog** (platform job syncs the Google Fonts metadata daily using a
   platform API key, §9.5) — no runtime calls from the browser to Google.
2. On selection, the platform downloads the font files once into our font store (shared across tenants,
   dedup by family+version), producing the same subsets/metrics as §4.5.
3. Served from **our** CDN (first-party relative to the booking host via the asset domain) — avoids
   sending visitor IPs to a third party (a 2022 German court ruling found embedding Google-hosted
   fonts leaked IPs unlawfully under GDPR [I — LG München I, 3 O 17493/20; verify]), and avoids an extra
   DNS/TLS connection.

### 4.7 Upload custom icons
1. Upload SVG (≤ 50 KB, ≤ 25 files per batch) → sanitize (§10.7) → normalize: compute bbox, set
   `viewBox` to square padding-fit (24×24 logical), remove `width/height`, convert single-color fills/
   strokes to `currentColor` (unless "Keep original colors"), flatten transforms where safe, optimize
   paths (precision 2). Reject if > 2,000 path commands after optimization or contains raster
   `<image>` > 64 KB.
2. Store sanitized icon; add to tenant icon library with tags (filename-derived + user-entered).
3. Mappings saved in draft. On publish, compile **tenant sprite** (SVG `<symbol>` sheet) for web and
   per-icon PDF/PNG for native/email.

### 4.8 Edit copy overrides
1. Owner edits a key for locale L → validator: placeholders ⊆ key allowlist; ICU syntax parse; required
   core phrase present (locked keys); length ≤ key max; no HTML (text is escaped at render; markdown-lite
   `**bold**` and links only on keys flagged `richText`, links must be `https:` or `tel:`/`mailto:`).
2. Saved to draft copy bundle; preview re-renders strings.
3. Missing translations for enabled locales flagged (fallback chain shows what customers will see).
4. On publish, the copy bundle is compiled per surface × locale to compact JSON maps (only overridden
   keys; defaults ship with platform builds).

### 4.9 Publish (now or scheduled)
1. `PublishBrandDraft {expectedRevision, scheduleAt?, note?, contrastOverride?}` (permission
   `settings.brand.publish`). Idempotency key required.
2. Lock draft revision; run **full audit** (§10.3) — blocking issues abort with list.
3. Freeze `ThemeVersion` snapshot (immutable ThemeDocument + resolved tokens + asset refs by content hash
   + copy bundle + custom CSS) → `BrandRelease{version:n+1, status:'compiling'}`.
4. **Compile all artifacts** (§10.4) in parallel: public CSS (booking/account/tracking/sms/embed),
   workspace CSS, email theme, document print CSS, native token bundles (iOS/Android/desktop), favicon &
   manifest set, logo derivatives, icon sprite, copy bundles, OG image. Each artifact content-hashed,
   uploaded to the asset store under immutable paths `/{tenantShortId}/b/{hash}.{ext}`.
5. All succeed → if scheduled: status `scheduled` (§6.1); else **activate**: atomically set
   `BrandKit.liveReleaseId = n+1` in the source-of-truth DB, then write the tenant's **edge pointer**
   (`tenant:{id}:brand → {version, manifestHash}`) to the edge key-value store, then purge HTML caches by
   tag `tenant:{id}:brand` (HTML embeds artifact URLs; artifacts themselves never need purging).
6. Emit `brand.release.published {tenantId, version, manifest, changedSections[], appBuildRequired:bool}`.
7. Consumers: 01 (portal HTML purge if not already), 08 (email theme cache refresh; queued emails
   render with the release active at **send** time), 04 (new document renders snapshot this release;
   existing sent documents unchanged), native app config service (bumps `themeVersion` → apps fetch on
   next foreground), 10.
8. Target: publish-to-live ≤ 10 s p95 globally (01 targets ≤ 5 s for config purge [S: 01] §4.12 — brand
   compile adds ~5 s) [D].
9. Any compile failure → release `failed`, nothing switches, draft untouched, error surfaced with retry.

### 4.10 Rollback
1. `RollbackBrand {toVersion}` → creates release n+1 whose snapshot = version K's snapshot (artifacts
   are reused by hash — near-instant, no recompile unless the compiler version changed, §5.14).
2. Activate as in §4.9 step 5–7; event `brand.release.rolled_back {from:n, to:K, newVersion:n+1}`.
3. Draft is **not** overwritten (owner may keep working); a banner says "Live site is now v(n+1)
   (restored from vK). Your draft still contains newer edits."
4. Emergency kill switches (no publish needed, audited): `DisableCustomCss`, `QuarantineAsset` (falls
   back to text logo/monogram) — used by owner or platform support.

### 4.11 Viewer changes appearance mode
- **Customer on web**: menu choice → set cookie `pl_mode=dark|light|system` → `<html data-mode>`
  attribute updated without reload (CSS already contains both palettes §10.5) → if signed in,
  `appearance.customer.updated`; stored in 11's `AppearancePreference` row keyed by the portal account
  id (the portal account itself is owned by 01) [D].
- **Staff**: `UpdateMyAppearance` → stored per user; pushed to all their open sessions via the
  realtime channel; desktop/team apps apply immediately.
- **Business changes default mode**: applies only to viewers **without an explicit choice**; a viewer
  who picked `system` keeps following their device (it is an explicit choice once selected).

### 4.12 Email render with brand (contract with 08)
1. 08 renders template T for tenant at send time → asks 11 for `EmailTheme(releaseId)` (cached):
   inline-able CSS map, `<style>` block with dark-mode media queries, email-safe font stacks, logo PNG
   URLs (light + dark + plated), button VML params.
2. 08's renderer applies; 11 provides the **inliner + dark-mode post-processor** library (§10.9) so
   all ~200 platform emails [cf. F10 S: llms] share one implementation.

### 4.13 Document (quote/invoice/receipt) render (contract with 04)
1. 04 renders document version → calls `DocumentTheme(releaseId)` → print stylesheet, logo (vector if
   SVG; else 300-dpi PNG), embeddable font files (or fallback flag), page size.
2. HTML view (`/q/`, `/i/` on business host) uses the public theme **with mode support**; PDF render
   uses the **light** palette with print adjustments (§5.12) — headless browser print-to-PDF, fonts
   embedded subset.
3. 04 stores `brandReleaseId` + content hash with the immutable rendering. Re-downloading an old
   invoice PDF returns the stored file (brand of the time), never re-rendered with a new brand.

### 4.14 Branded customer app / branded team app build (pro / tier-gated) [S: llms F8]
1. Prerequisites shown in `Surfaces → Customer app`: business's own Apple Developer account and Google
   Play Console account connected (App Store guideline on template apps — §9.5), app name, bundle id
   suggestion `com.{business}.app`, icons valid (1024 px no alpha), privacy details.
2. "Request build" → platform CI builds the white-label binary with **baked**: name, icon, splash,
   bundle id, push credentials, deep-link domains (business booking host for universal links / app
   links — `apple-app-site-association` and `assetlinks.json` served on the business host by 01) and a
   **fallback** theme bundle (current release).
3. Runtime: app fetches `GET /app/theme?platform=ios&v={current}` on launch/foreground (ETag) →
   updates tokens, fonts (downloaded and registered at runtime), copy, logos without store review.
4. Changes to name/icon/splash/bundle/permissions → "New app version needed" → new build + submission
   (status tracked: building → submitted → in review → released). Emits `brand.app_build.*`.

### 4.15 Plan downgrade / entitlement loss
- Consume `tenant.plan.changed`. If `custom_css` lost: custom CSS stays stored but **excluded** at next
  compile; auto-publish a "plan change" release v(n+1) without custom CSS within 24 h (notify owner 7 days
  ahead when downgrade is scheduled). "Powered by" badge re-appears if removal entitlement lost.
  Branded app: existing binaries keep working for 30 days, then theme endpoint serves platform theme
  and the app shows "Contact {business}" (policy decision §13). Everything else the owner listed in
  BRIEF (palettes, fonts, logos, icons, copy) remains available on all tiers (§12.2).

### 4.16 Hub brand template push (pro, multi-business) [S: llms "multi-business hub", "profile cloning"]
1. Hub owner saves template from business A's Brand Kit (snapshot) with lock flags per section.
2. Push to businesses B..N → each receives template as **draft** changes (locked sections overwritten;
   unlocked merged only where the child hasn't customized) → child publishes, or hub owner may publish
   on their behalf if they hold `settings.brand.publish` in each.
3. Locked sections appear read-only in child editors with "Managed by {hub name}".

### 4.17 Asset deletion
- Deleting an asset used by the **live release** or any pinned/scheduled release is blocked ("Used by
  v12 (live)") — only removable from the library after it is no longer referenced. Assets referenced only
  by superseded unpinned releases may be deleted; those releases become "not restorable" (marked) [D].
  Legal hold: assets referenced by issued documents are retained as long as the documents (04 retention).

---

## 5. Business rules & math

All `[D]` (our design) unless tagged; standards facts tagged `[I]` (from standards knowledge, verify
against the cited spec text during build).

### 5.1 Token layers and resolution order

```
Layer 0  platform defaults (shipped with each platform build; complete; light + dark)
Layer 1  brand inputs (BrandKit draft/release: seeds, fonts, radius base, shadow style, density …)
Layer 2  derived reference palettes  (ramps 50…950 per seed, per mode)          ← computed
Layer 3  semantic tokens per mode     (alias → ramp step, or raw override)       ← computed + overrides
Layer 4  component tokens             (alias → semantic, or raw override)       ← computed + overrides
Layer 5  surface overrides            (e.g. email button radius capped, PDF forces light)
Layer 6  viewer preferences           (mode, text scale, density, reduced motion, high contrast)
Layer 7  custom CSS (pro, public web only; cannot redefine tokens marked `locked`)
```

- Resolution = highest layer that defines the token wins; aliases resolved recursively (max depth 8;
  cycles rejected at validation).
- A **pinned** value (explicit override) is never re-derived when seeds change; un-pinned values always
  re-derive. UI shows pin state.
- `locked` tokens (platform-controlled): focus ring minimum width (2 px), minimum touch target sizes,
  danger/success *semantics* (hue family may be tuned, but danger cannot become green: hue guard
  ranges, §5.5), z-index scale, breakpoints.

### 5.2 Palette ramps (OKLCH) [D, math I]

- Every seed color generates an 11-step ramp `50,100,200,300,400,500,600,700,800,900,950` per mode.
- Work in **OKLCH** (perceptually uniform lightness) — then gamut-map to sRGB by reducing chroma at
  constant L and h until in gamut (CSS Color 4 gamut-mapping approach [I]).
- Target lightness per step (light-mode ramp):

| Step | 50 | 100 | 200 | 300 | 400 | 500 | 600 | 700 | 800 | 900 | 950 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| L (OKLCH) | 0.975 | 0.945 | 0.885 | 0.810 | 0.720 | 0.630 | 0.550 | 0.470 | 0.390 | 0.310 | 0.230 |
| chroma factor × seed C | 0.10 | 0.20 | 0.40 | 0.65 | 0.85 | 1.00 | 1.00 | 0.92 | 0.80 | 0.65 | 0.50 |

- **Anchor fidelity:** the step whose L is closest to the seed's L becomes the seed's *exact* color
  (`anchorStep`); neighbours are interpolated between the anchor and the table targets so the
  business's exact brand hex always appears in the palette.
- Hue drift: `h(step) = h_seed + hueShift × (L_seed − L_step)/0.75` (default `hueShift` = 6°; warm
  hues drift toward red as they darken; disabled for neutrals).
- Neutral ramp: seed chroma = `0.005 + 0.02 × |neutralTint|`, hue = primary hue (tint > 0 warm uses
  primary hue rotated toward 60°, tint < 0 cool toward 250°).
- Ramp curve presets multiply all chroma factors: Balanced 1.0, Vivid 1.15, Muted 0.75.
- Dark-mode ramps are the **same** ramps (reference palette is mode-independent); dark semantics pick
  different steps (§5.5). Exception: if "Customize dark separately" is chosen, dark seeds can differ and
  produce their own ramps.

### 5.3 Contrast requirements (audited pairs) [I: WCAG 2.2 thresholds]

Contrast ratio (WCAG 2.x): `CR = (L1 + 0.05) / (L2 + 0.05)` where L = relative luminance,
`L = 0.2126 R + 0.7152 G + 0.0722 B`, channel `c = s ≤ 0.04045 ? s/12.92 : ((s+0.055)/1.055)^2.4`,
`s = 8-bit/255`. Semi-transparent colors are composited over their declared background first.

| Pair (foreground on background) | Threshold | Basis | Severity if failing (public surfaces) |
|---|---|---|---|
| `fg.default` on `bg.canvas`, `bg.surface`, `bg.surface-raised` | 4.5:1 (target 7:1) | 1.4.3 normal text | **Blocking** |
| `fg.muted` on canvas/surface | 4.5:1 | 1.4.3 (muted text is still text) | **Blocking** |
| `fg.subtle` (placeholders, timestamps ≥ 14 px) | 4.5:1 | 1.4.3; placeholders count as text [I] | Blocking |
| `fg.disabled` | none required (1.4.3 exempts inactive components) | — | Info |
| Large text (≥ 24 px, or ≥ 18.66 px bold) e.g. headings | 3:1 | 1.4.3 large-text | Blocking |
| `action.primary.fg` on `action.primary.bg` (all states: default, hover, pressed) | 4.5:1 | 1.4.3 | **Blocking** |
| `action.primary.bg` vs adjacent `bg.*` (button boundary) | 3:1 **or** button has a border meeting 3:1 | 1.4.11 non-text | Warning |
| `fg.link` on canvas/surface | 4.5:1; plus 3:1 vs surrounding body text if links not underlined | 1.4.3 + F73 practice [I] | Blocking (auto-switch to underline if 3:1 vs body fails) |
| `border.input` (form field boundary) vs surface | 3:1 | 1.4.11 | Warning |
| `border.focus` vs both adjacent colors | 3:1 | 2.4.11/2.4.13 focus appearance [I] | **Blocking** (auto double-ring fallback) |
| Status fg on status bg (`status.danger.fg` on `status.danger.bg` …) | 4.5:1 | 1.4.3 | Blocking |
| Status icon/indicator vs surface | 3:1 | 1.4.11 | Warning |
| Calendar chip text on chip bg (every worker color) | 4.5:1 | 1.4.3 | Blocking (auto on-color) |
| Text over hero image (sampled 95th-percentile worst region under the text box, with scrim) | 4.5:1 | 1.4.3 | Warning → auto-scrim suggestion |
| Logo vs header background | 3:1 recommended | 1.4.11 does **not** apply to logos [I] | Info only |
| QR foreground vs background | ≥ 7:1 and fg darker than bg | scan reliability [D] | Blocking |

APCA (WCAG 3 draft) Lc values are shown as **advisory** only (not normative today) [I].

### 5.4 Publish gating

- Any **Blocking** contrast failure on a *public* surface prevents publish unless an Owner performs a
  typed override; override logged with the failing pairs, shown on the Brand health card until fixed.
- Workspace (CRM/team app) blocking failures **cannot be overridden** (staff accessibility is a
  platform obligation) — the editor applies auto-fix or refuses that value.
- Fonts uploaded without licence attestation cannot be referenced by a release.
- Assets not in `ready` state cannot be referenced.
- Custom CSS failing guardrails (§5.7) is excluded (publish proceeds without it, with warning) — never
  blocks the rest of the release.
- Performance budget overruns: warning only (owner's choice), except hard caps (§5.15).

### 5.5 Dark-mode derivation (auto strategy) [D]

| Semantic token | Light source | Dark source |
|---|---|---|
| `bg.canvas` | neutral.50 | neutral.950 adjusted to L = 0.17 (not pure black: reduces halation and keeps elevation visible [I]) |
| `bg.surface` | white (#FFFFFF) | canvas L + 0.035 |
| `bg.surface-raised` (elevation ≥ 2) | white | canvas L + 0.035 × (1 + level × 0.5), capped L 0.32 |
| `bg.surface-sunken` | neutral.100 | canvas L − 0.02 |
| `fg.default` | neutral.950 | neutral.50 (L 0.96) |
| `fg.muted` | neutral.700 | neutral.300 |
| `border.default` | neutral.200 | neutral.800 |
| `action.primary.bg` | primary.{anchorStep} if AA with its on-color, else nearest passing step toward 700 | primary step with L in [0.62, 0.78] that passes AA with its on-color **and** ≥ 3:1 vs canvas; prefer anchor if it qualifies |
| `action.primary.fg` | white or near-black, whichever has higher CR | same rule |
| `fg.link` | primary step with CR ≥ 4.5 vs canvas, closest to anchor | primary step with L ≥ 0.70 meeting 4.5:1 vs dark canvas |
| Status colors | `status.x.bg` = x.100, `fg` = x.800, `solid` = x.600 | `bg` = x.950 at 60% mix with canvas, `fg` = x.200, `solid` = x.400 |
| Images | — | optional `image.dim` 0–15% brightness reduction (photos only, never logos) |
| Shadows | per §5.9 | alpha × 1.6, plus surface lightening (above) and optional 1 px `border.highlight` (white 6%) |

- **Saturation clamp in dark**: chroma of large background fills (`bg.*` tints, `status.*.bg`) capped at
  0.04 to avoid vibrating saturated fields on dark backgrounds [I].
- **Hue guard ranges** for status semantics (OKLCH h): danger 0–40 ∪ 340–360, warning 50–100, success
  120–170, info 200–270. Edits outside → validation error "Danger must stay in the red family".
- If "Customize dark separately": every semantic token is directly editable in the Dark column; auto
  values shown greyed as suggestions.

### 5.6 Typography rules

- **Type scale** (step n relative to base `b`, ratio `r`): `size(n) = b × r^n`, n ∈ {−2 (xs), −1 (sm),
  0 (md/body), 1 (lg), 2 (xl/h4), 3 (2xl/h3), 4 (3xl/h2), 5 (4xl/h1), 6 (5xl/display)}; rounded to 0.5 px.
- **Fluid headings**: for n ≥ 2: `clamp(size_phone, calc(A + B·vw), size_desktop)` where `size_phone`
  uses ratio `max(1.125, r − 0.075)` and `size_desktop` uses `r`; interpolation between 360 px and
  1280 px viewport; uses `rem` + `vw` sum so browser zoom still scales (WCAG 1.4.4) [I].
- **Line height**: body = setting (default 1.5); headings `lh(n) = max(1.05, lhHeading − 0.03 × (n−2))`.
- **Weights**: requested weight snapped to nearest available face (ties → heavier for headings, lighter
  for body); variable fonts use exact value within `fvar` range. Synthetic bold/italic **disabled**
  (`font-synthesis: none`) when the real face exists; allowed otherwise.
- Minimum sizes: body text ≥ 14 px public / ≥ 13 px CRM dense tables; form inputs ≥ 16 px on coarse
  pointers (prevents iOS Safari auto-zoom on focus [I]); legal/footer text ≥ 12 px.
- Text scale preference (staff 90–150 %, customer = browser zoom / Dynamic Type) multiplies `rem` root.
- Max line length: body containers `max-width: 70ch`.
- **Font budgets** (public surfaces, compressed WOFF2, first view): ≤ 2 preloaded files (body regular +
  heading weight, latin subset), ≤ 120 KB total preloaded (warning above), hard cap 400 KB per family
  for all subsets referenced on a page (excluded above → error asks to choose fewer weights).
- **Licence & embedding** (`OS/2 fsType` [I]): `0x0000` installable → web + PDF OK; `0x0002` restricted →
  **no PDF embedding** (documents use fallback stack), web use only on owner attestation; `0x0004`
  preview & print → PDF embedding OK (read-only PDF); `0x0008` editable → OK; bit `0x0100` "no subsetting"
  → PDF embeds full font (size warning) and web subsets disabled (owner warned of larger downloads);
  bit `0x0200` bitmap-only → reject for PDF.
- `font-display`: body `swap`, headings `swap` (default) or `optional` (owner choice). With metric-matched
  fallbacks (§10.8) CLS from swap is targeted < 0.01.

### 5.7 Custom CSS (decision: **yes, tier `pro`, public web surfaces only, with guardrails**) [D]

Rationale: agencies and brand-strict businesses want pixel control; letting them do it safely is a
differentiator (Fieldd shows no such feature). Risk is contained by scoping, allowlisting and
guardrail checks.

| Rule | Detail |
|---|---|
| Surfaces | Booking, account, tracking, SMS pages, embed/popup. **Never**: CRM, team app, native apps, emails, PDFs, the payment step's processor-hosted fields (05), auth/OTP pages, consent dialogs |
| Delivery | Separate stylesheet `custom.{hash}.css` loaded **after** theme CSS inside `@layer tenant` (cascade layer below `@layer guard`) |
| Size | ≤ 20 KB source, ≤ 1,500 rules |
| Selectors | Must target stable public hooks: `[data-part="…"]`, `[data-variant]`, `[data-state]`, `.pl-*` classes, element selectors **inside** a part (`[data-part="service-card"] h3`). Rejected: `*` at top level, `html`, `body` (allowed: `:root` for token vars only), `[data-part^=` guard parts (`legal-*`, `price-total`, `payment-*`, `consent-*`, `powered-by` when plan requires it), attribute selectors on `value`/`data-*` of inputs (`input[value^="a"]` — CSS keylogger pattern) |
| Properties | Allowlist (color, background-color, background-image (only `linear/radial-gradient()` or tenant asset `url()`), border*, border-radius, box-shadow, font-*, letter-spacing, line-height, text-*, padding*, margin*, gap, width/height/min/max (not on guard parts), display (not `none` on guard parts), flex/grid props, opacity (≥ 0.4 on guard parts), transform (2D), transition/animation (respecting reduced motion — we wrap all tenant animations in `@media (prefers-reduced-motion: no-preference)` automatically), filter (limited), object-fit, aspect-ratio, `--pl-*` custom properties) |
| Forbidden | `@import`, `@font-face` (use Fonts), `@namespace`, `url()` to any origin other than the tenant's asset path, `image-set()` external, `expression()`, `behavior`, `-moz-binding`, `content:` with `attr()` of sensitive attrs, `position: fixed/sticky` (overlay phishing, covering consent), `z-index` > 10, `pointer-events: none` on interactive parts, `cursor: url()`, `clip-path`/`mask` on guard parts, unicode-range tricks, `!important` on guard parts |
| Parsing | Real CSS parser → AST → allowlist validation → **re-serialize** (never pass raw text through), preserving comments stripped |
| Guardrail test | At compile, headless render of 6 canonical pages (landing, service, date/time, details, payment step, confirmation) at phone & desktop × light/dark; assert guard parts visible (in viewport flow, non-zero size, opacity ≥ 0.4, not covered by another element at center point), text contrast of guard parts ≥ 4.5:1, focus ring visible on 5 sampled controls, touch targets ≥ 44 px on phone frames. Failure → custom CSS excluded from release, owner gets the failing assertions |
| Kill switch | Owner or platform support can disable instantly (edge flag) |
| CSP | Booking pages `style-src 'self' 'nonce-…'` + asset origin; `img-src` limited to platform asset origin, processor, map tiles — so even a bypass cannot exfiltrate to arbitrary hosts |

### 5.8 Copy & locale rules

- Key format `surface.screen.element[.qualifier]`, lowercase, `[a-z0-9_.]`, ≤ 96 chars; registry
  entries declare: `kind` (`short` ≤ 60 / `label` ≤ 32 / `long` ≤ 2000 / `richText`), `placeholders`
  allowlist, `icu` (plural/select allowed), `locked` (`none` | `full` | `corePhrase:{text per locale}`),
  `maxLength`, `owner` module, `surfaces[]`, `screenshotRef` (for editor context).
- Resolution chain for viewer locale `ll-RR`: tenant override `ll-RR` → tenant override `ll` → platform
  default `ll-RR` → platform default `ll` → tenant override in business default locale → platform default
  in business default locale → `en`. Missing at all levels = build-time error (never ship a raw key).
- Customer locale detection order (public): explicit `?lang=` → customer profile locale → cookie
  `pl_lang` → `Accept-Language` best match among **enabled** locales → business default. Locale also
  drives number/currency/date formatting (`Intl`), independent of copy availability.
- Staff (CRM) copy: platform UI strings are not overridable **except** terminology keys (`crm.terms.*`:
  "Job"/"Appointment"/"Visit", "Customer"/"Client"/"Patient", "Worker"/"Technician"/"Detailer") which
  cascade through UI via ICU select/plural variants (singular, plural, article where the language needs it).
- Overrides are plain text; output is always HTML-escaped; `richText` keys allow `**bold**`, `*italic*`,
  `[text](https://…)` only.
- Placeholders are validated both at edit and at render (missing variable → render the default text for
  that key rather than leaving `{x}` visible; logs a warning).

### 5.9 Shape, depth, density, motion math

- **Radius**: base `R` (0–24). `radius.xs = round(R×0.25)`, `sm = round(R×0.5)`, `md = R`, `lg = round(R×1.5)`,
  `xl = round(R×2)`, `full = 9999px`. Component defaults: button `md` (or `full` if Pill), input `md`,
  card `lg`, dialog `xl`, chip `full`, image `md`, bottom-sheet top corners `xl`. Email buttons capped at
  `min(R, 24px)` and degrade to square in Outlook desktop (VML `arcsize` approximates) [I].
- **Elevation** levels 0–5, each a two-layer shadow (key + ambient). Base values ("Soft", intensity 100 %):

| Level | Key shadow (y, blur, spread, alpha) | Ambient (y, blur, spread, alpha) | Typical use |
|---|---|---|---|
| 0 | none | none | flat |
| 1 | 0 1 2 0 / 0.06 | 0 1 3 0 / 0.10 | cards, header on scroll |
| 2 | 0 2 4 −1 / 0.06 | 0 4 6 −1 / 0.10 | raised cards, chips hover |
| 3 | 0 4 6 −2 / 0.05 | 0 10 15 −3 / 0.10 | dropdowns, popovers, sticky summary |
| 4 | 0 10 10 −5 / 0.04 | 0 20 25 −5 / 0.10 | dialogs, toasts |
| 5 | 0 25 50 −12 / 0.25 | — | full-screen sheets over content |

  Style transforms: **Crisp** = blur × 0.5, alpha × 1.2; **Hard offset** = key shadow `x=y=level×2px`,
  blur 0, alpha 1.0 using `neutral.950` (retro look), ambient none; **None** = all levels → `0 0 0 1px
  border.default` (borders instead). Intensity multiplies alphas (0–2×). Tint: shadow color = neutral.950
  (Neutral) or primary.900 (Brand-tinted). Dark mode: alpha × 1.6 clamped ≤ 0.6, plus surface lightening.
- **Spacing**: base unit 4 px × density multiplier `d` (Compact 0.875, Comfortable 1, Spacious 1.125);
  `space.n = round(4 × n × d)` for n ∈ {0,0.5,1,1.5,2,3,4,5,6,8,10,12,16,20,24}. Gutters: phone 16 px
  (never < 12), tablet 24, desktop 32 (not density-scaled below these minimums).
- **Control heights** (`sm`/`md`/`lg`): Compact 28/36/44, Comfortable 32/40/48, Spacious 36/44/52.
  On `pointer: coarse`, interactive targets get **min 44×44 px** hit area (via padding or `::before`
  extension) regardless of density; primary actions on phone ≥ 48 px [D; WCAG 2.5.8 AA minimum is 24×24,
  2.5.5 AAA 44×44 — we target 44 I].
- **Motion**: durations `instant 0ms, fast 120ms, base 200ms, slow 320ms, slower 480ms` × speed
  setting; easings `standard cubic-bezier(0.2,0,0,1)`, `enter cubic-bezier(0,0,0,1)`, `exit
  cubic-bezier(0.3,0,1,1)`, `spring` (native only). Reduced motion (`prefers-reduced-motion: reduce` or
  setting): durations → 0 for movement/parallax/auto-scroll; opacity fades allowed ≤ 120 ms; no
  auto-playing carousels/videos; skeleton shimmer becomes static.

### 5.10 Breakpoints & responsive rules

| Name | Min width (CSS px) | Typical device | Layout columns | Gutter |
|---|---|---|---|---|
| `xs` | 0 | small/standard phones portrait (320–479) | 4 | 16 |
| `sm` | 480 | large phones, phones landscape (480–767) | 4–8 | 16 |
| `md` | 768 | tablets portrait (iPad mini 744 falls in `sm` upper edge → treated as `md` by container queries; iPad 820/834) | 8 | 24 |
| `lg` | 1024 | tablets landscape (1024–1194), small laptops | 12 | 24 |
| `xl` | 1280 | laptops, iPad Pro 12.9" landscape (1366) | 12 | 32 |
| `2xl` | 1536 | desktops | 12 (max content 1440) | 32 |

- Breakpoints apply to **viewport**-level layout (shell, nav); **components use container queries**
  (`@container`) so a booking widget in a 400 px embed column or an iPad Slide Over window lays out as
  "phone" correctly [D].
- Input modality via `@media (pointer: coarse)` / `(hover: none)` — never infer touch from width.
- Must reflow to 320 px wide without horizontal scrolling (WCAG 1.4.10) except data tables/calendar
  grids, which scroll in their own region with sticky headers [I].
- Orientation: never locked on web; native apps support both orientations on tablets (phones: portrait
  lock allowed for team app camera flows only).
- Safe areas: `env(safe-area-inset-*)` applied to sticky headers/footers/bottom sheets; `viewport-fit=cover`.
- Virtual keyboard: sticky footers hide or move above keyboard (`interactive-widget=resizes-content`
  viewport meta / VisualViewport API) so "Continue" isn't covered [I].
- Hover-only affordances forbidden: anything revealed on hover must be reachable by focus and tap.

### 5.11 Accessibility rules (platform-wide, all surfaces)

Target conformance **WCAG 2.2 AA** on public and workspace surfaces [D]. Key rules:
- Keyboard: all functionality operable; visible focus (token ring, ≥ 2 px, 3:1); focus not obscured by
  sticky headers/footers (`scroll-padding` tokens = header/footer heights; 2.4.11 [I]); logical order;
  skip link "Skip to content"; dialogs trap focus and restore on close; Esc closes.
- Screen readers: landmarks (`header`, `nav`, `main`, `footer`), headings hierarchy, labelled controls,
  `aria-live` for async messages (slot taken, saved), price totals announced on change (polite, debounced),
  calendar grid uses `role="grid"` with roving tabindex; icons decorative `aria-hidden` unless standalone
  (then `aria-label` from copy key).
- Color is never the only signal (status = color + icon + text).
- `prefers-reduced-motion`, `prefers-contrast: more` (switches to high-contrast token set),
  `forced-colors: active` (Windows High Contrast — we rely on system colors, keep borders on controls,
  icons use `currentColor`) [I].
- Text spacing override tolerance (1.4.12): layouts must not clip when line-height 1.5, paragraph
  spacing 2×, letter-spacing 0.12 em, word-spacing 0.16 em are applied — no fixed-height text boxes.
- Target size ≥ 24×24 everywhere (2.5.8) and ≥ 44×44 on touch (our bar).
- Drag-and-drop (calendar) always has a non-drag alternative (2.5.7 [I]): "Move to…" menu.
- Language of page `lang` attribute = resolved locale; `dir` for RTL.
- Time limits (holds, OTP) announce remaining time and allow extension where possible (2.2.1).

### 5.12 Documents & PDF rules

- PDF always light palette; dark HTML view must not leak into PDF (renderer forces `data-mode="light"`
  and `@media print` rules).
- Paper: Letter for US/CA/MX/PH, A4 elsewhere (business country default; overridable) [I].
- Colors: large fills limited to header bar ≤ 15% page area when "Print-friendly" on; text on accent bars
  must pass 4.5:1; prints remain legible in grayscale (audit converts to grayscale and re-checks contrast
  of header text) [D].
- Fonts embedded as subsets when `fsType` allows (§5.6); otherwise fallback stack; never Type 3 fonts.
- Logos: SVG → vector in PDF; raster → ≥ 300 dpi at printed size (warn if source too small).
- Tagged PDF for accessibility where the renderer supports it; document `lang` and title metadata set
  (title = "Invoice INV-1042 — {business}") [I].
- Determinism: same document version + same release + same renderer version → byte-stable PDF except
  timestamps (allows content-hash dedupe; 04 stores hash) [D].

### 5.13 Native apps — what updates over the air vs. needs a build

| Change | Customer app / branded team app | Platform team app & desktop app |
|---|---|---|
| Colors, palettes, semantic tokens, radius, shadows, density, motion | OTA (theme bundle) | OTA |
| Fonts | OTA (downloaded, registered at runtime; cached; fallback to system until ready) | OTA |
| Copy overrides | OTA | OTA |
| In-app logos, icon mappings, custom SVG icons | OTA | OTA |
| App icon, app name, splash/launch screen, bundle id, permissions, deep-link domains | **New store build** | n/a (platform-branded) |
| Alternate app icons (iOS supports user-selectable alternates bundled in the binary) | build | n/a |

iOS launch screens are static storyboards baked into the binary; therefore splash color/logo require a
build; we render splash after launch from OTA tokens for subsequent "loading" states [I].

### 5.14 Versioning & retention

- Release versions are integers per tenant, monotonic, never reused.
- Retention: all releases' **snapshots** kept (small JSON); compiled **artifacts** kept for: live, last
  20 releases, all pinned/named, any release referenced by a scheduled publish, and any referenced by
  stored documents (PDFs keep their own embedded copies so this is only for HTML re-display). Older
  artifacts can be recompiled from snapshots on demand.
- `compilerVersion` stored on each release; if the platform compiler changes, the **live** release is
  recompiled in the background ("platform refresh") producing new hashes but **same version number**
  (artifact manifest version increments, e.g. `v12.c3`). Visual changes from platform refreshes are
  announced in release notes; tenants can't opt out of security/compat refreshes.
- Draft history: undo/redo per session (100 steps); server keeps 30 days of draft revision ops.

### 5.15 Performance budgets (public surfaces, mid-range phone, 4G) [D, aligned with BRIEF]

| Item | Budget (compressed) | Enforcement |
|---|---|---|
| Inline critical theme CSS (`:root` vars both modes + mode switch rules) | ≤ 4 KB | hard (compiler minifies, drops unused component tokens per surface) |
| Theme stylesheet per surface (component styles referencing vars) | ≤ 12 KB (platform-owned, shared across tenants, long-cached) | CI budget |
| Tenant-specific CSS (vars + font-face + custom CSS) | ≤ 8 KB excl. custom CSS; custom ≤ 20 KB source | hard |
| Preloaded fonts | ≤ 2 files, ≤ 120 KB | warning |
| Header logo | ≤ 15 KB (SVG optimized or AVIF/WebP) | warning |
| Hero image (phone) | ≤ 120 KB AVIF/WebP at 2× | auto-generated |
| Icon sprite (per surface, used icons only) | ≤ 10 KB | auto (tree-shaken) |
| CLS attributable to theme/fonts/logos | < 0.02 | measured in synthetic tests |
| Theme bundle for native apps | ≤ 25 KB JSON (gz) excl. fonts | hard |

### 5.16 Caching & cache-busting rules

- All artifacts are content-addressed: URL contains hash → `Cache-Control: public, max-age=31536000,
  immutable` [I].
- HTML is cached at the edge per `(host, path, locale)` — mode is **not** in the cache key: both
  palettes ship inline, so HTML is mode-independent except the initial `data-mode` attribute on
  `<html>`, which an edge function rewrites from the `pl_mode` cookie (tiny streaming rewrite). This
  avoids cache fragmentation while still server-rendering the right mode [D].
- Edge pointer `tenant:{id}:brand` (version + manifest) has TTL 60 s as safety net; publish writes it
  synchronously and purges HTML by tag; stale-while-revalidate ≤ 30 s.
- Native: `GET /app/theme` returns `ETag = manifestHash`; app sends `If-None-Match`; 304 is cheap.
- Email: images referenced by immutable URLs, so old emails keep showing the old logo (correct — the
  email was sent with that brand). Retention of email-referenced logo artifacts ≥ 2 years.
- Embed loader (`embed/v1.js`, 01) is platform-wide and cached long; tenant theme loads inside the iframe.

### 5.17 Plan gating (proposal; final pricing is an owner decision) [D]

Plan codes per [S: 09]: `solo`, `starter`, `team`, `pro`.

| Capability | solo | starter | team | pro |
|---|---|---|---|---|
| Full light **and** dark palettes, auto-derivation, contrast fixer | ✓ | ✓ | ✓ | ✓ |
| Google Fonts picker, separate heading/body, type scale | ✓ | ✓ | ✓ | ✓ |
| Custom font upload | ✓ (1 family) | ✓ (2 families) | ✓ (4) | ✓ (10) |
| Logos with light/dark variants, all placements, favicon/app icon generation | ✓ | ✓ | ✓ | ✓ |
| Icon set choice + custom SVG icons | ✓ (25 custom) | ✓ (100) | ✓ (300) | ✓ (1,000) |
| Shadows / radius / density / motion | ✓ | ✓ | ✓ | ✓ |
| Copy overrides (default locale) | ✓ | ✓ | ✓ | ✓ |
| Additional locales | 1 extra | 2 | 5 | unlimited |
| Versions & rollback | last 5 | last 20 | all | all + named |
| Scheduled publish | — | ✓ | ✓ | ✓ |
| Share-preview links | — | ✓ | ✓ | ✓ |
| CRM workspace full brand theme (beyond logo + accent) | — | ✓ | ✓ | ✓ |
| Remove "Powered by" badge | — | — | ✓ | ✓ (Fieldd: Pro only [S: llms]) |
| Branded customer app | — | — | ✓ (add-on cost covers build/store ops) | ✓ (Fieldd: Scale [S: llms]) |
| Branded team app | — | — | — | ✓ (Fieldd: Pro [S: llms]) |
| Custom CSS | — | — | — | ✓ |
| Hub brand templates | — | — | — | ✓ |

The owner's requirement "every business must be able to…" [S: BRIEF] is honoured: all items the owner
listed are on every tier (with only quantity limits).

### 5.18 Logo & image rules

- Accepted: SVG (≤ 512 KB), PNG/WebP/JPEG (≤ 10 MB, ≤ 8,000 × 8,000 px, ≤ 40 MP decoded — decompression-
  bomb guard), HEIC accepted → converted (phone uploads). GIF/animated rejected for logos.
- Placement sizing is **bounded by max-height and max-width**; final rendered size = fit-inside; `width`
  and `height` attributes always emitted (aspect ratio known) → zero layout shift.
- Optical sizing default (`Apply suggested sizes`): `h = clamp(hMin, hBase × (aspect)^-0.35, hMax)`
  so a 6:1 wordmark renders shorter than a 1:1 mark with similar visual weight [D, heuristic].
- Favicon/app icon come from a **mark**: if only a wide logo exists we do **not** auto-crop it; we use a
  monogram (initials on primary) and prompt the owner to upload a mark [D].
- App icon iOS: 1024×1024 PNG, no transparency (App Store requirement [I]); we composite on background
  token. Android adaptive: 108×108 dp foreground with logo inside the 66 dp safe circle [I]; monochrome
  layer for Android 13 themed icons generated from the mark silhouette.
- Favicon SVG includes `@media (prefers-color-scheme: dark)` swap when a dark mark exists [I: supported
  in Chromium/Firefox; Safari uses PNG/ICO fallback].
- Email logos are PNG (SVG unsupported in many clients [I]) at 2× width; transparent PNG + "plated" variant
  for dark-mode robustness.

---

## 6. State machines

### 6.1 BrandRelease

| State | Event | Guard | Next state | Side effects |
|---|---|---|---|---|
| — | `PublishBrandDraft` | permission; revision matches; audit has no blocking (or owner override) | `compiling` | freeze snapshot; enqueue compile jobs |
| `compiling` | all artifacts compiled | — | `ready` | manifest stored |
| `compiling` | any compile job failed after retries (3, backoff) | — | `failed` | `brand.release.failed`; notify publisher |
| `ready` | — | `scheduleAt` absent | `live` | activate (§4.9.5); previous live → `superseded`; `brand.release.published` |
| `ready` | — | `scheduleAt` present | `scheduled` | `brand.release.scheduled` |
| `scheduled` | clock ≥ scheduleAt | release still valid (assets/fonts exist; plan entitlements unchanged; else re-audit) | `live` | as above |
| `scheduled` | `CancelScheduledRelease` | permission | `cancelled` | `brand.release.schedule_cancelled` |
| `scheduled` | another release published live manually | — | `scheduled` (kept) | warn owner "Scheduled v14 will replace your live changes" |
| `live` | newer release activated | — | `superseded` | — |
| `superseded` | `RollbackBrand(toVersion = this)` | permission; artifacts restorable | (creates new release from this snapshot) | new release goes `compiling`/`ready`→`live`; `brand.release.rolled_back` |
| `superseded` | retention prune | not pinned/referenced | `superseded` (artifacts pruned; `restorable = recompile`) | — |
| `failed` | `RetryCompile` | — | `compiling` | — |

Exactly one release is `live` per tenant at any time (DB constraint: partial unique index on
`(tenantId) WHERE status = 'live'` or equivalent) [D].

### 6.2 BrandAsset (logo, image, icon, font file)

| State | Event | Next | Side effects |
|---|---|---|---|
| — | `CreateAssetUpload` | `awaiting_upload` | upload URL issued (15 min) |
| `awaiting_upload` | upload expires | `expired` | GC after 24 h |
| `awaiting_upload` | `CompleteAssetUpload` | `scanning` | — |
| `scanning` | malware/type check fail | `rejected` | `brand.asset.rejected {reason}`; file purged |
| `scanning` | pass | `processing` | sanitize/convert/analyze |
| `processing` | success | `ready` | `brand.asset.ready`; derived metadata saved |
| `processing` | failure (corrupt, too complex, limits) | `rejected` | as above |
| `ready` | `DeleteAsset` | (guard: not referenced by live/pinned/scheduled) → `deleted` | soft delete 30 d then purge |
| `ready` | `QuarantineAsset` (support/owner, e.g. abuse report, trademark complaint) | `quarantined` | live renders fallback immediately via edge flag; `brand.asset.quarantined` |
| `quarantined` | `ReleaseQuarantine` | `ready` | — |

### 6.3 FontFamily (licence + processing)

| State | Event | Guard | Next |
|---|---|---|---|
| — | first file uploaded with attestation | attestation present | `processing` |
| `processing` | all faces processed, ≥ 1 regular-ish face | — | `ready` |
| `processing` | all faces rejected | — | `rejected` |
| `ready` | licence revoked by owner ("I no longer have a licence") | — | `withdrawn` → next compile excludes; live release auto-republished with fallback within 24 h; owner warned |
| `ready` | platform takedown (DMCA/foundry complaint) | support | `takedown` → immediate edge fallback flag, auto-republish without font |

### 6.4 CustomCss

`draft_edited → validating → (valid | invalid)`; at publish `valid → guard_testing → (included |
excluded_guard_fail)`; kill switch: any → `disabled` (edge flag) → `enabled` on owner action (next
publish). Plan loss → `suspended_plan`.

### 6.5 AppBuild (branded native apps)

| State | Event | Next |
|---|---|---|
| — | `RequestAppBuild` (prereqs ok) | `queued` |
| `queued` | CI starts | `building` |
| `building` | success | `built` (artifacts: .ipa/.aab, store metadata) |
| `building` | failure | `build_failed` |
| `built` | submitted via store API with business's credentials | `submitted` |
| `submitted` | store review starts | `in_review` |
| `in_review` | approved | `approved` → `released` (auto or manual release per setting) |
| `in_review` | rejected | `rejected` (reason captured; guidance shown) |
| any | credentials invalid/revoked | `blocked_credentials` |

### 6.6 Viewer appearance (client-side)

`unset (business default) → light | dark | system`; `system` follows `prefers-color-scheme` changes live
(`matchMedia` listener) without reload; signed-in choice syncs across devices; cookie wins on device
until next sign-in sync where **most recent timestamp wins**.
