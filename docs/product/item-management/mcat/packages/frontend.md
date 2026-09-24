# Packages — Frontend

| | |
| --- | --- |
| **Rute** | `routes/_app/mcat-builder/packages.tsx` |
| **Komponen** | `features/mcat/components/PackageDialog.tsx`; pemasangan di `features/tool-component/components/McatItemDialog.tsx` |

## 1. Layar

**Daftar paket**: dropdown bahasa lalu tabel. Tiap baris menyebut isi Sesi 1 berurutan, setup Sesi 2, dan angka
berapa tes yang memakainya. Baris bisa dibuka untuk melihat tes mana saja.

**Dialog paket**: nama, deskripsi, bahasa, panel `Transfer` untuk kumpulan Sesi 1, dan satu dropdown untuk
Adaptive Setup.

**Dialog pemasangan**, di halaman Tool Component: satu dropdown paket, ringkasan isi paket yang dipilih, dan
sakelar acak urutan soal.

## 2. Keputusan antarmuka

**Daftar, bukan halaman detail.** Isi sebuah paket hanya nama, bahasa, beberapa kumpulan, dan satu setup. Itu
muat di dialog; halaman detail tersendiri hanya menambah satu klik tanpa menambah apa pun.

**"Dipakai di mana" jadi baris yang bisa dibuka, bukan kolom.** Sebagian besar paket dipakai sekali atau belum
sama sekali, jadi daftarnya tidak perlu selalu terlihat — yang perlu selalu terlihat cuma angkanya.

**`Transfer` dipakai di sini justru karena urutannya berarti**: itu urutan peserta mengerjakan kumpulan-kumpulan
itu. `Transfer` menyimpan urutan pemilihan, sedangkan sekumpulan checkbox tidak. Di
[`QuestionPicker`](../item-bank/frontend.md), yang urutannya tidak berarti, komponen ini justru sengaja tidak
dipakai.

**Dialog pemasangan menampilkan isi paketnya.** Memilih nama tanpa melihat isinya berarti memilih buta, dan
kesalahannya baru ketahuan saat peserta mengerjakan.

**Bahasa terkunci sesudah paket dibuat**, ditandai dengan dropdown `disabled` dan teks bantuan yang menyebut
alasannya.

## 3. Catatan implementasi

Setup yang ditawarkan disaring ke bahasa paket, dan bahasanya sendiri ditentukan lewat kumpulan Sesi 2 yang
ditarik setup itu — setup tidak punya kolom bahasa.
