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

## ❤️ ARK — Aplikasi Register KTP

Sistem register dan monitoring pelayanan KTP yang dirancang agar proses pencatatan, pengelolaan, pencarian, pelaporan, dan histori pengajuan menjadi lebih **rapi, terukur, dan mudah digunakan**.
