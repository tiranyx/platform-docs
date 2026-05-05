# TIRANYX PLATFORM — RESEARCH FINDINGS
**Version:** 1.0.0 | **Date:** 2026-05-05
**Tujuan:** Catat semua temuan riset ecosystem, mapping produk, infrastruktur, dan insight eksternal

---

## 1. ECOSYSTEM MAPPING (2026-05-05)

### 1.1 VPS Infrastruktur

**VPS:** Hostinger, IP: `72.62.125.6`
**Panel:** aaPanel + Nginx + PM2
**Node.js:** v24.14.1
**SSH:** alias `sidix-vps` → root@72.62.125.6, key: `~/.ssh/sidix_session_key`

**TEMUAN KRITIS:** tiranyx.co.id DNS resolves ke 72.62.125.6 = VPS yang sama dengan SIDIX. Bukan VPS terpisah seperti yang diasumsikan sebelumnya.

**23 PM2 Processes (semua di VPS ini):**

| ID | Nama | Status | Memory | Domain |
|----|------|--------|--------|--------|
| 5 | tiranyx | online | 61.2MB | tiranyx.co.id:3001 |
| 1 | shopee-gateway | online | 63.9MB | apix.tiranyx.co.id |
| 23 | gateway | online | 279.6MB | tiranyx-gateway |
| 9 | ixonomic-landing | online | 33.0MB | ixonomic.com |
| 11 | ixonomic-api | online | 45.1MB | api.ixonomic.com:3013 |
| 10 | ixonomic-bag | online | 33.8MB | bag.ixonomic.com |
| 12 | ixonomic-embed | online | 44.5MB | embed.ixonomic.com:3006 |
| 13 | ixonomic-bank | online | 70.4MB | bank.ixonomic.com |
| 16 | ixonomic-docs | online | 45.2MB | docs.ixonomic.com |
| 15 | ixonomic-hud | online | 44.6MB | hud.ixonomic.com |
| 20 | ixonomic-adm | online | 45.3MB | adm.ixonomic.com:3008 |
| 14 | ixonomic-uts | online | 44.9MB | uts.ixonomic.com |
| 19 | brangkas-dashboard | online | 44.0MB | brx.ixonomic.com |
| 25 | mighan-web | online | 44.0MB | mighan-next/mighan.com |
| 3 | galantara-mp | online | 76.1MB | galantara.io |
| 2 | abra-website | online | 419.1MB | abrabriket.com |
| 21 | sidix-brain | online | 947.2MB | app.sidixlab.com |
| 4 | sidix-ui | online | 62.0MB | app.sidixlab.com |
| 26 | sidix-next-ui | online | 43.9MB | ctrl.sidixlab.com |
| 0 | revolusitani | online | 90.9MB | revolusitani-app |
| 7 | sidix-health | stopped | 0B | - |
| 8 | sidix-health-prod | stopped | 0B | - |
| 6 | sidix-mcp-prod | stopped | 0B | - |

**Total memory used by PM2:** ~1.5GB

---

### 1.2 Directory Structure (/www/wwwroot/)

```
/www/wwwroot/
├── tiranyx.co.id/          → Main website (Next.js 16.2.1, port 3001)
│   ├── app/, components/, lib/
│   ├── tiranyx.db          → SQLite database
│   ├── data/               → migrations, seeding
│   └── public/uploads/     → uploaded files
├── apix.tiranyx.co.id/     → Shopee API Gateway (Hono)
├── brx.ixonomic.com/       → Brangkas dashboard (Next.js)
├── embed.ixonomic.com/     → Ixonomic JS Widget CDN
├── abra-website/           → abrabriket.com (SKIP)
├── revolusitani-app/       → Revolusi Tani app
├── mighan-next/            → mighan.com landing + studio
├── ops.mighan.com/         → mighantect-root (scripts)
└── galantara.io/           → Galantara platform
```

---

### 1.3 GitHub Repository Mapping

**Account: fahmiwol** (37 repos total)

