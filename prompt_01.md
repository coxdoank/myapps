# PROMPT UTAMA — BENGKELPRO

## Aplikasi PWA Manajemen Operasional Bengkel, POS, Work Order, dan Inventaris Multi-Tenant

### 1. PERAN DAN TUJUAN

Bertindaklah sebagai **Senior Full-Stack Engineer, Software Architect, UI/UX Designer, dan QA Engineer** yang berpengalaman membangun aplikasi bisnis berbasis web, Progressive Web Application (PWA), sistem Point of Sale (POS), inventaris, dan aplikasi multi-tenant.

Bangun aplikasi bernama **BengkelPro**, yaitu sistem manajemen operasional bengkel kendaraan yang mengintegrasikan:

* Dashboard operasional bengkel.
* Penerimaan kendaraan dan Work Order (WO).
* Pengerjaan dan pembaruan status servis.
* Kasir POS, invoice, pembayaran, DP, dan pelunasan.
* Manajemen pelanggan dan kendaraan.
* Katalog jasa servis dan sparepart.
* Inventaris, mutasi stok, dan peringatan stok menipis.
* Laporan pendapatan, HPP, laba kotor, dan komisi mekanik.
* Multi-user, multi-role, dan multi-bengkel (multi-tenant).
* PWA yang dapat diinstal, mendukung penggunaan offline, dan dapat dikembangkan untuk sinkronisasi dengan server.

Prioritas pengembangan adalah **fungsi yang benar, konsistensi transaksi, kemudahan penggunaan, keamanan data, dan kestabilan aplikasi**, baru kemudian penyempurnaan visual.

Jangan membuat dashboard statis yang hanya terlihat seperti aplikasi. Setiap tombol, formulir, filter, kalkulasi, dan alur transaksi yang ditampilkan harus benar-benar bekerja.

---

### 2. STRATEGI ARSITEKTUR DAN TEKNOLOGI

Gunakan arsitektur modular agar kode mudah dirawat dan dikembangkan.

**Teknologi yang disarankan:**

* Frontend: Vue 3 + TypeScript + Vite.
* UI: Tailwind CSS.
* Routing: Vue Router.
* State management: Pinia.
* Database lokal: IndexedDB melalui Dexie.js.
* PWA: Vite PWA Plugin atau solusi setara yang kompatibel.
* Validasi data: Zod atau validasi skema setara.
* Pengujian: Vitest dan Playwright atau perangkat pengujian setara.

Gunakan versi stabil dan kompatibel. Jangan mencampur Vue, React, dan Alpine.js dalam satu implementasi tanpa alasan teknis yang kuat.

**Ketentuan arsitektur:**

1. Pisahkan UI, komponen, halaman, domain bisnis, layanan data, validasi, dan konfigurasi.
2. Gunakan TypeScript dengan tipe yang jelas.
3. Hindari satu file HTML raksasa untuk seluruh aplikasi.
4. Jangan menggunakan API atau fungsi semu yang seolah-olah sudah terhubung ke backend.
5. Hindari ketergantungan pada layanan eksternal yang tidak diperlukan saat aplikasi offline.
6. Pastikan aplikasi bisa dijalankan dengan instruksi instalasi dan perintah yang jelas.
7. Jika lingkungan hanya mendukung satu file HTML, jelaskan keterbatasan PWA, keamanan, dan sinkronisasinya. Jangan mengklaim kemampuan yang tidak benar-benar tersedia.

#### Dua mode implementasi

**Mode A — Demo lokal/MVP**

* Database menggunakan IndexedDB.
* Akun dan data demo tersedia secara lokal.
* Isolasi tenant diterapkan pada seluruh operasi data.
* Dapat digunakan tanpa backend.
* Cocok untuk prototipe, pengujian alur, dan demonstrasi.

**Mode B — Produksi**

* Menggunakan backend dan database server.
* Autentikasi dan otorisasi divalidasi di server.
* Setiap permintaan memeriksa tenant dan role pengguna.
* Transaksi pembayaran dan perubahan stok dijalankan secara atomik di server.
* Sinkronisasi offline menggunakan mekanisme antrean dan rekonsiliasi.

