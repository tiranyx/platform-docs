# TIRANYX PLATFORM — TECHNICAL ARCHITECTURE
**Version:** 1.0.0 | **Date:** 2026-05-05
**Prinsip:** Simple dulu, scale nanti. Jangan over-engineer.

---

## 1. HIGH-LEVEL ARCHITECTURE

```
┌─────────────────────────────────────────────────────────────────┐
│                    VPS HOSTINGER (72.62.125.6)                   │
│                    aaPanel + PM2 + Nginx                         │
│                                                                  │
│  ┌──────────────────┐    ┌──────────────────────────────────┐   │
│  │  tiranyx.co.id   │    │      IXONOMIC ECOSYSTEM           │   │
│  │  (PM2: tiranyx)  │    │   (PM2: ixonomic-*)               │   │
│  │                  │    │                                    │   │
│  │  Next.js 16.2.1  │    │  api.ixonomic.com:3013 (B2B API) │   │
│  │  React 19        │◄───┤  adm.ixonomic.com:3008 (Admin)   │   │
│  │  TypeScript      │    │  bank.ixonomic.com (Banking)      │   │
│  │  Tailwind 4      │    │  embed.ixonomic.com:3006 (Widget) │   │
│  │  SQLite (local)  │    │  ixonomic.com (Landing)           │   │
│  │  Port: 3001      │    └──────────────────────────────────┘   │
│  └──────────────────┘                                            │
│          │               ┌──────────────────────────────────┐   │
│          │               │      SIDIX AI BRAIN               │   │
│          └───────────────┤   (PM2: sidix-brain, sidix-ui)    │   │
│                          │   app.sidixlab.com                │   │
│                          │   ctrl.sidixlab.com               │   │
│                          └──────────────────────────────────┘   │
│                                                                  │
│  ┌──────────────────┐    ┌──────────────────────────────────┐   │
│  │  apix.tiranyx    │    │      OTHER APPS                   │   │
│  │  (shopee-gateway)│    │  mighan-next (mighan.com)         │   │
│  │  Hono + Node.js  │    │  galantara-mp (galantara.io)      │   │
│  └──────────────────┘    └──────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┼───────────────┐
                    ▼               ▼               ▼
             Midtrans API      PayPal API      Pollinations AI
             (payment)         (payment)       (free AI content)
```

---

## 2. TIRANYX.CO.ID INTERNAL ARCHITECTURE

### 2.1 Next.js App Router Structure

```
app/
├── layout.tsx               # Root: html/body, fonts, metadata
├── globals.css              # Space theme, custom properties
├── robots.ts                # SEO robots
├── sitemap.ts               # Auto sitemap (extend untuk produk baru)
│
├── (site)/                  # Public frontend (Navbar + Footer)
│   ├── layout.tsx           # SciFiBackdrop, CursorGlow, AppShell
│   ├── page.tsx             # Homepage
│   ├── about/
│   ├── blog/                # listing + [slug]
│   ├── services/            # overview + [id] detail
│   ├── portfolio/           # listing + case-studies
│   ├── products/            # NEW: catalog + [slug] detail
│   ├── tools/               # listing + [slug] interactive
│   ├── demos/               # app demos showcase
│   ├── resources/
│   ├── waitlist/            # NEW: index + [product]
│   ├── saas/                # NEW: white-label catalog + [bundle]
│   ├── contact/
│   ├── privacy-policy/
│   └── disclaimer/
│
├── (auth)/                  # NEW: Auth pages (no sidebar, minimal)
│   ├── layout.tsx
│   ├── login/
│   ├── register/
│   ├── forgot-password/
│   └── verify-email/
│
├── (dashboard)/             # NEW: Personal member area
│   ├── layout.tsx           # DashboardLayout (sidebar + topbar)
│   ├── dashboard/
│   │   ├── page.tsx         # Overview
│   │   ├── tools/
│   │   ├── orders/
│   │   ├── services/
│   │   ├── subscriptions/
│   │   ├── downloads/
│   │   └── profile/
│
├── (client)/                # NEW: Agency client portal
│   ├── layout.tsx           # ClientLayout
│   ├── client/
│   │   ├── login/
│   │   ├── page.tsx         # Overview
│   │   ├── projects/
│   │   ├── deliverables/
│   │   ├── invoices/
│   │   └── messages/
│
├── admin/                   # Existing: Internal CMS (extend)
│   ├── (guest)/login/
│   └── (protected)/
│       ├── page.tsx         # Dashboard
│       ├── posts/
│       ├── portfolio/
│       ├── case-studies/
│       ├── resources/
│       ├── demos/
│       ├── contacts/
│       ├── settings/
│       ├── users/           # NEW
│       ├── orders/          # NEW
│       ├── products/        # NEW
│       ├── subscriptions/   # NEW
│       ├── waitlist/        # NEW
│       └── analytics/       # NEW
│
└── api/
    ├── admin/               # Existing admin API
    ├── auth/                # NEW: user auth
    │   ├── register/
    │   ├── login/
    │   ├── logout/
    │   ├── verify-email/
    │   └── forgot-password/
    ├── user/                # NEW: user data
    │   ├── profile/
    │   ├── purchases/
    │   └── usage/
    ├── tools/               # NEW: tool usage enforcement
    │   └── [slug]/use/
    ├── orders/              # NEW: service orders
    │   ├── create/
    │   └── [id]/
    ├── payment/             # NEW: payment integration
    │   ├── midtrans/
    │   │   ├── create/
    │   │   └── notification/   # webhook
    │   └── paypal/
    │       ├── create-order/
    │       └── capture/
    ├── waitlist/            # NEW: waitlist signup
    │   └── join/
    └── cron/                # Existing + new
        ├── content-publish/
        └── subscription-check/ # NEW: check expiring subs
```

