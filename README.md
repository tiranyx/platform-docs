# TIRANYX PLATFORM DOCS
**Repository:** tiranyx/platform-docs
**Owner:** Fahmi Ghani (fahmiwol) | Tiranyx Digital Indonesia
**Last updated:** 2026-05-05

---

> Platform documentation untuk pengembangan tiranyx.co.id sebagai platform AI marketing & commerce intelligence Indonesia.

---

## DOKUMEN INDEX

### Wajib Dibaca Pertama

| Dokumen | Deskripsi | Update |
|---------|---------|--------|
| [PROTOCOL.md](PROTOCOL.md) | **MANDATORY** — Cara kerja agent, anti context-loss, execution protocol | 2026-05-05 |
| [VISION.md](VISION.md) | Visi, misi, positioning, north star metric | 2026-05-05 |

### Product & Engineering

| Dokumen | Deskripsi | Update |
|---------|---------|--------|
| [PRD.md](PRD.md) | Product Requirements Document — semua fitur, modul, user stories | 2026-05-05 |
| [ERD.md](ERD.md) | Entity Relationship Diagram + full SQL schema (7 migrations) | 2026-05-05 |
| [ARCHITECTURE.md](ARCHITECTURE.md) | Tech architecture, system design, API specs, deployment | 2026-05-05 |
| [BENCHMARK.md](BENCHMARK.md) | Competitive analysis 10+ platform, best practices adoption | 2026-05-05 |

### Planning & Execution

| Dokumen | Deskripsi | Update |
|---------|---------|--------|
| [ROADMAP.md](ROADMAP.md) | Phased roadmap Fase 0–5 dengan milestones dan dependencies | 2026-05-05 |
| [SPRINT-LOG.md](SPRINT-LOG.md) | Sprint aktif, backlog prioritized, daily log, retrospektif | 2026-05-05 |
| [JOURNAL.md](JOURNAL.md) | Dev journal: findings, decisions, handoffs per sesi | 2026-05-05 |
| [RESEARCH.md](RESEARCH.md) | Ecosystem mapping, product catalog, external insights | 2026-05-05 |

### Midtrans Compliance

| Dokumen | Deskripsi | Update |
|---------|---------|--------|
| [midtrans/PRODUCT-LIST.md](midtrans/PRODUCT-LIST.md) | Daftar produk + harga untuk submission Midtrans | 2026-05-05 |
| [midtrans/TRANSACTION-FLOW.md](midtrans/TRANSACTION-FLOW.md) | Alur transaksi detail (4 skenario) untuk submission Midtrans | 2026-05-05 |

---

## QUICK CONTEXT

### Ekosistem Tiranyx
```
TIRANYX HOLDING
├── Tiranyx Agency    tiranyx.co.id (REPO INI)
├── Mighan World      mighan.com
├── SIDIX AI          sidixlab.com
├── Ixonomic          ixonomic.com
└── Galantara         galantara.io
```

### Infrastructure
```
VPS: Hostinger 72.62.125.6
SSH: ssh sidix-vps (alias configured)
Panel: aaPanel + PM2 + Nginx
Main app: PM2 id:5 "tiranyx" port 3001
```

### Tech Stack (tiranyx.co.id)
```
Next.js 16.2.1 + React 19 + TypeScript + Tailwind 4
SQLite (better-sqlite3 v11.10.0)
Node.js v24.14.1
```

---

## SPRINT AKTIF

**Sprint 0 — Midtrans Compliance** (2026-05-05 → 2026-05-16)

Objective: Website menampilkan harga semua produk + dokumen Midtrans siap submit

Top 3 tasks:
1. Redesign /products — tambah pricing cards
2. Buat /products/[slug] — 11 halaman detail
3. midtrans/PRODUCT-LIST.md + TRANSACTION-FLOW.md ← DONE ✅

→ Detail: [SPRINT-LOG.md](SPRINT-LOG.md)

---

## CARA BERKONTRIBUSI (untuk agent AI)

1. **Baca PROTOCOL.md terlebih dahulu** — wajib, tidak boleh skip
2. Baca VISION.md untuk memahami arah
3. Cek SPRINT-LOG.md untuk task yang sedang dikerjakan
4. Baca JOURNAL.md entri terbaru untuk konteks sesi sebelumnya
5. Setelah selesai: update JOURNAL.md + SPRINT-LOG.md

---

## LINKS PENTING

| Resource | URL / Path |
|---------|-----------|
| Main repo | `gh repo view fahmiwol/tiranyx-web` |
| Live website | https://tiranyx.co.id |
| Admin CMS | https://tiranyx.co.id/admin |
| VPS SSH | `ssh sidix-vps` |
| Ixonomic API | http://127.0.0.1:3013 (internal VPS) |
| SIDIX AI | https://app.sidixlab.com |

---

*"Tiranyx bukan hanya agency yang punya tools. Tiranyx adalah intelligence platform yang masih bisa ngerjain project."*
