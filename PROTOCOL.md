# TIRANYX — MANDATORY AGENT EXECUTION PROTOCOL
**Version:** 1.0.0 | **Date:** 2026-05-05 | **Status:** ENFORCED — semua agent WAJIB ikuti
**Berlaku untuk:** Claude Code, Cursor, semua AI agent yang bekerja di ekosistem Tiranyx

---

## ⚠️ BACA INI SEBELUM APAPUN

Dokumen ini adalah **protokol operasional wajib**. Tidak ada agent yang boleh mulai eksekusi sebelum melewati tahap-tahap di bawah. Protokol ini ada karena:

- Konteks hilang di tengah sesi menyebabkan pekerjaan diulang
- Diskusi dan arahan "menguap" dan harus dijelaskan ulang
- Agent bekerja tanpa riset cukup → hasil tidak optimal
- Tidak ada jejak yang bisa dilanjutkan oleh agent berikutnya

**Efek buruk yang dicegah:**
- ❌ Agent ngulang yang sudah dikerjakan
- ❌ Agent kehilangan arah dari visi/tujuan
- ❌ Agent eksekusi tanpa riset → hasil generik
- ❌ Tidak ada log → agent berikutnya mulai dari nol
- ❌ Tidak ada evaluasi → kegagalan diulang, keberhasilan tidak dilipatgandakan

---

## FASE 0 — ONBOARDING (setiap sesi baru)

### 0.1 Baca Dokumen Wajib (urutan ini)

| # | File | Isi | Maks waktu |
|---|------|-----|-----------|
| 1 | `PROTOCOL.md` (ini) | Cara kerja | 2 menit |
| 2 | `VISION.md` | Visi, misi, positioning | 3 menit |
| 3 | `RESEARCH.md` | Temuan riset ekosistem | 3 menit |
| 4 | `ROADMAP.md` | Fase + milestone saat ini | 2 menit |
| 5 | `SPRINT-LOG.md` | Sprint aktif + task backlog | 2 menit |
| 6 | `JOURNAL.md` | Entri terbaru (3 entri terakhir) | 2 menit |
| 7 | `ARCHITECTURE.md` | Stack + struktur teknis | 2 menit |

**Total onboarding: ~16 menit maksimum**

Setelah baca semua, **tulis summary 3 kalimat** tentang konteks sekarang sebelum mulai kerja.

### 0.2 Cek Status Terkini

```bash
# Di tiranyx-web repo (lokal atau VPS)
git log --oneline -10          # 10 commit terakhir
git status                      # ada file uncommitted?
pm2 list                        # (di VPS) proses yang jalan

# Cek task yang in_progress di SPRINT-LOG.md
# Pastikan tidak ada task abandoned
```

### 0.3 Konfirmasi Konteks dengan User (jika sesi baru)

Sebelum mulai, tulis ke user:
```
Konteks saya: [1 kalimat tentang sprint aktif]
Task terakhir: [apa yang terakhir dikerjakan]
Akan mulai dengan: [task berikutnya]
Ada yang perlu saya ketahui sebelum mulai?
```

---

## FASE 1 — PRE-EXECUTION: RISET & ANALISIS

**Berlaku untuk setiap task non-trivial (lebih dari 30 menit kerja)**

### 1.1 Kumpulkan Riset

Sebelum eksekusi, wajib riset dari minimal **3 sumber berbeda**:

| Kategori | Sumber yang diprioritaskan |
|----------|---------------------------|
| Teknologi terbaru | GitHub (trending, stars tinggi), arXiv, dev.to, HackerNews |
| Best practice industri | Stripe docs, Vercel blog, Next.js docs, Tailwind docs |
| Kompetitor | Product Hunt, IndieHackers, G2, Producthunt |
| Indonesia-specific | Mekari blog, Jurnal.id, komunitas developer Indonesia |
| User behavior | Reddit (r/webdev, r/entrepreneur), Twitter/X tech threads |

**Format riset (catat di JOURNAL.md):**
```markdown
## Riset: [Nama Task]
**Tanggal:** YYYY-MM-DD
**Sources:**
- [URL/referensi 1]: [Insight kunci]
- [URL/referensi 2]: [Insight kunci]
- [URL/referensi 3]: [Insight kunci]

**Insight utama:** [Apa yang paling relevan untuk task ini]
**Yang tidak kita lakukan:** [Trade-off yang disadari]
```

### 1.2 Benchmark

Untuk setiap fitur baru, bandingkan dengan minimal 3 platform yang sudah matang:
- Bagaimana mereka solve problem yang sama?
- Apa yang bagus dari pendekatan mereka?
- Apa yang buruk / kita bisa lakukan lebih baik?
- Berapa harga / business model mereka?

**Catat di BENCHMARK.md** atau di entri JOURNAL.md yang relevan.

