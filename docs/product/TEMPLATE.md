# Template dokumen produk

Salin bentuk di bawah ini saat menambah layar atau area baru. Tujuannya satu: siapa pun yang membuka berkas
mana pun sudah tahu di bagian nomor berapa jawabannya berada, tanpa membaca dari atas.

> Untuk **keluaran** — laporan PDF, ekspor, dokumen terbit — pakai [TEMPLATE-OUTPUT.md](TEMPLATE-OUTPUT.md).
> Keluaran bukan layar, jadi tidak dapat folder di cermin menu, dan aturan yang di layar boleh implisit di sana
> harus tertulis.

## Aturan menulis

1. **Folder mengikuti menu.** Ada di `LOCAL_MENUS` ([`apps/api/src/db/local/seed.ts`](../../apps/api/src/db/local/seed.ts))
   berarti dapat folder; tidak ada berarti tidak dapat folder. Yang bukan item menu ditulis di dalam layar yang
   memuatnya.
2. **Subfolder hanya untuk alur, bukan untuk tombol.** Sebuah alur boleh jadi subfolder kalau ia punya dialog
   atau wizard sendiri dan aturannya cukup banyak untuk punya ID sendiri. Kalau ragu, jangan.
3. **Satu layar, empat berkas:** `README.md`, `backend.md`, `frontend.md`, `acceptance-criteria.md`. Keempatnya
   selalu ada, walau salah satunya pendek.
4. **ID memakai slug layar**, bukan nomor urut folder: `FR-DIM-01`, `BR-DIM-01`, `AC-DIM-01`, `Q-DIM-01`,
   `DEC-MCAT-01`. Slug didaftarkan di [`docs/README.md`](../README.md) sebelum dipakai.
5. **ID tidak pernah dipakai ulang dan tidak pernah dinomori ulang.** Kebutuhan yang dibatalkan ditandai
   ~~`FR-DIM-07`~~ (dibatalkan), bukan dihapus — supaya `grep -rn "FR-DIM-07" .` tetap menemukan jejaknya.
6. **Satu kebutuhan, satu baris.** Kalau butuh dua paragraf, berarti itu dua kebutuhan.
7. **Wrap teks di 120 kolom.** Satu paragraf satu baris panjang membuat diff tidak terbaca.
8. **Tulis nilai konkret**: rentang, default, satuan, batas. Angka yang hilang akan ditebak orang lain.
9. **Semua tautan relatif**, supaya bisa di-Ctrl+klik dari IDE.
10. **Tabel hanya untuk hal yang benar-benar tabular** — rute, field, enum. Sisanya daftar biasa.
11. **Tidak ada riwayat di keempat berkas layar.** Tidak ada "dulu begini sekarang begitu", tidak ada status
    "sudah dibangun", tidak ada tanggal. Itu semua milik `decisions.md` area yang bersangkutan.
12. **Berkas ditulis untuk pembacanya.** `README.md` harus terbaca PO tanpa membuka kode; `backend.md` dan
    `frontend.md` untuk engineer; `acceptance-criteria.md` untuk QA.

## Susunan satu area

```
product/<parent-menu>/<area>/
├── README.md              §1 Ringkasan · §2 Pengguna & akses · §3 Bagaimana bagiannya tersusun
│                          §4 Aturan besar · §5 Daftar layar · §6 Di luar cakupan
│                          §7 Pertanyaan terbuka · §8 Kamus istilah
├── data-model.md          §1 Diagram · §2 Tabel · §3 Aturan integritas
├── architecture.md        §1 Peta rute · §2 Endpoint · §3 Migrasi · §4 Aturan antarmuka · §5 Pengujian
├── decisions.md           keputusan bertanggal, terbaru di bawah
└── <layar>/
    ├── README.md
    ├── backend.md
    ├── frontend.md
    └── acceptance-criteria.md
```

## `<layar>/README.md`

