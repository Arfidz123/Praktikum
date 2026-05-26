# Praktikum 4 — View & Subquery: Menyederhanakan Akses Data

## Deskripsi

Data terus bertambah dan tim analytics mulai kesulitan menulis ulang query panjang yang sama berulang kali. Saatnya membuat **jalan pintas** ke data yang paling sering dibutuhkan.

> *"Query-query laporan kita sudah bagus, tapi setiap orang di tim harus nulis ulang query JOIN yang panjang itu setiap kali mau lihat data. Bisakah kalian buat semacam 'tabel virtual' yang langsung menampilkan data siap pakai? Oh ya, manajemen juga minta analisis yang lebih dalam — misalnya produk mana yang harganya di atas rata-rata, atau departemen mana yang performanya di atas rata-rata semua departemen."*
> — Project Manager

## Tujuan Pembelajaran

Setelah menyelesaikan praktikum ini, mahasiswa mampu:

1. Membuat **VIEW** untuk menyimpan query kompleks yang sering digunakan
2. Mengakses, mem-filter, dan mengurutkan data dari **VIEW**
3. Menulis **subquery di WHERE** untuk membandingkan nilai terhadap aggregate
4. Menulis **subquery di FROM** (derived table) untuk analisis bertingkat
5. Menulis **subquery di HAVING** untuk memfilter kelompok secara dinamis

## Persiapan (Sebelum Praktikum)

Baca studi kasus kelompok kalian, khususnya bagian **Praktikum 4**. Siapkan **catatan tulis tangan** berisi:

### Query yang Harus Disiapkan

1. `CREATE VIEW` — **3 view** sesuai skenario di studi kasus
2. `SELECT * FROM nama_view` — 3 query mengakses view (dengan filter/ORDER BY)
3. Subquery **di WHERE** — 2 query perbandingan terhadap nilai rata-rata/maksimum
4. Subquery **di FROM** (nested subquery) — 2 query analisis bertingkat

> **Tips:** Subquery di P4 biasanya berbentuk `WHERE nilai > (SELECT AVG(...) FROM ...)` atau `HAVING jumlah > (SELECT AVG(cnt) FROM (SELECT COUNT(*) ... GROUP BY ...) AS sub)`. Latih pola ini.

## Konsep Penting

### VIEW — Query Tersimpan

View adalah **query SELECT yang disimpan** sebagai objek database. Setelah dibuat, view bisa diakses seperti tabel biasa — termasuk di-filter, di-join, atau dijadikan sumber subquery.

```sql
-- Membuat view laporan penjualan
CREATE VIEW v_laporan_penjualan AS
SELECT
    o.id          AS order_id,
    c.name        AS pelanggan,
    o.order_date,
    COUNT(oi.id)  AS total_item,
    SUM(oi.quantity * oi.price) AS total_harga
FROM orders o
JOIN customers  c  ON o.customer_id = c.id
JOIN order_items oi ON o.id = oi.order_id
GROUP BY o.id, c.name, o.order_date;

-- Menggunakan view seperti tabel biasa
SELECT * FROM v_laporan_penjualan;

-- Filter dan urutkan dari view
SELECT * FROM v_laporan_penjualan
WHERE total_harga > 500000
ORDER BY total_harga DESC;

-- Hapus view jika perlu membuat ulang
DROP VIEW IF EXISTS v_laporan_penjualan;
```

**Kapan membuat VIEW?**

| Situasi | Gunakan VIEW |
|---------|--------------|
| Query JOIN panjang yang sering dipakai ulang | ✓ |
| Laporan standar yang diakses banyak orang | ✓ |
| Menyederhanakan tampilan data untuk pengguna lain | ✓ |
| Query yang hanya dipakai sekali | ✗ |

**Catatan penting:**
- VIEW **tidak menyimpan data** — setiap kali diakses, query aslinya dijalankan ulang
- Jika tabel sumber diubah (kolom dihapus, dll), view bisa menjadi invalid
- Cek daftar view yang sudah ada: `SHOW FULL TABLES WHERE Table_type = 'VIEW';`

### Subquery — Query di Dalam Query

