# Packages — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Mengelola paket

- [x] **AC-PKG-01** (FR-PKG-01, FR-PKG-02) Diberi halaman Packages, saat admin membuat paket dengan satu
      kumpulan Sesi 1, maka paket itu muncul di daftar beserta isinya.
- [x] **AC-PKG-02** (BR-PKG-01) Diberi form paket dengan Sesi 1 dan Sesi 2 sama-sama kosong, saat admin
      menyimpan, maka sistem menolak.
- [ ] **AC-PKG-03** (BR-PKG-02, BR-PKG-04) Diberi form paket, saat admin membuka pilihan Sesi 1, maka hanya
      kumpulan Sesi 1 berbahasa sama yang ditawarkan.
- [x] **AC-PKG-04** (BR-PKG-03) Diberi form paket berisi dua kumpulan berkemampuan sama, saat admin menyimpan,
      maka sistem menolak dan menyebut bahwa kemampuan itu akan dinilai dua kali.
- [x] **AC-PKG-05** (FR-PKG-02, BR-PKG-07) Diberi paket berisi dua kumpulan, maka urutannya tersimpan seperti
      yang disusun admin, dan tetap begitu sesudah paket itu diubah.
- [ ] **AC-PKG-06** (BR-PKG-04) Diberi paket yang sudah dibuat, maka bahasanya tidak dapat diubah lagi.

## 2. Memasang ke tes

- [ ] **AC-PKG-07** (FR-PKG-07, FR-PKG-08) Diberi item MCAT di dalam Tool Component, maka yang dipilih adalah
      **satu** paket, dan dialognya menampilkan isi paket itu.
- [ ] **AC-PKG-08** (FR-PKG-10) Diberi item MCAT yang belum memilih paket, maka inspector-nya mengatakan
      begitu, dan tree Tool Component menandainya.
- [x] **AC-PKG-09** (FR-PKG-05) Diberi paket yang dipakai sebuah tes, maka barisnya menyebut angka pemakaian,
      dan membuka baris itu menampilkan tes mana saja.
- [ ] **AC-PKG-10** (FR-PKG-09) Diberi dua tes yang memakai paket yang sama, saat salah satunya menyalakan acak
      urutan soal, maka tes yang lain tidak ikut berubah.

## 3. Penolakan hapus

- [x] **AC-PKG-11** (BR-PKG-05) Diberi paket yang masih dipakai item MCAT, saat admin menghapusnya, maka sistem
      menolak dan menyebut tes yang memakainya.
- [ ] **AC-PKG-12** (BR-PKG-06) Diberi kumpulan atau setup yang masih ditunjuk sebuah paket, saat admin
      menghapusnya, maka sistem menolak.