---

## 3. AUTHENTICATION ARCHITECTURE

### 3.1 Dua Sistem Auth Terpisah (penting!)

```
Admin Auth (existing, JANGAN UBAH):
  Cookie name: trx_admin
  Algorithm: HMAC-SHA256
  Scope: /admin/* routes
  TTL: 24 jam
  
User Auth (NEW):
  Cookie name: trx_user
  Algorithm: signed JWT atau HMAC (consistent dengan existing)
  Scope: /dashboard/*, /client/*, /api/user/*, /api/orders/*, dll
  TTL: 7 hari (remember me) atau 24 jam (default)
  
Separation: middleware.ts (renamed proxy.ts eventually) checks:
  - /admin/* → verifikasi trx_admin cookie
  - /dashboard/* → verifikasi trx_user cookie
  - /client/* → verifikasi trx_user cookie + user_type = 'client'
```

### 3.2 Auth Flow

```typescript
// POST /api/auth/register
// 1. Validate input
// 2. Check email tidak duplicate
// 3. Hash password (bcrypt atau argon2)
// 4. Insert ke users table
// 5. Send verification email
// 6. Return: { success: true, message: "Cek email kamu" }

// POST /api/auth/login
// 1. Find user by email
// 2. Verify password hash
// 3. Check email_verified
// 4. Create session token (crypto.randomBytes(32).toString('hex'))
// 5. Insert ke user_sessions
// 6. Set trx_user cookie (httpOnly, sameSite: lax)
// 7. Return: { success: true, user: { id, name, email, user_type } }

// Middleware untuk protected routes
// 1. Read trx_user cookie
// 2. Query user_sessions WHERE token = ? AND expires_at > datetime('now')
// 3. If not found: redirect /login
// 4. Attach user ke request context
```

---

## 4. PAYMENT ARCHITECTURE

### 4.1 Midtrans Integration

```typescript
// lib/payment/midtrans.ts

import Midtrans from 'midtrans-client'

const snap = new Midtrans.Snap({
  isProduction: process.env.NODE_ENV === 'production',
  serverKey: process.env.MIDTRANS_SERVER_KEY!,
  clientKey: process.env.NEXT_PUBLIC_MIDTRANS_CLIENT_KEY!,
})

export async function createSnapTransaction(order: Order) {
  const parameter = {
    transaction_details: {
      order_id: order.midtrans_order_id,  // TRX-{timestamp}-{random}
      gross_amount: order.amount_idr,
    },
    customer_details: {
      first_name: order.customer_name,
      email: order.customer_email,
      phone: order.customer_phone,
    },
    item_details: [{
      id: order.product_slug,
      price: order.amount_idr,
      quantity: 1,
      name: order.product_name,
    }],
    callbacks: {
      finish: `${process.env.NEXT_PUBLIC_SITE_URL}/dashboard/orders?status=success`,
      error: `${process.env.NEXT_PUBLIC_SITE_URL}/dashboard/orders?status=error`,
      pending: `${process.env.NEXT_PUBLIC_SITE_URL}/dashboard/orders?status=pending`,
    }
  }
  return snap.createTransaction(parameter)
}
```

### 4.2 Webhook Processing

