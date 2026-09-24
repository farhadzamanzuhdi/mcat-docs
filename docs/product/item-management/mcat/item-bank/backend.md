# Item Bank — Backend

| | |
| --- | --- |
| **Modul** | `apps/api/src/modules/mcat/native/catalog.ts`, pembantu di `store.ts` |
| **Tabel** | `master_mcat_question`, `master_mcat_question_dimension` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/mcat/items?session=&language_id=&dimension=&q=` | Seluruh saringan opsional. `q` mencocokkan kode lebih dulu, baru isi soal |
| POST | `/mcat/items?question_set_id=` | Dengan `question_set_id`, soal itu sekalian masuk kolam kumpulan tersebut |
| PUT | `/mcat/items/:itemId` | Termasuk keempat kolom tampilan soal |
| DELETE | `/mcat/items/:itemId` | |
| POST | `/mcat/questions/image` | Unggah gambar, kembali sebagai data URL |

Tampilan satu soal tidak punya endpoint sendiri: ia ikut `PUT /mcat/items/:itemId` seperti field soal lainnya.
Penerapan massal ke satu kumpulan punya endpointnya sendiri, dan itu milik
[Question Sets](../question-sets/backend.md).

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-ITM-01 — kode unik | `requireFreeCode()`, mengecualikan baris yang sedang diubah → 409 |
| BR-ITM-02 — pola kode | `normalizeCode()` memangkas, menjadikan huruf besar, lalu memeriksa polanya → 400 |
| BR-ITM-03, BR-ITM-04, BR-ITM-05 | `validate()`, sebelum baris ditulis → 400 |
| BR-ITM-06 — kemampuan | `withSessionDimension()`; Sesi 1 tanpa kemampuan, atau Sesi 2 tanpa `a_params` → 400 |
| BR-ITM-07 — masih di kolam | `setsHolding()` → 409, pesannya menyebut nama kumpulannya |
| BR-ITM-08 — pernah dikerjakan | Diperiksa terhadap `trans_mcat_session_item` → 409 |
| Nilai enum tampilan | `image_position`, `image_aspect_ratio`, dan `option_layout` divalidasi terhadap daftar tertutup → 400 |

## 3. Catatan implementasi

**Nomor kode tanpa tabel counter.** `nextCode(db, prefix)` mengambil `max + 1` dari kode yang sudah ada
berprefiks sama, dihitung di dalam transaksi insert. Prefiksnya kode kemampuan untuk Sesi 1, dan `S2` untuk
Sesi 2 — soal Sesi 2 mengukur beberapa kemampuan sekaligus, jadi tidak ada satu pun yang benar sebagai prefiks.

**Satu bentuk untuk dua sesi.** Soal Sesi 1 menyatakan satu kemampuan tanpa bobot, tapi semua yang di hilir —
area pelaporan, dimensi kolam, mesin adaptif — membaca `a_params`. `withSessionDimension()` mengisi satu baris
berbobot 1 dari kemampuan soal itu, sehingga hilirnya tidak perlu tahu sesi mana yang sedang ditanganinya.

**Jebakan zod yang sudah pernah menggigit.** `itemPatch` tidak boleh dibuat dari `itemBody.partial()` begitu
saja: `.partial()` menjadikan field opsional **tapi mempertahankan `default`-nya**, sehingga PATCH yang tidak
menyebut `session` diam-diam menulis ulang soal Sesi 2 jadi Sesi 1. Tiga field bawaan — `session`,
`language_id`, `a_params` — dideklarasikan ulang sebagai opsional murni.