**Core Platform Repos:**
```
tiranyx-web (private, TypeScript)
  → MAIN WEBSITE: tiranyx.co.id
  → Stack: Next.js 16.2.1, React 19, TypeScript, Tailwind 4, SQLite
  → VPS path: /www/wwwroot/tiranyx.co.id/
  → Deploy: tar.gz upload → extract → npm install → build → pm2 restart tiranyx

tiranyx-gateway (private, TypeScript)
  → Backend orchestrator (PM2: gateway, 279MB)
  → Kemungkinan: Express/Fastify API layer

sociyx (PUBLIC, TypeScript) ← bisa showcase di website
  → SOCIYX Multi-client Social Media Management Platform
  → Ini yang dimaksud "SocioStudio Full" di /demos

bg-maker (private, TypeScript)
  → Brand Guideline Builder (StepbySTEPH based)
  → Ini yang mau diintegrasikan sebagai "Brand Guidelines Tool"
  → Status: di-develop
```

**Ixonomic Ecosystem Repos (semua private):**
```
mighan-coin     → Mighan Coin - Payment Infrastructure as a Service
mighan-embed    → Ixonomic JS Widget CDN (embed.ixonomic.com:3006)
kontrol-tiranyx → Ixonomic Admin Dashboard (adm.ixonomic.com:3008)
mighan-api      → Ixonomic B2B API Gateway (api.ixonomic.com:3013)
bank-tiranyx    → Bank backend (Fastify + PostgreSQL)
Dompet-tiranyx  → Wallet app
Uhud-tiranyx    → Uhud component
Raumah-tiranyx  → Raumah component
```

**AI Projects:**
```
sidix (PUBLIC, Python)
  → SIDIX Free & Open Source AI Agent
  → Self-hosted, Qwen2.5-7B + LoRA, 35 tools
  → VPS: app.sidixlab.com, ctrl.sidixlab.com, sidix-brain (947MB RAM!)
  → Islamic Epistemology (IHOS) based

fahmi-direction (private)
  → Master hub strategic docs, agent rules, roadmap
  → Baca: AGENT-RULES.md, INDEX.md, TIRANYX-HOLDING-PRD.md
```

**Waitlist Products (GitHub repos):**
```
tiranyx/migancore (PUBLIC, Python)
  → ADO AI Core Brain System Autonomous
  → Updated: 2026-05-05 (active development!)

tiranyx/migancore-community (PUBLIC)
  → Community repo untuk migancore

tiranyx/migancore-platform (private)
  → Platform implementation (private)

tiranyx/sidix (PUBLIC, Python)
  → Mirror/fork dari fahmiwol/sidix

fahmiwol/mighan-web (PUBLIC, TypeScript)
  → Mighan World landing page

fahmiwol/mighancoin_token (PUBLIC)
  → Mighan Coin token docs
  → "Setiap koin lahir dengan jiwa" — poetic!

fahmiwol/NPC-Agent-AI-Ecosystem (PUBLIC)
  → Open platform for Agentic AI NPCs
  
fahmiwol/AI-agent-Workflow-visual-script-builder (PUBLIC, HTML)
  → Visual drag-drop workflow untuk AI agents
```

---

### 1.4 tiranyx.co.id Internal Analysis

**Tech stack confirmed:**
- Next.js 16.2.1 (bukan 14, bukan 15)
- React 19
- TypeScript
- Tailwind 4 (bukan v3)
- SQLite via better-sqlite3 v11.10.0
- Node.js v24.14.1

**Admin CMS (sudah live, full CRUD):**
- Blog posts (SQLite, TinyMCE editor, image upload + AI generate)
- Case studies
- Portfolio items
- Resources
- App demos
- Contacts
- Settings
- AI writer tool (/admin/ai-writer)
- Saas (route exists, KOSONG)

**Public routes dan data source:**
- /blog → SQLite (fully dynamic)
- /blog/[slug] → SQLite
- /portfolio → SQLite (portfolio_items)
- /portfolio/case-studies → SQLite (case_studies)
- /resources → resources.json + SQLite blog
- /demos → SQLite (app_demos)
- /tools → lib/tools-data.ts (STATIC — perlu migrate ke DB)
- /products → STATIC (perlu migrate ke DB)
- /services → lib/services-data.ts (STATIC)

