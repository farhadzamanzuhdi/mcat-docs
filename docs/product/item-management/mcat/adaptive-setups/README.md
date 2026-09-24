# Adaptive Setups

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → Adaptive Setups |
| **Rute** | `/mcat-builder/setups`, `/mcat-builder/setups/:settingsId` |
| **Slug ID** | `ADP` |
| **Prasyarat** | [Question Sets](../question-sets/README.md), kumpulan Sesi 2 |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

Konfigurasi algoritma Sesi 2: dari kumpulan soal mana tesnya menarik, kemampuan apa yang diukur, bagaimana soal
berikutnya dipilih, dan kapan tesnya berhenti.

Sesi 1 mengeluarkan soal yang sudah ditentukan. Sesi 2 memilih soal berikutnya dari jawaban-jawaban sebelumnya:
peserta yang menjawab benar mendapat soal lebih sulit, yang salah mendapat yang lebih mudah. Tesnya berhenti
saat perkiraan kemampuannya sudah cukup akurat, atau saat batas panjangnya tercapai — jadi dua peserta bisa
mengerjakan jumlah soal yang berbeda.

Ini satu-satunya bagian MCAT yang bersifat psikometri. Admin konten biasanya tidak menyentuhnya.

## 2. Alur pengguna

1. Admin membuka **MCAT → Adaptive Setups** dan memilih bahasanya.
2. Tombol **New setup** membuka dialog: nama, kumpulan soal Sesi 2 yang dipakai, dan kemampuan yang diukur.
3. Kemampuan yang ditawarkan hanya yang benar-benar diukur soal di kumpulan yang dipilih.
4. Setup yang tersimpan langsung terbuka di halamannya, dengan empat tab.
5. Tab **Algorithm** mengatur cara memilih soal, cara mengestimasi, aturan berhenti, panjang tes, dan prior.
6. Tab **Try as participant** untuk mengerjakannya sendiri; tab **Simulation** untuk mengujinya terhadap
   kemampuan yang sudah diketahui.

## 3. Kebutuhan fungsional

### 3.1 Konfigurasi

- **FR-ADP-01** Admin dapat membuat setup dengan mengisi nama, kumpulan soal, dan kemampuan yang diukur.
- **FR-ADP-02** Admin dapat memilih lebih dari satu kumpulan soal Sesi 2 sebagai kolam.
- **FR-ADP-03** Kemampuan yang ditawarkan hanya yang benar-benar diukur soal di kolam yang dipilih.
- **FR-ADP-04** Admin dapat mengatur metode pemilihan soal, metode estimasi, aturan berhenti, kontrol
  keterpaparan, dan tindakan saat kolam habis.
- **FR-ADP-05** Admin dapat mengatur panjang tes minimal dan maksimal, ambang ketelitian, dan ambang
  konvergensi.
- **FR-ADP-06** Admin dapat mengatur prior — rata-rata dan ragam tebakan awal — per kemampuan yang diukur.
- **FR-ADP-07** Halaman setup memperingatkan kalau jumlah soal yang memenuhi syarat lebih sedikit daripada
  panjang tes maksimalnya.

### 3.2 Mencoba dan mensimulasikan

- **FR-ADP-08** Admin dapat mengerjakan tes adaptif sungguhan lewat **Try as participant**, dengan perkiraan
  kemampuan yang bergerak tiap jawaban.
- **FR-ADP-09** Admin dapat menjalankan simulasi: menentukan kemampuan "sebenarnya" peserta khayalan,
  menjalankan tesnya, lalu membandingkan tebakan algoritmanya.
- **FR-ADP-10** Simulasi dengan benih acak yang sama menghasilkan jalur yang sama persis.

## 4. Aturan bisnis

- **BR-ADP-01** Sebuah setup wajib menyebut kumpulan soalnya, minimal satu.
- **BR-ADP-02** Kumpulan yang ditunjuk harus kumpulan **Sesi 2**.
- **BR-ADP-03** Kemampuan yang diukur harus benar-benar diukur oleh soal di kolamnya; yang tidak ada soalnya
  ditolak.
- **BR-ADP-04** Kemampuan berurutan, dan urutannya dipakai seluruh angka lain yang menyertainya — prior, ragam,
  dan tiap vektor perkiraan kemampuan.
- **BR-ADP-05** Jumlah angka prior harus sama dengan jumlah kemampuan yang diukur.
- **BR-ADP-06** Panjang minimal tidak boleh lebih besar dari panjang maksimal.
- **BR-ADP-07** Kolam **tidak dapat diubah** setelah setup itu punya sesi peserta: perkiraan yang sudah
  dihitung akan bersandar pada kumpulan soal yang tidak lagi sama.
- **BR-ADP-08** Soal yang mengukur kemampuan di luar yang diukur tes ini tidak akan pernah keluar.
- **BR-ADP-09** Simulasi tidak menulis apa pun, termasuk tidak menambah hitungan keterpaparan soal.
- **BR-ADP-10** Soal yang sudah keluar dalam sebuah sesi tetap terbaca di riwayatnya walaupun sesudahnya
  dikeluarkan dari kolam.

## 5. Di luar cakupan

- Alat psikometri yang ada di layanan lama tapi belum dipindahkan: playground langkah-per-langkah, daftar sesi
  peserta, dan halaman debug pemilihan soal.
- Kalibrasi parameter soal dari data jawaban. Parameter diisi manual atau lewat impor.

## 6. Pertanyaan terbuka

- **Q-ADP-01** Admin non-psikometrikus sekarang bisa mengubah **seluruh** parameter algoritma. Usulan: kunci
  metode dan prior untuk psikometrikus, admin hanya memilih preset dan panjang tes. — *menunggu:* PO
- **Q-ADP-02** Pratinjau admin ikut menaikkan hitungan keterpaparan soal. Usulan: tandai sesi pratinjau supaya
  tidak dihitung. — *menunggu:* PO
