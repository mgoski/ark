# ARK — Aplikasi Register KTP by oski

**ARK (Aplikasi Register KTP)** adalah aplikasi web untuk membantu proses pencatatan, monitoring, pengelolaan, pencarian, dan pelaporan pengajuan layanan KTP secara terstruktur.

Aplikasi memisahkan alur **Masyarakat, Orang Kantor, OPD, dan Operator** agar data lebih rapi, mudah dimonitor, dan tidak saling tercampur.

## ✨ Fitur Utama

### Pendaftaran
- **Masyarakat**: nomor antrean manual, jenis pengajuan dari master, validasi NIK 16 digit.
- **Orang Kantor**: pilih nama dari master, kode dan nomor antrean otomatis, jenis pengajuan terpisah.
- **OPD**: khusus Admin/Admin MPP, pilih OPD dari master, nomor antrean otomatis, jenis pengajuan terpisah.
- **Operator**: nomor antrean otomatis berdasarkan username, maksimal 3 input per hari.
- Nomor antrean seluruh jalur diproteksi hanya pada **hari berjalan**, sehingga nomor dapat digunakan kembali pada hari berikutnya.

### 🏢 Master Data
- Master Jenis Pengajuan
- Master Orang Kantor
- Master OPD
- Status master **Aktif / Nonaktif**
- Data nonaktif otomatis tidak muncul di form pendaftaran.
- Master mencegah data duplikat.
- Pengurutan dan keterangan master.

### 👥 Hak Akses

| Level | Masyarakat | Orang Kantor | OPD | Operator |
|---|---:|---:|---:|---:|
| **Admin** | ✅ | ✅ | ✅ | ✅ |
| **Admin MPP** | ✅ | ✅ | ✅ | ✅ |
| **FO** | ✅ | ✅ | ❌ | ❌ |
| **FO MPP** | ✅ | ✅ | ❌ | ❌ |
| **Operator** | ❌ | ❌ | ❌ | ✅ |

### 📊 Dashboard
- Total pengajuan
- Total pengajuan Orang Kantor
- Total pengajuan Operator
- Total pengajuan OPD
- Total pendaftaran harian
- Rekap jenis pengajuan
- Rekap per user
- Monitoring sesuai wilayah

Total harian menggunakan **UUID pengajuan** agar satu pengajuan tidak dihitung ganda ketika berpindah tabel.

### 🔎 Pencarian
Pencarian dapat dilakukan berdasarkan:
- Nomor antrean
- NIK
- Nama pemohon
- Jenis pengajuan
- Status
- Data Disduk dan MPP

Pencarian tidak dibatasi tahun dan menggunakan pagination server-side.

### 🖨️ Cetak & Restore
- Data selesai dapat masuk ke Cetak.
- Menyimpan tanggal selesai dan user penyelesai.
- Data yang dihapus masuk ke Restore.
- Restore mempertahankan asal tabel dan UUID pengajuan.

### 📚 Riwayat & Audit
Mencatat:
- Input
- Edit
- Hapus
- Restore
- Perubahan status
- User yang melakukan tindakan
- Waktu aktivitas

### 🧾 Laporan
Admin dan Admin MPP dapat membuat laporan PDF dan rekap sesuai kebutuhan aplikasi.

### 👤 Profile
- Foto profile
- Ubah password
- Kuota penggantian foto untuk FO, FO MPP, dan Operator: maksimal **2 kali per minggu**.

### 💬 Chat Internal
- Chat antar user sesuai hak akses.
- Pesan memiliki masa berlaku.
- Cleanup otomatis untuk pesan yang sudah kedaluwarsa.

### 🔔 Notifikasi
Titik notifikasi pada menu tertentu digunakan untuk menandai pengajuan baru yang membutuhkan perhatian.

### ☰ Sidebar
- Sidebar dapat diperbesar/diperkecil.
- Kondisi sidebar dapat dikunci.
- Kondisi collapsed tetap tersimpan setelah reload menggunakan `localStorage`.
- Feather Icons digunakan untuk icon menu.

### 📖 Tutorial
Tersedia menu Tutorial Penggunaan dengan contoh form dan penjelasan:
- Pendaftaran Masyarakat
- Pendaftaran Orang Kantor
- Pendaftaran OPD
- Pendaftaran Operator
- Cetak
- Restore
- Pencarian
- Laporan
- Master Data
- Hak akses

Form contoh pada tutorial dibuat nonaktif sehingga tidak mengubah data aplikasi.

### 🩺 Kesehatan Aplikasi
Admin memiliki halaman monitoring untuk:
- Database
- PHP
- Storage Upload
- Storage Profile
- Chat
- Struktur tabel utama

## 🔐 Keamanan
ARK menerapkan:
- Session authentication
- Role-based access control
- CSRF protection
- Prepared statement
- Validasi input
- Escape output
- Pembatasan wilayah
- IP Allowed
- Audit Log
- UUID pengajuan
- Proteksi nomor antrean
- Soft delete master
- Validasi server-side untuk jenis pengajuan aktif

