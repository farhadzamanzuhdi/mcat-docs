# MCAT — tes kemampuan kognitif

| | |
| --- | --- |
| **Menu** | Item Management → MCAT |
| **Rute** | `/mcat-builder/*` |
| **Slug ID** | `MCAT` (hal yang melintasi layar; tiap layar punya slug sendiri) |
| **Teknis** | [model data](data-model.md) · [arsitektur](architecture.md) · [keputusan](decisions.md) |

## 1. Ringkasan

MCAT adalah tes kemampuan kognitif yang dikerjakan peserta dalam dua bagian.

| | Sesi 1 | Sesi 2 |
| --- | --- | --- |
| Bentuknya | Soal **tetap** — semua peserta dapat soal yang sama | Tes **adaptif** — soal berikutnya dipilih dari jawaban sebelumnya |
| Isinya | Question Set, satu per kemampuan yang diukur | Kumpulan soal terkalibrasi yang dijelajahi algoritma |
| Panjangnya | Tetap: seluruh kolam, atau sejumlah soal acak dari kolam itu | Berubah-ubah, berhenti saat perkiraannya sudah cukup akurat |
| Waktunya | Diatur per Question Set | Ditentukan panjang tes di setup |
| Hasilnya | Jumlah jawaban benar per kemampuan | Perkiraan kemampuan (theta) per dimensi |

Semua soal MCAT **pilihan ganda**, di kedua sesi.

## 2. Pengguna & akses

