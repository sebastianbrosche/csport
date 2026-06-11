# Csport Dashboard Screen Mockups

## Overview

Text-based descriptions of each major screen in the Csport studio management dashboard. Designed for a dark-themed, clean, data-dense UI optimized for desktop use. No mobile app — this is a single-page dashboard application.

---

## 1. Dashboard Overview

### Purpose
At-a-glance summary of studio health: revenue, attendance, and membership metrics. This is the default landing page.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                                   🔔 👤 Stine        [Logout] │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]        ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  ┌─────────────┬─────────────┬─────────────┬─────────────┐              │
│  │ Revenue     │ Attendance  │ New Members │ Active Mems │              │
│  │ Today       │ Today       │ This Month  │             │              │
│  │ €2,450      │ 14/20 (72%) │ 23          │ 187         │              │
│  │ ▲ 12% vs wk │ 4 classes   │ ▲ 8% vs mo  │             │              │
│  └─────────────┴─────────────┴─────────────┴─────────────┘              │
│                                                                         │
│  ┌───────────────────────────────────────┬──────────────────────────────┐│
│  │ Revenue Trend (This Month)            │ Attendance by Day (Week)    ││
│  │ ┌─────────────────────────────────┐   │ ┌────────────────────────┐  ││
│  │ │  ██                               │   │ │ ██ ██ ██ ██ ██       │  ││
│  │ │  ██ ██                            │   │ │ ██ ██ ██ ██ ██ ██    │  ││
│  │ │  ██ ██ ██ ██    ██               │   │ │ ██ ██ ██ ██ ██ ██ ██ │  ││
│  │ │  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  │   │ │ M  T  W  T  F  S  S  │  ││
│  │ │  1  2  3  4  5  6  7  8  9 10... │   │ └────────────────────────┘  ││
│  │ └─────────────────────────────────┘   │ €4,850 / €8,000 target     ││
│  └───────────────────────────────────────┴──────────────────────────────┘│
│                                                                         │
│  ┌───────────────────────────────────────┬──────────────────────────────┐│
│  │ Membership Breakdown                 │ Upcoming Classes Today      ││
│  │                                       │                              ││
│  │ ■ 12-Month  (45)  ████████████       │ 07:00 Pilates   Stine 16/20 ││
│  │ ■ 1-Month    (38)  ██████████        │ 08:00 Yoga      Ana   12/20 ││
│  │ ■ 10-Pack    (52)  ██████████████    │ 09:00 Sculpt    Seb   18/20 ││
│  │ ■ Intro      (28)  ███████           │ 10:00 Mobility  Agata  8/20 ││
│  │ ■ Drop-In    (15)  ████              │                              ││
│  │ ■ Vacation    (5)  █                 │ [View All →]                 ││
│  │ ■ Yearly      (4)  █                 │                              ││
│  │                                       │                              ││
│  │ Total: 187 active memberships        │                              ││
│  └───────────────────────────────────────┴──────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Metrics
- **Revenue Today:** Current day revenue with week-over-week comparison
- **Attendance Today:** Checked-in / booked / capacity %
- **New Members:** New customers this month with trend
- **Active Memberships:** Total active customer_memberships
- **Revenue Trend:** Bar chart — daily revenue for current month vs target line
- **Attendance by Day:** Weekly bar chart — total check-ins per day of week
- **Membership Breakdown:** Stacked horizontal bar chart with counts
- **Upcoming Classes:** Today's class list with live capacity indicators

### User Actions
- Click any metric card to navigate to detailed view
- Click a class row → navigate to that schedule session
- Click "View All" on upcoming classes → Schedule page
- Click revenue chart point → filtered payments view

---

## 2. Schedule Management

