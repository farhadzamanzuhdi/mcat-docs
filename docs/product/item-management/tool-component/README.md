# Tool Component

| | |
| --- | --- |
| **Menu** | Item Management → Tool Component |
| **Rute** | `/tool-component`, `/tool-component/folder/:folderId`, `/tool-component/:toolComponentId` |
| **Slug ID** | `TLC` |
| **Prasyarat** | — |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

Tool Component adalah **satu instrumen tes** yang dikerjakan peserta: kuesioner kepribadian, tes bahasa dengan
rekaman suara, atau tes kemampuan kognitif. Ia wadah paling luar yang dikenal peserta, dan isinya disusun
bertingkat:

```
Folder  ──►  Tool Component  ──►  Block  ──►  Page  ──►  Item
```

Satu item punya tipe, dan tipe itu yang menentukan apa yang dilihat dan dilakukan peserta — teks saja, pilihan
ganda, penggeser, rekaman video, dan seterusnya. Salah satu tipe itu adalah **MCAT**, dan isinya satu
[Package](../mcat/packages/README.md).

Di sinilah dua anak Item Management bertemu: MCAT menghasilkan paket, Tool Component memakainya.

## 2. Alur pengguna

1. Admin membuka **Item Management → Tool Component**. Yang tampil daftar **folder**.
2. Membuka sebuah folder menampilkan Tool Component di dalamnya.
3. Membuka sebuah Tool Component menampilkan explorer: pohon strukturnya di kiri, inspector di kanan.
4. Memilih sebuah node di pohon — blok, halaman, atau item — menampilkan pengaturannya di inspector.
5. Untuk item bertipe MCAT, inspector menampilkan dropdown paket beserta ringkasan isi paket yang dipilih.
6. Tab pratinjau menampilkan halaman itu seperti yang akan dilihat peserta.

## 3. Kebutuhan fungsional

### 3.1 Folder

- **FR-TLC-01** Admin dapat melihat daftar folder, mencarinya, dan berpindah halaman.
- **FR-TLC-02** Admin dapat membuat, mengubah nama, dan menghapus folder.
- **FR-TLC-03** Admin dapat memindahkan Tool Component dari satu folder ke folder lain.

### 3.2 Tool Component

- **FR-TLC-04** Admin dapat melihat Tool Component satu folder beserta tipe, bahasa, tag, dan apakah ia
  menghasilkan laporan individual.
- **FR-TLC-05** Admin dapat membuat, mengubah, menghapus, dan menduplikasi Tool Component.
- **FR-TLC-06** Admin dapat mencari Tool Component lintas folder.

### 3.3 Struktur

- **FR-TLC-07** Explorer menampilkan seluruh struktur sebagai pohon: blok, halaman di dalamnya, dan item di
  dalam halaman — masing-masing sesuai urutan pengerjaan peserta.
- **FR-TLC-08** Tiap node di pohon menampilkan ikon tipenya dan satu fakta pendek, misalnya "4 pilihan".
- **FR-TLC-09** Header dan footer ditampilkan terpisah dari blok pertanyaan, walaupun keduanya tersimpan
  sebagai blok biasa.
- **FR-TLC-10** Inspector menampilkan pengaturan node yang sedang dipilih, dan pratinjau tampilan pesertanya.
- **FR-TLC-11** Legenda tipe menyebut tipe item apa saja yang ada di Tool Component itu beserta jumlahnya.

### 3.4 Item MCAT

- **FR-TLC-12** Item bertipe MCAT memilih **satu** [Package](../mcat/packages/README.md) dari dropdown.
- **FR-TLC-13** Dialognya menampilkan isi paket yang dipilih: kumpulan Sesi 1 berurutan dan setup Sesi 2.
- **FR-TLC-14** Sakelar **acak urutan soal Sesi 1** disimpan di item itu, bukan di paketnya.
- **FR-TLC-15** Item MCAT yang belum memilih paket ditandai di inspector dan di pohon struktur.

## 4. Aturan bisnis

- **BR-TLC-01** Nama folder unik. Folder tidak dapat dihapus selama masih berisi Tool Component.
- **BR-TLC-02** Header dan footer dikenali dari `block_type_id`, dan — untuk data lama yang tidak punya kolom
  itu — dari `block_sort_order` `1` dan `99`.
- **BR-TLC-03** Urutan blok, halaman, dan item adalah urutan pengerjaan peserta, dan diambil dari kolom
  `sort_order` masing-masing, bukan dari urutan baris yang dikembalikan database.
- **BR-TLC-04** Menambah tipe item baru berarti menambah satu entri di registry tipe. Pohon, inspector,
  legenda, dan pratinjau peserta mengikutinya tanpa perubahan.
- **BR-TLC-05** Item MCAT wajib menunjuk sebuah paket sebelum tesnya bisa dijalankan.
- **BR-TLC-06** Paket yang masih dipakai item MCAT tidak dapat dihapus — lihat
  [BR-PKG-05](../mcat/packages/README.md#4-aturan-bisnis).
- **BR-TLC-07** Hanya sebagian tipe item yang mendukung penilaian. Sifat itu melekat pada tipenya, bukan pada
  itemnya.

## 5. Di luar cakupan

- **Penyunting struktur penuh.** Blok, halaman, dan item dibaca dan pengaturannya disunting; menyusun ulang
  strukturnya dari layar ini belum dibangun.
- **Terjemahan konten.** Sistem lama punya satu tabel terjemahan per tingkat; yang dipakai di sini baru bahasa
  bawaan Tool Component.
- **Tool Set dan Batch.** Wadah di atas Tool Component, milik modul delivery, belum dibangun di sini.
- **Point setting.** Penilaian per pilihan jawaban ada di model data lama, belum dibangun di sini.

## 6. Pertanyaan terbuka

- **Q-TLC-01** Saat Tool Component diduplikasi, item MCAT-nya ikut menunjuk paket yang sama, atau dikosongkan?
  Usulan: ikut disalin. Sama dengan [Q-PKG-02](../mcat/packages/README.md#6-pertanyaan-terbuka). —
  *menunggu:* PO
- **Q-TLC-02** Komponen MCAT tidak bisa dipratinjau seperti komponen lain, karena urutan soalnya baru
  ditentukan saat peserta menjawab. Ringkasan "apa yang akan dijalankan" sudah cukup, atau perlu contoh soal?
  Usulan: ringkasan plus tombol *Coba sebagai peserta* yang membuka simulasi di MCAT Builder. — *menunggu:* PO