Admin konten dan psikometrikus. Saat ini seluruh pengguna CMS yang login dapat membuka menu MCAT; permission
tersendiri belum ada — lihat [Q-MCAT-03](#7-pertanyaan-terbuka).

| Peran | Bisa apa |
| --- | --- |
| Admin konten | Dimensi, soal, kumpulan soal, tampilan soal, impor |
| Psikometrikus | Menentukan kemampuan yang diukur, dan seluruh parameter algoritma Sesi 2 |
| Admin tes | Memasang paket MCAT ke sebuah Tool Component |

## 3. Bagaimana bagiannya tersusun

```
Soal  ──┐                                 satu soal boleh dipakai banyak Question Set
        ├──►  Question Set  ──┐
Soal  ──┘     (satu kemampuan)│
                              ├──►  Package  ──►  dipasang ke Tool Component
        Adaptive Setup  ──────┘
```

Tiga hal yang menjelaskan hampir semua aturan di area ini:

**Soal berdiri sendiri.** Soal tidak dimiliki satu Question Set. Question Set memegang **kolam** — daftar soal
yang boleh dikeluarkannya. Soal yang sama bisa ada di beberapa kolam sekaligus, dan mengeluarkan soal dari
sebuah kolam tidak menghapus soalnya. Item Bank karena itu selalu berisi **lebih banyak** soal daripada kolam
mana pun.

**Satu Question Set mengukur satu kemampuan.** Itu sebabnya skor Sesi 1 bisa dilaporkan per kemampuan, dan itu
sebabnya satu tes yang mengukur tiga kemampuan butuh tiga Question Set.

**Package memegang konfigurasi, Question Set memegang isi.** Tes yang sama yang dipasang di lima Tool Component
menunjuk satu Package yang sama. Mengubah isi Package berlaku untuk kelimanya sekaligus.

## 4. Aturan besar

Berlaku di seluruh layar MCAT. Yang khas satu layar ada di `README.md` layar itu.

- **BR-MCAT-01** Semua soal pilihan ganda, 2 sampai 8 pilihan, kunci jawaban wajib cocok dengan salah satunya.
- **BR-MCAT-02** Seluruh konten satu tes berada dalam **satu bahasa**. Tidak ada pencampuran.
- **BR-MCAT-03** Tiap soal punya **kode unik**, misalnya `NUMERIC-0042`. Itu yang dipakai mencari soal dan
  mencocokkan baris saat impor.
- **BR-MCAT-04** Soal Sesi 1 tidak punya tingkat kesulitan dan tidak punya parameter statistik. Semua soal
  Sesi 1 dianggap setara; yang membedakan hanya kemampuan yang diukurnya.
- **BR-MCAT-05** Sesuatu tidak dapat dihapus selama masih dipakai. Soal yang masih ada di kolam, Question Set
  yang masih ditunjuk Package, Package yang masih dipasang di tes — ketiganya ditolak, dengan pesan yang
  menyebut siapa yang masih memakainya.
- **BR-MCAT-06** Soal yang sudah pernah dikerjakan peserta tidak dapat dihapus.

### 4.1 Bahasa konten

Bahasa bukan layar tersendiri — ia tidak ada di `LOCAL_MENUS`, dan dirawat dari dialog **Language Setting** di
halaman Question Sets. Tapi perilakunya berlaku di seluruh area, jadi aturannya tinggal di sini.

- **BR-MCAT-07** Bahasa adalah **ruang lingkup**, bukan penyaring. Item Bank, Question Sets, Adaptive Setups,
  dan Packages semuanya bekerja dalam satu bahasa; berganti bahasa berarti melihat kumpulan konten yang
  sepenuhnya lain. Dropdown bahasa karena itu tidak punya pilihan "semua".
- **BR-MCAT-08** Kode bahasa unik dan disimpan huruf besar, misalnya `EN`, `ID`. Menambah kode yang sudah ada
  ditolak.
- **BR-MCAT-09** Bahasa tidak dapat dihapus selama masih ada Question Set atau soal yang ditulis dalamnya.
  Pesannya menyebut berapa banyak masing-masing.
- **BR-MCAT-10** Bahasa sebuah Question Set, soal, atau Package tidak dapat dipindah setelah dibuat. Soal yang
  sama dalam tiga bahasa adalah tiga soal, masing-masing dengan kodenya sendiri.
- **BR-MCAT-11** Perubahan di dialog bahasa tersimpan saat ditulis. Dialognya hanya punya tombol **Close**,
  bukan Cancel dan Save.
- **BR-MCAT-12** Bahasa yang sedang dipilih ada di alamat halaman, sehingga layar yang tersaring bisa
  ditautkan.

## 5. Layar

Urut sesuai urutan pekerjaannya — dan itu juga urutannya di sidebar.

| Urutan | Layar | Isinya |
| --- | --- | --- |
| 1 | [Dimensions](dimensions/README.md) | Kemampuan apa saja yang diukur alat tes ini |
| 2 | [Item Bank](item-bank/README.md) | Menulis, mencari, dan merawat soal |
| 2a | [→ Impor soal](item-bank/import/README.md) | Unggah soal massal lewat CSV. Alur di dalam Item Bank |
| 3 | [Question Sets](question-sets/README.md) | Kumpulan soal, kolamnya, timer, dan berapa soal yang keluar |
| 4 | [Adaptive Setups](adaptive-setups/README.md) | Algoritma Sesi 2 |
| 5 | [Packages](packages/README.md) | Paket tes, dan pemasangannya ke Tool Component |

Paket itulah yang dipilih saat MCAT dipasang ke sebuah tes, lewat
**[Item Management → Tool Component](../tool-component/README.md)**.

## 6. Di luar cakupan

Sudah diputuskan tidak dikerjakan sekarang.

- **Aplikasi peserta.** Layar yang dipakai peserta mengerjakan Sesi 1 belum dibangun.
- **Riwayat perubahan soal.** Tidak ada versi; soal yang diubah langsung berlaku di tes yang memakainya.
- **Impor Excel.** Hanya CSV.
- **Bank gambar.** Gambar tertanam di database sebagai data URL.
- **Kalibrasi parameter soal dari data jawaban.** Parameter diisi manual atau lewat impor.
- **Alat psikometri yang belum diport dari `mcat-api`:** playground langkah-per-langkah, daftar sesi peserta,
  halaman debug pemilihan soal, hapus massal soal, impor XLSX.

## 7. Pertanyaan terbuka

Belum ada yang memutuskan. Terjawab berarti pindah ke §4, ke §6, atau ke [`decisions.md`](decisions.md).

- **Q-MCAT-01** Teks pengganti gambar (*alt text*) untuk aksesibilitas — wajib, opsional, atau tidak ada?
  Perlu diputuskan sebelum ada peserta sungguhan. — *menunggu:* PO
- **Q-MCAT-02** Bentuk laporan akhir. Sesi 1 menyimpan jawaban benar per kemampuan dan Sesi 2 menyimpan theta
  apa adanya; bagaimana keduanya digabung dan dinormakan belum ditentukan. — *menunggu:* PO
- **Q-MCAT-03** Permission menu tersendiri untuk MCAT. Sekarang semua pengguna CMS yang login bisa
  membukanya, termasuk mengubah seluruh parameter algoritma. — *menunggu:* PO
- **Q-MCAT-04** Gambar soal disimpan sebagai data URL di database. Untuk produksi sebaiknya berkas di storage
  dengan URL saja di database — kapan pindah? — *menunggu:* PO
- **Q-MCAT-05** Kalau waktu habis di tengah Sesi 2, hasilnya dianggap apa? Usulan: skor dari soal yang sudah
  dijawab, ditandai "berhenti karena waktu". — *menunggu:* PO
- **Q-MCAT-06** Peserta yang berhenti di tengah lalu lanjut lagi — melanjutkan dengan estimasi terakhir, atau
  mengulang dari awal? Usulan: melanjutkan. — *menunggu:* PO

## 8. Kamus istilah

| Istilah | Artinya |
| --- | --- |
| **Sesi 1** | Bagian tes yang soalnya tetap |
| **Sesi 2** | Bagian tes yang adaptif |
| **Dimensi** | Kemampuan yang diukur — penalaran angka, verbal, spasial, logika |
| **Item Bank** | Daftar semua soal, lepas dari kumpulan mana pun |
| **Kolam** | Soal-soal yang dipegang satu Question Set |
| **Kode soal** | Penanda pendek dan unik tiap soal, mis. `NUMERIC-0042` |
| **Package** | Paket tes bernama: Question Set Sesi 1 mana saja, dan Adaptive Setup mana |
| **Adaptive Setup** | Konfigurasi algoritma Sesi 2 |
| **Tool Component** | Wadah tes yang akhirnya sampai ke peserta |
| **Theta** | Angka perkiraan kemampuan peserta. Hanya Sesi 2 |