### Purpose
Weekly class calendar with drag-to-book and capacity management. Replace bsport's widget-based schedule.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                       Schedule                    [+ New Class]│
│  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]            │
│                                                                         │
│  ◀ Mon Jun 8 — Sun Jun 14, 2026 ▶      [Today] [Week] [Month]         │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │       │ Mon 8  │ Tue 9  │ Wed 10 │ Thu 11 │ Fri 12 │ Sat 13 │ Sun│
│  ├───────┼────────┼────────┼────────┼────────┼────────┼────────┼────┤│
│  │ 07:00 │ █████  │ █████  │ █████  │ █████  │ █████  │        │    ││
│  │       │Pilates │Pilates │Pilates │Pilates │Pilates │        │    ││
│  │       │Stine   │Stine   │Stine   │Stine   │Stine   │        │    ││
│  │       │16/20 ██│18/20 ██│14/20 ██│12/20 ██│20/20 ██│        │    ││
│  ├───────┼────────┼────────┼────────┼────────┼────────┼────────┼────┤│
│  │ 08:00 │ █████  │ █████  │ █████  │ █████  │ █████  │ █████  │    ││
│  │       │Yoga    │Sculpt  │Yoga    │Sculpt  │Yoga    │Yoga    │    ││
│  │       │Ana     │Seb     │Ana     │Seb     │Ana     │Ana     │    ││
│  │       │12/20 ██│15/20 ██│10/20 ██│18/20 ██│14/20 ██│ 8/20 ██│    ││
│  ├───────┼────────┼────────┼────────┼────────┼────────┼────────┼────┤│
│  │ 09:00 │ █████  │ █████  │ █████  │ █████  │ █████  │ █████  │    ││
│  │       │Sculpt  │Flow    │Sculpt  │Flow    │Sculpt  │Sculpt  │    ││
│  │       │Seb     │Agata   │Seb     │Agata   │Seb     │Seb     │    ││
│  │       │18/20 ██│10/20 ██│16/20 ██│14/20 ██│19/20 ██│12/20 ██│    ││
│  ├───────┼────────┼────────┼────────┼────────┼────────┼────────┼────┤│
│  │ 10:00 │ █████  │ █████  │ █████  │        │ █████  │        │    ││
│  │       │Mobility│Recovery│Mobility│        │Recovery│        │    ││
│  │       │Agata   │Ana     │Agata   │        │Ana     │        │    ││
│  │       │ 8/20 ██│ 6/20 ██│10/20 ██│        │ 9/20 ██│        │    ││
│  ├───────┼────────┼────────┼────────┼────────┼────────┼────────┼────┤│
│  │ 17:00 │ █████  │ █████  │ █████  │ █████  │ █████  │        │    ││
│  │       │Yin     │Pilates │Yin     │Pilates │Yin     │        │    ││
│  │       │Ana     │Stine   │Ana     │Stine   │Ana     │        │    ││
│  │       │ 7/20 ██│15/20 ██│ 9/20 ██│14/20 ██│11/20 ██│        │    ││
│  └───────┴────────┴────────┴────────┴────────┴────────┴────────┴────┘│
│                                                                         │
│  Legend:                                                                │
│  ■ Pilates  ■ Yoga  ■ Sculpt  ■ Flow  ■ Mobility  ■ Recovery  ■ Yin   │
│  ██ Capacity bar: ██ < 50%  ██ 50-80%  ██ > 80%  ██ Full             │
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Data
- **Weekly grid:** Time rows (06:00–21:00) × Day columns
- **Class tiles:** Colored by class type, show teacher name, capacity bar
- **Capacity indicator:** Color-coded bar — green (<50%), yellow (50-80%), orange (>80%), red (full)
- **Today highlight:** Current day column highlighted

