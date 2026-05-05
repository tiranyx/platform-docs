# TIRANYX PLATFORM — ERD & DATABASE SCHEMA
**Version:** 1.0.0 | **Date:** 2026-05-05
**Database:** SQLite (better-sqlite3) — tiranyx.co.id/tiranyx.db
**Migration strategy:** Tambah tabel baru tanpa break existing schema

---

## ENTITY RELATIONSHIP DIAGRAM

```
┌─────────────────┐         ┌──────────────────┐         ┌──────────────────┐
│     USERS        │         │   USER_SESSIONS   │         │  USER_PURCHASES  │
│─────────────────│         │──────────────────│         │──────────────────│
│ id (PK)         │◄────────│ user_id (FK)     │         │ id (PK)          │
│ email (UQ)      │         │ token (UQ)       │         │ user_id (FK) ────│──► USERS
│ password_hash   │         │ expires_at       │         │ product_id (FK) ─│──► PRODUCTS
│ name            │         │ created_at       │         │ plan_name        │
│ phone           │         └──────────────────┘         │ price_idr        │
│ user_type       │                                       │ payment_method   │
│ status          │         ┌──────────────────┐         │ midtrans_order_id│
│ email_verified  │         │  TOOL_USAGE       │         │ paypal_order_id  │
│ avatar_url      │◄────────│ user_id (FK)     │         │ status           │
│ company_name    │         │ tool_slug        │         │ started_at       │
│ created_at      │         │ usage_count      │         │ expires_at       │
│ updated_at      │         │ quota_limit      │         │ created_at       │
└─────────────────┘         │ reset_type       │         └──────────────────┘
         │                  │ reset_at         │
         │                  └──────────────────┘         ┌──────────────────┐
         │                                               │    PRODUCTS       │
         │                  ┌──────────────────┐         │──────────────────│
         │                  │  SERVICE_ORDERS   │         │ id (PK)          │
         └─────────────────►│ user_id (FK)     │         │ slug (UQ)        │
                            │ service_slug     │         │ name             │
                            │ assigned_to (FK)─│──► USR  │ category         │
                            │ status           │         │ type             │
                            │ brief_text       │         │ description_short│
                            │ brief_attach_json│         │ description_long │
                            │ deliverables_json│         │ pricing_tiers_json│
                            │ amount_idr       │         │ features_json    │
                            │ purchase_id (FK) │         │ delivery_type    │
                            │ submitted_at     │         │ status           │
                            │ delivered_at     │         │ thumbnail_url    │
                            └──────────────────┘         │ is_published     │
                                     │                   │ sort_order       │
                                     │                   └──────────────────┘
                            ┌──────────────────┐                  │
                            │  ORDER_MESSAGES   │         ┌──────────────────┐
                            │──────────────────│         │  PRODUCT_FAQ      │
                            │ id (PK)          │         │──────────────────│
                            │ order_id (FK)    │         │ id (PK)          │
                            │ from_user_id(FK) │         │ product_id (FK) ─│──► PRODUCTS
                            │ message          │         │ question         │
                            │ attachments_json │         │ answer           │
                            │ created_at       │         │ sort_order       │
                            └──────────────────┘         └──────────────────┘

┌──────────────────────┐    ┌──────────────────┐    ┌──────────────────────┐
│   CLIENT_PROJECTS    │    │CLIENT_MILESTONES  │    │   WAITLIST_ENTRIES   │
│──────────────────────│    │──────────────────│    │──────────────────────│
│ id (PK)              │    │ id (PK)          │    │ id (PK)              │
│ client_user_id (FK) ─│──► │ project_id (FK) ─│──► │ product_slug         │
│ project_name         │USR │ title            │PJT │ name                 │
│ status               │    │ description      │    │ email                │
│ scope_text           │    │ status           │    │ role                 │
│ budget_idr           │    │ due_at           │    │ notes                │
│ pic_user_id (FK)     │    │ completed_at     │    │ created_at           │
│ ixonomic_project_id  │    └──────────────────┘    └──────────────────────┘
│ started_at           │
│ deadline             │    ┌──────────────────┐
│ completed_at         │    │  CLIENT_INVOICES  │
└──────────────────────┘    │──────────────────│
                            │ id (PK)          │
                            │ client_user_id   │
                            │ project_id (FK)  │
                            │ invoice_number   │
                            │ amount_idr       │
                            │ status           │
                            │ due_at           │
                            │ paid_at          │
                            │ midtrans_order_id│
                            │ pdf_url          │
                            │ created_at       │
                            └──────────────────┘
```

