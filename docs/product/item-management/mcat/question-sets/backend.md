# Question Sets — Backend

| | |
| --- | --- |
| **Modul** | `apps/api/src/modules/mcat/native/catalog.ts` |
| **Tabel** | `master_mcat_question_set`, `master_mcat_question_set_item`, `ref_mcat_language` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/mcat/question-sets?language_id=&session=` | |
| POST | `/mcat/question-sets` | Sesi 1 wajib mengirim `dimension` dan `duration_minutes` |
| GET PATCH DELETE | `/mcat/question-sets/:setId` | |
| GET | `/mcat/question-sets/:setId/items` | Kolam, sesuai urutannya |
| PUT | `/mcat/question-sets/:setId/items` | **Seluruh kolam sekaligus** |
| PATCH | `/mcat/question-sets/:setId/display` | Menulis ketiga kolom tampilan ke tiap soal di kolam |
| GET POST | `/mcat/languages` | `id` opsional; tanpa itu diambil tiga huruf pertama namanya |
| PATCH DELETE | `/mcat/languages/:languageId` | Nama dan deskripsi; hapus hanya yang belum dipakai |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-SET-01 — kemampuan wajib Sesi 1 | `resolveDimension()`; Sesi 1 tanpa kemampuan → 400 |
| BR-SET-02 — kemampuan terkunci | Mengubah `dimension` kumpulan yang kolamnya tidak kosong → 409 |
| BR-SET-03 — kolam hanya menerima yang cocok | `requireFits()` memeriksa sesi, bahasa, dan kemampuan tiap soal → 400 |
| BR-SET-04 — batas jumlah yang keluar | `requireServable()`, dipanggil saat kumpulan disimpan dan saat kolam ditulis → 400 |
| BR-SET-05 — durasi Sesi 1 | Kumpulan Sesi 1 tanpa `duration_minutes` → 400 |
| BR-SET-07 — penolakan hapus | Diperiksa terhadap `question_set_ids` tiap setup dan tabel jembatan Package → 409 |
| BR-SET-10 — penerapan tampilan | `UPDATE` ke tiap soal di kolam, bukan kolom baru di kumpulan |
| BR-MCAT-08 — kode bahasa unik | Kode yang sudah ada → 400. `id` selalu dijadikan huruf besar saat dibuat |
| BR-MCAT-09 — bahasa masih dipakai | Menghitung Question Set **dan** soal; salah satu tidak nol → 409, pesannya menyebut kedua angkanya |

## 3. Catatan implementasi

**Kenapa kolam ditulis sekaligus.** Menambah, mengeluarkan, dan mengurutkan adalah satu tindakan dari sudut
pandang layar: admin menyusun kolamnya lalu menyimpan. Tiga endpoint terpisah berarti tiga panggilan untuk satu
maksud, dan tiga peluang berhenti di tengah. `PUT` menerima daftar id berurutan, memeriksa semuanya cocok, lalu
menulis ulang baris jembatannya di dalam satu transaksi — penulisan yang ditolak tidak mengubah apa pun.

**Dua kemampuan, satu otoritas.** Kemampuan ada di soal **dan** di kumpulan, tapi yang dinilai selalu kemampuan
kumpulan. Kemampuan di soal cuma atribut katalog supaya soal bisa dicari. `requireFits()` dan penguncian
BR-SET-02 yang menjaga keduanya tidak pernah berbeda.

**`requireServable()` sengaja tidak mengecilkan angkanya sendiri** saat kolam menyusut. Berapa soal yang
dikerjakan peserta ikut menentukan skor; itu berubah kalau admin bilang begitu, bukan sebagai efek samping
merapikan kolam. Kolam kosong dilewati pemeriksaannya, karena kumpulan tanpa kolam tidak menjanjikan apa pun.
Pemeriksaan ketiga terjadi saat tes dimulai, karena kolam bisa menyusut lewat jalan lain — misalnya soalnya
dinonaktifkan.

**Penerapan tampilan mengambil id soal lewat tabel jembatan kolam**, bukan lewat kolom pemilik di soal — soal
tidak lagi punya kolom seperti itu. Kumpulan berkolam kosong tidak menulis apa pun dan tetap membalas sukses.

**Pemeriksaan sebelum hapus bahasa menghitung dua tabel, bukan satu.** Sebelumnya ia menghitung tabel library
yang kini sudah tidak ada; kalau hanya salah satu yang dihitung, bahasa yang soalnya masih ada tapi
kumpulannya sudah dihapus akan lolos terhapus dan menyisakan soal tanpa bahasa.
