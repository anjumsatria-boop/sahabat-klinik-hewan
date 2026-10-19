# PRD: Aplikasi Klinik Dokter Hewan Berbasis Web (RME)

**Versi:** 1.0 (Draft) | **Tanggal:** 13 Oktober 2026 | **Status:** Untuk review pemangku kepentingan

## 1. Ringkasan Eksekutif

Aplikasi ini adalah sistem informasi klinik dokter hewan berbasis website yang mengelola seluruh proses layanan: pendaftaran pasien (hewan) dan pemilik, rekam medis elektronik (RME), pemeriksaan, tindakan, resep dan obat, laboratorium, rawat inap, kasir, hingga pelaporan. Sistem dibangun dengan **CodeIgniter 4** dan **MySQL**, dengan pengamanan data berbasis **enkripsi**, serta dirancang untuk seluruh lingkup fasilitas pelayanan kesehatan tingkat pertama (FKTP).

## 2. Latar Belakang dan Masalah

Klinik hewan umumnya masih memakai pencatatan manual atau aplikasi terpisah, sehingga muncul masalah berikut:

- Rekam medis tersebar, sulit dilacak, dan mudah hilang atau rusak.
- Riwayat vaksinasi, pengobatan, dan alergi hewan tidak terintegrasi antar kunjungan.
- Stok obat dan alat kesehatan tidak sinkron dengan resep dan tindakan.
- Penagihan lambat dan rawan selisih.
- Data pemilik dan data medis belum terlindungi memadai.
- Pelaporan ke pihak berwenang (mis. dinas peternakan dan kesehatan hewan) dilakukan manual.

**Pernyataan masalah:** dibutuhkan rancangan aplikasi yang efektif, terintegrasi, aman, dan sesuai regulasi untuk seluruh alur kerja klinik.

## 3. Tujuan dan Indikator Keberhasilan

| Tujuan | Indikator (KPI) | Target awal |
| --- | --- | --- |
| Digitalisasi rekam medis | Persentase kunjungan yang tercatat di RME | 100% dalam 3 bulan setelah go-live |
| Efisiensi pelayanan | Waktu pendaftaran hingga masuk ruang periksa | Turun minimal 30% |
| Akurasi stok | Selisih stok obat vs fisik | Di bawah 2% |
| Keamanan data | Insiden kebocoran data | 0 |
| Ketersediaan sistem | Uptime pada jam operasional | Minimal 99,5% |
| Kepuasan pengguna | Skor kepuasan staf dan pemilik | Minimal 4 dari 5 |

## 4. Ruang Lingkup

### 4.1 Dalam lingkup (in scope)

Seluruh lingkup faskes tingkat pertama, mencakup: pendaftaran dan antrean, RME, pemeriksaan dan diagnosis, tindakan dan bedah minor, vaksinasi dan jadwal ulang, resep dan farmasi/apotek, laboratorium sederhana, rawat inap/penitipan, kasir dan penagihan, manajemen stok, administrasi dan SDM, pelaporan, serta manajemen pengguna dan keamanan.

### 4.2 Di luar lingkup (versi 1.0)

- Aplikasi mobile native (hanya web responsif).
- Integrasi alat laboratorium otomatis (LIS/HL7) dan perangkat pencitraan (PACS).
- Telemedisin video.
- Marketplace obat.

### 4.3 Catatan asumsi regulasi

Istilah FKTP dan RME secara resmi berasal dari regulasi kesehatan manusia (mis. Permenkes tentang RME). Untuk klinik hewan, rujukan hukum utamanya berada di bawah bidang kesehatan hewan dan peternakan. PRD ini mengasumsikan FKTP bermakna "fasilitas layanan tingkat pertama" pada klinik hewan, dan menggunakan prinsip RME yang setara (kerahasiaan, integritas, ketersediaan, jejak audit, masa simpan). **Daftar regulasi yang berlaku harus diverifikasi** bersama pihak legal dan otoritas veteriner setempat sebelum desain final, termasuk ketentuan perlindungan data pribadi pemilik hewan (UU Pelindungan Data Pribadi) dan ketentuan izin praktik dokter hewan.

