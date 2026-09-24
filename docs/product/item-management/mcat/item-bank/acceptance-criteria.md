# Item Bank — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Daftar dan pencarian

- [x] **AC-ITM-01** (FR-ITM-03, FR-ITM-05) Diberi Item Bank, saat admin mengetik kode sebuah soal di kotak
      cari, maka hanya soal itu yang tersisa. Kata yang tidak cocok apa pun memunculkan keadaan "tidak ada yang
      cocok", bukan tabel kosong.
- [x] **AC-ITM-02** (FR-ITM-01) Diberi soal yang ada di kolam dua kumpulan, maka barisnya menyebut angka 2 di
      kolom jumlah kumpulan.
- [ ] **AC-ITM-03** (FR-ITM-04) Diberi Item Bank yang tersaring per kemampuan, saat alamatnya disalin dan
      dibuka di tab baru, maka saringannya sama seperti sebelumnya.
- [x] **AC-ITM-23** (FR-ITM-01) Diberi kemampuan yang soalnya ada di Item Bank tapi sebagian belum masuk kolam
      mana pun, maka jumlah soal di Item Bank **lebih besar** daripada isi kolam kumpulan mana pun yang
      mengukur kemampuan itu.

## 2. Menulis soal

- [x] **AC-ITM-04** (FR-ITM-12) Diberi panel soal Sesi 1, maka tidak ada pilihan tingkat kesulitan, dan tidak
      ada field `a`, `d`, `c`, maupun kontrol keterpaparan.
- [x] **AC-ITM-05** (FR-ITM-13) Diberi Item Bank tanpa saringan kemampuan, saat admin menekan **Add item**,
      maka panel menampilkan dropdown kemampuan yang bisa dipilih — bukan peringatan tanpa jalan keluar.
- [ ] **AC-ITM-06** (FR-ITM-13) Diberi Item Bank dengan saringan kemampuan aktif, saat admin menekan **Add
      item**, maka dropdown itu sudah terpilih ke kemampuan tersebut.
- [ ] **AC-ITM-07** (FR-ITM-13) Diberi halaman Question Set berkemampuan `numeric`, saat admin menekan **Add
      item**, maka panel menyebut bahwa soal itu mengukur `numeric` dan tidak meminta kemampuan.
- [x] **AC-ITM-08** (FR-ITM-06) Diberi soal yang ditulis dari halaman sebuah Question Set, maka soal itu muncul
      di kolam kumpulan tersebut **dan** di Item Bank.
- [ ] **AC-ITM-09** (FR-ITM-11) Diberi panel soal, saat admin mengetik teks soal, maka tampilan peserta ikut
      berubah tanpa perlu menyimpan.
- [ ] **AC-ITM-10** (BR-ITM-03) Diberi panel soal dengan satu pilihan jawaban, saat admin menyimpan, maka
      sistem menolak dengan pesan bahwa pilihan ganda butuh minimal dua pilihan.
- [ ] **AC-ITM-11** (BR-ITM-05) Diberi panel soal dengan kunci jawaban yang tidak cocok dengan pilihan mana
      pun, saat admin menyimpan, maka sistem menolak.
- [ ] **AC-ITM-12** (BR-ITM-04) Diberi dua pilihan jawaban berlabel sama, saat admin menyimpan, maka sistem
      menolak.
- [ ] **AC-ITM-13** (FR-ITM-10) Diberi gambar lebih dari 2 MB, atau berkas berekstensi selain
      `jpg`/`png`/`webp`, saat admin mengunggahnya, maka sistem menolak dengan pesan yang menyebut batasnya.

## 3. Kode soal

- [x] **AC-ITM-14** (FR-ITM-14) Diberi panel soal baru untuk kumpulan berkemampuan `numeric`, maka field kode
      sudah terisi `NUMERIC-` diikuti empat angka, tanpa admin mengetik apa pun.
- [x] **AC-ITM-15** (BR-ITM-01) Diberi kode yang sudah dipakai soal lain, saat admin menyimpan, maka sistem
      menolak.
- [ ] **AC-ITM-16** (FR-ITM-16) Diberi field kode berisi `num-7`, saat admin memindahkan fokus keluar dari
      field itu, maka isinya berubah jadi `NUM-7` **sebelum** disimpan.
- [ ] **AC-ITM-17** (BR-ITM-02) Diberi kode berawalan tanda hubung, saat admin menyimpan, maka sistem menolak.

## 4. Menghapus

- [ ] **AC-ITM-18** (BR-ITM-07) Diberi soal yang ada di kolam dua kumpulan, saat admin menekan **Delete
      question**, maka sistem menolak dan menyebut kumpulan mana saja yang masih memakainya.
- [ ] **AC-ITM-19** (BR-ITM-08) Diberi soal yang sudah pernah dikerjakan peserta, saat admin menghapusnya, maka
      sistem menolak.
- [x] **AC-ITM-20** (FR-ITM-20) Diberi soal di kolam sebuah kumpulan, saat admin menekan **Remove from set**,
      maka soal itu keluar dari kolam tapi tetap ada di Item Bank.

## 5. Tampilan satu soal

- [ ] **AC-ITM-21** (BR-ITM-11) Diberi soal tanpa gambar, maka pratinjaunya tidak menyisakan ruang kosong
      tempat gambar.
- [ ] **AC-ITM-22** (FR-ITM-24) Diberi satu soal yang tampilannya diubah dari *Settings View*, saat admin
      menyimpannya, maka soal lain di kumpulan yang sama tidak ikut berubah.
