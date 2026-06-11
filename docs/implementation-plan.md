# Csport Implementation Plan

## Overview

Phased build-out of the Csport studio management dashboard. Target: 12 weeks to a production-ready replacement for bsport at Heat Lagos (plan ID 5821). Each phase builds on the previous one and delivers working, testable functionality.

**Tech Stack (recommended):**
- **Backend:** Python (FastAPI) with PostgreSQL
- **Frontend:** Vanilla JS + Tailwind CSS + Chart.js (or React with a minimal stack)
- **Payments:** Stripe Connect or direct Stripe API
- **Deployment:** Docker + Fly.io / Railway / Hetzner VPS
- **CI/CD:** GitHub Actions

---

## Phase 1: Foundation (Week 1–2)

**Goal:** Core data layer, basic API, and Stripe integration. By the end of Phase 1, the system can accept payments and store customer/membership data.

### Deliverables

#### 1.1 Project Scaffolding
- [x] Initialize FastAPI project structure
- [x] Set up PostgreSQL database (via Docker Compose for dev)
- [x] Configure SQLAlchemy ORM with Alembic migrations
- [x] Add environment config (`.env` for secrets)
- [x] Set up CORS, middleware, error handlers

#### 1.2 Database Schema (Migration)
- [x] Create all 8 tables: `memberships`, `customers`, `customer_memberships`, `class_types`, `teachers`, `schedule`, `bookings`, `payments`
- [x] Add all indexes and foreign keys (see [database-schema.md](./database-schema.md))
- [x] Seed initial data:
  - 7 membership tiers (Intro €79, 12-mo €125/mo, 1-mo €160, Yearly €1200, 10-pack €180, Vacation €59, Drop-in €22)
  - 7 class types (Pilates, Yoga, Sculpt, Flow, Mobility, Recovery, Yin) with colors
  - 4 teachers (Stine, Sebastian, Anastasiia, Agata)

#### 1.3 Basic API Endpoints
- [x] `GET /api/memberships` — list all tiers
- [x] `POST /api/memberships` — create new tier
- [x] `PUT /api/memberships/{id}` — update tier
- [x] `DELETE /api/memberships/{id}` — soft-delete
- [x] `GET /api/class-types` — list class types
- [x] `GET /api/teachers` — list teachers
- [x] `GET /api/customers` — list with search/pagination
- [x] `POST /api/customers` — create customer
- [x] `GET /api/customers/{id}` — get with memberships/bookings
- [x] `PUT /api/customers/{id}` — update
- [x] `DELETE /api/customers/{id}` — soft-delete

#### 1.4 Stripe Integration
- [x] Set up Stripe SDK, webhook endpoint (`POST /api/payments/stripe-webhook`)
- [x] Handle `payment_intent.succeeded` → create payment record + activate membership
- [x] Handle `payment_intent.payment_failed` → mark payment failed
- [x] Handle `customer.subscription.updated` / `.deleted` → sync membership status
- [x] Handle `invoice.payment_succeeded` → renew subscription memberships
- [x] Create a `POST /api/payments/create-checkout-session` endpoint for frontend
- [x] Stripe signature verification for webhooks
- [x] Seed Stripe price IDs for each membership tier in config

### Testing Milestone (End of Phase 1)
> ✅ Can create a membership tier via API
> ✅ Can create a customer via API
> ✅ Can process a Stripe payment (or simulate it via webhook)
> ✅ Payment record appears in database after Stripe webhook

---

## Phase 2: Customer & Membership Management (Week 3–4)

**Goal:** Full customer lifecycle management, membership CRUD, and payment tracking. Admin users can create, search, and manage customers manually.

### Deliverables

#### 2.1 Customer Management
- [x] Enhance `GET /api/customers` with filtering (active/inactive, by membership type)
- [x] `GET /api/customers/{id}/bookings` — booking history
- [x] `GET /api/customers/{id}/payments` — payment history
- [x] `PUT /api/customers/{id}/notes` — inline notes editing
- [x] Dashboard UI: Customer list table with search, sort, pagination
- [x] Dashboard UI: Customer detail slide-in/panel

#### 2.2 Membership CRUD
- [x] Full membership management UI (list, create, edit, reorder, deactivate)
- [x] Membership analytics: count of active, expired, near-expiry
- [x] `GET /api/customer-memberships` + `POST` — assign membership
- [x] Auto-expire memberships (daily cron: check end_date, update status)

#### 2.3 Payment Flow
- [x] `GET /api/payments` — list with filters (customer, date range, status)
- [x] `POST /api/payments` — manual payment (cash/transfer), auto-activates membership
- [x] `GET /api/payments/{id}` — payment detail
- [x] Dashboard UI: Payment history table in customer detail
- [x] Dashboard UI: Manual payment capture form

#### 2.4 Frontend Foundation
- [x] Set up frontend project (Vite + Tailwind CSS)
- [x] Navigation bar with all sections
- [x] API client module (fetch wrapper with auth)
- [x] Dashboard overview page with KPI cards (static data wired to API)
- [x] Membership management page (CRUD)