```typescript
// POST /api/payment/midtrans/notification
// Diverifikasi dengan Midtrans signature key

export async function POST(request: Request) {
  const body = await request.json()
  
  // 1. Verify signature
  const hash = crypto.createHash('sha512')
    .update(`${body.order_id}${body.status_code}${body.gross_amount}${MIDTRANS_SERVER_KEY}`)
    .digest('hex')
  if (hash !== body.signature_key) return Response.json({ error: 'Invalid signature' }, { status: 401 })
  
  // 2. Update order status
  // 3. If paid: activate purchase, trigger delivery
  // 4. Send email konfirmasi
}
```

### 4.3 Delivery Automation

```typescript
// lib/delivery/index.ts

async function triggerDelivery(purchase: UserPurchase) {
  switch (purchase.delivery_type) {
    case 'web_access':
      // Activate purchase record, user bisa akses di dashboard
      await activatePurchase(purchase.id)
      break
      
    case 'email_file':
      // Kirim email dengan download link (signed URL, expired 72j)
      await sendDeliveryEmail(purchase)
      break
      
    case 'github_invite':
      // GitHub API: tambah user sebagai collaborator di private repo
      await inviteToGitHub(purchase.user_email, purchase.github_repo)
      break
      
    case 'license_key':
      // Generate license key, simpan di DB, kirim via email
      const licenseKey = generateLicenseKey(purchase)
      await sendLicenseEmail(purchase, licenseKey)
      break
      
    case 'manual':
      // Notify admin via email/WA untuk handle manual
      await notifyAdminForManualDelivery(purchase)
      break
  }
}
```

---

## 5. DATABASE STRATEGY

### 5.1 SQLite: Kenapa Tetap Pakai

| Pertanyaan | Jawaban |
|-----------|---------|
| Bisa scale? | Ya, sampai ~100K users concurrent (dengan caching) |
| Backup? | File copy sederhana; VPS otomatis bisa cron backup |
| Migration? | Better-sqlite3 sync migrations, sudah ada polanya |
| vs PostgreSQL | PostgreSQL lebih baik untuk scale besar, tapi overhead setup tidak worth it untuk MVP |
| Kapan migrate? | Jika > 50K users aktif atau butuh multi-server read replicas |

### 5.2 Caching Layer (minimal)

```typescript
// lib/cache/in-memory.ts — untuk data yang sering dibaca, jarang berubah
// Contoh: product catalog, pricing tiers

const cache = new Map<string, { data: unknown; expires: number }>()

export function getFromCache<T>(key: string): T | null {
  const entry = cache.get(key)
  if (!entry || entry.expires < Date.now()) return null
  return entry.data as T
}

export function setCache(key: string, data: unknown, ttlSeconds = 300) {
  cache.set(key, { data, expires: Date.now() + ttlSeconds * 1000 })
}
```

### 5.3 File Storage

```
VPS path: /www/wwwroot/tiranyx.co.id/public/uploads/
  ├── blog/         ← existing
  ├── products/     ← thumbnail, screenshots
  ├── deliverables/ ← service order files (private, tidak via /public)
  └── avatars/      ← user avatars

Deliverables TIDAK di /public — akses via signed API endpoint:
GET /api/files/deliverables/[token] — verify token → stream file
```

---

## 6. EXTERNAL INTEGRATIONS

### 6.1 Email (Transactional)

```
Current: SMTP (env: SMTP_HOST, SMTP_USER, SMTP_PASS)
Priority emails:
  - Email verifikasi (template: verify-email)
  - Reset password (template: reset-password)
  - Order confirmation (template: order-confirmed)
  - Delivery notification (template: delivery-ready)
  - Subscription expiry warning (template: sub-expiry, 7 hari sebelum)
  - Invoice (template: invoice, PDF attachment)

Library: nodemailer (sudah ada di env example sebagai pattern)
Template: React Email atau string template sederhana
```

### 6.2 Midtrans

```
SDK: midtrans-client (npm)
Mode: Sandbox → Production
Env vars:
  MIDTRANS_SERVER_KEY=SB-Mid-server-xxx (sandbox) / Mid-server-xxx (prod)
  NEXT_PUBLIC_MIDTRANS_CLIENT_KEY=SB-Mid-client-xxx (sandbox)
  MIDTRANS_IS_PRODUCTION=false/true
```

### 6.3 PayPal

```
SDK: @paypal/checkout-server-sdk atau REST API langsung
Mode: Sandbox → Live
Env vars:
  PAYPAL_CLIENT_ID=xxx
  PAYPAL_CLIENT_SECRET=xxx
  PAYPAL_MODE=sandbox/live
```

### 6.4 GitHub API (untuk white-label delivery)

```
Purpose: Auto-invite buyer ke private repo setelah bayar
Env vars:
  GITHUB_PAT=ghp_xxx (personal access token, scope: repo)
  
API call:
  PUT /repos/{owner}/{repo}/collaborators/{username}
  Body: { permission: "pull" }
```