**Tools yang live:**
1. QR Code Generator — /tools/qr-generator
2. UTM Link Builder — /tools/utm-builder
3. Color Palette Generator — /tools/color-palette
4. AI Article Writer — /tools/ai-writer (Pollinations AI)
5. Caption Generator — /tools/caption-generator
6. Hashtag Generator — /tools/hashtag-generator

**Tools yang ada di homepage/nav tapi beda dari /tools:**
- AI Article Writer (juga ada di /admin/ai-writer)

---

### 1.5 Produk yang Live di Demos (/demos)

| Produk | Status | Repo/Source |
|--------|--------|-------------|
| SocioStudio Lite | Live | sociyx? |
| SocioStudio Full | Beta | sociyx (SOCIYX) |
| WA Agency Suite | Beta | wa-gateway? |
| Bot Gateway | Beta | - |
| OPIX Agency CRM | Beta | Ixonomic system? |
| VoiceClone Studio | Beta | TTS_voice? |

---

## 2. IXONOMIC DEEP DIVE

**Temuan:** Ixonomic bukan hanya "OPIX CRM" — ini adalah ekosistem payment + CRM yang jauh lebih kompleks.

### 2.1 Ixonomic Architecture (dari nama PM2 processes)

```
ixonomic.com (landing page)
  ├── adm.ixonomic.com (admin dashboard = kontrol-tiranyx repo)
  ├── api.ixonomic.com (B2B API = mighan-api repo)
  ├── embed.ixonomic.com (JS widget = mighan-embed repo)
  ├── bank.ixonomic.com (banking component = bank-tiranyx repo)
  ├── bag.ixonomic.com (wallet/bag)
  ├── brx.ixonomic.com (brangkas dashboard = brangkas-dashboard PM2)
  ├── hud.ixonomic.com (HUD interface)
  ├── uts.ixonomic.com (unknown)
  └── docs.ixonomic.com (documentation)
```

### 2.2 Interpretasi

Berdasarkan nama repo dan domain:
- **Ixonomic** = payment rail ekosistem (coin/token economy)
- **Brangkas** = vault/wallet (simpan coin/token)
- **Bank** = banking layer (transfer, balance)
- **HUD** = heads-up display (mungkin trading/portfolio view)
- **Embed** = widget untuk embed ke website lain
- **ADM** = admin untuk manage semua transaksi
- **API** = B2B API untuk integrasi pihak ketiga

**Hipotesis:** Ixonomic adalah sistem coin/payment Tiranyx (yang disebut "Ixonomic Coin" di waitlist). Bukan CRM klien. OPIX CRM berbeda — kemungkinan adalah agency-os yang belum di-deploy.

---

## 3. PRODUK RISET (WAITLIST)

### 3.1 Migancore (tiranyx/migancore)

- **Deskripsi:** ADO AI Core Brain System Autonomous
- **Status:** Public repo, active development (updated 2026-05-05)
- **Language:** Python
- **URL:** github.com/tiranyx/migancore
- **Community repo:** tiranyx/migancore-community
- **Platform repo (private):** tiranyx/migancore-platform
- **Insight:** Diupdate hari ini, berarti sedang aktif dikerjakan

### 3.2 Mighan World (fahmiwol/mighan-web + mighantect-3d)

- **Deskripsi:** NPC AI Agent World — 3D virtual world city
- **Status:** Landing page live (mighan-next di VPS), 3D city (private repo)
- **VPS:** mighan-web PM2 process running
- **URL landing:** mighan.com
- **Insight:** 3D world sudah ada, hanya butuh landing page yang link ke demo + waitlist

### 3.3 SIDIXLab Creative AI (fahmiwol/sidix)

- **Deskripsi:** Free & Open Source AI Agent, Qwen2.5-7B + LoRA, 35 tools
- **Status:** LIVE di app.sidixlab.com + ctrl.sidixlab.com
- **Language:** Python
- **Insight:** Ini sudah live, bukan waitlist murni. Tapi masih "research phase" untuk versi productized

### 3.4 Ixonomic Coin (fahmiwol/mighancoin_token + mighan-coin)

- **Deskripsi:** Payment infrastructure, "setiap koin lahir dengan jiwa"
- **Status:** Sistem backend live (7 PM2 processes), token docs publik
- **Insight:** Infrastruktur sudah ada, butuh public-facing waitlist/explainer

