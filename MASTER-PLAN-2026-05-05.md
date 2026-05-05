# MASTER PLAN — Tiranyx Platform Rebuild
**Date:** 2026-05-05 | **Author:** Claude (after full session review) | **Status:** AWAITING USER APPROVAL

> Saya sudah baca semua chat log, memory, JOURNAL, SPRINT-LOG, PROTOCOL, dan dokumen platform-docs. Tidak akan touch VPS atau code apapun sebelum user approve plan ini. **Saya stop reactive fixing**.

---

## 0. EXECUTIVE SUMMARY

**Goal user (instruksi awal):**
> Rapikan tiranyx.co.id supaya menyesuaikan kebutuhan pendaftaran Midtrans (credit cards activation) + PayPal direct.

**Yang Midtrans butuhkan dari tiranyx.co.id:**
1. Konfirmasi midtrans akan digunakan untuk transaksi apa saja
2. Tambah produk + harga di website merchant
3. Lampirkan lisensi kominfo (untuk live streaming)
4. Flow transaksi PDF (dari pesan → bayar)

**Yang harus dibangun:**
1. Halaman Client + integrasi OPIX
2. Freemium tools flow (Design tool, Brand Guidelines)
3. SaaS white label (bundle digital, delivery via email / GitHub)
4. Waitlist 4 produk riset (Migancore, Mighan World, Sidixlab, Ixonomic Coin)
5. Alur order: display → detail → FAQ per produk
6. CMS lengkap (settings, pricing, CRUD, kategori, delivery type, order list)

**Constraint user:**
- ❌ JANGAN ubah design system & landing page Home
- ✅ Halaman lain boleh disesuaikan untuk UX
- ✅ SEO friendly, every page standing alone with URL
- ✅ Boleh buat repo baru di organisasi Tiranyx
- ✅ Modular — jangan rebutan database antar aplikasi

**Status sekarang (2026-05-05 11:00 UTC):**
- 🟡 tiranyx.co.id homepage UP (HTTP 200)
- 🔴 tiranyx.co.id checkout 500 (better-sqlite3 native binary belum compile untuk Node v24)
- 🟢 revolusitani.com FIXED (was pre-existing error, PM2 restart cured)
- 🟡 ixonomic.com — perlu check (user lapor error)
- 🟡 mighan — perlu check
- 🟢 22 PM2 processes lain online (tidak terganggu)
- 🔴 PayPal sandbox creds bocor di commit `0a1cef4` (perlu rotate)
- 🟢 Local dev env tiranyx-web jalan (npm install + dev ready)

---

## 1. ROOT CAUSE ANALYSIS — Kenapa hari ini chaos

### Issue 1: better-sqlite3 ABI mismatch
- Node v24.14.1 + node-gyp v12.2.0 punya bug: `.deps` directory tidak ter-create
- 4 parallel rebuild processes spawned → fight lock file
- **Impact:** tiranyx checkout 500

### Issue 2: Database "rebutan" antar aplikasi
- VPS jalanin 22 PM2 processes (tiranyx, ixonomic-*, revolusitani, mighan, sidix-*, dll)
- Beberapa share PostgreSQL instance di localhost:5432
- Saat saya rebuild tiranyx, IO pressure tinggi → connection pool habis → ixonomic/mighan/revolusitani timeout
- **Impact:** Multiple sites down

### Issue 3: Reactive fixing tanpa map dependency
- Saya patch satu tempat → break tempat lain
- Tidak ada E2E test untuk catch breakage
- **Impact:** Lost user trust + 3+ hours wasted

### Issue 4: Credentials di git history
- Commit `0a1cef4` di tiranyx-web push PayPal sandbox creds
- GitGuardian alert
- **Impact:** Security risk (sandbox-only, low severity)

### Issue 5: Tidak follow PROTOCOL.md
- Saya skip onboarding read VISION/RESEARCH/JOURNAL
- Skip riset 3 sumber sebelum eksekusi
- **Impact:** Decisions tanpa context

---

## 2. RE-MAPPING — Current State (terverifikasi)

### 2.1. VPS — 22+ PM2 Processes (jangan diganggu)

| ID | Process | Port | Domain | Status |
|----|---------|------|--------|--------|
| 5 | **tiranyx** | 3001 | tiranyx.co.id | 🔴 checkout 500 |
| 1 | shopee-gateway | - | apix.tiranyx.co.id | 🟢 |
| 23 | gateway | - | tiranyx-gateway | 🟢 |
| 9 | ixonomic-landing | - | ixonomic.com | 🟡 cek |
| 10-16 | ixonomic-* (7 services) | - | *.ixonomic.com | 🟢 |
| 19 | brangkas-dashboard | - | brx.ixonomic.com | 🟢 |
| 20 | ixonomic-adm | 3008 | adm.ixonomic.com | 🟢 |
| 25 | mighan-web | - | mighan.com | 🟡 cek |
| 3 | galantara-mp | - | galantara.io | 🟢 |
| 0 | revolusitani | - | revolusitani.com | 🟢 fixed |
| 21,4,26 | sidix-* (3 services) | - | sidixlab.com | 🟢 |
| 2 | abra-website | - | abrabriket.com | ⚪ skip |

**Database connections:**
- tiranyx-web → SQLite (file: /www/wwwroot/tiranyx.co.id/data/tiranyx.db)
- revolusitani-app → PostgreSQL localhost:5432/revolusitani
- ixonomic-* → likely shared DB (perlu audit)
- mighan-web → unknown (perlu audit)

**Critical insight:** SQLite per app = isolated. PostgreSQL shared = risk of "rebutan" connection pool.

### 2.2. GitHub Repos (relevant ke Tiranyx)

**fahmiwol** (personal, 25+ repos):
- `tiranyx-web` — MAIN website (private, TS, Next.js 16)
- `tiranyx-gateway` — Backend orchestrator
- `bg-maker` — Brand Guideline Builder
- `sociyx` — Social Media SaaS (public)
- `opix_tiranyx` — OPIX Agency CRM (EMPTY placeholder!)
- `mighan-coin`, `mighan-embed`, `mighan-api`, `mighantect-3d`, `mighan-web`
- `kontrol-tiranyx` (Ixonomic admin), `Dompet-tiranyx`, `Uhud-tiranyx`, `Raumah-tiranyx`, `bank-tiranyx`
- `sidix`, `sidix-research`
- `galantara`
- `wa-gateway`, `claudefree`, `tiranyx-game`
- `stepbysteph`, `micro-saas`, `omnyx_tiranyx`, `saas_tiranyx`
- `fahmi-direction` (master hub strategic docs)

**tiranyx org**:
- `migancore` (public, Python)
- `migancore-community`
- `migancore-platform` (private)
- `sidix` (public, mirror)

**Key gap:** `opix_tiranyx` repo EMPTY — actual OPIX code di `C:\Users\ASUS\Projects\agency-os` (local only).

### 2.3. tiranyx-web Current Structure

