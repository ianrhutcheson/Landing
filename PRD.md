# Massage Landing Page PRD & Business Model v1.0 (Deterministic)

> Single-page acquisition funnel to book paid massages or schedule a consult. Opinionated, build-ready, no choices for the implementer.

---

## 0) Executive Summary

**Goal:** Launch a high-converting landing page that sells massage sessions and captures consults, using a Hormozi-style offer with bonuses, guarantees, and real scarcity. Integrate booking, payments, CRM, and speed‑to‑lead.

**Primary Outcome Targets (Day 30):**

* Landing conversion (any booking or consult): **≥12%**
* Consult→Paid conversion (7‑day window): **≥55%**
* Membership attach (first‑timers within 7 days): **≥25%**
* First response to consult leads during business hours: **≤5 minutes**

**Launch Date:** T+14 calendar days from project start.

---

## 1) Offer (Hormozi-Mapped, Final)

* **Power Offer (Hero):** “Relieve neck & back pain in 60 minutes — **or your next 30‑minute session is free.** Book today; feel the difference this week.”
* **Bonuses (bundled, first‑time only, this week):**

  1. 10‑minute assisted stretch (**$29 value**)
  2. Personalized posture & desk‑setup micro‑guide PDF (**$19 value**)
  3. Aromatherapy upgrade (**$15 value**)
  4. Mobility mini‑routine video (**$19 value**) **Stack Value:** $82
* **Guarantee (risk reversal):** If the client doesn’t feel noticeably better after the session, **their next 30‑minute session is free** (first‑time clients; must notify at checkout; non‑transferable).
* **Scarcity & Urgency (real):** 40 first‑time slots per week, displayed live; bonus stack expires Sunday 11:59pm local.
* **Pricing (public):**

  * **Intro Relief (60 min): $99**
  * **Performance (90 min): $149** *(Most Popular)*
  * **Recovery Plus (120 min): $199**
* **Add‑ons (AOV):** Deep tissue +$15, Hot stones +$15, Cupping +$25, Extended stretch +$19.
* **Membership (continuity):**

  * **Standard:** 1×60‑min / month **$89** (rollover 2 months; share with 1 family member)
  * **Plus:** 2×60‑min / month **$169** (priority booking; 10% add‑on discount)
  * Overages billed at member rate: $89 per additional 60‑min.

---

## 2) Personas (for copy and targeting)

* **Desk‑Bound Professionals (25–55):** pain relief, time‑starved.
* **Athletes & Weekend Warriors:** recovery, mobility, performance.
* **Stress‑Relief Seekers:** calm, giftable experiences.

---

## 3) Scope

**In scope:** One landing page + thank‑you page; booking + consult flows; payments; CRM capture; SMS/Email automations; dashboards. **Out of scope (v1):** Blog, multi‑location switching UI, gift card store, loyalty portal.

---

## 4) Architecture & Tech Stack (finalized)

* **Frontend:** Next.js 14 (App Router), Tailwind CSS, Vercel hosting, ISR for static sections.
* **Design Tokens:** see Section 10.
* **Database:** Supabase Postgres 15.
* **ORM:** Prisma.
* **Auth:** Passwordless magic links (Supabase Auth) for admin/staff. No client accounts in v1.
* **Payments:** Stripe Checkout + Webhooks.
* **Scheduling:** Cal.com API (embedded widget) with therapist calendars; buffers and resources.
* **Messaging:** Twilio (SMS + voice) for speed‑to‑lead and reminders.
* **Email:** SendGrid.
* **Analytics:** Segment → Mixpanel (product analytics) + GA4 (attribution) + server events.
* **Infra Ops:** Vercel Env vars, Supabase backups daily 03:00 local, Stripe webhook retries, Status page via Better Stack.
* **Error Tracking:** Sentry.

---

## 5) Page IA (Information Architecture)