## 🆔 UUID Pengajuan
Setiap pengajuan memiliki `uuid_pengajuan` sebagai identitas permanen.

Contoh alur:

```text
Pendaftaran
    ↓
Cetak
```

atau:

```text
Pendaftaran
    ↓
Restore
```

UUID tetap sama sehingga histori dan perhitungan tetap konsisten.

## 🗃️ Struktur Data

### Master
```text
master_jenis_pengajuan
master_orang_kantor
master_opd
```

### Pendaftaran
```text
daftar
daftar_operator
daftar_orang_kantor
daftar_opd
```

### Arsip & histori
```text
cetak
restore
pengajuan_riwayat
audit_log
```

### Sistem
```text
pengguna
ip_alloweds
pengaturan
profile_photo_changes
chat_messages
chat_reads
```

## 🛠️ Teknologi
- PHP 7.4+
- PDO
- MariaDB / MySQL
- HTML5
- Tailwind CSS
- JavaScript
- Feather Icons
- DataTables
- PDF / CSV / SQL / Excel pada modul terkait

## 🚀 Instalasi

### 1. Clone repository

```bash
git clone https://github.com/mgoski/ark.git
cd ark
```

### 2. Siapkan database

Untuk instalasi baru:

```text
database.sql
```

Untuk database existing:

```text
upgrade_database.sql
```

**Backup database terlebih dahulu sebelum melakukan upgrade.**

### 3. Konfigurasi database

Sesuaikan koneksi database pada file konfigurasi aplikasi.

Contoh:

```php
$host = 'localhost';
$db   = 'db_name';
$user = 'root';
$pass = '';
```

### 4. Jalankan aplikasi

Pada XAMPP:

```text
http://localhost/{dir}/
```

## ⚠️ Catatan Deployment
- Jangan upload credential database ke repository publik.
- Jangan commit file `.env` atau file konfigurasi rahasia.
- Backup database secara berkala.
- Pastikan folder upload memiliki permission yang benar.
- Untuk deployment produksi, gunakan HTTPS.

## 📁 File Database

```text
database.sql
upgrade_database.sql
```

`database.sql` digunakan untuk instalasi baru.

`upgrade_database.sql` digunakan untuk memperbarui database existing tanpa menghapus data lama.

## 📄 Lisensi

Tambahkan lisensi sesuai kebutuhan organisasi/pengembang.

```text
Copyright © 2026 ARK — Aplikasi Register KTP by oski
```
## ℹ️ Info
- PHP Support : 7.4.33 up to 8.0 ✅
- Masih menggunakan php native ✅
- Layout menggunakan Tailwind ✅
  
---

# UPGRADE
# ARK - Sistem Hak Akses Dinamis

Admin dapat mengatur permission untuk role `FO`, `FO MPP`, dan `Operator` dari menu **Hak Akses Pengguna** tanpa mengubah source code.

Permission dikelompokkan menjadi:
- Pendaftaran Masyarakat: view, input
- Pendaftaran Orang Kantor: view, input
- Pendaftaran OPD: view, input
- Pendaftaran Operator: view, input
- Aksi Pendaftaran: edit, hapus, selesai
- Fitur Umum: cetak, pencarian, restore, laporan, chat, tentang, tutorial


`Admin` dan `Admin MPP` tetap menjadi super-role pada engine permission sehingga tidak terblokir oleh toggle permission.

Database: tabel `role_permissions` dibuat otomatis saat aplikasi berjalan. Untuk instalasi manual tersedia `upgrade_permissions.sql`.

Scope wilayah A/B tetap dipertahankan terpisah; permission tidak menghapus pembatasan wilayah.

# REGIS KTP V2

Tambahan V2:
- Tentang Aplikasi
- Import CSV
- Export CSV
- Template CSV per tabel
- Nama aplikasi dapat diubah oleh admin/admin_mpp
- Audit log
- Dashboard dan CRUD dasar dari V1
- Tailwind tetap menggunakan CDN

## Instalasi
1. Extract ZIP ke htdocs, misalnya `htdocs/regis_ktp_v2`.
2. Import `database.sql`.
3. Atur koneksi di `config/config.php`.
4. Daftarkan IP admin pada `ip_alloweds`.
5. Login default:
   - admin / password
6. Buka Management > Pengaturan Aplikasi untuk mengubah nama aplikasi.

## CSV
Template tersedia untuk:
- daftar
- cetak
- restore
- pengguna
- ip_alloweds

CSV harus memakai header yang sama persis. Kolom ID auto increment boleh dikosongkan.

## Catatan
Untuk import tabel pengguna, nilai password harus sudah berupa hash `password_hash()`, bukan plaintext.


### Fix V5 - Selesai Orang Kantor
Scope query untuk bulk `Selesai` sekarang disamakan dengan scope tabel Pendaftaran Orang Kantor, sehingga data yang terlihat melalui username wilayah tetap dapat dipindahkan ke Cetak.

