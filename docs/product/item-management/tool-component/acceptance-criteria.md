# Tool Component — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Folder

- [ ] **AC-TLC-01** (FR-TLC-01, FR-TLC-02) Diberi halaman Tool Component, saat admin membuat folder baru, maka
      folder itu muncul di daftar.
- [ ] **AC-TLC-02** (BR-TLC-01) Diberi folder yang masih berisi Tool Component, saat admin menghapusnya, maka
      sistem menolak.
- [ ] **AC-TLC-03** (BR-TLC-01) Diberi nama folder yang sudah ada, saat admin membuat folder bernama sama, maka
      sistem menolak.
- [ ] **AC-TLC-04** (FR-TLC-03) Diberi Tool Component di folder A, saat admin memindahkannya ke folder B, maka
      ia hilang dari A dan muncul di B.

## 2. Struktur

- [x] **AC-TLC-05** (FR-TLC-07, BR-TLC-03) Diberi Tool Component yang blok, halaman, dan itemnya tersimpan
      tidak berurutan, saat explorer dibuka, maka pohonnya menampilkan ketiganya sesuai `sort_order`.
- [x] **AC-TLC-06** (FR-TLC-09, BR-TLC-02) Diberi Tool Component yang punya header dan footer, maka keduanya
      ditampilkan terpisah dari blok pertanyaan.
- [x] **AC-TLC-07** (BR-TLC-02) Diberi blok lama tanpa `block_type_id`, saat `block_sort_order`-nya 1 atau 99,
      maka ia tetap dikenali sebagai header atau footer.
- [x] **AC-TLC-08** (FR-TLC-08) Diberi item pilihan ganda berisi empat pilihan, maka node-nya di pohon menyebut
      "4 pilihan".
- [x] **AC-TLC-09** (BR-TLC-04) Diberi item bertipe yang tidak ada di registry, saat explorer dibuka, maka
      node-nya tetap tampil sebagai node biasa dan halamannya tidak gagal dirender.
- [x] **AC-TLC-10** (FR-TLC-11) Diberi Tool Component berisi tiga tipe item berbeda, maka legenda menyebut
      ketiganya beserta jumlahnya.

## 3. Item MCAT

- [x] **AC-TLC-11** (FR-TLC-12, FR-TLC-13) Diberi item MCAT, saat admin membuka dialognya, maka yang dipilih
      satu paket, dan isinya ditampilkan.
- [ ] **AC-TLC-12** (FR-TLC-15, BR-TLC-05) Diberi item MCAT yang belum memilih paket, maka inspector-nya
      mengatakan begitu dan pohon strukturnya menandainya.
- [ ] **AC-TLC-13** (FR-TLC-14) Diberi dua item MCAT yang memakai paket sama, saat salah satunya menyalakan
      acak urutan soal, maka yang lain tidak ikut berubah.