Mulai dengan **Mode A sebagai MVP yang benar-benar dapat dijalankan**, tetapi rancang struktur domain agar dapat dipindahkan ke Mode B tanpa menulis ulang seluruh aturan bisnis.

Jangan menyebut Mode A aman untuk penggunaan multi-tenant produksi. Isolasi berbasis browser merupakan isolasi fungsional untuk demo, bukan batas keamanan yang dapat dipercaya terhadap pengguna yang memodifikasi aplikasi.

---

### 3. DESAIN UI/UX — INDUSTRIAL WORKSHOP

Gunakan desain industrial yang tegas, profesional, kontras tinggi, dan mudah digunakan oleh pemilik bengkel, kasir, serta mekanik.

#### Palet warna

* Latar utama: Asphalt Dark `#0F172A`.
* Panel: Charcoal `#1E293B`.
* Border: Slate `#334155`.
* Teks utama: `#F8FAFC`.
* Teks sekunder: `#94A3B8`.
* Status antrean: Electric Blue `#3B82F6`.
* Sedang dikerjakan: Warning Amber `#F59E0B`.
* Menunggu sparepart: Purple Violet `#8B5CF6`.
* Selesai/siap diambil: Cyber Emerald `#10B981`.
* Batal/terlambat: Neon Crimson `#EF4444`.

Jangan hanya mengandalkan warna untuk menunjukkan status. Sertakan label teks dan, jika sesuai, ikon.

#### Tata letak

**Mobile-first, mulai dari lebar 390 px:**

* Navigasi bawah untuk halaman utama.
* Tombol utama minimal setinggi 48 px.
* Formulir singkat dan mudah digunakan.
* Nomor WO, nomor antrean, dan total transaksi menggunakan angka yang mudah dibaca.
* Tombol tindakan penting tidak boleh terlalu rapat.
* Hindari tabel lebar yang merusak tampilan HP; gunakan kartu atau tabel responsif.

**Desktop/tablet:**

* Sidebar navigasi.
* Dashboard dengan ringkasan operasional.
* Kanban atau daftar WO yang mudah dipantau.
* Tabel inventaris dan riwayat dengan pencarian, pengurutan, serta filter.

Sediakan empty state, loading state, pesan error, konfirmasi tindakan berisiko, dan feedback keberhasilan yang jelas.

---

### 4. AUTENTIKASI, PENGGUNA, ROLE, DAN TENANT

#### 4.1 Model pengguna

Dukung entitas berikut:

* Bengkel/tenant.
* Pengguna.
* Keanggotaan pengguna pada bengkel.
* Role dan izin akses.

Satu pengguna dapat memiliki akses ke lebih dari satu bengkel apabila keanggotaan tersebut dikonfigurasi.

Sediakan fitur:

* Login dan logout.
* Registrasi bengkel pada mode demo.
* Pemilihan bengkel aktif jika pengguna memiliki lebih dari satu keanggotaan.
* Pergantian akun.
* Tampilan profil dan role aktif.
* Penanganan session yang jelas.

Jangan menyimpan password asli dalam LocalStorage atau IndexedDB. Untuk demo, gunakan akun demonstrasi yang jelas diberi label dan mekanisme login lokal yang tidak diklaim aman untuk produksi. Pada produksi, gunakan autentikasi backend dan penyimpanan password dengan hash yang sesuai standar.

#### 4.2 Role dan hak akses

**Owner/Admin**

* Mengelola profil bengkel dan pengguna.
* Melihat seluruh WO, pelanggan, kendaraan, dan inventaris dalam tenant yang diizinkan.
* Mengatur harga jual, harga pokok, diskon, dan komisi.
* Mengelola pembayaran, pembatalan, serta penyesuaian stok sesuai izin.
* Melihat laporan keuangan.
* Melakukan backup dan pemulihan data.

**Kasir**

* Membuat dan memperbarui WO sesuai izin.
* Membuat invoice dan mencatat pembayaran.
* Melihat pelanggan, kendaraan, jasa, dan sparepart.
* Tidak boleh mengakses pengaturan keamanan atau data tenant lain.
* Tidak boleh mengubah transaksi yang telah dikunci tanpa prosedur koreksi yang tercatat.