### User Actions
- Click on empty time slot → "New Class" modal (select type, teacher, capacity)
- Click existing class → Edit/View class detail (see roster, add notes, cancel)
- Click capacity bar → see booking roster (who's booked, checked in)
- Drag class tile → reschedule (with conflict checking)
- Filter bar: toggle class types, filter by teacher
- Navigation: arrow buttons or date picker to move between weeks
- "Today" button jumps to current week
- Right-click class → cancel class (with confirmation, notifies booked customers)

---

## 3. Customer Management

### Purpose
Searchable, filterable customer database. Quick access to membership status and booking history.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                       Customers                 [+ New Customer]│
│  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]            │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │ 🔍 Search by name, email, or phone...  [All] ▼  [Sort: Newest ▼]  ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  ┌────┬──────────┬──────────────┬───────────┬────────────┬────────────┐│
│  │ ID │ Name     │ Email        │ Phone     │ Memberships│ Last Visit ││
│  ├────┼──────────┼──────────────┼───────────┼────────────┼────────────┤│
│  │ 42 │ Jane Doe │ jane@e...    │ +49 123.. │ 10-Pack(4) │ Jun 10     ││
│  │    │          │              │           │ ████████░░ │            ││
│  │ 43 │ John S.  │ john@e...    │ +49 456.. │ 1-Mo(Act)  │ Jun 8      ││
│  │    │          │              │           │ ██████████ │            ││
│  │ 44 │ Alice M. │ alice@e...   │ +49 789.. │ —          │ Never      ││
│  │    │          │              │           │ [Assign]   │            ││
│  │ 45 │ Bob K.   │ bob@e...     │ +49 012.. │ Drop-In(0) │ Jun 5      ││
│  │    │          │              │           │ ██████████ │            ││
│  │ ...│ ...      │ ...          │ ...       │ ...        │ ...        ││
│  └────┴──────────┴──────────────┴───────────┴────────────┴────────────┘│
│                                                                         │
│  Showing 1-25 of 312 customers                ◀ 1 2 3 ... 13 ▶         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Customer Detail Panel (slide-in / modal)