### 6.5 Ixonomic API (untuk client portal)

```
Base URL: https://api.ixonomic.com (PM2: ixonomic-api, port 3013)
Internal access: http://127.0.0.1:3013 (same VPS, no internet roundtrip)
Auth: API key atau JWT (sesuai Ixonomic auth system)
Endpoints yang dibutuhkan:
  GET /projects?client_id={id}
  GET /projects/{id}/milestones
  GET /projects/{id}/deliverables
  POST /projects/{id}/approve
  GET /invoices?client_id={id}
```

---

## 7. DEPLOYMENT PIPELINE

### 7.1 Current (tetap dipakai)

```powershell
# Windows laptop → buat archive
cd D:\tiranyx && .\deploy.ps1
# Output: tiranyx-web-upload.tar.gz

# Upload ke VPS via aaPanel File Manager atau scp
# VPS: extract + build + restart
cd /www/wwwroot/tiranyx.co.id
tar -xzf ../tiranyx-web-upload.tar.gz --strip-components=1
npm install --legacy-peer-deps
npm run build
pm2 restart tiranyx
find /www/server/nginx/proxy_cache_dir -type f -delete 2>/dev/null
nginx -s reload
```

### 7.2 New Env Variables (tambahkan ke .env)

```bash
# USER AUTH
USER_COOKIE_SECRET=<random 32 char>
USER_SESSION_TTL=604800  # 7 hari dalam detik

# PAYMENT - MIDTRANS
MIDTRANS_SERVER_KEY=
NEXT_PUBLIC_MIDTRANS_CLIENT_KEY=
MIDTRANS_IS_PRODUCTION=false

# PAYMENT - PAYPAL
PAYPAL_CLIENT_ID=
PAYPAL_CLIENT_SECRET=
PAYPAL_MODE=sandbox

# EMAIL
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=noreply@tiranyx.co.id
SMTP_PASS=

# GITHUB (untuk white-label delivery)
GITHUB_PAT=

# IXONOMIC API
IXONOMIC_API_URL=http://127.0.0.1:3013
IXONOMIC_API_KEY=

# NOTIFICATIONS
WA_NOTIF_NUMBER=6282113590953  # WA admin untuk notifikasi order baru
```

---

## 8. PERFORMANCE & MONITORING

### 8.1 Nginx Caching Strategy

```nginx
# Existing: tiranyx.co.id nginx config
# Static assets: cache 30 hari
# API routes: no-cache
# HTML pages: cache 1 menit (fresh content)
# Dashboard routes: no-cache (personalized)
```

### 8.2 Monitoring (minimal untuk VPS solo)

```bash
# PM2 built-in monitoring
pm2 monit

# Simple uptime check (existing atau bisa tambah)
# UptimeRobot free: ping setiap 5 menit

# Error tracking: console.error ke PM2 log
pm2 logs tiranyx --lines 50

# Disk & memory
df -h && free -m
```

### 8.3 Backup Otomatis

```bash
# Cron di VPS (via aaPanel cron):
# Setiap hari jam 02:00 WIB
0 2 * * * cp /www/wwwroot/tiranyx.co.id/tiranyx.db /www/wwwroot/tiranyx.co.id/backups/tiranyx.db.$(date +\%Y\%m\%d) && find /www/wwwroot/tiranyx.co.id/backups -name "*.db.*" -mtime +30 -delete
```

---

## 9. SECURITY CHECKLIST

```
AUTH:
[x] Admin auth: HMAC-SHA256 (existing)
[ ] User auth: signed session token
[ ] Rate limiting: auth endpoints (5/min per IP)
[ ] Password hashing: bcrypt (saltRounds: 12)
[ ] Email verification required sebelum akses dashboard

PAYMENT:
[ ] Midtrans webhook signature verification
[ ] PayPal webhook verification
[ ] Idempotency: cek duplicate webhook (order sudah processed)

DATA:
[x] SQLite prepared statements (no SQL injection)
[ ] Input sanitization: semua user input
[ ] File upload: whitelist extension, size limit
[ ] Deliverable files: tidak exposed via /public

NETWORK:
[x] HTTPS enforced (Nginx)
[x] HSTS header
[ ] CSP header: extend untuk Midtrans Snap
[ ] No sensitive data di client bundle (env vars check)
```

---

*Architecture doc ini update ketika ada perubahan infrastruktur signifikan.*
*Reference: PROJECT_STATUS.md di tiranyx-web untuk versi teknis terkini.*