---

## FULL SQL SCHEMA

### TABEL EXISTING (referensi, jangan ubah)

```sql
-- Existing: blog
CREATE TABLE IF NOT EXISTS posts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  slug TEXT UNIQUE NOT NULL,
  title TEXT NOT NULL,
  excerpt TEXT,
  content TEXT,
  content_format TEXT DEFAULT 'html',
  cover_image TEXT,
  status TEXT DEFAULT 'draft',
  is_featured INTEGER DEFAULT 0,
  category_id INTEGER,
  views INTEGER DEFAULT 0,
  published_at TEXT,
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT DEFAULT (datetime('now'))
);

-- Existing: portfolio, case_studies, app_demos, resources, contacts, settings
-- (tidak perlu di-rewrite di sini, lihat lib/db/schema.ts di tiranyx-web)
```

---

### MIGRASI 001 — Users & Auth

```sql
-- ============================================================
-- MIGRATION 001: users, user_sessions
-- File: lib/db/migrations/001_users_auth.ts
-- Run: setelah deploy pertama setelah feature auth
-- ============================================================

CREATE TABLE IF NOT EXISTS users (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  email       TEXT    NOT NULL UNIQUE,
  password_hash TEXT  NOT NULL,
  name        TEXT    NOT NULL,
  phone       TEXT,
  user_type   TEXT    NOT NULL DEFAULT 'personal', -- personal|client|internal
  status      TEXT    NOT NULL DEFAULT 'active',   -- active|suspended|pending_verify
  email_verified INTEGER NOT NULL DEFAULT 0,
  avatar_url  TEXT,
  company_name TEXT,
  timezone    TEXT    DEFAULT 'Asia/Jakarta',
  created_at  TEXT    NOT NULL DEFAULT (datetime('now')),
  updated_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_users_email ON users(email);
CREATE INDEX IF NOT EXISTS idx_users_type ON users(user_type);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS user_sessions (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id     INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token       TEXT    NOT NULL UNIQUE,
  user_agent  TEXT,
  ip_address  TEXT,
  expires_at  TEXT    NOT NULL,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_sessions_token ON user_sessions(token);
CREATE INDEX IF NOT EXISTS idx_sessions_user ON user_sessions(user_id);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS password_resets (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id     INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token       TEXT    NOT NULL UNIQUE,
  used        INTEGER NOT NULL DEFAULT 0,
  expires_at  TEXT    NOT NULL,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE TABLE IF NOT EXISTS email_verifications (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id     INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  token       TEXT    NOT NULL UNIQUE,
  used        INTEGER NOT NULL DEFAULT 0,
  expires_at  TEXT    NOT NULL,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);
```

---

### MIGRASI 002 — Products & Catalog

```sql
-- ============================================================
-- MIGRATION 002: products, product_faq, product_pricing_tiers
-- File: lib/db/migrations/002_products.ts
-- ============================================================

CREATE TABLE IF NOT EXISTS products (
  id                  INTEGER PRIMARY KEY AUTOINCREMENT,
  slug                TEXT    NOT NULL UNIQUE,
  name                TEXT    NOT NULL,
  tagline             TEXT,
  category            TEXT    NOT NULL, -- tools|saas|on_demand|white_label|ai|waitlist
  type                TEXT    NOT NULL, -- subscription|one_time|custom|free
  description_short   TEXT,
  description_long    TEXT,             -- markdown
  
  -- Pricing (minimal harga untuk Midtrans)
  price_idr_min       INTEGER,          -- harga terendah dalam IDR
  price_idr_max       INTEGER,          -- harga tertinggi (null jika fixed)
  pricing_model       TEXT,             -- monthly|yearly|one_time|custom
  pricing_tiers_json  TEXT,             -- JSON: [{name, price_idr, price_usd, features[], is_popular}]
  
  -- Fitur & konten
  features_json       TEXT,             -- JSON: [{icon, title, desc}]
  how_it_works_json   TEXT,             -- JSON: [{step, title, desc}]
  screenshots_json    TEXT,             -- JSON: [url]
  demo_url            TEXT,
  
  -- Delivery
  delivery_type       TEXT    NOT NULL DEFAULT 'web_access',
                                        -- web_access|email_file|github_invite|license_key|manual
  delivery_notes      TEXT,
  
  -- SEO
  seo_title           TEXT,
  seo_description     TEXT,
  og_image_url        TEXT,
  
  -- Status
  status              TEXT    NOT NULL DEFAULT 'live', -- live|beta|coming_soon|waitlist|archived
  thumbnail_url       TEXT,
  is_published        INTEGER NOT NULL DEFAULT 0,
  sort_order          INTEGER NOT NULL DEFAULT 0,
  
  created_at          TEXT    NOT NULL DEFAULT (datetime('now')),
  updated_at          TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_products_slug ON products(slug);
CREATE INDEX IF NOT EXISTS idx_products_category ON products(category);
CREATE INDEX IF NOT EXISTS idx_products_published ON products(is_published);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS product_faq (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  product_id  INTEGER NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  question    TEXT    NOT NULL,
  answer      TEXT    NOT NULL,           -- markdown
  sort_order  INTEGER NOT NULL DEFAULT 0,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_faq_product ON product_faq(product_id);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS product_tags (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  product_id  INTEGER NOT NULL REFERENCES products(id) ON DELETE CASCADE,
  tag         TEXT    NOT NULL
);
```

