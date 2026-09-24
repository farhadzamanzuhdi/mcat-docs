# Adaptive Setups — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Konfigurasi

- [x] **AC-ADP-01** (BR-ADP-01) Diberi form setup baru tanpa satu pun kumpulan soal, saat admin menyimpan, maka
      sistem menolak.
- [x] **AC-ADP-02** (BR-ADP-02) Diberi form setup, saat admin memilih kumpulan **Sesi 1**, maka sistem menolak
      dengan pesan bahwa kumpulan itu bukan kumpulan Sesi 2.
- [x] **AC-ADP-03** (FR-ADP-03) Diberi form setup, maka kemampuan yang ditawarkan hanya yang benar-benar diukur
      soal di kumpulan yang dipilih.
- [ ] **AC-ADP-04** (BR-ADP-03) Diberi setup yang kemampuan diukurnya tidak ada soalnya, saat admin menyimpan,
      maka sistem menolak dan menyebut kemampuan itu.
- [x] **AC-ADP-05** (BR-ADP-06) Diberi setup dengan panjang minimal lebih besar dari panjang maksimal, saat
      admin menyimpan, maka sistem menolak.
- [x] **AC-ADP-06** (BR-ADP-05) Diberi setup dengan jumlah angka prior yang tidak sama dengan jumlah kemampuan
      yang diukur, saat admin menyimpan, maka sistem menolak.
- [ ] **AC-ADP-07** (FR-ADP-07) Diberi setup yang soal memenuhi syaratnya lebih sedikit daripada panjang tes
      maksimalnya, maka halamannya menampilkan peringatan yang menyebut kedua angkanya.

## 2. Menjalankan

- [x] **AC-ADP-08** (BR-ADP-08) Diberi setup yang kolamnya berisi tepat satu soal yang memenuhi syarat, saat
      sesi dimulai, maka soal itulah yang keluar — soal di kumpulan lain tidak pernah muncul.
- [ ] **AC-ADP-09** (BR-ADP-07) Diberi setup yang sudah punya sesi, saat admin mengubah kumpulan soalnya, maka
      sistem menolak.
- [ ] **AC-ADP-10** (BR-SET-07) Diberi kumpulan yang masih ditunjuk sebuah setup, saat admin menghapusnya, maka
      sistem menolak.
- [ ] **AC-ADP-11** (FR-ADP-08) Diberi sesi berjalan, saat soal berikutnya diminta dua kali berturut-turut
      tanpa dijawab, maka soal yang sama diberikan lagi — bukan soal baru.
- [x] **AC-ADP-12** (BR-ADP-10) Diberi satu soal yang dijawab dua kali bersamaan, maka hanya satu jawaban yang
      diterima dan jumlah soal yang dikerjakan tetap 1.
- [x] **AC-ADP-13** (FR-ADP-08) Diberi **Try as participant**, saat admin menjawab sampai tes berhenti, maka
      perkiraan kemampuannya bergerak dari titik awal dan riwayat soalnya terlihat.

## 3. Simulasi

- [x] **AC-ADP-14** (FR-ADP-10) Diberi simulasi dengan benih acak yang sama, maka hasilnya sama persis; dengan
      benih berbeda, jawabannya berbeda.
- [x] **AC-ADP-15** (BR-ADP-09) Diberi simulasi yang dijalankan, maka hitungan keterpaparan soal tidak berubah
      sama sekali.
