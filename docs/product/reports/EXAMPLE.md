# Contoh — satu bagian, dari prosa mentah sampai kode tes

Satu bagian kecil sebuah spesifikasi keluaran, ditelusuri penuh. Tujuannya menunjukkan **bentuk** perbaikannya,
bukan isi satu laporan tertentu — polanya sama untuk ekspor CSV, sertifikat, atau payload webhook.

Aturan lengkapnya di [TEMPLATE-OUTPUT.md](../TEMPLATE-OUTPUT.md). Berkas ini contohnya.

> ID di sini contoh. `docs-check` melewatkan berkas ini supaya nomornya tidak bertabrakan dengan nomor
> sungguhan.

---

## Tahap 0 — Yang biasanya datang dari PO

Bentuk khasnya: benar, terbaca, dan bolong di tepinya.

```markdown
## Logic — Skor Kesesuaian

Skor Kesesuaian menggambarkan tingkat kecocokan peserta terhadap profil jabatan.
Dihitung dari 4 kompetensi inti. Jumlah kompetensi pada tiap rating dikalikan bobot:

| Rating | Bobot |
| 1 | 0 |
| 2 | 1 |
| 3 | 1.75 |
| 4 | 2.25 |
| 5 | 2.5 |

Formula: =ROUND((O4*0+P4*1+Q4*1.75+R4*2.25+S4*2.5)*100/10,0)

| Rentang | Kategori |
| 0–49 | Rendah |
| 50–69 | Menengah |
| 70–85 | Memadai |
| 86–100 | Tinggi |

Kategori ditentukan langsung dari nilai skor. Jangan memakai label berdasarkan judgement narasi.
```

Ini **bukan dokumen jelek.** Bobotnya konkret, rentangnya bersambung, dan kalimat terakhirnya menutup jalur
salah yang nyata. Yang kurang ada di tepinya, dan tepinya yang jadi bug.

**Lima hal yang hilang:**

| # | Yang hilang | Akibatnya nanti |
| --- | --- | --- |
| 1 | Siapa yang menghitung — scoring engine atau renderer | Dua implementasi, dan keduanya menyimpang begitu salah satu diperbaiki |
| 2 | Pembagi `10` tidak dijelaskan asalnya | Komponen bertambah jadi lima, skornya salah, tidak ada yang error |
| 3 | `ROUND` tidak bahasa-netral | `62.5` jadi `63` di JavaScript, `62` di Python — angka berbeda di dokumen yang sama |
| 4 | Komponen yang tidak terukur tidak disebut | Satu kompetensi kosong tetap menghasilkan angka, angka yang salah |
| 5 | Tidak ada contoh terhitung penuh | Tidak ada satu pun bagian yang bisa terbukti salah saat di-review |

---

## Tahap 1 — Hasil perbaikannya

Isinya tidak berubah. Yang ditambahkan cuma tepinya.

```markdown
### 4.2 Skor Kesesuaian

Indeks 0–100 yang menggambarkan tingkat kecocokan profil kompetensi peserta terhadap profil jabatan.
Dibaca perekrut sebagai angka pembanding antar kandidat pada jabatan yang sama.

**Kepemilikan**

Dihitung **scoring engine**, dikirim ke renderer sebagai angka jadi. Renderer **tidak** menghitung ulang.
Formula di bawah mendokumentasikan cara hulu menghitungnya — bukan izin menghitungnya lagi di hilir.

**Aturan**

- **BR-RPT-01** Skor dihitung dari kompetensi inti saja. Kompetensi lain tidak masuk perhitungan.
- **BR-RPT-02** Kategori ditentukan langsung dari nilai skor, tidak pernah dari narasi atau penilaian manual.
- **BR-RPT-03** Skor hanya dihitung bila **seluruh** kompetensi inti punya rating. Satu saja tidak terukur,
  skornya tidak diterbitkan dan seluruh keluaran ditahan — lihat §7.

**Perhitungan**

    bobot        = { 1: 0, 2: 1, 3: 1.75, 4: 2.25, 5: 2.5 }
    terbobot     = Σ (jumlah kompetensi inti pada rating r × bobot[r])
    maksimum     = jumlah kompetensi inti × bobot[5]          ← bukan angka jadi
    skor         = bulat(terbobot × 100 / maksimum)

Dengan 4 kompetensi inti, `maksimum` = 10. **Ditulis sebagai rumus, bukan sebagai 10**, supaya penambahan
kompetensi inti kelima tidak menghasilkan skor yang salah tanpa gejala.

Pembulatan: setengah ke atas. `62.5 → 63`.

**Tabel batas**

| Rentang | Kategori |
| --- | --- |
| 0–49 | Rendah |
| 50–69 | Menengah |
| 70–85 | Memadai |
| 86–100 | Tinggi |

Lantai 0 dan atap 100 dijamin formulanya: terbobot minimum 0, maksimum `jumlah × bobot[5]`. Karena itu tidak
ada baris "di luar rentang" — dan alasan itu ditulis, bukan diandaikan.
```