---

## 4. MIDTRANS REQUIREMENT ANALYSIS

### 4.1 Apa yang Midtrans Minta

Berdasarkan pesan user:
1. **Konfirmasi penggunaan Midtrans** — untuk transaksi apa saja
2. **Produk + harga di website merchant** — website harus tampilkan harga
3. **Lisensi Kominfo** — karena ada layanan live streaming
4. **Alur transaksi dalam PDF** — dari awal order sampai bayar

### 4.2 Gap Analysis

| Requirement | Status | Action |
|-------------|--------|--------|
| Konfirmasi penggunaan | ❌ Belum | Tulis di midtrans/PRODUCT-LIST.md |
| Produk + harga di website | ❌ Tidak ada harga | Sprint 0 priority |
| Lisensi Kominfo | ❌ Perlu research | Research: apakah agency service butuh PSE? |
| Alur transaksi PDF | ❌ Belum ada | Buat midtrans/TRANSACTION-FLOW.md → PDF |

### 4.3 Kominfo PSE Research (Hipotesis)

**Pertanyaan:** Apakah tiranyx.co.id butuh PSE Kominfo?

**Konteks:** PSE (Penyelenggara Sistem Elektronik) wajib untuk:
- Platform live streaming berbayar
- Aplikasi yang memproses data pribadi >10K user
- Sistem transaksi elektronik

**Hipotesis 1 (likely):** Jika live streaming adalah **layanan agency** (Tiranyx yang *execute* live streaming untuk klien, bukan platform untuk orang lain live streaming) → mungkin tidak butuh PSE untuk live streaming secara khusus. Butuh PSE umum untuk sistem transaksi.

**Hipotesis 2:** Jika Tiranyx nanti punya fitur di mana user LAIN bisa live streaming melalui platform → butuh PSE + izin khusus.

**Action:** Konsultasi ke Kominfo atau lawyer digital. Jangan asumsikan.

---

## 5. EXTERNAL RESEARCH INSIGHTS

### 5.1 Freemium Conversion (2025-2026 data)

Dari berbagai sumber (Product Hunt discussions, IndieHackers, SaaS research):
- Median freemium → paid conversion: **2–5%**
- Top performers: **10–25%** (Canva, Notion saat early)
- Tools dengan clear quota limit + contextual upsell: **7–12%**
- Indonesia-specific: trial tanpa CC lebih convert (BAYAR setelah value terbukti)

**Implication untuk Tiranyx:**
- Target minimal 5% conversion
- Dengan 1000 free users → 50 paying users
- Pro Rp 99rb/bln × 50 = Rp 4.95jt MRR dari tools saja
- Perlu traffic: SEO tools pages (QR, caption, hashtag keywords volume tinggi)

### 5.2 Agency to Platform Pivot (2025 trend)

Tren yang terjadi di dunia:
- **HubSpot** bermula sebagai blog/consultant, pivot ke platform
- **Hootsuite** bermula sebagai agency tools, pivot ke SaaS
- **Semrush** bermula sebagai SEO tool, expand ke marketing platform

Pattern pivot yang berhasil:
1. Agency service sebagai anchor (recurring revenue + customer insight)
2. Build tools yang agensi butuhkan sendiri
3. Productize tools untuk klien lain
4. Agency menjadi upsell ke platform, bukan sebaliknya

**Tiranyx sedang di titik 2→3.** Timing bagus.

### 5.3 AI-Native SaaS vs AI-Added SaaS (2026)

Research dari berbagai sumber:
- **AI-added** (HubSpot AI, Canva AI, dll.) = fitur tambahan, user kadang tidak pakai
- **AI-native** (baru mulai dari scratch dengan AI) = jauh lebih defensible
- SIDIX sebagai brain layer = positioning yang kuat jika tersambung ke tools Tiranyx

**Rekomendasi:** Pastikan SIDIX terintegrasi ke tools Tiranyx sebagai differentiator. Bukan API eksternal (OpenAI) tapi model lokal yang bisa dikontrol + improve.

### 5.4 Indonesian SaaS Market (2025-2026)

