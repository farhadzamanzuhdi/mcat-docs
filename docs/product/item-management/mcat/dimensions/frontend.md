# Dimensions — Frontend

| | |
| --- | --- |
| **Rute** | `apps/cms/src/routes/_app/mcat-builder/dimensions.tsx` |
| **Komponen** | `features/mcat/components/molecules.tsx` (`WeightsEditor`), `features/mcat/components/AlgorithmForm.tsx` |

## 1. Layar

Halaman **Dimensions** berisi satu tabel: kode, nama, deskripsi, status, dan aksi ubah serta hapus. Di atasnya
ada kotak pencarian yang menyaring kode maupun nama. Tombol **Add Dimension** dan aksi ubah memakai satu modal
yang sama; kolom kode hanya muncul saat menambah, saklar status hanya muncul saat mengubah.

Layar **Item Bank** punya tombol **Dimension Setting** yang membuka halaman ini, sejajar dengan tombol
**Language Setting** di layar Question Sets.

Dua tempat lain memilih dimensi dalam jumlah banyak, dan keduanya memakai bentuk yang sama — kotak pilih yang
bisa dicari, isian terpilih tampil sebagai label yang bisa ditutup:

- **`WeightsEditor`** di laci soal Sesi 2. Di bawah kotak pilihnya, tiap dimensi terpilih mendapat satu baris
  berisi penggeser dan kotak angka untuk bobot diskriminasi `a`.
- **Adaptive Setup**, untuk `tested_dimensions`. Baris di bawahnya berisi `prior_mean` dan `prior_variance` tiap
  dimensi terpilih.

## 2. Keputusan antarmuka

**Label, bukan satu baris per dimensi.** Bentuk lamanya menampilkan satu saklar untuk tiap dimensi yang ada.
Terbaca saat dimensinya empat, tidak terpakai saat dimensinya seratus. Yang tumbuh sekarang hanya daftar di
dalam kotak pilih, yang memang bisa dicari dan digulir.

**Label saja tidak cukup, jadi barisnya tetap ada.** Tiap dimensi terpilih membawa angka — bobot `a` di laci
soal, `prior_mean` dan `prior_variance` di Adaptive Setup. Label tidak bisa memuat penggeser. Karena itu kotak
pilih hanya memutuskan **dimensi mana**, dan baris di bawahnya mengurus **berapa**.

**Menutup label berarti menghapus.** Dimensi yang tidak terpilih tidak punya bobot untuk diingat, jadi menutup
labelnya membuang bobotnya sekalian. Memilihnya kembali memulai dari bobot bawaan.

**Urutan mengikuti urutan kolam, bukan urutan klik.** Di Adaptive Setup, `prior_mean` dan `prior_variance`
disimpan sebagai larik yang sejajar posisinya dengan `tested_dimensions`. Kalau urutannya ikut urutan klik,
angka-angka itu bergeser ke dimensi yang salah. Karena itu pilihan selalu disaring ulang mengikuti urutan
kolam.

**Kode dimensi tidak bisa disunting.** Kolomnya hanya muncul saat menambah. Alasannya di
[backend.md](backend.md) §3, dan keterangannya juga ditulis di bawah kolom itu supaya admin tidak perlu
menebak.

## 3. Catatan implementasi

`dimensionsQuery` menyajikan daftar yang boleh dipilih dan dipakai seluruh layar lain; `allDimensionsQuery`
menyajikan daftar lengkap dan hanya dipakai halaman ini. Keduanya berbagi awalan kunci `mcatKeys.dimensions`,
jadi satu penulisan menyegarkan keduanya sekaligus.
