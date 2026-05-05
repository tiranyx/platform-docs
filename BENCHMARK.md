# TIRANYX PLATFORM — COMPETITIVE BENCHMARK
**Version:** 1.0.0 | **Date:** 2026-05-05
**Tujuan:** Pelajari platform terbaik dunia, ambil yang relevan, tinggalkan yang tidak cocok untuk konteks Indonesia

---

## METODOLOGI

Benchmark ini dibagi per **dimensi fungsi** bukan per kompetitor, karena Tiranyx adalah platform hybrid (tools + agency + SaaS). Untuk setiap dimensi, kami analisis 3–5 platform terbaik di kelasnya.

---

## 1. MEMBER DASHBOARD PASCA-PEMBELIAN

**Referensi benchmark:** Niagahoster, Hostinger, Dewaweb, DigitalOcean

### Apa yang mereka lakukan dengan baik:

| Platform | Pattern | Mengapa Berhasil |
|----------|---------|------------------|
| **Hostinger hPanel** | Single dashboard, semua produk dalam 1 tab | Zero context-switching, semua ada di satu tempat |
| **Niagahoster** | Card per produk aktif di overview | Visual, langsung tahu apa yang dimiliki |
| **DigitalOcean** | Billing + resource dalam 1 view | Transparansi biaya real-time |
| **Vercel** | Instant deploy feedback | Deployment status langsung visible |

### Anti-patterns yang dihindari:
- ❌ Terlalu banyak menu (> 7 item sidebar) → user bingung
- ❌ Dashboard kosong tanpa onboarding → high early churn
- ❌ Harus klik banyak untuk sampai ke fitur utama

### Adoptasi untuk Tiranyx:
```
✅ Dashboard overview = "control panel" sederhana
✅ Card per produk aktif (seperti Niagahoster)
✅ Quick actions langsung di card
✅ Onboarding checklist untuk user baru (progress bar)
✅ Empty state yang informatif dengan CTA jelas
```

---

## 2. FREEMIUM → PRO CONVERSION

**Referensi:** Canva, Notion, Mailchimp, Semrush, Grammarly

### Conversion patterns yang proven:

| Platform | Trigger | Conversion Rate (est.) |
|----------|---------|------------------------|
| **Canva** | Kena paywall saat klik elemen Pro | ~10-15% dari free users |
| **Grammarly** | Weekly report: "X issues missed because you're free" | ~8% trial conversion |
| **Notion** | Workspace full saat collaborate | ~12% |
| **Mailchimp** | Subscriber limit + fitur locked | ~7% |
| **Semrush** | Query limit habis saat riset | ~6% |

### Best practices:
1. **Show value first, gate later** — Canva model: biarkan user buat dulu, baru lock saat save/export
2. **Contextual upsell** — muncul saat user hit limit, bukan di homepage
3. **"X of Y used today"** — visual progress bar yang membuat user aware sebelum hit limit
4. **Free tier yang genuinely useful** — Mailchimp 500 subscriber FREE benar-benar berguna
5. **Annual discount** — 20-40% discount untuk annual, menurunkan monthly churn

### Adoptasi untuk Tiranyx:
```
✅ Tool tetap accessible, gated saat kuota habis
✅ "3 dari 5 caption dipakai hari ini" — visible di tool
✅ Upsell modal muncul SAAT limit, bukan sebelumnya
✅ Pro vs Free: comparison tabel yang jelas (Canva style)
✅ 7-day Pro trial gratis (collected email, no CC)
✅ Annual: hemat 2 bulan (bayar 10, dapat 12)
```

---

## 3. AGENCY CLIENT PORTAL

**Referensi:** Basecamp, Monday.com Client View, HubSpot Client Portal, Agency Analytics

### Apa yang klien agency butuhkan:

| Kebutuhan | Platform | Implementation |
|-----------|---------|----------------|
| Lihat progress project | Basecamp | Gantt + todolist per project |
| Approve deliverable | Agency Analytics | Preview + approve button |
| Download file | Google Drive embed | Folder per project |
| Bayar invoice | FreshBooks | Invoice + payment link |
| Komunikasi | Basecamp | Thread per project |

