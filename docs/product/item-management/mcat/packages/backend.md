# Packages — Backend

| | |
| --- | --- |
| **Modul** | `modules/mcat/native/packages.ts`; pemasangan di `modules/local/tool-component-mcat.ts` |
| **Tabel** | `master_mcat_package`, `master_mcat_package_question_set` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/mcat/packages?language_id=` | Tiap baris membawa `used_by` |
| POST | `/mcat/packages` | |
| GET PATCH DELETE | `/mcat/packages/:packageId` | |
| PUT | `/admin/secured/block/page/item/mcat/:itemId` | Item MCAT menyimpan `{ packageId, session1Shuffle }` |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-PKG-01 — tidak boleh kosong dua-duanya | `validate()` → 400 |
| BR-PKG-02 — kumpulan harus Sesi 1 | `validate()` memeriksa `session` dan `dimension_id` tiap kumpulan → 400 |
| BR-PKG-03 — kemampuan kembar | `validate()` mengumpulkan dimensi yang sudah dipakai → 400 |
| BR-PKG-04 — satu bahasa | Kumpulan dan kumpulan milik setup diperiksa terhadap bahasa paket → 400 |
| BR-PKG-05 — masih dipakai | `usageOf()` → 409, pesannya menyebut nama Tool Component-nya |
| Item wajib memilih paket | `validateAssignment()`; item tanpa `packageId` → 400 |

## 3. Catatan implementasi

**Pemasangan tidak punya tabel.** Ia satu field di dalam JSON `structure` milik Tool Component. Karena itu
`usageOf()` menjelajah struktur tiap Tool Component mencari item bertipe MCAT yang `packageId`-nya cocok — itu
yang menjawab "paket ini dipakai di mana" dan yang menahan penghapusan paket yang masih terpasang.

`ponytail:` `usageOf()` dipanggil sekali per paket saat daftarnya dibaca, dan tiap panggilan membaca seluruh
Tool Component. Untuk puluhan komponen itu tidak terasa; kalau sudah ratusan, hitung sekali lalu bagikan ke
semua baris.

**Urutan kumpulan** disimpan di `sort_order` tabel jembatan dan ditulis ulang tiap kali paket disimpan.

**Pemasangan lama tidak dimigrasi.** Item MCAT yang dibuat sebelum paket ada terbaca sebagai belum terpasang,
dan admin memilih paketnya sekali lagi. Memindahkannya berarti menjelajah array bersarang dari dalam SQL demi
data yang isinya satu item demo.
