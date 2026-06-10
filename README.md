# Csport — Open-Source Studio Management Dashboard

**Replace bsport. Run Heat Lagos. Built by grapplers, for studios.**

## Mission
Replace bsport with a self-hosted, free alternative. Dashboard only — no mobile app. Full control over memberships, scheduling, payments, and customer management.

## Features to Build (Based on bsport Reverse Engineering)

### 1. Membership Management
bsport implements these membership types. Each needs a Csport equivalent:

| Membership | Price | Duration | Notes |
|-----------|-------|----------|-------|
| Intro Offer | 79€ | 2 weeks | New students only |
| 12 Month Membership | 125€/mo | Rolling | Auto-renewing subscription |
| 1 Month Unlimited | 160€ | 1 month | One-off, no commitment |
| Yearly | 1,200€ | 12 months | Paid upfront, save 300€ |
| 10 Class Package | 180€ | 3 months | Punch card, expires |
| Vacation Week | 59€ | 7 days | Traveller-specific |
| Single Drop-in | 22€ | 1 class | No commitment |

**Key Behaviors to Replicate:**
- Passes linked to a membership plan (bsport uses plan IDs like 5821)
- Payment links generated per pass: `/customer/payment/pass/{passId}/?membership={planId}&force=true`
- Auto-renewing vs. fixed-term passes
- Class packages with expiry dates

### 2. Class Schedule & Booking
bsport's schedule widget tracks:

- **Daily schedule** with class times, types, and availability
- **Class types**: Heat Flow, Pilates (Non-heated), Sculpt, Power, Mobility, Recovery, Yin
- **Level filters**: All levels, Beginner, Intermediate
- **Booking limits**: Classes marked "CLOSED" when full
- **bsport widget integration**: Embedded via `https://cdn.bsport.io/scripts/widget.js`

**Key Features to Build:**
- [ ] Weekly schedule view (Mon-Sun)
- [ ] Class type icons/colors
- [ ] Booking limits & waitlist
- [ ] Class detail view (description, teacher, duration)
- [ ] Calendar sync (optional)

### 3. Customer Management
bsport stores:
- Customer profiles (name, email, phone)
- Membership/pass history
- Attendance tracking
- Booking history
- Payment history

**Key Features to Build:**
- [ ] Customer database
- [ ] Check-in system (QR code or manual)
- [ ] Attendance reports
- [ ] Customer notes/tags

### 4. Teacher Management
Heat Lagos has 4 teachers:
- Stine Brosche
- Sebastian Brosche
- Anastasiia
- Agata

**Key Features to Build:**
- [ ] Teacher profiles
- [ ] Schedule assignment
- [ ] Attendance tracking
- [ ] Payment tracking

### 5. Payments & Billing
bsport handles:
- One-time payments (drop-ins, class packs)
- Recurring subscriptions (monthly, yearly)
- Multiple payment methods (credit card, Apple Pay, Google Pay)

**Key Features to Build:**
- [ ] Stripe integration (you already have Stripe accounts)
- [ ] One-time payment processing
- [ ] Recurring billing
- [ ] Invoice generation
- [ ] Refund handling

### 6. Dashboard Analytics
bsport's dashboard likely shows:
- Revenue dashboard
- Class attendance
- Membership counts
- New vs. returning customers
- Peak hours

**Key Features to Build:**
- [ ] Revenue overview (daily, weekly, monthly)
- [ ] Attendance metrics
- [ ] Membership growth tracking
- [ ] Class popularity reports

### 7. Integration Points (for Heat Lagos specifically)

**Existing Integrations to Support:**
- [ ] **Stripe** — payment processing (you already have 3 Stripe accounts)
- [ ] **Google Calendar** — class schedule sync
- [ ] **Email notifications** — booking confirmations, reminders
- [ ] **Google Analytics / GTM** — track bookings
- [ ] **Website embedding** — schedule widget for heatlagos.com

---

## Tech Stack (Suggested)

| Layer | Choice | Why |
|-------|--------|-----|
| **Frontend** | Next.js (React) | Matches existing heatlagos.com stack. Same dev can work on both. |
| **Backend** | Next.js API routes | No separate server needed. |
| **Database** | SQLite (local) → PostgreSQL (production) | Start simple, scale when needed. |
| **Auth** | NextAuth / Lucia | Simple session management. |
| **Payments** | Stripe | You already have Stripe accounts. |
| **Deploy** | Cloudflare Pages (frontend) + D1 or Postgres | No Vercel. |

**Alternative (Simpler):** Plain HTML/CSS/JS + Go or Python backend. Same deployment model as the YTT site.

---

## bsport URLs & API Patterns (Discovered)

| Endpoint | Purpose |
|----------|---------|
| `backoffice.bsport.io/login` | Studio manager login |
| `backoffice.bsport.io/customer/payment/pass/{passId}/?membership={planId}&force=true` | Customer purchase link |
| `cdn.bsport.io/scripts/widget.js` | Embeddable schedule widget |
| bsport-widget-177399 | Schedule widget ID |
| bsport-widget-404125 | Workshop widget ID |

---

## Getting Started (After bsport Login)

1. Document every page in the bsport dashboard
2. Screenshot each feature
3. Build the equivalent Csport page
4. Connect Stripe
5. Test with real data

---

*This is a living document. Update as features are discovered.*