---

### MIGRASI 003 — Purchases & Subscriptions

```sql
-- ============================================================
-- MIGRATION 003: user_purchases, tool_usage
-- File: lib/db/migrations/003_purchases.ts
-- ============================================================

CREATE TABLE IF NOT EXISTS user_purchases (
  id                  INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id             INTEGER NOT NULL REFERENCES users(id),
  product_id          INTEGER REFERENCES products(id),
  product_slug        TEXT    NOT NULL,   -- denormalized untuk query cepat
  plan_name           TEXT    NOT NULL,   -- Free|Pro|Enterprise|custom
  price_idr           INTEGER NOT NULL DEFAULT 0,
  price_usd           REAL,
  currency            TEXT    NOT NULL DEFAULT 'IDR',
  
  -- Payment
  payment_method      TEXT,               -- midtrans_snap|paypal|manual|free
  midtrans_order_id   TEXT UNIQUE,
  midtrans_trx_id     TEXT,
  paypal_order_id     TEXT UNIQUE,
  payment_status      TEXT    NOT NULL DEFAULT 'pending',
                                          -- pending|paid|failed|refunded
  
  -- Subscription period
  status              TEXT    NOT NULL DEFAULT 'pending',
                                          -- pending|active|expired|cancelled
  billing_cycle       TEXT,               -- monthly|yearly|one_time|lifetime
  started_at          TEXT,
  expires_at          TEXT,               -- NULL = lifetime
  cancelled_at        TEXT,
  
  -- Delivery
  delivery_type       TEXT,
  delivery_status     TEXT    NOT NULL DEFAULT 'pending',
                                          -- pending|processing|delivered|failed
  delivery_notes      TEXT,
  delivered_at        TEXT,
  
  created_at          TEXT    NOT NULL DEFAULT (datetime('now')),
  updated_at          TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_purchases_user ON user_purchases(user_id);
CREATE INDEX IF NOT EXISTS idx_purchases_status ON user_purchases(status);
CREATE INDEX IF NOT EXISTS idx_purchases_midtrans ON user_purchases(midtrans_order_id);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS tool_usage (
  id            INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id       INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  tool_slug     TEXT    NOT NULL,
  usage_count   INTEGER NOT NULL DEFAULT 0,
  quota_limit   INTEGER NOT NULL DEFAULT 5,  -- 5 free, 9999 pro
  reset_type    TEXT    NOT NULL DEFAULT 'daily', -- daily|monthly|never
  reset_at      TEXT,                             -- kapan reset berikutnya
  last_used_at  TEXT,
  
  UNIQUE(user_id, tool_slug)
);

CREATE INDEX IF NOT EXISTS idx_tool_usage_user ON tool_usage(user_id, tool_slug);
```

---

### MIGRASI 004 — Service Orders