**Mekanik**

* Melihat WO yang ditugaskan kepadanya atau yang diizinkan oleh kebijakan bengkel.
* Melihat keluhan pelanggan dan detail pekerjaan yang relevan.
* Memperbarui status pengerjaan.
* Menambahkan catatan hasil pemeriksaan dan pengerjaan.
* Tidak boleh melihat laporan laba, mengubah harga pokok, atau melakukan penyesuaian stok tanpa izin.

Semua izin harus diperiksa pada lapisan logika bisnis. Menyembunyikan tombol di frontend saja tidak cukup.

#### 4.3 Isolasi multi-tenant

Setiap data operasional wajib terhubung dengan `tenant_id` atau `bengkel_id` yang konsisten.

Cakup minimal:

* Pengguna dan keanggotaan.
* Pelanggan dan kendaraan.
* Work Order.
* Item jasa dan sparepart.
* Pembayaran dan invoice.
* Inventaris dan mutasi stok.
* Komisi mekanik.
* Laporan dan catatan audit.

Terapkan ketentuan berikut:

1. Setiap query dan operasi tulis harus memeriksa tenant aktif.
2. Jangan menerima `tenant_id` dari formulir sebagai bukti otorisasi.
3. Tenant ditentukan dari konteks keanggotaan aktif yang tervalidasi.
4. Tolak referensi silang tenant, termasuk ID pelanggan, WO, barang, dan pembayaran.
5. Pastikan pergantian tenant tidak meninggalkan data tenant sebelumnya di layar atau state aplikasi.
6. Sediakan pengujian otomatis untuk membuktikan isolasi data.
7. Untuk produksi, wajibkan validasi di backend dan gunakan kebijakan keamanan database yang sesuai.

---

### 5. DASHBOARD DAN WORK ORDER

#### 5.1 Dashboard operasional

Tampilkan ringkasan:

* Total WO hari ini.
* Kendaraan dalam antrean.
* WO sedang dikerjakan.
* WO menunggu sparepart.
* WO selesai dan siap diambil.
* WO terlambat.
* Pendapatan yang telah dibayar hari ini.
* Peringatan stok menipis.

Sediakan filter tanggal, status, dan mekanik bila relevan.

Angka dashboard harus berasal dari data transaksi, bukan angka statis.

#### 5.2 Penerimaan kendaraan

Form check-in minimal berisi:

**Pelanggan**

* Nama.
* Nomor WhatsApp.
* Catatan opsional.

**Kendaraan**

* Jenis kendaraan.
* Merek dan model.
* Tahun/varian opsional.
* Nomor polisi.
* Odometer masuk.

**Servis**

* Nomor WO otomatis dan unik dalam tenant.
* Tanggal dan waktu masuk.
* Keluhan pelanggan.
* Pemeriksaan awal.
* Mekanik yang ditugaskan.
* Estimasi biaya awal.
* Target waktu selesai opsional.

Sediakan pilihan pelanggan dan kendaraan yang sudah terdaftar, serta opsi menambah data baru tanpa harus berpindah halaman.

Validasi kolom wajib, format nomor telepon, odometer, dan referensi tenant.

#### 5.3 Status Work Order

Gunakan alur status yang eksplisit:

`Diterima → Dikerjakan → Menunggu Sparepart → Dikerjakan → Selesai/Siap Diambil → Diserahkan`

Status tambahan:

* Dibatalkan.
* Ditunda jika dibutuhkan.

Jangan mengizinkan transisi status yang tidak valid tanpa konfirmasi atau aturan khusus.

Setiap perubahan status mencatat waktu, pengguna, status sebelumnya, dan status baru.

Tampilan dapat menggunakan Kanban pada desktop dan daftar/kartu pada mobile.

Sediakan pencarian nomor WO, nomor polisi, nama pelanggan, serta filter status dan mekanik.

#### 5.4 Penyelesaian pekerjaan

