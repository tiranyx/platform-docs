# TIRANYX PLATFORM — PRODUCT REQUIREMENTS DOCUMENT (PRD)
**Version:** 2.0.0 | **Date:** 2026-05-05 | **Status:** Living Document
**Replaces:** PRD versi lama di tiranyx-web/docs/PRD.md (fokus sempit, sudah outdated)

---

## BAGIAN 1 — KONTEKS & LATAR BELAKANG

### 1.1 Situasi Saat Ini

Website tiranyx.co.id saat ini berfungsi sebagai:
- Profil agency digital (portfolio, blog, case studies)
- Showcase produk SaaS (tanpa harga, tanpa pembelian)
- 6 freemium tools (tanpa quota enforcement, tanpa user auth)
- Admin CMS (blog, portfolio, resources, demos)

**Gap kritis:** Tidak ada sistem pembelian, tidak ada user account, tidak ada dashboard pasca-pembelian. Semua konversi masih via WhatsApp manual.

### 1.2 Trigger Perubahan

1. **Midtrans credit card activation** — membutuhkan: produk + harga di website, alur transaksi terdokumentasi, lisensi Kominfo untuk live streaming
2. **PayPal direct** — untuk klien internasional
3. **Scaling agency** — tidak bisa scale manual, perlu self-service
4. **Competitive pressure** — kompetitor mulai tawarkan tools freemium lokal

### 1.3 Scope PRD Ini

Dokumen ini mencakup **seluruh platform tiranyx.co.id** termasuk:
- Public marketing site (existing + extensions)
- User authentication & member dashboard
- Product catalog + e-commerce
- Client portal (OPIX integration)
- Internal admin (extension dari yang ada)
- Payment integration (Midtrans + PayPal)

---

## BAGIAN 2 — USER PERSONAS

### Persona 1: Dian — Digital Marketer Freelance
- **Umur:** 27 | **Kota:** Jakarta | **Income:** Rp 8–15jt/bln
- **Job:** Manage social media untuk 3–5 klien UMKM
- **Pain:** Nulis caption itu memakan waktu, hashtag susah nemu yang relevant
- **Goal:** Hemat 2 jam/hari dari konten routine
- **Journey:** Google "caption generator gratis Indonesia" → landing di /tools/caption → coba gratis 3x → upgrade Pro
- **Willingness to pay:** Rp 99rb/bln jika terbukti hemat waktu

### Persona 2: Budi — Pemilik Brand Pakaian
- **Umur:** 34 | **Kota:** Bandung | **Revenue:** Rp 2M/bln
- **Pain:** Tim marketing kecil, tidak punya budget agency besar
- **Goal:** Laporan performa yang bisa dibaca tanpa jadi data scientist
- **Journey:** Referral dari kenalan → /products/report-dashboard → demo → beli
- **Willingness to pay:** Rp 349rb/bln

### Persona 3: Citra — Agency Owner
- **Umur:** 38 | **Kota:** Surabaya | **Klien:** 12 brand
- **Pain:** Butuh tools yang bisa dipakai tim dan klien, mau branded namanya sendiri
- **Goal:** White-label social media tool untuk ditawarkan ke kliennya
- **Journey:** LinkedIn → sales call → /saas/sociostudio → demo → beli bundle
- **Willingness to pay:** Rp 5–10jt setup + Rp 2jt/bln

### Persona 4: Arif — Klien Agency Tiranyx (existing)
- **Umur:** 45 | **Kota:** Jakarta | **Bisnis:** Retail F&B
- **Pain:** Tidak tahu progress project, harus tanya via WA terus
- **Goal:** Bisa pantau deliverable dan approve tanpa harus meeting
- **Journey:** Invitation email dari tim Tiranyx → /client → dashboard project
- **Willingness to pay:** Sudah bayar retainer, butuh akses portal

### Persona 5: Rina — Tim Internal Tiranyx
- **Peran:** Project Manager / Content Strategist
- **Pain:** Tracking order manual di spreadsheet, delivery file lewat email berserakan
- **Goal:** Satu tempat untuk manage semua order + deliverable + klien
- **Journey:** /admin → orders → assign → upload deliverable → mark complete

---

## BAGIAN 3 — FITUR & REQUIREMENTS

### 3.1 MODUL AUTH (User Authentication)

**Priority:** P0 — Prerequisite untuk semua fitur lain

