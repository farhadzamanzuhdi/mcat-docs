# Question Sets — Frontend

| | |
| --- | --- |
| **Rute** | `routes/_app/mcat-builder/sets/index.tsx`, `sets/$setId.tsx` |
| **Komponen** | `features/mcat/components/dialogs.tsx` (`QuestionSetDialog`), `QuestionPicker.tsx`, `SetDisplaySettings.tsx`, `LanguageDialog.tsx` |

## 1. Layar

**Daftar** (`sets/index.tsx`): tab sesi, dropdown bahasa, tombol Language Setting, kotak cari, lalu tabel.
Menekan baris membuka halaman kumpulan; tombol aksi di baris tidak ikut membukanya.

**Halaman kumpulan** (`sets/$setId.tsx`): tiga kartu ringkasan di atas (jumlah soal, berapa yang keluar,
keterpaparan), panel tampilan yang bisa dilipat, lalu tabel kolam.

Membuat dan mengubah kumpulan lewat `QuestionSetDialog` — isinya hanya nama, deskripsi, kemampuan, durasi, dan
satu angka, jadi muat di dialog dan tidak perlu halaman tersendiri.

**`LanguageDialog`** berisi daftar bahasa dengan aksi tambah, ubah, dan hapus per baris. Tidak ada halaman
tersendiri: bahasa bukan sesuatu yang dikelola tiap hari, dan memberinya halaman sendiri berarti satu item menu
yang jarang diklik. Pemilih bahasa yang dipakai sehari-hari adalah dropdown di header, terpisah dari dialog ini.

**`SetDisplaySettings`** adalah panel lipat di halaman kumpulan: kontrol di kiri, satu soal sungguhan dari
kolam dirender di kanan lewat `QuestionPreview`.

## 2. Keputusan antarmuka

**Sesi adalah tab, di dalam URL.** `?session=1` atau `?session=2`. Bukan segmen path: sesi adalah kolom di
Question Set, dan meroutekannya akan menggandakan tiap layar di bawahnya dan memaksa tiap layar mengambil
sesinya dari alamat padahal datanya sudah tahu.

**Back dihitung dari data.** Kembali dari halaman sebuah kumpulan membawa sesi dan bahasa **kumpulan itu**,
diambil dari datanya, bukan dari riwayat browser.

**Field "Questions served" tidak perlu error.** Tiga hal membuatnya begitu: `max` diisi jumlah kolam sehingga
steppernya berhenti di sana; kolam kosong membuatnya `disabled` dengan teks bantuan yang menyuruh mengisi
soalnya dulu; dan teks bantuannya menyebut isi kolam saat ini **beserta satuannya**, jadi batasnya terlihat
sebelum ditabrak dan tidak tertukar dengan jumlah soal di Item Bank. Server tetap memeriksanya, karena kolam
bisa menyusut lewat jalan lain.

**Pegangan urutan kolam hanya muncul saat angkanya kosong.** Begitu terisi, urutannya tidak berpengaruh, dan
menawarkan penggeser yang tidak mengubah apa pun lebih buruk daripada tidak menawarkannya. Panel kolam
menampilkan keterangan singkat yang menjelaskan kenapa.

**Dropdown kemampuan terkunci saat kolamnya berisi**, dengan teks bantuan yang menyebut alasannya dan apa yang
harus dilakukan dulu. Server menolaknya juga — layarnya hanya mendahului supaya admin tidak mengetik lalu
ditolak.

**Bahasa tersimpan saat ditulis, bukan saat dialog ditutup.** Dialognya hanya punya tombol **Close**, bukan
Cancel dan Save. Menampung beberapa perubahan sampai Save berarti perlu penampung sementara dan aturan apa yang
terjadi kalau dialognya ditutup di tengah — untuk daftar sependek ini, ongkos itu tidak sebanding.

**Dropdown bahasa tidak punya pilihan "semua".** Bahasa adalah ruang lingkup, bukan penyaring; daftar yang
mencampur dua bahasa akan menyesatkan karena tidak ada satu pun yang bisa dilakukan terhadap campuran itu.

**Aksi tampilan massal diletakkan di tempat pratinjaunya ada.** Menyembunyikannya di balik checkbox pada panel
satu soal berarti admin menerapkan ke seluruh kumpulan sambil melihat satu soal yang kebetulan sedang dibuka.

**Panel tampilan dilipat secara bawaan.** Tampilan bukan hal yang diubah tiap kali halaman dibuka; yang dituju
admin biasanya kolam soalnya.

## 3. Catatan implementasi

Kartu "Serves" menampilkan jumlah kolam ketika angkanya kosong, bukan kartu kosong — nilainya memang seluruh
kolam, dan menampilkannya sebagai angka lebih terbaca daripada tanda hubung.

Panel tampilan mengambil soal pertama kolam sebagai contoh render. Kolam kosong berarti tidak ada yang bisa
dipratinjau; panelnya tetap bisa dibuka tapi tidak menampilkan apa-apa — itu keadaan wajar, bukan error.

Kunci cache bahasa dipakai bersama kunci soal dan kumpulan, karena menghapus atau menambah bahasa mengubah apa
yang boleh muncul di keduanya.
