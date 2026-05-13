# Praktikum 2 — DML: Mengisi & Mengelola Data

## Deskripsi

Database sudah dirancang, sekarang saatnya **mengisi data** dan mulai mengoperasikan sistem. PM memberikan update:

> *"Database-nya sudah jadi, bagus! Sekarang kita perlu mengisi data awal — data master, data transaksi percobaan. Tim QA juga minta kalian simulasikan skenario update dan hapus data. Oh ya, nanti ada juga data historis yang perlu di-import dari file CSV."*
> — Project Manager

## Tujuan Pembelajaran

Setelah menyelesaikan praktikum ini, mahasiswa mampu:

1. Memasukkan data menggunakan **INSERT** (single & multiple rows)
2. Memperbarui data menggunakan **UPDATE** dengan kondisi **WHERE**
3. Menghapus data menggunakan **DELETE** dengan kondisi **WHERE**
4. Memverifikasi data menggunakan **SELECT**
5. Mengimpor data dari file **CSV** melalui phpMyAdmin

## Persiapan (Sebelum Praktikum)

Baca studi kasus kelompok kalian, khususnya bagian **Praktikum 2**. Siapkan **catatan tulis tangan** berisi:

### Query yang Harus Disiapkan

1. `INSERT INTO` — minimal **3 baris** untuk setiap tabel (sesuai studi kasus)
2. `INSERT INTO ... VALUES` (multiple rows) — insert beberapa baris sekaligus
3. `UPDATE ... SET ... WHERE` — **3 skenario** koreksi data
4. `DELETE FROM ... WHERE` — **1–2 skenario** hapus data
5. `SELECT COUNT(*)` — verifikasi jumlah data semua tabel dengan UNION ALL

> **Peringatan:** Selalu gunakan **WHERE** pada UPDATE dan DELETE. Tanpa WHERE, **semua baris** akan terkena dampak!

## Konsep Penting

### INSERT — Memasukkan Data

```sql
-- Insert satu baris
INSERT INTO customers (name, email, phone)
VALUES ('Budi Santoso', 'budi@email.com', '081234567890');

-- Insert beberapa baris sekaligus
INSERT INTO customers (name, email, phone) VALUES
('Ani Wijaya', 'ani@email.com', '081234567891'),
('Citra Dewi', 'citra@email.com', '081234567892'),
('Doni Pratama', 'doni@email.com', '081234567893');
```

**Catatan:**
- Kolom `AUTO_INCREMENT` **tidak perlu** diisi (otomatis)
- Kolom dengan `DEFAULT` boleh dilewati
- Kolom `NOT NULL` tanpa default **wajib** diisi
- Urutan kolom di INSERT harus **sama** dengan urutan values

### UPDATE — Memperbarui Data

```sql
-- Update satu kolom
UPDATE customers
SET phone = '089876543210'
WHERE id = 1;

-- Update beberapa kolom sekaligus
UPDATE products
SET price = 150000, stock = 50
WHERE id = 5;

-- Update dengan kondisi lebih spesifik
UPDATE orders
SET status = 'cancelled'
WHERE status = 'pending' AND created_at < '2025-01-01';
```

**Peringatan:**
```sql
-- BERBAHAYA! Update SEMUA baris
UPDATE products SET price = 0;

-- AMAN! Update baris tertentu saja
UPDATE products SET price = 0 WHERE id = 99;
```

### DELETE — Menghapus Data

```sql
-- Hapus baris tertentu
DELETE FROM customers WHERE id = 10;

-- Hapus dengan kondisi
DELETE FROM orders WHERE status = 'cancelled';
```

**Catatan tentang Foreign Key:**
- Tidak bisa menghapus baris di tabel induk jika masih ada data di tabel anak yang mereferensinya
- Hapus data di tabel **anak** terlebih dahulu, baru tabel **induk**

### SELECT — Memverifikasi Data

```sql
-- Lihat semua data
SELECT * FROM customers;

-- Lihat data tertentu
SELECT name, email FROM customers WHERE id = 1;

-- Hitung jumlah baris
SELECT COUNT(*) FROM customers;
```

## Tugas

Lihat bagian **Praktikum 2** di studi kasus kelompok kalian untuk daftar tugas spesifik.

Secara umum:

1. Insert data **semua tabel** sesuai urutan FK (min 3 baris per tabel)
2. Lakukan **3 skenario UPDATE** sesuai studi kasus
3. Lakukan **1–2 skenario DELETE** sesuai studi kasus (perhatikan urutan anak → induk)
4. Verifikasi jumlah data semua tabel dengan `SELECT COUNT(*) ... UNION ALL`

## Pertanyaan Diskusi

1. Apa yang terjadi jika kita memasukkan data dengan foreign key yang tidak ada di tabel referensi?
2. Mengapa `UPDATE` dan `DELETE` tanpa `WHERE` itu berbahaya?
3. Apa perbedaan antara `DELETE` dan `TRUNCATE`?
4. Bagaimana cara mengembalikan data yang sudah dihapus dengan `DELETE`?
5. Apa kelebihan insert multiple rows dibanding insert satu per satu?

## Referensi

- [MySQL INSERT](https://dev.mysql.com/doc/refman/8.0/en/insert.html)
- [MySQL UPDATE](https://dev.mysql.com/doc/refman/8.0/en/update.html)
- [MySQL DELETE](https://dev.mysql.com/doc/refman/8.0/en/delete.html)
- [MySQL LOAD DATA](https://dev.mysql.com/doc/refman/8.0/en/load-data.html)