#### 3.1.1 Register
- Input: nama, email, password, phone (optional), tipe akun (personal default)
- Validasi: email format, password min 8 char + 1 angka
- Flow: Submit → email verifikasi → klik link → akun aktif → redirect /dashboard
- Social login: Google OAuth (nice to have, iter 2)
- **Catatan:** Pisah total dari admin auth (admin pakai cookie HMAC tersendiri)

#### 3.1.2 Login
- Email + password
- "Remember me" (session 30 hari)
- Forgot password → email reset link (expired 1 jam)
- Rate limiting: max 5 gagal → lockout 15 menit

#### 3.1.3 User Types
| Type | Akses | Dibuat oleh |
|------|-------|-------------|
| `personal` | /dashboard | Self-register |
| `client` | /client | Internal admin |
| `internal` | /admin (extended) | Super admin |

#### 3.1.4 Session Management
- JWT atau signed cookie (consistent dengan arsitektur existing)
- Prefix terpisah dari admin cookie (`trx_user` vs `trx_admin`)
- Refresh token untuk session panjang

---

### 3.2 MODUL PRODUCT CATALOG

**Priority:** P0 — Required untuk Midtrans

#### 3.2.1 Halaman /products (redesign)
- Grid semua produk dengan filter kategori: Tools | SaaS | On-Demand | White-label | AI
- Setiap card: nama, tagline, harga mulai dari Rp X/bln, badge status (Live/Beta/New)
- Sort: Popular | Harga Terendah | Terbaru
- **Harga WAJIB ditampilkan** (Midtrans requirement)

#### 3.2.2 Halaman /products/[slug]
Setiap produk punya halaman standalone:
- Hero section: nama, tagline, screenshot/demo video
- Features: 3–6 fitur utama dengan icon
- Pricing tiers: Free / Pro / Enterprise (card)
- How it works: 3 langkah simple
- FAQ: 5–10 pertanyaan (dari admin CMS)
- CTA: "Mulai Gratis" / "Beli Sekarang" / "Hubungi Sales"
- Related products (cross-sell)
- Schema.org: Product, FAQPage, BreadcrumbList

**Slug mapping:**
```
/products/brand-guidelines    → Brand Guideline Builder
/products/sociostudio-lite    → SocioStudio Lite
/products/sociostudio-full    → SocioStudio Full (SOCIYX)
/products/wa-agency           → WA Agency Suite
/products/bot-gateway         → Bot Gateway
/products/opix-crm            → OPIX Agency CRM
/products/voice-clone         → VoiceClone Studio
/products/market-intel        → Market Intelligence
/products/content-builder     → Content Builder AI
/products/seo-tools           → SEO Tools Suite
/products/report-dashboard    → Report Dashboard
```

#### 3.2.3 Admin: /admin/products
CRUD untuk semua produk:
- Nama, slug, kategori, deskripsi pendek/panjang
- Pricing tiers (JSON: [{name, price_idr, price_usd, features[], is_popular}])
- Features list (JSON)
- Delivery type: web_access | email | github | manual | api_key
- Status: live | beta | coming_soon | waitlist
- Is published + sort order
- Upload thumbnail/screenshot
- Manage FAQ per produk

---

### 3.3 MODUL FREEMIUM TOOLS

**Priority:** P0

#### 3.3.1 Tool Access Control
Saat ini tools bebas diakses tanpa login. Setelah iterasi 1:
- **Free tier:** 5 uses/hari tanpa login, ATAU unlimited jika login (free account)
- **Pro tier:** Unlimited, fitur tambahan, no rate limit
- Enforcement: middleware cek user session → quota di DB

#### 3.3.2 Upgrade Flow (dalam tool)
```
User pakai tool → Kuota habis → Banner "Kamu sudah pakai 5/5 hari ini"
→ Tombol "Upgrade Pro" → Modal pricing → Checkout Midtrans
→ Payment success → Kuota reset → Lanjut pakai
```

