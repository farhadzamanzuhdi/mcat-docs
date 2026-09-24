# Dimensions

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → Dimensions |
| **Rute** | `/mcat-builder/dimensions` |
| **Slug ID** | `DIM` |
| **Prasyarat** | — |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

Dimensi adalah **kemampuan yang diukur** alat tes ini: penalaran angka, verbal, spasial, logika, dan apa pun
yang ditambahkan setelahnya. Tiap soal mengukur setidaknya satu dimensi, tiap Question Set Sesi 1 dinilai
terhadap tepat satu dimensi, dan tiap Adaptive Setup menyebut dimensi mana saja yang diperkirakan. Karena itu
daftar dimensi adalah hal pertama yang harus ada sebelum satu soal pun bisa ditulis.

Daftar itu milik pelanggan, bukan milik sistem. Satu pelanggan mengukur empat kemampuan, pelanggan lain
mengukur dua puluh. Karena itu dimensi dirawat dari layar tersendiri, bukan ditanam di dalam kode — lihat
[DEC-MCAT-07](../decisions.md).

## 2. Alur pengguna

1. Admin membuka **Item Management → MCAT → Dimensions**.
2. Tabel menampilkan seluruh dimensi: kode, nama, deskripsi, dan statusnya.
3. Tombol **Add Dimension** membuka formulir berisi nama, kode, dan deskripsi.
4. Tombol ubah pada satu baris membuka formulir yang sama, dengan tambahan saklar status. Kodenya tidak bisa
   diubah lagi.
5. Mematikan saklar status membuat dimensi itu **tidak dipakai**: hilang dari semua pilihan, tetapi seluruh
   soal yang sudah mengukurnya tetap utuh.
6. Tombol hapus hanya berhasil untuk dimensi yang belum dipakai apa pun. Yang sudah dipakai ditolak, dengan
   pesan yang menyebut siapa yang memakainya.

## 3. Kebutuhan fungsional

- **FR-DIM-01** Admin dapat melihat daftar seluruh dimensi, termasuk yang sudah tidak dipakai, beserta
  statusnya.
- **FR-DIM-02** Admin dapat menambah dimensi dengan mengisi nama dan kode pendek, misalnya `numeric`.
- **FR-DIM-03** Admin dapat mengubah nama dan deskripsi sebuah dimensi.
- **FR-DIM-04** Admin dapat menandai sebuah dimensi tidak dipakai lagi, dan mengembalikannya.
- **FR-DIM-05** Admin dapat menghapus dimensi yang belum dipakai konten apa pun.
- **FR-DIM-06** Admin dapat mencari dimensi dari kode maupun namanya.
- **FR-DIM-07** Tiap layar yang meminta dimensi hanya menawarkan dimensi yang masih dipakai.
- **FR-DIM-08** Layar yang meminta lebih dari satu dimensi menawarkannya lewat kotak pilih yang bisa dicari,
  dan yang terpilih tampil sebagai label yang bisa ditutup satu per satu.

## 4. Aturan bisnis

- **BR-DIM-01** Kode dimensi unik dan ditulis huruf kecil. Menambah kode yang sudah ada ditolak.
- **BR-DIM-02** Kode dimensi tidak dapat diubah setelah dimensinya dibuat. Kode itu tersimpan sebagai kunci
  bobot tiap soal, sebagai isi daftar dimensi tiap Adaptive Setup, dan sebagai nama kolom berkas impor —
  mengubahnya akan memutus ketiganya tanpa jejak. Kode yang salah ketik dihapus lalu ditulis ulang.
- **BR-DIM-03** Dimensi tidak dapat dihapus selama masih ada soal, Question Set, atau Adaptive Setup yang
  memakainya. Pesannya menyebut berapa banyak masing-masing, dan menyarankan menandainya tidak dipakai.
- **BR-DIM-04** Dimensi yang tidak dipakai lagi tidak dapat dipilih untuk konten baru, tetapi soal yang sudah
  mengukurnya tetap terbaca, tetap bisa disunting, dan tetap dinilai seperti sebelumnya.
- **BR-DIM-05** Soal Sesi 1 tetap boleh disimpan pada dimensi yang sudah tidak dipakai, selama dimensinya tidak
  diganti. Menggantinya berarti memilih, dan pilihan hanya boleh jatuh pada dimensi yang masih dipakai.

## 5. Di luar cakupan

- **Pengelompokan dimensi.** Tidak ada kategori atau induk dimensi; daftarnya rata.
- **Urutan tampil.** Dimensi selalu urut kode; belum ada urutan yang bisa diatur admin.
- **Impor massal dimensi.** Ditambahkan satu per satu dari layar.
- **Pencarian di sisi server.** Seluruh daftar dikirim sekali, pencarian terjadi di layar.

## 6. Pertanyaan terbuka

- **Q-DIM-01** Daftar dimensi berlaku untuk seluruh instalasi. Perlu dipisah per perusahaan, atau satu daftar
  bersama memang cukup? — *menunggu:* PO
