# ARK — Aplikasi Register KTP

**ARK (Aplikasi Register KTP)** adalah aplikasi web internal untuk pencatatan, pemantauan, pengelolaan, pencarian, pemindahan status, restore, pelaporan, audit, notifikasi, dan pengamanan pengajuan layanan KTP.

ARK dirancang untuk lingkungan **intranet / local web (XAMPP/Apache)** dan menggunakan pendekatan **multi-role** serta **multi-wilayah**:

- **Disduk** menggunakan scope/prefix antrean **A**.
- **MPP** menggunakan scope/prefix antrean **B**.
- Scope diterapkan pada pendaftaran, cetak, restore, pencarian, dashboard, laporan, riwayat, monitoring, dan fitur operasional lain yang berkaitan dengan data pengajuan.

> **Status:** Active development / internal application
>
> **Versi referensi:** `11.2.1899`
>
> **Timezone:** `Asia/Jakarta`
>
> **Target deployment:** PHP `7.4.x` sampai `8.0.x`, Apache/XAMPP, MariaDB/MySQL

---

## Daftar Isi

- [Gambaran Umum](#gambaran-umum)
- [Tujuan](#tujuan)
- [Fitur Utama](#fitur-utama)
- [Role dan Hak Akses](#role-dan-hak-akses)
- [Scope Wilayah A dan B](#scope-wilayah-a-dan-b)
- [Alur Pengajuan](#alur-pengajuan)
- [Modul Pendaftaran](#modul-pendaftaran)
- [Modul Cetak](#modul-cetak)
- [Restore dan Riwayat](#restore-dan-riwayat)
- [Dashboard dan Monitoring](#dashboard-dan-monitoring)
- [Laporan](#laporan)
- [Pencarian](#pencarian)
- [Master Data](#master-data)
- [Hak Akses Dinamis](#hak-akses-dinamis)
- [Tema dan Tampilan](#tema-dan-tampilan)
- [Lock Screen dan Session Timeout](#lock-screen-dan-session-timeout)
- [Notifikasi](#notifikasi)
- [Chat Internal](#chat-internal)
- [Profile](#profile)
- [Import dan Export](#import-dan-export)
- [Backup Database](#backup-database)
- [Kesehatan Aplikasi](#kesehatan-aplikasi)
- [Keamanan](#keamanan)
- [Teknologi](#teknologi)
- [Struktur Direktori](#struktur-direktori)
- [Struktur Database](#struktur-database)
- [Instalasi](#instalasi)
- [Konfigurasi](#konfigurasi)
- [URL Bersih](#url-bersih)
- [IP Allowed](#ip-allowed)
- [Login dan Password](#login-dan-password)
- [Upgrade Database](#upgrade-database)
- [Troubleshooting](#troubleshooting)
- [Checklist Setelah Instalasi](#checklist-setelah-instalasi)
- [Catatan Pengembangan](#catatan-pengembangan)
- [Riwayat Revisi](#riwayat-revisi)
- [Lisensi](#lisensi)

---

## Gambaran Umum

ARK adalah aplikasi register/pengajuan KTP yang memisahkan jalur pelayanan berdasarkan jenis sumber data dan wilayah kerja.

Jalur pengajuan utama:

1. **Masyarakat**
2. **Operator**
3. **Orang Kantor**
4. **OPD / Instansi**

Data pengajuan disimpan pada tabel sumber masing-masing, kemudian dapat diproses ke tahap selesai/cetak atau dipindahkan ke restore sesuai aksi yang dilakukan.

ARK juga menggunakan **UUID pengajuan** sebagai identitas permanen lintas tabel sehingga perpindahan data tidak memutus histori dan perhitungan unik.

---

## Tujuan

ARK dibuat untuk:

- mempercepat proses input register/pengajuan;
- mengurangi bentrok nomor antrean;
- memisahkan data Disduk dan MPP;
- mengurangi duplikasi data;
- memisahkan jalur Masyarakat, Operator, Orang Kantor, dan OPD;
- menyediakan monitoring untuk Admin dan Admin MPP;
- menyediakan pencarian data lama tanpa bergantung pada filter tahun;
- menyediakan cetak, restore, laporan, audit, notifikasi, dan backup;
- menyediakan kontrol hak akses berbasis role dan permission;
- menyediakan pengamanan sesi, CSRF, prepared statement, dan pembatasan IP.

---

# Fitur Utama

## Pendaftaran Masyarakat

- Input NIK dan nama pemohon.
- Jenis pengajuan mengambil data dari `master_jenis_pengajuan` yang aktif.
- Nomor antrean menggunakan scope wilayah.
- Proteksi nomor antrean masyarakat diterapkan untuk hari berjalan.
- Anti-duplikasi berdasarkan kombinasi data pengajuan dan NIK sesuai aturan aplikasi.

## Pendaftaran Operator

- Form khusus level `operator`.
- Data disimpan pada `daftar_operator`.
- Nomor antrean dibuat sesuai wilayah/operator dan master kode.
- Batas input operator: **maksimal 3 data per hari per username**.
- Admin dan Admin MPP dapat membuka modul ini dalam mode monitoring/read-only sesuai aturan aplikasi.

## Pendaftaran Orang Kantor

- Form terpisah untuk Orang Kantor.
- Nama/entitas dipilih dari `master_orang_kantor`.
- Kode antrean mengikuti wilayah login.
- Data disimpan pada `daftar_orang_kantor`.
- Tidak menggunakan nama orang sebagai jenis layanan; `nama_entitas` dan `jenis_pengajuan` dipisahkan.

## Pendaftaran OPD

- Form terpisah untuk OPD/instansi.
- Master berasal dari `master_opd`.
- Pengguna yang berhak dapat memilih entitas dan jenis pengajuan.
- Nomor antrean mengikuti scope A/B sesuai aturan modul.
- Data disimpan pada `daftar_opd`.

## Cetak

- Menampilkan data yang telah berstatus `selesai`.
- Data ditampilkan sesuai scope wilayah user.
- **A tidak tampil pada user scope B**, dan **B tidak tampil pada user scope A**.
- Proses selesai dari sumber ke `cetak` dilakukan secara transaksional.
- Tersedia aksi cetak sesuai permission.

## Restore

- Data yang dihapus dari sumber dapat dipindahkan ke `restore`.
- Restore mendukung satu atau banyak data.
- Proses restore menggunakan transaksi.
- UUID, sumber tabel, dan metadata histori dipertahankan agar identitas pengajuan tetap konsisten.

## Pencarian

Pencarian dapat mencakup:

- nomor antrean;
- NIK;
- nama pemohon;
- jenis pengajuan;
- status;
- entitas;
- beberapa sumber tabel sekaligus.

Pencarian dirancang untuk data lama dan **tidak dibatasi tahun/tanggal** pada modul pencarian.

## Laporan

Tersedia untuk role yang berwenang:

- harian;
- bulanan;
- tahunan;
- semua data.

Laporan mengambil data sesuai scope wilayah user dan mendukung output PDF pada modul yang tersedia.

---

# Role dan Hak Akses

| Role | Wilayah | Fungsi Utama |
|---|---|---|
| `admin` | A / Disduk | Super-admin, monitoring semua fitur Disduk dan administrasi aplikasi |
| `admin_mpp` | B / MPP | Admin wilayah MPP dan monitoring |
| `fo` | A / Disduk | Input Masyarakat dan Orang Kantor, pencarian sesuai scope |
| `fo_mpp` | B / MPP | Input Masyarakat dan Orang Kantor, pencarian sesuai scope |
| `operator` | A / Disduk* | Pendaftaran Operator, dengan batas input harian |

`*` Wilayah operator ditetapkan oleh konfigurasi/user role pada aplikasi dan kode antrean harus mengikuti scope wilayah tersebut.

### Admin

Admin memiliki akses administratif paling luas, termasuk:

- Dashboard;
- Pendaftaran Masyarakat;
- Pendaftaran Orang Kantor;
- Pendaftaran OPD;
- Pendaftaran Operator (monitoring);
- Cetak;
- Restore;
- Pencarian;
- Laporan;
- Pengguna;
- Hak Akses;
- IP Allowed;
- Import/Export;
- Backup Database;
- Pengaturan Aplikasi;
- Master Data;
- Riwayat;
- Monitoring;
- Audit Log;
- Kesehatan Aplikasi.

### Admin MPP

Admin MPP memiliki fungsi administratif pada wilayah MPP, termasuk:

- Dashboard MPP;
- Pendaftaran Masyarakat;
- Pendaftaran Orang Kantor;
- Pendaftaran OPD;
- Pendaftaran Operator (monitoring);
- Cetak;
- Restore;
- Pencarian;
- Laporan;
- Pengguna;
- Hak Akses;
- IP Allowed;
- Import/Export;
- Pengaturan;
- Master Data;
- Riwayat;
- Monitoring;
- Audit Log.

Backup database merupakan fitur khusus Admin utama.

### FO

FO dapat melakukan input pada jalur:

- Masyarakat;
- Orang Kantor.

FO bekerja pada scope Disduk dan dapat melihat/mengolah data sesuai aturan permission serta scope.

### FO MPP

FO MPP memiliki fungsi yang sama dengan FO tetapi berada di scope MPP.

### Operator

Operator dibatasi pada jalur Pendaftaran Operator. Operator tidak memiliki akses administratif seperti master, backup, pengguna, dan fitur Admin.

---

# Scope Wilayah A dan B

ARK membedakan dua wilayah utama:

```text
A = Disduk
B = MPP
```

Aturan default berdasarkan role:

```text
admin       -> A
fo          -> A
operator    -> A
admin_mpp   -> B
fo_mpp      -> B
```

Scope dipakai pada:

- pendaftaran;
- cetak;
- restore;
- pencarian;
- dashboard;
- laporan;
- riwayat;
- monitoring;
- export operasional tertentu;
- notifikasi berdasarkan wilayah.

### Prinsip Penting

Data yang sudah masuk ke `cetak` **tetap membawa wilayah dari nomor antreannya**.

Contoh:

```text
Operator Disduk -> A001
Admin Disduk menyelesaikan
           ↓
       cetak = A001
           ↓
Admin MPP tidak melihat A001
```

Sebaliknya:

```text
Operator/FO MPP -> B001
Admin MPP menyelesaikan
           ↓
       cetak = B001
           ↓
Admin Disduk tidak melihat B001
```

Ini penting agar data antar wilayah tidak bercampur.

---

# Alur Pengajuan

## Alur normal

```text
Input Pengajuan
      ↓
 status = menunggu
      ↓
Monitoring / Verifikasi
      ↓
Selesai
      ↓
    CETAK
status = selesai
```

## Alur hapus

```text
Input Pengajuan
      ↓
 status = menunggu
      ↓
      Hapus
      ↓
   RESTORE
status = hapus
```

## Alur restore

```text
RESTORE
   ↓
 pilih 1 / banyak data
   ↓
 Restore
   ↓
 data dibuat kembali ke sumber
   ↓
 record restore dihapus setelah transaksi berhasil
```

### Aturan Selesai

Aksi **Selesai** memasukkan data ke `cetak` lebih dahulu. Setelah INSERT berhasil, data sumber dihapus dalam transaksi yang sama.

Jika transaksi gagal, perubahan di-rollback sehingga data sumber tidak hilang.

### Aturan Hapus

Aksi **Hapus** memasukkan data ke `restore` terlebih dahulu. Setelah berhasil, data sumber baru dihapus.

---

# Dashboard dan Monitoring

Dashboard menyediakan ringkasan operasional, termasuk:

- total pengajuan;
- jumlah cetak harian;
- rekap jenis pengajuan;
- rekap user;
- informasi wilayah;
- monitoring pengajuan.

Perhitungan yang membutuhkan identitas unik menggunakan:

```text
uuid_pengajuan
```

dengan fallback nomor antrean untuk data lama jika diperlukan.

Tujuannya mencegah satu pengajuan dihitung berkali-kali ketika berpindah dari tabel sumber ke `cetak` atau `restore`.

### Jumlah Cetak Harian

- Group Disduk melihat **Jumlah Cetak Disduk Hari Ini**.
- Group MPP melihat **Jumlah Cetak MPP Hari Ini**.
- Perhitungan berasal dari tabel `cetak`, status `selesai`, dan tanggal `tanggal_selesai` hari berjalan.

---

# Laporan

Menu laporan tersedia bagi role yang diizinkan.

### Harian

Filter berdasarkan tanggal dan menampilkan informasi pengajuan serta total.

### Bulanan

Filter bulan + tahun dan menampilkan rekap per hari.

### Tahunan

Filter tahun dan menampilkan rekap per bulan.

### Semua

Menampilkan data tanpa filter tanggal khusus sesuai scope modul.

PDF laporan dibuat melalui implementasi native aplikasi dan tidak bergantung pada PhpSpreadsheet untuk output PDF.

---

# Master Data

## Master Jenis Pengajuan

Berisi layanan/jenis pengajuan yang dapat digunakan pada form.

Status:

```text
aktif
nonaktif
```

Jenis nonaktif tidak ditampilkan pada form pendaftaran dan divalidasi kembali di server.

## Master Orang Kantor

Kolom utama:

- nama;
- kategori;
- kode Disduk;
- kode MPP;
- status;
- urutan;
- keterangan.

## Master OPD

Kolom utama:

- nama;
- kategori;
- kode Disduk;
- kode MPP;
- status;
- urutan;
- keterangan.

Master menggunakan soft disable melalui status aktif/nonaktif, bukan menghapus data secara langsung dari alur form.

---

# Hak Akses Dinamis

ARK memiliki engine permission untuk role:

- `fo`;
- `fo_mpp`;
- `operator`.

Admin dan Admin MPP adalah **super-role pada engine permission**, sehingga tidak diblokir oleh toggle permission.

Permission dapat mencakup:

- `pendaftaran.masyarakat.view`
- `pendaftaran.masyarakat.input`
- `pendaftaran.orang_kantor.view`
- `pendaftaran.orang_kantor.input`
- `pendaftaran.opd.view`
- `pendaftaran.opd.input`
- `pendaftaran.operator.view`
- `pendaftaran.operator.input`
- `pendaftaran.edit`
- `pendaftaran.hapus`
- `pendaftaran.selesai`
- `cetak.view`
- `cetak.print`
- `pencarian.view`
- `restore.view`
- `restore.action`
- `laporan.view`
- `chat.view`
- `chat.send`
- dan permission lain yang tersedia pada build.

Tabel permission dibuat otomatis oleh aplikasi bila belum tersedia. Instalasi manual juga menyediakan `upgrade_permissions.sql`.

Permission **tidak menghapus pembatasan wilayah A/B**.

---

# Tema dan Tampilan

ARK menggunakan UI berbasis Tailwind CSS dan Feather Icons.

### Tema warna

Pemilih tema menyediakan beberapa tema visual:

- `sky` — Biru Langit
- `emerald` — Emerald
- `violet` — Violet
- `rose` — Rose
- `amber` — Amber

Preferensi tema warna disimpan di `localStorage` sehingga pilihan tetap setelah reload.

### Mode terang/gelap

Mode terang/gelap tersedia sebagai switch pada header.

### Sidebar

Sidebar mendukung:

- collapse/expand;
- penyimpanan status melalui `localStorage`;
- Feather Icons;
- badge notifikasi;
- menu dinamis berdasarkan role dan permission.

---

# Lock Screen dan Session Timeout

ARK menyediakan mekanisme **lock screen** terpisah dari logout.

## FO / FO MPP / Operator

Jika session tidak aktif sampai melewati `session_timeout`:

```text
Session timeout
       ↓
set_account_locked(true, 'timeout')
       ↓
redirect ke lock
       ↓
password akun diminta
       ↓
password benar
       ↓
unlock + kembali ke dashboard
```

Password lock diverifikasi menggunakan `password_verify()` terhadap password akun yang sedang login.

## Admin dan Admin MPP

Admin dan Admin MPP tidak menggunakan timeout otomatis seperti FO/FO MPP/Operator, tetapi mendukung **kunci layar manual**.

Tombol lock dapat ditampilkan pada header di dekat kontrol tema.

Akses manual lock harus mencakup:

```php
['admin', 'admin_mpp']
```

Unlock tetap dilakukan menggunakan password akun yang sedang login.

### Proteksi percobaan password

Halaman lock membatasi percobaan password dalam jendela waktu tertentu untuk mengurangi percobaan berulang secara agresif.

### Prinsip keamanan

Lock screen bukan logout. Session login tetap dipertahankan sampai user benar-benar logout.

---

# Notifikasi

Sidebar menyediakan badge titik merah untuk pengajuan yang masih pending, termasuk:

- Pendaftaran Masyarakat;
- Pendaftaran Operator;
- Pendaftaran Orang Kantor;
- Pendaftaran OPD.

Notifikasi diambil melalui `notifications_api` dan diperbarui dengan polling ringan.

Polling dihentikan/diabaikan saat halaman sedang ditinggalkan untuk mencegah request yang tidak perlu.

Notifikasi juga mempertimbangkan scope Disduk/MPP sehingga data tidak saling tercampur.

---

# Chat Internal

ARK memiliki chat internal pada modul yang diizinkan.

Karakteristik chat:

- pesan teks + emoji;
- maksimal 1000 karakter per pesan;
- pesan memiliki masa berlaku 24 jam;
- cleanup expired message dilakukan otomatis berdasarkan request aplikasi;
- unread badge diperbarui melalui polling;
- membuka chat menandai pesan sebagai dibaca;
- group chat dipisahkan berdasarkan wilayah/grup yang digunakan aplikasi;
- tersedia emoji picker berkategori;
- panel chat dapat diperkecil kembali menjadi tombol floating.

Chat menggunakan:

- CSRF;
- prepared statement;
- escaping output;
- batas panjang input.

Tidak ada upload file/gambar/audio/video pada chat.

---

# Profile

Setiap user memiliki halaman profile.

Fitur meliputi:

- melihat informasi akun;
- mengubah password;
- mengelola foto profile;
- validasi perubahan foto;
- pembatasan pergantian foto untuk role tertentu.

Pada build yang memiliki pembatasan foto mingguan, FO, FO MPP, dan Operator dibatasi maksimal **2 kali pergantian foto per minggu**.

Password form memiliki tombol show/hide password.

---

# Import dan Export

Tersedia pada modul yang diizinkan:

### Import CSV

CSV harus mengikuti **header tabel yang sama persis**.

Template tersedia untuk tabel terkait.

### Export CSV

Data dapat diexport per tabel sesuai fitur yang tersedia.

### Export SQL

Data per tabel dapat diexport dalam format SQL.

### Export XLSX

Build menggunakan pembuatan XLSX native dengan:

- `ZipArchive`;
- XML Office Open XML.

Dengan pendekatan ini, export XLSX tidak wajib menggunakan PhpSpreadsheet/Composer.

---

# Backup Database

Backup Database merupakan fitur khusus Admin utama.

Backup membaca struktur tabel langsung dari MariaDB/MySQL, termasuk tabel chat.

Sebelum upgrade database atau perubahan besar pada source, **selalu buat backup penuh**.

---

# Kesehatan Aplikasi

Menu Kesehatan Aplikasi digunakan Admin utama untuk membantu diagnosis operasional.

Pemeriksaan mencakup antara lain:

- koneksi database;
- versi PHP;
- storage upload;
- storage profile;
- status chat;
- struktur tabel utama.

Modul ini dimaksudkan sebagai pemeriksaan awal sebelum troubleshooting lebih dalam.

---

# Keamanan

ARK menerapkan beberapa lapisan pengamanan:

## Session

- `session.use_strict_mode`;
- session cookie HTTP-only;
- SameSite `Lax`;
- timeout untuk role tertentu;
- lock screen;
- logout eksplisit.

## CSRF

Form POST penting menggunakan token CSRF.

## Password

Password baru menggunakan:

```php
password_hash()
```

dan diverifikasi menggunakan:

```php
password_verify()
```

## SQL Injection

Query database menggunakan **PDO + prepared statement** pada proses dinamis.

## XSS

Output user menggunakan helper escaping:

```php
e($value)
```

## IP Allowed

Login dapat dibatasi berdasarkan daftar IP yang diizinkan.

## Scope Wilayah

Data operasional dibatasi berdasarkan prefix A/B dan/atau aturan wilayah yang diterapkan oleh aplikasi.

## Anti-Duplikasi

Sistem memiliki pengecekan duplikasi pada pendaftaran dan NIK sesuai aturan modul.

## UUID

`uuid_pengajuan` menjadi identitas permanen agar histori lintas tabel tetap konsisten.

## Nomor Antrean

Generator nomor antrean menggunakan proteksi database/lock pada jalur yang memerlukannya agar dua user tidak memperoleh nomor yang sama.

## Upload Profile

Folder upload tidak boleh menjadi tempat eksekusi PHP.

## Security Headers

Header keamanan diterapkan melalui helper aplikasi dan konfigurasi deployment yang tersedia.

> Jangan menyimpan credential database, password, `.env`, backup database produksi, atau data pelayanan nyata di repository GitHub publik.

---

# Teknologi

| Komponen | Teknologi |
|---|---|
| Backend | PHP 7.4.x — 8.0.x |
| Database | MariaDB / MySQL |
| Database access | PDO |
| Frontend | HTML5 + Tailwind CSS |
| Icons | Feather Icons |
| Table | simple-datatables / DataTables pada modul terkait |
| JavaScript | Vanilla JavaScript |
| PDF | Native/simple PDF implementation |
| CSV | PHP native |
| XLSX | ZipArchive + XML Open XML |
| Web server | Apache / XAMPP |
| Timezone | Asia/Jakarta |

---

# Struktur Direktori

Struktur penting pada build ini:

```text
ARK/
├── assets/
│   ├── css/
│   │   └── ark-themes.css
│   ├── img/
│   │   └── profile-default.svg
│   └── js/
│       └── session.js
│
├── config/
│   ├── config.php
│   └── database.php
│
├── includes/
│   ├── auth_timeout.php
│   ├── chat_widget.php
│   ├── footer.php
│   ├── functions.php
│   ├── header.php
│   └── sidebar.php
│
├── templates/
│   └── README.md
│
├── tools/
│   ├── hash_password.php
│   ├── reset_admin.php
│   └── runtime/
│
├── uploads/
│
├── aktivitas_pengguna.php
├── audit_log.php
├── audit_log_action.php
├── backup_database.php
├── cetak.php
├── chat_api.php
├── csv_export.php
├── csv_import.php
├── csv_template.php
├── dashboard.php
├── database.sql
├── database_final.sql
├── forbidden.php
├── import_export.php
├── index.php
├── ip_alloweds.php
├── ip_alloweds_action.php
├── kesehatan_aplikasi.php
├── laporan.php
├── laporan_pdf.php
├── lock.php
├── lock_action.php
├── login.php
├── logout.php
├── master_jenis_pengajuan.php
├── master_opd.php
├── master_orang_kantor.php
├── monitoring_pengajuan.php
├── notifications_api.php
├── pencarian.php
├── pendaftaran.php
├── pendaftaran_action.php
├── pengaturan.php
├── pengaturan_action.php
├── pengguna.php
├── pengguna_action.php
├── permission.php
├── profile.php
├── profile_action.php
├── restore.php
├── restore_action.php
├── riwayat_action.php
├── riwayat_pengajuan.php
├── simple_pdf.php
├── sql_export.php
├── tentang.php
├── tutorial.php
└── xlsx_export.php
```

> Beberapa file dokumentasi, migration/upgrade SQL, dan metadata deployment juga berada di root.

---

# Struktur Database

Database referensi instalasi baru menggunakan nama:

```text
regis_ktp
```

## Tabel User dan Sistem

```text
pengguna
ip_alloweds
pengaturan
audit_log
user_activity_log
role_permissions
user_permissions
profile_photo_changes
```

## Tabel Pengajuan

```text
daftar
daftar_operator
daftar_orang_kantor
daftar_opd
```

## Tabel Hasil / Arsip

```text
cetak
restore
pengajuan_riwayat
```

## Master

```text
master_jenis_pengajuan
master_orang_kantor
master_opd
```

## Chat

```text
chat_messages
chat_reads
```

### Kolom Penting

Beberapa kolom penting pada build:

- `uuid_pengajuan`
- `nama_entitas`
- `asal_tabel`
- `tanggal_selesai`
- `diselesaikan_oleh`
- `waktu_hapus`
- `dihapus_oleh`
- `sumber_username`
- `wilayah`
- `session_timeout`

---

# Instalasi

## 1. Persyaratan

Pastikan tersedia:

- XAMPP atau Apache;
- PHP `7.4.x` — `8.0.x`;
- MariaDB/MySQL;
- extension PHP yang dibutuhkan aplikasi, termasuk PDO dan ZipArchive untuk fitur tertentu.

## 2. Copy Source

Extract project ke:

```text
C:\xampp\htdocs\ark
```

Contoh URL:

```text
http://localhost/ark/
```

Untuk LAN:

```text
http://192.168.x.x/ark/
```

## 3. Buat Database

Import:

```text
 database_final.sql
```

`database_final.sql` merupakan SQL referensi untuk instalasi baru.

## 4. Konfigurasi Database

Edit:

```text
config/config.php
config/database.php
```

Contoh konfigurasi:

```php
define('DB_HOST', '127.0.0.1');
define('DB_NAME', 'regis_ktp');
define('DB_USER', 'root');
define('DB_PASS', '');
```

## 5. Login Pertama

Database referensi menyediakan akun:

```text
Username : admin
Password : password
```

**Segera ganti password setelah login pertama.**

## 6. IP Allowed

Tambahkan IP PC yang diizinkan pada menu **IP Allowed** sebelum penggunaan operasional jika kebijakan IP aktif.

## 7. Uji Modul Dasar

Lakukan uji:

```text
Login
→ Dashboard
→ Pendaftaran
→ Selesai
→ Cetak
→ Hapus
→ Restore
→ Pencarian
→ Logout
```

---

# Konfigurasi

## Konstanta Utama

Pada `config/config.php` terdapat konfigurasi utama seperti:

```php
define('APP_NAME', 'REGIS KTP');
define('APP_VERSION', '11.2.1899');
define('MIN_PHP_VERSION', '7.4.0');
define('MAX_PHP_VERSION', '8.0.99');
define('SESSION_DEFAULT_TIMEOUT', 900);
define('OPERATOR_DAILY_LIMIT', 3);
```

### Session Default Timeout

```text
900 detik = 15 menit
```

Nilai timeout user dapat disimpan pada kolom `session_timeout`.

Admin dan Admin MPP menggunakan perilaku tanpa timeout otomatis pada build lock-screen saat ini.

---

# URL Bersih

ARK menggunakan rewrite Apache sehingga `.php` dapat disembunyikan.

Contoh:

```text
/dashboard
/pendaftaran
/cetak
/restore
/pencarian
/laporan
/pengguna
/profile
```

bukan:

```text
/dashboard.php
/pendaftaran.php
/cetak.php
```

`.htaccess` berada di root aplikasi.

---

# Konfigurasi Apache untuk XAMPP

Jika URL bersih menghasilkan `Not Found`, buka:

```text
C:\xampp\apache\conf\httpd.conf
```

Pastikan `mod_rewrite` aktif:

```apache
LoadModule rewrite_module modules/mod_rewrite.so
```

Pastikan directory htdocs mengizinkan override:

```apache
<Directory "C:/xampp/htdocs">
    AllowOverride All
    Require all granted
</Directory>
```

Setelah mengubah konfigurasi:

```text
Restart Apache
```

Jangan mengubah `DocumentRoot` hanya untuk fitur URL rewrite.

---

# IP Allowed

ARK mendukung pembatasan login berdasarkan `REMOTE_ADDR`.

Konsepnya:

```text
IP belum terdaftar / nonaktif
        ↓
login diblokir

IP terdaftar + aktif
        ↓
login dapat dilanjutkan
```

Menu IP Allowed dapat digunakan Admin untuk:

- menambah IP;
- mengaktifkan/nonaktifkan IP;
- melihat informasi IP;
- membantu user mengetahui `REMOTE_ADDR` PC.

Untuk aplikasi LAN, `REMOTE_ADDR` server adalah sumber IP yang digunakan oleh aplikasi.

---

# Login dan Password

Login menggunakan:

```php
password_verify()
```

Password disimpan dalam bentuk hash.

Untuk kompatibilitas dengan beberapa database lama, build tertentu dapat melakukan migrasi password lama ke hash ketika login berhasil, sesuai logic yang tersedia pada `login.php`.

## Reset Admin Darurat

Tersedia:

```text
tools/reset_admin.php
```

Gunakan hanya saat diperlukan, lalu **hapus file tersebut** setelah selesai agar endpoint administratif darurat tidak tertinggal pada server.

---

# Upgrade Database

Untuk database existing, lakukan backup terlebih dahulu.

File yang tersedia antara lain:

```text
upgrade_database.sql
upgrade_permissions.sql
upgrade_cetak.sql
upgrade_access_final.sql
repair_scope_wilayah.sql
repair_scope_wilayah_final.sql
```

Urutan upgrade mengikuti kebutuhan versi database yang sedang digunakan.

### Aturan Aman

```text
BACKUP
  ↓
backup SQL penuh
  ↓
copy source lama
  ↓
upgrade database
  ↓
uji pada local/staging
  ↓
baru digunakan pada pelayanan aktif
```

---

# Troubleshooting

## 1. URL `/dashboard` Not Found

Periksa:

- `mod_rewrite` aktif;
- `AllowOverride All`;
- `.htaccess` berada di root aplikasi;
- Apache sudah direstart.

## 2. Login Ditolak karena IP

Periksa:

- IP PC melalui `REMOTE_ADDR`;
- menu IP Allowed;
- status IP harus aktif.

## 3. Notifikasi Tidak Muncul

Periksa:

- `notifications_api`;
- query pending;
- badge ID pada sidebar;
- scope wilayah;
- polling JavaScript;
- session tidak tertahan oleh request polling.

## 4. Data Operator A Muncul pada Cetak MPP

Periksa:

- nomor antrean yang tersimpan pada `cetak`;
- prefix A/B;
- logic scope pada proses `Selesai`;
- query filter pada `cetak.php`;
- `data_scope_prefix()`;
- master kode Operator/Orang Kantor/OPD.

Prinsip yang benar:

```text
A → hanya scope A
B → hanya scope B
```

## 5. Admin MPP Tidak Bisa Lock Manual

Pastikan daftar role pada semua titik lock menggunakan:

```php
['admin', 'admin_mpp']
```

Periksa minimal:

- `header.php` untuk tombol;
- `functions.php` untuk `lock_supported_for_current_user()`;
- `lock_action.php` untuk authorization backend.

Tampilan tombol saja tidak cukup; backend harus mengizinkan `admin_mpp`.

## 6. Lock Screen Tidak Membuka

Periksa:

- password akun valid;
- akun berstatus aktif;
- `password_verify()`;
- session `ark_account_locked`;
- token CSRF;
- redirect ke route `lock`.

## 7. Pendaftaran Mengalami Loading Lama / Hang

Periksa:

- JavaScript Select All/DataTables;
- jangan memasang `MutationObserver` global pada `document.body`;
- polling notifikasi tidak boleh menahan session;
- pastikan request AJAX mengembalikan JSON valid.

## 8. Restore Gagal

Periksa:

- `ids[]` dikirim;
- source table valid;
- `uuid_pengajuan` tersedia;
- `nama_entitas` tersedia bila diperlukan;
- `asal_tabel` tersedia;
- transaksi database.

## 9. Selesai Tidak Masuk Cetak

Periksa:

- struktur tabel `cetak`;
- kolom `tanggal_selesai`;
- `diselesaikan_oleh`;
- `uuid_pengajuan`;
- `asal_tabel`;
- query INSERT dan transaksi.

---

# Checklist Setelah Instalasi

Gunakan checklist berikut sebelum aplikasi digunakan:

- [ ] PHP versi 7.4.x–8.0.x
- [ ] MariaDB/MySQL aktif
- [ ] Database `regis_ktp` berhasil diimport
- [ ] `config/config.php` sesuai lingkungan
- [ ] Apache `mod_rewrite` aktif
- [ ] `AllowOverride All` aktif
- [ ] `.htaccess` terbaca
- [ ] Login admin berhasil
- [ ] Password admin diganti
- [ ] IP Allowed sudah benar
- [ ] Dashboard tampil
- [ ] Pendaftaran Masyarakat berhasil
- [ ] Pendaftaran Operator berhasil
- [ ] Pendaftaran Orang Kantor berhasil
- [ ] Pendaftaran OPD berhasil
- [ ] Selesai → Cetak berhasil
- [ ] Hapus → Restore berhasil
- [ ] Restore satu data berhasil
- [ ] Restore multi-select berhasil
- [ ] Pencarian menemukan data lama
- [ ] Scope A/B benar
- [ ] Notifikasi muncul sesuai wilayah
- [ ] Chat berjalan sesuai permission
- [ ] Tema tersimpan setelah reload
- [ ] Lock FO/FO MPP/Operator berjalan saat timeout
- [ ] Lock manual Admin berjalan
- [ ] Lock manual Admin MPP berjalan
- [ ] Unlock menggunakan password akun
- [ ] Export CSV berjalan
- [ ] Export XLSX berjalan
- [ ] Laporan berjalan
- [ ] Backup database diuji
- [ ] Menu Kesehatan Aplikasi diuji
- [ ] Tool reset admin dihapus setelah digunakan

---

# Catatan Pengembangan

## Jangan Mengganti `includes/functions.php` Secara Sembarangan

`includes/functions.php` adalah salah satu file inti ARK dan memuat banyak helper untuk:

- authentication;
- permission;
- scope;
- notifikasi;
- chat;
- queue;
- validasi;
- session timeout;
- lock screen;
- utility database.

Saat melakukan patch fitur baru, lebih aman melakukan **merge perubahan secara selektif** daripada mengganti seluruh file dengan versi lain.

## Prinsip Perubahan Source

1. Backup source.
2. Backup database.
3. Ubah file seminimal mungkin.
4. Jalankan syntax check PHP.
5. Uji login.
6. Uji pendaftaran.
7. Uji selesai/cetak.
8. Uji restore.
9. Uji notifikasi.
10. Uji scope A/B.
11. Uji permission.
12. Uji lock screen.

---

# Riwayat Revisi

## Foundation

- PHP 7.4.x–8.0.x.
- PDO untuk database.
- Tailwind CSS.
- CRUD pendaftaran.
- Authentication.
- IP Allowed.
- CSV/SQL export.

## Security Hardening

- CSRF protection.
- Prepared statement.
- Security headers.
- Session hardening.
- Upload hardening.
- Output escaping.
- UUID pengajuan.
- Scope A/B.
- Audit Log.

## Workflow

- `daftar` → `cetak` untuk Selesai.
- `daftar` → `restore` untuk Hapus.
- Restore dapat mengembalikan data ke sumber.
- Riwayat dan audit mempertahankan jejak aktivitas.

## Master

- Master Jenis Pengajuan.
- Master Orang Kantor.
- Master OPD.
- Toggle aktif/nonaktif.
- Validasi master di server.

## Operator

- Tabel `daftar_operator` terpisah.
- Maksimal 3 input per hari.
- Admin/Admin MPP monitoring read-only.
- Prefix wilayah mengikuti scope.

## Dashboard

- Rekap berdasarkan jenis.
- Rekap berdasarkan user.
- Jumlah cetak harian.
- Total harian tanpa double count.
- Scope dashboard A/B.

## Search

- Pencarian lintas tabel.
- Tanpa batas tahun.
- Pagination server-side pada modul terkait.

## Chat

- Internal chat.
- Emoji picker.
- Unread badge.
- Expire 24 jam.
- Auto cleanup.

## Theme

- Multiple color themes.
- Light/dark mode.
- Persist melalui localStorage.

## Lock Screen

- Auto-lock untuk FO, FO MPP, Operator berdasarkan timeout.
- Manual lock untuk Admin dan Admin MPP.
- Unlock menggunakan password akun aktif.

## Scope Correction

- Perbaikan pemisahan data Operator A dan B.
- Cetak mengikuti scope wilayah.
- Nomor antrean sumber tetap menjadi dasar pemisahan wilayah.

---

# Struktur Alur Data Singkat

```text
                    ┌──────────────────┐
                    │ Pendaftaran      │
                    └────────┬─────────┘
                             │
            ┌────────────────┼─────────────────┐
            │                │                 │
            ▼                ▼                 ▼
       Masyarakat        Operator        Orang Kantor
            │                │                 │
            │                │                 └──────┐
            │                │                        │
            └────────────────┼────────────────────────┘
                             │
                             ▼
                        OPD / Instansi
                             │
                             ▼
                      status menunggu
                             │
              ┌──────────────┴──────────────┐
              │                             │
              ▼                             ▼
           Selesai                         Hapus
              │                             │
              ▼                             ▼
           `cetak`                        `restore`
              │                             │
              ▼                             ▼
          Cetak / histori                 Restore
```

---

# Prinsip Arsitektur

ARK menggunakan pendekatan server-rendered PHP:

```text
Browser
   ↓
Apache / XAMPP
   ↓
PHP page / action
   ↓
PDO
   ↓
MariaDB / MySQL
```

Interaksi tambahan:

```text
PHP
├── Tailwind / HTML
├── JavaScript
├── Feather Icons
├── DataTables / simple-datatables
├── PDF export
├── CSV export/import
├── XLSX native export
├── Notification API
└── Chat API
```

Prinsip desain utama:

- UI dan helper terpusat;
- data operasional dipisahkan per jalur;
- wilayah A/B tidak boleh bercampur;
- UUID menjaga identitas pengajuan;
- permission mengatur akses fitur;
- database transaction menjaga konsistensi perpindahan data;
- audit menjaga jejak aktivitas;
- backup menjadi bagian dari pemeliharaan aplikasi.

---

# Deployment Production / Internal LAN

Sebelum deployment:

1. ganti password default;
2. periksa IP Allowed;
3. backup database;
4. jangan commit data pelayanan nyata ke GitHub;
5. jangan commit credential database;
6. pastikan folder upload tidak dapat mengeksekusi PHP;
7. gunakan HTTPS jika aplikasi dipasang pada jaringan/lingkungan yang memerlukannya;
8. uji seluruh alur pada PC staging terlebih dahulu.

---

# Lisensi

Repository ini disiapkan sebagai source aplikasi internal **ARK — Aplikasi Register KTP**.

Gunakan, modifikasi, dan distribusikan kembali hanya sesuai kebijakan organisasi/pemilik source code dan data yang berlaku.

---

## Penutup

ARK bukan hanya modul input register. Sistem ini mencakup:

```text
Multi Role
   +
Multi Wilayah A/B
   +
Pendaftaran
   +
Cetak
   +
Restore
   +
Pencarian
   +
Dashboard
   +
Laporan
   +
Master Data
   +
Permission
   +
Notifikasi
   +
Chat
   +
Audit
   +
Backup
   +
Health Check
   +
Theme System
   +
Lock Screen
   +
Security Hardening
```

**ARK dirancang dengan prinsip utama: data tidak bercampur antar wilayah, setiap pengajuan tetap dapat ditelusuri, dan perubahan operasional dapat diaudit.**

---

**ARK — Aplikasi Register KTP**  
Internal Register & Monitoring System