### 1.3 Definisikan Objektif Task

Sebelum kode ditulis, definisikan:

```markdown
### Task: [Nama Task]

**Objective:** [Apa yang ingin dicapai dalam 1 kalimat]

**Success Criteria (measurable):**
- [ ] [Kriteria 1 yang bisa diukur]
- [ ] [Kriteria 2 yang bisa diukur]
- [ ] [Kriteria 3 yang bisa diukur]

**Indicator (bagaimana tahu berhasil):**
- Metrik: [apa yang diukur]
- Target: [angka/threshold]
- Cara ukur: [bagaimana mengukurnya]

**Risks:**
- [Risk 1]: [Likelihood] | [Impact] | [Mitigasi]
- [Risk 2]: [Likelihood] | [Impact] | [Mitigasi]

**Dependencies:**
- [Apa yang harus selesai dulu]
```

### 1.4 Hipotesis & Rencana Adaptasi

Untuk keputusan arsitektur atau UX yang tidak pasti:

```markdown
**Hipotesis:** [Asumsi kita tentang apa yang akan berhasil]
**Asumsi dasar:** [Kenapa kita pikir ini benar]
**Cara validasi:** [Bagaimana kita tahu hipotesis benar/salah]
**Jika benar:** [Lanjutkan dan lipat gandakan]
**Jika salah:** [Rencana pivot/adaptasi]
```

---

## FASE 2 — EXECUTION

### 2.1 Task Breakdown

Setiap task dibagi menjadi **sub-task 2–4 jam** maksimum. Di SPRINT-LOG.md:
```markdown
- [ ] Sub-task 1 (est: 2j)
- [ ] Sub-task 2 (est: 3j)
- [ ] Sub-task 3 (est: 1j)
```

### 2.2 Selama Eksekusi

- Update status sub-task di SPRINT-LOG.md **langsung** selesai
- Jika menemukan blocker: catat di JOURNAL.md, jangan diam
- Jika ada keputusan teknis penting: catat rationale di JOURNAL.md
- **JANGAN tinggalkan kode setengah jadi tanpa komentar `// TODO:` + penjelasan**

### 2.3 Anti-Pattern yang Dilarang

| ❌ Jangan | ✅ Gantinya |
|-----------|------------|
| Ubah design sistem tanpa izin | Gunakan komponen yang ada, extend jika perlu |
| Buat file baru tanpa cek yang sudah ada | `grep` dulu, edit file existing |
| Hardcode credentials atau API keys | Selalu env variable |
| Skip error handling | Handle semua error, log dengan context |
| Deploy tanpa test | Minimal manual test golden path |
| Commit tanpa message yang jelas | Conventional commits: feat/fix/chore/docs |
| Buka fitur baru sebelum yang lama selesai | Finish what you started |
| Asumsikan tanpa verifikasi | Cek VPS, cek DB, cek live site |

### 2.4 Code Standards

```typescript
// Setiap fungsi publik: tipe yang jelas
export async function createOrder(
  userId: number,
  productSlug: string,
  planName: string
): Promise<{ orderId: string; snapToken: string }> { ... }

// Error handling: jangan swallow errors
try {
  await processPayment(order)
} catch (error) {
  logger.error('Payment processing failed', { orderId: order.id, error })
  throw new PaymentError('Failed to process payment', { cause: error })
}

// Database: selalu gunakan prepared statements (SQLite / better-sqlite3)
const stmt = db.prepare('SELECT * FROM users WHERE email = ?')
const user = stmt.get(email)
```

---

## FASE 3 — POST-EXECUTION: VALIDASI & REVIEW

### 3.1 Self-Review Checklist

Sebelum declare task selesai:

```
FUNCTIONAL:
[ ] Semua success criteria terpenuhi
[ ] Golden path tested (manual atau automated)
[ ] Edge cases dipertimbangkan
[ ] Error states handled dengan baik
[ ] Mobile responsive (jika UI)

TEKNIS:
[ ] Tidak ada console.error yang tidak ditangani
[ ] Tidak ada hardcoded values yang seharusnya env
[ ] Tidak ada kode yang di-comment out tanpa alasan
[ ] Import yang tidak dipakai dihapus
[ ] TypeScript: tidak ada `any` tanpa alasan

SEO (jika halaman baru):
[ ] <title> unik dan deskriptif
[ ] <meta description> ada dan relevan
[ ] Open Graph image ada
[ ] Schema.org markup sesuai halaman
[ ] Sitemap akan include halaman baru

DEPLOY:
[ ] build berhasil (npm run build)
[ ] Tidak ada breaking changes untuk halaman existing
[ ] Env variables terdokumentasi di .env.example
```

### 3.2 Catat Hasil