Sebelum WO dapat diselesaikan, sistem harus memvalidasi pekerjaan, barang, dan aturan operasional yang diperlukan.

Tampilkan ringkasan pekerjaan, jasa, sparepart, diskon, DP, jumlah terbayar, dan saldo.

Penyelesaian WO tidak boleh otomatis mengubah status pembayaran menjadi lunas jika saldo masih ada.

---

### 6. KASIR POS, INVOICE, DAN PEMBAYARAN

#### 6.1 POS

Sediakan pencarian cepat berdasarkan SKU, kode, nama barang, dan nama jasa.

Kasir dapat menambahkan, menghapus, dan mengubah kuantitas item sebelum transaksi dikonfirmasi.

Setiap baris transaksi menyimpan snapshot:

* Kode dan nama item.
* Kuantitas.
* Harga satuan.
* Diskon baris jika diizinkan.
* Subtotal.

Perubahan harga katalog di kemudian hari tidak boleh mengubah invoice lama.

#### 6.2 Kalkulasi transaksi

Hitung otomatis:

* Subtotal jasa.
* Subtotal sparepart.
* Diskon.
* Pajak jika diaktifkan dan dikonfigurasi.
* Total invoice.
* DP atau pembayaran sebelumnya.
* Pembayaran saat ini.
* Total terbayar.
* Sisa tagihan.

Gunakan rupiah sebagai mata uang awal, format `id-ID`, dan bilangan bulat untuk nominal rupiah kecuali terdapat kebutuhan yang jelas untuk presisi desimal.

Validasi agar diskon tidak melebihi batas yang diizinkan, pembayaran tidak bernilai negatif, dan sisa tagihan tidak salah hitung.

Bedakan dengan jelas **nilai invoice, uang yang diterima, kembalian, total terbayar, dan saldo piutang**.

#### 6.3 Status pembayaran

Dukung status:

* Belum dibayar.
* DP/sebagian dibayar.
* Lunas.
* Dibatalkan atau dikoreksi melalui prosedur yang sesuai.

Sediakan metode pembayaran tunai, transfer, dan QRIS sebagai pencatatan metode. Jangan mengklaim QRIS sudah terintegrasi dengan penyedia pembayaran jika integrasi sebenarnya belum tersedia.

Riwayat pembayaran harus dapat ditelusuri.

#### 6.4 Pengendalian transaksi

* Hindari transaksi ganda akibat klik berulang.
* Gunakan ID transaksi yang unik.
* Terapkan mekanisme idempotensi untuk proses yang dapat diulang.
* Pada produksi, simpan invoice, pembayaran, dan mutasi stok dalam transaksi database yang konsisten.
* Jika terjadi kegagalan, jangan meninggalkan pembayaran tercatat tanpa transaksi terkait atau stok terpotong tanpa dasar.
* Transaksi yang telah difinalisasi tidak boleh dihapus sembarangan; gunakan pembatalan, void, atau koreksi yang tercatat.

#### 6.5 Nota dan struk

Sediakan:

* Tampilan invoice untuk layar.
* Format cetak thermal 58 mm dan 80 mm.
* Format cetak A4 jika diperlukan.
* Nomor invoice, tanggal, identitas bengkel, pelanggan, kendaraan, rincian item, total, pembayaran, dan saldo.
* Tombol cetak melalui dialog cetak browser.

Sediakan tombol WhatsApp yang membuka tautan berbagi dengan teks nota yang sudah disiapkan dan dikodekan dengan benar. Jangan mengklaim nota terkirim otomatis jika pengguna masih harus menekan tombol kirim di WhatsApp.

---

### 7. INVENTARIS DAN PENGENDALIAN STOK

#### 7.1 Master sparepart

Setiap item memiliki:

* SKU/kode barang yang unik per tenant.
* Nama barang.
* Kategori.
* Merek opsional.
* Satuan.
* Harga beli atau biaya perolehan.
* Harga jual.
* Stok tersedia.
* Batas minimum stok.
* Lokasi rak opsional.
* Status aktif/nonaktif.

Sediakan pencarian, filter kategori, pengurutan, serta formulir tambah/edit barang.

