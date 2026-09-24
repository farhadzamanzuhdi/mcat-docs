# Impor soal — Frontend

| | |
| --- | --- |
| **Rute** | Dipanggil dari `routes/_app/mcat-builder/items.tsx` dan `sets/$setId.tsx` |
| **Komponen** | `ImportDialog` di `features/mcat/components/dialogs.tsx` |

## 1. Layar

Satu dialog berisi tiga bagian, urut sesuai urutan pemakaiannya:

1. Tautan unduh template, dengan keterangan singkat isinya.
2. Area seret-dan-lepas berkas.
3. Ringkasan hasil, muncul setelah unggahan selesai: berapa dibuat, berapa diperbarui, lalu daftar baris yang
   gagal beserta nomor baris dan alasannya.

Dialog yang sama dipakai dari dua tempat; yang membedakan hanya cakupan yang dikirimkannya — sebuah kumpulan,
atau sepasang sesi dan bahasa.

## 2. Keputusan antarmuka

**Unduh template ada di dialog yang sama dengan unggahnya.** Admin yang mengimpor pertama kali tidak perlu
mencari templatenya di tempat lain, dan yang sudah punya tinggal melewatinya.

**Hasilnya tinggal di dialog, tidak jadi notifikasi sekilas.** Daftar baris yang gagal perlu dibaca sambil
memperbaiki berkasnya, jadi ia harus tetap ada sampai admin menutupnya sendiri.

**"Dibuat" dan "diperbarui" ditampilkan terpisah.** Angka gabungan menyembunyikan hal yang paling ingin
diketahui admin setelah unggah ulang: apakah berkasnya menambah soal baru, atau memperbaiki yang sudah ada.

**Nomor baris ditampilkan seperti di spreadsheet.** Admin memperbaiki berkasnya di Excel, bukan di indeks
array.

## 3. Catatan implementasi

Berkas dikirim sebagai `FormData`, bukan JSON, sehingga tidak perlu diubah jadi base64 lebih dulu. Cakupannya
ikut di query string, bukan di badan permintaan, supaya jalur unggahnya tetap satu bentuk.
