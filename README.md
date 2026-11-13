# Toko Buku API

REST API katalog buku dibuat dengan Laravel untuk praktikum Teknologi Web Service (Pertemuan 9: Database dan CRUD API). API menyimpan data kategori dan buku ke database, memvalidasi input, dan menjawab dengan status code yang tepat.

## Teknologi

- PHP 8.3
- Laravel 13
- SQLite (bawaan Laravel)
- Postman untuk pengujian

## Cara Menjalankan

```bash
git clone https://github.com/zahraayua/toko-buku.git
cd toko-buku
composer install
copy .env.example .env
php artisan key:generate
php artisan migrate
php artisan serve
```

Jika file `database/database.sqlite` belum ada, buat file kosong dengan nama itu, lalu jalankan `php artisan migrate`.

API berjalan di `http://127.0.0.1:8000/api`.

Semua request wajib mengirim header `Accept: application/json`.

## Struktur Data

**Category**

| Kolom | Tipe | Keterangan |
|---|---|---|
| id | integer | Primary key |
| name | string (100) | Wajib, unik |

**Book**

| Kolom | Tipe | Keterangan |
|---|---|---|
| id | integer | Primary key |
| category_id | integer | Wajib, harus ada di tabel categories |
| title | string (200) | Wajib |
| author | string (100) | Wajib |
| isbn | string (20) | Opsional, unik |
| price | integer | Wajib, minimal 0 (rupiah) |
| stock | integer | Wajib, minimal 0 |
| description | text | Opsional |

Satu kategori memiliki banyak buku. Kategori tidak bisa dihapus selama masih memiliki buku.

## Daftar Endpoint

| Method | Endpoint | Keterangan | Status |
|---|---|---|---|
| GET | /api/categories | Daftar kategori | 200 |
| POST | /api/categories | Buat kategori | 201, 422 |
| GET | /api/categories/{id} | Detail kategori beserta bukunya | 200, 404 |
| PUT/PATCH | /api/categories/{id} | Ubah kategori | 200, 404, 422 |
| DELETE | /api/categories/{id} | Hapus kategori | 204, 404, 409 |
| GET | /api/books | Daftar buku | 200 |
| POST | /api/books | Buat buku | 201, 422 |
| GET | /api/books/{id} | Detail buku | 200, 404 |
| PUT/PATCH | /api/books/{id} | Ubah buku (boleh sebagian) | 200, 404, 422 |
| DELETE | /api/books/{id} | Hapus buku | 204, 404 |

## Contoh Request

**Buat kategori**

```
POST /api/categories
{
    "name": "Novel"
}
```

**Buat buku**

```
POST /api/books
{
    "category_id": 1,
    "title": "Laskar Pelangi",
    "author": "Andrea Hirata",
    "price": 89000,
    "stock": 12,
    "isbn": "9789792248616"
}
```

**Contoh respons (201)**

```json
{
    "data": {
        "id": 2,
        "title": "Laskar Pelangi",
        "author": "Andrea Hirata",
        "isbn": "9789792248616",
        "price": 89000,
        "stock": 12,
        "description": null,
        "category": {
            "id": 1,
            "name": "Novel"
        },
        "created_at": "2026-11-13T05:29:31.000000Z"
    }
}
```

## Format Respons

- Data dikirim di dalam kunci `data`.
- Error memiliki kunci `message`, dan error validasi memiliki kunci `errors`.
- Semua error pada `/api/*` selalu berformat JSON.

## Hasil Pengujian

| Skenario | Hasil |
|---|---|
| Buat kategori | 201 |
| Buat kategori dengan nama yang sama | 422 |
| Buat buku lengkap | 201 |
| Buat buku tanpa title dan price negatif | 422 |
| Buat buku dengan ISBN yang sudah ada | 422 |
| Buat buku tanpa ISBN | 201 (isbn bernilai null) |
| Daftar buku | 200 |
| Detail buku yang tidak ada | 404 (JSON) |
| Ubah stok buku | 200 |
| Ubah ISBN dengan ISBN milik sendiri | 200 |
| Hapus kategori yang masih punya buku | 409 |
| Hapus buku | 204 |

Koleksi Postman tersedia di file `Toko-Buku-API.postman_collection.json` (jika disertakan di repo).

## Tugas Tambahan

Kolom `isbn` (teks, unik, boleh kosong) ditambahkan lewat migration baru `add_isbn_to_books_table`, lalu ditambahkan pada `$fillable` model `Book`, aturan validasi di `BookController`, dan `BookResource`.

## Penulis

- Nama: (Zahra Ayu Adinda)
- NIM: (24.0504.0022)