#### 7.2 Mutasi stok

Catat setiap perubahan stok dengan jenis dan alasan, misalnya:

* Stok awal.
* Barang masuk/pembelian.
* Pemakaian pada WO.
* Retur.
* Penyesuaian stok.
* Pembatalan transaksi.
* Koreksi karena kesalahan.

Jangan hanya mengubah angka stok tanpa menyimpan mutasinya.

Pada mode lokal, pastikan operasi perubahan stok dijalankan melalui satu layanan domain yang terpusat. Pada produksi, gunakan transaksi atomik di server dan mekanisme pengendalian stok bersamaan.

Jangan biarkan stok negatif kecuali fitur tersebut sengaja diaktifkan dan diberi aturan yang eksplisit.

#### 7.3 Pengurangan dan pengembalian stok

Tentukan satu kebijakan yang konsisten:

* Barang dipesan dalam draft tidak otomatis dianggap terjual.
* Stok final berkurang pada titik transaksi yang ditetapkan dan dijelaskan.
* Jika WO dibatalkan setelah stok digunakan, buat mutasi pembalik sesuai kondisi barang.
* Retur harus tercatat dan tidak boleh menambahkan stok secara otomatis tanpa validasi kondisi barang.
* Pengulangan operasi yang sama tidak boleh mengurangi atau menambah stok dua kali.

Sediakan alert untuk barang dengan stok sama dengan atau di bawah batas minimum.

---

### 8. LAPORAN KEUANGAN DAN KOMISI MEKANIK

Sediakan laporan berdasarkan rentang tanggal dan tenant aktif.

#### 8.1 Laporan

Minimal:

* Pendapatan jasa.
* Pendapatan sparepart.
* Diskon.
* Total invoice.
* Pembayaran yang benar-benar diterima.
* Piutang atau saldo yang belum dibayar.
* HPP barang terjual.
* Laba kotor.
* Jumlah WO.
* Rata-rata nilai invoice.
* Penjualan per kategori atau item.
* Pergerakan stok.

**Jangan menyamakan total invoice dengan kas masuk.** Laporan pendapatan, penerimaan pembayaran, piutang, dan HPP harus memiliki definisi yang jelas.

Untuk MVP, gunakan istilah *laba kotor* jika belum menghitung biaya operasional seperti listrik, sewa, dan gaji tetap. Jangan menyebutnya laba bersih tanpa perhitungan biaya yang lengkap.

#### 8.2 HPP

Simpan harga pokok sebagai snapshot atau referensi biaya yang konsisten pada transaksi.

Tentukan metode perhitungan HPP secara eksplisit, misalnya biaya rata-rata bergerak atau metode lain yang diimplementasikan secara konsisten.

Jika metode HPP belum diterapkan sepenuhnya, tandai laporan sebagai estimasi dan jangan menampilkan angka seolah-olah akuntansi final.

#### 8.3 Komisi mekanik

Dukung aturan komisi yang dapat dikonfigurasi:

* Persentase jasa.
* Nominal tetap per pekerjaan.
* Persentase per kategori jasa jika diperlukan.

Komisi dihitung dari basis yang ditentukan secara eksplisit, bukan otomatis dari seluruh nilai invoice.

Simpan snapshot aturan komisi pada saat transaksi difinalisasi agar perubahan aturan di masa mendatang tidak mengubah transaksi historis.

Tampilkan rekap per mekanik dan periode. Cegah komisi dihitung dua kali untuk transaksi yang sama.

#### 8.4 Ekspor

Sediakan ekspor CSV yang kompatibel dengan Excel untuk:

* Riwayat transaksi.
* Laporan harian dan bulanan.
* Inventaris.
* Mutasi stok.
* Komisi mekanik.

Pastikan format tanggal, mata uang, dan encoding karakter Indonesia konsisten.

---

### 9. PWA DAN MODE OFFLINE

Aplikasi harus memenuhi kebutuhan PWA yang benar-benar diimplementasikan.

Wajib tersedia:

