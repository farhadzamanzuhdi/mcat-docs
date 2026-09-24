# Impor soal — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. Template

- [ ] **AC-IMP-01** (FR-IMP-01, FR-IMP-02) Diberi halaman Question Set atau Item Bank, saat admin mengunduh
      template, maka berkas CSV terunduh berisi header dan satu baris contoh terisi.
- [x] **AC-IMP-02** (FR-IMP-03) Diberi template CSV Sesi 1, maka tidak ada kolom tingkat kesulitan dan tidak
      ada kolom parameter statistik. Yang ada: `code`, `content`, `item_format`, `dimension`, `answer_key`, dan
      kolom pilihan.
- [x] **AC-IMP-03** (FR-IMP-03) Diberi template CSV Sesi 2, maka ada satu kolom `a_<kemampuan>` untuk tiap
      kemampuan aktif.

## 2. Mengunggah

- [x] **AC-IMP-04** (FR-IMP-05) Diberi CSV berisi soal yang sah, saat admin mengunggahnya ke sebuah Question
      Set, maka soal itu masuk ke kolam kumpulan itu dan mengambil kemampuannya walau kolom `dimension`
      dikosongkan.
- [ ] **AC-IMP-05** (FR-IMP-06, FR-IMP-07, BR-IMP-01) Diberi berkas yang sama diunggah dua kali, maka unggahan
      kedua melaporkan 0 dibuat dan 1 diperbarui — bukan menggandakan soalnya.
- [x] **AC-IMP-06** (FR-IMP-08, FR-IMP-09) Diberi CSV berisi beberapa baris dengan salah satunya salah, saat
      admin mengunggahnya, maka baris yang benar tetap masuk dan laporannya menyebut nomor baris yang gagal
      beserta alasannya.
- [ ] **AC-IMP-07** (BR-IMP-04) Diberi CSV berisi header saja tanpa baris data, saat admin mengunggahnya, maka
      sistem menolak dengan pesan jelas — bukan melaporkan impor berhasil dengan 0 soal.
- [ ] **AC-IMP-08** (FR-IMP-10) Diberi CSV yang urutan kolomnya berbeda dari template, saat admin
      mengunggahnya, maka impornya tetap berhasil.
- [ ] **AC-IMP-09** (BR-IMP-02) Diberi CSV berisi soal dengan kunci jawaban yang tidak cocok, saat admin
      mengunggahnya, maka baris itu ditolak dengan alasan yang sama seperti bila soal itu diketik manual.
- [ ] **AC-IMP-10** (BR-IMP-03) Diberi CSV yang baris ketiganya salah, maka laporannya menyebut baris 3 — nomor
      yang sama dengan yang dilihat admin di spreadsheet.