1. **Hero (Power Offer + CTAs + Social Proof)**
2. **Problem → Outcome** band (why sitting hurts; how you’ll feel after session 1)
3. **How It Works (3 steps)**
4. **Bonus Stack** (cards with values)
5. **Offer Grid** (3 packages + add‑on selector + guarantee badge)
6. **Results & Proof** (testimonials, pain‑scale charts)
7. **Guarantee** (plain English + FAQ)
8. **Scarcity & Urgency** (live counter to weekly new‑client slots)
9. **Consult Module** (phone or in‑person; “call me now” if open)
10. **FAQ** (licensing, modalities, HSA/FSA, pregnancy, hygiene)
11. **Footer** (license #, location, contact, SMS consent)

---

## 6) Copy (final, production‑ready)

* **Hero H1:** Relieve neck & back pain in 60 minutes — or your next 30‑minute session is free.
* **Hero Subhead:** Board‑licensed therapists. Same‑week availability. **Bonus today:** 10‑minute assisted stretch included.
* **Primary CTA:** Book My Massage
* **Secondary CTA:** Schedule a Free 10‑min Phone Consult
* **3‑Step Section:** 1) Pick your session 2) Choose a time this week 3) Feel better today
* **Bonus Stack Heading:** Book this week and get $82 in bonuses — free.
* **Guarantee Blurb:** If you don’t feel noticeably better, your next 30‑minute session is free. Simple.
* **Urgency Microcopy:** Only **{dynamic_remaining_slots}** first‑time spots left this week. Offer ends Sunday.

---

## 7) UX/Flows (deterministic)

### 7.1 Booking Flow (4 steps)

A) Service & Duration → B) Date/Time (show next‑available within 48h at top) → C) Add‑ons → D) Stripe Checkout → TY page with membership upsell + Add to Calendar.

### 7.2 Consult Flow (2 paths)

* **Phone Consult:** time picker default today/tomorrow; if within business hours and an agent is free, show **“Call Me Now”**; triggers immediate outbound call.
* **In‑Person Consult:** calendar slot at clinic; no payment; reminder SMS 24h & 2h.

### 7.3 Speed‑to‑Lead

* On consult submit, create **lead** and post to **#consult-triage** (Slack) + auto‑dial via Twilio within **≤5 minutes** during business hours.

### 7.4 Thank‑You Page

* Confirms booking; one‑click membership join via Stripe Customer Portal link; calendar file (.ics) download; referral CTA.

---

## 8) Functional Requirements

1. **Real‑Time Availability:** Cal.com resource calendars per therapist; enforce 15‑minute prep buffers; room capacity 1 per room.
2. **Payments:** Stripe Checkout; deposit not used in v1; tipping at checkout (10/15/20/custom).
3. **CRM:** Supabase tables; Segment identify on submit; store UTM, referrer, and gclid if present.
4. **SMS/Email Automations:**

   * Booking confirmation + reminders (24h & 2h, SMS+Email)
   * Consult confirmations; missed call fallback SMS with rebook link
   * Post‑session NPS (0–10) + review link if NPS ≥9
   * Day‑3 follow‑up to join membership; Day‑7 final reminder
5. **Guarantee Handling:** Admin toggle to mark a visit as “Guarantee-eligible used”; issues a free 30‑min credit (Stripe coupon) for same client.
6. **Scarcity Widget:** Pulls count of remaining first‑time slots for the week from `availability_cache.weekly_new_client_slots_remaining`.
7. **Testimonials:** Hard‑coded JSON list editable in CMS table.
8. **FAQ:** Hard‑coded markdown in DB.
9. **Compliance:** Display state massage license in footer; explicit SMS consent checkbox (unchecked by default) with TCPA copy.

---

## 9) Non‑Functional Requirements

* **Performance:** LCP < 2.5s on 4G; images through next/image; Vercel CDN; font loading with `display=swap`.
* **Accessibility:** WCAG 2.1 AA; semantic headings; focus styles; contrast ≥ 4.5:1.
* **Security:** Stripe webhooks signed; Supabase RLS enabled for all tables; admin UI behind SSO magic‑link + role check.
* **Privacy:** No PHI; collect only contact and appointment details; privacy policy link in footer.

---

## 10) Design System (tokens, deterministic)

* **Font:** Inter (system fallback: ui-sans-serif, system-ui)
* **Colors:**

  * Primary: `#0EA5E9` (sky‑500)
  * Primary‑dark: `#0284C7`
  * Accent: `#22C55E`
  * Text: `#0F172A`
  * Subtext: `#334155`
  * Background: `#F8FAFC`
  * Card: `#FFFFFF`
  * Danger: `#DC2626`
* **Radii:** 16px cards/buttons
* **Elevations:** soft shadow `0 8px 24px rgba(15,23,42,0.08)`
* **Spacing scale:** Tailwind defaults (4px base)
* **Breakpoints:** Tailwind defaults

---

## 11) Data Model (Postgres)