```markdown
# <Nama layar>

| | |
| --- | --- |
| **Menu** | Item Management → MCAT → <Nama> |
| **Rute** | `/...` |
| **Slug ID** | `XXX` |
| **Prasyarat** | [<Layar lain>](../<layar>/README.md) |
| **Teknis** | [backend](backend.md) · [frontend](frontend.md) · [kriteria penerimaan](acceptance-criteria.md) |

## 1. Ringkasan

3–5 kalimat: apa layar ini, untuk siapa, dan kenapa bentuknya begini. Tanpa jargon internal. Kalau orang hanya
membaca bagian ini, dia sudah bisa menjelaskannya ke orang lain.

## 2. Alur pengguna

Langkah bernomor, dari layar pertama sampai selesai. Sebut nama tombol seperti yang tertulis di layar.

## 3. Kebutuhan fungsional

Kalimat aktif, satu perilaku, bisa dites. Dikelompokkan per layar atau per alur kalau isinya banyak.
Yang TIDAK boleh ada di sini: nama tabel, nama komponen, cara implementasi.

- **FR-XXX-01** Admin dapat ...
- **FR-XXX-02** Sistem menolak ... kalau ...

## 4. Aturan bisnis

Aturan yang tidak kelihatan dari UI: larangan hapus, keunikan, kaskade, kepemilikan data.

- **BR-XXX-01** ...

## 5. Di luar cakupan

Sudah diputuskan **tidak** dikerjakan. Sama pentingnya dengan yang di dalam cakupan, supaya tidak terbaca
sebagai "terlupa".

- ...

## 6. Pertanyaan terbuka

Belum ada yang memutuskan. Beda dari §5: yang di sini menunggu jawaban manusia, bukan menunggu giliran.
Terjawab berarti pindah — ke §4 sebagai aturan, ke §5 sebagai keputusan menolak, atau ke `decisions.md`.

- **Q-XXX-01** ... — *menunggu:* PO
```

## `<layar>/backend.md`

```markdown
# <Nama layar> — Backend

| | |
| --- | --- |
| **Modul** | `apps/api/src/...` |
| **Tabel** | `master_...` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |

## 2. Aturan yang ditegakkan di sini

Satu baris per aturan, sebut kode statusnya. Tautkan ke BR yang dibuktikannya.

## 3. Catatan implementasi

Keputusan teknis yang tidak jelas dari kodenya sendiri, dan jebakan yang sudah pernah menggigit.
Kosongkan kalau tidak ada — jangan diisi basa-basi.
```

## `<layar>/frontend.md`

```markdown
# <Nama layar> — Frontend

| | |
| --- | --- |
| **Rute** | `apps/cms/src/routes/...` |
| **Komponen** | `features/.../X.tsx` |

## 1. Layar

Apa yang ada di layar itu, dan bagian mana mengerjakan apa.

## 2. Keputusan antarmuka

Kenapa layarnya berperilaku begitu — terutama pilihan yang tampak sewenang-wenang kalau tidak dijelaskan.

## 3. Catatan implementasi

Sama seperti di backend.md. Kosongkan kalau tidak ada.
```

## `<layar>/acceptance-criteria.md`

Satu aturan yang membedakan berkas ini dari yang lain: **`- [x]` hanya sah kalau ada tes otomatis yang namanya
memuat ID AC itu.** Itu yang membuat centangnya bisa dipercaya, dan itu yang diperiksa `docs-check`.

```markdown
# <Nama layar> — Kriteria penerimaan

Lulus kalau hasilnya terlihat di layar, bukan hanya benar di database. Butir yang tercentang punya tes otomatis
yang namanya memuat ID-nya; sisanya diperiksa manual.

## 1. <Kelompok>

- [ ] **AC-XXX-01** (FR-XXX-01) Diberi ..., saat ..., maka ...
- [x] **AC-XXX-02** (BR-XXX-01) Diberi ..., saat ..., maka ...
```

Di sisi tes:

```ts
test('AC-XXX-02 menolak hapus dimensi yang masih diukur satu soal', async () => { … })
```

## `decisions.md`

Satu berkas per area. Entri baru ditambahkan **di bawah**, entri lama tidak pernah disunting — kalau sebuah
keputusan dibatalkan, tulis keputusan baru yang menyebutnya.

**Tiap entri wajib punya baris "Ditolak:".** Keputusan tanpa alternatif yang ditolak bukan keputusan, itu
deskripsi — dan deskripsi tempatnya di `README.md`. Aturan ini yang menjaga berkas ini tetap pendek.

```markdown
## DEC-XXX-01 — <judul keputusan> · YYYY-MM-DD

Dua sampai empat kalimat: apa yang diputuskan dan kenapa.

**Ditolak:** <alternatifnya>, karena <alasannya>.

Menegakkan: [BR-XXX-01](<layar>/README.md)
```
