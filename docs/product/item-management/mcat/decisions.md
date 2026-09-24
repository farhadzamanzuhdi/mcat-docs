# MCAT — keputusan

Kenapa bentuknya begini. Entri **bertanggal, hanya bertambah ke bawah, dan tidak pernah disunting ulang** —
kalau sebuah keputusan dibatalkan, tulis keputusan baru yang menyebutnya.

Bagaimana sistemnya bekerja **sekarang** ada di [`README.md`](README.md) dan di keempat berkas tiap layar. Kalau
dokumen di sini bertentangan dengan yang di sana, yang benar yang di sana.

Tiap entri wajib punya baris **Ditolak:**. Keputusan tanpa alternatif yang ditolak bukan keputusan, itu
deskripsi — dan deskripsi tempatnya di `README.md`.

---

## DEC-MCAT-01 — Timer menempel di Question Set, bukan di komponen · 2026-09-21

Waktu pengerjaan Sesi 1 disimpan di Question Set (`duration_minutes`), bukan satu timer untuk seluruh Tool
Component.

**Ditolak:** satu timer gabungan untuk Sesi 1 dan Sesi 2, karena panjang Sesi 2 tidak tetap — ia berhenti antara
`n_min` dan `n_max` soal. Timer gabungan membuat peserta yang lambat di Sesi 1 dirugikan justru di bagian yang
menentukan skor.

Menegakkan: [BR-SET-05](question-sets/README.md)

---

## DEC-MCAT-02 — Sesi 1 tidak punya tingkat kesulitan dan tidak menghitung theta · 2026-09-21

Soal Sesi 1 dianggap setara. Tidak ada kolom tingkat kesulitan, tidak ada parameter statistik, dan skornya
jumlah jawaban benar per dimensi — bukan theta.

**Ditolak:** menyimpan tingkat kesulitan sebagai informasi penyusun soal saja. Kolom yang ada tapi tidak dipakai
apa pun akan ditafsirkan orang berikutnya sebagai kolom yang ikut menyusun paket, dan aturannya tidak pernah
ada. Theta juga ditolak untuk Sesi 1 karena theta hanya berarti untuk dimensi yang punya soal terkalibrasi.