```sql
-- Clients and Leads
create table clients (
  id uuid primary key default gen_random_uuid(),
  first_name text not null,
  last_name text not null,
  email text not null unique,
  phone text not null,
  created_at timestamptz not null default now()
);

create table leads (
  id uuid primary key default gen_random_uuid(),
  first_name text not null,
  last_name text not null,
  email text not null,
  phone text not null,
  source text not null,
  utm_medium text,
  utm_campaign text,
  utm_content text,
  gclid text,
  status text not null check (status in ('new','contacted','scheduled','no_answer','disqualified','converted')) default 'new',
  created_at timestamptz not null default now()
);

-- Appointments
create table therapists (
  id uuid primary key default gen_random_uuid(),
  full_name text not null,
  license_number text not null,
  active boolean not null default true
);

create table services (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  duration_min int not null,
  price_cents int not null,
  is_intro boolean not null default false
);

create table add_ons (
  id uuid primary key default gen_random_uuid(),
  name text not null,
  price_cents int not null
);

create table appointments (
  id uuid primary key default gen_random_uuid(),
  client_id uuid references clients(id) on delete cascade,
  therapist_id uuid references therapists(id),
  service_id uuid references services(id),
  starts_at timestamptz not null,
  ends_at timestamptz not null,
  status text not null check (status in ('booked','completed','cancelled','no_show')) default 'booked',
  stripe_payment_intent text,
  created_at timestamptz not null default now()
);

create table appointment_add_ons (
  appointment_id uuid references appointments(id) on delete cascade,
  add_on_id uuid references add_ons(id) on delete cascade,
  primary key (appointment_id, add_on_id)
);

-- Memberships
create table memberships (
  id uuid primary key default gen_random_uuid(),
  client_id uuid references clients(id) on delete cascade,
  tier text not null check (tier in ('standard','plus')),
  stripe_subscription_id text not null,
  started_at timestamptz not null default now(),
  status text not null check (status in ('active','paused','cancelled')) default 'active'
);

-- Scarcity cache for weekly new-client slots
create table availability_cache (
  week_of date primary key,
  weekly_new_client_slots_total int not null,
  weekly_new_client_slots_remaining int not null,
  updated_at timestamptz not null default now()
);

-- Guarantees and coupons
create table guarantee_credits (
  id uuid primary key default gen_random_uuid(),
  client_id uuid references clients(id) on delete cascade,
  issued_at timestamptz not null default now(),
  redeemed_appointment_id uuid references appointments(id),
  stripe_promo_code text,
  redeemed boolean not null default false
);
```

---

## 12) API Spec (Next.js Route Handlers)

**Auth:** Admin/staff only via Supabase Auth; public booking uses Stripe/Cal.com embeds and public endpoints with input validation.

* `POST /api/lead`

  * Body: `{ firstName, lastName, email, phone, source, utm*, gclid }`
  * Creates `lead`, triggers Slack + Twilio dialer, returns `{leadId}`.

* `POST /api/lead/:id/status`

  * Body: `{ status }` where status ∈ new|contacted|scheduled|no_answer|disqualified|converted

* `POST /api/webhooks/stripe`

  * Handles `checkout.session.completed` → create `client` if new, `appointment` if metadata present, mark `lead` converted if mapping.

* `POST /api/webhooks/cal`

  * On `BOOKING_CREATED`/`BOOKING_RESCHEDULED`/`BOOKING_CANCELLED` → upsert `appointments` rows.

* `GET /api/availability/first-time`

  * Returns `{ remainingSlots, weekOf }` from `availability_cache`.

* `POST /api/guarantee/issue`

  * Body: `{ clientId }` → creates `guarantee_credits` + Stripe promo code.

* `POST /api/nps`

  * Body: `{ appointmentId, score: 0..10, comment }` → stores event and triggers review flow if score ≥ 9.

**Validation:** Zod schemas; rate limit public endpoints with Upstash.

---

## 13) Event Tracking (Segment → Mixpanel)

* `page_view` (path, utm*, gclid)
* `cta_click` (id: hero_book | hero_consult | offer_grid_book | consult_call_now)
* `lead_created` (leadId, source)
* `lead_connected` (leadId, secs_to_first_contact)
* `booking_started` (serviceId)
* `checkout_completed` (appointmentId, revenue_cents)
* `membership_joined` (tier)
* `nps_submitted` (score)

---

## 14) Business Model & Unit Economics (initial)

