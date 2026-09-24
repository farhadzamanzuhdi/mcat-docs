# Impor soal — Backend

| | |
| --- | --- |
| **Modul** | `parseImport()` dan `templateColumns()` di `modules/mcat/native/catalog.ts` |
| **Tabel** | `master_mcat_question`, `master_mcat_question_set_item` — lihat [data-model.md](../../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/mcat/items/template?session=` | Kolomnya berbeda per sesi |
| POST | `/mcat/items/import?question_set_id=` | Sesi dan bahasa diambil dari kumpulan itu |
| POST | `/mcat/items/import?session=&language_id=` | Tanpa kumpulan: soalnya masuk Item Bank saja |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-IMP-01 — pencocokan kode | Tiap baris dicari lewat `code` yang sudah dinormalkan; ketemu → `updateItem()`, tidak → `insertItem()` |
| BR-IMP-02 — validasi sama | Tiap baris melewati `itemBody` lalu `validate()`, sama seperti jalur ketik manual |
| BR-IMP-03 — nomor baris | `index + 2`, karena header adalah baris 1 |
| BR-IMP-04 — berkas tanpa data | Header tanpa baris data → 400 |
| Soal masuk kolam | Dimulai dari sebuah kumpulan, `requireFits()` lalu `addToSet()` per baris |

## 3. Catatan implementasi

**Ringkasan membedakan dibuat dan diperbarui.** `imported` dan `updated` dihitung terpisah karena admin perlu
tahu berkasnya menambah soal atau memperbaiki yang sudah ada — angka gabungan menyembunyikan hal yang paling
ingin diketahui setelah unggah ulang.

**Kegagalan per baris, bukan per berkas.** Tiap baris dibungkus `try/catch` sendiri; yang gagal masuk daftar
`errors` beserta nomor barisnya. Seluruh impor berjalan di dalam satu transaksi, jadi baris yang berhasil
tersimpan sebagai satu kesatuan.

**CSV saja.** `splitCsvLine()` menangani field berkutip dan kutip ganda di dalamnya — itu yang dibutuhkan.
`mcat-api` menerima `.xlsx`, dan menyamakannya berarti menarik pustaka spreadsheet ke sini. Ditandai
`ponytail:` di berkasnya: tambahkan kalau memang ada yang membutuhkan Excel bolak-balik.
