# Video 01 findings: owner's recorded website pitch

**Source:** owner-supplied screen recording (16:39, 1280×720, webcam overlay + narration),
analyzed 2026-11-13 from 100 frames (one every 10 s) and a full local transcript.
Raw frames and transcript are kept out of this public repo on purpose (they contain a
third-party business's details and people's names).

## What the video actually is
This is **not** the Swash.co / Fieldd booking walkthrough described in `BRIEF.md`. It is
a pitch from a local web designer to a small, shop-based upholstery business (35+ years
in business, Tri-Valley area, CA). It covers:

| Time | What is on screen | What is said |
|---|---|---|
| 0:00–2:10 | Webcam intro | Designer intro; wants a first client; dislikes generic "AI slop" sites |
| 2:20–5:20 | Figma brand board | New wordmark + monogram logo (U and H forming a chair); warm grayscale "newspaper" palette + two accents (terracotta orange preferred, olive green) |
| 2:30–8:20 | Business's current WordPress site | Critique: slow loads, too much text, broken testimonials, quote form slow, partners/blog wrongly in the nav, no trust signals; estimated ~0.5% conversion vs ~4% target |
| 6:50–7:30 | Current "get a quote" form | First/last name, email, phone, free-text request (200 chars), item dimensions (L/W/H, inches), reCAPTCHA; location and "store visits by appointment only" beside it |
| 8:30–16:10 | New homepage design in Figma | Header (logo, Projects, Testimonials, Services, About Us, phone); hero "Give Your Furniture a Fresh Start" with star rating + review count; primary CTA "Book My Free Consultation" + "See Our Work"; stats row (1,000+ customers, 30+ years, free consultation & quote, 100% satisfaction); 3-step process (01 Consult → 02 Customize → 03 Collect); before/after project carousel with "View All"; value-prop sections, each repeating the consultation CTA; secondary CTA "Call: <phone>" |
| 14:20–16:20 | Narration only | Plans a **custom multi-step booking form**: pick item type (chair / sofa / lounger / other), location, preferred contact method (phone / text / email). It would save to Google Sheets, email the business on each new consultation, and include a calendar. The designer isn't sure how to sync it with Google Calendar. "Fully branded, will look like you, will feel like you, and will just work." |

## Why it matters for regen_auto_CRM
The video shows the **owner's real use case**: an independent web designer building sites
for small service businesses and wanting a booking backend to plug in behind the site's
"Book" buttons. The Google-Sheets-plus-email form is the hack the CRM should replace.

### Requirements this adds or confirms
1. **Consultation/quote-first booking, not only priced services.** Many service
   businesses book a *free consultation*, then quote, then do a multi-week job.
   - Booking flow must support **unpriced "request" services** (consultation, estimate)
     with intake questions (item type, dimensions, photos, location, contact preference).
   - Map to Agent 04's single job ID: consultation → quote → job → pickup/collect.
   - Multi-week job stages (Consult → Customize → Build 2–4 weeks → Collect) need
     **custom job stages per business**, with customer-visible status.
   - Owners: Agents 01, 02, 04.
2. **Shop-based plus pickup/drop-off logistics.** Customers bring the item in, or the
   business picks it up and delivers it. Store visits are by appointment only. Owners:
   Agents 02 and 03.
3. **Preferred contact method** (phone / text / email) captured at booking and respected
   by notifications. Owners: Agents 07 and 08.
4. **Two-way calendar sync** (Google Calendar) is a real pain point. The CRM must do it
   natively. Owners: Agents 03 and 09.
5. **New-booking alerts to the owner** by email, SMS and push. Owner: Agent 08.
6. **Reviews:** show rating and review count, and link to third-party review sites
   (Yelp, Google). Owners: Agents 08 and 11.
7. **Branding depth (confirms Agent 11's scope).** The brand board is a good test case:
   - warm grayscale ramp plus two accent colors;
   - serif display headings with a geometric sans body font;
   - wordmark and monogram logo variants;
   - a hover state that uses the accent color.
   The booking flow must not look like a generic third-party widget.
8. **Speed is a selling point.** The pitch's main criticism of the old site is load time.
   The booking subdomain must be fast. Owners: Agents 01 and 11.
9. **Agency / multi-client mode (new).** A designer setting up the CRM for several client
   businesses needs:
   - one login across many businesses;
   - setup templates (clone a business's catalog, theme and forms);
   - a hand-off/ownership transfer to the client;
   - possibly white-label.
   This ties to Agent 09's multi-business hub. Owner: Agent 10 to place it.
10. **Embedding the booking UI into a designer-built site** must be clean: subdomain plus
    deep links per CTA (e.g. "Book My Free Consultation" → consultation service
    preselected). Owner: Agent 01.

## Still needed
The **customer-side Fieldd flow** (Swash.co's booking subdomain: service selection,
add-ons, time slots, checkout) is still unverified. Every "Verify against video"
checklist in `docs/brain/` still refers to that walkthrough.