* Web App Manifest yang valid.
* Nama aplikasi dan nama pendek.
* Ikon PWA dengan ukuran yang sesuai.
* Tema dan warna latar.
* Service worker.
* Strategi cache aset aplikasi.
* Halaman fallback offline yang sesuai.
* Petunjuk instalasi pada perangkat yang mendukung.
* Indikator status online/offline.
* Penanganan kegagalan penyimpanan lokal.

Jangan mengandalkan cache untuk menyimpan atau mengirim transaksi seolah-olah transaksi sudah tersinkronisasi.

Untuk Mode A, seluruh data bisnis tersimpan lokal di IndexedDB dan aplikasi dapat digunakan tanpa koneksi setelah aset aplikasi tersedia.

Untuk pengembangan Mode B, rancang antarmuka repository agar penyimpanan lokal dapat dipadukan dengan backend. Jika sinkronisasi offline diterapkan, sertakan:

* Antrean operasi yang belum tersinkronisasi.
* ID operasi unik.
* Retry yang aman dan idempoten.
* Penanda status sinkronisasi.
* Penanganan konflik data.
* Kebijakan saat dua perangkat mengubah stok yang sama.
* Mekanisme agar transaksi offline tidak menyebabkan stok negatif atau transaksi ganda ketika disinkronkan.

Jangan mengklaim sinkronisasi multi-perangkat real-time jika backend dan mekanisme sinkronisasi belum diimplementasikan.

---

### 10. BACKUP, RESTORE, DAN INTEGRITAS DATA

Sediakan fitur:

* Ekspor seluruh data tenant ke JSON.
* Impor dan pemulihan dari JSON.
* Validasi skema dan versi file.
* Pratinjau atau konfirmasi sebelum mengganti data.
* Pesan kesalahan jika file rusak atau format tidak dikenal.
* Peringatan sebelum tindakan yang dapat menghapus data.
* Timestamp backup dan versi skema.

Aturan keamanan:

1. Backup hanya mencakup tenant yang diizinkan.
2. Restore tidak boleh menggabungkan data dari tenant berbeda tanpa proses migrasi yang disengaja.
3. Jangan menerima file backup sebagai data tepercaya tanpa validasi.
4. Sediakan perlindungan terhadap ID duplikat dan referensi yang tidak valid.
5. Lakukan validasi integritas sebelum data dipulihkan.
6. Jangan pernah menghapus data lama sebelum proses validasi dan pemulihan berhasil.
7. Jelaskan bahwa backup lokal perlu disimpan di tempat yang aman.

---

### 11. DATA DEMO AWAL

Saat aplikasi pertama kali dijalankan dalam mode demo, buat dua tenant terpisah.

**Tenant A — Bengkel Mobil Boy**

* Username: `boy`
* Password demo: `123`
* Dua Work Order aktif.
* Lima item sparepart.
* Tiga item jasa.
* Data pelanggan dan kendaraan yang terkait.
* Contoh stok dan batas minimum.
* Contoh riwayat transaksi yang konsisten jika diperlukan.

**Tenant B — Bengkel Speed Auto**

* Username: `speed`
* Password demo: `123`
* Satu Work Order aktif.
* Tiga item sparepart.
* Data pelanggan dan kendaraan yang terkait.
* Contoh stok yang berbeda dari tenant A.

Gunakan data pelanggan dan kendaraan fiktif. Jangan memasukkan data pribadi nyata.

Pastikan seluruh data memiliki relasi yang benar dan setiap tenant mempunyai ID yang berbeda.

#### Uji isolasi wajib

1. Login sebagai `boy` dan pastikan data Bengkel Mobil Boy terlihat.
2. Pastikan sparepart, WO, pelanggan, dan laporan Bengkel Speed Auto tidak muncul.
3. Logout dan login sebagai `speed`.
4. Pastikan hanya data Bengkel Speed Auto yang terlihat.
5. Uji akses menggunakan ID milik tenant lain melalui operasi data aplikasi.
6. Pastikan operasi lintas tenant ditolak atau tidak mengembalikan data.
7. Pastikan pergantian akun tidak meninggalkan data tenant sebelumnya pada dashboard atau state aplikasi.

