# Dimensions — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Merawat daftar dimensi

- [ ] **AC-DIM-01** (FR-DIM-01) Diberi beberapa dimensi, saat admin membuka Dimensions, maka tabel menampilkan
      semuanya termasuk yang sudah tidak dipakai, masing-masing dengan statusnya.
- [x] **AC-DIM-02** (FR-DIM-02) Diberi layar Dimensions, saat admin menambah dimensi bernama Focus berkode
      `Focus`, maka dimensi itu tersimpan dengan kode `focus`.
- [x] **AC-DIM-03** (BR-DIM-01) Diberi dimensi berkode `focus`, saat admin menambah dimensi berkode `FOCUS`,
      maka sistem menolaknya.
- [ ] **AC-DIM-04** (FR-DIM-03) Diberi sebuah dimensi, saat admin mengubah nama dan deskripsinya, maka keduanya
      berubah di tabel.
- [x] **AC-DIM-05** (BR-DIM-02) Diberi sebuah dimensi, saat permintaan ubah menyertakan kode baru, maka kodenya
      tetap seperti semula.
- [ ] **AC-DIM-06** (FR-DIM-06) Diberi daftar berisi banyak dimensi, saat admin mengetik sebagian kode atau nama
      di kotak pencarian, maka tabel hanya menampilkan yang cocok.

## 2. Menghapus dan menonaktifkan

- [x] **AC-DIM-07** (BR-DIM-03) Diberi dimensi yang diukur satu soal, saat admin menghapusnya, maka sistem
      menolaknya dan menyebut ada 1 soal yang memakainya.
- [ ] **AC-DIM-08** (FR-DIM-05) Diberi dimensi yang tidak dipakai apa pun, saat admin menghapusnya, maka dimensi
      itu hilang dari daftar.
- [x] **AC-DIM-09** (FR-DIM-04, FR-DIM-07) Diberi dimensi yang ditandai tidak dipakai, saat layar mana pun
      meminta dimensi, maka dimensi itu tidak ditawarkan — tetapi tetap muncul di halaman Dimensions.
- [x] **AC-DIM-10** (BR-DIM-04) Diberi soal yang mengukur dimensi yang kemudian ditandai tidak dipakai, saat
      soal itu dibuka di Item Bank, maka kode dimensinya tetap tampil dan bobotnya tetap utuh.
- [x] **AC-DIM-11** (BR-DIM-05) Diberi soal yang sama, saat admin mengubah isi soalnya tanpa mengganti
      dimensinya, maka perubahan itu tersimpan.
- [x] **AC-DIM-12** (BR-DIM-04) Diberi dimensi yang tidak dipakai lagi, saat admin membuat soal baru pada
      dimensi itu, maka sistem menolaknya.

## 3. Memilih banyak dimensi

- [ ] **AC-DIM-13** (FR-DIM-08) Diberi laci soal Sesi 2, saat admin membuka kotak pilih dimensi dan mengetik
      sebagian nama, maka daftarnya menyusut sesuai ketikan.
- [ ] **AC-DIM-14** (FR-DIM-08) Diberi dua dimensi terpilih, saat admin menutup label salah satunya, maka baris
      bobot dimensi itu ikut hilang dan bobot dimensi lain tidak berubah.
- [ ] **AC-DIM-15** (FR-DIM-08) Diberi Adaptive Setup dengan tiga dimensi teruji, saat admin menambah dimensi
      keempat, maka `prior_mean` dan `prior_variance` tiap dimensi lama tetap menempel pada dimensinya
      masing-masing.