### Pain points klien agency (dari riset komunitas):
- "Harus tanya WA terus untuk update" → butuh self-service status
- "Tidak tahu file mana yang final" → butuh version control sederhana
- "Invoice datang tiba-tiba" → butuh transparency biaya
- "Tidak bisa approve dari HP" → butuh mobile-friendly

### Adoptasi untuk Tiranyx:
```
✅ Project status = 5 stage simple (bukan 20 kolom)
✅ Approve dengan 1 klik + textarea catatan revisi
✅ File terakhir selalu di atas (tidak perlu cari version)
✅ Invoice dengan payment button langsung (Midtrans)
✅ WhatsApp link ke PIC tersedia di setiap project
✅ Mobile-first design untuk /client
```

---

## 4. ON-DEMAND SERVICE MARKETPLACE

**Referensi:** Fiverr, Sribu, 99designs, Tokopedia Layanan, Jasa.com

### Order flow terbaik:

| Step | Fiverr | Sribu | Adoptasi Tiranyx |
|------|--------|-------|-----------------|
| Discovery | Search + filter | Kategori + harga | /services catalog dengan filter |
| Brief | Buyer requirement form | Brief upload | Custom form per service type |
| Pembayaran | CC/PayPal | Transfer/CC | Midtrans Snap |
| Progress | Order timeline | Update manual | Status + % progress |
| Delivery | File download | Email | Dashboard download |
| Review | Rating + review | Review | (fase 2) |

### Indonesian-specific:
- **Sribu** (kontes desain Indonesia) — buyer brief sederhana, pembayaran lokal
- **99designs** — quality tier (basic/standard/premium) memudahkan pilihan
- **Tokopedia Layanan** — percayai karena ekosistem yang sudah dikenal

### Learning:
- Order form yang terlalu panjang = high abandon rate
- Harga range (bukan fixed) acceptable di Indonesia ("mulai dari Rp X")
- Timeline yang jelas > timeline yang ambigu
- WA sebagai backup komunikasi = trust builder

### Adoptasi untuk Tiranyx:
```
✅ Brief form max 5 field untuk service dasar
✅ Harga range + "custom" untuk scope besar
✅ Timeline estimate di halaman service (sebelum order)
✅ Status notifikasi via email + optional WA
✅ Download deliverable max 1 klik dari dashboard
```

---

## 5. SaaS PRICING & PACKAGING

**Referensi:** Mekari, Majoo, Sleekr, Hootsuite, Buffer, Sprout Social

### Indonesian SaaS pricing patterns:

| Platform | Model | Harga | Notes |
|----------|-------|-------|-------|
| **Mekari Jurnal** | Per module/user | Rp 200rb – 500rb/usr/bln | Bundle discount |
| **Majoo** | Per outlet | Rp 350rb – 1.5jt/outlet/bln | Setup fee |
| **Sleekr** | Per karyawan | Rp 30rb – 100rb/karyawan/bln | HRIS |
| **Buffer** | Per channel | $6 – $120/bln | International |
| **Hootsuite** | Per user | $99 – $249/bln | Too expensive for ID |

### Pricing lessons dari market Indonesia:
1. **Anchor pricing** — tampilkan harga tertinggi dulu, user merasa harga tengah "terjangkau"
2. **"Paling Populer" badge** — social proof, tingkatkan conversion ke tier tengah
3. **Setup fee yang dibenarkan** — untuk white-label/enterprise (bukan barrier, tapi trust signal)
4. **Trial tanpa CC** — Indonesia lebih trust tanpa credit card requirement
5. **Tahunan dengan invoice** — banyak UMKM bayar tahunan karena pembukuan lebih mudah

### Adoptasi untuk Tiranyx:
```
Tier structure:
├── FREE      → 5 uses/day, basic features (lead magnet)
├── PRO       → Rp 99.000/bln — unlimited, semua tools (core revenue)
├── BUSINESS  → Rp 299.000/bln — Pro + API + priority support
└── ENTERPRISE → custom — white-label, dedicated support

✅ "Paling Populer" di tier Pro
✅ Annual pricing (hemat 2 bulan)
✅ 7-day free trial Pro (no CC)
✅ Setup fee untuk white-label bundle (Rp 2.5jt – 5jt)
✅ Invoice tersedia untuk payment tahunan
```