```sql
-- ============================================================
-- MIGRATION 004: service_orders, order_messages
-- File: lib/db/migrations/004_service_orders.ts
-- ============================================================

CREATE TABLE IF NOT EXISTS service_orders (
  id                  INTEGER PRIMARY KEY AUTOINCREMENT,
  order_number        TEXT    NOT NULL UNIQUE, -- TRX-2026-XXXXXX
  user_id             INTEGER NOT NULL REFERENCES users(id),
  service_slug        TEXT    NOT NULL,
  service_name        TEXT    NOT NULL,        -- denormalized
  
  -- Status flow
  status              TEXT    NOT NULL DEFAULT 'submitted',
                                -- submitted|review|in_progress|revision|done|cancelled
  
  -- Brief
  brief_text          TEXT,
  brief_data_json     TEXT,                    -- form fields per service type
  brief_attachments_json TEXT,                 -- [{filename, url, size}]
  
  -- Assignment (internal)
  assigned_to_user_id INTEGER REFERENCES users(id),
  assigned_at         TEXT,
  internal_notes      TEXT,
  
  -- Delivery
  deliverables_json   TEXT,                    -- [{filename, url, version, uploaded_at}]
  revision_count      INTEGER NOT NULL DEFAULT 0,
  revision_limit      INTEGER NOT NULL DEFAULT 2,
  
  -- Payment
  amount_idr          INTEGER NOT NULL,
  purchase_id         INTEGER REFERENCES user_purchases(id),
  
  -- Timeline
  estimated_days      INTEGER,
  submitted_at        TEXT    NOT NULL DEFAULT (datetime('now')),
  started_at          TEXT,
  due_at              TEXT,
  delivered_at        TEXT,
  completed_at        TEXT,
  cancelled_at        TEXT,
  
  created_at          TEXT    NOT NULL DEFAULT (datetime('now')),
  updated_at          TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_orders_user ON service_orders(user_id);
CREATE INDEX IF NOT EXISTS idx_orders_status ON service_orders(status);
CREATE INDEX IF NOT EXISTS idx_orders_assigned ON service_orders(assigned_to_user_id);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS order_messages (
  id            INTEGER PRIMARY KEY AUTOINCREMENT,
  order_id      INTEGER NOT NULL REFERENCES service_orders(id) ON DELETE CASCADE,
  from_user_id  INTEGER NOT NULL REFERENCES users(id),
  message       TEXT    NOT NULL,
  is_internal   INTEGER NOT NULL DEFAULT 0, -- 1 = hanya tim internal yang lihat
  attachments_json TEXT,
  created_at    TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_messages_order ON order_messages(order_id);
```

---

### MIGRASI 005 — Client Projects

```sql
-- ============================================================
-- MIGRATION 005: client_projects, client_milestones, client_invoices
-- File: lib/db/migrations/005_client_projects.ts
-- ============================================================

CREATE TABLE IF NOT EXISTS client_projects (
  id                  INTEGER PRIMARY KEY AUTOINCREMENT,
  client_user_id      INTEGER NOT NULL REFERENCES users(id),
  project_name        TEXT    NOT NULL,
  project_code        TEXT    UNIQUE,          -- TRX-PRJ-XXXXX
  status              TEXT    NOT NULL DEFAULT 'active',
                                -- active|paused|completed|cancelled
  scope_text          TEXT,
  budget_idr          INTEGER,
  retainer_monthly_idr INTEGER,
  
  -- Team
  pic_user_id         INTEGER REFERENCES users(id), -- project manager internal
  
  -- Ixonomic sync
  ixonomic_project_id TEXT,
  
  -- Timeline
  started_at          TEXT,
  deadline            TEXT,
  completed_at        TEXT,
  
  created_at          TEXT    NOT NULL DEFAULT (datetime('now')),
  updated_at          TEXT    NOT NULL DEFAULT (datetime('now'))
);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS client_milestones (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  project_id  INTEGER NOT NULL REFERENCES client_projects(id) ON DELETE CASCADE,
  title       TEXT    NOT NULL,
  description TEXT,
  status      TEXT    NOT NULL DEFAULT 'pending',
                      -- pending|in_progress|waiting_approval|revision|approved|done
  deliverable_url TEXT,
  revision_notes TEXT,
  due_at      TEXT,
  completed_at TEXT,
  sort_order  INTEGER NOT NULL DEFAULT 0,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS client_invoices (
  id              INTEGER PRIMARY KEY AUTOINCREMENT,
  client_user_id  INTEGER NOT NULL REFERENCES users(id),
  project_id      INTEGER REFERENCES client_projects(id),
  invoice_number  TEXT    NOT NULL UNIQUE,      -- INV-2026-XXXXX
  description     TEXT,
  amount_idr      INTEGER NOT NULL,
  tax_idr         INTEGER NOT NULL DEFAULT 0,
  total_idr       INTEGER NOT NULL,
  status          TEXT    NOT NULL DEFAULT 'pending',
                          -- pending|sent|paid|overdue|cancelled
  due_at          TEXT,
  paid_at         TEXT,
  midtrans_order_id TEXT,
  pdf_url         TEXT,
  notes           TEXT,
  created_at      TEXT    NOT NULL DEFAULT (datetime('now'))
);
```

