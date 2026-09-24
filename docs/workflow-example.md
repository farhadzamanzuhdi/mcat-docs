# Contoh lengkap: dari Business Request sampai QA

Satu permintaan nyata, ditelusuri dari awal sampai selesai. Diagram alurnya ada di
[workflow.md](workflow.md); berkas ini mengisi tiap kotaknya dengan isi yang sebenarnya.

Contohnya **bukan karangan**: permintaannya berangkat dari dua pertanyaan terbuka yang memang sudah tercatat di
repo ini — [`Q-MCAT-03`](product/item-management/mcat/README.md) dan
[`Q-ADP-01`](product/item-management/mcat/adaptive-setups/README.md) — dan analisisnya membaca kode yang
sungguhan ada.

> ID di berkas ini **contoh**, bukan ID yang berlaku. `docs-check` sengaja melewatkan berkas ini supaya nomor
> contoh tidak pernah bertabrakan dengan nomor sungguhan.

---

## Tahapannya sekilas

| # | Tahap | Siapa | Keluarannya | Tinggal di mana |
| --- | --- | --- | --- | --- |
| 0 | Business Request | PO / klien | Satu masalah, ditulis sebagai masalah | Issue tracker |
| 1 | Triage | BA | Diterima, ditolak, atau ditunda | Issue tracker |
| 2 | Requirement Analysis | BA | Temuan + keputusan cabang + pertanyaan terbuka | Issue tracker, lalu docs |
| 3 | Keputusan cabang | BA + PO | Salah satu dari tiga hasil | — |
| 4 | Feature / Epic | BA | `FR-` dan `BR-` di README layar | `docs/product/**/README.md` |
| 5 | User Story | BA + tim | Story yang bisa dikerjakan satu-satu | Issue tracker |
| 6 | Acceptance Criteria | BA + QA | `AC-` bergaya diberi/saat/maka | `<layar>/acceptance-criteria.md` |
| 7 | Technical Design | SA | Endpoint, tabel, keputusan teknis | `backend.md`, `frontend.md`, `data-model.md` |
| 8 | Implementasi | Dev | Kode + tes bernama ID AC-nya | `apps/**` |
| 9 | QA | QA | Centang di `acceptance-criteria.md` | `docs-check` yang memverifikasi |

