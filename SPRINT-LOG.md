# TIRANYX — SPRINT LOG
**Format:** Harian / Per Sprint | **Update:** Setiap selesai task
**Protocol:** Baca PROTOCOL.md sebelum mulai sprint baru

---

## CARA BACA DOKUMEN INI

```
Setiap sprint punya:
- Objective: satu kalimat tujuan sprint
- Success criteria: measurable outcomes
- Daily log: apa yang dikerjakan per hari
- Temuan: learning dari sprint ini
- Retrospektif: apa yang bisa lebih baik
```

---

## SPRINT AKTIF

### SPRINT 0 — Midtrans Compliance & Product Catalog
**Periode:** 2026-05-05 → 2026-05-16 (2 minggu)
**Objective:** Website menampilkan harga semua produk + dokumen Midtrans siap submit

**Success Criteria:**
- [x] Semua produk di /products punya harga yang tampil
- [x] /products/[slug] live dengan FAQ + pricing tiers
- [x] /tools menampilkan "Gratis Nx/hari → Pro Rp 99rb/bln"
- [x] /services on-demand menampilkan range harga
- [x] PRODUCT-LIST.md + TRANSACTION-FLOW.md lengkap
- [ ] Dokumen alur transaksi siap PDF

**Sprint Backlog:**
```
[x] Redesign /products page — tambah pricing cards (DONE 2026-05-05)
[x] Buat /products/[slug] template — 6 slug live (DONE 2026-05-05)
[x] Buat product_faq admin interface di /admin/products (DONE 2026-05-05)
[x] Tambah harga ke /tools listing (DONE 2026-05-05)
[x] Tambah harga ke /services on-demand section (DONE 2026-05-05)
[x] Tulis midtrans/PRODUCT-LIST.md (DONE 2026-05-05)
[x] Tulis midtrans/TRANSACTION-FLOW.md (DONE 2026-05-05)
[x] DB migration: products, pricing_tiers, faq, how_it_works (DONE 2026-05-05)
[x] Deploy ke VPS + build fix (DONE 2026-05-05)
[ ] Tambah harga ke /demos (atau "Request Demo")
[ ] Generate TRANSACTION-FLOW.pdf
[ ] Research: kebutuhan Kominfo PSE untuk live streaming service
[ ] White-label section di /products sudah ada, tapi perlu /products/[slug] untuk white-label produk
```

**Daily Log:**

#### 2026-05-05 (Senin)
```
SELESAI:
- [x] Riset menyeluruh ecosystem Tiranyx (VPS, GitHub, website)
- [x] Buat dokumentasi lengkap: VISION, PRD, ERD, PROTOCOL, BENCHMARK, ARCHITECTURE, ROADMAP, SPRINT-LOG
- [x] Buat memory files untuk context retention

IN PROGRESS:
- [ ] JOURNAL.md, RESEARCH.md, midtrans docs

TEMUAN HARI INI:
- VPS = 72.62.125.6 (sama dengan SIDIX VPS), SSH via sidix-vps alias
- Ixonomic ecosystem sudah live (7 PM2 proses aktif)
- opix_tiranyx repo kosong; OPIX = Ixonomic yang sudah live
- tiranyx.co.id punya 6 tools live, semua tanpa harga
- /admin/saas sudah ada routenya tapi belum ada konten

BLOCKER:
- Tidak ada (dokumentasi fase, belum mulai coding)

NEXT:
- Buat JOURNAL.md + RESEARCH.md + midtrans docs
- Push semua docs ke tiranyx/platform-docs repo
- Mulai sprint coding: tambah harga ke /products
```

#### 2026-05-05 — Sesi 2 (Sprint 0 Coding)
```
SELESAI:
- [x] S0-01: /products page redesign — 3 section (SaaS, On-demand, White-label), IDR pricing cards
- [x] S0-03: /products/[slug] page template — hero, how it works, pricing tiers, FAQ
- [x] lib/products-data.ts — 6 produk dengan full detail, pricing, FAQ, how-it-works
- [x] lib/tools-data.ts — tambah priceMonthly, priceYearly, freeQuota fields
- [x] /tools page — tambah pricing row "Gratis Nx/hari → Pro Rp 99rb/bln"
- [x] DB migration: products, product_pricing_tiers, product_faq, product_how_it_works + seeding
- [x] Admin CRUD: /admin/products list + toggle published API
- [x] Deploy ke VPS — fix next.config.ts (remove turbopack path.join), rebuild better-sqlite3
- [x] Build + pm2 restart sukses, semua route 200

TEMUAN:
- next.config.ts: path.join(__dirname) dalam turbopack config menyebabkan compile error
- VPS Node.js v20 tapi better-sqlite3 dikompil untuk v24 → perlu npm rebuild
- @anthropic-ai/sdk dan @google/genai tidak ada di package.json tapi dipakai di code → install manual
- /products/[slug] untuk white-label (sociyx, wa-suite, opix-crm, bot-gateway) belum ada — masih return 404

BLOCKER:
- Tidak ada (semua terselesaikan)

NEXT:
- Sprint 0 remaining: /demos pricing, PDF TRANSACTION-FLOW, Kominfo PSE research
- Sprint 1: Auth system (register/login), Midtrans sandbox integration
```

---

## SPRINT BACKLOG (Prioritized)

