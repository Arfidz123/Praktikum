# Praktikum 3 — Query: Analisis Data

## Deskripsi

Sistem sudah berjalan selama beberapa bulan dan data sudah terkumpul cukup banyak. Manajemen mulai membutuhkan **laporan dan analisis** untuk pengambilan keputusan.

> *"Sistem kita sudah jalan hampir setahun, data sudah banyak. Manajemen minta beberapa laporan — tren penjualan, performa tim, ringkasan per kategori. Kalian bisa buatkan query-nya? Ini prioritas tinggi, mereka butuh datanya minggu ini."*
> — Project Manager

## Tujuan Pembelajaran

Setelah menyelesaikan praktikum ini, mahasiswa mampu:

1. Menggabungkan data dari beberapa tabel menggunakan **JOIN** (INNER, LEFT, RIGHT)
2. Menghitung ringkasan data menggunakan **fungsi aggregate** (COUNT, SUM, AVG, MIN, MAX)
3. Mengelompokkan data dengan **GROUP BY** dan memfilter kelompok dengan **HAVING**
4. Menulis **subquery** untuk analisis yang lebih kompleks
5. Mengurutkan dan membatasi hasil dengan **ORDER BY** dan **LIMIT**

## Persiapan (Sebelum Praktikum)

Baca studi kasus kelompok kalian, khususnya bagian **Praktikum 3**. Siapkan **catatan tulis tangan** berisi:

### Query yang Harus Disiapkan

1. Minimal **3 query JOIN** (INNER, LEFT, dan/atau RIGHT)
2. Minimal **3 query aggregate** dengan GROUP BY
3. Minimal **2 query** dengan HAVING
4. Minimal **1–2 subquery** sederhana (di WHERE atau FROM)
5. Query analisis sesuai skenario di studi kasus

> **Catatan:** Subquery sederhana diperkenalkan di praktikum ini. Subquery yang lebih kompleks (nested aggregate, derived table, correlated) akan didalami di **Praktikum 4**.

## Konsep Penting

### JOIN — Menggabungkan Tabel

```sql
-- INNER JOIN: hanya data yang cocok di kedua tabel
SELECT o.id, c.name, o.total
FROM orders o
INNER JOIN customers c ON o.customer_id = c.id;

-- LEFT JOIN: semua data dari tabel kiri + yang cocok di kanan
SELECT c.name, COUNT(o.id) AS total_orders
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.id, c.name;

-- RIGHT JOIN: semua data dari tabel kanan + yang cocok di kiri
SELECT p.name, c.name AS category
FROM products p
RIGHT JOIN categories c ON p.category_id = c.id;

-- JOIN lebih dari 2 tabel
SELECT c.name AS customer, p.name AS product, oi.quantity
FROM orders o
INNER JOIN customers c ON o.customer_id = c.id
INNER JOIN order_items oi ON o.id = oi.order_id
INNER JOIN products p ON oi.product_id = p.id;
```

**Kapan menggunakan jenis JOIN yang mana?**

| Jenis | Gunakan ketika... |
|-------|-------------------|
| INNER JOIN | Hanya butuh data yang ada relasi di kedua tabel |
| LEFT JOIN | Butuh **semua** data dari tabel utama, meskipun tidak ada relasi |
| RIGHT JOIN | Butuh **semua** data dari tabel kedua, meskipun tidak ada relasi |

### Fungsi Aggregate

```sql
-- COUNT: hitung jumlah baris
SELECT COUNT(*) AS total_customers FROM customers;

-- SUM: jumlahkan nilai
SELECT SUM(total) AS total_revenue FROM orders WHERE status = 'completed';

-- AVG: rata-rata
SELECT AVG(price) AS avg_price FROM products;

-- MIN & MAX
SELECT MIN(price) AS cheapest, MAX(price) AS most_expensive FROM products;
```

### GROUP BY & HAVING

```sql
-- GROUP BY: kelompokkan data
SELECT category_id, COUNT(*) AS total_products, AVG(price) AS avg_price
FROM products
GROUP BY category_id;

-- HAVING: filter setelah pengelompokan
SELECT category_id, COUNT(*) AS total_products
FROM products
GROUP BY category_id
HAVING total_products > 5;
```

**Perbedaan WHERE vs HAVING:**
- `WHERE` memfilter **baris** sebelum pengelompokan
- `HAVING` memfilter **kelompok** setelah pengelompokan

```sql
-- WHERE + GROUP BY + HAVING
SELECT category_id, AVG(price) AS avg_price
FROM products
WHERE is_active = TRUE          -- filter baris dulu
GROUP BY category_id            -- kelompokkan
HAVING avg_price > 100000;      -- filter kelompok
```

### Subquery

```sql
-- Subquery di WHERE
SELECT name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- Subquery di FROM
SELECT category_name, avg_price
FROM (
    SELECT c.name AS category_name, AVG(p.price) AS avg_price
    FROM products p
    JOIN categories c ON p.category_id = c.id
    GROUP BY c.id, c.name
) AS category_stats
WHERE avg_price > 50000;

-- Subquery dengan IN
SELECT name
FROM customers
WHERE id IN (
    SELECT customer_id
    FROM orders
    WHERE total > 1000000
);
```

## Tugas

Lihat bagian **Praktikum 3** di studi kasus kelompok kalian untuk daftar pertanyaan analisis dan query yang harus disiapkan.

## Pertanyaan Diskusi

1. Apa perbedaan hasil antara `INNER JOIN` dan `LEFT JOIN`? Berikan contoh skenario nyata.
2. Mengapa `HAVING` tidak bisa digantikan oleh `WHERE` sepenuhnya?
3. Kapan sebaiknya menggunakan subquery vs JOIN?
4. Apa yang terjadi jika kita GROUP BY tanpa menyertakan kolom non-aggregate di SELECT?
5. Bagaimana cara menampilkan data yang **tidak ada** di tabel lain? (misal: customer yang belum pernah order)

## Referensi

- [MySQL JOIN Syntax](https://dev.mysql.com/doc/refman/8.0/en/join.html)
- [MySQL Aggregate Functions](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html)
- [MySQL GROUP BY](https://dev.mysql.com/doc/refman/8.0/en/group-by-modifiers.html)
- [MySQL Subqueries](https://dev.mysql.com/doc/refman/8.0/en/subqueries.html)