Tahap 0–3 dikerjakan **sebelum** ada baris kode. Tahap 7 hanya dikerjakan kalau memang perlu — §7 menjelaskan
kapan. Permintaan yang datang sebagai spesifikasi melewati Tahap 0 dan 1 dan masuk langsung di Tahap 2 —
caranya ada di [§Kalau permintaannya datang sebagai spesifikasi](#kalau-permintaannya-datang-sebagai-spesifikasi).

---

## Tahap 0 — Business Request

Yang menulis ini PO, bukan BA. Bentuknya bebas, tapi empat hal wajib ada: **masalahnya**, **siapa yang
terdampak**, **apa yang terjadi kalau dibiarkan**, dan **kapan dibutuhkan**. Solusi belum dibicarakan di sini.

### Contoh — `BR-2026-014`

```markdown
# BR-2026-014 — Parameter algoritma tes adaptif bisa diubah siapa saja

| | |
| --- | --- |
| Tanggal | 2026-09-23 |
| Pengaju | Rani, Product Owner |
| Prioritas usulan | Tinggi |

## Masalahnya

Minggu lalu seorang admin konten di klien A membuka Adaptive Setups, mengubah "stopping rule" dari
`se_threshold` jadi `fixed_length`, lalu menyimpannya. Dia mengira sedang mengatur panjang tes. Setup itu
sedang dipakai batch asesmen yang berjalan.

Akibatnya 40 peserta mengerjakan tes dengan aturan berhenti yang berbeda dari 200 peserta sebelumnya di
batch yang sama. Psikometrikus baru menyadarinya saat menyiapkan laporan.

## Siapa yang terdampak

- Psikometrikus — hasil satu batch jadi tidak setara dan tidak bisa dibandingkan.
- Admin konten — tidak tahu kalau yang dia ubah berbahaya; layarnya tidak memberi tanda apa pun.
- Klien — 40 peserta berpotensi harus mengulang.

## Kalau dibiarkan

Ini akan terulang. Dari lima klien yang sudah live, empat memberi akses MCAT ke admin kontennya.

## Kapan dibutuhkan

Sebelum onboarding klien F, 15 Oktober 2026.

## Yang sudah saya tahu

Saya tidak tahu apakah ini soal hak akses, soal tampilan layar, atau dua-duanya. Saya juga tidak tahu
apakah setup yang sedang dipakai batch memang boleh diubah sama sekali.
```

Perhatikan kalimat terakhirnya. **PO tidak berpura-pura tahu solusinya** — itu justru yang membuat requestnya
bagus. Request yang sudah menyebut solusi ("tolong tambahkan permission X") menyembunyikan masalah aslinya dan
membuat analisis melewatkan pilihan yang lebih murah.

---

## Tahap 1 — Triage

BA memutuskan satu hal saja: request ini **cukup jelas untuk dianalisis?** Bukan "setuju atau tidak".

| Pertanyaan | `BR-2026-014` |
| --- | --- |
| Masalahnya terbaca sebagai masalah, bukan sebagai solusi? | Ya |
| Ada yang terdampak dan bisa disebut namanya? | Ya — psikometrikus, admin konten, klien |
| Ada kejadian nyata, bukan kekhawatiran? | Ya — insiden klien A |
| Kebutuhan waktunya jelas? | Ya — 15 Oktober 2026 |

Lolos. Masuk analisis.

Request yang **tidak** lolos dikembalikan dengan pertanyaan spesifik, bukan ditolak. Contoh yang tidak lolos:
"tolong MCAT-nya dibikin lebih aman" — tidak ada masalah yang bisa dipegang.

---

## Tahap 2 — Requirement Analysis

Ini tahap yang paling sering dilewati, dan yang paling mahal kalau dilewati. Isinya lima langkah.

### 2.1 Nyatakan ulang masalahnya dalam satu kalimat

> Seseorang yang tidak berwenang secara psikometri dapat mengubah parameter Adaptive Setup yang sedang dipakai
> asesmen berjalan, dan tidak ada apa pun yang menghentikannya atau memberitahunya.

Kalau kalimat ini tidak bisa ditulis, analisisnya belum selesai.

### 2.2 Cari tahu apa yang **benar-benar** terjadi sekarang

Baca kodenya. Jangan percaya dokumen, termasuk dokumen sendiri.

```sh
grep -rn "VIEWER_MENUS" apps/api/src/db/local/seed.ts
grep -rn "isPathAllowed" apps/cms/src
grep -n "MODULES" -A 12 apps/api/src/app.ts
```

Hasilnya:

| Lapisan | Keadaannya | Buktinya |
| --- | --- | --- |
| Sidebar | Sudah per-peran; menu diambil dari `menuQuery(activeRoleId)` | `features/auth/session.ts` |
| Guard halaman | Sudah ada; URL di luar menu peran aktif menampilkan 403 | `features/shell/AppLayout.tsx:154` |
| Peran contoh `Content Admin` | **Sudah tidak** punya akses ke `/mcat-builder/setups` | `db/local/seed.ts:39` |
| API | **Tidak ada pemeriksaan peran sama sekali** | `app.ts:53` — `mcatNativeRoutes` dipasang tanpa middleware otorisasi |
| Per-field | **Tidak ada** — yang bisa memanggil `PATCH /api/mcat/settings/:id` bisa mengubah semua parameter | `modules/mcat/native/settings.ts` |
| Setup sedang dipakai | Kolam soalnya terkunci setelah ada sesi, **tapi parameter algoritmanya tidak** | `BR-ADP-07` |

### 2.3 Bandingkan dengan yang tertulis

| Dokumen | Katanya | Kenyataannya |
| --- | --- | --- |
| `Q-MCAT-03` | "Sekarang semua pengguna CMS yang login bisa membukanya" | **Basi.** Akses menu sudah berbeda per peran sejak guard-nya ada |
| `Q-ADP-01` | "Admin non-psikometrikus bisa mengubah seluruh parameter algoritma" | **Masih benar**, untuk siapa pun yang punya akses menunya |
| `BR-ADP-07` | Kolam terkunci setelah ada sesi | Benar — dan justru menunjukkan preseden: sebagian setup **memang** sudah dikunci saat berjalan |

Temuan terpenting ada di baris terakhir. Sistem **sudah punya konsep** "setup yang sedang berjalan tidak boleh
diubah", cuma baru diterapkan ke kolam soal. Artinya permintaan ini mungkin bukan fitur baru, melainkan
perluasan aturan yang sudah ada — dan itu jauh lebih murah.

### 2.4 Pisahkan jadi masalah-masalah yang berbeda

Satu request hampir tidak pernah satu masalah. `BR-2026-014` sebenarnya tiga:

| # | Masalah | Sifatnya |
| --- | --- | --- |
| M1 | API tidak memeriksa peran sama sekali; guard-nya cuma di browser | **Celah keamanan.** Ada atau tidak ada permintaan ini, ini harus ditutup |
| M2 | Parameter setup yang sedang dipakai asesmen berjalan masih bisa diubah | **Perluasan aturan yang sudah ada** (`BR-ADP-07`) |
| M3 | Admin konten tidak tahu apa yang berbahaya; layarnya tidak memberi tanda | **Keputusan produk.** Butuh jawaban PO |

M1 dan M2 bisa dikerjakan tanpa menunggu siapa pun. M3 tidak.

### 2.5 Tandai yang tidak diketahui

Jangan ditebak. Ditulis.

```markdown
OPEN QUESTION: Parameter mana yang boleh diubah admin konten, dan mana yang milik psikometrikus?
  Usulan: panjang tes (n_min, n_max) boleh; metode pemilihan, estimasi, aturan berhenti, dan prior tidak.
  Menunggu: PO + psikometrikus.

OPEN QUESTION: Setup yang sedang dipakai asesmen berjalan — parameternya dikunci total, atau boleh diubah
  dengan konfirmasi yang menyebut berapa peserta terdampak?
  Menunggu: PO.
```

---

## Tahap 3 — Keputusan cabang

Diagram di [workflow.md](workflow.md) menggambar dua cabang. Kenyataannya ada **tiga** hasil.

| Hasil | Artinya | `BR-2026-014` |
| --- | --- | --- |
| **SUDAH ADA** | Kebutuhannya tercatat; kalau sistem tidak berperilaku begitu, ini bug | M2 — perluasan `BR-ADP-07` |
| **BELUM ADA** | Kebutuhan produk baru; PRD diperbarui dulu | M1 dan M3 — model otorisasi belum pernah ditulis di mana pun |
| **SUDAH DIPUTUSKAN TIDAK** | Ada di §5 Di luar cakupan; membukanya lagi keputusan PO | — |

Jadi `BR-2026-014` memakai **dua cabang sekaligus**, dan itu normal. Yang tidak normal adalah memaksa satu
request masuk satu cabang.

---

## Tahap 4 — Feature / Epic

Satu Epic, karena ketiganya menyentuh layar yang sama dan dirilis bersama.

> **EPIC — Kewenangan atas Adaptive Setup**
> Hanya peran yang berwenang yang dapat mengubah parameter psikometri, pemeriksaannya ditegakkan di server,
> dan setup yang sedang dipakai asesmen berjalan dilindungi.

Kebutuhan yang lahir darinya, ditulis ke README layar yang bersangkutan:

```markdown
- **FR-ADP-11** Parameter algoritma hanya dapat diubah oleh peran yang berwenang mengubahnya.
- **FR-ADP-12** Layar setup menandai parameter yang tidak dapat diubah peran yang sedang login, beserta
  alasannya.

- **BR-ADP-11** Setiap endpoint MCAT menolak permintaan dari peran yang menunya tidak memuat layar itu.
  Guard di browser mendahului, tidak menggantikan.
- **BR-ADP-12** Parameter algoritma sebuah setup tidak dapat diubah selama setup itu punya sesi peserta yang
  belum selesai — aturan yang sama dengan kolam soalnya (BR-ADP-07).
```

Perhatikan `BR-ADP-11`. Itu M1, si celah keamanan — dan dia masuk sebagai aturan bisnis, bukan sebagai catatan
teknis, karena dampaknya dampak bisnis.

---

## Tahap 5 — User Story

Story = satu perubahan yang bisa dikerjakan dan diverifikasi sendiri. Kalau butuh kata "dan" dua kali, itu dua
story.

| # | Story | Dari |
| --- | --- | --- |
| S1 | Sebagai sistem, saya menolak permintaan MCAT dari peran yang tidak punya menunya, supaya guard di browser tidak bisa dilewati | `BR-ADP-11` |
| S2 | Sebagai psikometrikus, saya ingin parameter setup terkunci selama masih ada peserta mengerjakannya, supaya satu batch tetap setara | `BR-ADP-12` |
| S3 | Sebagai admin konten, saya ingin melihat mana yang tidak boleh saya ubah beserta alasannya, supaya saya tidak mencoba lalu ditolak | `FR-ADP-12` |
| S4 | Sebagai psikometrikus, saya ingin hanya saya yang bisa mengubah metode dan prior | `FR-ADP-11` — **diblokir** oleh OPEN QUESTION di §2.5 |

S1–S3 jalan sekarang. **S4 tidak boleh dimulai** sampai PO menjawab parameter mana milik siapa. Menebaknya
berarti membangun aturan bisnis yang tidak pernah diputuskan siapa pun.

---

## Tahap 6 — Acceptance Criteria

Satu perilaku, bisa dilihat di layar atau di respons API, bergaya **diberi / saat / maka**. Tiap butir menyebut
`FR-` atau `BR-` induknya.

```markdown
- [ ] **AC-ADP-16** (BR-ADP-11) Diberi pengguna berperan Content Admin, saat ia memanggil
      `PATCH /api/mcat/settings/:id` langsung, maka API menjawab 403 dan setup itu tidak berubah.
- [ ] **AC-ADP-17** (BR-ADP-12) Diberi setup yang punya satu sesi peserta belum selesai, saat admin mengubah
      `stopping_rule`-nya, maka sistem menolak dan menyebut berapa sesi yang masih berjalan.
- [ ] **AC-ADP-18** (BR-ADP-12) Diberi setup yang seluruh sesinya sudah selesai, saat admin mengubah
      `stopping_rule`-nya, maka perubahan itu tersimpan.
- [ ] **AC-ADP-19** (FR-ADP-12) Diberi pengguna yang tidak berwenang mengubah parameter, saat ia membuka tab
      Algorithm, maka field yang terkunci tampil nonaktif dengan keterangan alasannya — bukan aktif lalu
      ditolak saat disimpan.
```

`AC-ADP-18` itu **pasangan negatif** dari `AC-ADP-17`. Kriteria yang cuma menguji penolakan gampang dipuaskan
dengan menolak semuanya. Tiap larangan butuh satu butir yang membuktikan jalur normalnya masih hidup.

Di sisi tes, ID-nya masuk ke nama tes:

```ts
test('AC-ADP-17 menolak ubah parameter setup yang sesinya belum selesai', async () => { … })
```

Itu yang membuat centang `- [x]` bisa diperiksa mesin, lewat `bun scripts/docs-check.ts`.

---

## Tahap 7 — Technical Design

**Hanya kalau perlu.** Tiga pertanyaan; satu "ya" sudah cukup:

| Pertanyaan | `BR-2026-014` |
| --- | --- |
| Menyentuh lebih dari satu modul? | **Ya** — access control + MCAT |
| Menambah atau mengubah bentuk data? | Tidak |
| Ada lebih dari satu cara masuk akal mengerjakannya? | **Ya** — middleware global vs pemeriksaan per rute |

Perlu. Yang ditulis SA, ke `backend.md` dan `frontend.md` layarnya:

- **Di mana pemeriksaannya duduk.** Middleware sekali di `app.ts` untuk seluruh `mcatNativeRoutes`, bukan satu
  pemeriksaan per handler — satu tempat yang tidak bisa terlupa saat endpoint baru ditambahkan.
- **Dari mana kewenangannya dibaca.** Menu peran aktif, sumber yang sama dengan `isPathAllowed`, supaya browser
  dan server tidak pernah punya dua daftar berbeda.
- **Sesi belum selesai dihitung dari mana.** `trans_mcat_session` berstatus belum selesai, pola yang sama
  dengan pemeriksaan kolam terkunci di `BR-ADP-07`.

Kalau ketiga jawabannya "tidak", lewati tahap ini. Story-nya langsung dikerjakan, dan catatan teknis yang muncul
saat mengerjakannya ditulis ke §3 `backend.md` sesudahnya.

---

## Tahap 8 — Ke mana semuanya mendarat

| Keluaran | Berkasnya |
| --- | --- |
| `FR-ADP-11`, `FR-ADP-12`, `BR-ADP-11`, `BR-ADP-12` | `product/item-management/mcat/adaptive-setups/README.md` |
| `AC-ADP-16` … `AC-ADP-19` | `…/adaptive-setups/acceptance-criteria.md` |
| Letak middleware, cara membaca kewenangan | `…/adaptive-setups/backend.md` |
| Field terkunci dan keterangannya | `…/adaptive-setups/frontend.md` |
| Kenapa middleware global, bukan per rute — **beserta yang ditolak** | `product/item-management/mcat/decisions.md` |
| Dua OPEN QUESTION dari §2.5 | §6 README layar, jadi `Q-ADP-03` dan `Q-ADP-04` |
| Koreksi `Q-MCAT-03` yang ternyata basi | `product/item-management/mcat/README.md` §7 |
| S1–S4 | Issue tracker |

Baris terakhir sebelum tracker itu yang sering hilang. Analisis menemukan sebuah dokumen salah; memperbaikinya
bagian dari pekerjaan ini, bukan pekerjaan lain nanti.

---

## Dua contoh pendek, dua cabang lainnya

### Cabang SUDAH ADA

> **BR-2026-015** — "Admin minta bisa mencari soal pakai kodenya, sekarang harus scroll."

Analisis, satu perintah:

```sh
grep -rn "FR-ITM-03" docs
```

`FR-ITM-03` sudah menyatakannya, `AC-ITM-01` sudah menguji, dan tesnya ada. Jadi ini **bukan fitur baru** — ini
salah satu dari dua hal:

- Fiturnya ada tapi tidak ketemu admin → masalah penemuan, bukan kebutuhan. Perbaikannya di layar.
- Fiturnya rusak → **bug**, dan `AC-ITM-01` sudah jadi alat ukurnya.

Tidak ada `FR-` baru. Tidak ada Epic. Langsung satu bug atau satu perbaikan kecil.

### Cabang SUDAH DIPUTUSKAN TIDAK

> **BR-2026-016** — "Klien B minta impor soal dari Excel, bukan CSV."

`product/item-management/mcat/item-bank/import/README.md` §5 menyebutnya eksplisit: *"Impor Excel (`.xlsx`).
Hanya CSV. Belum dijadwalkan."* Alasannya ada di `backend.md` §3 — menariknya berarti menambah pustaka
spreadsheet, ditandai `ponytail:` di kodenya.

Jadi jawabannya **bukan** "tidak bisa", dan bukan juga langsung dikerjakan. Yang dikerjakan BA: membawa kembali
ke PO dengan ongkosnya, karena yang berubah bukan kemampuan teknis melainkan prioritas. Kalau PO menyetujui,
yang ditulis pertama adalah entri `DEC-` yang menyebut keputusan lama dan alasan pembalikannya.

**Yang tidak boleh terjadi:** BA diam-diam menghapus baris di §5 lalu menulis `FR-` baru. Keputusan yang hilang
tanpa jejak adalah keputusan yang akan diperdebatkan lagi enam bulan lagi.

---

## Kalau permintaannya datang sebagai spesifikasi

Tahap 0 mengandaikan PO menyerahkan sebuah masalah. Kadang yang datang sudah berupa aturan jadi: tabel bobot,
formula, urutan section, edge case yang sudah dijawab sendiri oleh penulisnya.

Contoh nyatanya di proyek ini: spesifikasi logic laporan PDF asesmen recruitment yang disiapkan PO untuk tim
engineering. Tujuh belas bagian, seluruhnya aturan rendering, dan tidak satu pun kalimat tentang masalah apa
yang sedang dipecahkan.

Itu **bukan** request yang buruk. Bobot skor dan ambang batas rekomendasi adalah keputusan psikometri, bukan
keputusan tim pengembang. Meminta PO menulisnya ulang sebagai masalah tidak menghasilkan apa pun.

### Dokumen itu setara apa

**Feature Requirement (Tahap 4), ditulis PO, belum ber-ID.** Di tiga tingkat dokumentasi
[workflow.md §2](workflow.md), dia duduk di FEATURE LEVEL.

| Kandidat | Kenapa bukan |
| --- | --- |
| Business Request | Tidak ada masalah, tidak ada yang terdampak, tidak ada tanggal. Isinya sudah solusi |
| Product PRD | Cakupannya satu keluaran, bukan produknya. PRD tumbuh; ini sekali pakai untuk satu klien |
| Technical Design | Menjawab **apa** yang dirender, bukan **bagaimana** sistem mendukungnya. Tidak ada endpoint atau tabel |
| Acceptance Criteria | Tidak bergaya diberi/saat/maka, tidak ber-ID, tidak bisa dicentang. Edge case-nya **bahan** AC, belum AC |

Jadi spesifikasi itu **sumber, bukan tujuan.** Yang masuk repo adalah hasil transformasinya:

| Isi spesifikasi | Jadi | Berkasnya |
| --- | --- | --- |
| Aturan turunan — bucket, ambang batas, formula, urutan wajib | `BR-` | `<layar>/README.md` §3 |
| Apa yang ditampilkan dan dari field mana | `FR-` | `<layar>/README.md` §2 |
| Edge case yang sudah dijawab penulisnya | `AC-` | `<layar>/acceptance-criteria.md` |
| Input yang tidak disebut sumbernya | `Q-` | `<layar>/README.md` §6 |
| Bentuk data dan asal fieldnya | — | `<area>/data-model.md`, `<layar>/backend.md` |
| Kenapa aturannya begitu, **beserta dokumen asalnya** | `DEC-` | `<area>/decisions.md` |

Baris terakhir yang paling sering hilang. Enam bulan lagi seseorang akan bertanya kenapa bobotnya angka itu, dan
satu-satunya jawaban yang tersisa adalah "karena begitu di spreadsheet". Entri `DEC-` yang menyebut dokumen
asalnya menutup pertanyaan itu sekali.

Foldernya belum tentu langsung jelas. `docs/product/` mencerminkan `LOCAL_MENUS`, jadi sebuah keluaran yang
tidak punya entri menu sendiri harus ditulis di dalam layar yang memuatnya — dan slugnya didaftarkan di
[docs/README.md](README.md) sebelum `FR-` pertama ditulis.

### Triage untuk spesifikasi

Empat pertanyaan di [Tahap 1](#tahap-1--triage) mengandaikan bentuk masalah, jadi tidak terpakai. Empat ini
penggantinya, dengan hasilnya pada contoh di atas:

| Pertanyaan | Hasilnya |
| --- | --- |
| Tiap aturan punya contoh yang bisa dihitung ulang? | Ya — skor contohnya menghasilkan kategori yang sama dengan yang tertulis |
| Tiap input disebut dari mana asalnya? | **Tidak** — dua input utama tidak punya sumber |
| Edge case sudah dijawab penulisnya? | Ya — ada bagiannya sendiri |
| Ada data contoh yang nyata, bukan karangan? | Ya — mockup laporan lengkap |

**Lolos kalau baris pertama dan ketiga "Ya".** Baris kedua "Tidak" tidak memblokir analisis — dia jadi `Q-`
pertama. Yang diblokir implementasinya, bukan dokumennya.

Baris pertama pengaman yang paling murah. Spesifikasi yang tidak bisa dihitung ulang dari contohnya sendiri
berarti belum selesai, dan itu ketahuan dalam lima menit.

### Yang tetap tidak boleh dilewati

Melewati Tahap 0 dan 1 menghilangkan dua pengaman, jadi **Tahap 2 dikerjakan penuh**, termasuk §2.2 membaca kode
dan §2.5 menandai yang tidak diketahui.

Aturannya satu kalimat: **spesifikasi berwenang atas aturan bisnisnya, tidak berwenang atas kelengkapannya.**

Spesifikasi yang rapi justru yang paling menipu. Contoh di atas konsisten, angkanya bisa dihitung ulang, edge
case-nya sudah dipikirkan — dan tetap kehilangan dua input yang membuatnya tidak bisa dieksekusi sama sekali.
Rapi bukan lengkap.

---

## Templat yang bisa dipakai ulang

### Business Request

```markdown
# BR-YYYY-NNN — <judul: masalahnya, bukan solusinya>

| | |
| --- | --- |
| Tanggal | |
| Pengaju | |
| Prioritas usulan | |

## Masalahnya
<Apa yang terjadi. Kejadian nyata kalau ada, dengan angkanya.>

## Siapa yang terdampak
<Per peran, dan bagaimana terdampaknya.>

## Kalau dibiarkan
<Akibatnya, dan seberapa sering terulang.>

## Kapan dibutuhkan
<Tanggal, beserta apa yang terikat tanggal itu.>

## Yang sudah saya tahu
<Batasan yang diketahui. Boleh kosong. Boleh juga berisi "saya tidak tahu apakah ini soal X atau Y".>
```

### Requirement Analysis

```markdown
# Analisis BR-YYYY-NNN

## 1. Masalahnya, satu kalimat
## 2. Yang benar-benar terjadi sekarang
   <Tabel: lapisan · keadaannya · berkas:baris yang membuktikannya>
## 3. Dibandingkan yang tertulis
   <Tabel: dokumen · katanya · kenyataannya>
## 4. Masalah-masalah yang berbeda di dalamnya
   <M1, M2, … masing-masing dengan sifatnya: celah / perluasan aturan / keputusan produk>
## 5. Yang tidak diketahui
   OPEN QUESTION: … Usulan: … Menunggu: …
## 6. Cabang
   SUDAH ADA / BELUM ADA / SUDAH DIPUTUSKAN TIDAK — boleh lebih dari satu
## 7. Usulan cakupan
   Dikerjakan sekarang: … · Tidak sekarang: … · Diblokir sampai dijawab: …
```

---

## Yang paling sering salah

| Kebiasaan | Kenapa mahal |
| --- | --- |
| Business Request yang sudah menyebut solusi | Menyembunyikan masalahnya; analisis melewatkan pilihan yang lebih murah |
| Analisis yang cuma membaca dokumen | Dokumen bisa basi. Di contoh ini, dua dari tiga lapisan ternyata tidak seperti yang tertulis |
| Satu request dipaksa jadi satu masalah | M1 di contoh ini celah keamanan yang harus ditutup terlepas dari permintaannya |
| Menebak yang tidak diketahui | Membangun aturan bisnis yang tidak pernah diputuskan siapa pun |
| AC yang semuanya penolakan | Terpuaskan dengan menolak semuanya. Tiap larangan butuh pasangan positifnya |
| Technical Design untuk tiap perubahan | Dokumen yang tidak dibaca siapa pun, dan basi lebih cepat dari kodenya |
| Memperbaiki dokumen yang salah "nanti" | Tidak pernah ada nanti |