### Testing Milestone (End of Phase 2)
> ✅ Can create/edit customers with full details
> ✅ Can assign memberships to customers
> ✅ Can log a manual cash payment
> ✅ Membership auto-expires correctly
> ✅ All customer/membership CRUD flows work end-to-end

---

## Phase 3: Schedule, Bookings & Check-In (Week 5–6)

**Goal:** The core studio operations — weekly schedule, class booking, and front-desk check-in. This replaces bsport's schedule widget.

### Deliverables

#### 3.1 Schedule Management
- [x] `GET /api/schedule?date=YYYY-MM-DD` — get schedule for date range
- [x] `POST /api/schedule` — create a class session
- [x] `PUT /api/schedule/{id}` — update (time, capacity, teacher)
- [x] `DELETE /api/schedule/{id}` — cancel class, auto-cancel all bookings
- [x] Conflict detection: prevent double-booking a teacher at same time
- [x] Dashboard UI: Weekly calendar grid with colored class tiles
- [x] Dashboard UI: Class creation/editing modal
- [x] Dashboard UI: Capacity indicators (color-coded bars)

#### 3.2 Booking System
- [x] `POST /api/bookings` — book a class (full validation):
  - ✅ Customer exists and is active
  - ✅ Schedule exists, not cancelled, hasn't started
  - ✅ Capacity available
  - ✅ Not already booked
  - ✅ No time conflict with another booking
  - ✅ Has active membership with remaining classes (or drop-in)
  - Decrements `customer_memberships.remaining_classes` for packages