## 5. Pemangku Kepentingan dan Peran Pengguna

| Peran | Tanggung jawab utama |
| --- | --- |
| Super Admin | Konfigurasi sistem, manajemen pengguna, kunci enkripsi, audit |
| Dokter Hewan | Anamnesis, pemeriksaan, diagnosis, tindakan, resep, tanda tangan elektronik RME |
| Paramedis Veteriner / Perawat | Pemeriksaan awal, tanda vital, tindakan, perawatan rawat inap |
| Petugas Pendaftaran | Registrasi pemilik dan hewan, antrean, janji temu |
| Apoteker / Petugas Farmasi | Verifikasi resep, dispensing, stok obat |
| Analis Laboratorium | Permintaan dan input hasil pemeriksaan |
| Kasir | Tagihan, pembayaran, kwitansi |
| Manajer / Pemilik Klinik | Dashboard, laporan, persetujuan |
| Pemilik Hewan (opsional) | Portal: janji temu, riwayat, jadwal vaksin |
| Auditor / Pengawas | Akses baca terbatas untuk laporan dan audit |

Akses mengikuti prinsip **RBAC (Role-Based Access Control)** dan *least privilege*.

## 6. Persona dan Alur Pengguna Utama

**Alur kunjungan standar:**

1. Pemilik datang atau membuat janji temu, lalu petugas mendaftarkan atau mencari data pemilik dan hewan.
2. Sistem membuat nomor kunjungan dan antrean.
3. Paramedis mencatat pemeriksaan awal (berat badan, suhu, denyut, keluhan).
4. Dokter melakukan anamnesis, pemeriksaan, diagnosis, dan rencana terapi di RME.
5. Bila perlu, permintaan lab dan tindakan dibuat; hasil kembali ke RME.
6. Dokter membuat resep; farmasi memverifikasi dan menyerahkan obat (stok berkurang otomatis).
7. Kasir menerbitkan tagihan dari tindakan, obat, dan layanan.
8. Dokter mengunci dan menandatangani RME; jadwal kontrol atau vaksin berikutnya dibuat.

## 7. Kebutuhan Fungsional

Prioritas: **P0** = wajib rilis awal, **P1** = penting, **P2** = tambahan.

### 7.1 Manajemen Pengguna dan Akses

- FR-01 (P0): Login dengan autentikasi aman, manajemen peran dan izin (RBAC).
- FR-02 (P0): Pengelolaan data staf, termasuk nomor izin praktik (mis. SIP dokter hewan) dan masa berlakunya.
- FR-03 (P1): Autentikasi dua faktor (2FA) untuk peran dokter dan admin.
- FR-04 (P0): Log aktivitas (audit trail) seluruh akses dan perubahan data sensitif.

### 7.2 Pendaftaran dan Antrean

- FR-05 (P0): Registrasi pemilik (identitas, kontak, alamat) dan hewan (nama, spesies, ras, jenis kelamin, tanggal lahir/umur, warna, ciri khusus, status steril, nomor microchip bila ada).
- FR-06 (P0): Pencarian cepat pemilik dan hewan; pencegahan data ganda.
- FR-07 (P0): Nomor rekam medis unik per hewan; satu pemilik dapat memiliki banyak hewan.
- FR-08 (P0): Antrean kunjungan dan pembagian layanan (poli umum, bedah, vaksin, grooming bila ada).
- FR-09 (P1): Janji temu dan pengingat (WhatsApp/SMS/email).
- FR-10 (P0): Persetujuan tindakan (informed consent) pemilik, tersimpan digital.

### 7.3 Rekam Medis Elektronik (Inti)