Akun demo tersebut hanya untuk pengujian lokal dan tidak boleh dipromosikan sebagai kredensial produksi.

---

### 12. STRUKTUR DATA DAN ATURAN DOMAIN

Buat model data yang jelas, minimal meliputi:

* `Tenant`
* `User`
* `Membership`
* `Customer`
* `Vehicle`
* `Mechanic`
* `ServiceCatalog`
* `SparePart`
* `WorkOrder`
* `WorkOrderItem`
* `Invoice`
* `Payment`
* `StockMovement`
* `CommissionRule`
* `MechanicCommission`
* `AuditLog`

Setiap entitas yang relevan dengan tenant wajib memiliki relasi tenant yang tervalidasi.

Gunakan ID unik dan timestamp yang konsisten. Bedakan ID internal dari nomor WO atau nomor invoice yang dibaca manusia.

Tentukan aturan penghapusan dan relasi data. Data transaksi historis tidak boleh rusak ketika nama pelanggan, kendaraan, mekanik, atau katalog berubah.

Jangan mengandalkan angka stok sebagai satu-satunya bukti riwayat. Simpan mutasi stok agar pergerakan barang dapat ditelusuri.

---

### 13. VALIDASI, KEAMANAN, DAN PENANGANAN ERROR

Wajib terapkan:

* Validasi input di sisi klien dan validasi ulang di server untuk produksi.
* Pencegahan referensi lintas tenant.
* Pemeriksaan role pada setiap tindakan sensitif.
* Penanganan ID tidak ditemukan.
* Penanganan data kosong dan database yang gagal dibuka.
* Konfirmasi untuk penghapusan, pembatalan, dan pemulihan data.
* Pencegahan transaksi dan mutasi stok ganda.
* Logging untuk perubahan penting.
* Pesan error yang informatif tanpa membocorkan data sensitif.
* Perlindungan terhadap injeksi HTML saat menampilkan data pengguna.
* Kebijakan yang jelas untuk data yang disimpan lokal.

Jangan menyimpan rahasia server, API key privat, atau kredensial produksi dalam source code frontend.

Jika autentikasi, backend, integrasi WhatsApp, printer khusus, atau pembayaran eksternal belum tersedia, tampilkan sebagai fitur yang belum dikonfigurasi. Jangan membuat simulasi yang tampak seolah-olah integrasi eksternal telah berhasil.

---

### 14. PENGUJIAN DAN KRITERIA PENERIMAAN

Sebelum menyatakan aplikasi selesai, jalankan pengujian berikut.

**A. Instalasi dan aplikasi**

* Dependensi dapat dipasang.
* Aplikasi dapat dijalankan dengan instruksi yang diberikan.
* Build produksi berhasil.
* Tidak ada error JavaScript kritis pada alur utama.
* Layout berfungsi pada lebar 390 px dan desktop.
* Manifest dan service worker valid.

**B. Data**

* Data tersimpan setelah halaman dimuat ulang.
* Data demo dibuat dengan benar.
* Pencarian dan filter berfungsi.
* Backup dan restore dapat diuji.
* Data tenant tidak bercampur.

**C. Work Order**

* Check-in menghasilkan nomor WO yang unik.
* Perubahan status tercatat.
* Mekanik yang ditugaskan tampil benar.
* Status pembayaran tidak otomatis berubah menjadi lunas hanya karena WO selesai.

**D. POS dan pembayaran**

* Total transaksi benar.
* DP, pelunasan, dan saldo benar.
* Diskon mengikuti aturan.
* Klik berulang tidak membuat transaksi ganda.
* Invoice historis tetap konsisten setelah katalog diubah.

**E. Inventaris**

* Mutasi stok tercatat.
* Stok berubah sesuai aturan transaksi.
* Pembatalan dan retur mengikuti kebijakan yang ditentukan.
* Operasi berulang tidak menggandakan mutasi.
* Alert stok minimum bekerja.

**F. Laporan**

* Nilai laporan sesuai transaksi sumber.
* Pendapatan tidak disamakan dengan pembayaran yang diterima.
* HPP dan komisi mengikuti aturan yang ditetapkan.
* Ekspor CSV menghasilkan data yang benar.

