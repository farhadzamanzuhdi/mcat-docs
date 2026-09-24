# Item Bank

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → Item Bank |
| **Rute** | `/mcat-builder/items` |
| **Slug ID** | `ITM` |
| **Prasyarat** | [Dimensions](../dimensions/README.md) |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |
| **Alur di dalamnya** | [Impor soal](import/README.md) |

## 1. Ringkasan

Tempat semua soal MCAT tinggal, lepas dari kumpulan mana pun. Soal **tidak dimiliki** satu Question Set:
sebuah soal ditulis sekali, lalu dipakai di berapa pun kumpulan yang membutuhkannya. Itu yang membuat soal bisa
dicari, dipakai ulang, dan diperbaiki di satu tempat — lihat [DEC-MCAT-03](../decisions.md).

Akibat langsungnya: **Item Bank selalu berisi lebih banyak soal daripada kolam mana pun.** Soal yang belum
masuk kumpulan apa pun tetap hidup di sini.

Tiap soal punya **kode unik** seperti `NUMERIC-0042`. Kode itu yang dipakai mencari soal di antara ribuan, dan
yang dipakai mencocokkan baris saat mengimpor.

## 2. Alur pengguna

1. Admin membuka **MCAT → Item Bank** dan memilih sesi serta bahasanya.
2. Daftarnya menampilkan kode, isi soal, kemampuan yang diukur, dipakai di berapa kumpulan, dan berapa kali
   sudah keluar ke peserta.
3. Menyaring per kemampuan, atau mencari lewat kode maupun isi soal.
4. Tombol **Add item** membuka panel soal: form di kiri, tampilan peserta di kanan yang ikut berubah saat
   mengetik.
5. Kode sudah terisi. Admin boleh menimpanya dengan penomorannya sendiri.
6. Mengisi teks soal, pilihan jawaban, kunci jawaban, kemampuan yang diukur, dan gambar bila ada.
7. Menekan **Save**. Soal muncul di daftar.

## 3. Kebutuhan fungsional

### 3.1 Daftar dan pencarian

- **FR-ITM-01** Admin dapat melihat seluruh soal beserta kode, isi, kemampuan, sesi, jumlah kumpulan yang
  memakainya, dan jumlah keterpaparannya.
- **FR-ITM-02** Admin dapat menyaring daftar per sesi, bahasa, dan kemampuan.
- **FR-ITM-03** Admin dapat mencari soal. Pencarian mencocokkan kode lebih dulu, baru isi soal.
- **FR-ITM-04** Seluruh saringan yang sedang dipakai ada di alamat halaman.
- **FR-ITM-05** Hasil pencarian yang kosong menampilkan keadaan "tidak ada yang cocok" beserta saran
  melonggarkan saringan, bukan tabel tanpa baris.

### 3.2 Menulis soal

- **FR-ITM-06** Admin dapat menulis soal baru, dari Item Bank maupun dari halaman sebuah Question Set.
- **FR-ITM-07** Admin mengisi teks soal. Teks soal wajib.
- **FR-ITM-08** Admin dapat menambah 2 sampai 8 pilihan jawaban, masing-masing dengan label dan teks, dan
  masing-masing boleh bergambar.
- **FR-ITM-09** Admin menandai tepat satu pilihan sebagai kunci jawaban.
- **FR-ITM-10** Admin dapat mengunggah satu gambar untuk soal, maksimal 2 MB, format `jpg`, `png`, atau `webp`.
- **FR-ITM-11** Panel kanan menampilkan tampilan peserta yang ikut berubah saat admin mengetik, sehingga admin
  tahu apa yang akan dilihat peserta sebelum menyimpan.
- **FR-ITM-12** Soal Sesi 1 **tidak** meminta tingkat kesulitan, parameter `a`, `d`, `c`, maupun kontrol
  keterpaparan. Semua itu milik Sesi 2.
- **FR-ITM-13** Kemampuan yang diukur ditanyakan kalau tidak ada yang bisa diwarisi, dan ditampilkan saja kalau
  ada. Dibuka dari halaman sebuah kumpulan, kemampuannya milik kumpulan itu dan tidak bisa diubah; dibuka dari
  Item Bank, kemampuannya dipilih lewat dropdown yang sudah terisi sesuai saringan aktif.

### 3.3 Kode soal

- **FR-ITM-14** Tiap soal punya kode unik. Field-nya sudah terisi saat form dibuka: `NUMERIC-0042` untuk Sesi 1
  (kode kemampuan + nomor urut) dan `S2-0042` untuk Sesi 2.