* **Prices:** 60m $99, 90m $149, 120m $199; add‑ons: +$15/+25/+19 per above.
* **Therapist Comp:** $40/hour effective (incl. prep) + $5 per add‑on performed.
* **Room Cost (alloc.):** $8/session hour (rent, utilities, laundry).
* **Consumables:** $3/session.
* **Processing Fees:** 2.9% + $0.30 per Stripe txn.
* **Marketing CAC (blended):** $45 first 60 days.
* **Intro Visit Gross Margin (60m, no add‑ons):**

  * Revenue $99 − Stripe ~ $3.17 − Therapist $40 − Room $8 − Cons $3 = **$44.83**
* **Break‑Even on Visit #1:** Achieved (margin covers CAC if add‑on or upsell present). Target ≥ 50% intro attach to at least one add‑on.
* **Membership LTV (Standard):** $89 × 6 months avg tenure = **$534** gross; margin per month ≈ $89 − (40 + 8 + 3 + fees $3) = **$35** → **$210** 6‑mo contribution (pre‑overhead).
* **North Star:** Membership MRR and 7‑day repeat rate.

---

## 15) Operations & SLAs

* **Business Hours:** Mon–Fri 9:00–19:00, Sat 10:00–16:00. Sun closed.
* **Speed‑to‑Lead:** **≤5 minutes** first contact during business hours; **≤15 minutes** outside hours via SMS reply + morning call.
* **No‑Show Policy:** 24h cancellation window; <24h forfeits 50% fee (enforced via Stripe saved card).
* **Hygiene:** Room turnaround buffer 15min scheduled in Cal.com.
* **Licensing:** Display clinic license and therapist license numbers on the footer and consult confirmation.

---

## 16) SEO & Schema (final)

* Title: “Massage for Neck & Back Pain Relief | [Clinic Name] — Book Online”
* Meta Description: “Relieve pain in 60 minutes — or your next 30‑minute session is free. Licensed therapists. Same‑week availability. Book online now.”
* LocalBusiness + Service + FAQ schema via JSON‑LD.

**JSON‑LD (inject as script):**

```json
{
  "@context": "https://schema.org",
  "@type": "LocalBusiness",
  "name": "[Clinic Name]",
  "image": "https://example.com/hero.jpg",
  "telephone": "+1-312-555-0162",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "1234 Main St",
    "addressLocality": "Chicago",
    "addressRegion": "IL",
    "postalCode": "60601",
    "addressCountry": "US"
  },
  "openingHours": "Mo-Fr 09:00-19:00, Sa 10:00-16:00",
  "url": "https://example.com",
  "priceRange": "$$",
  "servesCuisine": "",
  "sameAs": ["https://www.facebook.com/example","https://www.instagram.com/example"],
  "makesOffer": {
    "@type": "Offer",
    "name": "Intro Relief 60 min",
    "price": "99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock"
  }
}
```

---

## 17) UI Block Contract (for vibe coding)

```json
{
  "page": "landing",
  "blocks": [
    {"id":"hero","component":"HeroOffer","props":{"title":"Relieve neck & back pain in 60 minutes — or your next 30‑minute session is free.","sub":"Board‑licensed therapists. Same‑week availability. Bonus today: 10‑minute assisted stretch included.","primaryCta":"Book My Massage","secondaryCta":"Schedule a Free 10‑min Phone Consult","rating":4.9,"reviewCount":327}},
    {"id":"problem_outcome","component":"ProblemOutcome","props":{"bullets":["Sitting 6+ hours tightens hip flexors and traps","Headaches from neck tension","Feel looser and lighter after your first session"]}},
    {"id":"how_it_works","component":"Steps3","props":{"steps":["Pick your session","Choose a time this week","Feel better today"]}},
    {"id":"bonus_stack","component":"BonusCards","props":{"items":[{"title":"10‑minute assisted stretch","value":29},{"title":"Posture & desk‑setup guide PDF","value":19},{"title":"Aromatherapy upgrade","value":15},{"title":"Mobility mini‑routine video","value":19}],"totalValue":82}},
    {"id":"offer_grid","component":"OfferGrid","props":{"plans":[{"name":"Intro Relief","minutes":60,"price":99},{"name":"Performance","minutes":90,"price":149,"popular":true},{"name":"Recovery Plus","minutes":120,"price":199}],"addons":[{"name":"Deep tissue","price":15},{"name":"Hot stones","price":15},{"name":"Cupping","price":25},{"name":"Extended stretch","price":19}],"guarantee":"If you don’t feel noticeably better, your next 30‑minute session is free."}},
    {"id":"proof","component":"Testimonials","props":{"items":[{"name":"A. Patel","text":"Neck pain gone after one visit."},{"name":"J. Morales","text":"Best 90 minutes of my week."},{"name":"S. Kim","text":"Signed up for membership immediately."}]}},
    {"id":"scarcity","component":"ScarcityCounter","props":{"remaining":"{dynamic_remaining_slots}","deadline":"Sunday 23:59"}},
    {"id":"consult","component":"ConsultModule","props":{"modes":["phone","in_person"],"slaMinutes":5}},
    {"id":"faq","component":"FAQ","props":{"items":[{"q":"Do you accept HSA/FSA?","a":"Yes, we provide itemized receipts."},{"q":"Is prenatal massage available?","a":"Yes, after the first trimester with proper positioning."},{"q":"Tipping?","a":"Optional at checkout."}]}}
  ]
}
```