**G. Keamanan**

* Role mekanik tidak dapat melakukan tindakan khusus owner.
* Data lintas tenant ditolak.
* Restore file tidak valid tidak merusak data yang sudah ada.

Buat unit test untuk aturan domain yang kritis dan pengujian integrasi untuk alur utama. Jangan hanya menyatakan lulus berdasarkan inspeksi kode.

---

### 15. OUTPUT YANG WAJIB DIBERIKAN

Berikan hasil dalam urutan berikut:

**1. Ringkasan arsitektur**

* Pilihan teknologi.
* Batasan Mode A dan jalur ke Mode B.
* Diagram alur data singkat.
* Aturan domain yang paling penting.

**2. Struktur proyek**
Tampilkan struktur folder dan jelaskan tanggung jawab file penting.

**3. Implementasi lengkap**

* Seluruh source code yang diperlukan.
* Komponen UI.
* Model dan skema database.
* Layanan bisnis.
* Seed data.
* Manifest dan service worker.
* Validasi dan error handling.
* Unit test serta pengujian alur utama.

Jangan memberikan placeholder seperti `implement later`, `TODO`, atau tombol yang belum berfungsi untuk fitur yang dinyatakan selesai.

Jika batas respons tidak memungkinkan seluruh kode disampaikan sekaligus, pecah implementasi menjadi beberapa bagian yang konsisten dan lanjutkan dari struktur yang sama. Jangan mengganti arsitektur di tengah implementasi.

**4. Panduan menjalankan aplikasi**

* Prasyarat.
* Instalasi dependensi.
* Perintah development.
* Perintah build.
* Cara menjalankan hasil build melalui HTTP/HTTPS.
* Cara menguji instalasi PWA.
* Cara menggunakan akun demo.
* Cara melakukan backup dan restore.

**5. Laporan pengujian**
Cantumkan pengujian yang benar-benar dijalankan, hasilnya, dan masalah yang belum terselesaikan. Jika tidak dapat menjalankan browser atau pengujian otomatis, jelaskan keterbatasannya secara jujur.

**6. Checklist kesiapan**
Pisahkan secara tegas:

* Fitur yang sudah berfungsi.
* Fitur yang baru berupa prototipe.
* Fitur yang membutuhkan backend.
* Fitur yang belum dibuat.
* Pekerjaan yang wajib diselesaikan sebelum penggunaan produksi.

**7. Artefak siap dicoba**
Jika lingkungan mendukung pembuatan file, hasilkan proyek dengan struktur folder yang benar dan sediakan ZIP atau artefak yang bisa diakses. Jangan hanya menampilkan potongan kode apabila file proyek dapat dibuat.

---

### 16. CARA KERJA

Kerjakan secara sistematis:

1. Identifikasi kebutuhan dan batasan yang berpotensi menimbulkan kesalahan.
2. Tetapkan model data dan aturan bisnis sebelum membuat UI.
3. Implementasikan alur inti: tenant → pelanggan/kendaraan → WO → jasa/sparepart → invoice → pembayaran → mutasi stok → laporan.
4. Buat seed data dan validasi isolasi tenant.
5. Implementasikan PWA dan penyimpanan lokal.
6. Jalankan pengujian dan perbaiki kegagalan yang ditemukan.
7. Sampaikan artefak, cara menjalankan, dan keterbatasan yang masih ada.

Jika ada keputusan yang belum ditentukan tetapi tidak menghalangi implementasi, gunakan asumsi yang masuk akal dan dokumentasikan. Ajukan pertanyaan hanya jika keputusan tersebut benar-benar menentukan arsitektur atau aturan bisnis.

**Target akhir:** BengkelPro MVP yang dapat dijalankan, responsif, mempunyai alur operasional yang konsisten, mendukung demo multi-bengkel, memiliki PWA yang benar-benar diimplementasikan, serta mempunyai jalur peningkatan yang jelas menuju aplikasi produksi yang aman dan dapat digunakan banyak bengkel.