- FR-11 (P0): Format RME terstruktur: anamnesis, pemeriksaan fisik, tanda vital, diagnosis, prognosis, terapi, tindakan, catatan.
- FR-12 (P0): Riwayat lengkap per hewan (kunjungan, alergi, penyakit kronis, vaksinasi, obat, bedah).
- FR-13 (P1): Kodefikasi diagnosis dengan standar terminologi veteriner (mis. VeNom atau SNOMED CT Veterinary) agar seragam dan terlaporkan.
- FR-14 (P0): Lampiran foto/dokumen (hasil lab, foto lesi) dengan penyimpanan terenkripsi.
- FR-15 (P0): Penguncian RME setelah finalisasi; koreksi hanya lewat addendum dengan jejak versi.
- FR-16 (P1): Tanda tangan elektronik dokter.
- FR-17 (P1): Grafik pertumbuhan dan berat badan, serta kalkulator dosis berdasarkan berat dan spesies.
- FR-18 (P1): Ringkasan medis dan surat rujukan/keterangan sehat dapat dicetak (PDF).

### 7.4 Vaksinasi dan Pencegahan

- FR-19 (P0): Catatan vaksin (jenis, batch, tanggal, dokter) dan jadwal ulang otomatis.
- FR-20 (P1): Pengingat jatuh tempo vaksin, obat cacing, dan kontrol.
- FR-21 (P1): Kartu/sertifikat vaksin digital.

### 7.5 Laboratorium

- FR-22 (P1): Permintaan pemeriksaan dari RME, input hasil, nilai rujukan per spesies, penandaan hasil abnormal.
- FR-23 (P2): Tren hasil lab antar kunjungan.

### 7.6 Farmasi dan Inventori

- FR-24 (P0): Resep elektronik terhubung ke stok farmasi.
- FR-25 (P0): Master obat dan alat (satuan, harga, golongan, batch, kedaluwarsa).
- FR-26 (P0): Penerimaan barang, mutasi, stok opname, dan peringatan stok minimum dan kedaluwarsa (FEFO).
- FR-27 (P1): Pencatatan obat keras/terbatas dan antibiotik sesuai ketentuan yang berlaku.

### 7.7 Rawat Inap dan Penitipan

- FR-28 (P1): Manajemen kandang/ruang, admisi, pemantauan harian, pemberian obat terjadwal, pulang.

### 7.8 Kasir dan Keuangan

- FR-29 (P0): Tagihan otomatis dari layanan, tindakan, dan obat; diskon dan paket.
- FR-30 (P0): Pembayaran tunai, transfer, QRIS/e-wallet (integrasi bertahap); kwitansi.
- FR-31 (P1): Piutang/deposit dan rekonsiliasi harian kas.

### 7.9 Pelaporan dan Dashboard

- FR-32 (P0): Laporan kunjungan, 10 penyakit terbanyak, pendapatan, stok, vaksinasi.
- FR-33 (P1): Laporan penyakit hewan menular strategis dan data untuk pelaporan ke otoritas veteriner.
- FR-34 (P1): Ekspor ke Excel dan PDF; dashboard manajer.

### 7.10 Portal Pemilik (opsional)

- FR-35 (P2): Pemilik melihat riwayat, jadwal vaksin, tagihan, dan membuat janji temu.

## 8. Kebutuhan Non-Fungsional

| Kategori | Kebutuhan |
| --- | --- |
| Kinerja | Halaman utama dimuat di bawah 3 detik pada jaringan normal; mendukung minimal 50 pengguna bersamaan |
| Ketersediaan | Uptime 99,5% pada jam operasional; backup otomatis harian |
| Skalabilitas | Dapat melayani satu klinik, dan dikembangkan multi-cabang |
| Kegunaan | Antarmuka responsif (desktop, tablet), alur input maksimal 3 klik untuk aksi umum |
| Kompatibilitas | Browser modern (Chrome, Edge, Firefox, Safari versi terbaru) |
| Pemulihan bencana | RPO maksimal 24 jam, RTO maksimal 4 jam |
| Retensi data | Masa simpan RME mengikuti regulasi yang berlaku (ditetapkan setelah verifikasi, bukan dihapus fisik sebelum waktunya) |
| Lokalisasi | Bahasa Indonesia, zona waktu WIB/WITA/WIT, format mata uang Rupiah |

