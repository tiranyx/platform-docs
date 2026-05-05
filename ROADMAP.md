# TIRANYX PLATFORM — ROADMAP
**Version:** 1.0.0 | **Date:** 2026-05-05
**Format:** Fase → Sprint → Task
**Update:** Setiap sprint akhir (Jumat)

---

## LEGEND

```
🔴 BLOCKER / CRITICAL PATH
🟠 HIGH PRIORITY
🟡 MEDIUM PRIORITY
🟢 NICE TO HAVE / FASE BERIKUT
✅ SELESAI
🔄 IN PROGRESS
⏳ WAITING / BLOCKED
📋 BACKLOG
```

---

## FASE 0 — MIDTRANS COMPLIANCE (Target: 2 Minggu)
**Tujuan:** Lolos syarat pendaftaran Midtrans credit card & PayPal direct

### Sprint 0.1 — Product Catalog dengan Harga
```
🔴 [P0] Redesign /products — tambah harga ke semua produk
🔴 [P0] Buat /products/[slug] — 11 halaman detail produk
🔴 [P0] Tambah FAQ per produk di admin + public render
🔴 [P0] Tampilkan harga di /tools (Free / Pro Rp 99rb/bln)
🔴 [P0] Tampilkan harga di /demos (atau "Request Demo")
🔴 [P0] Tambah harga ke /services on-demand
```

### Sprint 0.2 — Midtrans Documentation
```
🔴 [P0] Buat midtrans/PRODUCT-LIST.md (daftar produk + harga)
🔴 [P0] Buat midtrans/TRANSACTION-FLOW.md (alur transaksi PDF)
🔴 [P0] Buat midtrans/TRANSACTION-FLOW.pdf (untuk submission)
🟠 [P1] Identifikasi kebutuhan Kominfo PSE untuk live streaming
🟠 [P1] Daftarkan PayPal Business account jika belum ada
```

**Done criteria:** Website menampilkan harga jelas, dokumen siap submit ke Midtrans

---

## FASE 1 — PLATFORM FOUNDATION (Target: 4 Minggu)
**Tujuan:** User auth, dashboard dasar, payment integration pertama

### Sprint 1.1 — User Authentication
```
🔴 [P0] DB Migration 001: users + user_sessions
🔴 [P0] POST /api/auth/register
🔴 [P0] POST /api/auth/login + logout
🔴 [P0] Email verifikasi (link + expire 24j)
🔴 [P0] Middleware: protect /dashboard/* routes
🔴 [P0] Pages: /login, /register, /verify-email
🔴 [P0] Forgot password + reset flow
🟠 [P1] Rate limiting: auth endpoints
```

### Sprint 1.2 — Product DB + CMS
```
🔴 [P0] DB Migration 002: products + product_faq
🔴 [P0] Admin CRUD: /admin/products
🔴 [P0] Seed: semua produk existing ke DB
🔴 [P0] Public /products render dari DB (bukan static)
🟠 [P1] Admin CRUD: /admin/products/[id]/faq
🟠 [P1] Image upload untuk product thumbnail
```

### Sprint 1.3 — Payment Integration (Sandbox)
```
🔴 [P0] DB Migration 003: user_purchases
🔴 [P0] Install midtrans-client + konfigurasi
🔴 [P0] POST /api/orders/create (tools Pro subscription)
🔴 [P0] Snap.js frontend: payment popup
🔴 [P0] POST /api/payment/midtrans/notification (webhook)
🔴 [P0] Activate purchase setelah payment confirmed
🟠 [P1] PayPal Orders API v2 (create + capture)
🟠 [P1] Email konfirmasi setelah bayar
```

