# Packages

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → Packages |
| **Rute** | `/mcat-builder/packages`; dipasang dari `/tool-component/:id` |
| **Slug ID** | `PKG` |
| **Prasyarat** | [Question Sets](../question-sets/README.md), [Adaptive Setups](../adaptive-setups/README.md) |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

Package adalah **satu paket tes bernama**: Sesi 1 memakai kumpulan soal yang mana saja, Sesi 2 memakai setup
yang mana. Inilah yang dipilih saat MCAT dipasang ke sebuah tes.

Tanpa paket, pemasangan berarti memilih beberapa hal terpisah di tiap tes. Tes yang sama yang dipasang di lima
Tool Component dikonfigurasi lima kali, dan tidak ada nama yang bisa dirujuk saat orang membicarakannya — lihat
[DEC-MCAT-05](../decisions.md).

Sesi 1 jamak karena satu kumpulan mengukur satu kemampuan, dan skor Sesi 1 dilaporkan per kemampuan.

## 2. Alur pengguna

1. Admin membuka **MCAT → Packages** dan memilih bahasanya.
2. Tombol **New package** membuka dialog: nama, deskripsi, kumpulan soal Sesi 1 beserta urutannya, dan satu
   Adaptive Setup untuk Sesi 2.
3. Paket tersimpan dan muncul di daftar, lengkap dengan isinya dan angka berapa tes yang memakainya.
4. Membuka baris paket menampilkan tes mana saja yang memakainya.
5. Di **Item Management → Tool Component**, admin menambahkan item bertipe MCAT ke sebuah halaman, lalu memilih
   **satu paket** dari dropdown. Dialognya menampilkan isi paket itu.
6. Satu setelan tetap tinggal di tes, bukan di paket: **acak urutan soal Sesi 1**.

## 3. Kebutuhan fungsional

### 3.1 Mengelola paket

- **FR-PKG-01** Admin dapat membuat paket dengan mengisi nama (wajib) dan deskripsi (opsional).
- **FR-PKG-02** Admin dapat memilih **beberapa** kumpulan Sesi 1 untuk sebuah paket, satu per kemampuan,
  beserta urutan pengerjaannya.
- **FR-PKG-03** Admin dapat memilih satu Adaptive Setup untuk Sesi 2.
- **FR-PKG-04** Daftar paket menampilkan isi Sesi 1 dan Sesi 2-nya, dan berapa tes yang memakainya.
- **FR-PKG-05** Membuka sebuah baris paket menampilkan tes mana saja yang memakainya.
- **FR-PKG-06** Admin dapat mengubah dan menghapus paket.

### 3.2 Memasang ke tes

- **FR-PKG-07** Item MCAT di dalam Tool Component memilih **satu paket**, bukan kumpulan dan setup terpisah.
- **FR-PKG-08** Dialog pemasangan menampilkan isi paket yang dipilih.
- **FR-PKG-09** Acak urutan soal Sesi 1 adalah setelan **pemasangan**, disimpan di item, bukan di paket.
- **FR-PKG-10** Item MCAT yang belum memilih paket ditandai di inspector-nya dan di tree Tool Component.
- **FR-PKG-11** Mengubah isi paket berlaku untuk semua tes yang memakainya sekaligus.

## 4. Aturan bisnis

- **BR-PKG-01** Sebuah paket tidak boleh kosong dua-duanya. Minimal salah satu dari Sesi 1 atau Sesi 2 terisi.
- **BR-PKG-02** Kumpulan yang ditunjuk harus kumpulan **Sesi 1**; setup yang ditunjuk menjalankan konten
  Sesi 2.
- **BR-PKG-03** Satu paket tidak boleh memuat dua kumpulan **berkemampuan sama** — kemampuan itu akan dinilai
  dua kali. Gabungkan jadi satu kumpulan, atau pakai "Questions served".
- **BR-PKG-04** Satu paket terikat satu bahasa, dan bahasanya terkunci setelah paket dibuat. Tiap kumpulan dan
  setup di dalamnya harus berbahasa sama.
- **BR-PKG-05** Paket tidak dapat dihapus selama masih dipakai item MCAT. Pesannya menyebut tes mana saja.
- **BR-PKG-06** Kumpulan dan setup tidak dapat dihapus selama masih ditunjuk sebuah paket.
- **BR-PKG-07** Urutan kumpulan Sesi 1 ditentukan admin. Yang diacak adalah soal di dalam tiap kumpulan, bukan
  urutan kemampuannya.
- **BR-PKG-08** Acak urutan soal hanya berpengaruh pada kumpulan yang "Questions served"-nya kosong — kalau
  terisi, urutannya sudah acak.

## 5. Di luar cakupan

- Menyalin paket. Belum dijadwalkan.
- Penjadwalan: paket yang berlaku di rentang tanggal tertentu. Belum dijadwalkan.
- Satu layar yang mengumpulkan tes-tes yang **belum** dipasangi paket. Penandanya ada di tiap tes, tapi tidak
  ada daftar lintas tes — lihat [DEC-MCAT-06](../decisions.md).

## 6. Pertanyaan terbuka

- **Q-PKG-01** Versi paket — mengunci isinya supaya tes yang sedang berjalan tidak ikut berubah. Perlu diangkat
  sebelum ada peserta sungguhan. — *menunggu:* PO
- **Q-PKG-02** Saat Tool Component diduplikasi, item MCAT-nya ikut menunjuk paket yang sama, atau dikosongkan?
  Usulan: ikut disalin. — *menunggu:* PO
