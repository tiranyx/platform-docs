# TIRANYX PLATFORM — DEV JOURNAL
**Format:** Kronologis, terbaru di atas
**Tujuan:** Anti context-loss, learning log, temuan penting
**Protocol:** Tulis SETIAP selesai task signifikan, SETIAP akhir sesi

---

## CARA BACA

Setiap entri berisi:
- **Tanggal & sesi**
- **Yang dikerjakan** (daftar aksi)
- **Temuan / learning** (insight kritis)
- **Keputusan teknis** (dengan rationale)
- **Risiko yang diidentifikasi**
- **Handoff notes** (untuk agent/session berikutnya)

---

## ENTRI JOURNAL

---

### 2026-05-05 | Sesi 2 — Sprint 0 Coding: Products, Pricing, Admin

**Status:** ✅ Selesai — semua target Sprint 0 coding phase terpenuhi, live di VPS

**Konteks sesi:**
Lanjut dari Sesi 1. Mulai coding Sprint 0: tambah pricing ke semua halaman produk, buat /products/[slug] detail pages, admin CRUD, dan deploy ke VPS.

**Yang dikerjakan:**

1. **`app/(site)/products/page.tsx`** — Redesign penuh:
   - Tambah 3 section: SaaS Platforms (6 produk + harga), On-Demand Services (6 layanan + range harga IDR), White-label Bundles (4 produk + setup/monthly pricing)
   - Setiap SaaS card: pricing box (monthly + yearly + hemat info), dual CTA ("Lihat Detail" → /products/[slug] + "Tanya Sales" → WA)
   - On-demand: harga range IDR + timeline tampil (bukan "Per project" lagi)
   - White-label: setup price + monthly license + delivery info

2. **`lib/products-data.ts`** — File baru (tidak ada sebelumnya):
   - 6 produk SaaS dengan full detail: heroFeatures, pricingTiers (starter + pro), howItWorks (4 step), FAQs (4 Q&A), paymentNote, whatsappText
   - Bisa jadi source of truth atau akan diganti DB-driven nanti

3. **`app/(site)/products/[slug]/page.tsx`** — File baru:
   - Breadcrumb navigation, hero dengan status badge + kategori
   - Sections: hero features grid, description, how-it-works (4 steps), pricing tiers (highlighted + regular), FAQ cards
   - Payment methods note, WhatsApp CTA, back to products link
   - generateStaticParams + generateMetadata untuk SEO

4. **`lib/tools-data.ts`** — ToolDef interface ditambah: `freeQuota?`, `priceMonthly?`, `priceYearly?`. Semua 6 tools diupdate dengan pricing (Rp 99.000/bulan Pro).

5. **`app/(site)/tools/page.tsx`** — Tambah pricing row di setiap tool card: "Gratis 5×/hari → Pro Rp 99rb/bln".

6. **`lib/db/migrations-products.ts`** — File baru:
   - CREATE TABLE: products, product_pricing_tiers, product_faq, product_how_it_works
   - Seed otomatis dari `lib/products-data.ts` saat tabel kosong

7. **`lib/db/products.ts`** — File baru: query helpers (getAllProducts, getProductBySlug, updateProductPublished, dll).

8. **`lib/db/schema.ts`** — Tambah import + call `runProductMigrations(db)`.

9. **`app/admin/(protected)/products/page.tsx`** + **`ProductsTable.tsx`** — Admin list dengan toggle published, preview + edit link.

10. **`app/api/admin/products/toggle-published/route.ts`** — API endpoint untuk toggle is_published.

11. **`app/admin/AdminLayoutClient.tsx`** — Tambah nav item "Products" (💎).

12. **`next.config.ts`** — Fix: hapus `turbopack: { root: path.join(__dirname) }` yang menyebabkan build error di VPS.

**Temuan penting:**

1. **next.config.ts turbopack bug**: `path.join(__dirname)` dalam konteks TypeScript compiled config di Next.js 16 menyebabkan `_path.default.join is not a function`. Fix: hapus konfigurasi turbopack root dari config.

2. **VPS Node.js mismatch**: VPS punya Node.js v20 tapi better-sqlite3 dikompil untuk v24 (dari developer lokal). Solusi: `npm rebuild better-sqlite3` di VPS setelah upload.

3. **Missing packages**: `@anthropic-ai/sdk` dan `@google/genai` dipakai di kode tapi tidak ada di package.json. Harus install manual di VPS. Perlu ditambahkan ke package.json lokal.