```
tiranyx-web/
├── app/
│   ├── (site)/         ← Public pages (home, about, blog, products, tools, services, ...)
│   ├── (dashboard)/    ← User dashboard (orders, wallet, profile, results, tools)
│   ├── (platform)/     ← Platform area (apps, licenses, opix, tools, wallet, workspace)
│   └── admin/          ← CMS (posts, products, orders, users, contacts, settings, ...)
├── lib/
│   ├── ai/             ← AI helpers
│   ├── cms/             ← CMS logic (sebagian)
│   ├── db/              ← DB layer
│   │   ├── index.ts     ← getDb singleton
│   │   ├── schema.ts    ← initSchema (40+ tables di 1 file SQLite)
│   │   ├── migrations-*.ts ← per-domain migrations
│   │   └── *.ts         ← per-domain queries
│   ├── seo/             ← SEO helpers
│   ├── nav-config.ts    ← Nav structure
│   ├── products-data.ts ← HARDCODED products (10 products)
│   ├── tools-data.ts    ← HARDCODED tools (8 tools)
│   ├── services-data.ts ← Services
│   ├── user-auth.ts, auth.ts ← Auth (split admin vs user)
│   ├── admin-nav.ts, site.ts
│   └── ...flat files
├── components/
│   ├── sections/        ← Page sections (Hero, CTA)
│   ├── blog/, products/, portfolio/, resources/
│   ├── FlexibleCheckoutClient.tsx
│   ├── CheckoutClient.tsx
│   ├── Navbar.tsx, SiteHeader.tsx
│   └── ...flat
├── data/
│   └── tiranyx.db       ← Single SQLite (40+ tables, mixed domains)
├── docs/                ← MD documentation (10+ files)
├── next.config.ts
└── package.json
```

**Issue:** Mixed concerns, hard to trace, single DB shared everything.

---

## 3. TARGET ARCHITECTURE — Modular per Feature

### 3.1. Folder Layout (feature-first)

```
tiranyx-web/
├── src/
│   ├── app/                  ← Next.js routes (THIN — delegates ke features)
│   │   ├── (site)/
│   │   ├── (dashboard)/
│   │   ├── (platform)/
│   │   └── admin/
│   │
│   ├── features/             ← MODULAR — per business domain
│   │   ├── auth/
│   │   ├── catalog/          ← products, tools, services
│   │   ├── content/          ← blog, case-studies, portfolio, resources
│   │   ├── orders/
│   │   ├── payments/         ← midtrans/, paypal/
│   │   ├── wallet/
│   │   ├── ai-tools/
│   │   ├── waitlist/
│   │   ├── admin/
│   │   ├── opix/             ← integrasi OPIX
│   │   ├── client/           ← halaman client management
│   │   └── navigation/
│   │
│   ├── shared/               ← Cross-feature primitives
│   │   ├── ui/               ← Button, Card, Input (no business logic)
│   │   ├── lib/              ← Generic utils
│   │   └── hooks/
│   │
│   ├── infrastructure/       ← External adapters
│   │   ├── db/               ← SQLite connection + migrations
│   │   ├── env.ts            ← Type-safe env
│   │   └── logger.ts
│   │
│   └── config/
│
├── data/                     ← Per-domain SQLite files (ATAU single DB dengan prefix)
├── docs/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/                  ← Playwright
└── ...
```

### 3.2. Database Strategy — Recommended: Per-Domain SQLite Files

```
data/
├── auth.db        ← users, sessions, roles, password_resets
├── content.db     ← blog_posts, case_studies, portfolio_items, resources
├── catalog.db     ← products, product_tiers, product_faqs, tools, services
├── orders.db      ← product_orders, topup_orders, order_items
├── wallet.db      ← platform_wallets, platform_transactions
├── waitlist.db    ← waitlist_entries
├── ai_tools.db    ← saved_contents, tool_outputs
└── settings.db    ← site_settings, sections, shop_connections
```

**Rationale:**
- ✅ Crystal clear per-domain isolation
- ✅ Backup/restore per domain trivially (`cp orders.db orders-backup.db`)
- ✅ Tidak ada "rebutan" lock antar domain
- ✅ Each app feature has clear DB ownership
- ⚠️ No JOINs antar domain (acceptable — query then merge in app code)

**Alternative (if JOINs critical):** Single DB dengan table prefix (`auth_users`, `orders_products`).

### 3.3. Module Contract

Setiap feature folder:
```
features/orders/
├── index.ts              ← Public API exports
├── README.md             ← What, dependencies, gotchas
├── types.ts              ← TS interfaces
├── lib/
│   ├── createOrder.ts
│   ├── getOrderStatus.ts
│   └── ...
├── components/
│   ├── CheckoutClient.tsx
│   └── OrderRow.tsx
├── api/
│   ├── create.route.ts
│   └── webhook.route.ts
├── db/
│   ├── orders.db         ← SQLite for this domain
│   └── queries.ts
└── __tests__/
    ├── createOrder.test.ts
    └── e2e/
        └── checkout.spec.ts
```

**Import rules:**
- Feature can import from `shared/`, `infrastructure/`, `config/`
- Feature should NOT import from another feature directly (use events / shared interfaces)
- Routes (`app/`) thin — delegate ke `features/<domain>/index.ts`

### 3.4. E2E Testing — Playwright

```
tests/e2e/
├── critical/
│   ├── homepage.spec.ts            ← Homepage loads, nav renders
│   ├── checkout-tools.spec.ts      ← Tools order flow
│   ├── checkout-saas.spec.ts       ← SaaS subscription order
│   ├── checkout-beta.spec.ts       ← Founding member onetime
│   └── admin-login.spec.ts         ← Admin auth + dashboard
├── flows/
│   ├── user-journey-shopper.spec.ts  ← Browse → product detail → checkout → success
│   ├── user-journey-tool-user.spec.ts ← Pick tool → use freemium → upgrade
│   └── client-journey.spec.ts        ← Login → manage services → invoice
└── playwright.config.ts
```

**Pre-deploy gate:** All E2E pass before push to VPS. CI runs on every PR.

---

## 4. PAGES & FLOWS — Mapped to User Requirements

### 4.1. Public Pages (sudah ada, perlu polish)