## 9. Rancangan Keamanan dan Enkripsi

### 9.1 Enkripsi data

- **Data bergerak (in transit):** HTTPS wajib dengan TLS 1.2 atau lebih baru dan HSTS.
- **Data diam (at rest):** enkripsi kolom sensitif (identitas pemilik, kontak, isi RME, lampiran) dengan AES-256 melalui layanan Encryption bawaan CodeIgniter 4 (OpenSSL/Sodium). Pertimbangkan juga enkripsi tingkat basis data/disk (tablespace encryption MySQL atau enkripsi volume server).
- **Kata sandi:** hash dengan Argon2id atau bcrypt, tidak pernah dienkripsi balik.
- **Pengelolaan kunci:** kunci disimpan di luar kode dan basis data (variabel lingkungan atau vault/KMS), rotasi berkala, pemisahan hak akses kunci.
- **Backup:** terenkripsi, disimpan di lokasi terpisah, diuji pemulihannya.

**Catatan teknis:** kolom terenkripsi tidak dapat dicari dengan LIKE biasa. Gunakan *blind index* (hash ber-kunci) untuk pencarian eksak, seperti nomor telepon atau nomor RM, dan simpan data pencarian non-sensitif secara terpisah.

### 9.2 Kontrol aplikasi

- RBAC, 2FA, pembatasan percobaan login, timeout sesi, dan kebijakan sandi kuat.
- Perlindungan CSRF, XSS, dan SQL injection (CSRF filter, *query builder*/prepared statement, escaping output, *Content Security Policy*).
- Validasi dan pembatasan unggahan file (tipe, ukuran, pemindaian).
- Audit trail tidak dapat diubah (append-only) untuk akses dan perubahan RME.
- Mekanisme *break-glass* untuk akses darurat dengan pencatatan khusus.
- Pembatasan IP/WAF bila memungkinkan; pembaruan keamanan berkala.

### 9.3 Kepatuhan

- Persetujuan pengolahan data pribadi pemilik, hak akses dan koreksi data, serta prosedur penanganan insiden kebocoran.
- Pemisahan lingkungan (dev, staging, production) dan tanpa data nyata di lingkungan uji.

## 10. Arsitektur dan Teknologi

- **Backend:** CodeIgniter 4 (PHP 8.1+), pola MVC, Filters untuk autentikasi dan otorisasi, Validation dan Entities/Models.
- **Basis data:** MySQL 8 (InnoDB), migrasi dan seeder melalui CI4 Migrations.
- **Frontend:** Blade-like View Layer CI4, Bootstrap/Tailwind, JavaScript ringan (mis. Alpine.js atau jQuery), DataTables untuk tabel besar.
- **Server:** Nginx/Apache, PHP-FPM, Linux; opsional Docker.
- **Layanan pendukung:** antrean tugas (cron/queue) untuk pengingat dan backup, penyimpanan file terenkripsi, gateway WhatsApp/SMS, pembuat PDF (mis. Dompdf).
- **API:** REST internal berotentikasi token untuk integrasi masa depan.

## 11. Model Data Tingkat Tinggi

Entitas utama: `users`, `roles`, `permissions`, `owners`, `patients` (hewan), `visits`, `medical_records`, `diagnoses`, `procedures`, `vaccinations`, `lab_requests`, `lab_results`, `prescriptions`, `prescription_items`, `medicines`, `stock_batches`, `stock_movements`, `inpatient_stays`, `invoices`, `invoice_items`, `payments`, `appointments`, `consents`, `attachments`, `audit_logs`.

