# Csport Database Schema

## Overview

PostgreSQL relational schema for the Csport studio management dashboard. Designed for Heat Lagos (plan ID 5821) with support for memberships, class scheduling, bookings, check-ins, payments, and teacher management.

---

## Table: `memberships`

Membership tiers/passes that customers can purchase. Heat Lagos has 7 tiers: Intro, 12-month, 1-month, Yearly, 10-pack, Vacation, Drop-in.

```sql
CREATE TYPE membership_type AS ENUM ('subscription', 'package', 'dropin');

CREATE TABLE memberships (
    id            SERIAL PRIMARY KEY,
    name          VARCHAR(100) NOT NULL,
    type          membership_type NOT NULL,
    price_cents   INTEGER NOT NULL CHECK (price_cents >= 0),
    duration_days INTEGER CHECK (duration_days IS NULL OR duration_days > 0),
    class_limit   INTEGER CHECK (class_limit IS NULL OR class_limit > 0),
    description   TEXT,
    is_active     BOOLEAN NOT NULL DEFAULT TRUE,
    sort_order    INTEGER NOT NULL DEFAULT 0,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_memberships_type ON memberships (type);
CREATE INDEX idx_memberships_active ON memberships (is_active);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `name` | VARCHAR(100) | e.g. "Intro Pass", "12-Month Unlimited", "10-Class Pack" |
| `type` | ENUM | `subscription` (recurring), `package` (fixed classes), `dropin` (single visit) |
| `price_cents` | INTEGER | Price in euro-cents. Intro=7900, 1mo=16000, Yearly=120000, 10-pack=18000, Drop-in=2200 |
| `duration_days` | INTEGER | NULL for class packs/drop-ins. 14 for Intro, 365 for Yearly, 30 for Monthly |
| `class_limit` | INTEGER | NULL for unlimited. 10 for 10-pack, 1 for drop-in |
| `description` | TEXT | Human-readable description of what the pass includes |
| `is_active` | BOOLEAN | Soft-delete / hide from new purchases |
| `sort_order` | INTEGER | Display ordering on the purchase page |
| `created_at` | TIMESTAMPTZ | Row creation timestamp |
| `updated_at` | TIMESTAMPTZ | Row last-updated timestamp |

**Heat Lagos seed data:**

| Name | Type | Price (€) | Duration | Classes |
|------|------|-----------|----------|---------|
| Intro Pass | subscription | 79 | 14 days | unlimited |
| 12-Month Unlimited | subscription | 125/mo | 365 days | unlimited |
| 1-Month Unlimited | subscription | 160 | 30 days | unlimited |
| Yearly Upfront | subscription | 1200 | 365 days | unlimited |
| 10-Class Pack | package | 180 | NULL | 10 |
| Vacation Pass | subscription | 59 | 30 days | unlimited |
| Drop-In | dropin | 22 | NULL | 1 |

---

## Table: `customers`

Studio customers / members.

```sql
CREATE TABLE customers (
    id          SERIAL PRIMARY KEY,
    name        VARCHAR(200) NOT NULL,
    email       VARCHAR(255),
    phone       VARCHAR(50),
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    notes       TEXT,
    is_active   BOOLEAN NOT NULL DEFAULT TRUE,
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE UNIQUE INDEX idx_customers_email ON customers (email) WHERE email IS NOT NULL;
CREATE INDEX idx_customers_name ON customers USING gin (to_tsvector('english', name));
CREATE INDEX idx_customers_phone ON customers (phone) WHERE phone IS NOT NULL;
CREATE INDEX idx_customers_created ON customers (created_at DESC);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `name` | VARCHAR(200) | Customer's full name |
| `email` | VARCHAR(255) | Unique, used for Stripe lookup and login |
| `phone` | VARCHAR(50) | Contact number |
| `created_at` | TIMESTAMPTZ | When they first joined |
| `notes` | TEXT | Staff notes about the customer |
| `is_active` | BOOLEAN | Soft-delete flag |
| `updated_at` | TIMESTAMPTZ | Last update timestamp |

---

## Table: `customer_memberships`

Tracks which membership(s) a customer currently holds or has held. Supports multiple concurrent memberships (e.g. a class pack + an active subscription).

```sql
CREATE TYPE membership_status AS ENUM ('active', 'expired', 'cancelled', 'pending');

CREATE TABLE customer_memberships (
    id                SERIAL PRIMARY KEY,
    customer_id       INTEGER NOT NULL REFERENCES customers(id) ON DELETE CASCADE,
    membership_id     INTEGER NOT NULL REFERENCES memberships(id) ON DELETE RESTRICT,
    start_date        DATE NOT NULL,
    end_date          DATE,
    remaining_classes INTEGER CHECK (remaining_classes IS NULL OR remaining_classes >= 0),
    status            membership_status NOT NULL DEFAULT 'active',
    stripe_subscription_id VARCHAR(255),
    stripe_payment_intent_id VARCHAR(255),
    created_at        TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_cm_customer ON customer_memberships (customer_id);
CREATE INDEX idx_cm_status ON customer_memberships (status);
CREATE INDEX idx_cm_dates ON customer_memberships (start_date, end_date);
CREATE INDEX idx_cm_stripe_sub ON customer_memberships (stripe_subscription_id);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `customer_id` | FK→customers | Owning customer |
| `membership_id` | FK→memberships | The membership tier definition |
| `start_date` | DATE | When this membership became active |
| `end_date` | DATE | When it expires (NULL for class packs) |
| `remaining_classes` | INTEGER | Remaining classes for packages/drop-ins; NULL for unlimited |
| `status` | ENUM | `active`, `expired`, `cancelled`, `pending` |
| `stripe_subscription_id` | VARCHAR | Stripe subscription ID (for recurring memberships) |
| `stripe_payment_intent_id` | VARCHAR | Stripe payment intent ID (for one-time purchases) |

---

## Table: `class_types`

Types of classes offered. Heat Lagos offers: Pilates, Yoga, Sculpt, Flow, Mobility, Recovery, Yin.

```sql
CREATE TABLE class_types (
    id               SERIAL PRIMARY KEY,
    name             VARCHAR(100) NOT NULL UNIQUE,
    description      TEXT,
    duration_minutes INTEGER NOT NULL CHECK (duration_minutes > 0),
    color            VARCHAR(7) NOT NULL DEFAULT '#6366F1',
    icon             VARCHAR(50),
    is_active        BOOLEAN NOT NULL DEFAULT TRUE,
    sort_order       INTEGER NOT NULL DEFAULT 0,
    created_at       TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_class_types_active ON class_types (is_active);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `name` | VARCHAR(100) | e.g. "Pilates", "Yoga", "Sculpt" |
| `description` | TEXT | What this class type involves |
| `duration_minutes` | INTEGER | Typical duration (e.g. 50, 60) |
| `color` | VARCHAR(7) | Hex color for UI display (e.g. `#8B5CF6` for Pilates) |
| `icon` | VARCHAR(50) | Icon identifier for UI |
| `is_active` | BOOLEAN | Soft-delete |
| `sort_order` | INTEGER | Display order in schedule |

---

## Table: `teachers`

Instructors who teach classes.

```sql
CREATE TABLE teachers (
    id          SERIAL PRIMARY KEY,
    name        VARCHAR(200) NOT NULL,
    bio         TEXT,
    photo_url   VARCHAR(500),
    specialties VARCHAR(255),
    is_active   BOOLEAN NOT NULL DEFAULT TRUE,
    sort_order  INTEGER NOT NULL DEFAULT 0,
    created_at  TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at  TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_teachers_active ON teachers (is_active);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `name` | VARCHAR(200) | Full name |
| `bio` | TEXT | Instructor background |
| `photo_url` | VARCHAR(500) | Profile photo URL |
| `specialties` | VARCHAR(255) | Comma-separated specialties, e.g. "Pilates, Yoga, Mobility" |
| `is_active` | BOOLEAN | Soft-delete |

**Heat Lagos teachers:** Stine, Sebastian, Anastasiia, Agata

---

## Table: `schedule`

Individual class sessions on the calendar.

```sql
CREATE TABLE schedule (
    id            SERIAL PRIMARY KEY,
    class_type_id INTEGER NOT NULL REFERENCES class_types(id) ON DELETE RESTRICT,
    teacher_id    INTEGER NOT NULL REFERENCES teachers(id) ON DELETE RESTRICT,
    date          DATE NOT NULL,
    start_time    TIME NOT NULL,
    end_time      TIME NOT NULL CHECK (end_time > start_time),
    max_capacity  INTEGER NOT NULL CHECK (max_capacity > 0) DEFAULT 20,
    is_cancelled  BOOLEAN NOT NULL DEFAULT FALSE,
    notes         TEXT,
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_schedule_date ON schedule (date);
CREATE INDEX idx_schedule_class_type ON schedule (class_type_id);
CREATE INDEX idx_schedule_teacher ON schedule (teacher_id);
CREATE INDEX idx_schedule_datetime ON schedule (date, start_time);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `class_type_id` | FK→class_types | Which type of class |
| `teacher_id` | FK→teachers | Who is teaching |
| `date` | DATE | Session date |
| `start_time` | TIME | Start time (e.g. 07:00) |
| `end_time` | TIME | End time (e.g. 07:50) |
| `max_capacity` | INTEGER | Maximum attendees (defaults to 20) |
| `is_cancelled` | BOOLEAN | Cancel a session without deleting it |
| `notes` | TEXT | Any staff notes for this session |

---

## Table: `bookings`

When a customer books a spot in a scheduled class.

```sql
CREATE TYPE booking_status AS ENUM ('confirmed', 'checked_in', 'cancelled', 'no_show');

CREATE TABLE bookings (
    id              SERIAL PRIMARY KEY,
    schedule_id     INTEGER NOT NULL REFERENCES schedule(id) ON DELETE CASCADE,
    customer_id     INTEGER NOT NULL REFERENCES customers(id) ON DELETE CASCADE,
    customer_membership_id INTEGER REFERENCES customer_memberships(id) ON DELETE SET NULL,
    status          booking_status NOT NULL DEFAULT 'confirmed',
    booked_at       TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    checked_in_at   TIMESTAMPTZ,
    cancelled_at    TIMESTAMPTZ,
    created_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at      TIMESTAMPTZ NOT NULL DEFAULT NOW(),

    CONSTRAINT unique_booking UNIQUE (schedule_id, customer_id)
);

CREATE INDEX idx_bookings_schedule ON bookings (schedule_id);
CREATE INDEX idx_bookings_customer ON bookings (customer_id);
CREATE INDEX idx_bookings_status ON bookings (status);
CREATE INDEX idx_bookings_date ON bookings (booked_at DESC);
CREATE INDEX idx_bookings_checked_in ON bookings (checked_in_at) WHERE status = 'checked_in';
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `schedule_id` | FK→schedule | Which session |
| `customer_id` | FK→customers | Who booked |
| `customer_membership_id` | FK→customer_memberships | Which membership was consumed (NULL if dropped in without a pass) |
| `status` | ENUM | `confirmed`, `checked_in`, `cancelled`, `no_show` |
| `booked_at` | TIMESTAMPTZ | Timestamp of booking |
| `checked_in_at` | TIMESTAMPTZ | Timestamp of actual check-in |
| `cancelled_at` | TIMESTAMPTZ | Timestamp of cancellation |

**Constraints:**
- `UNIQUE (schedule_id, customer_id)` — prevents double-booking the same session

---

## Table: `payments`

Payment records, linked to Stripe for processing.

```sql
CREATE TYPE payment_method AS ENUM ('stripe', 'cash', 'transfer', 'free');
CREATE TYPE payment_status AS ENUM ('pending', 'completed', 'failed', 'refunded');

CREATE TABLE payments (
    id                  SERIAL PRIMARY KEY,
    customer_id         INTEGER NOT NULL REFERENCES customers(id) ON DELETE CASCADE,
    customer_membership_id INTEGER REFERENCES customer_memberships(id) ON DELETE SET NULL,
    amount_cents        INTEGER NOT NULL CHECK (amount_cents >= 0),
    method              payment_method NOT NULL DEFAULT 'stripe',
    status              payment_status NOT NULL DEFAULT 'pending',
    stripe_payment_intent_id VARCHAR(255) UNIQUE,
    stripe_subscription_id    VARCHAR(255),
    description         TEXT,
    created_at          TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at          TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_payments_customer ON payments (customer_id);
CREATE INDEX idx_payments_status ON payments (status);
CREATE INDEX idx_payments_created ON payments (created_at DESC);
CREATE INDEX idx_payments_stripe ON payments (stripe_payment_intent_id);
```

| Column | Type | Description |
|--------|------|-------------|
| `id` | SERIAL | Primary key |
| `customer_id` | FK→customers | Who paid |
| `customer_membership_id` | FK→customer_memberships | What they paid for (optional) |
| `amount_cents` | INTEGER | Amount in euro-cents |
| `method` | ENUM | `stripe`, `cash`, `transfer`, `free` |
| `status` | ENUM | `pending`, `completed`, `failed`, `refunded` |
| `stripe_payment_intent_id` | VARCHAR | Stripe payment intent reference |
| `stripe_subscription_id` | VARCHAR | Stripe subscription reference |
| `description` | TEXT | Human description of what was paid for |
| `created_at` | TIMESTAMPTZ | When payment was recorded |

---

## Entity Relationship Summary

```
memberships ──┐
               ├── customer_memberships ──┐
customers ─────┘                          │
               ┌──────────────────────────┘
               │
               ├── bookings ──── schedule ──── class_types
               │                               └── teachers
               └── payments
```

- `memberships` → `customer_memberships` → `customers` (many-to-many with metadata)
- `schedule` → `class_types` (what kind of class) + `teachers` (who teaches it)
- `bookings` joins `schedule` + `customers` + `customer_memberships`
- `payments` links `customers` + optional `customer_memberships`

---

## Migration Strategy

All tables use `TIMESTAMPTZ` (timezone-aware timestamps). Foreign keys use `ON DELETE CASCADE` for dependent data (bookings → schedule, customer_memberships → customer) and `ON DELETE RESTRICT` for reference data (schedule → class_types, schedule → teachers). Indexes are added for all foreign keys and common query patterns (date lookups, status filters, Stripe ID lookups).