# ARK Access/Permission Upgrade

Untuk database yang sudah berjalan, jalankan `upgrade_access_final.sql` satu kali.
Aplikasi juga membuat tabel permission/activity yang hilang secara otomatis saat user login.

Kolom `pengguna.wilayah`:
- A = Disduk
- B = MPP

Tabel:
- role_permissions
- user_permissions
- user_activity_log

# ARK Ultra Repair — 23 Agustus 2026

Source basis: ARK latest package plus uploaded Ultra Scanner report.

The uploaded scanner report contains 52 scanned files / 5290 source lines and 900 findings. Many "Syntax Error" findings use the Apache `AH02965` message as evidence rather than a PHP parser message, so they require separate runtime verification.

Repairs applied in this pass:
- Session cookie hardening: strict mode, cookie-only, HttpOnly, SameSite=Lax, Secure under HTTPS.
- Central scalar input helpers for POST/GET integer/string/enum validation.
- Chat API scalar input validation.
- Pendaftaran/Restore ID and bulk-array validation.
- Login input validation and 5-attempt / 5-minute session throttle.
- Report/search/export/master/admin action parameter validation.
- CSV import restricted to actual uploaded files, 5 MB and 10,000 rows.
- Master Jenis Pengajuan nested-form markup corrected.
- Database cleanup statements use query()/prepare()->execute() instead of PDO exec where appropriate.
- Existing CSRF, role checks, allowlists and prepared statements retained.

Verification:
- PHP lint is run across all PHP files after patching.
- No claim is made that all scanner findings are real application defects; heuristic/style findings are treated separately.

## Follow-up from Ultra Scanner rerun
- Removed the remaining `PDO->exec()` from `audit_log_action.php`.
- Replaced the dynamic queue-table identifier in `pendaftaran_action.php` with six fixed SQL statements, eliminating identifier injection risk at that location.
- Removed the hardcoded reset password from `pengguna_action.php`; password resets now generate a random temporary password.
- Profile password fields now use the central scalar validator.
- The scanner's duplicate-function findings involving included global helpers / PHP private methods / JavaScript helpers should still be treated as heuristic findings until the scanner understands PHP/JS scopes and `require_once`.


## Ultra Galak Follow-up
- Renamed `SimplePdf::esc()` to `pdfEscape()` because the scanner incorrectly reported the private method as a global duplicate.
- Strengthened pendaftaran edit inputs with centralized scalar validation.
- Added strict database-identifier validation for full database backup; identifiers originate from server-side metadata only.
- Backup SQL is delivered as `application/octet-stream` attachment rather than browser-renderable HTML.
- Added `quote_identifier()` allowlist helper for CSV/XLSX/SQL exports and imports.
- The remaining dynamic SQL warnings in backup/export code are for schema-driven identifiers that cannot be parameterized as values; the identifiers are now strictly allowlisted/validated before use.


## V4 follow-up
- Removed dynamic source-table INSERT/SELECT/DELETE statements from `pendaftaran_action.php`; mode now selects fixed SQL statements for `daftar`, `daftar_operator`, `daftar_orang_kantor`, and `daftar_opd`.
- Removed dynamic restore target INSERT from `restore_action.php`; `asal_tabel` now selects one of four fixed SQL statements.
- Removed the dynamic `IN (...)` delete from Master Jenis Pengajuan deduplication; duplicate IDs are deleted one-by-one with a fixed prepared statement.
- Removed plaintext-password comparison/migration from `login.php`; authentication now accepts only password hashes. Legacy accounts require an admin reset.

# Revisi ARK 31 Agustus 2026

1. Hak Akses Pengguna: Admin dan Admin MPP dapat membuka dan menyimpan permission.
2. Aktivitas Pengguna: hanya menampilkan aktivitas hari ini, mengikuti pola menu Cetak.
3. Pendaftaran Orang Kantor dan OPD: daftar dibatasi ketat berdasarkan prefix wilayah A/B. Admin/FO = A, Admin MPP/FO MPP = B.
4. Pendaftaran Operator: tidak ditampilkan untuk Admin MPP dan endpoint langsungnya ditolak.
5. Notifikasi Pendaftaran Operator: Admin MPP tidak mendapatkan notifikasi.

# ARK Forbidden UI

Semua penolakan akses 403 diarahkan ke `forbidden.php`, bukan halaman putih dengan teks polos.
Halaman menampilkan:
- kode 403 dan ikon shield-off,
- alasan akses ditolak,
- tombol Kembali,
- tombol Dashboard/Login,
- instruksi meminta Admin mengatur permission.

HTTP status tetap 403.

---

## ❤️ ARK — Aplikasi Register KTP

Sistem register dan monitoring pelayanan KTP yang dirancang agar proses pencatatan, pengelolaan, pencarian, pelaporan, dan histori pengajuan menjadi lebih **rapi, terukur, dan mudah digunakan**.
