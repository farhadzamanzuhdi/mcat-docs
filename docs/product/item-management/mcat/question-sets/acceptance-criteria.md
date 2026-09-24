# Question Sets — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Daftar dan navigasi

- [x] **AC-SET-01** (FR-SET-01, FR-SET-03) Diberi kumpulan di dua sesi, saat admin berpindah tab sesi, maka
      daftarnya hanya menampilkan kumpulan sesi itu, dan tabnya ikut di alamat halaman.
- [ ] **AC-SET-02** (FR-SET-04) Diberi baris kumpulan, saat admin menekan tombol **Edit** di baris itu, maka
      dialognya terbuka tanpa ikut membuka halaman kumpulannya.
- [x] **AC-SET-03** (FR-SET-02) Diberi kumpulan berisi lima soal di kolam dengan tiga soal yang keluar, maka
      barisnya menyebut kedua angka itu terpisah.

## 2. Membuat dan mengubah

- [ ] **AC-SET-04** (FR-SET-05, BR-SET-01) Diberi form kumpulan Sesi 1 tanpa kemampuan, saat admin menyimpan,
      maka sistem menolak.
- [x] **AC-SET-05** (BR-SET-05) Diberi form kumpulan Sesi 1 tanpa waktu pengerjaan, saat admin menyimpan, maka
      sistem menolak.
- [x] **AC-SET-06** (BR-SET-02) Diberi kumpulan yang kolamnya berisi soal, saat admin mengubah kemampuannya,
      maka sistem menolak dan menyuruh mengosongkan kolamnya dulu.
- [ ] **AC-SET-07** (BR-SET-09) Diberi kumpulan yang sudah dibuat, maka bahasa dan sesinya tidak dapat diubah
      lagi.

## 3. Kolam soal

- [x] **AC-SET-08** (BR-SET-03) Diberi kumpulan berkemampuan `numeric` berbahasa `EN`, saat kolamnya diisi soal
      berkemampuan `verbal`, maka sistem menolak.
- [x] **AC-SET-09** (FR-SET-10) Diberi halaman kumpulan, saat admin membuka **Add from Item Bank**, maka
      daftarnya sudah tersaring ke sesi, bahasa, dan kemampuan kumpulan itu.
- [x] **AC-SET-10** (BR-SET-04) Diberi kumpulan yang mengeluarkan 3 soal dari kolam berisi 3, saat admin
      mengeluarkan satu soal dari kolamnya, maka sistem menolak dan menyuruh menurunkan angkanya dulu.
- [x] **AC-SET-11** (BR-SET-04) Diberi kumpulan yang sama, saat admin mengosongkan kolamnya sama sekali, maka
      sistem menerimanya.
- [ ] **AC-SET-12** (BR-SET-06, FR-SET-13) Diberi kumpulan yang jumlah soal keluarnya terisi, maka pegangan
      urutan kolam tidak ditampilkan.
- [ ] **AC-SET-13** (BR-SET-07) Diberi kumpulan yang masih ditunjuk sebuah Package, saat admin menghapusnya,
      maka sistem menolak.

## 4. Bahasa

- [x] **AC-SET-14** (FR-SET-14) Diberi dialog Language Setting, saat admin menambah bahasa bernama `Vietnam`
      berkode `VN`, maka bahasa itu muncul di daftar dan di dropdown ruang lingkup.
- [ ] **AC-SET-15** (BR-MCAT-08) Diberi kode bahasa yang sudah ada, saat admin menambahkannya lagi, maka sistem
      menolak.
- [ ] **AC-SET-16** (FR-SET-15, BR-MCAT-11) Diberi dialog bahasa, saat admin mengubah nama sebuah bahasa lalu
      menutup dialog, maka nama baru itu sudah tersimpan tanpa tombol Save terpisah.
- [x] **AC-SET-17** (BR-MCAT-07) Diberi bahasa baru yang belum punya konten, saat admin memilihnya di dropdown,
      maka daftar Question Set kosong — bukan menampilkan kumpulan bahasa lain.
- [x] **AC-SET-18** (BR-MCAT-09) Diberi bahasa yang masih dipakai Question Set, saat admin menghapusnya, maka
      sistem menolak dan menyebut berapa kumpulan dan berapa soal yang masih memakainya.
- [ ] **AC-SET-19** (BR-MCAT-12) Diberi layar MCAT pada bahasa tertentu, saat alamatnya disalin dan dibuka di
      tab baru, maka bahasanya sama seperti sebelumnya.

## 5. Tampilan satu kumpulan

- [ ] **AC-SET-20** (FR-SET-16) Diberi kumpulan yang kolamnya berisi soal, saat admin membuka panel Display
      settings, maka pratinjaunya menampilkan soal nyata dari kolam itu, dan berubah seketika saat
      pengaturannya diubah.
- [ ] **AC-SET-21** (FR-SET-17) Diberi kumpulan berisi beberapa soal bertampilan berbeda-beda, saat admin
      menerapkan tampilan ke seluruh kumpulan, maka semua soal di kolam itu memakai pengaturan yang sama.
- [ ] **AC-SET-22** (BR-SET-10) Diberi penerapan ke seluruh kumpulan, maka soal di kumpulan **lain** yang tidak
      ada di kolam itu tidak ikut berubah.
- [ ] **AC-SET-23** (BR-SET-11) Diberi aksi terapkan ke seluruh kumpulan, saat admin menekannya, maka muncul
      konfirmasi yang menyebut bahwa pengaturan per soal akan ditimpa.

## 6. Navigasi

- [x] **AC-SET-24** (FR-SET-03) Diberi halaman sebuah kumpulan Sesi 2 berbahasa `ID`, saat admin menekan Back,
      maka daftarnya terbuka pada tab Sesi 2 dan bahasa `ID`.