- **FR-ITM-15** Admin boleh menimpa kode dengan penomorannya sendiri.
- **FR-ITM-16** Kode dinormalkan jadi huruf besar saat admin meninggalkan field-nya, bukan saat menyimpan.
- **FR-ITM-17** Kode dapat diganti kapan saja tanpa merusak jejak pengerjaan yang sudah ada.

### 3.4 Mengubah dan menghapus

- **FR-ITM-18** Admin dapat mengubah soal, baik dari Item Bank maupun dari kolam sebuah kumpulan.
- **FR-ITM-19** Admin dapat menghapus soal yang tidak sedang dipakai.
- **FR-ITM-20** Mengeluarkan soal dari sebuah kumpulan dan menghapus soal adalah dua aksi berbeda, dengan kata
  yang berbeda: *Remove from set* dan *Delete question*.

### 3.5 Tampilan satu soal

- **FR-ITM-21** Admin dapat menentukan posisi gambar soal: kiri, kanan, spanduk di atas, atau menempel di
  bawah.
- **FR-ITM-22** Admin dapat menentukan rasio gambar: otomatis, 16:9, atau persegi.
- **FR-ITM-23** Admin dapat menentukan tata letak jawaban: di bawah soal, atau di samping soal.
- **FR-ITM-24** Admin dapat mengubah ketiganya untuk satu soal saja, dari panel *Settings View* di panel soal
  itu. Menerapkannya ke seluruh kumpulan sekaligus dilakukan dari
  [halaman Question Set](../question-sets/README.md#35-tampilan-satu-kumpulan).

## 4. Aturan bisnis

- **BR-ITM-01** Kode soal unik secara global — bukan per bahasa, bukan per kemampuan — karena tujuannya jadi
  satu acuan yang dicari.
- **BR-ITM-02** Kode berpola `^[A-Z0-9][A-Z0-9-]{0,31}$`, disimpan dalam huruf besar.
- **BR-ITM-03** Semua soal MCAT pilihan ganda, minimal 2 dan maksimal 8 pilihan.
- **BR-ITM-04** Label pilihan jawaban unik dalam satu soal.
- **BR-ITM-05** Kunci jawaban wajib cocok dengan salah satu label pilihan.
- **BR-ITM-06** Soal Sesi 1 wajib punya tepat satu kemampuan. Soal Sesi 2 menyatakan beberapa kemampuan beserta
  bobotnya.
- **BR-ITM-07** Soal tidak dapat dihapus selama masih ada di kolam kumpulan mana pun. Pesannya menyebut
  kumpulan mana saja.
- **BR-ITM-08** Soal yang sudah pernah dikerjakan peserta tidak dapat dihapus.
- **BR-ITM-09** Soal yang sama dalam beberapa bahasa adalah beberapa soal dengan beberapa kode, bukan satu soal
  bertranslasi.
- **BR-ITM-10** Pengaturan tampilan tersimpan di **soal**, bukan di kumpulan. Satu soal yang ada di dua kolam
  karena itu tampil sama di keduanya.
- **BR-ITM-11** Soal tanpa gambar tidak menyisakan ruang kosong tempat gambar.
- **BR-ITM-12** Satu soal terikat satu sesi. Soal Sesi 1 tidak dapat dipakai di kumpulan Sesi 2, dan
  sebaliknya — Sesi 2 menuntut kalibrasi (`d`, `c`, `sh_r`, bobot `a`) yang tidak berarti di Sesi 1.

## 5. Di luar cakupan

- Format soal selain pilihan ganda. Belum dijadwalkan.
- Riwayat perubahan per soal. Belum dijadwalkan.
- Penanda bebas (*tag*) untuk penyaringan lebih halus. Kode, kemampuan, bahasa, dan sesi sudah menutup
  kebutuhan pencarian sejauh ini.
- Hapus massal soal. Ada di `mcat-api`, belum diport.
- Tampilan berbeda untuk layar sempit. Pengaturannya satu, dipakai apa adanya.

## 6. Pertanyaan terbuka

- **Q-ITM-01** Nomor urut kode dilanjutkan per prefiks, jadi `NUM-0043` menyusul `NUM-0042` walau soal dimensi
  lain dibuat di antaranya. Konfirmasi bahwa itu yang diinginkan, bukan penomoran global. — *menunggu:* PO
- **Q-ITM-02** Di desain, opsi **Split Left** judulnya *Split Left* tapi keterangannya *Left Question*, dan
  thumbnail-nya menaruh gambar di kiri. Yang dipakai sekarang: gambar kiri, soal kanan. — *menunggu:* PO