### Sprint 1.4 — Dashboard Personal (MVP)
```
🟠 [P1] Layout: DashboardLayout (sidebar, topbar)
🟠 [P1] /dashboard/overview — stats + quick actions
🟠 [P1] /dashboard/tools — list tools + usage
🟠 [P1] /dashboard/orders — riwayat order
🟠 [P1] /dashboard/subscriptions — active plans
🟠 [P1] /dashboard/profile — update data diri
🟡 [P2] /dashboard/downloads — digital products
```

**Done criteria:** User bisa register → beli tool Pro → akses dashboard

---

## FASE 2 — FREEMIUM + TOOLS ENFORCEMENT (Target: 3 Minggu)
**Tujuan:** Tools freemium benar-benar enforce quota, conversion funnel aktif

### Sprint 2.1 — Tool Quota System
```
🟠 [P1] DB Migration: tool_usage table
🟠 [P1] API: POST /api/tools/[slug]/use — increment + check quota
🟠 [P1] Free: 5 uses/hari (reset midnight WIB)
🟠 [P1] Pro: unlimited (cek user_purchases aktif)
🟠 [P1] Quota bar di setiap tool: "3 dari 5 dipakai hari ini"
🟠 [P1] Upsell modal saat quota habis
```

### Sprint 2.2 — On-Demand Service Orders
```
🟠 [P1] DB Migration 004: service_orders + order_messages
🟠 [P1] Order form per service (max 5 field wajib)
🟠 [P1] POST /api/orders/service/create
🟠 [P1] /dashboard/services — track progress
🟠 [P1] Admin: /admin/orders — incoming orders + assign
🟠 [P1] Admin: upload deliverable + update status
🟡 [P2] Email notif per status change
🟡 [P2] In-order messaging thread
```

### Sprint 2.3 — Waitlist Pages
```
🟠 [P1] DB Migration 006: waitlist_entries
🟠 [P1] /waitlist — index semua produk waitlist
🟠 [P1] /waitlist/migancore
🟠 [P1] /waitlist/mighan-world
🟠 [P1] /waitlist/sidixlab
🟠 [P1] /waitlist/ixonomic-coin
🟠 [P1] POST /api/waitlist/join
🟠 [P1] Admin: /admin/waitlist — list + export
```

**Done criteria:** Tool quota enforced, service order bisa masuk, waitlist aktif

---

## FASE 3 — CLIENT PORTAL (Target: 3 Minggu)
**Tujuan:** Client agency bisa pantau project tanpa WA terus

### Sprint 3.1 — Client Auth + Setup
```
🟡 [P2] Admin: buat akun client (invite by email)
🟡 [P2] Email invite dengan link setup password
🟡 [P2] Client login page (/client/login)
🟡 [P2] Middleware: /client/* → user_type = 'client'
```

### Sprint 3.2 — Client Dashboard
```
🟡 [P2] DB Migration 005: client_projects + client_milestones
🟡 [P2] /client/overview — active projects summary
🟡 [P2] /client/projects — detail per project
🟡 [P2] /client/deliverables — approve / request revisi
🟡 [P2] /client/invoices — lihat + bayar
🟡 [P2] Ixonomic API integration (read project data)
```

### Sprint 3.3 — Client Invoice Payment
```
🟡 [P2] DB Migration: client_invoices
🟡 [P2] Admin: buat + kirim invoice ke client
🟡 [P2] Client: bayar invoice via Midtrans
🟡 [P2] Generate PDF invoice
🟡 [P2] Email invoice + reminder
```

**Done criteria:** Klien agency bisa login, lihat project, approve, bayar invoice

---

## FASE 4 — SaaS WHITE-LABEL (Target: 2 Minggu)
**Tujuan:** Bundle produk digital bisa dibeli dan dikirim otomatis

### Sprint 4.1 — White-label Catalog
```
🟡 [P2] /saas — landing page bundle
🟡 [P2] /saas/[bundle] — 5 bundle pages
🟡 [P2] Order form + Midtrans checkout
🟡 [P2] Delivery automation: GitHub invite + email
🟡 [P2] Admin: /admin/saas-orders
```