#### 3.3.3 Tools yang Ada + Roadmap
| Tool | Status | Free Quota | Pro Feature |
|------|--------|-----------|-------------|
| QR Generator | Live | 5/hari | Custom logo, warna brand, batch |
| UTM Builder | Live | Unlimited | Dashboard analytics, saved links |
| Color Palette | Live | 3 palette/hari | Export SVG/PDF, brand save |
| AI Writer | Live | 3 artikel/hari | Unlimited, SEO scoring, schedule |
| Caption Generator | Live | 3 caption/hari | Unlimited platform, tone setting |
| Hashtag Generator | Live | 1 set/hari | Trending data, competitor analysis |
| Brand Guidelines | bg-maker | N/A (link) | Full builder, export PDF |
| ROI Calculator | Planned | Free | Save + share |
| Social Audit | Planned | 1 audit/bln | Full + auto-update |
| Ads Analyzer | Planned | — | Pro only |
| Competitor Research | Planned | — | Pro only |

#### 3.3.4 Tool Bundle Pricing
- **Free:** limited quota, basic features
- **Pro Tools** Rp 99.000/bln — semua tools unlimited
- **Pro Tools Yearly** Rp 799.000/thn (hemat 33%)
- **Per-tool** Rp 39.000/bln (untuk yang butuh 1 saja)

---

### 3.4 MODUL ON-DEMAND SERVICES

**Priority:** P1

#### 3.4.1 Service Order Flow
```
/services/[id] → "Order Service" → Form brief
→ Login/Register → Review order + harga
→ Midtrans checkout → Order confirmed
→ Email konfirmasi → /dashboard/services (status: submitted)
→ Tim internal pick up → status: in_progress
→ Upload deliverable → status: done
→ Customer download → Email notifikasi
```

#### 3.4.2 Brief Form per Service
Setiap on-demand service punya form brief yang berbeda:
- Market Research: industri, kompetitor, target audience, scope
- Content Batch: platform, jumlah konten, tone, referensi
- Creative Design: jenis design, brand guideline (upload), referensi
- Social Audit: handle sosmed, periode analisis
- Brand Refresh: logo sekarang (upload), arah baru, referensi
- Ads Review: platform, periode, akun ads (read access)

#### 3.4.3 Service Pricing (wajib tampil)
| Service | Timeline | Harga |
|---------|----------|-------|
| Market Research Report | 5–7 hari kerja | Rp 3.000.000 – 15.000.000 |
| Content Batch Writing | 3–5 hari kerja | Rp 1.500.000 – 5.000.000 |
| Creative Design Pack | 3–5 hari kerja | Rp 1.000.000 – 3.000.000 |
| Social Media Audit | 2–3 hari kerja | Rp 2.000.000 – 5.000.000 |
| Brand Identity Refresh | 7–14 hari kerja | Rp 5.000.000 – 15.000.000 |
| Ads Performance Review | 2–3 hari kerja | Rp 1.500.000 – 3.000.000 |

---

### 3.5 MODUL DASHBOARD — PERSONAL (/dashboard)

**Priority:** P1

#### 3.5.1 /dashboard/overview
- Greeting: "Selamat datang, [nama]"
- Active plan badge (Free / Pro / Enterprise)
- Quick stats: tools used today, active orders, downloads ready
- Active subscriptions (card per produk)
- Recent activity feed (last 10 actions)
- Upsell CTA jika masih free

#### 3.5.2 /dashboard/tools
- Grid semua tools yang bisa diakses
- Per card: nama tool, usage hari ini vs quota, tombol "Buka Tool"
- Pro tools: lock icon jika belum subscribe
- Usage history chart (7 hari terakhir)

#### 3.5.3 /dashboard/orders
- Tabel semua order (on-demand services)
- Kolom: No order, Nama service, Status, Tanggal, Harga, Aksi
- Status chip: Submitted | Review | In Progress | Revision | Done | Cancelled
- Filter: status, tanggal
- Detail order: klik → modal atau halaman detail
- Download deliverable: muncul jika status = Done

#### 3.5.4 /dashboard/services (detail service orders)
- Progress tracker visual (stepper)
- Brief yang disubmit (read-only)
- Thread pesan dengan tim (simple chat)
- File attachments (upload by user, download from tim)
- Timeline: submitted → in progress → delivered

#### 3.5.5 /dashboard/subscriptions
- Active subscriptions: produk, plan, billing date, status
- Upgrade/downgrade option
- Cancel (soft cancel: tetap aktif sampai expired)
- Billing history: tabel invoice dengan download PDF

#### 3.5.6 /dashboard/downloads
- Digital products yang sudah dibeli/diterima
- Tipe: ZIP file, PDF report, GitHub invite link, License key, Access URL
- Sorted by date, filter by type
- Download counter (tracking)

