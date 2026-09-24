# Item Bank — Frontend

| | |
| --- | --- |
| **Rute** | `apps/cms/src/routes/_app/mcat-builder/items.tsx` |
| **Komponen** | `features/mcat/components/ItemDrawer.tsx`, `QuestionPicker.tsx`, `QuestionPreview.tsx`; bentuk data di `domain/item.ts` |

## 1. Layar

### 1.1 Daftar dan panel soal

Satu tabel dengan `FilterBar` di atasnya: tab sesi, dropdown bahasa, dropdown kemampuan, dan kotak cari.
Menekan baris membuka `ItemDrawer` — panel yang sama yang dipakai halaman Question Set, hanya dengan cakupan
berbeda.

`ItemDrawer` membelah dua: form di kiri, tampilan peserta di kanan yang ikut berubah saat mengetik, dengan
panel *Settings View* di bawahnya untuk tata letak soal itu sendiri.

`QuestionPicker` adalah modal terpisah, dipakai halaman Question Set untuk mengambil soal dari bank ini.

### 1.2 Tampilan satu soal

Tata letak satu soal diatur dari *Settings View* di dalam `ItemDrawer`, memakai `QuestionPreview` yang sama
dengan yang dipakai panel **Display settings** di halaman kumpulan. Bedanya cuma cakupan:

| Di mana | Cakupannya |
| --- | --- |
| *Settings View*, di panel soal | Soal itu saja |
| `SetDisplaySettings`, panel lipat di halaman kumpulan | Seluruh kolam, lewat satu panggilan |

Nilai bawaan untuk soal baru ada di `DEFAULT_DISPLAY` (`domain/item.ts`): gambar di atas, rasio otomatis,
jawaban bertumpuk.

## 2. Keputusan antarmuka

**Saringan ada di URL.** Sesi, bahasa, kemampuan, dan kata pencarian semuanya query string yang divalidasi
`validateSearch`. Akibat yang dituju: layar tersaring bisa ditautkan apa adanya. Akibat sampingannya: tombol
Back berperilaku seperti dugaan admin.

**Kemampuan: ditanya atau ditampilkan.** `ItemDrawer` memutuskannya dari satu tanda — apakah ia dibuka dari
halaman sebuah kumpulan (`questionSetId` ada dan kumpulan itu punya kemampuan). Kalau ya, kemampuannya chip
yang tidak bisa diubah, karena kumpulan itu memang tidak akan menerima soal berkemampuan lain. Kalau tidak,
dropdown, dengan nilai awal dari saringan yang sedang aktif sehingga kasus paling umum tetap nol klik.

**Yang menentukan field mana yang muncul adalah kolom `session` soal itu**, bukan tebakan dari ada-tidaknya
kemampuan.

**Kode dinormalkan saat `onBlur`, bukan saat simpan.** Admin melihat apa yang akan tersimpan sebelum menekan
Save, bukan kaget sesudahnya.

**`QuestionPicker` memakai tabel, bukan `Transfer`.** Dua panel berarti dua gaya tabel dan tidak ada paginasi,
sedangkan bank soal produksi berisi ribuan baris. Daftarnya sudah tersaring ke sesi, bahasa, dan kemampuan
kumpulan tujuan, jadi soal yang akan ditolak server tidak pernah ditawarkan. Di
[`PackageDialog`](../packages/frontend.md), yang urutannya berarti, `Transfer` justru sengaja dipakai.

**Dua aksi hapus ditulis dengan kata berbeda.** *Remove from set* dan *Delete question* berakibat sangat
berbeda dan terlihat mirip; ikon saja tidak cukup membedakannya.

**Pratinjaunya soal nyata, bukan contoh buatan.** Mengatur tata letak tanpa melihat isi yang sebenarnya adalah
menebak — teks pendek dan teks panjang berperilaku beda, dan gambar lebar tidak sama dengan gambar tinggi.

## 3. Catatan implementasi

Sumber kebenaran kemampuan adalah `draft.dimension`, **bukan** `scope`. `scope` hanya memberi nilai awal dan
menentukan boleh-tidaknya diubah. Sebelumnya kemampuan diambil langsung dari `scope` saat menyimpan, dan
akibatnya soal Sesi 1 tidak bisa ditulis dari Item Bank sama sekali.
