# Impor soal

| | |
| --- | --- |
| **Menu** | — (alur di dalam [Item Bank](../README.md)) |
| **Rute** | Dialog **Import** di `/mcat-builder/items` dan `/mcat-builder/sets/:setId` |
| **Slug ID** | `IMP` |
| **Prasyarat** | [Item Bank](../README.md) |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

Ini bukan item menu. Ia dapat foldernya sendiri karena punya dialog sendiri, format berkasnya sendiri, dan
cukup banyak aturan untuk punya ID sendiri — lihat aturan 2 di [TEMPLATE.md](../../../../TEMPLATE.md).

## 1. Ringkasan

Soal kognitif biasanya sudah tersusun di spreadsheet sebelum masuk sistem. Alur ini memindahkan puluhan soal
sekaligus, dan melaporkan baris mana saja yang salah supaya admin bisa memperbaiki berkasnya lalu mengunggah
ulang.

Baris dicocokkan lewat kolom **`code`**, jadi unggah ulang **memperbarui**, bukan menggandakan. Itu yang
membuat siklus "unggah, lihat yang gagal, perbaiki, unggah lagi" aman — lihat [DEC-MCAT-04](../../decisions.md).

## 2. Alur pengguna

1. Admin menekan **Import**, dari halaman sebuah Question Set atau dari Item Bank.
2. Mengunduh template CSV. Template sudah berisi satu baris contoh terisi, supaya format tiap kolom terlihat
   dari berkas itu sendiri.
3. Mengisinya di spreadsheet, menyimpannya sebagai CSV, lalu mengunggahnya.
4. Sistem melaporkan berapa soal **dibuat**, berapa **diperbarui**, dan tiap baris yang gagal beserta
   alasannya.
5. Dimulai dari sebuah Question Set, soal yang berhasil langsung masuk ke kolam kumpulan itu. Dimulai dari Item
   Bank, soalnya berdiri sendiri dulu.

## 3. Kebutuhan fungsional

- **FR-IMP-01** Admin dapat mengunduh template CSV, dari Item Bank maupun dari halaman sebuah Question Set.
- **FR-IMP-02** Template berisi header dan satu baris contoh yang sudah terisi.
- **FR-IMP-03** Template Sesi 1 dan Sesi 2 berbeda kolomnya:

  | Kolom | Sesi 1 | Sesi 2 |
  | --- | --- | --- |
  | `code`, `content`, `item_format`, `answer_key`, kolom pilihan | ada | ada |
  | `dimension` | ada | — |
  | `a_<kemampuan>`, `d_param`, `c_param`, `sh_r_param` | — | ada |

- **FR-IMP-04** Admin dapat mengunggah CSV untuk sebuah Question Set, atau untuk Item Bank saja.
- **FR-IMP-05** Dimulai dari sebuah Question Set, soal hasil impor masuk ke kolam kumpulan itu dan mengambil
  kemampuannya — kolom `dimension` boleh dikosongkan. Dimulai dari Item Bank, kolom itu wajib diisi.
- **FR-IMP-06** Baris yang kolom `code`-nya sudah ada **memperbarui** soal itu; kode baru membuat soal baru.
  Baris tanpa `code` tetap boleh, dan kodenya dibuatkan.
- **FR-IMP-07** Sistem melaporkan jumlah soal yang dibuat dan jumlah yang diperbarui secara terpisah.
- **FR-IMP-08** Sistem melaporkan **setiap** baris yang gagal, dengan nomor baris dan alasan yang bisa
  dimengerti admin non-teknis.
- **FR-IMP-09** Baris yang benar tetap masuk walaupun ada baris lain yang gagal.
- **FR-IMP-10** Urutan kolom di berkas tidak harus sama dengan template; pencocokannya lewat nama kolom.

## 4. Aturan bisnis

- **BR-IMP-01** Baris dicocokkan lewat kolom `code` yang sudah dinormalkan. Ketemu berarti memperbarui, tidak
  ketemu berarti membuat baru.
- **BR-IMP-02** Aturan validasi soal hasil impor **sama persis** dengan soal yang diketik manual
  ([BR-ITM-01…BR-ITM-06](../README.md#4-aturan-bisnis)).
- **BR-IMP-03** Nomor baris yang dilaporkan seperti yang dilihat di spreadsheet: header adalah baris 1.
- **BR-IMP-04** Berkas berisi header saja tanpa baris data ditolak dengan pesan jelas, bukan dilaporkan sebagai
  impor berhasil dengan 0 soal.
- **BR-IMP-05** Gambar tidak dapat diimpor lewat CSV. Gambar ditambahkan setelahnya lewat panel soal.

## 5. Di luar cakupan

- Impor Excel (`.xlsx`). Hanya CSV. Belum dijadwalkan.
- Impor gambar massal lewat berkas ZIP. Belum dijadwalkan.
- Mengekspor soal ke CSV. Belum dijadwalkan.

## 6. Pertanyaan terbuka

Tidak ada.