---

## 6. WAITLIST & PRODUCT LAUNCH

**Referensi:** Product Hunt, Robinhood (US waitlist), Notion (beta), Superhuman

### Waitlist best practices:

| Pattern | Platform | Effectiveness |
|---------|---------|---------------|
| Referral-based queue | Robinhood (free stock) | Viral growth, 1M+ waitlist |
| "X people ahead of you" | Superhuman | FOMO trigger |
| Progress updates email | Notion Public Beta | Retention in queue |
| GitHub repo + public build | SIDIX/Migancore | Developer trust |
| Early bird discount | Common SaaS | Incentivize early commit |

### Learning:
- Referral waitlist ≠ viral di semua konteks; butuh incentive yang benar
- Developer audiences: show code > show marketing copy
- B2C: countdown + FOMO > feature list
- Konfirmasi email penting untuk hygiene list

### Adoptasi untuk Tiranyx:
```
✅ Tiap waitlist produk punya halaman sendiri (/waitlist/[slug])
✅ Counter "X sudah daftar" (social proof)
✅ GitHub link untuk produk open source (Migancore, SIDIX)
✅ Email update setiap milestone (milestone-based, bukan weekly spam)
✅ Early bird: 30% diskon 3 bulan pertama untuk waitlist user
✅ Role selection: User / Developer / Partner / Investor
   (segmentasi untuk outreach yang tepat)
```

---

## 7. SEO & CONTENT STRATEGY

**Referensi:** Ahrefs, Semrush blog, HubSpot Academy, Neil Patel, Niagahoster blog ID

### Tool-based SEO (kunci Tiranyx):

| Platform | Tactic | Traffic contribution |
|----------|--------|---------------------|
| **HubSpot** | Free tools (website grader, signature gen) | ~30% organic traffic |
| **Ahrefs** | Backlink checker (free version) | Massive free tool traffic |
| **Neil Patel** | Free SEO analyzer | Lead magnet SEO |
| **Canva** | "Free [X] template" pages | Long-tail dominance |
| **Moz** | DA checker, keyword tools | Developer/SEO traffic |

### Learning: setiap tool = SEO entry point
- "Qr code generator gratis" → 40K/bln searches (Indonesia)
- "Caption instagram generator AI" → 15K/bln
- "Hashtag generator tiktok" → 25K/bln
- "Hitung ROI iklan facebook" → 8K/bln

### Adoptasi untuk Tiranyx:
```
✅ Setiap tool = halaman terpisah dengan URL SEO-friendly
✅ Schema.org SoftwareApplication per tool
✅ Blog content: "Cara pakai [tool]" + "Contoh [output tool]"
✅ Bahasa Indonesia FIRST (Canva ID model: translate + localize)
✅ Long-tail: "generator caption instagram gratis 2026 AI"
✅ Tool comparison pages: "Canva vs Tiranyx Tools"
```

---

## 8. PAYMENT & CHECKOUT UX

**Referensi:** Tokopedia, Shopee, Midtrans Snap, Xendit, Stripe Checkout

### Checkout friction points Indonesia:

| Friction | Penyebab | Solusi |
|---------|---------|--------|
| Tidak punya CC | Penetrasi CC rendah | GoPay, QRIS, VA sebagai default |
| Takut ketipu | Trust issue | Logo Midtrans, SSL, review produk |
| Form terlalu panjang | Cognitive overload | Guest checkout, min field |
| Promo tidak jelas | UX buruk | Kode promo field yang prominent |
| Tidak ada konfirmasi | Anxiety | Email + WA instant setelah bayar |

### Checkout flow terbaik (Tokopedia model):
```
Pilih → Review order (1 halaman) → Pilih metode bayar → Bayar → Done
```
Bukan:
```
Pilih → Form data → Konfirmasi → Pilih bank → Transfer → Lapor → Verifikasi → Done
```

