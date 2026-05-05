# TIRANYX — ALUR TRANSAKSI
**Untuk keperluan:** Pendaftaran Midtrans Payment Gateway
**Merchant:** Tiranyx Digital Indonesia
**Website:** https://tiranyx.co.id
**Tanggal:** 2026-05-05

---

## DIAGRAM ALUR TRANSAKSI UTAMA

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    ALUR TRANSAKSI TIRANYX.CO.ID                          │
│                                                                          │
│  [CUSTOMER]         [WEBSITE]          [MIDTRANS]       [TIRANYX TIM]   │
│                                                                          │
│  1. Kunjungi        2. Browse          .                .                │
│     website ──────► produk/tools       .                .                │
│                                                                          │
│  3. Klik "Beli"    4. Login/Register   .                .                │
│     atau            atau guest          .                .                │
│     "Upgrade" ────► checkout            .                .                │
│                                                                          │
│  5. Review order   6. Buat order       .                .                │
│     (nama, email,   + hitung total     .                .                │
│     produk, harga)  POST /api/         .                .                │
│                     orders/create      .                .                │
│                         │                                                │
│                     7. Midtrans ───────► 8. Return                      │
│                        createTransaction   snap_token                   │
│                                            ◄───────────                 │
│                                                                          │
│  9. Popup payment  10. Midtrans         .                .               │
│     Midtrans Snap  popup muncul         .                .               │
│     ◄──────────────────────────────────                                  │
│                                                                          │
│  10. Pilih metode  .                   11. User isi      .               │
│      bayar:        .                       data +        .               │
│      - CC/Debit    .                       bayar         .               │
│      - GoPay       .                       ──────────────►               │
│      - QRIS        .                       12. Proses     .              │
│      - VA          .                           pembayaran .              │
│      - Alfamart    .                           ◄──────────               │
│                                                                          │
│  13. Konfirmasi    14. Midtrans ───────────────────────────────────►    │
│      pembayaran         kirim webhook     (notification)                │
│      ◄──────────        ke /api/payment/                                │
│                         midtrans/notification                            │
│                                   │                                      │
│                     15. Verifikasi +                    16. Notif       │
│                         Update status order ───────────► admin          │
│                         Activate purchase               (email/WA)      │
│                         Trigger delivery                                 │
│                                   │                                      │
│  17. Email ◄────────── 18. Kirim email                  .               │
│      konfirmasi         konfirmasi +                     .               │
│      + instruksi        delivery                         .               │
│      akses              (akses web / file / GitHub)      .               │
│                                                                          │
│  19. Akses         20. Login dashboard .                .                │
│      produk ──────► /dashboard →        .                .               │
│                      tools/orders/       .                .               │
│                      subscriptions       .                .               │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## DETAIL ALUR PER SKENARIO

### SKENARIO 1: Beli Tools Pro Subscription

**Jenis Transaksi:** Recurring subscription bulanan
**Produk:** Tiranyx Tools Pro — Rp 99.000/bulan

```
Step 1: Customer mengunjungi tiranyx.co.id/tools
Step 2: Menggunakan tool gratis (quota habis atau ingin upgrade)
Step 3: Klik "Upgrade Pro" / "Lihat Paket"
Step 4: Tampil halaman /pricing atau modal pricing
Step 5: Klik "Beli Sekarang — Rp 99.000/bulan"
Step 6: Jika belum login → redirect ke /register atau /login
Step 7: Setelah login → halaman review order
         [Tools Pro — Rp 99.000/bulan | Billing: Bulanan]
         [Data customer: nama, email]
         [Tombol: Konfirmasi & Bayar]
Step 8: Klik "Konfirmasi & Bayar"
         → POST /api/orders/create
         → Server buat order di DB (status: pending)
         → Midtrans createTransaction(order_id, amount, customer)
         → Return: snap_token
Step 9: Frontend: window.snap.pay(snap_token)
         → Muncul popup Midtrans Snap
Step 10: Customer pilih metode bayar (GoPay/CC/VA/dll)
Step 11: Customer selesaikan pembayaran
Step 12: Midtrans memproses pembayaran
Step 13: Midtrans kirim webhook ke:
         POST https://tiranyx.co.id/api/payment/midtrans/notification
         Body: { order_id, transaction_status: "settlement", ... }
Step 14: Server verifikasi signature Midtrans
Step 15: Update order status → "paid"
Step 16: Buat/activate user_purchases record (status: active, expires: +30 hari)
Step 17: Kirim email konfirmasi ke customer
Step 18: Customer redirect ke /dashboard
Step 19: Di /dashboard/tools → semua tools unlock (Pro aktif)
Step 20: Di /dashboard/subscriptions → tampil: "Tools Pro — Aktif hingga [tanggal]"
```

**Durasi total:** < 5 menit dari klik sampai akses aktif

---

### SKENARIO 2: Pesan On-Demand Service

**Jenis Transaksi:** One-time payment
**Produk:** Market Research Report — Rp 3.000.000 – 15.000.000