```
┌─────────────────────────────────────────────────────────────────────────┐
│  [← Back]  Jane Doe                           [Edit] [Delete]          │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │ 📧 jane@example.com  📞 +49 123 456 7890                           ││
│  │ 🗓 Joined: Jan 15, 2026                                            ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  Active Memberships:                                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │ 10-Class Pack  │ Purchased Jun 1 │ 4 / 10 remaining  │ Active   ██││
│  │                │                 │                    │ [Extend] │││
│  │ 1-Month Unlim. │ Purchased May 1│ Expires Jun 1      │ Expired  ░░││
│  │                │                 │                    │ [Renew]  │││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  Recent Bookings (5):                                                  │
│  ┌──────┬──────────┬────────┬──────────┬──────────┬──────────────────┐│
│  │ Date │ Class    │ Time   │ Teacher  │ Status   │                  ││
│  ├──────┼──────────┼────────┼──────────┼──────────┤                  ││
│  │Jun 10│ Pilates  │ 07:00  │ Stine    │ CheckedIn│ ✅               ││
│  │Jun 8 │ Sculpt   │ 09:00  │ Seb      │ NoShow   │ ❌               ││
│  │Jun 5 │ Yoga     │ 08:00  │ Ana      │ CheckedIn│ ✅               ││
│  │Jun 3 │ Flow     │ 09:00  │ Agata    │ Cancelled│ ↪                ││
│  │Jun 1 │ Pilates  │ 07:00  │ Stine    │ CheckedIn│ ✅               ││
│  └──────┴──────────┴────────┴──────────┴──────────┴──────────────────┘│
│                                                                         │
│  Payment History:                                                       │
│  ┌──────┬──────────┬───────┬────────┬─────────────────────────────────┐│
│  │ Date │ Amount   │ Method│ Status │ Description                     ││
│  ├──────┼──────────┼───────┼────────┼─────────────────────────────────┤│
│  │Jun 1 │ €180.00  │ Stripe│ ✅ Paid│ 10-Class Pack                   ││
│  │May 1 │ €160.00  │ Stripe│ ✅ Paid│ 1-Month Unlimited               ││
│  └──────┴──────────┴───────┴────────┴─────────────────────────────────┘│
│                                                                         │
│  Notes:                                                                 │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │ Prefers morning classes. Has back issues — avoid certain mat      ││
│  │ exercises.                                                         ││
│  │ [Save Notes]                                                       ││
│  └─────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Data
- **Customer table:** ID, Name, Email, Phone, Membership status (with progress bar for class packs), Last visit date
- **Customer Detail:** Full profile, all memberships (active + expired), booking history, payment history, notes

### User Actions
- Search: instant filter by name, email, or phone
- Filter dropdown: All / Active / Inactive / Expiring Soon
- Sort: Newest / Oldest / A-Z / Z-A / Most Visits
- Click row → slide-in detail panel
- "New Customer" button → create form
- "Assign" button for customers without membership → purchase flow
- "Renew" / "Extend" buttons on memberships
- Edit notes inline and save

---

## 4. Membership Management

### Purpose
CRUD interface for membership tiers. Manage pricing, duration, class limits, and display order.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                      Memberships                [+ New Tier]   │
│  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]            │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Membership Tiers                              [Save Order] [▲▼]  ││
│  ├─────────────────────────────────────────────────────────────────────┤│
│  │                                                                     ││
│  │  ═══ Subscriptions ═══════════════════════════════════════════════  ││
│  │                                                                     ││
│  │  ┌─────────┬──────────┬──────────┬────────┬────────┬───────────┬──┐││
│  │  │ Name    │ Price    │ Duration │ Type   │ Active │ Sales     │  │││
│  │  ├─────────┼──────────┼──────────┼────────┼────────┼───────────┤  │││
│  │  │Intro    │ €79      │ 14 days  │Subscr. │ ✅     │ 28 this mo│  │││
│  │  │12-Month │ €125/mo  │ 365 days │Subscr. │ ✅     │ 45 active │  │││
│  │  │1-Month  │ €160     │ 30 days  │Subscr. │ ✅     │ 38 active │  │││
│  │  │Yearly   │ €1200    │ 365 days │Subscr. │ ✅     │ 4 active  │  │││
│  │  │Vacation │ €59      │ 30 days  │Subscr. │ ✅     │ 5 active  │  │││
│  │  └─────────┴──────────┴──────────┴────────┴────────┴───────────┴──┘││
│  │                                                                     ││
│  │  ═══ Packages ═══════════════════════════════════════════════════   ││
│  │  ┌─────────┬──────────┬──────────┬────────┬────────┬───────────┬──┐││
│  │  │ 10-Pack │ €180     │ —        │Package │ ✅     │ 52 active │  │││
│  │  └─────────┴──────────┴──────────┴────────┴────────┴───────────┴──┘││
│  │                                                                     ││
│  │  ═══ Drop-Ins ═══════════════════════════════════════════════════   ││
│  │  ┌─────────┬──────────┬──────────┬────────┬────────┬───────────┬──┐││
│  │  │ Drop-In │ €22      │ —        │Drop-in │ ✅     │ 15 this mo│  │││
│  │  └─────────┴──────────┴──────────┴────────┴────────┴───────────┴──┘││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Edit: 10-Class Pack                                                ││
│  │                                                                     ││
│  │  Name:         [10-Class Pack                    ]                  ││
│  │  Type:         [Package ▼]                                          ││
│  │  Price (€):    [180.00        ]                                     ││
│  │  Duration (d): [               ] (leave blank for no expiry)        ││
│  │  Class Limit:  [10             ]                                    ││
│  │  Description:  [10 classes to use any time. No expiry.     ]        ││
│  │  Active:       [x] Sort Order: [3                                   ││
│  │                                                                     ││
│  │  [Cancel] [Save Changes] [Deactivate Tier]                          ││
│  └─────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Data
- **Grouped by type:** Subscriptions, Packages, Drop-ins
- **Each tier:** Name, Price, Duration, Type badge, Active toggle, Sales count (active memberships this period)
- **Edit panel:** Full form for all fields

### User Actions
- Click any tier → edit panel opens (inline or slide-in)
- "New Tier" button → create form with same fields
- Toggle Active/Inactive switch
- Drag handle (≡) to reorder tiers, "Save Order" button persists
- "Deactivate Tier" — soft-delete, preserves existing memberships
- Price editing updates future purchases only (grandfathering note in UI)

---

## 5. Check-In Flow

### Purpose
Fast check-in for walk-in customers at the front desk. Search, select, verify membership, confirm.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                       Check-In                                 │
│  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]            │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │                        🔍 Check-In                                 ││
│  │                                                                     ││
│  │  ┌─────────────────────────────────────────────────────────────────┐││
│  │  │ [🔍 Search by name, email, or phone...                    ]     │││
│  │  │                               or                                │││
│  │  │ [Scan QR Code / Barcode]                                       │││
│  │  └─────────────────────────────────────────────────────────────────┘││
│  │                                                                     ││
│  │  ──── Quick Select: Today's Schedule ────────────────────────────  ││
│  │                                                                     ││
│  │  ┌──────┬──────────────┬──────────┬────────┬──────────┬──────────┐ ││
│  │  │ Time │ Class        │ Teacher  │ Spots  │ Select   │          │ ││
│  │  ├──────┼──────────────┼──────────┼────────┼──────────┤          │ ││
│  │  │07:00 │ Pilates      │ Stine    │ 4 left │ [Select] │          │ ││
│  │  │08:00 │ Yoga         │ Ana      │ 8 left │ [Select] │          │ ││
│  │  │09:00 │ Sculpt       │ Seb      │ 2 left │ [Select] │          │ ││
│  │  │10:00 │ Mobility     │ Agata    │ 12 left│ [Select] │          │ ││
│  │  └──────┴──────────────┴──────────┴────────┴──────────┴──────────┘ ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Selected: 09:00 Sculpt with Sebastian                              ││
│  │                                                                     ││
│  │  ┌─────────────────────────────────────────────────────────────────┐││
│  │  │  Customer Found: Jane Doe                                      ││
│  │  │  ┌─────────────────────────────────────────────────────────────┐││
│  │  │  │ ✅ Already Booked — Checking in now...                     │││
│  │  │  │                                                            │││
│  │  │  │  Booking Ref: #452                                         │││
│  │  │  │  Membership: 10-Class Pack (3 remaining → 2 after check-in) │││
│  │  │  │                                                            │││
│  │  │  │              [Confirm Check-In]                             │││
│  │  │  └─────────────────────────────────────────────────────────────┘││
│  │  └─────────────────────────────────────────────────────────────────┘│
│  └─────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### Check-In Confirmed State

```
┌─────────────────────────────────────────────────────────────────────────┐
│  ✅ Check-In Successful!                                               │
│                                                                         │
│  Jane Doe checked in to Sculpt (09:00) with Sebastian                   │
│  10-Class Pack: 2 classes remaining                                    │
│                                                                         │
│  [Check In Another] [View Customer Profile]                             │
└─────────────────────────────────────────────────────────────────────────┘
```

### Error States

```
┌─────────────────────────────────────────────────────────────────────────┐
│  ❌ Cannot Check In                                                    │
│                                                                         │
│  Jane Doe has no active membership for this class.                      │
│                                                                         │
│  Options:                                                               │
│  [Purchase Drop-In (€22)] [Buy 10-Class Pack (€180)] [Cancel]          │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────────────┐
│  ❌ Not Booked — Would you like to book now?                           │
│                                                                         │
│  Jane Doe is not booked for this class.                                │
│  They have: 10-Class Pack (4 remaining)                                │
│                                                                         │
│  [Book & Check In] [Cancel]                                            │
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Data
- **Search bar:** Type-ahead autocomplete for customers
- **Today's schedule:** Quick-select buttons for currently running / next classes
- **Customer card:** Shows name, photo (if any), active memberships
- **Membership validation:** Which membership will be used, remaining count, auto-decrement

