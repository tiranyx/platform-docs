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
- [ ] Semua produk di /products punya harga yang tampil
- [ ] 11 halaman /products/[slug] live dengan FAQ
- [ ] /tools menampilkan "Free / Pro Rp 99rb/bln"
- [ ] /services on-demand menampilkan range harga
- [ ] PRODUCT-LIST.md + TRANSACTION-FLOW.md lengkap
- [ ] Dokumen alur transaksi siap PDF

**Sprint Backlog:**
```
[ ] Redesign /products page — tambah pricing cards
[ ] Buat /products/[slug] template + 11 implementasi
[ ] Buat product_faq admin interface
[ ] Tambah harga ke /tools listing
[ ] Tambah harga ke /services on-demand section
[ ] Tambah harga ke /demos (atau "Request Demo")
[ ] Tulis midtrans/PRODUCT-LIST.md
[ ] Tulis midtrans/TRANSACTION-FLOW.md
[ ] Generate TRANSACTION-FLOW.pdf
[ ] Research: kebutuhan Kominfo PSE untuk live streaming service
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

#### 2026-05-06 (Selasa) — Template
```
SELESAI:
- [ ] 

IN PROGRESS:
- [ ] 

TEMUAN:
- 

BLOCKER:
- 

NEXT:
- 
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