4. **White-label slugs**: `/products/sociyx-whitelabel`, `/products/wa-agency-suite`, dll. tampil di /products page tapi link `/products/[slug]` akan 404 karena tidak ada di `PRODUCTS` array di products-data.ts. Perlu tambah entri atau buat halaman terpisah.

**Keputusan:**
- Tidak hapus white-label section karena Midtrans membutuhkan harga tampil — CTA-nya menuju WA, bukan detail page
- products-data.ts sebagai static source of truth untuk fase 0; fase 1 akan DB-driven
- Pricing halaman tools tidak di-hardcode; dibaca dari tools-data.ts field baru

**State deployment:**
- Build sukses di VPS (Node v20)
- PM2 id:5 "tiranyx" restart ✅
- Routes verified: /products 200, /products/market-intelligence 200, /tools 200, /admin/products 307 (→ login)
- Nginx cache cleared

**Handoff untuk sesi berikutnya:**
- Todo: tambah 4 white-label produk ke products-data.ts (atau buat halaman khusus)
- Todo: tambah @anthropic-ai/sdk + @google/genai ke package.json lokal
- Todo: /demos pricing (Request Demo vs harga)
- Todo: generate PDF TRANSACTION-FLOW
- Sprint 1: auth system, Midtrans sandbox
- PR lokal: perlu git commit + push ke fahmiwol/tiranyx-web

---

### 2026-05-05 | Sesi 1 — Riset & Dokumentasi Awal

**Status:** ✅ Selesai (dokumentasi), 🔄 Coding belum mulai

**Konteks sesi:**
User meminta: rapihkan website tiranyx.co.id untuk kebutuhan pendaftaran Midtrans (credit card) dan PayPal direct. Diperlukan: product catalog dengan harga, alur transaksi, client management, freemium tools flow, SaaS white-label, waitlist produk riset, dashboard user.

**Yang dikerjakan:**
1. Riset website tiranyx.co.id (semua halaman public)
2. Mapping semua GitHub repos: fahmiwol (37 repos) + tiranyx org (4 repos)
3. SSH ke VPS 72.62.125.6 — eksplorasi semua domains + PM2 processes
4. Baca dokumen kunci: PROJECT_STATUS.md, CLAUDE.md, AGENT-CONTEXT.md, fahmi-direction/INDEX.md
5. Query SQLite schema dari tiranyx.db (VPS)
6. Buat memory system di ~/.claude/projects/C--web-tiranyx/memory/
7. Buat dokumentasi lengkap platform (11 file)

**Temuan Kritis:**

🔴 **CRITICAL:** VPS 72.62.125.6 adalah VPS YANG SAMA untuk tiranyx.co.id DAN SIDIX. SSH sudah terkonfigurasi via alias `sidix-vps`.

🟠 **IMPORTANT:** Ixonomic ecosystem sudah LIVE (7 PM2 proses):
  - ixonomic.com (landing)
  - adm.ixonomic.com:3008 (admin dashboard)
  - api.ixonomic.com:3013 (B2B API)
  - embed.ixonomic.com:3006 (JS Widget CDN)
  - bank.ixonomic.com, bag.ixonomic.com, brx.ixonomic.com, hud.ixonomic.com
  - Ini adalah sistem yang akan diintegrasikan untuk Client Portal!

🟠 **IMPORTANT:** `opix_tiranyx` repo di GitHub KOSONG. OPIX CRM yang dimaksud user = sistem Ixonomic yang sudah live di VPS.

🟡 **NOTE:** apix.tiranyx.co.id sudah live sebagai Shopee API Gateway (Hono framework). Ini bisa jadi fondasi untuk API aggregator publik.

🟡 **NOTE:** tiranyx.co.id punya 6 tools (QR, UTM, Color Palette, AI Writer, Caption, Hashtag) tapi TANPA quota enforcement dan TANPA user auth. Tools accessible bebas.

**Keputusan Teknis:**

| Keputusan | Rationale |
|-----------|-----------|
| Auth terpisah untuk user vs admin | Admin auth existing (HMAC cookie trx_admin) tidak boleh diubah. User auth pakai sistem baru (trx_user cookie). |
| SQLite tetap (tidak migrate ke PostgreSQL) | Overhead setup tidak worth it untuk fase awal. SQLite mampu handle sampai ~50K concurrent users dengan caching. |
| /products/[slug] bukan /saas/[slug] | Semua produk (tools, SaaS, on-demand) under /products untuk konsistensi SEO dan routing. |
| Ixonomic API via internal call | VPS sama, API call ke 127.0.0.1:3013 lebih cepat + aman daripada lewat internet roundtrip. |
| White-label delivery via GitHub API | Automasi: setelah bayar → auto-invite ke private repo. Scalable, tidak manual. |