Relasi inti: satu *owner* memiliki banyak *patients*; satu *patient* memiliki banyak *visits*; satu *visit* memiliki satu *medical\_record* dengan banyak diagnosis, tindakan, resep, dan lab; *invoice* menaut ke *visit*.

## 12. Persyaratan Integrasi

- WhatsApp/SMS untuk pengingat (P1).
- Payment gateway QRIS/e-wallet (P2).
- Ekspor data pelaporan ke otoritas veteriner (format menyesuaikan ketentuan, P1).
- Printer kwitansi dan label obat; pemindai barcode/microchip (P2).

## 13. Rencana Rilis (Roadmap)

| Fase | Cakupan | Estimasi |
| --- | --- | --- |
| Fase 0 | Analisis kebutuhan, verifikasi regulasi, desain UI/UX dan basis data | 3 sampai 4 minggu |
| Fase 1 (MVP) | Pengguna dan RBAC, pendaftaran, RME inti, resep dan farmasi dasar, kasir, enkripsi, audit log | 8 sampai 10 minggu |
| Fase 2 | Vaksinasi dan pengingat, laboratorium, inventori lengkap, laporan | 6 sampai 8 minggu |
| Fase 3 | Rawat inap, portal pemilik, integrasi pembayaran, pelaporan otoritas | 6 sampai 8 minggu |
| Fase 4 | Uji penetrasi, UAT, pelatihan, migrasi data, go-live dan hypercare | 4 minggu |

Estimasi bersifat indikatif dan bergantung pada ukuran tim.

## 14. Kriteria Penerimaan (Contoh)

- Dokter dapat membuat RME lengkap untuk satu kunjungan, menandatangani, dan RME terkunci; perubahan lanjutan hanya melalui addendum yang tercatat.
- Data sensitif di basis data tidak terbaca dalam bentuk teks biasa saat diperiksa langsung.
- Pengguna tanpa izin tidak dapat membuka RME peran lain (diuji pada setiap peran).
- Resep yang diserahkan mengurangi stok sesuai batch, dan tagihan terbentuk otomatis.
- Setiap akses dan perubahan RME tercatat di audit log dengan pengguna, waktu, dan aksi.
- Pemulihan dari backup berhasil diuji dalam target RTO.

## 15. Risiko dan Mitigasi

| Risiko | Dampak | Mitigasi |
| --- | --- | --- |
| Ketidakjelasan regulasi RME untuk klinik hewan | Rancangan perlu diubah | Verifikasi legal di Fase 0, desain modular |
| Kebocoran data/kunci enkripsi | Pelanggaran privasi, reputasi | Manajemen kunci terpisah, audit, uji penetrasi |
| Resistensi staf terhadap sistem baru | Adopsi rendah | Pelatihan, UI sederhana, pendampingan |
| Pencarian lambat pada data terenkripsi | UX buruk | Blind index dan indeks data non-sensitif |
| Internet tidak stabil | Layanan terhenti | Mode ringan, backup lokal, SOP manual sementara |
| Migrasi data lama berkualitas rendah | Data tidak akurat | Pembersihan data dan validasi sebelum impor |

## 16. Asumsi dan Pertanyaan Terbuka

1. Apakah klinik single-cabang atau multi-cabang sejak awal?
2. Spesies yang dilayani: hewan kecil saja, atau juga ternak/hewan besar?
3. Regulasi spesifik mana yang wajib dipatuhi, dan apakah ada format pelaporan resmi ke dinas?
4. Apakah pemilik hewan memerlukan portal sejak rilis awal?
5. Apakah ada data lama (kertas/Excel) yang perlu dimigrasi?
6. Infrastruktur hosting: server lokal atau cloud, dan siapa yang mengelola backup?
7. Metode pembayaran dan integrasi yang diprioritaskan?

## 17. Lampiran yang Disarankan

Diagram alur kunjungan (BPMN), ERD lengkap, wireframe UI per peran, matriks RBAC (peran × modul × aksi), dan rencana uji (UAT dan keamanan).