Tambahan di dua tempat lain dokumen yang sama:

```markdown
## 2. Kontrak data

| Input | Tipe & rentang | Dihitung oleh | Wajib? | Kalau tidak ada |
| --- | --- | --- | --- | --- |
| Skor Kesesuaian | Bilangan bulat 0–100 | Scoring engine | Wajib | Keluaran tidak diterbitkan |

## 7. Kasus batas

| Kasus | Yang terjadi |
| --- | --- |
| Satu kompetensi inti tidak terukur | Skor tidak dihitung; keluaran tidak diterbitkan; kegagalan dicatat |
| Seluruh kompetensi inti rating 1 | Skor 0 → "Rendah". Sah, bukan galat |
| Seluruh kompetensi inti rating 5 | Skor 100 → "Tinggi". Sah |
| Skor tepat di batas — 49, 50, 69, 70, 85, 86 | Batas bawah tiap rentang masuk ke rentang itu |
```

Baris terakhir itu delapan kata, dan ia menutup kelas bug yang paling sering terjadi pada tabel rentang.

---

## Tahap 2 — Jadi fungsi

Tabel batas jadi **fungsi murni**: masuk angka, keluar label, tanpa I/O, tanpa render.

```ts
/** Weights per rating, and the ceiling they imply. Never hardcode the divisor: it moves with the list. */
const WEIGHT: Record<number, number> = { 1: 0, 2: 1, 3: 1.75, 4: 2.25, 5: 2.5 }

/** Half away from zero, so the number matches what the spec's worked example prints. */
const roundHalfUp = (value: number) => Math.sign(value) * Math.round(Math.abs(value))

export function fitScore(coreRatings: number[]): number {
  const weighted = coreRatings.reduce((sum, rating) => sum + (WEIGHT[rating] ?? 0), 0)
  const ceiling = coreRatings.length * WEIGHT[5]
  return roundHalfUp((weighted * 100) / ceiling)
}

export type FitCategory = 'Rendah' | 'Menengah' | 'Memadai' | 'Tinggi'

/** Lower bound inclusive, in order. The last band carries the ceiling. */
const BANDS: [min: number, category: FitCategory][] = [
  [86, 'Tinggi'],
  [70, 'Memadai'],
  [50, 'Menengah'],
  [0, 'Rendah'],
]

export const fitCategory = (score: number): FitCategory =>
  BANDS.find(([min]) => score >= min)?.[1] ?? 'Rendah'
```

Tiga hal yang dipindahkan dari spesifikasi ke bentuk kode, bukan ke komentar:

- **`ceiling` dihitung dari panjang daftar.** Rumus §4, bukan angka `10`.
- **`BANDS` tabel, bukan `if` bertingkat.** Rentang bolong terlihat saat dibaca; `if` bertingkat
  menyembunyikannya.
- **`roundHalfUp` punya nama sendiri.** `Math.round` kebetulan benar untuk bilangan positif; namanya yang
  menahan orang berikutnya menggantinya dengan pembulatan bawaan bahasa lain.

Yang sengaja **tidak** ada di sini: pembacaan database, pemanggilan API, dan perenderan. Begitu salah satunya
masuk, rentangnya tidak bisa lagi diuji satu per satu, dan rentang yang bolong baru ketahuan dari PDF yang
sudah terkirim.