### User Actions
- Type customer name/email/phone → autocomplete dropdown
- Scan QR code → camera input, looks up customer ID encoded in QR
- Select class from today's schedule (or type class name)
- Click "Select" on a class to target it
- "Confirm Check-In" → POST /api/checkin → success/error feedback
- If no booking exists → offer to auto-book
- If no membership → offer to purchase (redirect to Stripe payment link)

---

## 6. Reports

### Purpose
Data analysis and business intelligence for studio performance metrics.

### Layout

```
┌─────────────────────────────────────────────────────────────────────────┐
│  Csport                       Reports                                  │
│  [Dashboard] [Schedule] [Customers] [Memberships] [Reports]            │
│                                                                         │
│  [Revenue ▼] [Class Popularity ▼] [New vs Returning ▼] [Export CSV]   │
│                                                                         │
│  ──── Revenue Report ────────────────────────────────────────────────── │
│  Year: [2026 ▼]  Group by: [Month ▼]  Period: [Jan 2026 - Jun 2026]  │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Revenue by Month (€)                                              ││
│  │  ┌───────────────────────────────────────────────────────────────┐  ││
│  │  │ €51k                                                         │  ││
│  │  │ ██████████████████████████                                    │  ││
│  │  │ €42k  €38k     ████████████████████████████████████           │  ││
│  │  │ ████████████████ ██████████████   €49k  €48k  €49k            │  ││
│  │  │ ████████████████ ██████████████ █████████████████████████████  │  ││
│  │  │ ████████████████ ██████████████ █████████████████████████████  │  ││
│  │  │ ██ Jan ██ ██ Feb ████ Mar ██████ Apr ██████ May ██████ Jun ██  │  ││
│  │  └───────────────────────────────────────────────────────────────┘  ││
│  │                                                                     ││
│  │  ┌──────┬────────────┬──────────┬────────────┬────────────────────┐ ││
│  │  │ Month│ Revenue    │ Trans.   │ Avg/Txn    │ vs Prev Month     │ ││
│  │  ├──────┼────────────┼──────────┼────────────┼────────────────────┤ ││
│  │  │ Jan  │ €42,000    │ 45       │ €933       │ —                  │ ││
│  │  │ Feb  │ €38,500    │ 40       │ €962       │ ▼ 8.3%             │ ││
│  │  │ Mar  │ €51,000    │ 52       │ €981       │ ▲ 32.5%            │ ││
│  │  │ Apr  │ €48,000    │ 48       │ €1,000     │ ▼ 5.9%             │ ││
│  │  │ May  │ €49,500    │ 50       │ €990       │ ▲ 3.1%             │ ││
│  │  │ Jun  │ €22,500    │ 24       │ €938       │ — (partial)        │ ││
│  │  └──────┴────────────┴──────────┴────────────┴────────────────────┘ ││
│  │  Total: €251,500    │ 259      │ €971 avg                          ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  ──── Other Report Tabs (when selected) ─────────────────────────────── │
│                                                                         │
│  Class Popularity: Bar chart of avg attendance per class type           │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Avg Attendance (% of Capacity)                                    ││
│  │  ┌───────────────────────────────────────────────────────────────┐  ││
│  │  │ Pilates ████████████████████████████████████████  86%        │  ││
│  │  │ Sculpt  ██████████████████████████████████        76%        │  ││
│  │  │ Flow    ██████████████████████████████            71%        │  ││
│  │  │ Yoga    ██████████████████████████                 62%        │  ││
│  │  │ Mobility████████████████████████                   59%        │  ││
│  │  │ Yin     ████████████████████                       51%        │  ││
│  │  │ Recovery██████████████████                         48%        │  ││
│  │  └───────────────────────────────────────────────────────────────┘  ││
│  └─────────────────────────────────────────────────────────────────────┘│
│                                                                         │
│  New vs Returning:                                                      │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │  Customer Retention                                                ││
│  │  ┌───────────────────────────────────────────────────────────────┐  ││
│  │  │ 100%│  ████████████████  ████████████████  ████████████████  │  ││
│  │  │  75%│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  │  ││
│  │  │  50%│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  │  ││
│  │  │  25%│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  │  ││
│  │  │   0%│  ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██ ██  │  ││
│  │  │     │ Jan   Feb   Mar   Apr   May   Jun                       │  ││
│  │  │     │ ██ New Customers   ██ Returning Customers               │  ││
│  │  └───────────────────────────────────────────────────────────────┘  ││
│  └─────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### Key Data
- **Revenue Report:** Bar chart + data table, grouped by month/week/day/membership type
- **Class Popularity:** Horizontal bar chart showing avg attendance % per class type
- **New vs Returning:** Stacked bar chart comparing new customer acquisition vs retention
- **Totals row:** Aggregate metrics at bottom of each table

### User Actions
- Tab selection: Revenue / Class Popularity / New vs Returning
- Date range picker (presets: Last 30 days, This Quarter, This Year, Custom)
- Group by dropdown
- Export CSV button → downloads current report as CSV
- Hover on chart → tooltip with exact values
- Click chart segment → filter customer/payment list to that segment