Data yang relevant:
- Penetrasi internet Indonesia ~79% (250+ juta jiwa → ~200M pengguna internet)
- UMKM Indonesia: 65 juta unit, 80% masih pakai digital tools seadanya
- Pasar digital marketing Indonesia: tumbuh ~20% YoY
- Payment: GoPay + QRIS mendominasi untuk transaksi < Rp 1jt
- CC: ~15% populasi punya kartu kredit, tapi growing
- WA Business: 90%+ UMKM pakai WA sebagai channel utama

**Implication:**
- GoPay/QRIS harus di-prioritaskan, bukan CC
- WA integration = trust signal + support channel
- Bahasa Indonesia UI = wajib (English optional)
- Mobile-first (60%+ traffic dari mobile)

---

## 6. PRODUK MAPPING — DIKELOMPOKKAN

### 6.1 Yang SUDAH ADA (live, bisa langsung showcase)

| Produk | URL / Akses | Status |
|--------|-------------|--------|
| Tiranyx.co.id | tiranyx.co.id | ✅ Live |
| SIDIX AI | app.sidixlab.com | ✅ Live |
| Ixonomic ecosystem | ixonomic.com | ✅ Live |
| Shopee API Gateway | apix.tiranyx.co.id | ✅ Live |
| Mighan World landing | mighan.com (mighan-next) | ✅ Live |
| Galantara | galantara.io | ✅ Live |
| QR, UTM, Color, AI Writer, Caption, Hashtag tools | tiranyx.co.id/tools | ✅ Live |

### 6.2 Yang ADA di VPS tapi BELUM TERDOKUMENTASI BAIK

| Produk | PM2 Name | Notes |
|--------|---------|-------|
| Tiranyx Gateway | gateway | 279MB, kemungkinan orchestrator |
| Brangkas Dashboard | brangkas-dashboard | brx.ixonomic.com |
| SIDIX Next UI | sidix-next-ui | ctrl.sidixlab.com |
| Revolusi Tani app | revolusitani | revolusitani-app dir |

### 6.3 Yang ADA di GitHub tapi BELUM di-deploy

| Produk | Repo | Notes |
|--------|------|-------|
| SOCIYX | fahmiwol/sociyx | Social Media SaaS |
| Brand Guideline Builder | fahmiwol/bg-maker | TypeScript, aktif dikembangkan |
| Migancore Platform | tiranyx/migancore-platform | Private |
| WA Gateway | fahmiwol/wa-gateway | TypeScript |

### 6.4 Yang BUTUH WAITLIST SEKARANG

| Produk | Target audience | Selling point |
|--------|----------------|---------------|
| Migancore ADO AI | Developer, enterprise | AI autonomous system |
| Mighan World | Gamer, developer, brand | NPC AI world |
| SIDIXLab | Developer, AI researcher | Local AI, no vendor lock |
| Ixonomic Coin | Crypto enthusiast, ecosystem user | Payment rail |

---

## 7. CATATAN PENTING — JANGAN DIULANGI

### ❌ Anti-patterns dari sesi riset:

1. **Jangan assume semua pada VPS terpisah** — semua on 72.62.125.6
2. **Jangan ubah design system homepage** — space theme adalah brand identity
3. **Jangan touch ABRA/Nutrisius** — explicitly excluded
4. **Jangan commit tiranyx.db** — SQLite file diabaikan di .gitignore
5. **Jangan deploy tanpa backup DB** — data produksi ada di sana
6. **Jangan pakai `--no-verify` saat commit** — hooks ada alasannya
7. **Jangan deploy jam kerja sibuk (10:00-22:00 WIB)** — risk window lebar

### ✅ Patterns yang berhasil:

1. **SSH via `sidix-vps` alias** — sudah ada, langsung bisa
2. **SQLite query via sqlite3 CLI di VPS** — untuk inspect data
3. **PM2 list** — untuk monitoring semua proses
4. **gh CLI** — untuk GitHub operations tanpa browser
5. **Dokumentasi lengkap di awal** — investasi yang worth it

---

*Research doc diupdate setiap ada temuan baru yang significant.*
*Selalu verify temuan dengan melihat kondisi aktual (VPS, GitHub, live website) sebelum implement.*