Menegakkan: [BR-MCAT-04](README.md#4-aturan-besar)

---

## DEC-MCAT-03 — Library dihapus, soal berdiri sendiri di Item Bank · 2026-09-22

`master_mcat_library` dijatuhkan seluruhnya. Soal tidak lagi dimiliki satu Question Set; hubungan soal–set jadi
many-to-many lewat tabel jembatan, dan soal dikelola dari menu Item Bank tersendiri.

Sebelum ini soal milik tepat satu set dan hidup di dalam satu library. Akibatnya soal tidak bisa dipakai ulang,
tidak bisa dicari, dan library sendiri tidak memegang tugas apa pun — bahasa dan sesi sudah menempel di Question
Set.

**Ditolak:** mempertahankan library sebagai wadah pengelompokan saja. Wadah yang tidak menegakkan satu aturan pun
adalah satu tingkat hierarki yang harus dilewati tiap layar, tiap query, dan tiap orang baru, tanpa imbalan.

Menegakkan: [Item Bank](item-bank/README.md), [BR-SET-03](question-sets/README.md)

---

## DEC-MCAT-04 — Soal punya kode unik global · 2026-09-22

Tiap soal punya `code` unik seperti `NUMERIC-0042`, dinomori per prefiks, dan dipakai sebagai kunci pencocokan
impor CSV. Nomornya diambil `max + 1` per prefiks di dalam transaksi insert, tanpa tabel counter.

**Ditolak:** kode unik per bahasa atau per dimensi, karena tujuan kode adalah jadi satu acuan yang dicari — dua
soal berkode sama di dua bahasa mengembalikan dua hasil untuk satu pencarian. Juga ditolak: impor yang selalu
menambah, karena mengunggah ulang berkas yang sama menggandakan soalnya dan siklus "unggah, perbaiki, unggah
lagi" jadi tidak aman.

Menegakkan: [BR-ITM-01](item-bank/README.md), [BR-IMP-01](item-bank/import/README.md)

---

## DEC-MCAT-05 — Package sebagai satu benda bernama · 2026-09-22

Pasangan {Question Set Sesi 1, Adaptive Setup} dulu hanya hidup sebagai dua field di dalam item MCAT. Pasangan
itu sekarang jadi entitas bernama: **Package**. Sesi 1-nya menunjuk **beberapa** Question Set berurutan, karena
satu set mengukur satu dimensi sementara skor Sesi 1 dilaporkan per dimensi.

Satu setelan sengaja **tidak** ikut ke Package: `session1_shuffle` tetap di item, karena dua tes boleh
menjalankan paket yang sama dengan urutan soal berbeda.

**Ditolak:** membiarkan pasangan itu tinggal di item. Tanpa nama ia tidak bisa dirujuk, dicari, atau
dibicarakan, dan tes yang sama yang dipasang di lima Tool Component harus dikonfigurasi lima kali.

Menegakkan: [Packages](packages/README.md)

---

## DEC-MCAT-06 — Halaman MCAT Assignment dihapus · 2026-09-22

`/mcat-builder/assignment` dan endpoint `GET /admin/secured/tool/component/mcat` dihapus. Tempatnya di menu
diisi **Packages**.

Halaman itu menjawab satu pertanyaan: item MCAT mana saja yang ada, dan masing-masing menjalankan apa. Sesudah
Package, pertanyaan itu pecah dua dan keduanya punya rumah yang lebih dekat — "paket ini dipakai di mana"
dijawab kolom *Used by* di halaman Packages, dan "item ini menjalankan apa" dijawab inspector item itu sendiri.

**Ditolak:** mempertahankannya sebagai layar audit. Yang tersisa hanyalah satu tabel yang membacakan kembali apa
yang sudah tertulis di dua tempat lain, dan yang harus ikut diperbarui tiap kali salah satunya berubah.

Satu hal memang hilang: daftar lintas-tes berisi item MCAT yang **belum** dipasangi paket. Kalau audit seperti
itu nanti dibutuhkan, tempatnya filter di daftar Tool Component, bukan halaman Assignment yang lama.

---

## DEC-MCAT-07 — Dimensi jadi master data · 2026-09-22

Daftar dimensi dipindah dari konstanta di dalam kode ke tabel `ref_mcat_dimension` dengan layarnya sendiri.
Kodenya tidak dapat diubah setelah dibuat; dimensi yang tidak dipakai lagi ditandai nonaktif, bukan dihapus.

Daftar dimensi milik pelanggan, bukan milik sistem: satu pelanggan mengukur empat kemampuan, pelanggan lain dua
puluh.

**Ditolak:** membiarkannya sebagai enum di kode. Menambah satu kemampuan berarti deploy. Juga ditolak:
mengizinkan kode diubah — kode tersimpan sebagai kunci bobot tiap soal, isi `tested_dimensions` tiap setup, dan
nama kolom berkas impor; mengubahnya memutus ketiganya tanpa jejak, dan tidak ada transaksi yang melindungi
rename berkaskade ke tiga tempat itu.

Menegakkan: [BR-DIM-02](dimensions/README.md), [BR-DIM-04](dimensions/README.md)

---

## DEC-MCAT-08 — Pemilih dimensi jamak memakai kotak pilih berlabel · 2026-09-23

Tempat yang memilih banyak dimensi sekaligus — `WeightsEditor` di laci soal Sesi 2, dan `tested_dimensions` di
Adaptive Setup — memakai kotak pilih yang bisa dicari, dengan yang terpilih tampil sebagai label yang bisa
ditutup. Baris angka di bawahnya tetap ada, karena tiap dimensi terpilih membawa angka.

**Ditolak:** satu saklar untuk tiap dimensi yang ada, bentuk sebelumnya. Terbaca saat dimensinya empat, tidak
terpakai saat dimensinya seratus. Juga ditolak: mengganti baris angka dengan label saja — label tidak bisa
memuat penggeser bobot.

Menegakkan: [FR-DIM-08](dimensions/README.md)