### Fase 0 Backlog
| ID | Task | Est | Priority | Status |
|----|------|-----|---------|--------|
| S0-01 | Redesign /products — pricing cards | 4j | P0 | 📋 |
| S0-02 | Template /products/[slug] | 6j | P0 | 📋 |
| S0-03 | Implementasi 11 product pages | 8j | P0 | 📋 |
| S0-04 | Product FAQ admin CRUD | 3j | P0 | 📋 |
| S0-05 | Harga di /tools | 2j | P0 | 📋 |
| S0-06 | Harga di /services | 2j | P0 | 📋 |
| S0-07 | Harga di /demos | 1j | P0 | 📋 |
| S0-08 | midtrans/PRODUCT-LIST.md | 2j | P0 | 📋 |
| S0-09 | midtrans/TRANSACTION-FLOW.md | 3j | P0 | 📋 |
| S0-10 | Research Kominfo PSE | 2j | P1 | 📋 |

### Fase 1 Backlog
| ID | Task | Est | Priority | Status |
|----|------|-----|---------|--------|
| S1-01 | DB Migration 001: users + sessions | 2j | P0 | 📋 |
| S1-02 | POST /api/auth/register | 3j | P0 | 📋 |
| S1-03 | POST /api/auth/login + logout | 2j | P0 | 📋 |
| S1-04 | Email verification flow | 4j | P0 | 📋 |
| S1-05 | Middleware: protect /dashboard/* | 2j | P0 | 📋 |
| S1-06 | Pages: /login, /register | 4j | P0 | 📋 |
| S1-07 | Forgot password + reset | 3j | P1 | 📋 |
| S1-08 | DB Migration 002: products | 2j | P0 | 📋 |
| S1-09 | Admin: /admin/products CRUD | 8j | P0 | 📋 |
| S1-10 | Seed products ke DB | 2j | P0 | 📋 |
| S1-11 | Midtrans SDK setup + config | 2j | P0 | 📋 |
| S1-12 | POST /api/orders/create | 4j | P0 | 📋 |
| S1-13 | Snap.js frontend integration | 3j | P0 | 📋 |
| S1-14 | Webhook: /api/payment/midtrans | 4j | P0 | 📋 |
| S1-15 | Activate purchase post-payment | 2j | P0 | 📋 |
| S1-16 | Dashboard layout + overview | 6j | P1 | 📋 |
| S1-17 | /dashboard/tools | 4j | P1 | 📋 |
| S1-18 | /dashboard/orders | 3j | P1 | 📋 |
| S1-19 | /dashboard/subscriptions | 4j | P1 | 📋 |
| S1-20 | /dashboard/profile | 3j | P1 | 📋 |

### Fase 2 Backlog
| ID | Task | Est | Priority | Status |
|----|------|-----|---------|--------|
| S2-01 | DB Migration: tool_usage | 1j | P1 | 📋 |
| S2-02 | POST /api/tools/[slug]/use | 3j | P1 | 📋 |
| S2-03 | Quota bar UI per tool | 3j | P1 | 📋 |
| S2-04 | Upsell modal saat quota habis | 2j | P1 | 📋 |
| S2-05 | DB Migration: service_orders | 2j | P1 | 📋 |
| S2-06 | Service order form per service | 8j | P1 | 📋 |
| S2-07 | /dashboard/services tracking | 4j | P1 | 📋 |
| S2-08 | Admin: orders management | 6j | P1 | 📋 |
| S2-09 | DB Migration: waitlist | 1j | P1 | 📋 |
| S2-10 | /waitlist + 4 product pages | 8j | P1 | 📋 |
| S2-11 | Admin: waitlist management | 3j | P1 | 📋 |

---

## SPRINT HISTORY

### SPRINT -1 (Pre-documentation, sebelum 2026-05-05)
```
Konteks: tiranyx.co.id sudah live dengan Next.js 16.2.1 + SQLite
Done:
  ✅ Homepage + Navbar + Footer
  ✅ Blog CMS (SQLite + TinyMCE)
  ✅ Portfolio + Case Studies (SQLite)
  ✅ Resources
  ✅ App Demos (SQLite)
  ✅ Freemium Tools (6 tools, tanpa quota)
  ✅ Services pages (dari lib/services-data.ts)
  ✅ Admin CMS (semua CRUD)
  ✅ SSL, DNS, PM2, Nginx di VPS
  
Known issues:
  - Tools tanpa quota enforcement
  - Products tanpa harga
  - Tidak ada user auth
  - Tidak ada payment integration
  - /admin/saas kosong
```

---

## SPRINT RETROSPEKTIF TEMPLATE

```markdown
## Retrospektif Sprint [N] — [Tanggal]

**Yang berjalan baik:**
- 

**Yang tidak berjalan baik:**
- 

**Apa yang akan diubah:**
- 

**Velocity aktual vs estimasi:**
- Estimasi: X jam
- Aktual: Y jam
- Ratio: Y/X (>1 = over-estimate, <1 = under-estimate)

**Action items untuk sprint berikut:**
- 
```

---

## DEFINITION OF DONE (DoD)

Sebuah task dianggap **DONE** jika:

1. ✅ Fungsi berjalan sesuai acceptance criteria
2. ✅ Tidak ada TypeScript error
3. ✅ Tidak ada console.error yang unhandled
4. ✅ Responsive di mobile (375px) dan desktop (1440px)
5. ✅ Error states handled (loading, empty, error)
6. ✅ Build berhasil (`npm run build`)
7. ✅ Untuk halaman baru: title, meta description, OG ada
8. ✅ Untuk API baru: error handling + response format consistent
9. ✅ Env variables baru: terdokumentasi di .env.example
10. ✅ Untuk DB changes: migration file dibuat + tested

---

## VELOCITY TRACKER

| Sprint | Planned (j) | Actual (j) | Completed | Ratio |
|--------|------------|-----------|-----------|-------|
| Sprint 0 | - | - | - | - |
| Sprint 1 | - | - | - | - |

*Isi setiap sprint selesai untuk track velocity tim*