| URL | Content | Priority | Status |
|-----|---------|----------|--------|
| `/` | Homepage (DON'T CHANGE design) | — | ✅ |
| `/about` | About | Low | ✅ |
| `/blog`, `/blog/[slug]` | Blog | Med | ⚠️ Image issue |
| `/portfolio`, `/portfolio/case-studies` | Portfolio | Low | ✅ |
| `/services`, `/services/[id]` | Jasa | Med | ⚠️ Pricing missing |
| `/products` | SaaS catalog | High | ⚠️ Pricing missing |
| `/products/[slug]` | Product detail + FAQ | **Critical** | ❌ Banyak slug 404 |
| `/tools`, `/tools/[slug]` | Tools | High | ⚠️ Pricing missing |
| `/demos` | Live demos | Med | ✅ |
| `/games`, `/galantara` | Games | Low | ✅ |
| `/resources` | Free downloads | Low | ✅ |
| `/contact` | Contact form | Low | ✅ |
| `/waitlist` | Beta program signup | High | ✅ |
| `/checkout` | Order flow | **Critical** | 🔴 500 error |

**Pages to BUILD (per user instruksi):**

| URL | Purpose | Priority |
|-----|---------|----------|
| `/client` | Client management hub + OPIX integration | High |
| `/saas` | SaaS white label catalog (bundle digital) | High |
| `/pricing` | Single page summary semua harga | **Critical for Midtrans** |
| `/products/migancore-beta` (detail) | Migancore detail + FAQ + waitlist CTA | High |
| `/products/mighan-world-beta` | Mighan World detail | High |
| `/products/sidixlab-beta` | Sidix detail | High |
| `/products/ixonomic-beta` | Ixonomic Coin detail | High |
| `/legal/midtrans-flow.pdf` | Static PDF for Midtrans submission | **Critical** |

### 4.2. Admin CMS (sudah ada, perlu lengkap)

| URL | Function | Status |
|-----|----------|--------|
| `/admin/login` | Login | ✅ |
| `/admin` | Dashboard stats | ✅ |
| `/admin/posts` | Blog CRUD | ✅ |
| `/admin/products` | Product CRUD | ⚠️ Cek if functional |
| `/admin/portfolio` | Portfolio CRUD | ✅ |
| `/admin/case-studies` | Case studies CRUD | ✅ |
| `/admin/resources` | Resources CRUD | ✅ |
| `/admin/demos` | Demos CRUD | ✅ |
| `/admin/contacts` | Contact submissions | ✅ |
| `/admin/orders` | Order management (NEW) | ✅ |
| `/admin/users` | User management (NEW) | ✅ |
| `/admin/settings` | Site settings | ✅ |
| `/admin/sections` | Page sections content | ✅ |
| `/admin/saas` | SaaS module | ❌ STUB |
| `/admin/ai-writer` | AI content writer | ✅ |

**Admin to BUILD:**
- `/admin/saas` — manage white-label bundles
- `/admin/orders/[id]` — order detail + status change + delivery tracking
- `/admin/users/[id]` — user detail + edit role + history
- `/admin/clients` — client management (related ke /client public page)
- `/admin/products/[id]/faq` — FAQ CRUD per product
- `/admin/products/[id]/howto` — How-to-use CRUD per product
- `/admin/products/[id]/delivery` — Delivery type + instructions per product

### 4.3. User Dashboard (sudah ada, polish)

| URL | Function | Status |
|-----|----------|--------|
| `/dashboard` | Overview | ✅ |
| `/dashboard/wallet` | Coin top-up (Perak/Emas/Berlian) | ✅ |
| `/dashboard/tools` | Tool catalog | ✅ |
| `/dashboard/results` | Saved tool outputs | ✅ |
| `/dashboard/orders` | Order history | ✅ |
| `/dashboard/profile` | Profile edit | ✅ |
| `/dashboard/subscriptions` | Active subs management | ❌ NEW |
| `/dashboard/downloads` | Digital deliveries | ❌ NEW |

### 4.4. Order Flows — Per Product Category (each has different flow)

**Update 2026-05-05 (per user feedback):** Flow harus beda per kategori, jangan disamain.

**Product Categories:**
| Kategori | Examples | Pricing Model | Flow |
|----------|----------|---------------|------|
| **A. Utility Tools (Freemium)** | QR Generator, UTM Builder, Color Palette | Free + paid unlimited | Flow A |
| **B. AI Tools (Quota-based)** | AI Article Writer, Caption Gen, Image AI, TTS | Free quota + coin top-up | Flow B |
| **C. Live Demos** | SocioStudio Lite, WA Bot Demo, OPIX Demo | Demo free → upsell SaaS | Flow C |
| **D. SaaS Subscriptions** | Market Intelligence, Content Builder, SEO Tools, Report Dashboard | Monthly/yearly subscription | Flow D |
| **E. White-label Bundles** | OPIX, SOCIYX, WA Suite | One-time setup + license | Flow E |
| **F. Beta / Founding Member** | Migancore, Sidix, Mighan, Ixonomic | One-time USD (lifetime) | Flow F |
| **G. On-demand Services** | Market Research, Content Batch, Brand Refresh | One-time IDR | Flow G |

---

#### Flow A — Utility Tools (Freemium → Pro Subscription)

**Use case:** User butuh QR code 50x sehari, free tier cuma 5x.

```
[/tools/qr-code-generator] → Use freemium (5/day quota)
                          → Quota habis → Modal "Upgrade Pro Rp 99k/bulan"
                          → Click upgrade → /checkout?type=tools-pro&billing=monthly
                          → Login required → Midtrans Snap (IDR)
                          → Webhook → Subscription active
                          → Unlimited unlock
```

**Data:**
- `tools.db` → tool_metadata, tool_usage_log
- `subscriptions.db` → user_subscriptions (type='tools-pro')

#### Flow B — AI Tools (Coin-based — server cost coverage)

**Use case:** User generate AI article, butuh OpenAI/Pollinations API call.

```
[/tools/ai-article-writer] → Use freemium (3/day quota)
                          → Quota habis → CTA "Top-up koin"
                          → /dashboard/wallet → Pilih package (Perak/Emas/Berlian)
                          → Midtrans Snap → Coin masuk wallet
                          → Use AI tool → Debit coin per generate
                          → No subscription needed (pay-as-you-go)
```

**Data:**
- `wallet.db` → platform_wallets, platform_transactions
- `ai_tools.db` → tool_outputs (saved generations), tool_usage_cost

#### Flow C — Live Demos (Try → Upsell SaaS)

**Use case:** User explore SocioStudio Lite, terkesan, mau subscribe full version.

```
[/demos] → Browse demo cards
        → Click [SocioStudio Lite Demo] → Open demo (limited features)
        → Banner "Like it? Get full version Rp 199k/bulan"
        → Click → /products/sociostudio → Detail page + FAQ
        → Pilih tier → /checkout (Flow D continues)
```

**Data:**
- `catalog.db` → demos, products, product_tiers, product_faqs

#### Flow D — SaaS Subscriptions (Direct buy or from demo)

**Use case:** B2B agency butuh Market Intelligence dashboard.

```
[/products/market-intelligence] → Detail page + tier comparison + FAQ
                               → Click "Pilih Pro" → /checkout?product=market-intelligence&plan=pro&billing=monthly
                               → Login required (or register)
                               → Order summary + Midtrans Snap
                               → Payment success → Subscription active
                               → User dashboard → /dashboard/subscriptions
                               → Email kredensial / akses link 1×24h
```

**Data:**
- `subscriptions.db` → user_subscriptions, subscription_tiers
- `orders.db` → product_orders

#### Flow E — White-label Bundles (One-time setup + license)

**Use case:** Agency mau white-label OPIX untuk klien mereka.

```
[/saas] → Browse white-label catalog
       → Click [OPIX Bundle] → Detail + scope + delivery info
       → "Pesan Setup" → Form (Company name, domain, brand colors, contact)
       → /checkout (one-time) → Midtrans / PayPal
       → Payment success → Order masuk admin queue
       → Admin manual setup (3-5 hari) → GitHub access invitation + setup docs
```

**Data:**
- `bundles.db` → whitelabel_bundles, bundle_orders
- `deliveries.db` → digital_deliveries (GitHub invites, email creds)

#### Flow F — Beta Founding Member (Guest, USD, onetime)

**Use case:** International researcher mau dukung Migancore.

```
[/products/migancore-beta] → Detail page (English + Indonesian)
                          → "Become Founding Member" → /checkout?product=migancore-beta&plan=beta&billing=onetime
                          → NO login required
                          → Pilih amount (USD presets $500/$1000/$2500/$5000 + custom)
                          → Toggle USD/IDR
                          → Form: Nama, Email, WA/Telegram (opsional)
                          → Pilih payment: Midtrans (IDR converted) ATAU PayPal (USD direct)
                          → Payment success → Email kredensial 1×24h + Founding badge
                          → Admin notif via WA
```

**Data:**
- `orders.db` → product_orders (with guest_name, guest_email, guest_contact)
- `beta_members.db` → beta_members, beta_access

#### Flow G — On-demand Services (Custom delivery)

**Use case:** Brand butuh Market Research Report.

```
[/services/market-research] → Detail + scope + sample
                           → "Pesan Sekarang" → Form (Brand, industry, scope, target market)
                           → Auto quote (Rp 3-15M) atau "Hubungi sales" untuk custom
                           → /checkout (one-time) → Midtrans
                           → Payment success → Order masuk operations queue
                           → Tim deliver dalam 3-10 hari → PDF/ZIP via email
                           → User dashboard → /dashboard/downloads
```

**Data:**
- `service_orders.db` → service_orders, service_deliverables

---

### 4.5. Database per Flow (Modular)

```
data/
├── auth.db                ← All flows (user identity)
├── catalog.db             ← Flow A,B,C,D,E,F,G product metadata
├── wallet.db              ← Flow B (coin top-up + balance)
├── subscriptions.db       ← Flow A,D (recurring billing)
├── orders.db              ← All flows (order records)
├── bundles.db             ← Flow E (white-label specific)
├── beta_members.db        ← Flow F (founding members)
├── service_orders.db      ← Flow G (custom services)
├── deliveries.db          ← Flow E,F,G (digital delivery tracking)
├── content.db             ← Blog, case studies, portfolio (no order flow)
└── settings.db            ← Site config
```

(Flow 1-4 di atas sudah replaced dengan Flow A-G yang lebih granular)

---

## 5. MIDTRANS DOCUMENT REQUIREMENTS

### 5.1. Konfirmasi penggunaan Midtrans

**Draft email balasan ke Tia (Midtrans Sales):**

> Halo Tia,
>
> Mengenai penggunaan Midtrans di tiranyx.co.id, kami menggunakan untuk **4 jenis transaksi**:
>
> 1. **Top-up Koin Digital (Ixonomic Wallet)** — Customer top-up koin internal untuk akses tools AI di platform. Range: Rp 50.000 – Rp 5.000.000 per transaksi. Frequency: high (daily).
>
> 2. **Subscription SaaS** — Langganan bulanan/tahunan untuk tools AI dan SaaS B2C. Range: Rp 99.000 – Rp 5.000.000 per bulan. Frequency: medium.
>
> 3. **One-time Founding Member / Beta Access** — Pembelian akses lifetime untuk produk beta (Migancore, Sidix Lab, Mighan World, Ixonomic). USD-denominated (charged in IDR). Range: Rp 7.500.000 – Rp 78.500.000 per transaksi.
>
> 4. **On-demand Services** — Order jasa custom (Market Research, Audit, Brand Refresh, dll.). Range: Rp 1.500.000 – Rp 15.000.000. Frequency: low (1-5/month).
>
> Semua transaksi dilakukan oleh konsumen ritel ke PT Tiranyx Digitalis Nusantara. Untuk klien enterprise B2B (jasa agency, KOL campaigns, ads management) tidak via Midtrans — pakai invoice manual.

### 5.2. Produk + harga di website

**Action:** Buat halaman `/pricing` (single page summary) + pastikan semua product/tool punya pricing visible. Detail di section 4.1.

### 5.3. Lisensi Kominfo

**Status:** User action (Fahmi handle sendiri):
- Cek di pse.kominfo.go.id apakah PT Tiranyx terdaftar
- Jika belum: daftar PSE Privat (gratis online) untuk layanan live streaming

### 5.4. Flow transaksi PDF

**Action:** Generate PDF dari TRANSACTION-FLOW.md di repo platform-docs (Indonesian).
- Tools: Pandoc atau wkhtmltopdf
- Output: `docs/midtrans/TRANSACTION-FLOW.pdf`
- Email ke Tia sebagai attachment

---

## 6. PAYPAL INTEGRATION (Direct, Live)

**Credentials sudah di VPS `.env.local`** (live + sandbox, app name `tiranyxweb`).

**Implementation plan:**

```
features/payments/paypal/
├── client.ts             ← PayPal SDK wrapper
├── createOrder.ts        ← Create PayPal order
├── captureOrder.ts       ← Capture after approval
├── webhook.ts            ← Verify webhook signature
└── types.ts
```

**API routes:**
- `POST /api/orders/create-paypal` — for guest founding member checkout USD
- `POST /api/orders/capture-paypal` — capture after PayPal popup approval
- `POST /api/payment/paypal-webhook` — verify + update order status

**UI:**
- Add "Pay with PayPal" button di FlexibleCheckoutClient (founding member only)
- Toggle: Midtrans (IDR) vs PayPal (USD)

**Webhook setup (manual via PayPal dashboard):**
- URL: `https://tiranyx.co.id/api/payment/paypal-webhook`
- Events: `CHECKOUT.ORDER.APPROVED`, `PAYMENT.CAPTURE.COMPLETED`, `PAYMENT.CAPTURE.DENIED`

---

## 7. INFRASTRUCTURE FIXES

### 7.1. better-sqlite3 ABI Fix
- **Option A:** Update node-gyp globally (already attempted)
- **Option B:** Pre-create `.deps` directory tree manually then run make
- **Option C:** Switch to Node v22 LTS (well-supported)
- **Option D:** Use libsql or other SQLite wrapper that doesn't need native compile

**Recommendation:** Option C (Node v22 LTS) — most stable long-term.

### 7.2. PostgreSQL "rebutan" Issue
- Apps that share PostgreSQL: revolusitani, ixonomic-*, possibly mighan
- Risk: connection pool exhaustion when 1 app misbehaves
- **Solution:**
  1. Audit which apps use which DB
  2. Add `?connection_limit=10&pool_timeout=30` to each app's DATABASE_URL
  3. Add health check endpoints → PM2 restart on DB error
  4. Monitor with `pm2 logs --lines 100`

### 7.3. PayPal Sandbox Credential Rotation
- Bocor di commit `0a1cef4`
- Action: Fahmi rotate via PayPal Developer Dashboard (5 min)
- Update VPS `.env.local` `PAYPAL_SANDBOX_CLIENT_SECRET=<new>`

### 7.4. Build Pipeline
- ❌ Stop using `npm run build` (Turbopack — no BUILD_ID)
- ✅ Use `npm run build:webpack`
- ❌ Stop running parallel rebuilds
- ✅ Use feature flags for new code paths

---

## 8. MIGRATION PLAN — Phased, No Breakage

### Phase 0 — Stabilize Production (TODAY, 2 hours)
1. ✅ Document current state (DONE — this MD)
2. Fix tiranyx checkout 500 (better-sqlite3 ABI):
   - Try Option C: install Node v22.18.0 via NVM
   - Update PM2 ecosystem to use v22
   - npm install for v22 ABI
   - Test checkout
3. Verify ixonomic.com + mighan.com (audit, fix if broken)
4. Rotate PayPal sandbox creds

**Gate:** All public sites HTTP 200, checkout HTTP 200, no other apps broken.

### Phase 1 — Foundation (1 day)
1. Setup Playwright E2E
   - Install `@playwright/test`
   - Write 5 critical specs (homepage, checkout flows, admin login)
   - Add to CI gate
2. Create folder skeleton (src/features, shared, infrastructure)
3. Add TypeScript path aliases (`@/features/*`, `@/shared/*`)
4. Add bundle analyzer
5. Run baseline performance audit (Lighthouse)

**Gate:** E2E pass on local + production. No breakage.

### Phase 2 — Database Modular (1 day)
1. Backup current `tiranyx.db`
2. Create per-domain SQLite files
3. Write migration script (copy data from monolith → per-domain)
4. Update `lib/db/index.ts` → `infrastructure/db/connections.ts`
5. Validate row counts match
6. Run E2E — should still pass

**Gate:** All E2E pass, all routes 200, data integrity verified.

### Phase 3 — Move Payments Module (1 day)
1. `features/payments/midtrans/*` from `app/api/orders/create/route.ts`
2. `features/payments/paypal/*` (NEW)
3. Update API routes to use feature module
4. Add PayPal UI to FlexibleCheckoutClient

**Gate:** Checkout E2E pass for both Midtrans + PayPal.

### Phase 4 — Move Auth (0.5 day)
1. `features/auth/lib/user-session.ts` from `lib/user-auth.ts`
2. `features/auth/lib/admin-session.ts` from `lib/auth.ts`
3. Update imports

**Gate:** Login flows work (admin + user).

### Phase 5 — Build New Pages (2 days)
1. `/pricing` — single page summary all products + prices
2. `/client` — client management hub
3. `/saas` — white-label catalog
4. `/products/migancore-beta` (detail polish)
5. `/products/mighan-world-beta`, `/products/sidixlab-beta`, `/products/ixonomic-beta`
6. PDF generation for Midtrans flow doc

**Gate:** All new pages SEO-ready (metadata, OG, schema), responsive, E2E pass.

### Phase 6 — CMS Enhancement (1.5 days)
1. `/admin/products/[id]/faq` — FAQ CRUD
2. `/admin/products/[id]/howto` — How-to-use CRUD
3. `/admin/products/[id]/delivery` — Delivery type CRUD
4. `/admin/orders/[id]` — Order detail + status change
5. `/admin/clients` — Client management

**Gate:** Admin CRUD all working, E2E covers admin flows.

### Phase 7 — SEO + Performance (1 day)
1. `app/sitemap.ts` — dynamic sitemap
2. `app/robots.ts` — verify config
3. Audit metadata on all pages
4. Add Schema.org JSON-LD
5. Bundle analyzer → optimize big chunks
6. Image optimization (next/image audit, AI image domains whitelist)
7. Submit sitemap to Google Search Console

**Gate:** Lighthouse score >90, sitemap accepted by Google.

### Phase 8 — Testing & Polish (0.5 day)
1. E2E coverage for all critical flows
2. Manual smoke test
3. Mobile responsive audit
4. Fix any bugs found

**Total estimate: ~8.5 days of work, executable over 1-2 weeks.**

---

## 9. NEW REPO PROPOSAL (per user request)

**Question:** Apakah perlu repo baru?

**Recommendation: NO new repo, refactor in-place.**

**Reasoning:**
- tiranyx-web sudah punya semua infrastructure (DB, CMS, auth, deploy)
- New repo = duplicate effort untuk auth, CMS, deploy
- Modular folder structure di tiranyx-web cukup untuk isolation
- Kalau OPIX butuh own service (CRM agency), pakai existing `opix_tiranyx` repo (currently empty)

**Exception — buat repo baru jika:**
- OPIX akan deploy ke domain berbeda (opix.tiranyx.co.id sudah separate Hono app)
- Mighan/Sidix dashboard butuh app standalone

**Proposal:** Refactor tiranyx-web modular dulu. Jika ada feature yang clearly butuh isolated deploy, spin off ke repo baru di organisasi `tiranyx`.

---

## 10. DECISION POINTS — Need User Approval

Sebelum saya eksekusi, user pilih:

### Decision 1: Database strategy
- [ ] **A. Per-domain SQLite files** (RECOMMENDED — clearest isolation)
- [ ] B. Single DB dengan table prefix
- [ ] C. PostgreSQL untuk semua (heavyweight)

### Decision 2: Better-sqlite3 fix
- [ ] **A. Switch ke Node v22 LTS** (RECOMMENDED — well-supported)
- [ ] B. Force fix node-gyp v12 + Node v24 (fragile)
- [ ] C. Replace better-sqlite3 dengan libsql

### Decision 3: Migration speed
- [ ] **A. Phase 0 + 1 dulu hari ini** (stabilize + foundation, RECOMMENDED)
- [ ] B. All 8 phases dalam 1 sprint (ambitious, risky)

### Decision 4: New repo vs refactor in-place
- [ ] **A. Refactor in-place tiranyx-web** (RECOMMENDED)
- [ ] B. Create new modular repo from scratch

### Decision 5: E2E tooling
- [ ] **A. Playwright** (RECOMMENDED — modern, fast, cross-browser)
- [ ] B. Cypress
- [ ] C. Manual testing only (faster, less safety)

### Decision 6: PayPal integration timing
- [ ] **A. Phase 3** (after foundation + payments module ready)
- [ ] B. Phase 0 (urgent for Midtrans submission)

---

## 11. WHAT I WILL NOT DO WITHOUT APPROVAL

- ❌ Touch VPS production
- ❌ Run any rebuild/install on VPS
- ❌ Push code that hasn't been tested locally
- ❌ Create new repos
- ❌ Change Home design system
- ❌ Run database migrations on production
- ❌ Modify other apps in VPS (ixonomic, mighan, etc.)
- ❌ Push credentials to git ever again

---

## 12. ACTION CHECKLIST FOR USER

User cukup balas dengan satu pesan format:

```
Decision 1: A
Decision 2: A
Decision 3: A
Decision 4: A
Decision 5: A
Decision 6: A
Mulai dengan Phase 0.
```

Saya akan eksekusi sesuai pilihan. Setiap phase end → kasih report → user approve next phase.

---

---

## 13. GRANULAR MODULAR PATTERN (per user feedback 2026-05-05)

User minta: **"modular per page, per content, per flow, per post, per input, dll"**

### 13.1. Granularity Levels

```
features/
├── orders/                      ← Domain (largest unit)
│   ├── flows/                   ← FLOW level
│   │   ├── tools-checkout/      ← Per flow
│   │   │   ├── index.ts
│   │   │   ├── steps/
│   │   │   │   ├── 1-select-tool.tsx
│   │   │   │   ├── 2-quota-check.tsx
│   │   │   │   ├── 3-topup-amount.tsx
│   │   │   │   ├── 4-midtrans-snap.tsx
│   │   │   │   └── 5-success.tsx
│   │   │   ├── state.ts         ← Flow-specific state machine
│   │   │   ├── api.ts
│   │   │   └── README.md
│   │   ├── saas-checkout/       ← Per flow
│   │   ├── beta-onetime/        ← Per flow (founding member)
│   │   └── service-order/       ← Per flow
│   │
│   ├── pages/                   ← PAGE level
│   │   ├── checkout/            ← Per page
│   │   │   ├── page.tsx
│   │   │   ├── metadata.ts      ← SEO per page
│   │   │   ├── schema.ts        ← Schema.org per page
│   │   │   └── README.md
│   │   ├── checkout-success/    ← Per page
│   │   └── order-history/       ← Per page
│   │
│   ├── inputs/                  ← INPUT level (form fields)
│   │   ├── GuestContactForm.tsx ← Per form
│   │   ├── AmountInput.tsx      ← Per input
│   │   ├── BillingToggle.tsx    ← Per input
│   │   ├── PaymentMethodSelect.tsx
│   │   └── README.md
│   │
│   ├── data/                    ← DATA level (DB queries)
│   │   ├── orders.db
│   │   └── queries/
│   │       ├── createOrder.ts
│   │       ├── getOrderById.ts
│   │       ├── listOrdersByUser.ts
│   │       └── updateOrderStatus.ts
│   │
│   └── api/                     ← API level (route handlers)
│       ├── create.route.ts
│       ├── capture.route.ts
│       ├── webhook-midtrans.route.ts
│       └── webhook-paypal.route.ts
│
├── content/
│   ├── posts/                   ← POST level
│   │   ├── blog-post/           ← Per post type
│   │   │   ├── components/
│   │   │   ├── data/
│   │   │   ├── pages/
│   │   │   └── README.md
│   │   ├── case-study/          ← Per post type
│   │   ├── portfolio-item/      ← Per post type
│   │   └── resource/            ← Per post type
│   ...
```

### 13.2. Per-Flow State Machine

Setiap flow punya state machine eksplisit:

```typescript
// features/orders/flows/beta-onetime/state.ts
type BetaCheckoutState =
  | { step: 'amount-selection'; amountUSD: number }
  | { step: 'contact-form'; amountUSD: number; partial: { name?: string; email?: string } }
  | { step: 'contact-validated'; amountUSD: number; contact: GuestContact }
  | { step: 'payment-pending'; orderId: string; snapToken: string }
  | { step: 'payment-success'; orderId: string }
  | { step: 'payment-failed'; error: string };
```

**Pro:**
- Flow visible secara explicit
- Easy testing (unit test per state transition)
- Easy debugging (current state always known)
- Easy adding new steps

### 13.3. Per-Input Component

Setiap input field reusable + tested:

```typescript
// features/orders/inputs/AmountInput.tsx
export interface AmountInputProps {
  currency: 'USD' | 'IDR';
  value: number;
  onChange: (n: number) => void;
  presets?: number[];
  min?: number;
  max?: number;
  showConversion?: boolean;
}

export function AmountInput(props: AmountInputProps) { ... }

// Tested in isolation
// __tests__/AmountInput.test.tsx
```

### 13.4. Per-Page Modular SEO

Setiap page punya own SEO module:

```typescript
// features/orders/pages/checkout/metadata.ts
import type { Metadata } from 'next';

export function generateMetadata(): Metadata {
  return {
    title: 'Checkout — Tiranyx',
    description: '...',
    openGraph: { ... },
    robots: { index: false, follow: false },  // checkout = noindex
  };
}

// features/orders/pages/checkout/schema.ts
export function checkoutSchema() {
  return {
    '@context': 'https://schema.org',
    '@type': 'CheckoutPage',
    ...
  };
}
```

---

## 14. USER TYPE CLASSIFICATION (per user feedback 2026-05-05)

User minta: **"kelompokkan user type"**

### 14.1. User Types Matrix

| Type | Auth | Access | DB Table | Use Cases |
|------|------|--------|----------|-----------|
| **Guest** | None | Public + checkout (founding member) | — | Browse, founding member purchase |
| **Individual** | Email + password | Tools, dashboard, wallet | `users` (role_id=3) | Personal tools user, top-up koin |
| **Pro** | Email + password | All Individual + paid SaaS | `users` (role_id=2) | Subscriber, premium tools |
| **Client** | Email + password | All Pro + service management | `users` (role_id=2) + `clients` | B2B agency clients, manage services via OPIX |
| **Affiliate** | Email + password | Public + commissions dashboard | `affiliates` | Refer customers, earn commission |
| **Beta** | Email (founding) | All Pro + beta access | `users` + `beta_access` | Founding members of Migancore/Sidix/etc. |
| **Admin** | Separate auth | Full CMS + analytics | `users` (role_id=1) | Tiranyx team — Fahmi + future hires |
| **Super Admin** | Separate auth | Admin + system config | `users` (role_id=0) | System admin, env management |

### 14.2. Per-Type Folder Structure

```
features/users/
├── types/
│   ├── guest/              ← Guest checkout flow only
│   │   ├── api/
│   │   └── lib/
│   ├── individual/         ← Personal user
│   │   ├── pages/
│   │   ├── components/
│   │   ├── api/
│   │   └── README.md
│   ├── pro/                ← Subscriber
│   ├── client/             ← B2B client
│   │   ├── pages/
│   │   │   └── manage-services/    ← OPIX integration
│   │   ├── components/
│   │   └── api/
│   ├── affiliate/
│   ├── beta/               ← Founding members
│   └── admin/              ← Admin functions
│
├── auth/
│   ├── login/              ← Per type? Single login → role-based redirect
│   ├── register/
│   ├── forgot-password/
│   └── session/
│
└── shared/
    ├── User.ts             ← Base type
    └── permissions.ts      ← RBAC matrix
```

### 14.3. Per-Type Database Tables

```
data/auth.db
├── users                   (role_id determines type)
├── sessions
├── roles
├── password_resets
├── email_verifications
└── ...

data/clients.db             ← NEW for B2B client features
├── clients                 (1-to-1 with users where role_id=2)
├── client_services         (services purchased per client)
├── client_invoices         (manual invoices via OPIX)
└── ...

data/affiliates.db          ← NEW
├── affiliates
├── referrals
├── commissions
└── ...

data/beta_members.db        ← NEW
├── beta_members
├── beta_access (per product)
├── feedback
└── ...
```

### 14.4. Per-Type Routes

```
app/(site)/                  ← Public + guest
├── login/                   ← Single login, role-based redirect after
├── register/

app/(individual)/            ← role_id=3 (Individual user)
├── dashboard/
│   ├── tools/
│   ├── wallet/
│   └── ...

app/(pro)/                   ← role_id=2 + paid (Pro subscriber)
├── dashboard/
│   ├── subscriptions/
│   └── ...

app/(client)/                ← role_id=2 + has client record (B2B)
├── client/
│   ├── services/
│   ├── invoices/
│   └── opix/                ← OPIX integration

app/(affiliate)/             ← Affiliate
├── affiliate/
│   ├── dashboard/
│   ├── referrals/
│   └── commissions/

app/(beta)/                  ← Beta founding members
├── beta/
│   ├── access/              ← Per product access
│   ├── feedback/
│   └── community/

app/admin/                   ← Admin (separate route group)
└── ...
```

### 14.5. Permission Matrix (RBAC)

```typescript
// features/users/shared/permissions.ts

export const PERMISSIONS = {
  // Public actions
  'view:public-pages': ['guest', 'individual', 'pro', 'client', 'affiliate', 'beta', 'admin'],
  'use:freemium-tools': ['guest', 'individual', 'pro', 'client', 'affiliate', 'beta', 'admin'],

  // Authenticated user actions
  'view:dashboard': ['individual', 'pro', 'client', 'affiliate', 'beta', 'admin'],
  'topup:wallet': ['individual', 'pro', 'client', 'beta'],
  'use:premium-tools': ['pro', 'client', 'beta', 'admin'],

  // Pro+ actions
  'subscribe:saas': ['individual', 'pro', 'client'],
  'view:order-history': ['individual', 'pro', 'client', 'beta'],

  // Client-only
  'manage:client-services': ['client'],
  'access:opix': ['client', 'admin'],
  'view:client-invoices': ['client'],

  // Affiliate-only
  'view:affiliate-dashboard': ['affiliate'],
  'view:commissions': ['affiliate'],

  // Beta-only
  'access:beta-product': ['beta', 'admin'],
  'submit:beta-feedback': ['beta'],

  // Admin only
  'cms:edit': ['admin'],
  'view:all-orders': ['admin'],
  'manage:users': ['admin'],
  'config:system': ['admin'],  // super-admin only ideally
} as const;

export function can(user: User | null, action: keyof typeof PERMISSIONS): boolean {
  const allowedRoles = PERMISSIONS[action];
  if (!user) return allowedRoles.includes('guest');
  return allowedRoles.includes(user.type);
}
```

### 14.6. Login Flow with Role-Based Redirect

```
User input login
  ↓
Validate credentials
  ↓
Read user.role_id + user.has_client_record + user.has_affiliate_record + user.has_beta_access
  ↓
Determine primary type
  ↓
Redirect:
  - role_id=1 (admin) → /admin
  - has_client_record → /client/dashboard
  - has_affiliate_record → /affiliate/dashboard
  - has_beta_access → /beta/access
  - role_id=2 (pro) → /dashboard
  - role_id=3 (individual) → /dashboard
```

### 14.7. Decision: New Decision Point 7

**Decision 7: User type granularity**
- [ ] **A. 7 types** (Guest, Individual, Pro, Client, Affiliate, Beta, Admin) — RECOMMENDED, complete coverage
- [ ] B. 4 types (Guest, User, Client, Admin) — simpler, less granular
- [ ] C. Keep current (Admin + Individual only) — minimal change

---

---

## 15. ADDENDUM — User Clarifications 2026-05-05 (LATEST)

### 15.1. User Types — Simplified to 4 (override section 14)

```
1. PERSONAL          ← Public user (registered or guest)
                     ← Use freemium tools, top-up koin, beli SaaS subscription
                     ← Dashboard: /dashboard

2. CLIENT            ← B2B agency client
                     ← Subscribe layanan, manage services
                     ← Dashboard: opix.tiranyx.co.id (SEPARATE app)

3. INTERNAL TIRANYX  ← Tim Tiranyx (project manager, content writer, sales, dll)
                     ← Manage client services, bantu klien
                     ← Dashboard: opix.tiranyx.co.id (SEPARATE app, role-based)

4. ADMIN             ← Fahmi + super admin
                     ← Full CMS + system config
                     ← Dashboard: /admin (di tiranyx.co.id)
```

**Simplifikasi:**
- ❌ Hapus: Pro, Affiliate, Beta sebagai user types (those are sub-states, bukan separate type)
- ✅ Personal user bisa jadi Beta (sub-state) — beli founding member tetap Personal type
- ✅ Personal bisa subscribe SaaS — sub-state "subscription_active"
- ✅ Affiliate program optional later (sub-state lagi, bukan type baru)

### 15.2. Routing per User Type

```
[User type]              [Route]                          [Auth]
PERSONAL (registered)    /dashboard/*                     ← user-auth
PERSONAL (guest)         /                                ← no auth
CLIENT + INTERNAL        opix.tiranyx.co.id (separate)    ← own auth
ADMIN                    /admin/*                         ← admin-auth
```

**Important:**
- `tiranyx.co.id` = public + personal user dashboard + admin
- `opix.tiranyx.co.id` = client + internal team management (sudah separate Hono app)
- DON'T merge OPIX into main site

### 15.3. Order Flow CORRECTION — Flow D, E, F via Manual Invoice

**Override Flow D, E, F yang automatic Midtrans Snap:**

User clarifies: **Flow D (SaaS), E (White-label), F (Beta Founding) = MANUAL INVOICE flow**, bukan inline Snap.

```
[User] → Order form (nama, email, WA, produk, amount/tier)
       → Submit form
       → Form data:
         - Saved ke DB (status='pending_invoice')
         - Notif ke admin WA (otomatis via Fonnte/Wablas)
       → Admin terima notif WA
       → Admin chat user via WA (clarify scope, custom requirements)
       → Admin create invoice via Midtrans Invoice API (atau panel manual)
       → Midtrans generate payment link
       → Admin kirim link payment via WA + email ke user
       → User klik link → bayar (credit card / VA / e-wallet)
       → Webhook confirm paid → status='paid' di DB
       → Admin manual kirim credentials/bundle via email:
         - SaaS: subscription credentials + onboarding doc
         - White-label: GitHub access invitation + setup docs
         - Founding Member: product access + community invite
       → Status='delivered' di DB
       → User dashboard menampilkan history + delivery
```

**Why manual invoice (not Snap):**
- High-value transactions (>Rp 5M typically)
- Often custom scope (whitelabel especially)
- Need human verification (KYC for B2B)
- Better UX for B2B (sales-led, not self-serve)

**Snap is OK for:**
- Flow A (Utility tools Pro) — small price (Rp 99k/month), self-serve
- Flow B (AI tools coin top-up) — instant gratification
- Flow C (Live demo upsell) — small SaaS subs

### 15.4. Order Flow Re-categorization

| Flow | Category | Payment Method | Confirmation |
|------|----------|---------------|--------------|
| A | Utility tools Pro subscription | **Midtrans Snap** (auto) | Instant |
| B | AI tools coin top-up | **Midtrans Snap** (auto) | Instant |
| C | Live demo → small SaaS | **Midtrans Snap** (auto) | Instant |
| **D** | **SaaS subscription B2B** | **Manual Invoice via WA** | Admin sends |
| **E** | **White-label bundle** | **Manual Invoice via WA** | Admin sends |
| **F** | **Founding Member (guest)** | **Manual Invoice via WA** | Admin sends |
| **G** | **On-demand services** | **Manual Invoice via WA** | Admin sends |

### 15.5. New Database Tables for Manual Invoice Flow

```sql
-- orders.db
CREATE TABLE order_inquiries (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  inquiry_id TEXT UNIQUE,
  type TEXT,  -- 'saas' | 'whitelabel' | 'founding-member' | 'service'
  product_slug TEXT,
  customer_name TEXT,
  customer_email TEXT,
  customer_wa TEXT,
  customer_telegram TEXT,
  amount_idr INTEGER,
  amount_usd REAL,
  custom_notes TEXT,
  status TEXT DEFAULT 'new',  -- 'new' | 'reviewed' | 'invoice_sent' | 'paid' | 'delivered' | 'cancelled'
  invoice_url TEXT,           -- Midtrans invoice link
  payment_method TEXT,
  paid_at TEXT,
  delivery_notes TEXT,
  delivered_at TEXT,
  created_at TEXT DEFAULT (datetime('now')),
  updated_at TEXT DEFAULT (datetime('now'))
);

CREATE TABLE order_inquiry_messages (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  inquiry_id TEXT,
  sender TEXT,  -- 'admin' | 'customer' | 'system'
  channel TEXT, -- 'wa' | 'email' | 'in-app'
  content TEXT,
  created_at TEXT DEFAULT (datetime('now')),
  FOREIGN KEY (inquiry_id) REFERENCES order_inquiries(inquiry_id)
);
```

### 15.6. Admin Panel Scope (Confirmed)

```
/admin/                           ← Dashboard overview (stats)
├── /cms/                         ← CONTENT MANAGEMENT
│   ├── /pages                    ← Static pages (about, contact, dll)
│   ├── /posts                    ← Blog posts CRUD
│   ├── /content/sections         ← Page sections (hero, CTA blocks)
│   ├── /content/case-studies
│   ├── /content/portfolio
│   ├── /content/resources
│   └── /content/demos
│
├── /users/                       ← USER MANAGEMENT (Personal users)
│   ├── /users                    ← List all
│   ├── /users/[id]               ← Detail + edit role + history
│   ├── /users/[id]/orders        ← User order history
│   └── /users/[id]/wallet        ← User wallet balance + transactions
│
├── /clients/                     ← CLIENT MANAGEMENT (B2B)
│   ├── /clients                  ← List all clients
│   ├── /clients/[id]             ← Detail + services + invoices
│   ├── /clients/[id]/services    ← Active services per client
│   └── /clients/[id]/messages    ← WA chat history (synced from Fonnte)
│
├── /products/                    ← PRODUCT MANAGEMENT
│   ├── /products                 ← List all (SaaS + tools + services)
│   ├── /products/[id]            ← Edit details + pricing + tiers
│   ├── /products/[id]/faq        ← FAQ CRUD per product
│   ├── /products/[id]/howto      ← How-to-use CRUD
│   └── /products/[id]/delivery   ← Delivery type + instructions
│
├── /orders/                      ← ORDER MANAGEMENT (all types)
│   ├── /orders                   ← List all (filter by type/status)
│   ├── /orders/[id]              ← Order detail
│   ├── /orders/[id]/invoice      ← Generate Midtrans invoice
│   ├── /orders/[id]/messages     ← Chat history
│   └── /orders/[id]/deliver      ← Mark delivered + send creds via email
│
├── /tools/                       ← TOOLS MANAGEMENT
│   ├── /tools                    ← List + enable/disable
│   ├── /tools/[id]/usage         ← Usage stats + cost
│   └── /tools/[id]/quota         ← Edit free quota
│
├── /settings/                    ← SITE SETTINGS
│   ├── /settings/site            ← Title, description, logo, colors
│   ├── /settings/seo             ← Robots, sitemap, GA, GTM
│   ├── /settings/payments        ← Midtrans + PayPal config
│   ├── /settings/notifications   ← Fonnte WA, email SMTP
│   └── /settings/team            ← Internal team members
│
└── /analytics/                   ← ANALYTICS DASHBOARD
    ├── /analytics/revenue        ← Revenue by month + by product
    ├── /analytics/users          ← User growth + activity
    └── /analytics/orders         ← Conversion funnel
```

### 15.7. Order Form Pages (Manual Invoice Flow)

Setiap product yang pakai manual invoice butuh order form page:

```
/products/<slug>/order        ← Order form (D: SaaS subscription)
/saas/<slug>/order            ← Order form (E: White-label)
/products/<beta-slug>/join    ← Order form (F: Founding member)
/services/<slug>/order        ← Order form (G: On-demand service)
```

**Form fields generic:**
- Nama lengkap (required)
- Email (required, validated)
- WhatsApp (required)
- Telegram (optional)
- Company / Brand (for B2B)
- Custom requirements / Scope notes
- Budget range / Amount (USD or IDR)
- Preferred contact time

**On submit:**
1. POST to `/api/orders/inquiry` → save to `order_inquiries` table
2. Trigger Fonnte WA notif ke admin: "New {type} inquiry from {name}, slug={product_slug}"
3. Auto-reply WA ke customer: "Thanks {name}, tim kami akan reach out dalam 1-24 jam"
4. Show success page: "Inquiry received! Cek WA/email Anda"

### 15.8. opix.tiranyx.co.id — Client x Internal Team

**Status:** SEPARATE Hono app (sudah ada di apix.tiranyx.co.id, di-rebrand ke opix)

**Scope opix:**
- Client: Browse own services, view invoices, message internal team
- Internal: View all clients, manage services, send invoices, fulfill orders

**Integration dengan tiranyx.co.id:**
- Auth shared (single sign-on via cookie domain `*.tiranyx.co.id`)
- Order inquiries from tiranyx.co.id (Flow D, E, F, G) → forwarded to opix queue
- Admin di tiranyx.co.id sees same inquiries from opix dashboard

**Decision needed:** Apakah opix lanjut sebagai separate app atau merge?
- Recommend: **STAY SEPARATE**
- Reason: Different audience (B2B clients), different UX (operations-heavy), different deployment
- Just sync auth + order data via shared DB or API

### 15.9. Updated Decision Points

**Decision 7 REVISED: User type granularity**
- [x] **A. 4 types** (Personal, Client, Internal, Admin) — CONFIRMED by user

**Decision 8 NEW: Manual invoice flow scope**
- [ ] **A. Flow D, E, F, G all manual** (RECOMMENDED — high-value B2B, custom scope)
- [ ] B. Only Flow E, F, G manual (D = automatic SaaS Snap)
- [ ] C. Only Flow F, G manual (D, E = automatic)

**Decision 9 NEW: opix integration**
- [ ] **A. Stay separate, sync auth + orders via API** (RECOMMENDED)
- [ ] B. Merge opix into tiranyx.co.id `/client/*` routes
- [ ] C. Defer opix integration to Phase 7+

---

**Total docs di tiranyx-web/docs/:**
- USER-CONCERNS-2026-05-05.md (8 concerns plan)
- COGNITIVE-FINDINGS-2026-05-05.md (root cause analysis)
- MODULAR-ARCHITECTURE-PLAN.md (architecture details)
- NEXT-WORK-SPEC.md (PayPal + Midtrans + order flows)

**Total docs di platform-docs (this repo):**
- VISION, PRD, ERD, BENCHMARK, ARCHITECTURE, ROADMAP
- SPRINT-LOG, JOURNAL, RESEARCH
- midtrans/PRODUCT-LIST, midtrans/TRANSACTION-FLOW
- **MASTER-PLAN-2026-05-05.md (this doc)** ← single source of truth for next session

Saya menunggu jawaban Anda. Tidak akan eksekusi apapun sampai user balas.