#### 3.5.7 /dashboard/profile
- Update nama, phone, company, avatar
- Change email (perlu verifikasi ulang)
- Change password
- Notification preferences (email, WA)
- Delete account (soft delete)

---

### 3.6 MODUL CLIENT PORTAL (/client)

**Priority:** P2

#### 3.6.1 Akses & Auth
- Akun dibuat oleh internal (bukan self-register)
- Email invite dengan link setup password
- Login page terpisah: /client/login (branded berbeda dari /login personal)

#### 3.6.2 /client/overview
- Active projects dengan progress bar
- Upcoming milestone (3 terdekat)
- Unpaid invoice alert
- Quick links ke deliverable terbaru

#### 3.6.3 /client/projects
- List semua project dengan status
- Detail per project:
  - Scope & brief (read-only)
  - Timeline/Gantt sederhana
  - Milestones dengan status
  - PIC dari tim Tiranyx + kontak WA
  - % completion

#### 3.6.4 /client/deliverables
- File yang perlu di-approve
- Tombol: Approve ✓ | Minta Revisi ✗
- Catatan revisi (textarea)
- Version history (v1, v2, dst)
- Download final file

#### 3.6.5 /client/invoices
- Invoice pending (belum dibayar) → tombol "Bayar" → Midtrans
- Invoice lunas → download PDF
- History semua invoice

#### 3.6.6 Integrasi OPIX / Ixonomic
- Data project dari api.ixonomic.com (sudah live)
- SSO atau token-based auth ke Ixonomic
- Tiranyx /client = frontend layer di atas Ixonomic API

---

### 3.7 MODUL ADMIN (Extended)

**Priority:** P1

#### 3.7.1 /admin/users (NEW)
- List semua user (personal + client + internal)
- Filter by type, status
- Create client account (invite by email)
- View user detail: orders, subscriptions, activity
- Suspend / activate / delete

#### 3.7.2 /admin/orders (NEW)
- Incoming service orders
- Assign ke internal user
- Update status
- Upload deliverable files
- Internal notes
- Mark as complete → trigger delivery email

#### 3.7.3 /admin/products (NEW — lihat 3.2.3)

#### 3.7.4 /admin/subscriptions (NEW)
- Active subscriptions per produk
- Expiry dalam 7 hari (alert)
- Manual extend (untuk kasus khusus)
- Revenue summary per periode

#### 3.7.5 /admin/waitlist (NEW)
- Entries per produk waitlist
- Export CSV
- Send email blast ke waitlist
- Statistik: total, per produk, per minggu

#### 3.7.6 /admin/analytics (NEW)
- Revenue: hari ini, bulan ini, MRR, ARR
- Active users: DAU, MAU
- Tool usage: per tool, per hari
- Conversion funnel: visitor → register → first purchase
- Top products by revenue

---

### 3.8 MODUL WAITLIST

**Priority:** P1 (untuk Midtrans requirement juga)

#### 3.8.1 /waitlist (index)
- Landing semua produk dalam riset
- Hero: "Jadilah yang pertama mengakses teknologi terdepan Tiranyx"
- Grid produk: Migancore, Mighan World, SIDIXLab, Ixonomic Coin

#### 3.8.2 /waitlist/[product] — per produk
- Hero + pitch singkat produk
- Preview: screenshot, video, atau animated mockup
- Early bird benefits (diskon, beta access, badge)
- Counter: "X dari 1000 spot tersedia"
- Form: nama, email, peran (Pengguna / Developer / Partner / Investor)
- Link ke GitHub repo publik jika ada
- FAQ produk (5–8 pertanyaan)
- Status updates (changelog mini)

**Produk:**
| Slug | Produk | GitHub |
|------|--------|--------|
| /waitlist/migancore | Migancore ADO AI | tiranyx/migancore |
| /waitlist/mighan-world | Mighan World NPC AI | fahmiwol/mighan-web |
| /waitlist/sidixlab | SIDIXLab Creative AI | fahmiwol/sidix |
| /waitlist/ixonomic-coin | Ixonomic Coin | fahmiwol/mighancoin_token |

---

### 3.9 MODUL SaaS WHITE-LABEL (/saas)

**Priority:** P2

#### 3.9.1 /saas (catalog)
- Penjelasan white-label: "Dapatkan SaaS Tiranyx dengan brand kamu sendiri"
- Grid produk white-label yang tersedia
- Comparison: Build it yourself vs White-label Tiranyx