Di JOURNAL.md setelah setiap task selesai:

```markdown
## [Tanggal] — Task: [Nama]

**Status:** ✅ Selesai / ⚠️ Partial / ❌ Gagal

**Yang dilakukan:**
- [Aksi 1]
- [Aksi 2]

**Hasil:**
- [Hasil terukur 1]
- [Hasil terukur 2]

**Temuan/Learning:**
- [Apa yang dipelajari]
- [Apa yang akan dilakukan berbeda]

**Keputusan teknis:**
- [Keputusan A]: [Alasan]
- [Keputusan B]: [Alasan]

**Next action:**
- [Task berikutnya yang jelas]
```

### 3.3 Evaluasi Framework

Untuk setiap fitur/task yang selesai, evaluasi:

| Dimensi | Pertanyaan | Rating (1–5) |
|---------|-----------|--------------|
| **Dampak** | Seberapa besar pengaruhnya ke user/bisnis? | — |
| **Manfaat** | Apakah ini solve real problem? | — |
| **Risiko** | Apakah ada risiko yang muncul? | — |
| **Skalabilitas** | Bisa scale tanpa refactor besar? | — |
| **Maintainability** | Mudah diubah oleh agent berikutnya? | — |

**Jika rating < 3 di 2+ dimensi:** buat issue untuk refactor di sprint berikutnya.

---

## FASE 4 — HANDOFF & CONTINUITY

### 4.1 Setiap Akhir Sesi

Wajib tulis di JOURNAL.md:
```markdown
## HANDOFF — [Tanggal] [Waktu]

**Sesi ini mengerjakan:**
- [Task 1]: [Status]
- [Task 2]: [Status]

**State saat ini:**
- Branch: [nama branch]
- Build status: [pass/fail]
- VPS: [sync/perlu deploy]

**Context penting:**
- [Hal yang perlu diketahui agent berikutnya]
- [Keputusan yang sedang pending]

**Next task (prioritas):**
1. [Task paling urgent]
2. [Task berikutnya]

**File yang diubah:**
- [file 1]: [apa yang berubah]
- [file 2]: [apa yang berubah]
```

### 4.2 Naming Convention

```
Branch: feat/dashboard-personal, fix/midtrans-webhook, docs/prd-update
Commit: feat(auth): add user registration endpoint
        fix(tools): enforce daily quota for free users
        docs(prd): update product pricing section
        chore(db): add user_purchases migration
```

### 4.3 Jangan Tinggalkan

- ❌ Migration yang belum dijalankan di VPS tanpa catatan
- ❌ Env variable baru tanpa dokumentasi di .env.example
- ❌ TODO comment tanpa issue/sprint entry
- ❌ Dead code yang belum dihapus
- ❌ Build yang fail
- ❌ Test yang merah

---

## PRINSIP KRITIS (Hafal Ini)

```
1. RISET DULU, KODE KEMUDIAN — jangan asumsi, verifikasi
2. CATAT SEGALANYA — konteks yang tidak dicatat = hilang selamanya
3. SATU TASK SAMPAI SELESAI — jangan buka task baru sebelum yang lama done
4. GAGAL = PELAJARAN, BERHASIL = TEMPLATE — keduanya harus dicatat
5. VISI > FITUR — setiap task harus align dengan VISION.md
6. SIMPLE DULU, SCALE NANTI — MVP yang berjalan > rancangan sempurna yang belum jadi
7. UKUR SEMUA — jika tidak bisa diukur, tidak bisa ditingkatkan
8. USER PERTAMA — setiap keputusan UX/teknis: "apakah ini mudah untuk user?"
```

---

## FILE REFERENSI CEPAT

| Yang perlu | File |
|-----------|------|
| Visi & positioning | `VISION.md` |
| Product requirements | `PRD.md` |
| Database schema | `ERD.md` |
| Competitive landscape | `BENCHMARK.md` |
| Tech stack & architecture | `ARCHITECTURE.md` |
| Phased roadmap | `ROADMAP.md` |
| Sprint aktif & backlog | `SPRINT-LOG.md` |
| Dev journal & findings | `JOURNAL.md` |
| Ecosystem research | `RESEARCH.md` |
| Midtrans requirements | `midtrans/PRODUCT-LIST.md` |
| Transaction flow | `midtrans/TRANSACTION-FLOW.md` |
| SSH ke VPS | `ssh sidix-vps` (alias ke 72.62.125.6) |
| Deploy ke VPS | `deploy.ps1` → tar.gz → upload → extract + build + pm2 restart tiranyx |

---

*Protokol ini diperbarui setiap kali ada pola baru yang ditemukan — baik kegagalan maupun keberhasilan.*
*Owner: Fahmi Ghani | Enforced by: semua agent AI yang bekerja di ekosistem Tiranyx*
