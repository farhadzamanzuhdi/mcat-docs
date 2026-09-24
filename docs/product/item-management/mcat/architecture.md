# MCAT — arsitektur

Hal teknis yang melintasi banyak layar. Yang khas satu layar ada di `frontend.md` / `backend.md` layar itu.

| | |
| --- | --- |
| Backend | `apps/api`, modul MCAT di `apps/api/src/modules/mcat/native/` |
| Frontend | `apps/cms/src/features/mcat/`, rute di `apps/cms/src/routes/_app/mcat-builder/` |
| Skema | `apps/api/src/db/local/schema.ts`, migrasi di `apps/api/drizzle-local/` |

## 1. Peta rute

Tiap item menu adalah satu url, dan halaman dalamnya berada **di bawah** url itu. Penjaga akses mencocokkan
awalan path, jadi halaman yang tidak berada di bawah url menunya tidak akan terbuka.

| Rute | Halaman |
| --- | --- |
| `/mcat-builder` | Pengalihan ke Question Sets |
| `/mcat-builder/dimensions` | [Dimensions](dimensions/README.md) |
| `/mcat-builder/items?session=&language=&dimension=&q=` | [Item Bank](item-bank/README.md) |
| `/mcat-builder/sets?session=&language=` | [Question Sets](question-sets/README.md), sesi sebagai tab |
| `/mcat-builder/sets/:setId` | Satu Question Set: tampilan, pratinjau, kolam soal |
| `/mcat-builder/setups?language=` | [Adaptive Setups](adaptive-setups/README.md) |
| `/mcat-builder/setups/:settingsId` | Satu setup: kolam, algoritma, coba sebagai peserta, simulasi |
| `/mcat-builder/packages?language=` | [Packages](packages/README.md) |
| `/tool-component/:id` | Explorer biasa; item MCAT di dalamnya memilih satu Package |

Aturan navigasi:

- **Ruang lingkup ada di URL, bukan di state komponen.** Sesi, bahasa, dimensi, dan kata pencarian semuanya
  query string yang divalidasi `validateSearch`. Akibatnya layar yang tersaring bisa ditautkan ke orang lain.
- **Sesi adalah tab, bukan segmen path.** Sesi adalah kolom di Question Set; kalau ia jadi segmen path, tiap
  layar di bawahnya tergandakan dan tiap layar harus tahu sesinya dari alamat padahal datanya sudah tahu.
- **Back dihitung dari data, bukan riwayat browser.** `PageHeader` menerima `back` dan `backSearch`; kembali
  dari sebuah set membawa sesi dan bahasa **set itu**.

## 2. Endpoint

Semua di bawah `/api/mcat`, memakai bentuk payload yang sama dengan layanan Rust `mcat-api`, supaya CMS tidak
perlu tahu siapa yang menjawab.

| Method | Path | Guna |
| --- | --- | --- |
| GET POST | `/dimensions?include_inactive=` | Daftar dan buat dimensi; tanpa flag hanya yang masih dipakai |
| PATCH DELETE | `/dimensions/:dimensionId` | Ubah dan hapus dimensi; kode tidak ikut berubah |
| GET POST | `/languages` | Daftar dan buat bahasa |
| PATCH DELETE | `/languages/:languageId` | Ubah dan hapus bahasa |
| GET | `/items?session=&language_id=&dimension=&q=` | Item Bank |
| POST | `/items?question_set_id=` | Buat soal; dengan `question_set_id` ia sekalian masuk kolam itu |
| PUT DELETE | `/items/:itemId` | Ubah dan hapus soal |
| GET | `/items/template?session=` | Template CSV, berbeda per sesi |
| POST | `/items/import?question_set_id=` \| `?session=&language_id=` | Impor CSV |
| POST | `/questions/image` | Unggah gambar, kembali sebagai data URL |
| GET POST | `/question-sets` | Daftar dan buat Question Set |
| GET PATCH DELETE | `/question-sets/:setId` | Detail, ubah, hapus |
| GET PUT | `/question-sets/:setId/items` | Baca dan tulis kolam |
| PATCH | `/question-sets/:setId/display` | Terapkan tampilan ke seluruh set |
| GET POST | `/settings` | Daftar dan buat adaptive setup |
| GET PATCH | `/settings/:settingsId` | Detail dan ubah setup |
| GET POST | `/packages` | Daftar dan buat Package |
| GET PATCH DELETE | `/packages/:packageId` | Detail, ubah, hapus Package |
| POST | `/sessions`, `/sessions/:id/respond`, `/sessions/:id/terminate` | Sesi peserta dan pratinjau |
| GET | `/sessions/:id/next-item`, `/sessions/:id/result` | Soal berikutnya dan hasilnya |
| POST | `/playground/run` | Simulasi |

Satu endpoint yang bentuknya tidak biasa: **`PUT /question-sets/:setId/items` menulis seluruh kolam sekaligus.**
Menambah, mengeluarkan, dan mengurutkan adalah satu tindakan dari sudut pandang layar, jadi satu panggilan.

## 3. Migrasi