```
Step 1: Customer mengunjungi tiranyx.co.id/services atau /products/market-intel
Step 2: Klik "Pesan Sekarang" / "Order Service"
Step 3: Tampil halaman detail layanan + brief form
Step 4: Customer isi brief form:
         - Industri yang ingin diriset
         - Kompetitor yang ingin dianalisis
         - Target audience
         - Scope / tujuan riset
         - Timeline preferensi
Step 5: Klik "Lanjut ke Pembayaran"
Step 6: Jika belum login → /register atau /login
Step 7: Halaman konfirmasi order:
         [Market Research Report]
         [Timeline: 5–7 hari kerja]
         [Harga: Rp 3.000.000] ← fixed untuk scope yang dipilih
         [Brief summary]
         [Tombol: Bayar & Konfirmasi Order]
Step 8: Klik → POST /api/orders/service/create
         → Buat service_order di DB (status: submitted)
         → Midtrans createTransaction
         → Return snap_token
Step 9: Popup Midtrans Snap
Step 10: Customer bayar
Step 11: Midtrans webhook → /api/payment/midtrans/notification
Step 12: Update order status → "review"
Step 13: Notifikasi ke tim Tiranyx (email + WA)
         "Order baru masuk: Market Research Report | Rp 3.000.000"
Step 14: Customer terima email konfirmasi:
         "Pesanan Anda diterima. Tim kami akan review dalam 1 x 24 jam."
Step 15: Customer bisa track di /dashboard/services:
         Status: Submitted → Review → In Progress → Revision → Done
Step 16: Tim Tiranyx assign ke team member di /admin/orders
Step 17: Status update → "in_progress" → notif email ke customer
Step 18: Tim upload deliverable di /admin/orders/[id]
Step 19: Status → "done" → email ke customer: "Laporan siap diunduh"
Step 20: Customer download dari /dashboard/orders atau /dashboard/downloads
```

**Durasi total:** Pembayaran instan → delivery 5-7 hari kerja

---

### SKENARIO 3: Beli SaaS White-label Bundle

**Jenis Transaksi:** One-time setup fee + recurring license
**Produk:** SOCIYX White-label — Rp 5.000.000 setup + Rp 2.000.000/bulan

```
Step 1: Customer kunjungi tiranyx.co.id/saas/sociostudio
Step 2: Klik "Beli Bundle"
Step 3: Tampil halaman konfirmasi:
         [SOCIYX White-label]
         [Setup fee: Rp 5.000.000 (satu kali)]
         [License: Rp 2.000.000/bulan mulai bulan ke-2]
         [Termasuk: setup + onboarding + support 30 hari]
         [Form: nama, email, domain target, nama brand]
Step 4: Customer isi form + klik "Bayar Setup Fee"
Step 5: Midtrans Snap → bayar Rp 5.000.000
Step 6: Payment confirmed → webhook
Step 7: Update order: status "paid"
Step 8: Trigger delivery automation:
         - GitHub API: invite customer ke private repo sociyx
         - Email: sambutan + link setup guide + credentials
         - Notif admin untuk onboarding call
Step 9: Customer terima email dalam 5 menit:
         - Link GitHub repository (private)
         - Setup documentation
         - Jadwal onboarding call
Step 10: Akses /dashboard/downloads:
          - GitHub link aktif
          - Setup guide PDF
          - License key
Step 11: Bulan ke-2 dst: sistem tagih Rp 2.000.000/bulan otomatis
```

---

### SKENARIO 4: Client Invoice Payment

**Jenis Transaksi:** B2B Invoice payment
**Payer:** Klien agency Tiranyx (account type: client)

```
Step 1: Tim Tiranyx selesaikan project milestone
Step 2: Admin buat invoice di /admin → /client/invoices
         [Invoice #INV-2026-XXXXX]
         [Project: [nama project]]
         [Amount: Rp X.XXX.XXX]
         [Due date: 14 hari]
Step 3: Sistem kirim email invoice ke client
Step 4: Client login ke /client portal
Step 5: Client lihat invoice di /client/invoices
Step 6: Klik "Bayar Sekarang"
Step 7: Midtrans Snap popup
Step 8: Client bayar via CC / Transfer
Step 9: Midtrans webhook → update invoice status → "paid"
Step 10: Kirim email receipt ke client
Step 11: Tim Tiranyx notif bahwa invoice sudah lunas
```

---

## WEBHOOK SPECIFICATION

### Endpoint
```
POST https://tiranyx.co.id/api/payment/midtrans/notification
```

### Verification
```
Signature verification menggunakan SHA-512:
hash = SHA512(order_id + status_code + gross_amount + server_key)
Bandingkan dengan body.signature_key
```

### Status yang diproses
| transaction_status | Aksi |
|-------------------|------|
| `settlement` | Payment sukses → activate purchase |
| `capture` | CC authorized → activate purchase |
| `pending` | Menunggu bayar → tidak ada aksi |
| `deny` | Ditolak → update order failed |
| `cancel` | Dibatalkan → update order cancelled |
| `expire` | Expired → update order expired |

---

## REFUND POLICY

| Jenis Produk | Refund Policy |
|-------------|---------------|
| Tools Pro (bulanan) | Tidak ada refund, tapi bisa cancel sebelum billing berikutnya |
| On-demand Service | Refund 100% jika belum dimulai; 50% jika in-progress |
| White-label Setup Fee | Tidak ada refund setelah delivery |
| White-label License | Tidak ada refund, bisa cancel sebelum billing berikutnya |

---

## CATATAN TEKNIS UNTUK MIDTRANS

1. **Notification URL:** `https://tiranyx.co.id/api/payment/midtrans/notification`
2. **Finish URL:** `https://tiranyx.co.id/dashboard/orders?status=success`
3. **Error URL:** `https://tiranyx.co.id/dashboard/orders?status=error`
4. **Unfinish URL:** `https://tiranyx.co.id/dashboard/orders?status=pending`
5. **Order ID format:** `TRX-{YYYYMMDD}-{6random}` (contoh: TRX-20260505-XK9M2P)
6. **Currency:** IDR
7. **Idempotency:** Order ID unique per transaksi, duplicate webhook diabaikan

---

*Dokumen ini menggambarkan alur transaksi yang akan diimplementasikan di tiranyx.co.id.*
*Untuk pertanyaan: info@tiranyx.co.id | WA: +62 821 1359 0953*
