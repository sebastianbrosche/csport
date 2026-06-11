# Csport REST API Specification

## Overview

RESTful JSON API for the Csport studio management dashboard. Built with Node.js/Express or Python/FastAPI. All endpoints return JSON. Authentication via Bearer token (JWT) for dashboard users. Webhook endpoints use Stripe signature verification.

**Base URL:** `https://csport.example.com/api` (production) / `http://localhost:8000/api` (development)

**Date format:** ISO 8601 (`YYYY-MM-DD`) for dates, ISO 8601 with timezone for timestamps.

**Currency:** All monetary values in euro-cents (`INTEGER`). Display as euros on the frontend.

---

## Table of Contents

1. [Dashboard](#1-dashboard)
2. [Customers](#2-customers)
3. [Memberships](#3-memberships)
4. [Customer Memberships](#4-customer-memberships)
5. [Class Types](#5-class-types)
6. [Teachers](#6-teachers)
7. [Schedule](#7-schedule)
8. [Bookings](#8-bookings)
9. [Check-in](#9-check-in)
10. [Payments](#10-payments)
11. [Stripe Webhook](#11-stripe-webhook)
12. [Reports](#12-reports)

---

## 1. Dashboard

### GET /dashboard

Returns summary statistics for the dashboard overview page.

**Response `200 OK`:**

```json
{
  "revenue_today": {
    "amount_cents": 245000,
    "count": 14
  },
  "revenue_this_month": {
    "amount_cents": 4850000,
    "count": 312
  },
  "revenue_this_year": {
    "amount_cents": 52400000,
    "count": 3890
  },
  "attendance_today": {
    "booked": 18,
    "checked_in": 14,
    "capacity_percent": 72
  },
  "attendance_this_week": {
    "total_bookings": 124,
    "total_checkins": 98,
    "avg_capacity_percent": 68
  },
  "new_customers_this_month": 23,
  "total_active_memberships": 187,
  "membership_breakdown": [
    { "name": "12-Month Unlimited", "count": 45, "revenue_cents": 562500 },
    { "name": "1-Month Unlimited", "count": 38, "revenue_cents": 608000 },
    { "name": "10-Class Pack", "count": 52, "revenue_cents": 936000 },
    { "name": "Intro Pass", "count": 28, "revenue_cents": 221200 },
    { "name": "Drop-In", "count": 15, "revenue_cents": 33000 },
    { "name": "Vacation Pass", "count": 5, "revenue_cents": 29500 },
    { "name": "Yearly Upfront", "count": 4, "revenue_cents": 480000 }
  ],
  "upcoming_classes_today": [
    {
      "id": 1,
      "class_type": "Pilates",
      "teacher": "Stine",
      "time": "07:00",
      "booked": 16,
      "capacity": 20,
      "color": "#8B5CF6"
    }
  ]
}
```

---

## 2. Customers

### GET /customers

List customers with search and pagination.

**Query Parameters:**
| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `search` | string | — | Search by name, email, or phone |
| `status` | enum | `all` | `all`, `active`, `inactive` |
| `page` | int | 1 | Page number |
| `per_page` | int | 25 | Items per page (max 100) |
| `sort_by` | string | `created_at` | `name`, `created_at`, `email` |
| `sort_order` | string | `desc` | `asc`, `desc` |

**Response `200 OK`:**

```json
{
  "data": [
    {
      "id": 1,
      "name": "Jane Doe",
      "email": "jane@example.com",
      "phone": "+491234567890",
      "created_at": "2026-01-15T10:30:00Z",
      "notes": "Prefers morning classes",
      "is_active": true,
      "active_memberships": [
        { "id": 3, "name": "10-Class Pack", "remaining_classes": 4, "status": "active" }
      ]
    }
  ],
  "pagination": {
    "page": 1,
    "per_page": 25,
    "total": 312,
    "total_pages": 13
  }
}
```

### POST /customers

Create a new customer.

**Request Body:**

```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "phone": "+491234567890",
  "notes": "Prefers morning classes"
}
```

**Response `201 Created`:** Returns the created customer object.

### GET /customers/{id}

Get a single customer with full details.

**Response `200 OK`:**

```json
{
  "id": 1,
  "name": "Jane Doe",
  "email": "jane@example.com",
  "phone": "+491234567890",
  "created_at": "2026-01-15T10:30:00Z",
  "notes": "Prefers morning classes",
  "is_active": true,
  "memberships": [
    {
      "id": 3,
      "membership": { "id": 4, "name": "10-Class Pack" },
      "start_date": "2026-06-01",
      "end_date": null,
      "remaining_classes": 4,
      "status": "active"
    }
  ],
  "recent_bookings": [
    {
      "id": 42,
      "schedule": {
        "id": 101,
        "class_type": "Pilates",
        "date": "2026-06-11",
        "start_time": "07:00"
      },
      "status": "confirmed",
      "booked_at": "2026-06-10T14:00:00Z"
    }
  ],
  "payments": [
    {
      "id": 88,
      "amount_cents": 18000,
      "method": "stripe",
      "status": "completed",
      "created_at": "2026-06-01T09:00:00Z"
    }
  ]
}
```

### PUT /customers/{id}

Update a customer.

**Request Body** (partial update):

```json
{
  "name": "Jane Smith",
  "phone": "+491111222333",
  "notes": "Updated contact info"
}
```

**Response `200 OK`:** Returns updated customer object.

### DELETE /customers/{id}

Soft-delete a customer (marks `is_active = false`).

**Response `204 No Content`.**

---

## 3. Memberships

### GET /memberships

List all membership tiers.

**Response `200 OK`:**

```json
{
  "data": [
    {
      "id": 1,
      "name": "Intro Pass",
      "type": "subscription",
      "price_cents": 7900,
      "duration_days": 14,
      "class_limit": null,
      "description": "Two-week unlimited intro pass",
      "is_active": true,
      "sort_order": 1
    }
  ]
}
```

### POST /memberships

Create a new membership tier.

**Request Body:**

```json
{
  "name": "Summer Special",
  "type": "package",
  "price_cents": 15000,
  "duration_days": null,
  "class_limit": 8,
  "description": "8-class summer pack"
}
```

**Response `201 Created`.**

### PUT /memberships/{id}

Update a membership tier.

**Response `200 OK`.**

### DELETE /memberships/{id}

Soft-delete (sets `is_active = false`). No hard deletes.

**Response `204 No Content`.**

---

## 4. Customer Memberships

### GET /customers/{customer_id}/memberships

List all memberships for a customer.

**Response `200 OK`:**

```json
{
  "data": [
    {
      "id": 5,
      "membership": { "id": 4, "name": "10-Class Pack" },
      "start_date": "2026-06-01",
      "end_date": null,
      "remaining_classes": 4,
      "status": "active"
    }
  ]
}
```

### POST /customer-memberships

Assign a membership to a customer (after purchase).

**Request Body:**

```json
{
  "customer_id": 1,
  "membership_id": 4,
  "start_date": "2026-06-01",
  "end_date": null,
  "remaining_classes": 10,
  "status": "active",
  "stripe_payment_intent_id": "pi_3MqL3qLkdIwz7FUI1C"
}
```

**Response `201 Created`.**

---

## 5. Class Types

### GET /class-types

List all class types.

**Response `200 OK`:**

```json
{
  "data": [
    { "id": 1, "name": "Pilates", "duration_minutes": 50, "color": "#8B5CF6", "icon": "pilates", "is_active": true },
    { "id": 2, "name": "Yoga", "duration_minutes": 60, "color": "#10B981", "icon": "yoga", "is_active": true },
    { "id": 3, "name": "Sculpt", "duration_minutes": 50, "color": "#F59E0B", "icon": "sculpt", "is_active": true },
    { "id": 4, "name": "Flow", "duration_minutes": 45, "color": "#EC4899", "icon": "flow", "is_active": true },
    { "id": 5, "name": "Mobility", "duration_minutes": 50, "color": "#6366F1", "icon": "mobility", "is_active": true },
    { "id": 6, "name": "Recovery", "duration_minutes": 45, "color": "#14B8A6", "icon": "recovery", "is_active": true },
    { "id": 7, "name": "Yin", "duration_minutes": 60, "color": "#8B5CF6", "icon": "yin", "is_active": true }
  ]
}
```

### POST /class-types

**Request Body:**

```json
{
  "name": "HIIT",
  "duration_minutes": 45,
  "color": "#EF4444",
  "icon": "hiit",
  "description": "High intensity interval training"
}
```

**Response `201 Created`.**

### PUT /class-types/{id} / DELETE /class-types/{id}

Standard CRUD. Delete sets `is_active = false`.

---

## 6. Teachers

### GET /teachers

```json
{
  "data": [
    {
      "id": 1,
      "name": "Stine",
      "bio": "Founder and lead instructor with 10+ years experience",
      "photo_url": "/images/teachers/stine.jpg",
      "specialties": "Pilates, Yoga, Sculpt",
      "is_active": true
    },
    {
      "id": 2,
      "name": "Sebastian",
      "bio": "Strength and mobility specialist",
      "photo_url": "/images/teachers/sebastian.jpg",
      "specialties": "Sculpt, Mobility, Flow",
      "is_active": true
    },
    {
      "id": 3,
      "name": "Anastasiia",
      "bio": "Yoga and mindfulness instructor",
      "photo_url": "/images/teachers/anastasiia.jpg",
      "specialties": "Yoga, Yin, Recovery",
      "is_active": true
    },
    {
      "id": 4,
      "name": "Agata",
      "bio": "Pilates and movement coach",
      "photo_url": "/images/teachers/agata.jpg",
      "specialties": "Pilates, Flow, Mobility",
      "is_active": true
    }
  ]
}
```

### POST/PUT/DELETE /teachers

Standard CRUD. Delete sets `is_active = false`.

---

## 7. Schedule

### GET /schedule

Get schedule for a date range. Default: today + 7 days.

**Query Parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `date` | string (YYYY-MM-DD) | today | Single date or start date |
| `end_date` | string (YYYY-MM-DD) | date+6 | End date for range |
| `class_type_id` | int | — | Filter by class type |
| `teacher_id` | int | — | Filter by teacher |

**Response `200 OK`:**

```json
{
  "data": [
    {
      "id": 101,
      "date": "2026-06-11",
      "start_time": "07:00",
      "end_time": "07:50",
      "class_type": { "id": 1, "name": "Pilates", "color": "#8B5CF6", "duration_minutes": 50 },
      "teacher": { "id": 1, "name": "Stine" },
      "max_capacity": 20,
      "booked_count": 16,
      "checked_in_count": 14,
      "available_spots": 4,
      "is_cancelled": false
    }
  ]
}

```

### POST /schedule

Create a scheduled class.

**Request Body:**

```json
{
  "class_type_id": 1,
  "teacher_id": 1,
  "date": "2026-06-12",
  "start_time": "07:00",
  "end_time": "07:50",
  "max_capacity": 20
}
```

**Response `201 Created`.**

### PUT /schedule/{id}

Update a scheduled class (can't update if bookings exist without flag).

**Response `200 OK`.**

### DELETE /schedule/{id}

Cancel a class. Sets `is_cancelled = true` and cancels all bookings.

**Response `204 No Content`.**

---

## 8. Bookings

### GET /bookings

List bookings with filters.

**Query Parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `schedule_id` | int | Filter by session |
| `customer_id` | int | Filter by customer |
| `date` | string | Filter by date |
| `status` | string | `confirmed`, `checked_in`, `cancelled`, `no_show` |

### POST /bookings

Book a class for a customer. Validates membership and capacity.

**Request Body:**

```json
{
  "customer_id": 1,
  "schedule_id": 101,
  "customer_membership_id": 5
}
```

**Validation Rules (server-side):**
1. ✅ Customer exists and is active
2. ✅ Schedule exists and is not cancelled
3. ✅ Schedule hasn't already started
4. ✅ Customer is not already booked for this session
5. ✅ Available spots > 0
6. ✅ Customer membership is active and has remaining classes (if applicable)
7. ✅ No conflicting bookings at same time

**Response `201 Created`:**

```json
{
  "id": 42,
  "customer": { "id": 1, "name": "Jane Doe" },
  "schedule": {
    "id": 101,
    "class_type": "Pilates",
    "date": "2026-06-11",
    "start_time": "07:00",
    "end_time": "07:50"
  },
  "membership_used": { "id": 5, "name": "10-Class Pack", "remaining_classes_after": 3 },
  "status": "confirmed",
  "booked_at": "2026-06-10T14:00:00Z"
}
```

**Error Responses:**

```json
{ "error": "schedule_full", "message": "This class is at full capacity (20/20 booked)" }
{ "error": "no_membership", "message": "Customer has no active membership covering this class" }
{ "error": "already_booked", "message": "Customer is already booked for this session" }
{ "error": "conflicting_booking", "message": "Customer has another booking at the same time" }
```

### PUT /bookings/{id}

Update a booking (e.g. change membership used, or to mark as no-show).

### DELETE /bookings/{id}

Cancel a booking. Restores class capacity and class pack count (if package).

**Response `204 No Content`.**

---

## 9. Check-in

### POST /checkin

Check a customer into a scheduled class. Marks booking as `checked_in`, decrements membership class count if applicable.

**Request Body:**

```json
{
  "customer_id": 1,
  "schedule_id": 101
}
```

**Alternative — search by email/phone:**

```json
{
  "email": "jane@example.com",
  "schedule_id": 101
}
```

**Response `200 OK`:**

```json
{
  "booking_id": 42,
  "customer": { "id": 1, "name": "Jane Doe" },
  "class": { "name": "Pilates", "date": "2026-06-11", "time": "07:00" },
  "checked_in_at": "2026-06-11T06:55:00Z",
  "membership_status": { "name": "10-Class Pack", "remaining_classes": 3 }
}
```

**Edge Cases:**
- If customer has no booking for this session → auto-create a booking using their active membership (or prompt to purchase)
- If membership has 0 remaining classes → reject with error

---

## 10. Payments

### GET /payments

List all payments.

**Query Parameters:**

| Param | Type | Description |
|-------|------|-------------|
| `customer_id` | int | Filter by customer |
| `status` | string | `pending`, `completed`, `failed`, `refunded` |
| `date_from` | string | Start of date range |
| `date_to` | string | End of date range |
| `page` | int | Pagination |
| `per_page` | int | Items per page |

### POST /payments

Record a manual payment (cash/transfer).

**Request Body:**

```json
{
  "customer_id": 1,
  "customer_membership_id": 5,
  "amount_cents": 18000,
  "method": "cash",
  "status": "completed",
  "description": "10-Class Pack - cash payment"
}
```

**Response `201 Created`.**

### GET /payments/{id}

Get payment details.

**Response `200 OK`:** Returns payment object with customer info.

### POST /payments/stripe-webhook

Stripe webhook endpoint — called by Stripe when events occur.

**Stripe Events Handled:**

| Event | Action |
|-------|--------|
| `payment_intent.succeeded` | Create `payments` record, activate `customer_memberships` |
| `payment_intent.payment_failed` | Mark payment as failed |
| `invoice.payment_succeeded` | Renew subscription membership |
| `invoice.payment_failed` | Mark membership for suspension |
| `customer.subscription.updated` | Sync subscription changes |
| `customer.subscription.deleted` | Cancel membership |

**Request:** Stripe-signed webhook payload (raw JSON body with `Stripe-Signature` header).

**Response `200 OK`:**

```json
{ "received": true }
```

**Response `400 Bad Request`:** If signature verification fails.

---

## 11. Reports

### GET /reports/revenue

Revenue by month.

**Query Parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `year` | int | current | Year to report on |
| `group_by` | string | `month` | `month`, `week`, `day`, `membership_type` |

**Response `200 OK`:**

```json
{
  "year": 2026,
  "data": [
    { "period": "2026-01", "revenue_cents": 4200000, "transaction_count": 45 },
    { "period": "2026-02", "revenue_cents": 3850000, "transaction_count": 40 },
    { "period": "2026-03", "revenue_cents": 5100000, "transaction_count": 52 },
    { "period": "2026-04", "revenue_cents": 4800000, "transaction_count": 48 },
    { "period": "2026-05", "revenue_cents": 4950000, "transaction_count": 50 },
    { "period": "2026-06", "revenue_cents": 2250000, "transaction_count": 24 }
  ]
}
```

### GET /reports/class-popularity

Most and least attended class types.

**Response `200 OK`:**

```json
{
  "data": [
    { "class_type": "Pilates", "total_bookings": 312, "avg_attendance": 17.3, "capacity_percent": 86.5 },
    { "class_type": "Sculpt", "total_bookings": 245, "avg_attendance": 15.1, "capacity_percent": 75.5 },
    { "class_type": "Yoga", "total_bookings": 198, "avg_attendance": 12.4, "capacity_percent": 62.0 },
    { "class_type": "Flow", "total_bookings": 187, "avg_attendance": 14.2, "capacity_percent": 71.0 },
    { "class_type": "Mobility", "total_bookings": 134, "avg_attendance": 11.8, "capacity_percent": 59.0 },
    { "class_type": "Recovery", "total_bookings": 89, "avg_attendance": 9.5, "capacity_percent": 47.5 },
    { "class_type": "Yin", "total_bookings": 112, "avg_attendance": 10.2, "capacity_percent": 51.0 }
  ]
}
```

### GET /reports/customers

New vs returning customer analysis.

**Query Parameters:**

| Param | Type | Default | Description |
|-------|------|---------|-------------|
| `period` | string | `month` | `month`, `quarter`, `year` |
| `from` | date | 6 months ago | Start date |
| `to` | date | today | End date |

**Response `200 OK`:**

```json
{
  "data": [
    { "period": "2026-01", "new_customers": 18, "returning_customers": 142, "retention_rate": 88.8 },
    { "period": "2026-02", "new_customers": 15, "returning_customers": 138, "retention_rate": 90.2 },
    { "period": "2026-03", "new_customers": 22, "returning_customers": 155, "retention_rate": 87.6 },
    { "period": "2026-04", "new_customers": 20, "returning_customers": 148, "retention_rate": 88.1 },
    { "period": "2026-05", "new_customers": 25, "returning_customers": 160, "retention_rate": 86.5 },
    { "period": "2026-06", "new_customers": 12, "returning_customers": 85, "retention_rate": 87.6 }
  ]
}
```

---

## Error Response Format

All errors follow a consistent format:

```json
{
  "error": "error_code",
  "message": "Human-readable message",
  "details": {}  // Optional validation details
}
```

**HTTP Status Codes:**

| Code | Description |
|------|-------------|
| 200 | Success |
| 201 | Created |
| 204 | No Content (delete success) |
| 400 | Bad Request (validation error) |
| 401 | Unauthorized (missing/invalid token) |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict (duplicate booking, etc.) |
| 422 | Unprocessable Entity (business logic failure) |
| 500 | Internal Server Error |

---

## Rate Limiting

- 100 requests per minute per IP for dashboard API
- Webhook endpoints exempt from rate limiting
- Response headers: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`