Migrasi lokal ada di `apps/api/drizzle-local/`. **Selalu tambahkan berkas inkremental baru** — jangan membuat
ulang baseline. Baseline baru memaksa tiap orang menghapus `apps/api/.data/engauge.db` miliknya beserta data
demo yang sudah dimasukkan.

Sebelum menjalankan migrasi pada database berisi data, uji dulu pada **salinannya**, lalu pastikan jumlah baris
Tool Component dan MCAT Setting tetap sama.

### 3.1 Satu jebakan yang akan terulang

Migrasi `0003` membangun ulang tiga tabel yang punya anak. Itu tidak bisa dilakukan di dalam transaksi yang
dibuka drizzle, dan ketiga jalan keluar yang biasa dipakai gagal:

| Cara | Kenapa gagal |
| --- | --- |
| `PRAGMA foreign_keys = OFF` | Diabaikan di dalam transaksi |
| `PRAGMA defer_foreign_keys` | Hanya menunda hitungan yang naik saat induk dijatuhkan, dan tidak pernah turun lagi |
| `ALTER TABLE ... RENAME` induknya | Selama foreign key menyala, RENAME justru menulis ulang klausa `REFERENCES` anak-anaknya |

Yang dipakai: migrasi menutup transaksi drizzle sebentar, mematikan foreign key, membangun ulang, lalu membuka
transaksinya lagi. Konsekuensinya ditulis di komentar berkasnya — kalau gagal di tengah, store-nya separuh
termigrasi dan harus dibuat ulang. Untuk store lokal yang memang sekali pakai itu bisa diterima; untuk database
sungguhan, jangan tiru polanya tanpa berpikir ulang.

## 4. Aturan antarmuka

Berlaku di seluruh layar MCAT:

- **Aksi baris tidak boleh memicu aksi baris.** Tombol di dalam baris tabel yang barisnya sendiri bisa diklik
  wajib menghentikan perambatan klik.
- **Satu aksi utama per halaman**, di `PageHeader`, tidak tersebar di tengah halaman.
- **Kata yang berbeda untuk akibat yang berbeda.** *Remove from set* dan *Delete question* terlihat mirip dan
  berakibat sangat berbeda; yang membedakan harus kata-katanya, bukan ikonnya.
- **Jangan menawarkan kendali yang tidak berpengaruh.** Pegangan urutan kolam disembunyikan saat soal
  dikeluarkan acak; stepper "Questions served" berhenti di jumlah kolam.
- **Empat keadaan, bukan satu.** Tiap daftar dan form menangani memuat, kosong, gagal, dan ditolak validasi —
  bukan hanya keadaan normal. Kosong memakai `EmptyState` yang menyebut langkah berikutnya; ditolak menempelkan
  pesan di field yang bersangkutan.
- **Warna bukan satu-satunya penanda**, dan rasio kontras minimal 4.5:1 termasuk untuk teks bantuan abu-abu.

## 5. Teks

Seluruh teks layar melewati `useT()`. Menambah teks berarti menambah kunci di
`apps/cms/src/shared/i18n/translations.json` **pada posisi yang tepat** — mengurutkan ulang seluruh berkas
membuat diff tidak terbaca.

## 6. Pengujian

| Jenis | Di mana |
| --- | --- |
| Logika bentuk data | `apps/cms/src/features/mcat/domain/mcat.test.ts` |
| Kontrak API | `apps/api/test/mcat-native.test.ts` |
| Mesin adaptif | `apps/api/src/mcat/engine/engine.test.ts` |
| Alur layar | `e2e/mcat.spec.ts`, `e2e/tool-component.spec.ts` |

Tes yang membuktikan sebuah kriteria penerimaan **menyebut ID-nya di nama tes**, misalnya
`test('AC-DIM-07 menolak hapus dimensi yang masih diukur satu soal', …)`. Itu yang membuat centang di
`acceptance-criteria.md` bisa diperiksa mesin — lihat [`scripts/docs-check.ts`](../../../../scripts/docs-check.ts).

Tiap perubahan navigasi wajib punya satu uji yang menekan Back dan memeriksa alamat tujuannya.

## 7. Regresi wajib

Bukan fitur baru, tapi pernah rusak:

- Tombol Back di tiap halaman MCAT membawa ke induk yang benar, beserta sesi dan bahasa yang sedang aktif.
- Migrasi pada salinan database berisi data tidak menghilangkan Tool Component maupun adaptive setup.
- Seluruh aksi di daftar Question Set tetap terjangkau pada lebar layar 400 px, tanpa gulir menyamping.
- Sesi 2 tetap berfungsi penuh: mengubah algoritma, mencoba sebagai peserta, menjalankan simulasi.

## 8. Hubungan dengan `mcat-api`

Sesi 2 sudah ada lebih dulu sebagai produk terpisah: `mcat-api` (Rust) dan `mcat-fe`. Yang dibangun di sini
memakai **kontrak API yang sama**, tapi datanya di Engauge dan algoritmanya diport ke TypeScript — dicocokkan
dengan versi Rust sampai ~1e-15.

Yang ada di sana tapi belum diport, semuanya alat psikometri dan bukan alur admin konten: `playground/step`,
`playground/batch-run`, halaman Sessions dan Debug, hapus massal soal, dan impor XLSX.
