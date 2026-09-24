# Question Sets

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → Question Sets |
| **Rute** | `/mcat-builder/sets`, `/mcat-builder/sets/:setId` |
| **Slug ID** | `SET` |
| **Prasyarat** | [Dimensions](../dimensions/README.md), [Item Bank](../item-bank/README.md) |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

Question Set adalah benda utama Sesi 1: satu kumpulan soal yang mengukur **satu kemampuan**, punya waktu
pengerjaan sendiri, dan menentukan berapa soal yang benar-benar keluar ke peserta.

Satu kumpulan mengukur satu kemampuan — itu yang membuat skor Sesi 1 bisa dilaporkan per kemampuan, dan itu
sebabnya tes yang mengukur tiga kemampuan butuh tiga kumpulan.

Kumpulan tidak memiliki soalnya; ia memegang **kolam** — daftar soal yang boleh dikeluarkannya. Soal yang sama
bisa ada di beberapa kolam sekaligus, dan kolam selalu lebih kecil daripada Item Bank.

Dialog **Language Setting** juga tinggal di halaman ini. Bahasa bukan layar tersendiri; aturannya ada di
[README area §4.1](../README.md#41-bahasa-konten).

## 2. Alur pengguna

1. Admin membuka **MCAT → Question Sets**, memilih tab sesi dan bahasanya.
2. Daftarnya menampilkan nama, kemampuan, jumlah soal di kolam, berapa yang keluar, waktu pengerjaan, dan
   terakhir diubah.
3. Tombol **Add question set** membuka form: nama, deskripsi, kemampuan, waktu pengerjaan, dan berapa soal yang
   keluar.
4. Menekan sebuah baris membuka halaman kumpulan itu: kolam soalnya, panel tampilan, dan pratinjaunya.
5. Kolam diisi lewat **Add from Item Bank** (memilih soal yang sudah ada) atau **Add item** (menulis soal baru
   sekaligus memasukkannya).
6. Tombol Back kembali ke daftar dengan tab dan bahasa yang sama seperti tadi.

## 3. Kebutuhan fungsional

### 3.1 Daftar

- **FR-SET-01** Admin dapat melihat seluruh kumpulan satu sesi dan satu bahasa dalam satu daftar.
- **FR-SET-02** Daftar menampilkan nama, kemampuan, jumlah soal di kolam, berapa soal yang keluar, waktu
  pengerjaan, dan waktu terakhir diubah.
- **FR-SET-03** Sesi adalah tab, dan tab itu ada di alamat halaman.
- **FR-SET-04** Tiap baris punya aksi **Edit** dan **Delete** yang tidak ikut membuka halaman kumpulan saat
  ditekan.

### 3.2 Membuat dan mengubah

- **FR-SET-05** Admin dapat membuat kumpulan dengan mengisi nama (wajib), deskripsi (opsional), kemampuan
  (wajib untuk Sesi 1), waktu pengerjaan (wajib untuk Sesi 1), dan berapa soal yang keluar (opsional).
- **FR-SET-06** Waktu pengerjaan diisi dalam menit, minimal 1.
- **FR-SET-07** Berapa soal yang keluar menentukan jumlah soal yang benar-benar dikerjakan peserta, diambil
  acak dari kolam. Dikosongkan berarti seluruh kolam keluar sesuai urutannya.
- **FR-SET-08** Admin dapat menghapus kumpulan yang belum ditunjuk Package maupun Adaptive Setup.

### 3.3 Kolam soal

- **FR-SET-09** Halaman kumpulan menampilkan kolamnya: kode, isi soal, kemampuan, dipakai di berapa kumpulan,
  dan keterpaparan.
- **FR-SET-10** Tombol **Add from Item Bank** membuka pemilih yang sudah tersaring ke kemampuan, bahasa, dan
  sesi kumpulan itu, lengkap dengan pencarian dan aksi *Select all matching*.
- **FR-SET-11** Tombol **Add item** menulis soal baru sekaligus memasukkannya ke kolam ini dan ke Item Bank.
- **FR-SET-12** Tiap baris kolam punya dua aksi berbeda maknanya: **Remove from set** dan **Delete question**.
- **FR-SET-13** Urutan kolam dapat diatur admin, dan hanya ketika berapa soal yang keluar dikosongkan.

### 3.4 Bahasa

- **FR-SET-14** Tombol **Language Setting** membuka dialog daftar bahasa: tambah, ubah nama dan deskripsi, dan
  hapus yang belum dipakai.
- **FR-SET-15** Tiap perubahan di dialog itu langsung tersimpan; menutup dialog tidak membatalkan apa pun.

### 3.5 Tampilan satu kumpulan

- **FR-SET-16** Panel **Display settings** menampilkan pratinjau memakai soal sungguhan dari kolam kumpulan
  itu, dan pratinjaunya berubah seketika saat pengaturannya diubah.
- **FR-SET-17** Admin dapat menerapkan posisi gambar, rasio, dan tata letak jawaban ke seluruh soal di kolam
  satu kumpulan sekaligus.

## 4. Aturan bisnis

- **BR-SET-01** Kumpulan Sesi 1 wajib punya **tepat satu** kemampuan, dan itulah kemampuan yang dinilai.
  Kumpulan Sesi 2 tidak punya kemampuan di level kumpulan.
- **BR-SET-02** Kemampuan tidak dapat diubah selama kolamnya berisi soal — itu akan mengubah arti seluruh soal
  di dalamnya. Admin mengosongkan kolamnya dulu, atau membuat kumpulan baru.
- **BR-SET-03** Kolam hanya menerima soal yang **kemampuan, bahasa, dan sesinya** sama dengan kumpulan itu.
- **BR-SET-04** Berapa soal yang keluar tidak boleh lebih besar dari isi kolam. Tiga tempat, tiga perilaku:

  | Di mana | Perilakunya | Kenapa begitu |
  | --- | --- | --- |
  | Field-nya sendiri | Steppernya **berhenti** di jumlah kolam; kolam kosong membuatnya nonaktif | Batasnya fakta tentang kumpulan itu. Stepper yang berhenti menyampaikannya tanpa admin harus menabrak error |
  | Mengecilkan kolam di bawah angka itu | **Ditolak**, pesannya menyuruh menurunkan angkanya dulu | Berapa soal yang dikerjakan peserta ikut menentukan skor. Itu berubah kalau admin bilang begitu, bukan sebagai efek samping merapikan kolam |
  | Mengosongkan kolam sama sekali | Boleh | Kumpulan tanpa kolam tidak menjalankan apa pun, jadi tidak ada yang dijanjikan |

- **BR-SET-05** Waktu pengerjaan Sesi 1 menempel di kumpulan, bukan satu timer untuk seluruh Tool Component —
  lihat [DEC-MCAT-01](../decisions.md).
- **BR-SET-06** Kalau berapa soal yang keluar terisi, urutan kolam tidak berpengaruh, dan pegangan
  penggesernya disembunyikan.
- **BR-SET-07** Kumpulan tidak dapat dihapus selama masih ditunjuk sebuah Package atau Adaptive Setup.
  Menghapusnya hanya melepas kolamnya; soal-soalnya tetap hidup di Item Bank.
- **BR-SET-08** Satu kumpulan boleh dipakai beberapa Package sekaligus.
- **BR-SET-09** Bahasa dan sesi sebuah kumpulan tidak dapat dipindah setelah dibuat.
- **BR-SET-10** Menerapkan tampilan ke seluruh kumpulan berarti menulis nilai yang sama ke tiap soal di
  kolamnya, bukan menyimpan satu nilai di kumpulan itu. Karena pengaturannya milik soal, kumpulan lain yang
  memakai soal yang sama **ikut** terpengaruh.
- **BR-SET-11** Penerapan itu menimpa pengaturan tiap soal dan tidak dapat dibatalkan — karena itu ada
  konfirmasinya.

## 5. Di luar cakupan

- Menyalin kumpulan beserta isinya. Belum dijadwalkan.
- Menyusun kumpulan otomatis berdasarkan komposisi tingkat kesulitan. Tidak relevan: soal Sesi 1 tidak punya
  tingkat kesulitan.
- Pratinjau beberapa soal sekaligus di panel tampilan. Yang dirender satu soal per saat.

## 6. Pertanyaan terbuka

- **Q-SET-01** Mengacak urutan **pilihan jawaban** — usulan: ya, sebagai setelan per Question Set. Belum
  dibangun. — *menunggu:* PO
- **Q-SET-02** Peserta boleh mundur ke soal sebelumnya di Sesi 1? Usulan: boleh di Sesi 1, tidak di Sesi 2.
  — *menunggu:* PO
- **Q-SET-03** Berapa soal per halaman di sisi peserta? Usulan: 1 soal per halaman. — *menunggu:* PO
- **Q-SET-04** Satu dimensi yang kolamnya melebihi satu kumpulan — sekarang satu kumpulan per dimensi per
  bahasa. Kalau nanti satu kumpulan tidak cukup, perlu kolam lintas kumpulan? — *menunggu:* PO
