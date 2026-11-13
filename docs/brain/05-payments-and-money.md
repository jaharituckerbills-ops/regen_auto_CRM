# 05 — Payments & Money Movement

> Agent 05 · Phase 1 "CRM brain" · 2026-11-12
> Scope: every dollar from quote total to bank payout — totals math, payment options on a job,
> deposits / prepayments / pre-authorization, card on file, worker-app POS, tipping, cash/check,
> transactions screens, refunds, disputes, payouts, processor integration, double-entry ledger.
> Product name: "the platform". Our processor product is referred to as **"Platform Payments"**
> (working name; never "fielddPAY").

### Citation legend

| Tag | File |
|---|---|
| `[S: 01.txt]` | `research/transcripts/01_mP9YP49qDiI.txt` (Subscriptions) |
| `[S: 04.txt]` | `research/transcripts/04_pASG8RTrhWc.txt` (Quotes & Invoices) |
| `[S: 05.txt]` | `research/transcripts/05_0qNwovgNTqM.txt` (Online Booking Page) |
| `[S: 06.txt]` | `research/transcripts/06_0atw7gNWDzg.txt` (Services & Prices) |
| `[S: 07.txt]` | `research/transcripts/07_uD5ND6GyoYA.txt` (Add-Ons) |
| `[S: 08.txt]` | `research/transcripts/08_w3D1W6oOaNY.txt` (Jobs & Appointments) |
| `[S: 09.txt]` | `research/transcripts/09_xO4jahos3rE.txt` (CRM Tips — transactions, discounts, gift cards) |
| `[S: 11.txt]` | `research/transcripts/11_8sT9cFazZAA.txt` (Service Areas — travel fee, zone tax) |
| `[S: 14.txt]` | `research/transcripts/14_5zqqoRbV1DU.txt` (Recurring Job — recurring discount) |
| `[S: 15.txt]` | `research/transcripts/15_Dh1Ib3a4psU.txt` (Book a Job) |
| `[S: 16.txt]` / `[S: 16.desc]` | `research/transcripts/16_G43PcI4hWOk.txt` / `.description` (Payment Methods) |
| `[S: 17.txt]` / `[S: 17.desc]` | `research/transcripts/17_UezcRXW1BcE.txt` / `.description` (Transactions) |
| `[S: 19.txt]` | `research/transcripts/19_ZPf7JSlvIcw.txt` (Schedule a Job) |
| `[S: llms.txt]` | `research/site/llms.txt` (Fieldd official facts, updated 2026-10-23) |
| `[I]` | Inferred: industry convention, processor documentation knowledge, or design decision. Processor facts tagged `[I]` (fees, auth windows, API names) must be re-verified against current processor docs before build. |

---

## 1. Summary

The Payments module turns a job's priced line items into a correct, auditable amount owed, collects
that amount through whatever channel the business and customer use (online booking deposit, card on
file, worker-app point of sale, invoice link, cash, check), and tracks the money all the way to the
business's bank account. It owns: the **totals engine** (services + add-ons + travel fee − discounts,
+ zone tax, then gift card and tip as tenders/extras), the **payment plan** attached to each job
(charge at booking / after job / card on file / deposit / pre-auth), the **POS** in the worker app,
**Transactions** (worker summary, card payments, payouts, disputes, CSV export), **refunds**, and
the **processor integration** (proposed: Stripe Connect, direct charges, tokenized cards, SAQ-A
scope). Underneath sits an append-only **double-entry ledger** so every number on every screen
(worker revenue, amount due, payout contents, cash owed by a worker) is derived from balanced
journal entries rather than mutable counters. Design priorities: never double-charge, never lose a
payment on a flaky network, never let an authorization silently expire, and make every payout
explainable line by line.

### 1.1 What the research actually says (source facts)

| # | Fact | Source |
|---|---|---|
| F1 | After creating an appointment and adding a service, there is a section labeled **"Options and Payment"**. | [S: 16.txt] |
| F2 | Under **Options**: attach or create a **discount**, apply **tax**, add a **gift card**. | [S: 16.txt] |
| F3 | Under **Payment**: decide when and how to collect. Choose **charge after the job is complete** or **add a card on file**. | [S: 16.txt] |
| F4 | Once a card is added: **charge it at the time of booking** or **wait until after the job is complete**. | [S: 16.txt] |
| F5 | If "after job": worker marks the job complete in the worker app, then is taken to the **POS**. | [S: 16.txt] |
| F6 | POS options: **enter new credit card details**, **charge card on file**, or mark a **custom payment method like cash, check, or send an invoice**. | [S: 16.txt] |
| F7 | Recorded payments appear under the **Transactions** window. | [S: 16.txt] |
| F8 | Video chapters: "Open the Job Details" → "Tap 'Add Payment'" → "Select Payment Type" → "Enter Amount & Save" → "Payment Confirmation". (Implies an explicit amount field ⇒ partial payments.) | [S: 16.desc] |
| F9 | Marketing claim: "secure, contactless payments… get paid on the spot, no extra hardware required" (implies phone-based tap-to-pay). | [S: 16.desc] |
| F10 | Transactions: **Worker summary** = total revenue broken down by what each team member brought in; **default = current week**; click the dates at the top to select any week. | [S: 17.txt] |
| F11 | All electronic payments are stored separately in the **Card payments** section: payments charged on **invoices through the customer portal** and in the **worker app POS**. | [S: 17.txt] |
| F12 | fielddPAY users get **advanced insights**; **payout history** sits "right under the tabs above". | [S: 17.txt] |
| F13 | fielddPAY is marketed as **cheaper than Stripe and Square**. | [S: 17.txt] |
| F14 | Description: "access worker payment records, view card and invoice transactions, and review payouts". | [S: 17.desc] |
| F15 | Transactions tab lets you review **every payment processed**; filter by **staff member** or **payment method**. | [S: 09.txt] |
| F16 | With fielddPAY: **recent payouts, dispute management, card transaction details**; all reports **linked to their respective job**; **downloadable as CSV**. | [S: 09.txt] |
| F17 | Gift cards are listed (in the discounts area) **only if using fielddPAY**; issued manually or sold online. | [S: 09.txt] |
| F18 | Settings → Communication → Email; "right below that" is the **Payment** section to turn on/configure **deposits, prepayments, and pre-authorization**. | [S: 04.txt] |
| F19 | Single job ID from quote → appointment → invoice; invoice auto-generated when a quote is accepted and booked. | [S: 04.txt] |
| F20 | In the job editor: "add discounts, tax and a card on file for easy payment **before or after** the job is completed"; service **cost** attached to track profit. | [S: 19.txt] |
| F21 | Zone: **travel charge** = name + **fixed dollar amount**, applied to **any mobile service** booked in the zone; zone **custom tax rate**. | [S: 11.txt] |
| F22 | Recurring job discount: **fixed dollar amount or percentage off each job**. | [S: 14.txt] |
| F23 | Add-ons can be added after booking in the CRM or in the **worker app POS before the job is marked complete**; services/add-ons carry **cost** for profit tracking. | [S: 07.txt] |
| F24 | Jobs tab shows **payment status** per appointment. | [S: 08.txt] |
| F25 | Subscriptions: "process the payment and activate the subscription"; Transactions tab → **Subscriptions** → active list; dropdown → **Packages**. | [S: 01.txt] |
| F26 | fielddPAY: next-day deposits, in-app POS, tipping, card on file, gift cards, **Apple Pay**, PCI-compliant; **card-not-present rate 2.9%** (invoices, online, card on file). | [S: llms.txt] |
| F27 | "Point of sale", "invoicing and quoting", "credit cards", "gift cards" are in the cheapest (Start, $199/mo) plan. | [S: llms.txt] |
| F28 | Customer portal: buy and send gift cards; terms & conditions acceptance and cancellation policy at booking. | [S: 05.txt] |
| F29 | Booking a job (Oct 2026): **split a job or its services between team members**. | [S: llms.txt] |
| F30 | Fieldd operates in **37 countries**; founders previously ran an Australian business. | [S: llms.txt] |