---

### MIGRASI 006 — Waitlist

```sql
-- ============================================================
-- MIGRATION 006: waitlist_entries
-- File: lib/db/migrations/006_waitlist.ts
-- ============================================================

CREATE TABLE IF NOT EXISTS waitlist_entries (
  id            INTEGER PRIMARY KEY AUTOINCREMENT,
  product_slug  TEXT    NOT NULL,  -- migancore|mighan-world|sidixlab|ixonomic-coin
  name          TEXT    NOT NULL,
  email         TEXT    NOT NULL,
  role          TEXT,              -- user|developer|partner|investor
  company       TEXT,
  notes         TEXT,
  source        TEXT,              -- utm_source, referrer
  confirmed     INTEGER NOT NULL DEFAULT 0,  -- email confirmed
  created_at    TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_waitlist_product ON waitlist_entries(product_slug);
CREATE INDEX IF NOT EXISTS idx_waitlist_email ON waitlist_entries(email);
UNIQUE(product_slug, email)  -- satu email per produk
```

---

### MIGRASI 007 — Notifications & Audit

```sql
-- ============================================================
-- MIGRATION 007: notifications, audit_log
-- ============================================================

CREATE TABLE IF NOT EXISTS notifications (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id     INTEGER NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  type        TEXT    NOT NULL,  -- order_update|payment_success|subscription_expiry|etc
  title       TEXT    NOT NULL,
  message     TEXT,
  link        TEXT,              -- URL untuk action
  is_read     INTEGER NOT NULL DEFAULT 0,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);

CREATE INDEX IF NOT EXISTS idx_notif_user ON notifications(user_id, is_read);

-- -----------------------------------------------

CREATE TABLE IF NOT EXISTS audit_log (
  id          INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id     INTEGER REFERENCES users(id),
  action      TEXT    NOT NULL,  -- user.login|order.create|payment.success|etc
  entity_type TEXT,              -- user|order|product|etc
  entity_id   TEXT,
  old_value   TEXT,              -- JSON
  new_value   TEXT,              -- JSON
  ip_address  TEXT,
  created_at  TEXT    NOT NULL DEFAULT (datetime('now'))
);
```

---

## DATA SEEDING (initial data)

```typescript
// scripts/seed-products.ts — jalankan setelah migration 002

const SEED_PRODUCTS = [
  {
    slug: 'tools-pro',
    name: 'Tiranyx Tools Pro',
    category: 'tools',
    type: 'subscription',
    price_idr_min: 99000,
    pricing_model: 'monthly',
    delivery_type: 'web_access',
    status: 'live',
    is_published: 1,
  },
  {
    slug: 'brand-guidelines',
    name: 'Brand Guideline Builder',
    category: 'tools',
    type: 'subscription',
    price_idr_min: 149000,
    pricing_model: 'monthly',
    delivery_type: 'web_access',
    status: 'beta',
    is_published: 1,
  },
  // ... dst untuk semua produk
]
```

---

## NAMING CONVENTIONS

| Entity | Format |
|--------|--------|
| Order number | `TRX-2026-XXXXXX` (random 6 char) |
| Invoice number | `INV-2026-XXXXX` (sequential) |
| Project code | `TRX-PRJ-XXXXX` |
| Midtrans order ID | `TRX-{timestamp}-{random}` |
| Session token | 64-char hex (crypto.randomBytes(32)) |
| Reset token | 32-char hex |

---

## CATATAN MIGRATION

1. **Jalankan di VPS setelah deploy** — SQLite auto-migrate saat app start (via schema.ts)
2. **Backup dulu:** `cp tiranyx.db tiranyx.db.bak.$(date +%Y%m%d)` sebelum migration besar
3. **Tidak ada `DROP TABLE`** di migration — only `CREATE TABLE IF NOT EXISTS` + `ALTER TABLE ADD COLUMN IF NOT EXISTS`
4. **Test di dev dulu** — jalankan migration di lokal sebelum push ke VPS
5. **Semua timestamp:** format ISO 8601 (`datetime('now')`) — consistent dengan existing schema
