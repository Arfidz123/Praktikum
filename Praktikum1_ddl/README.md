# Praktikum 1 — DDL: Merancang Database

## Deskripsi

Pada praktikum ini, kalian akan berperan sebagai **Database Engineer** yang baru saja bergabung di sebuah perusahaan. Seorang **Project Manager (PM)** memperkenalkan project baru dan meminta kalian merancang struktur database dari nol.

> *"Selamat datang di tim! Kita baru saja mendapat project baru. Saya sudah menyiapkan dokumen kebutuhannya. Tugas pertama kalian adalah merancang database-nya. Pastikan strukturnya solid — ini fondasi dari seluruh sistem kita."*
> — Project Manager

## Tujuan Pembelajaran

Setelah menyelesaikan praktikum ini, mahasiswa mampu:

1. Membuat **database** baru di MySQL
2. Merancang dan membuat **tabel** dengan tipe data yang tepat
3. Menerapkan **constraint** untuk menjaga integritas data
4. Memahami relasi antar tabel (**one-to-many**, **many-to-many**)
5. Menggambar **Entity Relationship Diagram (ERD)** sederhana

## Persiapan (Sebelum Praktikum)

Baca studi kasus kelompok kalian di folder `studi-kasus/`. Siapkan **catatan tulis tangan** berisi:

### Query yang Harus Disiapkan

1. `CREATE DATABASE` — membuat database sesuai studi kasus
2. `USE` — memilih database
3. `CREATE TABLE` — untuk **setiap tabel** dalam studi kasus (6-8 tabel)
   - Tentukan **tipe data** setiap kolom
   - Tentukan **constraint** yang sesuai
4. `SHOW TABLES` dan `DESCRIBE` — untuk verifikasi
5. `ALTER TABLE` — jika perlu modifikasi setelah verifikasi
6. `DROP TABLE` — jika perlu membuat ulang tabel

> **Tips:** Buat tabel **induk** (yang tidak punya foreign key) terlebih dahulu, baru tabel **anak** (yang mereferensi tabel lain).
>
> **Kasus khusus:** Jika ada **referensi melingkar** (tabel A FK ke B, tabel B FK ke A), buat salah satu tabel tanpa FK terlebih dahulu, lalu tambahkan FK-nya dengan `ALTER TABLE` setelah kedua tabel selesai dibuat.

## Konsep Penting

### Tipe Data Umum di MySQL

| Tipe Data | Kegunaan | Contoh |
|-----------|----------|--------|
| `INT` | Bilangan bulat | id, jumlah, stok |
| `BIGINT` | Bilangan bulat besar | nomor telepon |
| `DECIMAL(p,s)` | Angka presisi (uang) | harga, total |
| `VARCHAR(n)` | Teks variabel | nama, email, alamat |
| `TEXT` | Teks panjang | deskripsi, catatan |
| `DATE` | Tanggal | tanggal_lahir |
| `DATETIME` | Tanggal dan waktu | created_at |
| `TIMESTAMP` | Timestamp otomatis | updated_at |
| `ENUM(...)` | Pilihan terbatas | status, jenis_kelamin |
| `BOOLEAN` | True/False | is_active |

### Constraint

| Constraint | Fungsi | Contoh |
|------------|--------|--------|
| `PRIMARY KEY` | Identitas unik baris | `id INT PRIMARY KEY` |
| `AUTO_INCREMENT` | ID otomatis naik | `id INT AUTO_INCREMENT` |
| `NOT NULL` | Wajib diisi | `nama VARCHAR(100) NOT NULL` |
| `UNIQUE` | Tidak boleh duplikat | `email VARCHAR(100) UNIQUE` |
| `DEFAULT` | Nilai bawaan | `status ENUM('aktif') DEFAULT 'aktif'` |
| `CHECK` | Validasi nilai | `CHECK (harga >= 0)` |
| `FOREIGN KEY` | Relasi antar tabel | `FOREIGN KEY (customer_id) REFERENCES customers(id)` |

### Contoh Sintaks CREATE TABLE

```sql
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(200) NOT NULL,
    price DECIMAL(12,2) NOT NULL CHECK (price >= 0),
    stock INT NOT NULL DEFAULT 0,
    category_id INT NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (category_id) REFERENCES categories(id)
);
```

## Tahapan Praktikum

### Tahap 1: Pahami Studi Kasus (10 menit)

1. Buka studi kasus kelompok kalian
2. Identifikasi **entitas** (tabel) yang dibutuhkan
3. Identifikasi **atribut** (kolom) setiap entitas
4. Identifikasi **relasi** antar entitas

### Tahap 2: Gambar ERD (15 menit)

1. Gambar ERD sederhana di kertas atau whiteboard
2. Tandai **Primary Key (PK)** dan **Foreign Key (FK)**
3. Tandai **kardinalitas** relasi (1:1, 1:N, M:N)
4. Diskusikan dengan kelompok — apakah ada yang perlu ditambah/diubah?

### Tahap 3: Tulis & Eksekusi DDL (45 menit)

1. Login ke phpMyAdmin
2. Buat database baru:

```sql
CREATE DATABASE nama_database;
USE nama_database;
```

3. Buat tabel satu per satu, mulai dari tabel **induk**:
   - Tabel tanpa foreign key → dibuat pertama
   - Tabel dengan foreign key → dibuat setelah tabel yang direferensi

4. Verifikasi struktur:

```sql
SHOW TABLES;
DESCRIBE nama_tabel;
```

5. Jika ada kesalahan, gunakan `ALTER TABLE` atau `DROP TABLE`:

```sql
-- Menambah kolom
ALTER TABLE products ADD COLUMN description TEXT;

-- Mengubah tipe data
ALTER TABLE products MODIFY COLUMN name VARCHAR(255) NOT NULL;

-- Menghapus kolom
ALTER TABLE products DROP COLUMN description;

-- Menghapus tabel (hati-hati!)
DROP TABLE IF EXISTS nama_tabel;
```

### Tahap 4: Verifikasi & Dokumentasi (10 menit)

1. Jalankan `SHOW CREATE TABLE` untuk setiap tabel
2. Pastikan semua constraint sudah benar
3. Screenshot hasil sebagai dokumentasi

```sql
SHOW CREATE TABLE nama_tabel;
```

## Tugas

Lihat bagian **Praktikum 1** di studi kasus kelompok kalian untuk daftar tugas spesifik.

Secara umum, kalian harus:

1. Membuat **database** baru
2. Membuat **semua tabel** sesuai spesifikasi (6-8 tabel)
3. Menerapkan **semua constraint** yang diminta
4. Memverifikasi struktur dengan `DESCRIBE` dan `SHOW CREATE TABLE`

## Referensi

- [MySQL CREATE TABLE](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)
- [MySQL Data Types](https://dev.mysql.com/doc/refman/8.0/en/data-types.html)
- [MySQL Constraints](https://dev.mysql.com/doc/refman/8.0/en/constraints.html)
