# Dimensions — Backend

| | |
| --- | --- |
| **Modul** | `apps/api/src/modules/mcat/native/catalog.ts`, `apps/api/src/modules/mcat/native/store.ts` |
| **Tabel** | `ref_mcat_dimension` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/mcat/dimensions` | Hanya yang masih dipakai. `?include_inactive=1` menambahkan yang sudah tidak dipakai |
| POST | `/mcat/dimensions` | Kode dilipat ke huruf kecil; kode yang sudah ada dijawab `409` |
| PATCH | `/mcat/dimensions/:dimensionId` | Nama, deskripsi, `is_active`. Kolom `code` diabaikan |
| DELETE | `/mcat/dimensions/:dimensionId` | Hanya untuk dimensi yang belum dipakai; kalau dipakai dijawab `409` |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| Bentuk kode | `400` untuk kode yang bentuknya salah — aturannya sama dengan kode soal, lewat `normalizeCode` |
| BR-DIM-01 — kode unik | `409` untuk kode yang sudah dipakai dimensi lain |
| BR-DIM-02 — kode tetap | Kolom `code` dibuang dari badan `PATCH` lewat `.omit({ code: true })`, jadi kode tidak pernah berubah |
| BR-DIM-03 — masih dipakai | `409` dengan cacah soal, Question Set, dan Adaptive Setup yang memakainya. `dimensionUsage()` yang menghitungnya |
| BR-DIM-04 — tidak dipakai lagi | `400` untuk memilih dimensi nonaktif, lewat `selectableDimension()` |

## 3. Catatan implementasi

**Dua fungsi pencari, sengaja.** `knownDimension()` menerima dimensi apa pun yang ada, `selectableDimension()`
menolak yang sudah tidak dipakai. Jalur yang **membaca** data tersimpan memakai yang pertama; jalur yang
**memilih** dimensi baru memakai yang kedua. Memakai satu fungsi untuk keduanya berarti mematikan satu dimensi
akan menolak penyuntingan soal-soal lamanya.

**Alasan yang sama di `store.ts`.** `activeDimensions()` mengisi daftar pilihan, sedangkan `allDimensions()` dan
`dimensionCodes()` menerjemahkan `id` jadi kode saat menyusun DTO soal dan Question Set. Sebelum keduanya
dipisah, DTO memakai daftar aktif — jadi mematikan satu dimensi membuat kode dimensi hilang dari tiap soal yang
mengukurnya, tanpa galat apa pun. Uji regresinya ada di `mcat-native.test.ts`, di blok `dimensions`.

**`validate()` menerima daftar kode yang sudah melekat.** Parameter `kept` berisi kode yang sudah diukur soal
itu sebelum disunting. Kode di dalamnya diperiksa dengan `knownDimension`, sisanya dengan `selectableDimension`.
Itu yang membuat soal lama tetap bisa disunting isinya tanpa dipaksa mengganti dimensinya (BR-DIM-05).

**Kode dimensi huruf kecil.** `normalizeCode()` melipat ke huruf besar karena dipakai untuk kode soal, lalu
hasilnya dikecilkan di sini. Alasannya bukan selera: importir mencocokkan kolom `a_<kode>` terhadap header yang
sudah dikecilkan, jadi kode huruf besar tidak akan pernah cocok dengan kolomnya sendiri.