**Risiko yang Diidentifikasi:**

| Risiko | Likelihood | Impact | Mitigasi |
|--------|-----------|--------|---------|
| Kominfo PSE untuk live streaming | Medium | High | Research kebutuhan, mungkin tidak perlu jika hanya agency service bukan platform |
| SQLite concurrent write limit | Low (saat ini) | Medium | Monitor jika user growth >1000/hari |
| Ixonomic API auth compatibility | Medium | Medium | Test Ixonomic API sebelum build client portal |
| Midtrans webhook delivery failure | Low | High | Idempotency check + retry mechanism |
| GitHub API rate limit (delivery) | Low | Low | Rate: 5000 req/jam, cukup untuk fase awal |

**Handoff untuk Sesi Berikutnya:**

```
STATE SEKARANG:
- Repo: fahmiwol/tiranyx-web (main codebase, private)
- Docs repo baru: tiranyx/platform-docs (akan dibuat di akhir sesi ini)
- VPS: 72.62.125.6, SSH via `sidix-vps`
- PM2 process: tiranyx (id:5, port 3001)
- Build: sudah live, tidak ada pending changes

TASK BERIKUTNYA (prioritas):
1. Push docs ke tiranyx/platform-docs
2. Sprint 0 coding: tambah harga ke /products
3. Buat /products/[slug] template

YANG HARUS DIBACA DULU:
- PROTOCOL.md (mandatory)
- ROADMAP.md Sprint 0 backlog
- SPRINT-LOG.md daily log terbaru

FILES YANG BERUBAH SESI INI:
- Hanya documentation (file baru di C:\web tiranyx\)
- tiranyx-web/tiranyx.db: tidak ada perubahan
- VPS: tidak ada deploy
```

---

### [TEMPLATE ENTRI BARU]

```markdown
### YYYY-MM-DD | Sesi N — [Judul Singkat]

**Status:** ✅/🔄/❌

**Konteks sesi:**
[Apa yang user minta, konteks sprint]

**Yang dikerjakan:**
1. 
2. 

**Temuan:**
🔴 CRITICAL: 
🟠 IMPORTANT: 
🟡 NOTE: 

**Keputusan teknis:**
| Keputusan | Rationale |
|-----------|-----------|
| | |

**Risiko:**
| Risiko | Likelihood | Impact | Mitigasi |
|--------|-----------|--------|---------|
| | | | |

**Handoff:**
STATE SEKARANG:
- 

TASK BERIKUTNYA:
1. 

FILES YANG BERUBAH:
- 
```

---

## LESSONS LEARNED LOG

*Akumulasi learning yang tidak boleh diulang atau harus dilipatgandakan*

| Tanggal | Learning | Tipe | Aksi |
|---------|---------|------|------|
| 2026-05-05 | VPS 72.62.125.6 = shared VPS untuk semua proyek Tiranyx, SSH via sidix-vps alias | Temuan infrastruktur | Catat di PROTOCOL.md sebagai referensi cepat |
| 2026-05-05 | Ixonomic sudah live tapi belum diintegrasikan ke tiranyx.co.id | Gap temuan | Masukkan ke Sprint 3 sebagai integration task |
| 2026-05-05 | opix_tiranyx repo kosong, bingungkan agent yang tidak tahu | Clarity issue | Catat di RESEARCH.md bahwa OPIX = Ixonomic |
| 2026-05-05 | Deploy tidak bisa via SSH port 22 dari local; pakai aaPanel atau tar.gz upload | Infrastruktur | Dokumentasikan di ARCHITECTURE.md deploy section |

---

## QUICK REFERENCE — COMMAND CHEATSHEET

```bash
# SSH ke VPS
ssh sidix-vps

# Di VPS: restart tiranyx
pm2 restart tiranyx

# Di VPS: lihat log
pm2 logs tiranyx --lines 50

# Di VPS: check semua proses
pm2 list

# Di VPS: backup DB
cp /www/wwwroot/tiranyx.co.id/tiranyx.db /www/wwwroot/tiranyx.co.id/backups/tiranyx.db.$(date +%Y%m%d)

# Di VPS: clear nginx cache
find /www/server/nginx/proxy_cache_dir -type f -delete 2>/dev/null && nginx -s reload

# Di VPS: check disk
df -h && free -m

# GitHub: clone tiranyx-web
gh repo clone fahmiwol/tiranyx-web

# GitHub: list tiranyx org repos  
gh repo list tiranyx
```

---

*Journal ini harus di-update setiap sesi kerja. Context yang tidak tercatat = hilang.*