### Sprint 4.2 — GitHub Delivery Automation
```
🟡 [P2] GitHub API integration (PAT)
🟡 [P2] Auto-invite setelah payment webhook
🟡 [P2] Revoke access jika subscription cancelled
🟡 [P2] Email: welcome + repo link + setup guide
```

**Done criteria:** Buyer beli bundle → dapat GitHub access + email dalam 5 menit

---

## FASE 5 — SCALE & OPTIMIZATION (Ongoing dari Q3 2026)

### Subscription Management
```
🟢 [P3] Auto-renewal via Midtrans recurring
🟢 [P3] Upgrade/downgrade plan dari dashboard
🟢 [P3] Dunning: email reminder 7 hari sebelum expired
🟢 [P3] Grace period 3 hari setelah expired
```

### Analytics & Admin
```
🟢 [P3] /admin/analytics — MRR, MAU, conversion
🟢 [P3] /admin/users — full user management
🟢 [P3] Audit log untuk semua aksi penting
```

### API Aggregator (Fase 2 PRD)
```
🟢 [P3] api.tiranyx.co.id publik (subdomain)
🟢 [P3] Shopee API (sudah ada di apix.tiranyx.co.id)
🟢 [P3] TikTok API
🟢 [P3] Instagram Basic Display API
🟢 [P3] Rate limiting + API key management
🟢 [P3] Developer docs (docs.tiranyx.co.id)
```

### International Expansion
```
🟢 [P4] English UI toggle
🟢 [P4] USD pricing (PayPal primary)
🟢 [P4] Subdomain: tools.tiranyx.co.id
```

---

## MILESTONE TRACKER

| Milestone | Target | Status | Notes |
|-----------|--------|--------|-------|
| M1: Harga tampil di website | Minggu 1 | 🔄 | Blocker Midtrans |
| M2: Midtrans sandbox live | Minggu 3 | 📋 | Setelah M1 |
| M3: User bisa register + beli | Minggu 4 | 📋 | Setelah M2 |
| M4: First paid user | Minggu 5 | 📋 | North star |
| M5: 10 paid users | Bulan 2 | 📋 | Product-market fit signal |
| M6: Service order flow live | Bulan 2 | 📋 | |
| M7: Client portal live | Bulan 2.5 | 📋 | |
| M8: Waitlist 1000 signup | Bulan 3 | 📋 | Migancore/Mighan |
| M9: Midtrans production aktif | Bulan 2 | 📋 | Setelah sandbox OK |
| M10: MRR Rp 10jt | Bulan 4 | 📋 | Real revenue |

---

## DEPENDENCY MAP

```
Fase 0 (Harga di website)
    └── Fase 1.1 (User Auth)
            └── Fase 1.3 (Payment Sandbox)
                    └── Fase 1.4 (Dashboard)
                            └── Fase 2.1 (Tool Quota)
                            └── Fase 2.2 (Service Orders)
                    
Fase 0 (Harga di website)
    └── Midtrans Production Approval
    
Fase 1.1 (User Auth)
    └── Fase 3.1 (Client Auth — sama sistem, beda user_type)
    
Ixonomic API (sudah live)
    └── Fase 3.2 (Client Dashboard data)
```

---

## TECH DEBT YANG DISADARI (untuk sprint nanti)

| Item | Impact | Priority |
|------|--------|---------|
| `middleware.ts` deprecated di Next 16.2 | Low | Q3 2026 |
| Font loading via @import (bukan next/font) | Performance | Sprint 2+ |
| Services data masih di `lib/services-data.ts` | Maintainability | Fase 2 |
| Tidak ada automated test (unit/integration) | Quality | Fase 3+ |
| SQLite → PostgreSQL migration planning | Scale | Fase 5 |

---

*Roadmap ini living document — update setiap Jumat sprint review.*
*Semua estimasi waktu asumsi: ~20 jam coding/minggu efektif*