Not in sources (all `[I]`): card-present rate, whether 2.9% includes a per-transaction fixed fee,
processor behind fielddPAY, whether non-fielddPAY gateways are supported (F12/F16 wording "for those
using field pay" implies **some** customers record payments without fielddPAY `[I]`), tip UI,
refund UI, dispute UI, payout schedule control, deposit amount types.

### 1.2 Key decisions in this spec (each detailed later)

1. **Integer minor units everywhere**; currency fixed per business; no FX in-app. (§5.1, §5.9)
2. **Gift cards and deposits are tenders, not discounts** — they never reduce the tax base. Discounts do. Tips are never taxed or discounted. (§5.3)
3. **Tax computed per invoice per rate, then allocated to lines** (configurable to per-line). (§5.4)
4. **Stripe Connect, direct charges, Stripe-owned loss liability and Stripe-collected fees**, embedded Connect components in our UI; application fee configurable per plan, default 0 on budget tier. (§5.12, OQ1–OQ2)
5. **Pre-auth is scheduled, not taken at booking** when the job is further out than the card's auth window; we read `capture_before` from the processor instead of hard-coding 7 days. (§10.5)
6. **One active payment attempt per job** enforced in the database + idempotency keys on every hop (client→API, API→processor, webhook→ledger). (§10.3)
7. **Append-only double-entry ledger** is the source of truth for balances; processor objects are mirrored and reconciled nightly. (§7.4, §10.9)

---

## 2. Roles

| Role | What they do in this module | Default permissions `[I]` (Agent 09 owns the RBAC matrix; these are the payment-specific permission keys we ask 09 to include) |
|---|---|---|
| **Owner** | Connects/onboards the processor account, sets payment settings (deposits, prepayment, pre-auth, tipping, methods allowed), sees all transactions, payouts, disputes; refunds without limit; exports CSV; manages bank account (via processor-hosted component). | All `payments.*` |
| **Admin** | Same as owner except processor onboarding/bank details and changing the application-fee plan. | All except `payments.account.manage` |
| **Manager** | Takes payments in CRM, issues refunds up to a configurable limit, views all transactions & payouts, responds to disputes, reconciles worker cash. | `payments.collect`, `payments.refund.limited`, `payments.view.all`, `payments.disputes.respond`, `payments.cash.reconcile`, `payments.export` |
| **Foreman** | Field lead: collects on their crew's jobs, can apply manual discount up to limit, sees crew's transactions. | `payments.collect`, `payments.discount.limited`, `payments.view.crew` |
| **Worker** | Completes job → POS: new card / tap to pay / card on file / cash / check / send invoice; adds add-ons before completion [S: 07.txt]; receives tips; sees **own** collected payments and own cash-on-hand balance. No refunds by default. | `payments.collect.assigned`, `payments.view.own` |
| **Customer** | Pays deposit/prepayment or saves card at online booking (Agent 01 UI, our payment component); pays invoice via customer portal link [S: 17.txt]; manages saved cards; tips on receipt link; disputes via their bank. | Customer-scoped tokens only |
| **System** | Schedules authorizations, captures on completion, retries failed off-session charges, processes webhooks, posts ledger entries, reconciles payouts nightly, emits events. | Service identity; every action audited |
| **Platform staff (us)** | Support/risk: view (not modify) payment records with business consent; manage application-fee plans; respond to platform-level risk (if we ever hold loss liability). | Break-glass, audited |

Permission keys we request from Agent 09: `payments.collect`, `payments.collect.assigned`,
`payments.collect.manual_card_entry`, `payments.record_offline` (cash/check/other),
`payments.refund` (with `max_refund_minor`), `payments.void`, `payments.discount.apply` (with
`max_discount_bp`), `payments.view.own|crew|all`, `payments.export`, `payments.payouts.view`,
`payments.disputes.respond`, `payments.cash.reconcile`, `payments.settings.manage`,
`payments.account.manage`.

---

## 3. Screens

Layouts are `[I]` until the owner's walkthrough video; field lists derive from F1–F30 plus processor
necessities. Money inputs everywhere: numeric keypad on mobile, currency-symbol prefix, two-decimal
mask (or currency exponent), paste-safe parsing ("$1,234.50" → 123450), never floats.

### 3.1 Job editor → "Options & Payment" panel (CRM web)

**Where:** inside New → Appointment / Quote / Invoice / Recurring job editors and the job detail
page, below the service lines [S: 16.txt][S: 19.txt]. Same component reused by Agent 04's job
editor; we own its contents.

**Layout regions `[I]`:**
1. **Line summary (read-only, live):** Services subtotal · Add-ons · Travel fee (zone name) · Discounts · Tax (rate %, zone) · Gift card · Tip · **Total** · Paid · **Amount due**. Updates within 100 ms of any line change (client-side engine, server re-validates).
2. **Options** [S: 16.txt]:
   - **Discount** — picker (search existing discount codes/promos from Agent 06) + "Create discount" inline + "Manual discount" (amount or %; requires `payments.discount.apply`). Shows scope ("applies to: Full Detail, Pet hair") and any rejection reason ("Code expired 3 Nov").
   - **Tax** — toggle "Apply tax" (default ON when the zone has a rate [S: 11.txt]); shows rate and zone; "Override rate" (admin only, reason required); "Customer is tax-exempt" displayed read-only from Agent 07 profile.
   - **Gift card** — enter code or pick from customer's cards (Agent 06 lookup); shows remaining balance; amount to apply (default = min(balance, amount due)).
3. **Payment** [S: 16.txt]:
   - Radio **"Collect after job"** (POS at completion) vs **"Card on file"**.
   - If card on file: card selector (brand icon, •••• last4, exp, "default" badge, wallet badge e.g. Apple Pay) + "Add card" (opens processor-hosted card element in a modal — SAQ-A) + sub-radio **"Charge at booking"** / **"Charge after job"** [S: 16.txt] + (if pre-auth enabled) read-only note "A hold of $X will be placed on {date}".
   - **Deposit** row (if business/service setting requires one): amount (default from settings, editable with permission), status chip (Not collected / Collected $X / Refunded).
   - **Send invoice** shortcut (delegates to Agent 04 invoice send; we attach the pay link).
4. **Payments ledger for this job:** list of payments/refunds (date, method icon, amount, tip, who collected, status chip, link to transaction detail). Actions: Add payment, Refund, Void hold.

**Fields:**

| Field | Type | Validation | Default |
|---|---|---|---|
| discount_code | string | must resolve via Agent 06 `discounts.evaluate`; case-insensitive; trimmed | none |
| manual_discount | {type: 'amount'\|'percent', value} | amount ≥ 1 minor unit, ≤ eligible subtotal; percent 0.01–100 (stored bp 1–10000); limit by role | none |
| tax_enabled | boolean | — | true if zone tax rate > 0 and customer not exempt |
| tax_rate_override_ppm | int | 0–300000 (30%); reason ≥ 5 chars | null |
| gift_card_code | string | Agent 06 validates; currency must match | none |
| gift_card_amount_minor | int | 1 ≤ x ≤ min(balance, total_before_tip − other tenders) | min(balance, due) |
| collection_mode | enum | see §7.2 | business default (§3.3) |
| payment_method_id | ref | must belong to this customer, not expired by job date (warn if exp < job date) | customer default card |
| charge_timing | 'at_booking' \| 'after_job' | — | 'after_job' |
| deposit_amount_minor | int | ≥ processor minimum (e.g., 50¢ USD `[I]`) or 0; ≤ total | from settings |

**States:** empty (no services → panel shows "Add a service to see totals", Payment radios disabled);
loading (skeleton for totals, never a blank $0.00); error (discount invalid → inline under field;
totals mismatch with server → banner "Prices changed — review" with diff); success (toast "Card
saved", "Deposit collected $X").

**Mobile (CRM responsive):** panel collapses to an accordion "Options & Payment · Due $X"; sticky
footer shows Total and primary action.

### 3.2 Add Payment sheet (CRM + worker app)

From job details → **Add Payment** → **Select payment type** → **Enter amount & save** → **Payment
confirmation** [S: 16.desc].

| Field | Type | Validation | Default |
|---|---|---|---|
| method | enum: card_new, card_present (tap/reader), card_on_file, cash, check, bank_transfer (other), gift_card, store_credit, custom label | method enabled in settings and allowed for role | last used by this user `[I]` |
| amount_minor | int | 1 ≤ amount ≤ amount_due (+ overpay only for cash with change calc) | amount_due |
| tip_minor | int | 0 ≤ tip ≤ max(amount×1.0, settings cap) — warn above 50% `[I]` | from tip screen |
| cash_tendered_minor | int (cash only) | ≥ amount + tip | — ; shows **Change due** |
| check_number | string ≤ 30 | required for check if setting on | — |
| reference / note | string ≤ 500 | — | — |
| received_by | worker ref | defaults to current user; admin can change | self |
| received_at | datetime | not in future; backdating > 7 days needs admin `[I]` | now (business TZ) |

Confirmation screen: big amount, method, last4/check#, "Receipt sent to {email/phone}" (Agent 08),
buttons "Done", "Send receipt again", "Print/Share PDF".

### 3.3 Settings → Payments (CRM web)

Location: Settings sidebar, directly below Communication → Email [S: 04.txt]. Sub-sections:

1. **Payment account** — status card for processor connection: Not connected / Onboarding incomplete (list of due requirements, deadline) / Active (charges ✓ payouts ✓) / Restricted. "Set up Platform Payments" button → embedded onboarding (processor-hosted). Bank account & payout schedule shown via embedded component. Statement descriptor (5–22 chars, Latin, no `<>\'"*`; suffix ≤ 22 incl. prefix `[I]`).
2. **Booking payments** (applies to online booking; Agent 01 renders) [S: 04.txt]:
   - Collect at booking: `none` | `card_on_file` (save card, charge later) | `deposit` | `prepayment` (full) | `preauthorization` (hold).
   - Deposit amount: fixed (minor) or percent (bp) of estimated total; min/max clamps; "refundable until N hours before" (links to cancellation policy, Agent 01/04).
   - Pre-auth: amount basis (estimated total | estimated total + X% buffer, max 15% `[I]`), lead time ("place hold N hours before job", default 24 h `[I]`), fallback if hold fails (notify customer + flag job / auto-cancel after N hours).
   - Per-service override (link to Agent 02 service editor field "Payment requirement").
3. **CRM/office defaults:** default collection mode for jobs booked in CRM (after job / card on file after job / card on file at booking).
4. **Point of sale (worker app):** methods enabled (card new, tap to pay, card on file, cash, check, send invoice, custom methods list); allow manual card entry (on/off, per role); require signature (off) `[I]`; require photo of check (off) `[I]`.
5. **Tipping:** enabled; presets (default 15/20/25 % `[I]`, up to 4 values, 0–100); "custom amount" on/off; "no tip" button label; tip base (pre-tax after discounts | total incl. tax); prompt on receipt/invoice links; distribution among split-job workers (equal | by revenue share | lead gets all).
6. **Receipts & invoices:** auto-send receipt (email/SMS) — content owned by Agent 08.
7. **Refunds:** manager refund limit; reasons list; allow refund to gift card (store credit).
8. **Surcharging / convenience fee:** OFF and hidden on budget tier (legal complexity; OQ5).
9. **Taxes (read-only summary):** zone rates come from Agent 02 [S: 11.txt]; here: prices include tax (on/off, per business, locked after first transaction), tax rounding mode (per invoice | per line), taxability of travel fee and company add-ons.

### 3.4 Worker app — Complete job → POS

Trigger: worker swipes/taps **Complete** on a job whose collection mode is `after_job` or whose
balance > 0 [S: 16.txt]. Steps:

1. **Review** — line items (editable: add add-ons/company add-ons [S: 07.txt]; quantity ±; remove only with permission), discount/gift-card chips, totals, deposit/prepaid credit shown as "Paid".
2. **Tip** (if enabled) — customer-facing screen (phone handed to customer): business logo, amount, preset % buttons with computed amounts ("20% · $52.32"), Custom, No tip. Large targets (≥ 48 dp), high contrast, screen reader labels.
3. **Pay** — method tiles: Tap to Pay (card present), Card on file (•••• 4242), New card (manual entry — processor SDK sheet), Apple Pay/Google Pay (via tap-to-pay or payment link), Cash, Check, Send invoice, Split payment.
4. **Processing** — non-dismissable spinner with "Don't close the app"; if > 15 s show "Still working… we'll never charge twice" and a "Check status" button (§10.3).
5. **Result** — success (amount, method, receipt destination, "Done") / declined (decline reason mapped to plain language + retry with another method) / requires action (customer must authenticate — show QR/send link) / offline (cash/check queued with sync badge; card methods disabled with "No connection").

Split payment: user enters first tender amount, remaining due recalculates, loop until 0.
Cash: tendered keypad, change due, "Exact" shortcut. Check: number + optional photo.
Send invoice: confirms customer contact, delegates to Agent 04 invoice send (pay link from us); job
marked complete with balance outstanding (payment_status = `invoice_sent`).

### 3.5 Transactions tab (CRM web)

Main nav item "Transactions" [S: 09.txt][S: 17.txt]. Tabs `[I]` ordering:

| Tab | Content | Source |
|---|---|---|
| **Worker summary** | Table: team member · jobs paid · payments count · gross collected · tips · refunds · net · (cash on hand). Default range **current week** in business TZ; date control at top selects any week [S: 17.txt]; we add custom range. Row click → that worker's payments list. Totals row. | [S: 17.txt] |
| **Card payments** | All electronic payments (portal invoice payments + POS + booking deposits + card on file) [S: 17.txt]: date/time · customer · job # (link) · method (brand •••• last4 / wallet) · channel · amount · tip · fee · net · status · collected by. Filters: staff, payment method [S: 09.txt], status, channel, amount range, date. | [S: 17.txt][S: 09.txt] |
| **All payments** `[I]` | Same as card payments plus cash/check/other/gift card tenders (Fieldd's "every payment processed" [S: 09.txt] may be this or the default view — verify). | [S: 09.txt] |
| **Subscriptions** | Dropdown: Active subscriptions / Packages [S: 01.txt] — **screen owned by Agent 06**, hosted under this tab; we supply the charge history column. | [S: 01.txt] |
| **Payouts** | Processor users only [S: 17.txt]: payout date · arrival date · amount · status · bank ••••last4 · # transactions; click → payout detail (§3.7). | [S: 17.txt][S: 09.txt] |
| **Disputes** | Processor users only [S: 09.txt]: opened · customer · job · amount · reason · status · evidence due (countdown) · action. | [S: 09.txt] |
| **Cash & checks** `[I]` | Per-worker cash on hand, undeposited checks, "Mark collected/deposited". | — |

Global actions: **Download CSV** (current filters) [S: 09.txt]; search by job #, customer, last4,
amount. Empty states: "No payments this week — payments taken at POS or online appear here." /
Payouts tab without processor: "Connect Platform Payments to see payouts" CTA. Loading: table
skeleton; large ranges paginate server-side (cursor, 50 rows). Mobile: cards instead of table, filters
in bottom sheet; CSV export emailed if > 10k rows.

### 3.6 Payment detail drawer ("card transaction details" [S: 09.txt])

Header: amount, status chip, method. Sections: Customer & job (links) · Breakdown (amount, tip,
tax portion, processing fee, application fee, net) · Card (brand, last4, exp, funding type, country,
wallet, 3DS result, CVC/AVS checks, risk level) · Timeline (created, authorized, captured, refunded,
disputed, included in payout X — with timestamps in business TZ) · Refunds list · Actions: Refund,
Void (if uncaptured), Resend receipt, Copy processor ID (admin).

### 3.7 Payout detail

Payout amount, arrival date, status, bank, and a **line-by-line table of every balance transaction
in the payout** (charge, refund, dispute, fee, adjustment) with job links and a computed check:
Σ(net) = payout amount. Export CSV. (Better-than-Fieldd item B3.)

### 3.8 Dispute detail

Reason, amount, deadline countdown (business TZ), status timeline, **auto-assembled evidence pack**
(job record, service description, before/after photos [S: 06.txt], checklist results, on-my-way and
arrival timestamps, signature, terms accepted at booking [S: 05.txt], receipt, message thread) with
checkboxes to include; free-text rebuttal; "Submit evidence" (one-shot, confirm dialog); "Accept
dispute".

### 3.9 Refund dialog

Amount (default full remaining refundable), "Refund tip too" toggle (separate amount), destination
(original method | gift card/store credit | cash), reason (enum + note), line allocation (optional:
which lines; default pro-rata), preview of tax reduction and fee impact ("processing fee of $X is not
returned by the processor" `[I]`), confirm requiring typed amount if > $500 `[I]`.

### 3.10 Customer-facing payment surfaces (we own the payment component; Agent 01/04 own pages)

- **Booking checkout payment step** (Agent 01 page): processor Payment Element (card, Apple Pay, Google Pay, Link/ACH where enabled) + consent text for card on file ("I authorize {Business} to charge this card for this and future services per the terms" `[I]`) + deposit/prepay/hold summary line.
- **Invoice pay page** (Agent 04 document, our pay box): amount due, tip prompt (if enabled), method, pay button with amount; success state; already-paid state; partially-paid state.
- **Customer portal → Payment methods** (Agent 01): list cards, add, set default, remove (blocked if used by active subscription or pending hold — explain why).
- **Tip-after-service link** (receipt): tip only, charges card on file or new card.

---

## 4. Workflows

Notation: **W** = writes, **E** = event emitted, **N** = notification (template owned by Agent 08),
**L** = ledger entry posted (§7.4 account codes).

### W1. Processor onboarding (owner)
1. Owner opens Settings → Payments → "Set up Platform Payments".
2. Server creates connected account (country, business type prefilled from business profile; currency = business currency) — idempotency key `acct-create:{business_id}`. **W** `PaymentAccount{status:'onboarding'}`.
3. Embedded onboarding component collects KYC, bank account. We never see bank numbers beyond last4.
4. Webhook `account.updated` → map `charges_enabled`, `payouts_enabled`, `requirements.currently_due`, `disabled_reason`. **W** PaymentAccount. **E** `payments.account.updated`; when both enabled first time **E** `payments.account.activated`; **N** "You can now take card payments".
5. Register payment-method domains for every booking hostname the business uses (platform subdomain + custom domain from Agent 01) so Apple Pay/Google Pay work on their site `[I]` (Apple Pay requires per-domain registration). Re-run whenever Agent 01 emits `booking.domain.verified`.
6. If requirements become past due → **E** `payments.account.restricted`; banner in CRM; online booking falls back to "pay later/after job" mode automatically (no broken checkout).

### W2. Online booking with payment requirement (Agent 01 ↔ 05)
1. Customer completes the booking flow; Agent 01 calls `payments.quoteTotals` (preview, no persistence) to display totals, then `payments.prepareBookingPayment({draftBookingId, customer, lines, zoneId, discountCodes, giftCards})`.
2. We compute totals (§5) and the **booking payment requirement** from settings + service overrides: `none | card_on_file | deposit | prepayment | preauthorization`, returning a `PaymentSession {id, mode, amount_minor, client_secret, expires_at}`:
   - `card_on_file` → SetupIntent (`usage: off_session`).
   - `deposit` / `prepayment` → PaymentIntent (automatic capture), `setup_future_usage: off_session` so the card is saved for the balance.
   - `preauthorization` → if job start − now ≤ hold lead window: PaymentIntent `capture_method: manual`; else SetupIntent now + **scheduled hold** (§10.5).
3. Customer submits the processor element (3DS challenge if required). Card data goes browser → processor only (SAQ-A).
4. Agent 01 calls `booking.confirm` which calls our `payments.confirmBookingPayment(paymentSessionId)` → we verify server-side the intent status is `succeeded` / `requires_capture` / SetupIntent `succeeded` **and** amount/currency/customer match the session (never trust the client).
5. Agent 01 creates the job (Agent 04) atomically with linking our `payment_id` → **W** Payment(purpose=deposit|prepayment|authorization) linked to job; StoredPaymentMethod. **L** deposit: Dr `1100 processor_clearing` / Cr `2300 customer_deposits`. **E** `payments.payment.succeeded{purpose:'deposit'}` or `payments.payment.authorized`, `payments.card.saved`. **N** booking receipt includes deposit line.
6. If the job creation fails after money moved (crash between steps): the PaymentSession has `draft_booking_id`; reconciler finds succeeded sessions without a job after 15 min → auto-refund/void + **N** to customer + alert to business (§11 E12).
7. Gift card portion: Agent 01 calls Agent 06 `giftcards.hold(code, amount, ref=paymentSessionId)` before step 3; on confirm we call `giftcards.capture(holdId)`; on abandon `giftcards.release(holdId)` (TTL 30 min).

### W3. CRM booking with Options & Payment [S: 16.txt][S: 19.txt]
1. Office user builds job (services, add-ons; Agent 03/04), zone resolves travel fee + tax (Agent 02).
2. Applies discount / tax / gift card in Options [S: 16.txt] → totals recomputed (§10.1) → **W** `PriceBreakdown` snapshot version n on the job.
3. Chooses Payment: after job | card on file (+ at booking | after job) [S: 16.txt].
4. If "Add card": `payments.createSetupSession(customerId)` → modal with processor card element (office staff typing a card the customer read over the phone = MOTO; flagged `entry_mode: 'moto'` `[I]`; consent checkbox "Customer authorized card storage verbally" required).
5. "Book now": if `at_booking` → `payments.chargeJob(jobId, {amount: amount_due, paymentMethodId, idempotencyKey})` off-session; result displayed inline; on failure job is still booked with payment_status `failed` and a banner (booking is never rolled back because of a decline `[I]`, configurable "require successful charge to book").
6. **E** `payments.job.payment_plan_set`, plus payment events.

### W4. Pre-authorization lifecycle
1. Hold scheduled at `max(now, job_start − lead_hours)` but never later than the job's expected end (§10.5).
2. Scheduler creates PaymentIntent manual-capture off-session with saved card, amount = estimate × (1 + buffer). **W** Authorization{status:'authorized', capture_before}. **L** none (holds are memo-only; no money moved). **E** `payments.authorization.placed`.
3. Failure (decline / authentication_required) → **E** `payments.authorization.failed`; **N** customer "Update your card" link + business alert; job flagged `payment_at_risk` on calendar (Agent 03 renders badge). Retry policy: after customer updates card, or 2 automatic retries at +4 h and job_start − 2 h `[I]`.
4. Job rescheduled (Agent 03 `schedule.job.rescheduled`): if new start is beyond `capture_before` − completion buffer → cancel hold, reschedule new hold. **E** `payments.authorization.rescheduled`.
5. Job completed → capture (W5). Job canceled → void hold (or capture cancellation fee portion if policy allows and consent exists, then void remainder). **E** `payments.authorization.voided`.
6. 12 h before `capture_before` and job not complete → re-authorize (new PI, then void old one; never void first) **E** `payments.authorization.renewed`.

### W5. Job completion & collection (worker app POS) [S: 16.txt]
1. Worker marks complete in worker app (Agent 04 owns the status transition) → app opens POS.
2. Worker optionally adds add-ons/company add-ons [S: 07.txt] → `payments.quoteTotals` → new PriceBreakdown version.
3. Agent 04 finalizes invoice (`job.invoice.finalized{jobId, breakdownVersion}`) → **L** revenue recognition: Dr `1200 accounts_receivable` total_before_tip; Cr `4000/4010/4020 revenue` per line class; Dr `4900 discounts` (contra) for discounts; Cr `2200 sales_tax_payable:{rate}`. If deposits exist: Dr `2300 customer_deposits` / Cr `1200 A/R`. Gift card redemption: Dr `2400 gift_card_liability` / Cr `1200 A/R`.
4. Tip screen → tip amount → **L** Dr `1200 A/R` / Cr `2100 tips_payable:{worker}` (posted at payment time with the tip).
5. Collection branch:
   - **Authorized hold exists** → capture min(authorized, due incl. tip). If due > authorized → capture authorized + create a second off-session charge for the difference (§10.6).
   - **Card on file, after job** → off-session PaymentIntent (automatic capture) for amount due + tip.
   - **Tap to pay / reader** → card-present PaymentIntent via terminal SDK; tip may be collected on-device.
   - **New card (manual)** → processor SDK card sheet → PaymentIntent on-session.
   - **Cash** → **W** Payment(method=cash, status=succeeded, custody=worker). **L** Dr `1010 cash_on_hand:{worker}` / Cr `1200 A/R`.
   - **Check** → **L** Dr `1020 undeposited_checks` / Cr `1200 A/R`.
   - **Send invoice** → payment_status `invoice_sent`; A/R remains open; Agent 04 sends; reminders per Agent 08.
6. Card success → **W** Payment(succeeded); **L** Dr `1100 processor_clearing` / Cr `1200 A/R`; fee known from balance transaction → Dr `6100 processing_fees` / Cr `1100 processor_clearing` (+ application fee Dr `6110 platform_fees`).
7. **E** `payments.payment.succeeded`, `payments.job.balance_changed{payment_status:'paid'}`, `payments.tip.added` (if tip). **N** receipt (Agent 08).
8. Revenue attribution rows written for the worker(s) on the job (Agent 09 split shares) — §5.8.

### W6. Invoice paid through customer portal [S: 17.txt]
1. Customer opens invoice link (Agent 04) → pay box loads `payments.getPayableState(invoiceToken)` (amount due, tip settings, saved cards if authenticated).
2. Creates PaymentIntent bound to `{jobId, breakdownVersion, amount_due}`; if the invoice changed since load, confirm endpoint returns 409 `AMOUNT_CHANGED` and the page refreshes the total (never charge a stale amount).
3. Success → same as W5 step 6–7, channel `customer_portal`, collected_by = null (attributed to job workers per §5.8).

### W7. Add Payment (manual, any time) [S: 16.desc]
Job details → Add Payment → method → amount → save. Partial payments allowed; payment_status
becomes `partially_paid` until due = 0. Overpayment: only cash may exceed due (change given);
otherwise blocked with "Amount exceeds balance by $X — record as credit?" (store credit, admin).

### W8. Refund
1. User with `payments.refund` opens Refund dialog (§3.9) on a payment.
2. Validate: refundable = captured − refunded − disputed amount; role limit; payment not in an open dispute (processor blocks refunds on disputed charges `[I]`).
3. Allocation: tip portion (explicit), tax portion (pro-rata to the refunded revenue, rounded half-up, final refund trues up to exact remaining tax), revenue lines (pro-rata or user-chosen).
4. Destination:
   - original card → processor refund (idempotency key `refund:{refund_id}`); application fee refunded pro-rata by default `[I]` (configurable).
   - gift card / store credit → Agent 06 `giftcards.issueOrReload` (no processor call); **L** Dr contra revenue / Cr `2400 gift_card_liability`.
   - cash → record; **L** Cr `1010 cash_on_hand:{user}`.
   - original gift-card tender → always back to the same gift card (Agent 06 `giftcards.refund`).
5. **W** Refund(pending) → webhook `charge.refunded` / `refund.updated` → succeeded/failed. **L** reversal entries: Dr `4950 refunds` (contra revenue) by class, Dr `2200 sales_tax_payable`, Dr `2100 tips_payable` (if tip refunded), Cr `1100 processor_clearing`.
6. **E** `payments.refund.created`, `payments.refund.succeeded|failed`, `payments.job.balance_changed`. **N** refund receipt.

### W9. Dispute
1. Webhook `charge.dispute.created` → **W** Dispute; **L** Dr `1300 dispute_receivable` (amount) + Dr `6200 dispute_fees` / Cr `1100 processor_clearing`. **E** `payments.dispute.opened`; **N** owner/admin urgent (email + push) with deadline.
2. Evidence auto-pack built from Agent 04 (job, photos, checklist, signature, timestamps), Agent 01 (terms acceptance record, IP, booking timestamp), Agent 08 (receipts, messages). Draft saved; submit is explicit and final.
3. `charge.dispute.closed` won → Dr `1100` / Cr `1300` (fee may or may not be returned by processor `[I]`); lost → Dr `6210 dispute_losses` / Cr `1300`; plus revenue/tax reversal per policy (we do **not** automatically reverse sales tax on lost disputes; flagged for owner/accountant, OQ9).
4. **E** `payments.dispute.updated`, `payments.dispute.closed{outcome}`.

### W10. Payouts
1. Processor pays out on its schedule (daily rolling `[I]`; Fieldd advertises next-day [S: llms.txt]).
2. Webhooks `payout.created|updated|paid|failed` on the connected account → **W** Payout; fetch balance transactions for the payout (paginated) → **W** BalanceTransaction rows with `payout_id`.
3. **L** on `payout.paid`: Dr `1050 bank_in_transit→bank` / Cr `1100 processor_clearing` (we model processor balance as the clearing account; bank is external, so we credit clearing and debit `1060 bank_external`).
4. Reconcile: Σ balance transaction nets == payout amount; mismatch → `payments.reconciliation.mismatch` alert (internal).
5. `payout.failed` (bad bank) → **E** `payments.payout.failed`; **N** owner "Update bank account".

### W11. Cash custody & reconciliation
1. Every cash payment increases `cash_on_hand:{worker}`.
2. Owner/manager opens Cash & checks tab → per worker balance with list of cash payments.
3. "Collect from worker" (amount counted) → **L** Dr `1015 cash_office` / Cr `1010 cash_on_hand:{worker}`; variance (counted − expected) → Dr/Cr `6300 cash_over_short`. **E** `payments.cash.collected`, `payments.cash.variance_detected` if ≠ 0.
4. "Mark deposited" (bank deposit slip ref) → Dr `1060 bank_external` / Cr `1015 cash_office`; checks: Dr `1060` / Cr `1020 undeposited_checks`.
5. Bounced check → payment reversed: **W** Payment status `reversed`; **L** Dr `1200 A/R` + fee / Cr `1060`; job balance reopens; **E** `payments.payment.reversed`.

### W12. Subscription & other off-session charges (Agent 06 drives)
Agent 06 calls `payments.chargeOffSession({purpose:'subscription', subscriptionId, cycleId,
customerId, amount, idempotencyKey: 'sub:{subscriptionId}:{cycleId}:{attempt}'})`. We return a
deterministic result; `authentication_required` / declines returned with machine codes for Agent 06
dunning. Gift card purchases (Agent 06 / 01) use `purpose:'gift_card_purchase'` (**L** Dr clearing /
Cr `2400 gift_card_liability` — not revenue).

### W13. Cancellation / no-show fee
Agent 04 emits `job.job.canceled{feeMinor, reason}` computed from policy [S: 05.txt]. If fee > 0 and
consent exists: capture fee from hold (partial capture) or charge card on file; else create invoice.
Deposit handling per policy: refund (full/partial) or retain (retained deposit → **L** Dr `2300` /
Cr `4030 cancellation_fee_revenue`).

### W14. Tip after capture
Tip via receipt link or worker "Add tip" after payment: new PaymentIntent (purpose `tip`) linked to
the original payment and job; card on file (customer-initiated via link ⇒ on-session) or new card.
Never modify the captured amount. Tip distribution follows §5.8.

---

## 5. Business rules & math

### 5.1 Money representation
- All amounts are **signed 64-bit integers in minor units** (`amount_minor`) plus an ISO-4217 `currency` code. Never floats, never decimal strings in storage. `[I]`
- Minor-unit exponent comes from a currency table (USD/AUD/CAD/GBP/EUR = 2, JPY/KRW = 0, KWD/BHD = 3). Display formatting via locale (`Intl.NumberFormat`-equivalent) — business locale for CRM, customer locale for customer pages, currency always the business's.
- Rates: **percent discounts** stored as basis points (`bp`, 1 bp = 0.01 %; 10000 = 100 %). **Tax rates** stored as parts-per-million of 1 (`ppm`; 8.25 % = 82 500; 8.875 % = 88 750) to support 3-decimal jurisdictions. Quantities stored as `qty_milli` (1 unit = 1000) to allow hourly/area pricing (1.5 h = 1500).
- Rounding primitive: `roundHalfUp(numerator, denominator)` on integers (half away from zero for negatives) — exact rational arithmetic, no intermediate floats. One rounding per computed quantity; never round a value twice.

### 5.2 Line classes
| Class | Source | Discountable | Taxable default | Revenue account |
|---|---|---|---|---|
| `service` | Agent 02 catalog [S: 06.txt] | yes | yes | 4000 |
| `customer_addon` | Agent 02 [S: 07.txt] | yes | yes | 4010 |
| `company_addon` | Agent 02 (labor fees, equipment charges) [S: 07.txt] | configurable (default no) `[I]` | yes | 4010 |
| `product` (add-on with inventory) | Agent 02 [S: 07.txt] | yes | yes | 4015 |
| `travel_fee` | zone fixed amount, mobile services only [S: 11.txt] | no `[I]` | configurable (default no `[I]`; jurisdiction-dependent) | 4020 |
| `custom` | free-text line in CRM | yes | yes | 4000 |
| `cancellation_fee` | policy (Agent 04) | no | configurable (default no) | 4030 |

Each line: `unit_price_minor × qty_milli / 1000` → `gross_minor` (rounded half-up once).
Unit prices may be negative only for `custom` credit lines (admin), which are treated as order-level
discounts for tax purposes.

### 5.3 Order of operations (canonical)

```
 1. gross per line            = round(unit_price × qty)
 2. line-level discounts      (scoped to specific lines; applied in fixed order:)
      a. subscription "included" (free) items        → line net = 0 for included qty   [Agent 06]
      b. subscription % benefit (e.g. 20% off services) [S: 01.txt]
      c. recurring-job discount (fixed or % per job) [S: 14.txt]
      d. promo/discount codes scoped to services/add-ons [S: 09.txt]
      each % step operates on the running net of that line (compounding, not additive);
      fixed-amount scoped discounts are allocated across their eligible lines pro-rata (§10.2).
 3. order-level discounts     (manual discount from Options [S: 16.txt], order-wide codes)
      allocated pro-rata over eligible lines' running net (largest-remainder, §10.2)
      clamp: no line net < 0; excess discount is dropped and reported (never negative totals)
 4. travel fee                added as its own line (not discounted unless rule says so)
 5. tax                       per tax rate: Σ taxable line nets → tax = roundHalfUp(base × ppm / 1e6)
                              then allocated back to lines (largest remainder) for reporting/refunds
                              (mode 'per_line' instead rounds each line and sums)
                              tax-inclusive mode: tax = round(gross_incl × ppm / (1e6 + ppm))
 6. total_before_tip          = Σ line nets + Σ tax   (tax-exclusive mode)
 7. tip                       added after tax; never taxed, never discounted; base per settings
 8. grand_total               = total_before_tip + tip
 9. tenders                   gift cards, deposits, prepayments, store credit, card, cash…
                              amount_due = grand_total − Σ succeeded tenders + Σ refunds-to-customer
                              (refunds re-open balance only when the line is not also removed/credited)
```

Rationale and flags:
- **Gift card is a tender, not a discount** `[I]`: Fieldd shows gift card in "Options" next to discount and tax [S: 16.txt], but stored-value redemption must not reduce taxable sales (tax was not collected when the card was sold; it is collected when redeemed). **Promotional** gift cards given free are still tenders for tax; their cost is marketing expense (Agent 06 ledger nuance). Verify Fieldd's behavior in video (V-list).
- **Deposit/prepayment is a tender** recorded against the job; it never changes the total.
- **Discounts before tax** (seller-funded discounts reduce the taxable base in most US states `[I]`).
- **Travel fee taxability** varies by jurisdiction; default non-taxable with a business toggle `[I]`.
- **Tip base** default = Σ line nets (after discounts, before tax) `[I]`; alternative = total_before_tip. Tip presets computed with roundHalfUp per preset.
- Mandatory "service charges" are **not tips** (taxable, revenue) — never label a mandatory fee as a tip `[I]`.

### 5.4 Tax rules
- Rate source: zone custom tax rate [S: 11.txt] (Agent 02). Job zone determined by job address (mobile) or shop (shop jobs). One combined rate per zone in v1; data model allows multiple `TaxComponent`s (state/county/city) later.
- Customer tax-exempt flag (Agent 07, business customers [S: llms.txt]) → tax 0, exemption certificate ref stored on the breakdown.
- Rate is **snapshotted** on the PriceBreakdown at finalization. A zone rate change after finalization does not alter invoices; before finalization the next recompute uses the new rate and the UI shows "Tax rate updated".
- Rounding mode default `per_invoice` (sum per rate, round once). Example: 3 lines × $3.33 at 8.25 %: per-line 27+27+27 = 81¢; per-invoice 999 × 0.0825 = 82.4175 → 82¢. Both are offered because some jurisdictions/accountants require one; default is per-invoice `[I]`.
- Tax-inclusive pricing (AU GST, UK VAT `[I]`; relevant because Fieldd operates in 37 countries [S: llms.txt]): business-level `prices_include_tax`. $110.00 incl. 10 % GST → tax = round(11000 × 100000 / 1100000) = 1000. Locked after the first finalized invoice.
- Refunds reduce tax payable pro-rata (§10.7). Gift card sales: no tax at sale (liability), tax at redemption via the job.

### 5.5 Worked example (verified with exact integer arithmetic)

Business: tax 8.25 % (82 500 ppm), per-invoice rounding, travel fee non-taxable, tip base = net
before tax.

| Line | Class | Gross | Recurring 10 % [S: 14.txt] | After line disc | Order promo $20 alloc | Net |
|---|---|---|---|---|---|---|
| L1 Full Detail | service | 18 900 | −1 890 | 17 010 | −1 276 | 15 734 |
| L2 Pet hair removal | customer_addon | 3 500 | −350 | 3 150 | −236 | 2 914 |
| L3 Ceramic spray ×2 @ 24.99 | product | 4 998 | −500 (499.8 → 500) | 4 498 | −338 | 4 160 |
| L4 Heavy soil fee | company_addon | 2 000 | — (not eligible) | 2 000 | −150 | 1 850 |
| L5 Travel fee "North zone" | travel_fee | 1 500 | — | 1 500 | — (not eligible) | 1 500 |

Promo allocation: eligible base 26 658; raw shares 1276.1648 / 236.3268 / 337.4597 / 150.0488;
floors sum 1 999; the 1 remaining cent goes to the largest fractional part (L3 .4597) → 338.

- Taxable base = 15 734 + 2 914 + 4 160 + 1 850 = **24 658**; tax = 24 658 × 0.0825 = 2034.285 → **2 034** (per-line mode also yields 1 298 + 240 + 343 + 153 = 2 034 here).
- Net subtotal = 26 158; **total_before_tip = 28 192**.
- Tip 18 % of 26 158 = 4708.44 → **4 708**; **grand_total = 32 900**.
- Tenders: gift card 5 000; deposit at booking 25 % of (total_before_tip − gift card) = 25 % × 23 192 = **5 798**; balance charged after job = 32 900 − 5 000 − 5 798 = **22 102**.
- Ledger check: Σ revenue nets (26 158) + tax (2 034) + tip (4 708) = tenders (5 000 + 5 798 + 22 102) = 32 900 ✓.

### 5.6 Deposits, prepayments, pre-authorization [S: 04.txt]
- **Deposit**: fixed or % of estimated `total_before_tip − gift_card_applied`; `max(deposit, processor_min)` unless 0; never > estimate. Applied as tender at invoice finalization. Refundability governed by cancellation policy (Agent 01/04 own the policy text; we execute).
- **Prepayment**: 100 % of estimate charged at booking. If the final total differs: increase → charge difference at completion (card on file, saved via `setup_future_usage`); decrease → automatic partial refund of the excess or store credit (setting; default refund `[I]`).
- **Pre-authorization**: hold = estimate × (1 + buffer_bp/10000), buffer default 0, max 1 500 bp `[I]`. Capture ≤ authorized. Holds are **not** ledger entries (no money moves); they are memo records with `capture_before`.
- **Card on file**: SetupIntent with off-session usage + stored consent (text version, timestamp, IP/user-agent or staff user id for MOTO).
- Precedence: service-level requirement > business booking setting > none. If a booking has several services with different requirements, the **strictest** wins (prepayment > preauth > deposit > card_on_file > none) and deposit amounts sum per service if each service defines a fixed deposit `[I]`.
- Charge-at-booking from CRM [S: 16.txt] = prepayment semantics (full estimate now).

### 5.7 Payment status (per job, derived — shown on Jobs tab [S: 08.txt])
Computed from the job's PriceBreakdown and tenders (never set by hand):

| Status | Condition |
|---|---|
| `no_charge` | grand_total = 0 |
| `unpaid` | no tenders, nothing scheduled |
| `card_on_file` | valid stored card linked, nothing charged |
| `authorized` | open hold ≥ 0, nothing captured |
| `deposit_paid` | Σ tenders > 0 and < total, all tenders purpose deposit |
| `partially_paid` | 0 < Σ tenders < total (other) |
| `invoice_sent` | amount_due > 0 and invoice sent, job completed |
| `paid` | amount_due = 0 |
| `overpaid` | amount_due < 0 (credit owed) |
| `partially_refunded` / `refunded` | refunds > 0 / refunds = captured |
| `failed` | latest attempt failed and amount_due > 0 |
| `payment_at_risk` | hold failed or card expires before job date |
| `disputed` | any open dispute (overlay flag, combined with others) |

### 5.8 Attribution (worker summary [S: 17.txt]) and tips
- Revenue attribution unit = **payment** (cash basis) for the Transactions worker summary `[I]`: each succeeded payment's revenue portion (amount − tip − tax) is split among the job's assigned workers by Agent 09's `revenue_share_bp` (split jobs [S: llms.txt]); default equal split; remainder cents to the lead worker. Refunds attributed with the same shares as the original payment (negative rows).
- "Collected by" (who ran the POS / holds the cash) is separate from attribution (who earned it).
- **Tips**: 100 % to workers, split per settings (equal | by revenue share | lead only) with largest-remainder; **L** Cr `2100 tips_payable:{worker}`; payout of tips to workers happens through payroll (Agent 09 timesheets/pay rates [S: 03.txt context]) — we expose `tipsPayable(worker, period)`; we never move money to workers in v1.
- Week boundaries: business timezone, week start day from business settings (default Monday `[I]`; verify Fieldd). Payments bucketed by `succeeded_at` in business TZ; DST-safe (use local date of the instant, not UTC date).
- Agent 08's reports may use accrual (completed-job date) — both bases are defined in §9 contracts so numbers are labeled, never silently different.

### 5.9 Multi-currency stance
- One settlement currency per business, set at onboarding = processor account default currency; immutable after first transaction.
- Customers with foreign cards are charged in business currency (network handles FX) `[I]`.
- Multi-business hub (Pro plan in Fieldd [S: llms.txt]): each business has its own currency; cross-business reports group by currency, never sum across currencies; optional "converted for display" column labeled with rate source/date (phase 2).
- Gift cards, discounts (fixed amounts), travel fees are denominated in the business currency; cross-currency redemption rejected.

### 5.10 Limits & validations
- Processor minimum charge (e.g., 50¢ USD `[I]`): amounts below cannot go to card; UI offers cash/waive. Maximum per charge configurable (default $25 000 `[I]`) to cap fraud/typo damage; above requires owner confirmation.
- Manual card entry in the worker app: on/off, per-role; per-transaction cap (default $2 000 `[I]`) because card-not-present manual entry carries highest fraud/dispute risk.
- Refund ≤ captured − refunded − disputed; tip refund ≤ tip; refund window ≤ processor limit (typically 180 days for cards `[I]`); older → store credit/cash only.
- Discount cannot make any line negative; total cannot go below 0.
- Tip ≤ 100 % of base without confirmation.

### 5.11 Fees & pricing transparency
- Processing fee per payment shown in the detail drawer and CSV (`fee_minor`, `application_fee_minor`, `net_minor`) as reported by the processor's balance transaction — never estimated after the fact; estimated only on the POS preview (labeled "est.").
- Fieldd's advertised CNP rate: 2.9 % [S: llms.txt]. Our target (OQ1): at or below the processor's public rate on the budget tier (application fee 0), with a small application fee on higher tiers or for instant payouts.

### 5.12 Processor layer (design)

**Goals:** never touch card numbers (PCI SAQ-A for web, processor SDK for mobile), let each business
be merchant of record, low platform liability, affordable, fast to build, portable later.

**Recommendation: Stripe Connect** `[I]`, direct charges on connected accounts.

| Option | How it works | Pros | Cons | Verdict |
|---|---|---|---|---|
| **Standard-style** (Stripe-owned dashboard, Stripe-collected fees, Stripe-owned loss liability) | Business has full Stripe account; we create **direct charges** on it with optional `application_fee_amount`. | Lowest platform risk (negative balances/chargeback losses are the business's with Stripe); Stripe does KYC & support; fastest to launch; business can see Stripe dashboard. | Less control over branding/payout schedule; business sees Stripe pricing directly, so "cheaper than Stripe" isn't possible unless we pay fees. | **Recommended for v1** with embedded components so users rarely leave our UI. |
| **Express-style** (Express dashboard, platform liable for losses) | Platform-controlled onboarding & payouts; platform can set pricing and pay Stripe fees. | Branded experience; platform controls pricing (can bundle/undercut using volume pricing). | Platform eats negative balances from small new businesses (chargebacks, fraud) — real cost for an affordable product; more support burden. | Phase 2 option once risk tooling exists. |
| **Custom** | Fully white-label; platform builds all UI, handles all comms. | Total control (closest to a "fielddPAY"-style branded gateway `[I]`). | Highest build, compliance and loss burden. | No. |
| **PayFac-as-a-Service** (e.g., Finix, Tilled, Rainforest, Payrix `[I]`) | We become a sub-merchant platform with our own pricing. | Margin on interchange-plus; this is plausibly how fielddPAY works `[I]`. | Underwriting, risk, volume minimums. | Re-evaluate at scale (OQ3). |

Notes:
- Charge type: **direct charges** (`Stripe-Account` header) so the business is merchant of record, its statement descriptor shows, disputes debit its balance, and **the platform never holds funds** (avoids money-transmission concerns `[I]`). Separate-charges-and-transfers is explicitly avoided.
- Customers & payment methods live **on the connected account**. Mapping `customer_id → provider_customer_id` is 1:N (merges, §11).
- Revenue for us: `application_fee_amount` per charge, computed by our fee plan (bp + fixed), possibly 0 on budget tier. Application fee on refunds: refunded pro-rata by default.
- Card present: **Stripe Terminal** with **Tap to Pay on iPhone / Android** (no extra hardware [S: 16.desc]) plus optional Bluetooth readers for businesses that want physical readers `[I]`. Card-present rates are typically lower than CNP `[I]`. Offline card-present is limited/unsupported for tap-to-pay `[I]` — POS disables card methods offline.
- Wallets: Apple Pay / Google Pay through the Payment Element on web (domain registration per booking domain — critical for customer subdomains/custom domains, Brief) and through PaymentSheet/Tap to Pay on mobile [S: llms.txt].
- ACH/bank debit (lower fee for large jobs `[I]`) — phase 2, gated by plan; settles in days, so job status must tolerate `processing`.
- Webhooks: one **Connect** endpoint (events carry `account`), signature verified (HMAC-SHA256 over `timestamp.payload`, 5-min tolerance `[I]`), raw body stored, 2xx returned after durable insert into the inbox, processing async.
- Idempotency: every POST to the processor carries `Idempotency-Key` derived from our record ids (§10.3). Processor keys expire after ~24 h `[I]`; our own dedupe tables are permanent.
- Abstraction: a `PaymentProvider` port (createSetup, createPayment, capture, cancel, refund, retrieve, listBalanceTransactions, verifyWebhook) so a PFaaS migration is possible; card portability requires processor-assisted PAN migration `[I]`.
- **PCI**: web payment fields are processor iframes (SAQ-A eligibility `[I]`, including PCI DSS v4 script-integrity expectations for the page that embeds them — CSP, SRI on our scripts, no third-party tags on the checkout step). Mobile uses processor SDK UI. We never log request bodies on payment routes; card data never in our DB, logs, analytics, session replays, or support tools. Platform completes its own service-provider assessment as advised by a QSA (OQ10).
- **SCA/3DS** (EU/UK and increasingly elsewhere `[I]`): on-session confirmations may require challenge; off-session charges rely on saved mandate and may still return `authentication_required` → customer action link (Agent 08 notification).
- **Auth windows**: online card holds are commonly valid ~7 days, some networks/types shorter `[I]`; we read `capture_before` from the charge and never assume. Extended authorizations, incremental authorization and overcapture are limited to specific card-present/MCC cases `[I]` and are optional optimizations, not dependencies.
- Payout speed: standard daily payouts; instant payouts (fee) optional `[I]`. "Next-day deposits" parity with Fieldd [S: llms.txt] depends on processor account eligibility (OQ4).

---

## 6. State machines

### 6.1 Payment (one tender; card payments mirror a processor PaymentIntent)

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `createPayment` | job lock acquired; no other active attempt for (job, purpose); amount ≤ due | `created` | W Payment; idempotency record |
| `created` | provider intent created | — | `requires_payment_method` / `requires_confirmation` | store `provider_intent_id` **before** returning to client |
| `requires_*` | client/off-session confirm | — | `processing` | — |
| `processing` | provider `requires_action` | — | `requires_action` | E `payments.payment.requires_action`; N customer auth link (off-session) |
| `requires_action` | customer completes 3DS | — | `processing` | — |
| `processing` | `succeeded` (auto capture) | amount/currency match | `succeeded` | L clearing/A/R; E `payments.payment.succeeded`; balance recompute |
| `processing` | `requires_capture` | manual capture | `authorized` | W Authorization{capture_before}; E `payments.payment.authorized` |
| `authorized` | capture(amount ≤ authorized) | job completed or fee policy | `succeeded` | L as above for captured amount; remainder released |
| `authorized` | cancel / expiry (`capture_before` passed) | — | `canceled` / `expired` | E `payments.authorization.voided`/`expired`; release |
| `processing` | `payment_failed` | — | `failed` | E `payments.payment.failed{decline_code}`; release lock |
| any non-terminal | request timeout / network error | — | `unknown` | reconcile job queries provider by `provider_intent_id` within 30 s, then every 5 min ×12 |
| `unknown` | provider retrieve | — | actual state | apply transition; never create a new intent while `unknown` |
| `succeeded` | refund partial | Σrefunds < captured | `partially_refunded` | see 6.3 |
| `succeeded`/`partially_refunded` | refund to remaining 0 | — | `refunded` | — |
| `succeeded` | dispute opened | — | (flag `disputed=true`, state unchanged) | 6.4 |
| `succeeded` (check/cash) | bounced/reversed | admin | `reversed` | L reversal; E `payments.payment.reversed` |
| `created` (offline cash/check) | synced | client_uuid unique | `succeeded` | — |

Terminal: `failed`, `canceled`, `expired`, `refunded`, `reversed`. Transitions are **monotonic** by a
precedence lattice (§10.4) so out-of-order webhooks cannot move a payment backwards.

### 6.2 Authorization (hold) schedule

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| `planned` | scheduler tick at `hold_at` | job still scheduled; card valid | `placing` | create manual-capture intent |
| `placing` | provider requires_capture | — | `held` | store `capture_before`; schedule renewal at `capture_before − 12h` |
| `placing` | decline / auth required | retries < 2 | `failed_retrying` | N customer + business; flag `payment_at_risk` |
| `failed_retrying` | customer updates card / retry time | — | `placing` | — |
| `failed_retrying` | retries exhausted | — | `failed` | E `payments.authorization.failed`; policy: keep job flagged or auto-cancel (setting) |
| `held` | renewal time & job not complete | — | `renewing` | create new hold first |
| `renewing` | new hold ok | — | `held` (new) | cancel old hold; E `payments.authorization.renewed` |
| `renewing` | new hold fails | — | `held` (old, until expiry) → `failed_retrying` | N |
| `held` | job rescheduled outside window | — | `planned` | cancel hold; recompute `hold_at` |
| `held` | job completed | — | `capturing` | capture (W5) |
| `held` | job canceled | fee > 0 & consent | `capturing` (fee) | partial capture; release rest |
| `held` | job canceled, no fee | — | `voided` | cancel |
| `capturing` | captured | — | `captured` | Payment → succeeded |

### 6.3 Refund

| State | Event | Guard | Next | Side effects |
|---|---|---|---|---|
| — | `createRefund` | role limit; refundable ≥ amount; no open dispute | `pending` | W Refund; idempotency key `refund:{id}` |
| `pending` | provider `succeeded` | — | `succeeded` | L reversal; E `payments.refund.succeeded`; N |
| `pending` | provider `requires_action` (rare, some methods `[I]`) | — | `requires_action` | admin alert |
| `pending` | provider `failed` (closed card) | — | `failed` | E `payments.refund.failed`; suggest store credit / cash |
| `pending` | provider `canceled` | — | `canceled` | — |
| — (non-card destination) | create | — | `succeeded` immediately | gift card/cash ledger |

### 6.4 Dispute (mirrors processor statuses `[I]`)

| State | Event | Next | Side effects |
|---|---|---|---|
| — | `charge.dispute.created` (inquiry) | `warning_needs_response` | E `payments.dispute.opened{inquiry:true}` |
| — | `charge.dispute.created` | `needs_response` | L funds withdrawn; E; N urgent |
| `*needs_response` | evidence submitted | `under_review` / `warning_under_review` | E `payments.dispute.updated` |
| `*needs_response` | deadline passes | `lost` (eventually) | N reminder at 72/24 h before |
| `under_review` | closed won | `won` | L reinstated; E `payments.dispute.closed{outcome:'won'}` |
| `under_review` | closed lost | `lost` | L loss; E `…closed{outcome:'lost'}` |
| `warning_*` | inquiry closed | `warning_closed` | — |
| any | accept dispute | `lost` | — |

### 6.5 Payout

| State | Event | Next | Side effects |
|---|---|---|---|
| — | `payout.created` | `pending` | W Payout; fetch balance txns |
| `pending` | `payout.updated` in_transit | `in_transit` | — |
| `in_transit` | `payout.paid` | `paid` | L; reconcile Σ; E `payments.payout.paid` |
| `pending`/`in_transit` | `payout.failed` | `failed` | E `payments.payout.failed`; N owner |
| `pending` | `payout.canceled` | `canceled` | — |

### 6.6 Cash custody (per cash payment)

| State | Event | Next | Side effects |
|---|---|---|---|
| — | cash recorded by worker | `with_worker` | L Dr cash_on_hand:{worker} |
| `with_worker` | collected by office (counted) | `in_office` | L transfer + variance |
| `in_office` | marked deposited | `deposited` | L to bank_external |
| `with_worker` | refunded in cash by same worker | `refunded` | L Cr cash_on_hand |
| `with_worker` | worker deactivated (Agent 09) | (unchanged, alert) | E `payments.cash.outstanding_on_deactivation` |

### 6.7 Webhook inbox event

`received` → (signature ok) `stored` → `processing` → `processed` | `retrying` (backoff 1 m, 5 m,
30 m, 2 h, 6 h) → `dead_lettered` (alert). Duplicate `provider_event_id` → `duplicate` (2xx, no-op).
Signature failure → 400, not stored (only counter/metric).

### 6.8 Processor account

`not_connected` → `onboarding` → `active` (charges & payouts enabled) ⇄ `restricted` (requirements
past due / under review) → `rejected` | `disconnected` (business deauthorized). In `restricted`,
online booking downgrades to no-prepay modes; POS card methods disabled with explanation;
cash/check still allowed.

---

## 7. Data model

TypeScript-style, stack-agnostic. All tables carry `business_id` (tenant key, part of every unique
index and every query), `created_at`, `updated_at` (UTC instants), and `version` (optimistic
concurrency) unless noted. IDs are opaque prefixed ULIDs (`pay_…`, `rfd_…`).

### 7.1 Configuration

```ts
type MinorUnits = number;           // int64 in storage
type CurrencyCode = string;         // ISO-4217, upper-case
type Bp = number;                   // 0..10000
type Ppm = number;                  // tax rate, 0..1_000_000

interface PaymentAccount {
  id: string; business_id: string;
  provider: 'stripe' | 'manual_only';      // manual_only = cash/check/invoice without processor
  provider_account_id?: string;            // acct_…
  status: 'not_connected'|'onboarding'|'active'|'restricted'|'rejected'|'disconnected';
  charges_enabled: boolean; payouts_enabled: boolean;
  requirements_due: string[]; requirements_deadline?: string;
  country: string; default_currency: CurrencyCode;   // immutable after first payment
  statement_descriptor?: string; statement_descriptor_suffix?: string;
  payout_schedule?: { interval: 'daily'|'weekly'|'monthly'|'manual'; delay_days?: number };
  fee_plan_id: string;                     // application fee plan (OQ1)
  terminal_location_id?: string;           // card-present
  payment_method_domains: { hostname: string; status: 'pending'|'active'|'failed' }[];
}

interface FeePlan { id: string; name: string; card_bp: Bp; card_fixed_minor: MinorUnits;
  card_present_bp: Bp; card_present_fixed_minor: MinorUnits; ach_bp: Bp; ach_cap_minor?: MinorUnits; }

type BookingRequirement = 'none'|'card_on_file'|'deposit'|'prepayment'|'preauthorization';

interface PaymentSettings {
  business_id: string;
  booking_requirement: BookingRequirement;
  deposit?: { kind: 'fixed'|'percent'; fixed_minor?: MinorUnits; percent_bp?: Bp;
              min_minor?: MinorUnits; max_minor?: MinorUnits };
  preauth?: { buffer_bp: Bp; lead_hours: number; on_failure: 'flag'|'auto_cancel'; auto_cancel_after_hours?: number };
  crm_default_collection: CollectionMode;
  pos_methods: { card_present: boolean; card_manual: boolean; card_on_file: boolean; cash: boolean;
                 check: boolean; send_invoice: boolean; custom: { key: string; label: string }[] };
  manual_entry_cap_minor: MinorUnits;
  require_check_number: boolean;
  tipping: { enabled: boolean; presets_bp: Bp[]; allow_custom: boolean;
             base: 'net_before_tax'|'total_before_tip'; distribution: 'equal'|'revenue_share'|'lead_only';
             on_receipt_link: boolean };
  refunds: { manager_limit_minor: MinorUnits; allow_store_credit: boolean };
  prepayment_excess: 'refund'|'store_credit';
  require_successful_charge_to_book: boolean;
  tax: { prices_include_tax: boolean; rounding: 'per_invoice'|'per_line';
         travel_fee_taxable: boolean; company_addon_taxable: boolean; company_addon_discountable: boolean };
  week_start: 'monday'|'sunday';
  max_charge_minor: MinorUnits;
}

interface ServicePaymentOverride {       // stored on Agent 02's service; read by us
  service_id: string; requirement?: BookingRequirement;
  deposit?: PaymentSettings['deposit'];
}
```

### 7.2 Job pricing & payment plan

```ts
type CollectionMode = 'after_job_pos' | 'card_on_file_after_job' | 'card_on_file_at_booking' | 'invoice';

interface JobPaymentPlan {
  job_id: string;                         // Agent 04's single job ID [S: 04.txt]
  collection_mode: CollectionMode;
  payment_method_id?: string;             // StoredPaymentMethod
  booking_requirement: BookingRequirement;
  deposit_required_minor: MinorUnits;
  authorization_id?: string;
  consent_id?: string;
  payment_status: PaymentStatus;          // derived, cached (§5.7)
  balance_version: number;                // bumps on any tender/total change; used as CAS token
}

interface PriceBreakdown {                // immutable snapshot; new row per recompute
  id: string; job_id: string; version: number; currency: CurrencyCode;
  inputs_hash: string;                    // sha256 of canonical inputs → dedupe recomputes
  finalized: boolean;                     // true once invoice finalized (Agent 04)
  lines: BreakdownLine[];
  discounts: DiscountApplication[];
  tax_lines: { tax_rate_id: string; name: string; rate_ppm: Ppm; taxable_base_minor: MinorUnits;
               tax_minor: MinorUnits; inclusive: boolean }[];
  subtotal_gross_minor: MinorUnits; discount_total_minor: MinorUnits;
  subtotal_net_minor: MinorUnits; tax_total_minor: MinorUnits;
  total_before_tip_minor: MinorUnits; tip_total_minor: MinorUnits; grand_total_minor: MinorUnits;
  tax_exempt_ref?: string; rounding_mode: 'per_invoice'|'per_line';
  warnings: { code: 'DISCOUNT_CLAMPED'|'RATE_CHANGED'|'BELOW_PROCESSOR_MIN'; detail: string }[];
}

interface BreakdownLine {
  line_id: string;                        // stable id from Agent 04 job line
  class: 'service'|'customer_addon'|'company_addon'|'product'|'travel_fee'|'custom'|'cancellation_fee';
  ref_id?: string; name: string;
  unit_price_minor: MinorUnits; qty_milli: number; gross_minor: MinorUnits;
  discount_minor: MinorUnits; net_minor: MinorUnits;
  taxable: boolean; tax_rate_id?: string; tax_minor: MinorUnits;  // allocated
  cost_minor?: MinorUnits;                // service/add-on cost for profit [S: 06.txt][S: 07.txt]
  performed_by?: { worker_id: string; share_bp: Bp }[];          // Agent 09 split
}

interface DiscountApplication {
  id: string; source: 'subscription'|'recurring'|'code'|'manual'|'credit_line';
  source_ref?: string;                    // Agent 06 discount/subscription id, Agent 03 series id
  kind: 'percent'|'fixed'|'free_item'; value_bp?: Bp; value_minor?: MinorUnits;
  scope: 'lines'|'order'; eligible_line_ids: string[];
  applied_minor: MinorUnits; allocations: { line_id: string; amount_minor: MinorUnits }[];
  applied_by?: string; reason?: string;
}
```

### 7.3 Payments, methods, refunds, disputes, payouts

```ts
interface ProviderCustomer { customer_id: string; provider_customer_id: string; provider_account_id: string;
  primary: boolean; }                      // 1:N after merges

interface StoredPaymentMethod {
  id: string; customer_id: string; provider_payment_method_id: string; provider_customer_id: string;
  type: 'card'|'us_bank_account'; brand?: string; last4: string; exp_month?: number; exp_year?: number;
  funding?: 'credit'|'debit'|'prepaid'|'unknown'; country?: string; wallet?: 'apple_pay'|'google_pay'|'link';
  fingerprint: string;                    // dedupe same card saved twice
  is_default: boolean; status: 'active'|'expired'|'detached'|'requires_update';
  consent_id: string;
}

interface PaymentConsent { id: string; customer_id: string; text_version: string; channel: 'booking_site'|'crm_moto'|'portal'|'worker_app';
  captured_at: string; ip?: string; user_agent?: string; staff_user_id?: string; scope: 'off_session_job'|'subscription'|'cancellation_fee'; }

type PaymentMethodKind = 'card'|'card_present'|'wallet'|'ach'|'cash'|'check'|'gift_card'|'store_credit'|'custom';
type PaymentPurpose = 'deposit'|'prepayment'|'balance'|'tip'|'cancellation_fee'|'subscription'|'gift_card_purchase'|'other';
type PaymentChannel = 'booking_site'|'customer_portal'|'crm'|'worker_app'|'subscription_engine'|'api'|'mcp';

interface Payment {
  id: string; business_id: string; job_id?: string; invoice_id?: string; customer_id?: string;
  subscription_id?: string; related_payment_id?: string;   // tip-after-capture → original
  purpose: PaymentPurpose; method: PaymentMethodKind; channel: PaymentChannel;
  custom_method_key?: string;
  currency: CurrencyCode;
  amount_minor: MinorUnits;               // total tendered incl. tip
  tip_minor: MinorUnits;
  tax_portion_minor: MinorUnits;          // allocated at success for refunds/reports
  authorized_minor?: MinorUnits; captured_minor: MinorUnits; refunded_minor: MinorUnits; disputed_minor: MinorUnits;
  fee_minor?: MinorUnits; application_fee_minor?: MinorUnits; net_minor?: MinorUnits;
  status: 'created'|'requires_payment_method'|'requires_confirmation'|'requires_action'|'processing'
        |'authorized'|'succeeded'|'partially_refunded'|'refunded'|'failed'|'canceled'|'expired'|'reversed'|'unknown';
  disputed: boolean;
  failure?: { code: string; decline_code?: string; message_for_staff: string; message_for_customer: string };
  provider_intent_id?: string; provider_charge_id?: string; provider_balance_txn_id?: string;
  payment_method_id?: string; card_snapshot?: { brand: string; last4: string; wallet?: string; entry_mode: 'ecom'|'moto'|'tap'|'chip'|'swipe'|'manual' };
  check_number?: string; reference?: string;
  collected_by_user_id?: string;          // who ran POS / holds cash
  cash_custody_state?: 'with_worker'|'in_office'|'deposited'|'refunded';
  breakdown_version?: number; balance_version_at_create?: number;
  idempotency_key: string;                // unique per business
  client_uuid?: string;                   // offline-created records
  occurred_at: string;                    // business-meaningful time (backdating rules)
  succeeded_at?: string;
}

interface Authorization { id: string; payment_id?: string; job_id: string; state: string /* §6.2 */;
  hold_at: string; amount_minor: MinorUnits; capture_before?: string; attempt_count: number;
  replaced_by_id?: string; last_error?: string; }

interface Refund { id: string; payment_id: string; amount_minor: MinorUnits; tip_minor: MinorUnits;
  tax_minor: MinorUnits; revenue_allocations: { line_id: string; amount_minor: MinorUnits }[];
  destination: 'original'|'gift_card'|'store_credit'|'cash'; gift_card_ref?: string;
  reason: 'requested_by_customer'|'duplicate'|'fraudulent'|'service_issue'|'price_adjustment'|'cancellation'|'other';
  note?: string; status: 'pending'|'succeeded'|'failed'|'canceled'|'requires_action';
  provider_refund_id?: string; refund_application_fee: boolean; created_by: string; idempotency_key: string; }

interface Dispute { id: string; payment_id: string; provider_dispute_id: string; amount_minor: MinorUnits;
  reason: string; status: string /* §6.4 */; evidence_due_by?: string; evidence_submitted_at?: string;
  evidence_draft?: Record<string, unknown>; fee_minor?: MinorUnits; outcome?: 'won'|'lost'|'accepted'; }

interface Payout { id: string; provider_payout_id: string; amount_minor: MinorUnits; currency: CurrencyCode;
  status: 'pending'|'in_transit'|'paid'|'failed'|'canceled'; method: 'standard'|'instant';
  created_at: string; arrival_date: string; bank_last4?: string; failure_code?: string;
  reconciled: boolean; reconciliation_diff_minor?: MinorUnits; }

interface BalanceTransaction { id: string; provider_id: string; payout_id?: string;
  type: 'charge'|'refund'|'adjustment'|'application_fee'|'dispute'|'dispute_reversal'|'payout'|'stripe_fee'|'other';
  amount_minor: MinorUnits; fee_minor: MinorUnits; net_minor: MinorUnits; currency: CurrencyCode;
  source_provider_id: string; payment_id?: string; refund_id?: string; dispute_id?: string;
  available_on: string; }

interface RevenueAttribution { id: string; payment_id: string; refund_id?: string; job_id: string;
  worker_id: string; share_bp: Bp; revenue_minor: MinorUnits; tip_minor: MinorUnits; occurred_at: string; }

interface CashCollection { id: string; worker_id: string; payment_ids: string[]; expected_minor: MinorUnits;
  counted_minor: MinorUnits; variance_minor: MinorUnits; collected_by: string; deposited_at?: string; deposit_ref?: string; }

interface ProcessorEvent { id: string; provider_event_id: string /* unique */; provider_account_id?: string;
  type: string; created_at_provider: string; payload: unknown; status: 'stored'|'processing'|'processed'|'retrying'|'dead_lettered'|'duplicate';
  attempts: number; last_error?: string; }

interface IdempotencyRecord { business_id: string; key: string; route: string; request_hash: string;
  response_status?: number; response_body?: unknown; locked_until?: string; created_at: string; } // unique(business_id, key)

interface PaymentSession {              // booking-site payment step (Agent 01)
  id: string; draft_booking_id: string; mode: BookingRequirement; amount_minor: MinorUnits;
  breakdown_inputs_hash: string; provider_intent_id?: string; provider_setup_id?: string;
  gift_card_hold_ids: string[]; status: 'open'|'confirmed'|'expired'|'canceled'|'orphaned_refunded'; expires_at: string; }
```

Key indexes/constraints:
- `UNIQUE(business_id, idempotency_key)` on Payment and Refund.
- **Partial unique** `UNIQUE(job_id, purpose) WHERE status IN ('created','requires_payment_method','requires_confirmation','requires_action','processing','unknown')` — at most one in-flight attempt per job+purpose (double-charge guard).
- `UNIQUE(provider_event_id)` on ProcessorEvent; `UNIQUE(provider_intent_id)`, `UNIQUE(provider_refund_id)`, `UNIQUE(provider_payout_id)`.
- `UNIQUE(client_uuid)` per business for offline records.
- `CHECK(refunded_minor + disputed_minor <= captured_minor)`; `CHECK(tip_minor <= amount_minor)`.

### 7.4 Double-entry ledger

```ts
interface LedgerAccount {
  id: string; business_id: string; code: string;           // chart below
  type: 'asset'|'liability'|'revenue'|'contra_revenue'|'expense';
  normal_balance: 'debit'|'credit';
  subledger_key?: string;                                  // e.g. worker_id, tax_rate_id, customer_id
  currency: CurrencyCode;
}

interface JournalEntry {                                   // immutable; corrections = new reversing entry
  id: string; business_id: string;
  occurred_at: string;                                     // instant
  effective_date: string;                                  // business-local date for reporting
  source_type: 'price_finalized'|'payment'|'refund'|'dispute'|'payout'|'fee'|'cash_collection'|'gift_card'|'adjustment'|'reversal';
  source_id: string;
  idempotency_key: string;                                 // UNIQUE(business_id, idempotency_key) e.g. "pay:{id}:succeeded"
  reverses_entry_id?: string; memo?: string; posted_by: string;
  lines: JournalLine[];                                    // Σ debit == Σ credit per currency (DB-enforced)
}

interface JournalLine {
  entry_id: string; account_id: string; direction: 'debit'|'credit'; amount_minor: MinorUnits; // > 0
  currency: CurrencyCode;
  dims: { job_id?: string; customer_id?: string; worker_id?: string; payment_id?: string;
          line_class?: string; tax_rate_id?: string; discount_id?: string; payout_id?: string };
}
```

Chart of accounts (per business, created at onboarding):

| Code | Account | Type | Notes |
|---|---|---|---|
| 1010 | cash_on_hand:{worker} | asset | cash custody per worker |
| 1015 | cash_office | asset | collected, not yet banked |
| 1020 | undeposited_checks | asset | |
| 1060 | bank_external | asset | memo of money that reached the bank |
| 1100 | processor_clearing | asset | money at the processor (pending + available) |
| 1200 | accounts_receivable:{customer} | asset | open job balances |
| 1300 | dispute_receivable | asset | funds withdrawn pending dispute outcome |
| 2100 | tips_payable:{worker} | liability | owed to workers via payroll |
| 2200 | sales_tax_payable:{tax_rate} | liability | remitted by business off-platform |
| 2300 | customer_deposits | liability | deposits/prepayments before service delivered |
| 2350 | customer_credit:{customer} | liability | overpayments/store credit (non-gift-card) |
| 2400 | gift_card_liability | liability | **sub-ledger owned by Agent 06**; postings via our journal API |
| 4000 / 4010 / 4015 / 4020 / 4030 | revenue: services / add-ons / products / travel fees / cancellation fees | revenue | |
| 4900 | discounts | contra_revenue | dims.discount_id |
| 4950 | refunds | contra_revenue | |
| 6100 | processing_fees | expense | from balance transactions |
| 6110 | platform_fees | expense | application fees (our revenue) |
| 6200 / 6210 | dispute_fees / dispute_losses | expense | |
| 6300 | cash_over_short | expense | variances |
| 6400 | bad_debt | expense | write-offs of uncollectible A/R |

Invariants:
- Every entry balances; entries are never updated/deleted (DB permissions + trigger).
- `Payment.captured_minor` etc. are caches; the ledger + provider mirror are authoritative and a nightly job asserts they agree.
- Job amount_due == balance of `1200:{customer}` filtered by `dims.job_id` (+ `2300` deposits applied) — asserted on every finalize.
- Account balances per (business, account, currency) materialized incrementally; full recomputation job weekly.
- The ledger is an **operational sub-ledger**, not the business's general ledger; it exports to accounting tools (QuickBooks/Xero, phase 2) via summarized daily journals.

---

## 8. API surface

Transport-agnostic commands/queries. Every mutating command requires `Idempotency-Key` (client
UUID v4/v7); repeating a key with the same body returns the stored response; with a different body
returns `422 IDEMPOTENCY_KEY_REUSED`. Errors use machine codes. Auth: CRM session (role
permissions §2), worker-app session, customer token (scoped to one customer/invoice/booking), or
service identity (other modules).

| Name | Input | Output | Auth |
|---|---|---|---|
| `payments.quoteTotals` (query, pure) | `{lines[], zoneId?, shopId?, customerId?, discountRefs[], giftCards[{code, amount?}], tip?: {bp?|minor?}, taxOverride?, asOf}` | `PriceBreakdown` (unsaved) + `amount_due` preview | any authenticated incl. customer booking token |
| `payments.saveBreakdown` | `{jobId, inputs, expectedVersion}` | `PriceBreakdown` (versioned) | CRM `payments.collect` / service (Agent 04) |
| `payments.setJobPaymentPlan` | `{jobId, collectionMode, paymentMethodId?, chargeTiming?, depositMinor?}` | `JobPaymentPlan` | CRM collect |
| `payments.prepareBookingPayment` | `{draftBookingId, customerRef, lines, zoneId, discountRefs, giftCardHolds}` | `PaymentSession {id, mode, amount_minor, client_secret, publishable_key, provider_account_id, expires_at}` | booking token (Agent 01) |
| `payments.confirmBookingPayment` | `{paymentSessionId}` | `{status, paymentId?, paymentMethodId?, authorizationId?}` | service (Agent 01) |
| `payments.attachBookingToJob` | `{paymentSessionId, jobId}` | `{ok}` | service (Agent 01/04) |
| `payments.createSetupSession` | `{customerId, channel, consentText}` | `{client_secret, setupId}` | CRM / portal / worker |
| `payments.listPaymentMethods` | `{customerId}` | `StoredPaymentMethod[]` | CRM view / customer self |
| `payments.setDefaultPaymentMethod` / `payments.detachPaymentMethod` | `{paymentMethodId}` | ok / `409 METHOD_IN_USE{subscriptionIds, authorizationIds}` | CRM / customer self |
| `payments.chargeJob` | `{jobId, amountMinor, tipMinor, method: 'card_on_file'\|'card_manual'\|'card_present'\|'wallet', paymentMethodId?, expectedBalanceVersion}` | `Payment` (+ `client_secret` when on-session) | collect / collect.assigned |
| `payments.recordOfflinePayment` | `{jobId, method: 'cash'\|'check'\|'custom', amountMinor, tipMinor, tenderedMinor?, checkNumber?, customKey?, occurredAt, clientUuid}` | `Payment` | `payments.record_offline` |
| `payments.capture` | `{authorizationId, amountMinor}` | `Payment` | system / collect |
| `payments.voidAuthorization` | `{authorizationId, reason}` | `Authorization` | `payments.void` / system |
| `payments.chargeOffSession` | `{purpose, customerId, paymentMethodId?, amountMinor, currency, refs{subscriptionId?, cycleId?, giftCardOrderId?, jobId?}}` | `{paymentId, status, failure?}` | service (Agent 06/04) |
| `payments.addTip` | `{paymentId \| jobId, tipMinor, method}` | `Payment` (purpose tip) | collect / customer token |
| `payments.getPayableState` | `{invoiceToken}` | `{amount_due, currency, tip_settings, saved_methods?, status}` | customer token |
| `payments.payInvoice` | `{invoiceToken, amountMinor, tipMinor, expectedBalanceVersion}` | `{client_secret, paymentId}` | customer token |
| `payments.createRefund` | `{paymentId, amountMinor, tipMinor, destination, reason, note?, lineAllocations?}` | `Refund` | `payments.refund` (+limit) |
| `payments.getJobLedger` | `{jobId}` | `{breakdown, payments[], refunds[], authorization?, amount_due, payment_status}` | view |
| `payments.listTransactions` | `{range{from,to}, staffIds?, methods?, statuses?, channels?, search?, cursor, limit≤200}` | `{rows[], totals, nextCursor}` | view.own/crew/all (row-filtered) |
| `payments.workerSummary` | `{weekOf? \| range, basis:'collected'}` | `{rows[{workerId, jobs, payments, gross, tips, refunds, net, cashOnHand}], totals}` | view.all (view.own → own row) |
| `payments.exportTransactionsCsv` | same filters as list | `{downloadUrl \| jobId (async)}` | `payments.export` |
| `payments.listPayouts` / `payments.getPayout` | `{range, cursor}` / `{payoutId}` | `Payout[]` / `{payout, balanceTransactions[], reconciled}` | `payments.payouts.view` |
| `payments.listDisputes` / `payments.getDispute` | filters / id | `Dispute[]` / `{dispute, evidenceDraft, autoEvidence}` | view.all |
| `payments.saveDisputeEvidence` / `payments.submitDisputeEvidence` / `payments.acceptDispute` | `{disputeId, evidence}` | `Dispute` | `payments.disputes.respond` |
| `payments.cashOnHand` | `{workerId?}` | `{workerId, expected_minor, payments[]}`[] | view.own / cash.reconcile |
| `payments.collectCash` | `{workerId, paymentIds[], countedMinor}` | `CashCollection` | `payments.cash.reconcile` |
| `payments.markDeposited` | `{collectionIds[] \| checkPaymentIds[], depositRef, depositedAt}` | ok | cash.reconcile |
| `payments.reversePayment` | `{paymentId, reason:'nsf_check'\|'entered_in_error', feeMinor?}` | `Payment` | admin |
| `payments.account.onboardingSession` | `{}` | `{embeddedSessionSecret}` | `payments.account.manage` |
| `payments.account.status` | `{}` | `PaymentAccount` | settings view |
| `payments.settings.get` / `.update` | `PaymentSettings` (partial) | `PaymentSettings` | `payments.settings.manage` |
| `payments.terminal.connectionToken` | `{}` | `{secret}` | worker app (collect.assigned) |
| `payments.webhooks.receive` (HTTP) | raw body + signature header | 2xx / 400 | processor signature |
| `payments.ledger.post` (internal) | `JournalEntry` draft | entry | service (Agent 06 gift card postings) |
| `payments.reconcile.run` (internal) | `{accountId, date}` | report | system |

MCP exposure (Fieldd exposes CRM/Bookings MCP [S: llms.txt]): read-only queries
(`listTransactions`, `workerSummary`, `getJobLedger`, `listPayouts`) are safe to expose to the CRM
MCP; **money-moving commands** (`chargeJob`, `createRefund`, `chargeOffSession`) require an explicit
human confirmation step and are never exposed to the customer-level Bookings MCP except
`prepareBookingPayment` (which only returns a client secret for the customer to confirm) `[I]`.

---

## 9. Interfaces with other modules

Canonical event domain for this module is **`payments.`** (`payments.<entity>.<verb>`). Every
payments event carries the envelope `{event_id, occurred_at, business_id, correlation_id,
causation_id, schema_version}` and money as `{amount_minor, currency}` pairs. Events are published
via a transactional outbox (written in the same DB transaction as the state change) and delivered
at-least-once; consumers must dedupe on `event_id`.

### 9.1 PRODUCES (events)

| Event | Payload (beyond envelope) | Main consumers |
|---|---|---|
| `payments.account.updated` | `{status, charges_enabled, payouts_enabled, requirements_due[], deadline?}` | 01 (downgrade checkout), 08 (owner alert) |
| `payments.account.activated` | `{provider_account_id}` | 01, 08, 10 |
| `payments.account.restricted` | `{reason, requirements_due[]}` | 01, 04 (POS banner), 08 |
| `payments.breakdown.computed` | `{job_id, version, grand_total, tax_total, discount_total, inputs_hash}` | 04 (invoice lines), 08 |
| `payments.job.payment_plan_set` | `{job_id, collection_mode, booking_requirement, deposit_required}` | 04, 03 (badge) |
| `payments.job.balance_changed` | `{job_id, invoice_id?, grand_total, amount_paid, amount_refunded, amount_due, payment_status, balance_version}` | 04 (invoice state), 03 (calendar chip), 08 (reminders cancel), 01 (portal) |
| `payments.payment.succeeded` | `{payment_id, job_id?, invoice_id?, customer_id?, subscription_id?, purpose, method, channel, amount, tip, tax_portion, fee?, collected_by_user_id?, allocations:[{invoice_id, amount}], attributions:[{worker_id, revenue, tip}]}` | 04, 06, 08 (receipt N-P01), 09 (tips/commission), 07 (LTV) |
| `payments.payment.authorized` | `{payment_id, job_id, amount, capture_before}` | 04, 08 (N-P02) |
| `payments.payment.requires_action` | `{payment_id, job_id?, customer_id, action_url}` | 08 (customer auth link), 06 |
| `payments.payment.failed` | `{payment_id, job_id?, subscription_id?, purpose, failure:{code, decline_code, retryable}, amount}` | 04, 06 (dunning), 08 (N-P03), 03 (`payment_at_risk`) |
| `payments.payment.canceled` | `{payment_id, reason}` | 04 |
| `payments.payment.reversed` | `{payment_id, reason:'nsf_check'\|'entered_in_error', amount}` | 04, 08 |
| `payments.authorization.placed` / `.renewed` / `.rescheduled` / `.voided` / `.expired` | `{authorization_id, job_id, amount, capture_before?}` | 04, 03, 08 |
| `payments.authorization.failed` | `{authorization_id, job_id, customer_id, failure, update_card_url, retries_left}` | 03 (flag/hold release), 01, 08 |
| `payments.deposit.collected` (alias of `payment.succeeded` with purpose=deposit, emitted additionally for convenience) | `{payment_id, job_id?, payment_session_id, amount}` | 01, 04, 08 (N-P02) |
| `payments.tip.added` | `{payment_id, job_id, tip, allocations:[{worker_id, amount}]}` | 09 (payroll), 08 (leaderboard) |
| `payments.card.saved` / `.removed` / `.updated` (account updater) | `{payment_method_id, customer_id, brand, last4, exp}` | 06 (subs), 07 (profile), 01 (portal) |
| `payments.card.expiring` | `{payment_method_id, customer_id, exp, used_by:{subscription_ids[], future_job_ids[]}}` — 30 days before expiry, only if in use | 08 (N-P04), 06 |
| `payments.refund.created` / `.succeeded` / `.failed` | `{refund_id, payment_id, job_id?, amount, tip, tax, destination, reason, allocations}` | 04 (invoice netPaid), 06 (gift card refunds), 08 (N-P05), 09 (tip clawback) |
| `payments.dispute.opened` / `.updated` / `.closed` | `{dispute_id, payment_id, job_id?, subscription_id?, amount, reason, status, evidence_due_by, outcome?}` | 08 (A-06), 06 (auto-pause sub), 04 (job flag), 08 (review requests suppressed) |
| `payments.payout.paid` / `.failed` | `{payout_id, amount, arrival_date, bank_last4, failure_code?}` | 08 (A-07) |
| `payments.cash.collected` / `.variance_detected` / `.outstanding_on_deactivation` | `{worker_id, expected, counted, variance}` | 09, 08 |
| `payments.reconciliation.mismatch` (internal ops) | `{scope, expected, actual}` | platform ops |
| `payments.booking_session.orphaned` | `{payment_session_id, draft_booking_id, action:'refunded'\|'voided'}` | 01, 08 (customer + business notice) |

### 9.2 CONSUMES (events & data)

| From | Event / data | Our reaction |
|---|---|---|
| **01** Booking | `booking.appointment.created {job_id, payment_session_id?}` | attach session → job (`attachBookingToJob`); post deposit ledger |
| 01 | `booking.session.abandoned {draft_booking_id}` | cancel PaymentSession / SetupIntent, release gift-card holds |
| 01 | `booking.domain.verified {hostname}` / `booking.domain.removed` | register / unregister wallet payment-method domain |
| 01 | `booking.appointment.canceled` / `.rescheduled` (customer self-service) | same as 04/03 equivalents below |
| **02** Catalog & Coverage | `PricedLine[]` via `catalog.priceLines(job)` (service, add-ons, company add-ons, travel fee, question surcharges; `amount`, `cost`, `taxable`, `taxRatePpm`, `taxSource`, `pricesIncludeTax`) | input to §10.1; we never re-derive catalog prices |
| 02 | `coverage.tax_rate.changed {zone_id, rate_ppm, applyToJobIds?}`, `coverage.travel_charge.changed` | recompute non-finalized breakdowns for listed jobs; emit `payments.breakdown.computed` |
| **03** Scheduling | `scheduling.job.scheduled`, `scheduling.job.rescheduled {job_id, start, end}`, `scheduling.job.unscheduled` | (re)plan authorization `hold_at` (§10.5); cancel holds when unscheduled |
| 03 | `scheduling.assignment.created` / `.changed {job_id, workers:[{worker_id, share_bp, lead}]}` | attribution snapshot for future payments (past payments keep their snapshot) |
| **04** Job lifecycle | `jobs.lineitem.added` / `job.lines.changed {job_id}` | recompute breakdown (not finalized) |
| 04 | `job.job.completed {job_id, completed_by, at}` | trigger capture / auto-charge card on file (collection_mode) or nothing (POS handles) |
| 04 | `jobs.invoice.created` / `invoice.invoice.finalized {job_id, invoice_id, breakdown_version}` | freeze breakdown; post revenue recognition entries |
| 04 | `invoice.invoice.sent {invoice_id, pay_token}` | payment_status `invoice_sent` if completed |
| 04 | `job.job.cancelled {job_id, fee_minor, deposit_policy:'refund'\|'retain'\|'partial', reason}` / no-show | W13: void hold / charge fee / refund-or-retain deposit |
| 04 | `job.job.reopened` | unfreeze not allowed if paid: new invoice version via 04, delta handled as additional charge or refund |
| **06** Subs/Discounts/Gift cards | modifier stages 3–6 (`pricing.evaluate` modifiers: coverage, member %, recurring, promo/manual) returning `DiscountApplication[]` with line allocations | applied in §10.1 step 2–3 |
| 06 | `giftcards.authorize/hold(code, amount, ref)` → `{hold_id, usable}`; `giftcards.capture(hold_id)`; `giftcards.release(hold_id)`; `giftcards.refund(card, amount, ref)`; `giftcards.issueOrReload(customer, amount, ref)` | gift card tender lifecycle; our ledger entries mirror 06's gift-card ledger postings through `payments.ledger.post` |
| 06 | `payments.chargeOffSession` calls for subscription cycles (06 currently names it `payments.charge.create`; alias accepted) | W12 |
| **07** Customers | `customer.customer.merged {survivor_id, merged_id}` | remap ProviderCustomer rows (1:N), StoredPaymentMethods, A/R subledger dims; never detach PMs |
| 07 | `customer.customer.unmerged` | reverse mapping from merge log |
| 07 | `customer.customer.erased {customer_id}` (privacy) | detach PMs at processor, null PII in consents; **retain** financial records (legal retention) with pseudonymized customer ref |
| 07 | customer `tax_exempt` + certificate ref (read) | tax = 0 with exemption reference |
| **08** Comms & Reporting | none consumed for money logic; reads our events for N-P01…N-P05, A-05…A-07 | — |
| **09** Team | `team.worker.deactivated` / `team.member.deactivated {worker_id}` | block POS for that worker; raise `payments.cash.outstanding_on_deactivation` if cash on hand > 0; tips payable remain |
| 09 | permission checks (`payments.*` keys in 09 matrix rows 174–181) and refund limits | enforced at API |

### 9.3 Contracts (typed, per agent)

**Agent 01 — deposit / card / pre-auth at booking**
```ts
// 01 → 05
prepareBookingPayment(i: { draftBookingId: string; customer: { id?: string; email?: string; phone?: string; name?: string };
  lines: JobLineInput[]; zoneId?: string; shopId?: string; discountRefs: string[];
  giftCardHolds: { holdId: string; amountMinor: number }[]; jobStart: string /* ISO, business TZ offset */ })
  : { paymentSessionId: string; mode: BookingRequirement; amountMinor: number; currency: string;
      breakdown: PriceBreakdown; processor: { publishableKey: string; accountId: string; clientSecret?: string };
      expiresAt: string; consentText: string; consentTextVersion: string };
confirmBookingPayment(i: { paymentSessionId: string }) : { status: 'ok'|'requires_action'|'failed'; failure?: Failure; paymentId?: string; paymentMethodId?: string; authorizationId?: string };
attachBookingToJob(i: { paymentSessionId: string; jobId: string }): { ok: true };
// Guarantees: 05 never creates the job; 01 never computes totals; amounts shown by 01 = breakdown.grand_total from 05.
// Slot holds (03) must outlive PaymentSession.expiresAt (30 min) — 03 `scheduling.hold.extend` on requires_action.
// Theme: 01 passes tenant theme tokens {colors(light/dark), radius, font family + font file URLs, spacing}
//        → 05 maps to the processor element "appearance" config so card fields match the brand (owner req. 2026-11-13).
```

**Agent 04 — job / invoice**
```ts
// 04 → 05: saveBreakdown(jobId, inputs, expectedVersion) on every line change; finalize via invoice.invoice.finalized
// 05 → 04: payments.job.balance_changed (authoritative amount_paid / amount_due); payments.payment.succeeded.allocations[]
// Rules: invoice.amountPaid is derived from 05 allocations only (04 §987 agrees).
// Overpayment: 04 sets creditDue flag; 05 decides refund vs customer_credit (setting prepayment_excess).
// Void invoice with netPaid > 0 → 04 must call createRefund (or convert to credit) first; 05 returns 409 otherwise.
// POS: 04 owns cart editing/completion; 05 owns tender screen. Hand-off object:
interface PosHandoff { jobId: string; invoiceId: string; breakdownVersion: number; balanceVersion: number; amountDueMinor: number; allowedMethods: PaymentMethodKind[]; tipSettings: PaymentSettings['tipping'] }
```

**Agent 06 — subscriptions, discounts, gift cards**
```ts
// 05 → 06 (sync, inside quoteTotals): evaluateModifiers({ businessId, customerId, lines, codes, jobContext }) : DiscountApplication[]
//   06 owns eligibility/stacking; 05 owns arithmetic order, allocation, clamping, tax after discounts.
// 06 → 05: chargeOffSession({ purpose:'subscription'|'gift_card_purchase', ..., idempotencyKey })
//   returns { paymentId, status:'succeeded'|'requires_action'|'failed'|'processing', failure?:{code, declineCode, retryable} }
//   Final status always also arrives as payments.payment.* event; 06 dunning keys off events, not the sync reply.
// Gift cards: tender only (never reduce tax base) — both specs agree (06 §5.1 stage 9).
// Refund to gift card: 05 calls giftcards.issueOrReload / giftcards.refund; 06 posts its ledger; 05 posts revenue reversal.
// Gift card purchase: revenue = none; liability 2400; sales tax none at sale `[I]`.
```

**Agent 08 — receipts, notifications, reports**
```ts
// 05 provides variable set VS-PAYMENT / VS-PAYOUT data via event payloads + getJobLedger(jobId).
// Report metric definitions (05 is source for money metrics):
//   gross_collected = Σ succeeded payment amounts (excl. tips) in range (cash basis, succeeded_at in business TZ)
//   tips = Σ tip_minor; refunds = Σ succeeded refunds (by refund date); net_collected = gross_collected − refunds
//   fees = Σ processor fee + application fee (processor users only)
//   earned_revenue (accrual) = Σ finalized breakdown net (excl. tax, tips) by job completion date — owned by 08 for dashboards,
//   computed from 05 ledger accounts 4000–4030 minus 4900/4950.
// Every report tile must label its basis ("Collected" vs "Earned").
```

**Agent 09 — worker attribution & tips**
```ts
// 09 provides: assignment shares per job (share_bp sum = 10000, lead flag), worker status, permission matrix.
// 05 provides: tipsPayable(workerId, periodStart, periodEnd) → { tips_minor, payments[] } for payroll export;
//              revenueAttributed(workerId, range, basis) for leaderboard/commission; cashOnHand(workerId).
// Attribution snapshot is taken at payment success; later reassignment does NOT re-attribute past payments
// (manual "re-attribute" admin action posts reversing + new attribution rows).
```

**Agent 02** — consumes nothing from 05 except `payments.breakdown.computed` for profit views; 02 supplies priced lines and tax rate (02 §1341 aligns: "02 never totals").
**Agent 03** — consumes `payments.authorization.failed` (03 currently listens to `payment.authorization.failed` / `payment.deposit.failed`) to release online-booking holds; consumes `payments.job.balance_changed` for the calendar payment chip [S: 08.txt].
**Agent 07** — consumes `payments.card.*` for the profile "Payment methods" panel; LTV from `payments.payment.succeeded` minus refunds.

### 9.4 Naming reconciliation for Agent 10

Other specs use slightly different names for our events. Proposed canonical (left) and aliases seen:

| Canonical | Seen in | Note |
|---|---|---|
| `payments.payment.succeeded` | 04 ✓; 08 `payment.payment.captured`; 06 `payments.charge.succeeded` | one event; `captured` not used because cash/check are never "captured" |
| `payments.payment.failed` | 08 `payment.payment.failed`; 06 `payments.charge.failed`; 06 `subscription.payment.failed` is 06's own downstream event | |
| `payments.chargeOffSession` (command) | 06 `payments.charge.create` | |
| `payments.authorization.placed` | 08 `payment.preauth.created` | |
| `payments.deposit.collected` | 08 `payment.deposit.captured`; 03 `payment.deposit.failed` → use `payments.payment.failed{purpose:'deposit'}` | |
| `payments.authorization.failed` | 03 `payment.authorization.failed` | |
| `payments.card.expiring` | 08 `payment.card.expiring` | |
| `payments.refund.created`/`.succeeded` | 08 `payment.refund.created`; 04 `payments.refund.succeeded` ✓ | receipts should fire on `succeeded` for card refunds |
| `payments.dispute.opened` | 08 `payment.dispute.opened` | |
| `payments.payout.paid`/`.failed` | 08 `payment.payout.paid` | |
| `payments.quoteTotals` (pricing façade) | 06 `pricing.evaluate()` | single pipeline; 05 hosts the façade, 06 supplies modifier stages; either name, one implementation |

Rounding conflict: 06 §5.2 uses **half-even** for percent discounts; this spec uses **half-up**
everywhere (tax jurisdictions generally expect half-up `[I]`). Results differ only at exact .5
minor-unit ties. Proposal: half-up for tax (non-negotiable), single shared primitive for discounts —
Agent 10 picks one (OQ12).

### 9.5 API keys & integrations register (payments)

Principle: **businesses never give us their processor secret keys.** With Connect, all calls use
the platform's keys plus the connected account id. Secrets live in a managed secrets store with
KMS envelope encryption, accessed only by service identity, never in source, env files in repos,
logs, analytics, crash reports or support tooling. Separate test vs live everywhere.

| # | Service / secret | Purpose | Owner | Where stored | Exposure | Rotation |
|---|---|---|---|---|---|---|
| K1 | Stripe **live secret key** (`sk_live_…`) — replaced in production by **restricted keys** (`rk_live_…`) per service | All server-side API calls | Platform | Secrets manager; injected at runtime into payments service only | Server only | Every 90 days `[I]` + on staff departure/incident; roll with overlap (new key deployed, old key expired after verify) |
| K2 | Restricted key: payments-core (write PaymentIntents, SetupIntents, Refunds, Customers, PaymentMethods, Terminal) | money movement | Platform | Secrets manager | payments service | 90 days |
| K3 | Restricted key: read-only (balance transactions, payouts, disputes read, events) | reconciliation, reporting, webhook re-fetch | Platform | Secrets manager | reconciler/report workers | 90 days |
| K4 | Restricted key: disputes write (evidence submission) + accounts (onboarding sessions) | admin flows | Platform | Secrets manager | payments-admin service | 90 days |
| K5 | Stripe **publishable key** (`pk_live_…`) | client-side elements (booking widget, invoice pay page, apps) | Platform | Config (public) | Public by design | Rotated together with K1 |
| K6 | **Webhook signing secret** — Connect endpoint (`whsec_…`) | verify connected-account events | Platform | Secrets manager | webhook receiver | 180 days `[I]` or on suspicion; roll with overlap window, verify against both secrets during window |
| K7 | Webhook signing secret — platform-account endpoint | platform events (application fees, account) | Platform | Secrets manager | webhook receiver | as K6 |
| K8 | Stripe **Terminal connection tokens** | Tap to Pay / reader SDK auth | Platform-minted, per device session | Not stored (short-lived) | worker app memory | per session |
| K9 | Embedded Connect component **account session** client secrets | onboarding/payout/dispute components | Per business, minted per page load | Not stored | browser memory | per session (short-lived) |
| K10 | Apple Pay **domain registration** (web, via processor) | Apple Pay on platform subdomain + every custom booking domain (Agent 01) | Per business hostname (records), platform API | DB (`payment_method_domains`) | — | re-register on domain change; no secret |
| K11 | Apple **Merchant ID + payment processing certificate** (native iOS app, later phase) | in-app Apple Pay | Platform | Apple developer account; cert generated via processor CSR | — | certificate expires ~every 25 months `[I]` — calendar reminder 60 days ahead |
| K12 | Apple **Tap to Pay on iPhone entitlement**; Android tap-to-pay requirements | card-present on phones | Platform developer accounts | Apple/Google developer consoles | — | n/a (entitlement); track renewals of developer accounts |
| K13 | Google Pay merchant ID (Android app, later) | in-app Google Pay | Platform | Google Pay console | — | n/a |
| K14 | **Invoice pay-link / receipt-link token signing key** (our own, asymmetric or HMAC with `kid`) | customer tokens for `getPayableState`/`payInvoice`/tip links | Platform | Secrets manager (KMS) | server only | yearly + incident; keep old `kid` valid until longest token TTL elapses |
| K15 | **Idempotency/outbox** — no secret; **ledger DB credentials** | DB access for ledger (separate role, insert-only on journal tables) | Platform | Secrets manager / IAM auth | services | DB credential rotation per infra policy (≤ 90 days) |
| K16 | **CSV export signed-URL key** | time-limited download links | Platform | Secrets manager | server | 90 days |
| K17 | Tax engine API key (phase 2: processor tax product or Avalara/TaxJar-class `[I]`) | multi-component tax | Platform | Secrets manager | tax adapter | per vendor policy |
| K18 | Accounting integration **OAuth client id/secret** (QuickBooks Online, Xero — phase 2) | export journals | Platform | Secrets manager | integration service | per vendor policy |
| K19 | Accounting integration **per-business OAuth access/refresh tokens** | business's own books | **Per business** | DB, column-encrypted with KMS data key; revocable by business | integration service | provider-driven refresh (rolling refresh tokens `[I]`); revoke on disconnect |
| K20 | Connected account id (`acct_…`) | identifies business at processor | Per business (identifier, **not a secret**) | DB `PaymentAccount` | server | n/a |
| K21 | Customer `cus_…` / `pm_…` ids | card on file references | Per business (identifiers) | DB, tenant-scoped | server; last4/brand to UI | n/a; detached on erase |
| K22 | Future PFaaS provider keys (OQ3) | alternative processor | Platform | Secrets manager | adapter | per vendor |
| K23 | "Bring your own gateway" OAuth tokens (not in v1) | businesses keeping their existing processor | Per business | DB encrypted | adapter | provider-driven |

Not stored anywhere by us: card numbers, CVC, full bank account numbers, card-reader keys.
Monitoring: alert on any 401 from processor (rotated/revoked key), webhook signature-failure rate
spike, and keys approaching rotation date. Test-mode keys are never present in production config
(deploy check fails if a `sk_test`/`pk_test` prefix appears in live).

---

## 10. Algorithms / pseudo-code

### 10.1 computeTotals (pure, deterministic; same code on server, CRM, worker app, booking widget)

```ts
function computeTotals(inp: TotalsInput): PriceBreakdown {
  const cur = inp.currency;
  // 1. gross
  const lines = inp.pricedLines.map(l => ({ ...l,
     gross: roundHalfUp(l.unit_price_minor * l.qty_milli, 1000), net: 0, discount: 0, tax: 0 }));
  lines.forEach(l => l.net = l.gross);

  // 2. line-scoped modifiers from Agent 06, already ordered by stage (coverage → member % → recurring → scoped promo)
  for (const m of inp.modifiers.filter(m => m.scope === 'lines')) {
    const elig = lines.filter(l => m.eligible_line_ids.includes(l.line_id) && l.net > 0);
    if (m.kind === 'free_item' || m.kind === 'percent') {
      for (const l of elig) {
        const d = m.kind === 'free_item' ? l.net : roundHalfUp(l.net * m.value_bp!, 10000);
        apply(l, m, Math.min(d, l.net));               // clamp ≥ 0
      }
    } else { // fixed, spread across eligible lines
      const amt = Math.min(m.value_minor!, sum(elig, 'net'));
      allocateLargestRemainder(amt, elig.map(l => l.net)).forEach((a, i) => apply(elig[i], m, a));
    }
  }
  // 3. order-level modifiers (manual discount [S: 16.txt], order-wide codes)
  for (const m of inp.modifiers.filter(m => m.scope === 'order')) {
    const elig = lines.filter(l => isDiscountable(l, inp.settings) && l.net > 0);
    const base = sum(elig, 'net');
    const want = m.kind === 'percent' ? roundHalfUp(base * m.value_bp!, 10000) : m.value_minor!;
    const amt = Math.min(want, base);
    if (amt < want) warn('DISCOUNT_CLAMPED', want - amt);
    allocateLargestRemainder(amt, elig.map(l => l.net)).forEach((a, i) => apply(elig[i], m, a));
  }
  // 4. travel fee is already a line (class travel_fee) from Agent 02 [S: 11.txt]

  // 5. tax
  const exempt = inp.customerTaxExempt;
  const groups = groupBy(lines.filter(l => isTaxable(l, inp.settings) && !exempt), l => l.tax_rate_id);
  const taxLines = [];
  for (const [rateId, ls] of groups) {
    const ppm = rateOf(rateId, inp.taxOverridePpm);
    if (inp.settings.tax.prices_include_tax) {
      for (const l of ls) l.tax = roundHalfUp(l.net * ppm, 1_000_000 + ppm);   // embedded tax; net stays gross-inclusive
    } else if (inp.settings.tax.rounding === 'per_line') {
      for (const l of ls) l.tax = roundHalfUp(l.net * ppm, 1_000_000);
    } else {                                                                   // per_invoice (default)
      const base = sum(ls, 'net');
      const total = roundHalfUp(base * ppm, 1_000_000);
      allocateLargestRemainder(total, ls.map(l => l.net)).forEach((t, i) => ls[i].tax = t);
    }
    taxLines.push({ tax_rate_id: rateId, rate_ppm: ppm, taxable_base_minor: sum(ls, 'net'), tax_minor: sum(ls, 'tax'),
                    inclusive: inp.settings.tax.prices_include_tax });
  }
  // 6–8
  const subtotalNet = sum(lines, 'net');
  const taxTotal = sum(taxLines, 'tax_minor');
  const totalBeforeTip = inp.settings.tax.prices_include_tax ? subtotalNet : subtotalNet + taxTotal;
  const tipBase = inp.settings.tipping.base === 'net_before_tax'
        ? (inp.settings.tax.prices_include_tax ? subtotalNet - taxTotal : subtotalNet) : totalBeforeTip;
  const tip = inp.tip?.minor ?? (inp.tip?.bp != null ? roundHalfUp(tipBase * inp.tip.bp, 10000) : 0);
  return freeze({ lines, taxLines, subtotal_net_minor: subtotalNet, tax_total_minor: taxTotal,
                  total_before_tip_minor: totalBeforeTip, tip_total_minor: tip,
                  grand_total_minor: totalBeforeTip + tip, inputs_hash: sha256(canonicalJson(inp)), currency: cur });
}
```
Determinism: integers only, stable ordering by `line_id` order from Agent 04, `canonicalJson`
sorts keys. Property tests: Σ allocations == allocated amount; no negative nets; totals invariant
under line reordering except tie-break; client and server produce byte-identical breakdowns for the
same inputs (golden test vectors shared across platforms, incl. §5.5).

### 10.2 allocateLargestRemainder (Hamilton)

```ts
function allocateLargestRemainder(amount: number, weights: number[]): number[] {
  const W = weights.reduce((a, b) => a + b, 0);
  if (amount === 0 || W === 0) return weights.map(() => 0);
  const raw = weights.map(w => ({ q: Math.floor(amount * w / W), r: (amount * w) % W }));  // exact via BigInt in impl
  let left = amount - raw.reduce((a, x) => a + x.q, 0);
  const order = raw.map((x, i) => i).sort((i, j) => raw[j].r - raw[i].r || i - j);          // ties → earlier line
  for (const i of order) { if (left === 0) break; raw[i].q += 1; left -= 1; }
  return raw.map(x => x.q);
}
```
(Use BigInt/128-bit for `amount * w` — 64-bit products can overflow for large invoices × weights.)

### 10.3 chargeJob — double-charge prevention

```ts
async function chargeJob(cmd, actor, idemKey) {
  return idempotent(actor.businessId, idemKey, 'chargeJob', hash(cmd), async () => {
    return tx(async db => {
      const plan = await db.lockRow('job_payment_plan', cmd.jobId);          // SELECT … FOR UPDATE
      authorize(actor, 'payments.collect', plan);
      if (plan.balance_version !== cmd.expectedBalanceVersion) throw Conflict('BALANCE_CHANGED', currentState(plan));
      const due = amountDue(plan);
      if (cmd.amountMinor - cmd.tipMinor > due) throw Unprocessable('AMOUNT_EXCEEDS_DUE');
      if (cmd.amountMinor < processorMin(plan.currency)) throw Unprocessable('BELOW_PROCESSOR_MIN');
      // partial unique index guarantees a single in-flight attempt per (job, purpose)
      const pay = await db.insert('payment', { status: 'created', idempotency_key: `pay:${idemKey}`, … })
                    .onUniqueViolation(() => { throw Conflict('PAYMENT_IN_PROGRESS', inFlight(plan)); });
      return pay;
    }).then(async pay => {
      // outside DB tx: provider call with deterministic idempotency key
      try {
        const pi = await provider.createPaymentIntent({ amount: pay.amount_minor, currency, customer, payment_method,
              off_session: cmd.method === 'card_on_file', confirm: cmd.method === 'card_on_file',
              capture_method: 'automatic', application_fee_amount: fee(pay), metadata: { payment_id: pay.id, job_id } },
              { idempotencyKey: `pi:${pay.id}`, stripeAccount });
        await savePiId(pay.id, pi.id);            // persisted before anything else
        return applyProviderState(pay.id, pi);    // same function the webhook uses
      } catch (e) {
        if (isNetworkOrTimeout(e)) { await markUnknown(pay.id); scheduleResolve(pay.id); return statusOf(pay.id); }
        if (isCardError(e)) return applyFailure(pay.id, e);
        throw e;
      }
    });
  });
}
```
Rules:
- UI disables the pay button on first tap and keeps the same `idemKey` for retries of the **same** attempt (generated when the tender screen opens, regenerated only after a terminal failure).
- `unknown` resolution: retrieve by stored `pi.id`; if no id was stored (crash before save), search provider by `metadata.payment_id` and/or replay the create with the **same** provider idempotency key `pi:{payment.id}` (returns the original object within the key's lifetime `[I]`).
- Customer portal + worker POS racing on the same job: second caller gets `409 PAYMENT_IN_PROGRESS {by, channel, started_at}`; once the first succeeds, the second's `expectedBalanceVersion` is stale → `409 BALANCE_CHANGED` with the new due (possibly 0 → "Already paid").

### 10.4 Webhook processing & out-of-order safety

```ts
async function receiveWebhook(raw: Buffer, sig: string) {
  const evt = verifySignature(raw, sig, [K6.current, K6.previous], toleranceSec = 300);   // else 400
  const inserted = await db.insertIgnore('processor_event', { provider_event_id: evt.id, type: evt.type,
                     provider_account_id: evt.account, payload: raw, status: 'stored' });
  return 200;                                   // ack fast (< 2 s); processing is async from the inbox
}

async function processEvent(e: ProcessorEvent) {
  // Never trust the payload snapshot for state: re-fetch current object (handles out-of-order + stale payloads)
  const obj = await provider.retrieve(objectRef(e), { stripeAccount: e.provider_account_id });
  switch (kind(obj)) {
    case 'payment_intent': return applyProviderState(paymentIdFrom(obj), obj);
    case 'charge':         return applyChargeDetails(obj);       // card snapshot, balance txn → fee/net
    case 'refund':         return applyRefundState(obj);
    case 'dispute':        return applyDisputeState(obj);
    case 'payout':         return applyPayout(obj);
    case 'account':        return applyAccount(obj);
    case 'payment_method': return applyPaymentMethod(obj);       // automatic card updates, detach
  }
}

const RANK = { created:0, requires_payment_method:1, requires_confirmation:1, requires_action:2, processing:3,
               authorized:4, failed:5, canceled:5, expired:5, succeeded:6, partially_refunded:7, refunded:8 };
function applyProviderState(paymentId, pi) {
  tx(db => {
    const p = db.lockRow('payment', paymentId);
    const next = mapStatus(pi);
    if (RANK[next] < RANK[p.status] && !isLegalBackEdge(p.status, next)) return;   // stale → ignore
    if (p.status === next) return;                                                  // idempotent
    assert(pi.amount_received <= p.amount_minor && pi.currency === p.currency);
    db.update('payment', paymentId, { status: next, … });
    postLedger(`pay:${paymentId}:${next}`, entriesFor(p, next));                   // ledger idempotency key
    outbox.emit(eventFor(next, p));
    recomputeBalance(p.job_id);                                                      // bumps balance_version
  });
}
```
Legal back-edges: `requires_action → processing`, `failed → processing` is **not** legal (a retry is a
new Payment). Events for objects we don't know (e.g., created in the processor dashboard by the
business) are imported as `channel:'external'` payments linked to no job, shown in Card payments
with a "Link to job" action.

### 10.5 Authorization scheduling (beat the hold window)

```ts
function planHold(job, settings, now): Date | 'now' | 'none' {
  if (settings.booking_requirement !== 'preauthorization') return 'none';
  const lead = hours(settings.preauth.lead_hours);            // default 24h
  const desired = job.start.minus(lead);
  // assume conservative window W until we learn capture_before from the actual auth (W = 5 days `[I]`)
  const latestSafeStart = now.plus(days(5)).minus(job.durationEstimate).minus(hours(12));
  return job.start <= latestSafeStart ? 'now' : max(now, desired);
}
// After placing: renewal at capture_before − 12h if job not completed; completion after capture_before is
// handled by charging the saved card off-session (W4/W5), never by capturing an expired auth.
```
Recompute on `scheduling.job.rescheduled`, card replacement, or settings change. The scheduler is a
durable job queue keyed `hold:{job_id}:{generation}`; a generation bump invalidates queued work.

### 10.6 Capture at completion with over/under amount

```ts
function settleAuthorizedJob(job, auth, due /* incl. tip */) {
  if (due <= auth.amount_minor) capture(auth, due, key = `cap:${auth.id}`);           // remainder auto-released
  else {
    capture(auth, auth.amount_minor, key = `cap:${auth.id}`);
    chargeOffSession(job, due - auth.amount_minor, key = `topup:${auth.id}`);        // card on file
    // if topup fails → payment_status partially_paid + invoice for remainder (Agent 04) + N-P03
  }
  if (due === 0) cancel(auth);                                                        // e.g. fully gift-card paid
}
```

### 10.7 Refund allocation

```ts
function allocateRefund(payment, amount, tipPart, linesOfPayment) {
  assert(tipPart <= payment.tip_minor - refundedTip(payment));
  const nonTip = amount - tipPart;
  const remainingTax = payment.tax_portion_minor - refundedTax(payment);
  const remainingNonTip = (payment.amount_minor - payment.tip_minor) - refundedNonTip(payment);
  // final refund: true-up exactly so Σ refunds == original components
  const tax = nonTip === remainingNonTip ? remainingTax : roundHalfUp(nonTip * remainingTax, remainingNonTip);
  const revenue = nonTip - tax;
  const lineAlloc = allocateLargestRemainder(revenue, linesOfPayment.map(l => l.remaining_revenue));
  return { tip: tipPart, tax, revenue, lineAlloc };
}
```
Payment-level `tax_portion_minor` is set at success by allocating the job's tax pro-rata to each
tender (largest remainder over tenders, final tender absorbs the remainder) — needed because a job
can be paid by deposit + gift card + card + cash.

### 10.8 Worker summary (Transactions [S: 17.txt])

```sql
-- range = [weekStart, weekStart + 7d) in business TZ, converted to UTC instants at query time
SELECT ra.worker_id,
       COUNT(DISTINCT ra.job_id)                         AS jobs,
       COUNT(DISTINCT ra.payment_id) FILTER (WHERE ra.refund_id IS NULL) AS payments,
       SUM(ra.revenue_minor) FILTER (WHERE ra.refund_id IS NULL) AS gross,
       SUM(ra.tip_minor)                                 AS tips,        -- refunds carry negative values
       -SUM(ra.revenue_minor) FILTER (WHERE ra.refund_id IS NOT NULL) AS refunds,
       SUM(ra.revenue_minor)                             AS net
FROM revenue_attribution ra
WHERE ra.business_id = $1 AND ra.occurred_at >= $from AND ra.occurred_at < $to
  AND ($staff IS NULL OR ra.worker_id = ANY($staff))
GROUP BY ra.worker_id;
-- plus row "Unassigned" for payments on jobs with no worker (e.g. gift card sales, subscriptions)
```
Week start computed as local date math (DST weeks are 167 or 169 hours — never "now − 7×24h").

### 10.9 Nightly reconciliation (per connected account)

1. Pull balance transactions created in [D−2, D] (overlap absorbs late writes), upsert mirror.
2. For each charge/refund/dispute txn: ensure a matching Payment/Refund/Dispute exists with equal amount, currency, and fee; post missing fee entries (`fee:{balance_txn_id}`).
3. For each Payment `succeeded` in our DB without a provider charge → `reconciliation.mismatch` (critical).
4. Payment stuck in `processing`/`unknown`/`requires_capture` past thresholds → re-retrieve; holds within 24 h of `capture_before` with job not complete → alert + renewal.
5. Payouts: Σ net of txns with `payout_id` = payout amount; ledger `1100 processor_clearing` balance = provider pending + available balance (± in-flight) — difference alarms if ≠ 0 for 2 consecutive runs.
6. PaymentSessions confirmed but without job after 15 min → orphan handling (void/refund + notify).
7. Ledger invariants: every entry balanced; A/R per job equals 04's invoice balance; report diffs.

### 10.10 Offline queue (worker app)

- Card methods require connectivity; cash/check/custom recorded offline with `client_uuid` (UUIDv7), device time and monotonic sequence; shown as "Pending sync".
- On reconnect: POST in sequence; server dedupes on `client_uuid`; if the job balance changed meanwhile (e.g., customer paid online) the server still records the cash (money physically received) and flags **overpayment** → `payments.job.balance_changed{overpaid}` + office task to refund or credit.
- Device clock skew > 5 min → server time used for `succeeded_at`, device time kept as `occurred_at_device`.

### 10.11 CSV export

Columns: `date_local, time_local, timezone, payment_id, job_number, customer_name, staff_collected,
staff_attributed, channel, method, card_brand, last4, purpose, status, currency, amount, tip, tax,
fee, application_fee, net, refunded, payout_id, payout_arrival_date, processor_id`. Amounts in major
units with fixed decimals (currency exponent) and a separate currency column. UTF-8 with BOM;
RFC-4180 quoting; **formula-injection guard**: prefix `'` to any cell starting with `= + - @ \t \r`.
> 10 000 rows → async job, emailed signed link (K16, 24 h expiry). Export audited (who, filters,
row count).