---

## Tahap 3 — Jadi kriteria penerimaan

```markdown
- [ ] **AC-RPT-01** (BR-RPT-01) Diberi kompetensi inti berrating 2, 3, 3, 4, maka Skor Kesesuaian = 68.
- [ ] **AC-RPT-02** (BR-RPT-01) Diberi peserta yang sama ditambah kompetensi non-inti berrating 5, maka
      Skor Kesesuaian tetap 68.
- [ ] **AC-RPT-03** (BR-RPT-02) Diberi skor 69, maka kategorinya "Menengah"; diberi 70, maka "Memadai".
- [ ] **AC-RPT-04** (BR-RPT-02) Diberi skor 49/50, 85/86, maka kategorinya berpindah tepat di batas itu.
- [ ] **AC-RPT-05** (BR-RPT-03) Diberi satu kompetensi inti tanpa rating, maka keluaran tidak diterbitkan —
      bukan diterbitkan dengan skor yang dihitung dari sisanya.
- [ ] **AC-RPT-06** Diberi lima kompetensi inti yang semuanya berrating 5, maka skornya 100 — bukan 125.
```

Empat dari enam butir itu **tidak akan pernah ditulis** kalau spesifikasinya berhenti di Tahap 0:

- `AC-RPT-02` lahir dari kata "inti saja" yang dinaikkan jadi `BR-RPT-01`.
- `AC-RPT-04` lahir dari baris kasus batas "skor tepat di batas".
- `AC-RPT-05` lahir dari baris "komponen tidak terukur".
- `AC-RPT-06` lahir dari pembagi yang ditulis sebagai rumus — dan itu satu-satunya butir yang menguji
  spesifikasinya sendiri masih benar saat daftarnya berubah.

---

## Tahap 4 — Jadi tes

```ts
test('AC-RPT-01 skor kesesuaian dari rating kompetensi inti', () => {
  expect(fitScore([2, 3, 3, 4])).toBe(68)
})

test('AC-RPT-03 AC-RPT-04 kategori berpindah tepat di batas bawah tiap rentang', () => {
  const cases: [number, FitCategory][] = [
    [0, 'Rendah'], [49, 'Rendah'],
    [50, 'Menengah'], [69, 'Menengah'],
    [70, 'Memadai'], [85, 'Memadai'],
    [86, 'Tinggi'], [100, 'Tinggi'],
  ]
  for (const [score, expected] of cases) expect(fitCategory(score)).toBe(expected)
})

test('AC-RPT-06 pembagi mengikuti jumlah kompetensi inti, bukan angka tetap', () => {
  expect(fitScore([5, 5, 5, 5])).toBe(100)
  expect(fitScore([5, 5, 5, 5, 5])).toBe(100)
})
```

Tes batas menguji **kedua sisi tiap rentang**, bukan satu nilai di tengahnya. `fitCategory(75)` benar bahkan
kalau perbandingannya `>` yang seharusnya `>=`; yang membuktikannya `70` dan `69`.

Nama tesnya memuat ID AC-nya, jadi `bun scripts/docs-check.ts` bisa memverifikasi centang di
`acceptance-criteria.md` — dan `grep -rn "RPT-" docs apps` memberi rantai utuhnya: aturan, kriteria, tes.

---

## Rantainya, ringkas

```
prosa PO
   ↓  tambahkan kepemilikan, rumus, pembulatan, kasus batas
spesifikasi §4 + §7
   ↓  tabel batas jadi fungsi murni
fitScore() · fitCategory()
   ↓  tiap aturan dan tiap kasus batas jadi satu butir
AC-RPT-01 … AC-RPT-06
   ↓  ID masuk nama tes
test('AC-RPT-03 AC-RPT-04 …')
   ↓
docs-check memverifikasi centangnya
```

Yang bertambah dari Tahap 0 ke Tahap 1 kira-kira **dua belas baris**. Yang dibeli dua belas baris itu: empat
kriteria penerimaan yang tidak akan pernah terpikir, dan empat kelas bug yang tidak menimbulkan galat apa
pun — angka yang salah, terbit rapi, ketahuan saat sudah sampai ke klien.