### Adoptasi untuk Tiranyx:
```
✅ Midtrans Snap (popup, tidak redirect keluar site)
✅ Default: tampilkan QRIS + GoPay + VA BCA/Mandiri/BNI
✅ CC tersedia tapi bukan urutan pertama
✅ Guest checkout untuk tools one-time (baru register setelah bayar)
✅ Order summary terlihat selama checkout (tidak hilang)
✅ Email konfirmasi dalam 30 detik setelah pembayaran
✅ WA konfirmasi (optional opt-in)
```

---

## 9. WHITE-LABEL SaaS DELIVERY

**Referensi:** Vendasta, GoHighLevel, Simvoly, White Label Suite

### White-label delivery patterns:

| Platform | Delivery | Time to market |
|----------|---------|----------------|
| **GoHighLevel** | SaaS access, rebrand dari panel | Menit |
| **Vendasta** | Partner marketplace, onboarding guided | Hari |
| **Simvoly** | Domain + logo swap + custom CSS | Jam |

### Learning:
- White-label buyer utama: agency yang ingin tambah revenue stream
- Pain: setup teknis sulit → butuh guided onboarding
- Trust: butuh demo/sandbox sebelum beli
- Delivery via email + documentation = acceptable untuk market Indonesia
- GitHub access = value add untuk developer-oriented buyers

### Adoptasi untuk Tiranyx:
```
✅ Demo/sandbox tersedia sebelum beli
✅ Setup guide (dokumentasi) dikirim via email
✅ Optional: scheduled setup call (via WA/Meet)
✅ GitHub: invite ke private repo setelah bayar (automated)
✅ License key untuk tracking usage
✅ Support 30 hari pasca-setup included dalam bundle
```

---

## 10. BENCHMARK LOKAL — MEKARI ECOSYSTEM

Mekari adalah referensi utama platform SaaS Indonesia yang berhasil scale.

### Kenapa Mekari berhasil:
1. **Bundle ecosystem** — Jurnal (accounting) + Talenta (HR) + Klikpajak (pajak) = cross-sell natural
2. **Enterprise-grade trust** — ISO certified, data lokal, support Bahasa Indonesia
3. **Partner program** — reseller network yang kuat (seperti yang ingin Tiranyx bangun)
4. **Freemium entry** — trial tanpa CC, onboarding mudah
5. **Annual billing dominance** — cash flow lebih predictable

### Gap yang Mekari tidak cover (peluang Tiranyx):
- Tidak ada AI-native tools (marketing, konten)
- Tidak ada social media/content tools
- Tidak ada e-commerce/marketplace integration
- Tidak ada agency service component
- Price point masih tinggi untuk UMKM kecil

### Positioning Tiranyx vs Mekari:
```
Mekari: Accounting + HR + Tax → finance-focused, medium-large business
Tiranyx: Marketing + Content + AI → growth-focused, UMKM to SME
```
**Tidak kompetitif langsung** — complementary untuk banyak bisnis.

---

## SUMMARY: ADOPTASI PRIORITAS

| # | Insight | Sumber | Status |
|---|---------|--------|--------|
| 1 | Dashboard per produk aktif (card-based) | Niagahoster | TODO ITER 1 |
| 2 | Quota bar visible dalam tool | Canva | TODO ITER 2 |
| 3 | Contextual upsell saat limit habis | Grammarly | TODO ITER 2 |
| 4 | Simple client portal (5 status) | Basecamp | TODO ITER 4 |
| 5 | Brief form max 5 field | Fiverr | TODO ITER 3 |
| 6 | Midtrans Snap popup (tidak redirect) | Tokopedia | TODO ITER 1 |
| 7 | Annual discount 2 bulan gratis | Buffer | TODO ITER 1 |
| 8 | Tool page = SEO entry point | HubSpot | IN PROGRESS |
| 9 | Waitlist FOMO + GitHub link | Product Hunt | TODO FASE 1 |
| 10 | White-label + 30-day support | GoHighLevel | TODO ITER 6 |

---

*Benchmark ini perlu di-update setiap Q (quarterly) karena landscape berubah cepat.*
*Sumber riset: Product Hunt, IndieHackers, G2, Producthunt, komunitas developer Indonesia, pengalaman langsung*