---

## 18) Acceptance Criteria (selected)

* **Booking:** Selecting any plan proceeds to date/time with next‑available within 48h pinned on top; completion opens Stripe Checkout; Stripe success returns to TY with appointment details.
* **Consult:** Submitting the form creates a lead row, posts to Slack, and triggers Twilio dialer within SLA; status updates persist.
* **Scarcity:** Counter reflects `availability_cache.weekly_new_client_slots_remaining`; when 0, hero switches to consult‑first CTA.
* **Guarantee:** Admin issuing guarantee credit creates a Stripe promo; redemption flips `redeemed=true`.
* **Membership:** TY page shows Stripe Customer Portal link; joining fires `membership_joined` event.
* **Analytics:** All events land in Mixpanel with userId set to email hash if available.

---

## 19) Test Plan

* **Unit:** Zod validation, webhook signatures, time‑zone correctness (America/Chicago).
* **Integration:** Stripe test mode bookings; Cal.com sandbox bookings; Twilio test credentials; end‑to‑end consult lead to outbound call.
* **Perf:** Lighthouse ≥ 90 performance; LCP < 2.5s on Moto G4 emulation.
* **Accessibility:** Axe clean; keyboard nav through all CTAs.

---

## 20) Rollout Plan

* **T+0–3:** Implement DB + API + webhooks + skeleton UI.
* **T+4–7:** Integrate Cal.com, Stripe, Twilio, Segment; build scarcity + testimonials.
* **T+8–10:** Copy, design polish, SEO schema, QA.
* **T+11–12:** Beta with $500 ad spend; collect 20+ sessions data.
* **T+13–14:** Iterate and launch public.

---

## 21) Risks & Mitigations

* **Lead response delays:** On‑call rotation; Twilio auto‑dial; Slack escalation after 3 minutes.
* **Over‑promising:** Guarantee scoped to first‑timers; clear terms in FAQ; staff training.
* **Calendar conflicts:** Cal.com buffers enforced; room capacity check before booking create.

---

## 22) Admin Ops (MVP)

* Simple `/admin` with:

  * Leads table with status dropdown
  * Appointments calendar (read‑only v1)
  * Availability cache editor for weekly first‑time slots
  * Guarantee issue button on client profile

---

## 23) Seed Data (for dev/testing)

```sql
insert into services (id,name,duration_min,price_cents,is_intro) values
  ('00000000-0000-0000-0000-000000000061','Intro Relief 60',60,9900,true),
  ('00000000-0000-0000-0000-000000000091','Performance 90',90,14900,false),
  ('00000000-0000-0000-0000-000000000121','Recovery Plus 120',120,19900,false);

insert into add_ons (id,name,price_cents) values
  ('00000000-0000-0000-0000-000000000001','Deep tissue',1500),
  ('00000000-0000-0000-0000-000000000002','Hot stones',1500),
  ('00000000-0000-0000-0000-000000000003','Cupping',2500),
  ('00000000-0000-0000-0000-000000000004','Extended stretch',1900);
```

---

## 24) Ads & Nurture (operationalized)

* **Lead Magnet:** “7‑Minute Desk Reset” video + printable checklist (auto‑delivered on consult form submit even if no booking).
* **5‑Day Sequence:** Day0 proof, Day1 demo, Day3 FAQ/objections, Day5 bonus reminder, Day6 last‑chance Sunday push.
* **Retargeting:** 7‑day site visitors; exclude purchasers; creative emphasizes guarantee + $82 bonus stack.

---