Subquery adalah **query SELECT yang diletakkan di dalam query lain**. Di Praktikum 3 kita sudah menggunakan subquery sederhana. Di praktikum ini kita fokus pada subquery yang lebih kompleks.

#### Subquery di WHERE — Perbandingan Dinamis

```sql
-- Tampilkan produk dengan harga DI ATAS rata-rata
SELECT name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- Tampilkan pelanggan yang total belinya DI ATAS rata-rata
SELECT c.name, SUM(o.total) AS total_beli
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name
HAVING SUM(o.total) > (
    SELECT AVG(total_per_customer)
    FROM (
        SELECT SUM(total) AS total_per_customer
        FROM orders
        GROUP BY customer_id
    ) AS sub
);
```

#### Subquery di FROM — Derived Table (Tabel Sementara)

Subquery di klausa `FROM` menghasilkan **tabel sementara** yang bisa diquery lebih lanjut. Wajib diberi alias.

```sql
-- Subquery satu level: rata-rata harga per kategori
SELECT category_id, avg_price
FROM (
    SELECT category_id, AVG(price) AS avg_price
    FROM products
    GROUP BY category_id
) AS stats_per_kategori
WHERE avg_price > 100000;

-- Subquery dua level: cari nilai maksimum dari hasil rata-rata
SELECT *
FROM products
WHERE price = (
    SELECT MAX(avg_per_cat)
    FROM (
        SELECT AVG(price) AS avg_per_cat
        FROM products
        GROUP BY category_id
    ) AS avg_table
);
```

#### Subquery Correlated — Merujuk Query Luar

Subquery correlated dieksekusi **untuk setiap baris** di query luar karena merujuk kolom dari tabel luar.

```sql
-- Tampilkan produk dengan harga di atas rata-rata kategorinya sendiri
SELECT p.name, p.price, p.category_id
FROM products p
WHERE p.price > (
    SELECT AVG(p2.price)
    FROM products p2
    WHERE p2.category_id = p.category_id  -- merujuk ke baris luar
);
```

### Pola Subquery yang Sering Muncul di Studi Kasus

| Pola | Contoh Kegunaan |
|------|-----------------|
| `WHERE x > (SELECT AVG(x) FROM t)` | Nilai/harga di atas rata-rata |
| `HAVING cnt > (SELECT AVG(cnt) FROM (SELECT COUNT(*) ... GROUP BY ...) AS s)` | Kelompok yang jumlahnya di atas rata-rata |
| `WHERE x = (SELECT MAX(avg) FROM (SELECT AVG(x) ... GROUP BY ...) AS s)` | Kelompok dengan nilai rata-rata tertinggi |
| `WHERE NOT EXISTS (SELECT 1 FROM t2 WHERE t2.id = t1.id)` | Data yang tidak ada di tabel lain |

## Tugas

Lihat bagian **Praktikum 4** di studi kasus kelompok kalian untuk daftar tugas spesifik.

Secara umum, kalian harus:

1. Membuat **3 VIEW** sesuai studi kasus
2. Menjalankan **3 query akses** dari view (dengan filter dan ORDER BY)
3. Menulis **4 subquery lanjutan** sesuai pertanyaan analisis di studi kasus

## Pertanyaan Diskusi

1. Apakah VIEW menyimpan data secara fisik di database? Jelaskan apa yang terjadi saat view diakses.
2. Apa keuntungan menggunakan VIEW dibanding menyalin-tempel query yang sama berulang kali?
3. Apa perbedaan subquery di `WHERE` dengan subquery di `FROM`? Kapan masing-masing digunakan?
4. Mengapa subquery correlated bisa lebih lambat dari subquery biasa? Jelaskan cara kerjanya.
5. Bisakah VIEW digunakan sebagai sumber subquery? Berikan contoh skenarinya.

## Referensi

- [MySQL CREATE VIEW](https://dev.mysql.com/doc/refman/8.0/en/create-view.html)
- [MySQL Subqueries](https://dev.mysql.com/doc/refman/8.0/en/subqueries.html)
- [MySQL Correlated Subqueries](https://dev.mysql.com/doc/refman/8.0/en/correlated-subqueries.html)
- [MySQL Derived Tables](https://dev.mysql.com/doc/refman/8.0/en/derived-tables.html)