#### 3.9.2 /saas/[bundle]
- Apa yang termasuk dalam bundle
- Requirement teknis (server, domain)
- Setup process (3–5 langkah)
- Pricing: setup fee + monthly license
- Form order → payment → delivery via email + GitHub

**Bundles:**
```
/saas/sociostudio    → SOCIYX Social Media Platform
/saas/wa-suite       → WA Agency Gateway
/saas/bot-platform   → Bot Gateway Multi-platform
/saas/opix-crm       → OPIX Agency CRM
/saas/voice-clone    → VoiceClone Studio
```

---

### 3.10 MODUL PAYMENT

**Priority:** P0

#### 3.10.1 Midtrans Integration
- **Snap.js** untuk payment popup (credit card, GoPay, QRIS, VA, Alfamart)
- **Server-side:** Midtrans Node.js SDK
- **API Endpoints:**
  - `POST /api/orders/create` — buat order + Midtrans transaction
  - `POST /api/payment/midtrans/notification` — webhook callback
  - `GET /api/orders/[id]/status` — cek status
- **Webhook processing:**
  1. Verifikasi signature Midtrans
  2. Update order status di DB
  3. Activate subscription/purchase
  4. Trigger delivery
  5. Send email konfirmasi

#### 3.10.2 PayPal Integration
- PayPal Orders API v2
- `POST /api/payment/paypal/create-order`
- `POST /api/payment/paypal/capture-order`
- Currency: USD (convert from IDR at current rate)
- Untuk: international users, SaaS bundles

#### 3.10.3 Delivery Automation
| Delivery Type | Trigger | Aksi |
|---------------|---------|------|
| Web access | Payment confirmed | Activate user_purchase record |
| Email file | Payment confirmed | Kirim email dengan download link |
| GitHub access | Payment confirmed | GitHub API: invite ke repo sebagai collaborator |
| License key | Payment confirmed | Generate + email license key |
| Manual | Payment confirmed | Notifikasi ke admin, assign manual |

---

## BAGIAN 4 — NON-FUNCTIONAL REQUIREMENTS

### 4.1 Performance
- Homepage LCP < 2.5 detik
- Tool response < 1 detik (AI tool: < 5 detik)
- Dashboard load < 2 detik
- API response < 500ms (p95)

### 4.2 SEO
- Setiap halaman produk: unique title, description, OG image
- Schema.org: Product, Service, FAQPage, BreadcrumbList, Organization
- Sitemap auto-generated (sudah ada, extend ke produk baru)
- Canonical URL untuk semua halaman
- Core Web Vitals: semua green di production

### 4.3 Security
- HTTPS enforced (sudah via Nginx)
- Rate limiting: auth endpoints (5 req/min), AI tools (10 req/min)
- Input sanitization: semua form input
- CSRF protection: semua POST endpoints
- Midtrans webhook signature verification
- No sensitive data in client-side code

### 4.4 Accessibility
- WCAG 2.1 AA minimum
- Keyboard navigable
- Screen reader friendly labels
- Color contrast ratio ≥ 4.5:1

### 4.5 Mobile
- Responsive di semua breakpoint (320px – 1920px)
- Touch targets ≥ 44px
- Dashboard usable di mobile (priority untuk personal dashboard)

---

## BAGIAN 5 — DESIGN PRINCIPLES

1. **Jangan ubah design system** — space/sci-fi dark theme tetap
2. **Homepage style adalah reference** — halaman lain boleh menyesuaikan UX tapi tetap dalam design system yang sama
3. **Dashboard** bisa lebih "software-like" (lebih terang, clean) vs landing pages yang lebih dramatic
4. **Konsistensi komponen** — reuse komponen yang ada, extend jika perlu

---

## BAGIAN 6 — OUT OF SCOPE

- ABRA / Nutrisius — bukan bagian Tiranyx
- Mighan World 3D engine — separate repo, hanya waitlist di tiranyx.co.id
- Ixonomic core banking — separate system, hanya integrasi API
- Mobile app (iOS/Android) — fase 3+
- Marketplace pembeli lain (bukan white-label Tiranyx)

---

*PRD ini adalah living document. Update setiap sprint akhir.*
*Maintained by: Fahmi Ghani + AI Engineering Team*
