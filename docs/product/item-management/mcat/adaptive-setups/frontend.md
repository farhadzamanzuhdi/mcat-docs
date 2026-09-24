# Adaptive Setups — Frontend

| | |
| --- | --- |
| **Rute** | `routes/_app/mcat-builder/setups/index.tsx`, `setups/$settingsId.tsx` |
| **Komponen** | `features/mcat/components/AlgorithmForm.tsx`, `PreviewRunner.tsx`, `SimulationPanel.tsx`, `SetupDialog` di `dialogs.tsx` |

## 1. Layar

**Daftar** (`setups/index.tsx`): dropdown bahasa lalu daftar setup, tiap baris menyebut kumpulan yang
ditariknya, kemampuan yang diukur, metode yang dipakai, dan rentang panjang tesnya.

**Halaman setup** (`setups/$settingsId.tsx`): empat tab, dan tab yang aktif ada di alamat halaman.

| Tab | Isinya |
| --- | --- |
| Item bank | Kumpulan yang ditarik, jumlah soal di kolam, dan berapa yang memenuhi syarat |
| Algorithm | Seluruh parameter, dikelompokkan per tahap: memilih, mengestimasi, berhenti |
| Try as participant | Mengerjakan tes adaptif sungguhan |
| Simulation | Menjalankan terhadap kemampuan yang sudah diketahui, lalu membandingkan hasilnya |

## 2. Keputusan antarmuka

**Peringatan kolam kurang muncul di atas seluruh tab**, bukan hanya di tab Item bank. Setup yang kolamnya lebih
sedikit daripada panjang tes maksimalnya akan berhenti lebih awal dari yang dikira admin, dan itu perlu
terlihat saat ia sedang menyetel angka panjang tes.

**Kemampuan yang ditawarkan dihitung dari kolam yang dipilih**, bukan dari daftar kemampuan seluruh sistem.
Menawarkan kemampuan yang tidak ada soalnya berarti menawarkan setup yang pasti ditolak server.

**Parameter dikelompokkan per tahap algoritma**, bukan per tipe field. Psikometrikus berpikir dalam urutan
"pilih soal, hitung perkiraan, putuskan berhenti", dan formnya mengikuti urutan itu.

**Perubahan yang belum disimpan ditandai eksplisit.** Halaman ini penuh angka yang saling terkait; pindah tab
sambil mengira sudah tersimpan adalah kesalahan yang mahal.

**Pemilih dimensi memakai kotak pilih berlabel**, sama seperti di laci soal Sesi 2 — lihat
[Dimensions frontend](../dimensions/frontend.md) dan [DEC-MCAT-08](../decisions.md).

## 3. Catatan implementasi

Halaman setup membaca kolamnya lewat `useQueries` atas tiap kumpulan yang disebut, lalu menyatukannya —
gabungannya dihitung di klien karena dipakai tiga tempat di halaman yang sama (peringatan, kartu ringkasan, dan
daftar kemampuan di form).

`AlgorithmForm` menerima daftar kemampuan kolam sebagai prop, bukan mengambilnya sendiri, supaya angka yang
dipakai form sama persis dengan yang dipakai peringatan di atasnya.