- [x] `DELETE /api/bookings/{id}` — cancel booking, restore counts
- [x] `GET /api/bookings?schedule_id=X` — roster for a class
- [x] `PUT /api/bookings/{id}` — change membership, mark no-show
- [x] Dashboard UI: Class roster view (who's booked, checked in, no-show)
- [x] Dashboard UI: Customer booking history view

#### 3.3 Check-In
- [x] `POST /api/checkin` — fast check-in:
  - By customer ID or email/phone lookup
  - If already booked → confirm check-in, decrement membership
  - If not booked → auto-book (if membership valid) or prompt purchase
  - Returns confirmation with updated remaining classes
- [x] Dashboard UI: Check-in page with search and quick-select
- [x] Dashboard UI: Success/error feedback with next actions
- [x] Edge case: Check-in for expired membership → offer renewal

### Testing Milestone (End of Phase 3)
> ✅ Can create/recurring schedule for the week
> ✅ Customer can book a class (membership validated, capacity checked)
> ✅ Class roster shows correctly
> ✅ Check-in flow works: search → select → verify → confirm
> ✅ Membership classes decrement on check-in
> ✅ Cancelling a booking restores class count + capacity

---

## Phase 4: Analytics, Reports & Polish (Week 7–8)

**Goal:** Data-driven insights. Dashboard analytics, revenue reports, class popularity, teacher management.

### Deliverables

#### 4.1 Dashboard Analytics API
- [x] `GET /api/dashboard` — aggregate stats:
  - Revenue today, this month, this year
  - Attendance today (booked, checked in, capacity %)
  - New customers this month
  - Active membership count + breakdown
  - Upcoming classes today with live capacity
- [x] Revenue trend data (by day/month)
- [x] Attendance trend data (by day of week)

#### 4.2 Reports
- [x] `GET /api/reports/revenue?year=2026&group_by=month` — monthly revenue
- [x] `GET /api/reports/class-popularity` — attendance per class type
- [x] `GET /api/reports/customers?period=month` — new vs returning
- [x] Dashboard UI: Revenue chart (bar + line overlay)
- [x] Dashboard UI: Class popularity chart (horizontal bars)
- [x] Dashboard UI: New vs returning stacked bars
- [x] Dashboard UI: CSV export for all reports

#### 4.3 Teacher Management
- [x] `GET /api/teachers` — list with schedule counts
- [x] `POST /api/teachers` — add teacher
- [x] `PUT /api/teachers/{id}` — update bio, photo, specialties
- [x] `DELETE /api/teachers/{id}` — soft-delete
- [x] Dashboard UI: Teacher list with schedule load indicators
- [x] Dashboard UI: Teacher detail/edit panel

#### 4.4 Frontend Dashboard
- [x] Dashboard overview page: KPI cards + charts + membership breakdown
- [x] Live data wiring (no more mock data)
- [x] Reports page with tab switching and date ranges
- [x] Navigation improvements, loading states, error handling

### Testing Milestone (End of Phase 4)
> ✅ Dashboard shows real aggregate data from database
> ✅ Revenue report matches Stripe data
> ✅ Class popularity report shows accurate attendance rates
> ✅ CSV export produces valid files
> ✅ Teacher profiles fully manageable

---

## Phase 5: Migration, Testing & Launch (Week 9–12)

**Goal:** Migrate from bsport, thorough testing, performance optimization, production deployment.

### Deliverables

#### 5.1 Heat Lagos Migration
- [x] Export all customer data from bsport (CSV via admin panel)
- [x] Export all membership assignments and history
- [x] Export all scheduled classes (recurring pattern)
- [x] Write import scripts:
  - `scripts/import_customers.py`
  - `scripts/import_memberships.py`
  - `scripts/import_schedule.py`
  - `scripts/import_bookings.py`
- [x] Map bsport widget IDs to Csport entities
- [x] Verify data integrity (counts match, no orphans)
- [x] Set up Stripe product/price IDs matching membership tiers
- [x] Configure booking URL pattern: `/customer/payment/pass/{passId}/?membership={planId}&force=true`

#### 5.2 bsport Feature Gap Analysis
- [x] Audit bsport widget features against Csport (see [README.md feature map])
- [x] Implement any missing features:
  - Workshop handling (bsport-widget-404125)
  - Waitlist management (if needed)
  - Email notifications (booking confirmations, reminders)
  - Class cancellation notifications
- [x] Handle corner cases:
  - Membership auto-renewal
  - Early bird / late cancel penalties
  - Class substitutions (different teacher)
  - Free trials / promo memberships

#### 5.3 Testing
- [ ] Unit tests for all API endpoints (pytest + httpx async)
- [ ] Integration tests for booking/check-in logic
- [ ] Stripe webhook integration tests (local Stripe CLI)
- [ ] Load test: 50 concurrent booking requests
- [ ] Migration data validation: spot-check 20 random customers
- [ ] UI sanity: all 6 screens render, all CRUD flows work
- [ ] Edge case testing:
  - Book when full → error
  - Check in without booking → auto-book
  - Check in with expired membership → reject
  - Cancel class with bookings → all cancelled
  - Stripe webhook retries → idempotent

#### 5.4 Production Readiness
- [ ] Docker Compose for production stack
- [ ] CI/CD pipeline (GitHub Actions: test → build → deploy)
- [ ] PostgreSQL backup setup (daily + WAL archiving)
- [ ] Monitoring: server health, Stripe webhook logging, error alerts
- [ ] Rate limiting, request validation hardening
- [ ] SSL/HTTPS via Caddy or nginx
- [ ] Environment config for production secrets
- [ ] Admin authentication (JWT, password hashing)
- [ ] Audit logging (who did what, when)

#### 5.5 Launch
- [ ] Deploy to production (Fly.io / Railway / VPS)
- [ ] Run migration scripts on production database
- [ ] Verify bsport widget URLs redirect/point to Csport
- [ ] Parallel run: manual comparison of Csport data vs bsport for 1 week
- [ ] Cutover: update DNS, disable bsport widgets
- [ ] Post-launch monitoring (1 week watch)

### Testing Milestone (End of Phase 5)
> ✅ All customer data migrated correctly
> ✅ All membership histories preserved
> ✅ Booking system works end-to-end with real data
> ✅ Stripe payments flow correctly
> ✅ Dashboard reports match bsport's numbers
> ✅ Production deployment stable for 1 week
> ✅ bsport fully replaced

---

## Risk Register

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| Stripe webhook delivery delays | Booking delay | Low | Idempotency keys, retry queue |
| Migration data quality issues | Incorrect data | Medium | Validation scripts, manual spot-checks |
| Concurrent booking race conditions | Double-book | Low | DB unique constraint, optimistic locking |
| bsport API limitations for export | Incomplete data | Medium | Manual CSV export via admin panel |
| Customer confusion during cutover | Lost bookings | Low | Communication, parallel run period |

---

## Dependencies

- **Stripe account**: Active Stripe account with product/price IDs configured
- **PostgreSQL 15+**: For production database
- **Node.js 20+ / Python 3.11+**: Runtime
- **Docker**: For local development and production deployment
- **bsport admin access**: For data export

---

## Appendix A: Heat Lagos Specific Configuration

| Item | Value |
|------|-------|
| Plan ID (bsport) | 5821 |
| Schedule Widget ID | bsport-widget-177399 |
| Workshop Widget ID | bsport-widget-404125 |
| Booking URL pattern | `/customer/payment/pass/{passId}/?membership={planId}&force=true` |
| Teachers | Stine, Sebastian, Anastasiia, Agata |
| Locations (if any) | Heat Lagos (single location assumed) |

## Appendix B: Membership Pricing Table

| Name | Type | Price (€) | Duration | Classes | Stripe Price ID |
|------|------|-----------|----------|---------|-----------------|
| Intro Pass | subscription | 79 | 14 days | Unlimited | `price_intro` |
| 12-Month Unlimited | subscription | 125/mo | 365 days | Unlimited | `price_12mo` |
| 1-Month Unlimited | subscription | 160 | 30 days | Unlimited | `price_1mo` |
| Yearly Upfront | subscription | 1200 | 365 days | Unlimited | `price_yearly` |
| 10-Class Pack | package | 180 | — | 10 | `price_10pack` |
| Vacation Pass | subscription | 59 | 30 days | Unlimited | `price_vacation` |
| Drop-In | dropin | 22 | — | 1 | `price_dropin` |