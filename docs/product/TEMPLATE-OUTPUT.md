# Template — spesifikasi keluaran

Untuk apa pun yang **dihasilkan** sistem dan dibaca di luar layar: laporan PDF, ekspor CSV, dokumen terbit,
sertifikat, payload webhook. Keluaran bukan layar, jadi ia tidak dapat folder di cermin menu — lihat
[README.md](../README.md).

Layar memakai [TEMPLATE.md](TEMPLATE.md). Keluaran memakai berkas ini. Bedanya satu: layar punya pengguna yang
bisa bertanya saat bingung; **keluaran tidak.** Sebuah PDF yang salah tetap terbit, tetap terkirim, dan
ketahuannya belakangan — jadi aturan yang di layar boleh implisit, di sini harus tertulis.

```
product/reports/<nama-keluaran>/
├── README.md               §1–§10 di bawah
├── data-model.md           dari mana tiap angka datang (kalau rumit)
└── acceptance-criteria.md  AC- yang terikat nama tes
```

> **Cara memakai berkas ini.** Salin kerangkanya, lalu isi. Blok berawalan **Kenapa bagian ini ada** dan
> **Jebakannya** adalah penunjuk untuk penulisnya — hapus saat dokumennya jadi.

---

## Kepala dokumen

```markdown
# <Nama keluaran>

| | |
| --- | --- |
| **Jenis** | Laporan PDF / ekspor CSV / dokumen terbit |
| **Slug ID** | `XXX` |
| **Versi** | 1.2 |
| **Tanggal** | YYYY-MM-DD |
| **Status** | Draf · Dalam review · Disetujui |
| **Pemilik isi** | <nama, peran> |
| **Rujukan visual** | <tautan mockup, kalau ada> |
| **Teknis** | [model data](data-model.md) · [kriteria penerimaan](acceptance-criteria.md) |
```

> **Kenapa bagian ini ada:** renderer dibangun dari dokumen ini, dan dokumen tanpa versi tidak bisa dirujuk saat
> keluarannya ternyata salah. "Ikut spek" tidak berarti apa-apa kalau spek-nya tidak punya nomor.
>
> **Jebakannya:** status ditulis manusia, tidak pernah oleh AI.

---

## Tujuan & batas

```markdown
## 1. Tujuan & batas

<Dua sampai empat kalimat: keluaran ini apa, siapa pembacanya, dan keputusan apa yang diambil orang dari sana.>

**Yang diputuskan dokumen ini:** logika isi — apa yang muncul, kapan, dan dari angka mana.

**Yang TIDAK diputuskan dokumen ini:** tata letak, warna, tipografi, jumlah halaman. Itu mengikuti template
visual <nama>.
```

> **Kenapa bagian ini ada:** tanpa kalimat "yang tidak diputuskan", diskusi logika akan tenggelam oleh diskusi
> desain, dan dokumennya berhenti bisa di-review orang yang mengerti logikanya.

---

## Kontrak data

Kolom **Dihitung oleh** wajib. Bukan opsional, bukan "nanti".

```markdown
## 2. Kontrak data

| Input | Tipe & rentang | Dihitung oleh | Wajib? | Kalau tidak ada | Dipakai section |
| --- | --- | --- | --- | --- | --- |
| Skor kompetensi | Bilangan bulat 1–5, per kompetensi | Scoring engine | Wajib | Keluaran tidak diterbitkan | §4.1, §4.5, §4.6 |
| Skor kesesuaian | Bilangan bulat 0–100 | **Scoring engine** — renderer tidak menghitung ulang | Wajib | Keluaran tidak diterbitkan | §4.2, §4.4 |
| Catatan pengawasan | Daftar, boleh kosong | Layanan pengawasan | Opsional | Section dilewati | §4.8 |
```

> **Kenapa bagian ini ada:** ini bagian yang paling sering salah, dan salahnya paling mahal.
>
> **Jebakannya:** dokumen mendaftarkan sebuah angka sebagai **input**, lalu di bagian lain memberikan
> **formulanya**. Dua-duanya terbaca benar, dan akibatnya scoring engine dan renderer sama-sama menghitungnya.
> Dua implementasi dari satu formula akan berbeda — bukan mungkin, pasti, begitu salah satunya diperbaiki.
> **Satu angka, satu pemilik.** Kalau formulanya ditulis di sini, tandai jelas bahwa itu dokumentasi cara
> hulu menghitungnya, bukan izin untuk menghitung ulang.
>
> Kolom **Kalau tidak ada** yang membedakan spesifikasi dari harapan. Tiga jawaban yang sah: keluaran tidak
> diterbitkan, section dilewati, atau nilai penggantinya `<sebutkan>`. "Tidak mungkin kosong" bukan jawaban —
> tulis apa yang terjadi kalau ternyata kosong.

---

## Peta section

```markdown
## 3. Peta section

Urutan ini tetap. Data kosong melewati section-nya, **tidak** menggeser urutannya.

| # | Section | Input yang dikonsumsi | Kalau inputnya kosong |
| --- | --- | --- | --- |
| 1 | Sampul | Profil peserta | Tidak pernah kosong |
| 2 | Ringkasan penilaian | Skor kompetensi | Keluaran tidak diterbitkan |
| 3 | Area kekuatan | Skor kompetensi ≥ 4 | Section tidak muncul |
| 4 | Lampiran pengawasan | Catatan pengawasan | Section tidak muncul |

*Nomor halaman bukan logika bisnis. Renderer mempertahankan urutan section, bukan nomor halamannya.*
```

> **Kenapa bagian ini ada:** ini satu-satunya tempat yang menjawab "section ini kosong, lalu bagaimana" untuk
> **semua** section sekaligus. Ditaruh terpencar di tiap section, selalu ada satu yang terlewat.
>
> **Jebakannya:** kalimat terakhir. Tanpa itu, seseorang akan menyamakan keluaran dengan mockup halaman
> per halaman, lalu memaksa halaman kosong muncul supaya nomornya cocok.

---

## Aturan per section

Satu blok per section. Bentuknya seragam supaya bisa dibaca melompat.

```markdown
### 4.2 <Nama section>

<Satu paragraf: section ini menampilkan apa, dan pembacanya memakainya untuk apa.>

**Aturan**

- **BR-XXX-01** <satu aturan, satu baris, bisa diuji>
- **BR-XXX-02** …

**Tabel batas**

| Rentang | Kategori | Yang ditampilkan |
| --- | --- | --- |
| 0–49 | Rendah | … |
| 50–69 | Menengah | … |
| 70–100 | Tinggi | … |

**Pseudocode** — hanya kalau ada percabangan

    IF a >= 70 AND b >= 100  → "Disarankan"
    ELSE IF a >= 50 AND b >= 90 → "Disarankan dengan pertimbangan"
    ELSE → "Belum disarankan"

Urutan evaluasi mengikat: cabang pertama diperiksa lebih dulu.

**Contoh terhitung**

<Angka masuk → langkah perhitungannya → angka keluar → yang tampil.>
```

> **Kenapa "urutan evaluasi mengikat" ditulis:** percabangan bertingkat tanpa urutan eksplisit akan
> diimplementasikan sebagai tiga `if` terpisah, dan kondisi yang tumpang tindih memberi hasil berbeda tergantung
> siapa yang menang. Sebut urutannya, atau buat cabangnya benar-benar saling lepas.

---

## Rentang & batas

Satu tempat untuk **semua** rentang di dokumen ini. Wajib, walaupun rentangnya sudah muncul di §4.

```markdown
## 5. Rentang & batas

| Besaran | Lantai | Atap | Rentangnya | Bersambung? | Di luar rentang |
| --- | --- | --- | --- | --- | --- |
| Skor kesesuaian | 0 | 100 | 0–49 · 50–69 · 70–85 · 86–100 | ✔ tanpa celah | Tidak mungkin: formulanya berlantai 0 dan beratap 100 |
| Skor kognitif | **70** | tak terbatas | 70–79 · 80–89 · 90–110 · 111–120 · 121–130 · >130 | ✔ tanpa celah | **< 70 → <sebutkan yang terjadi>** |
```

> **Kenapa bagian ini ada:** rentang yang ditulis tersebar terlihat lengkap satu per satu, dan bolong saat
> dijajarkan. Menjajarkannya dalam satu tabel memaksa tiga pertanyaan yang mudah terlewat: **apa lantainya, apa
> atapnya, dan apa yang terjadi di luar keduanya.**
>
> **Jebakannya:** tabel yang mulai dari nilai terendah yang *pernah terlihat*, bukan dari nilai terendah yang
> *mungkin*. Rentang pertama yang dimulai dari 70 diam-diam mengandaikan nilai di bawah 70 tidak ada. Kalau
> memang berlantai 70, tulis lantainya. Kalau tidak, isi barisnya.
>
> Periksa bersambungnya dengan tangan: akhir tiap rentang + 1 harus sama dengan awal rentang berikutnya.

---

## Angka, pembulatan, satuan

```markdown
## 6. Angka, pembulatan, satuan

- **Pembulatan:** setengah dibulatkan ke atas. `62.5 → 63`, `-2.5 → -3`.
- **Desimal yang ditampilkan:** <berapa angka di belakang koma, per besaran>.
- **Satuan:** <sebutkan, termasuk untuk yang terlihat jelas — "soal", "menit", "persen">.
- **Pembagi dan konstanta:** tulis rumusnya, bukan hasilnya.
  Benar: `bobot_maksimum × jumlah_komponen`. Salah: `10`.
```

> **Kenapa "setengah ke atas" ditulis eksplisit:** `ROUND(x, 0)` itu semantik Excel, bukan aturan
> bahasa-netral. Excel dan JavaScript membulatkan setengah menjauhi nol; Python membulatkan ke bilangan genap
> terdekat, jadi `round(62.5)` di Python memberi `62`, bukan `63`. Kalau nilai bisa jatuh tepat di `.5` — dan
> itu terjadi tiap kali bobotnya pecahan — dua implementasi yang sama-sama "ikut spek" akan mencetak angka
> berbeda di dokumen yang sama.
>
> **Jebakan konstanta:** angka hasil perhitungan yang ditulis sebagai angka jadi. Pembagi `10` yang sebenarnya
> `4 komponen × bobot maksimum 2.5` akan tetap `10` saat komponennya jadi lima — dan hasilnya salah tanpa satu
> pun error. Tulis asalnya.

---

## Kasus batas

Daftar ini **wajib dijawab semuanya**. Baris yang tidak relevan ditulis "tidak berlaku, karena …", bukan
dihapus.

```markdown
## 7. Kasus batas

| Kasus | Yang terjadi |
| --- | --- |
| Tidak ada satu pun butir memenuhi syarat sebuah section | Section tidak muncul |
| Seluruh butir masuk ke satu kategori yang sama | … |
| Satu butir memenuhi syarat dua section sekaligus | Tidak mungkin: kategorinya eksklusif dari skornya |
| Sebuah input wajib **kosong / null** | … |
| Sebuah input di **luar rentang** yang didefinisikan | … |
| Sebuah komponen **tidak terukur** — pesertanya tidak menyelesaikannya | … |
| Nilai **nol** — ditampilkan sebagai 0, atau disembunyikan? | … |
| Hanya **satu** butir di sebuah daftar | … |
| Teks yang jauh **lebih panjang** dari contoh di mockup | … |
```

> **Kenapa bagian ini ada:** kasus batas keluaran tidak punya siapa-siapa untuk bertanya. Layar yang bingung
> menampilkan pesan; PDF yang bingung tetap terbit.
>
> **Jebakannya, dan ini yang paling sering:** baris **"komponen tidak terukur"**. Dokumen menangani "tidak ada
> yang berskor tinggi" dan "tidak ada yang berskor rendah" dengan rapi, lalu tidak pernah menyebut apa yang
> terjadi kalau salah satu komponen tidak punya skor sama sekali. Formula agregat yang membagi dengan jumlah
> komponen tetap menghasilkan angka — angka yang salah, tanpa gejala.

---

## Inventaris konten

```markdown
## 8. Inventaris konten

| Jenis teks | Rumus jumlahnya | Jumlah | Penulis | Tempatnya | Bahasa |
| --- | --- | --- | --- | --- | --- |
| Definisi komponen | 1 × 8 komponen | 8 | Tim konten | Content master | ID, EN |
| Narasi umum per skor | 8 komponen × 5 skor | 40 | Tim konten | Content master | ID, EN |
| Narasi kekuatan | 8 komponen × 2 skor tinggi | 16 | Tim konten | Content master | ID, EN |
| Narasi pengembangan | 8 komponen × 2 skor rendah | 16 | Tim konten | Content master | ID, EN |
| Narasi rekomendasi | 1 × 3 kategori | 3 | Tim konten | Dokumen ini §4.4 | ID, EN |
| | | **83 × 2 bahasa** | | | |
```

> **Kenapa bagian ini ada:** ini biasanya jalur kritis sebenarnya, dan ia tidak terlihat di mana pun. Dokumen
> menulis "narasi diambil dari library yang telah disiapkan" dalam satu kalimat, dan kalimat itu menyembunyikan
> **delapan puluh potong teks** yang harus ditulis manusia, di-review, dan diterjemahkan.
>
> **Jebakannya:** menulis jumlahnya sebagai angka jadi. Tulis **rumusnya** — `8 × 5` — supaya saat komponennya
> bertambah jadi sembilan, tambahan 5 narasi baru itu ikut terlihat.
>
> Kolom bahasa bukan formalitas. Kalau keluarannya punya lebih dari satu bahasa, tiap potong teks berlipat, dan
> keluaran berbahasa kedua yang narasinya belum ada akan terbit dengan bagian kosong.

---

## Contoh terhitung penuh

```markdown
## 9. Contoh terhitung penuh

Satu peserta, dari input sampai yang tercetak. Angkanya nyata, bukan "misalnya X".

**Input**

| Komponen | Skor |
| --- | --- |
| A | 2 |
| B | 3 |
| … | … |

**Perhitungan**

    jumlah rating 2 = 1  → 1 × 1    = 1
    jumlah rating 3 = 2  → 2 × 1.75 = 3.5
    jumlah rating 4 = 1  → 1 × 2.25 = 2.25
                                     ------
                                       6.75
    skor = bulat(6.75 × 100 / 10) = 68  → kategori "Menengah"

**Yang tercetak**

| Section | Isinya |
| --- | --- |
| Ringkasan | 8 komponen, semuanya tampil |
| Area kekuatan | Hanya komponen D (skor 4) |
| Area pengembangan | Komponen A, C, F (skor 2) |
| Rekomendasi | "Disarankan dengan pertimbangan" |
```

> **Kenapa bagian ini ada:** contoh terhitung penuh adalah satu-satunya bagian dokumen yang bisa **salah secara
> terbukti**. Semua bagian lain bisa dibaca dan disetujui sambil salah paham; angka yang tidak berujung di hasil
> yang tertulis akan ketahuan saat ditelusuri.
>
> Ini juga yang langsung jadi kasus uji pertama. Lihat §11.

---

## Pertanyaan terbuka

```markdown
## 10. Pertanyaan terbuka

- **Q-XXX-01** <pertanyaannya> — *usulan:* <usulan> — *menunggu:* <siapa>
```

> **Kenapa bagian ini ada:** tanpa tempat resmi, yang belum diketahui akan ditulis sebagai kalimat yang
> terdengar seperti aturan. Contohnya: *"ditampilkan ketika datanya tersedia dan memenuhi kondisi yang
> ditentukan"* — kondisi yang mana, tidak pernah disebut, dan kalimat itu lolos review karena bentuknya seperti
> aturan. **Aturan yang tidak menyebut nilainya bukan aturan.** Pindahkan ke sini sebagai `Q-`.
>
> Sertakan usulan. Pertanyaan yang datang dengan usulan bisa dijawab "ikut usulan"; yang tidak, butuh rapat.

---

## Transfer ke technical docs

Bagian ini bukan bagian dokumennya — ini cara membacanya saat berpindah ke teknis.

Aturannya satu: **tiap tabel batas jadi satu fungsi murni, satu `BR-`, dan satu tes berbasis tabel.**

| Di spesifikasi | Jadi kode | Jadi dokumen | Jadi tes |
| --- | --- | --- | --- |
| §2 baris input | Tipe di `packages/contracts` | Tabel di `data-model.md` | Penolakan input di luar kontrak |
| §4 tabel batas | `categoryOf(score): Category` — fungsi murni, tanpa I/O | `BR-XXX-nn` | Satu kasus per rentang **plus kedua nilai batasnya** |
| §4 pseudocode | Satu fungsi, urutan cabang sesuai tulisan | `BR-XXX-nn` | Satu kasus per cabang |
| §5 lantai/atap | Guard di tepi, bukan di tiap pemanggil | `BR-XXX-nn` | Satu kasus di bawah lantai, satu di atas atap |
| §6 pembulatan | Satu pembantu, dipakai semua | Catatan di `backend.md` §3 | Kasus `.5` eksplisit |
| §7 tiap baris | Cabang tertangani | `AC-XXX-nn` | Satu tes per baris |
| §8 tiap jenis teks | Kunci di content master | Tabel di `README.md` | Pemeriksaan kelengkapan per bahasa |
| §9 contoh terhitung | — | — | **Kasus uji pertama, apa adanya** |

Bentuk konkretnya:

```
§4 tabel batas skor kesesuaian
      ↓
fitCategory(score: number): 'Rendah' | 'Menengah' | 'Tinggi'      fungsi murni
      ↓
BR-XXX-04  Kategori ditentukan dari skor, bukan dari narasi        aturan bisnis
      ↓
AC-XXX-07  Diberi skor 69, maka kategorinya "Menengah";            kriteria penerimaan
           diberi 70, maka "Tinggi"
      ↓
test('AC-XXX-07 batas kategori skor kesesuaian', …)                tes
```

> **Kenapa fungsi murni:** tabel batas adalah pemetaan angka ke label. Begitu ia hidup di dalam fungsi yang juga
> mengambil data atau merender sesuatu, ia tidak bisa diuji per rentang, dan rentang yang bolong baru ketahuan
> dari keluaran yang sudah terbit.
>
> **Kenapa "plus kedua nilai batasnya":** bug rentang hampir selalu ada di ujungnya — `>` yang seharusnya `>=`.
> Menguji 75 di rentang 70–85 tidak membuktikan apa-apa; yang membuktikan adalah 69, 70, 85, dan 86.

---

## Daftar periksa sebelum diserahkan ke tim teknis

Dokumen yang salah satu barisnya "tidak" belum siap dikerjakan.

- [ ] Tiap input di §2 menyebut **siapa yang menghitungnya**, dan tidak ada angka yang punya dua pemilik.
- [ ] Tiap input di §2 menyebut **apa yang terjadi kalau tidak ada**.
- [ ] Tiap rentang di §5 punya **lantai, atap**, dan baris **di luar rentang**.
- [ ] Rentang di §5 **bersambung**, diperiksa dengan tangan: akhir + 1 = awal berikutnya.
- [ ] Tiap konstanta di §6 ditulis sebagai **rumus**, bukan hasil.
- [ ] Aturan pembulatan tertulis, dan **bukan** cuma "ROUND".
- [ ] Seluruh baris §7 terjawab, termasuk **komponen yang tidak terukur**.
- [ ] §8 menyebut **jumlah total** potongan teks, per bahasa, beserta penulisnya.
- [ ] §9 berisi **satu contoh terhitung penuh** yang angkanya bisa ditelusuri sampai keluaran.
- [ ] Tidak ada kalimat berbentuk **"memenuhi kondisi yang ditentukan"** tanpa menyebut kondisinya.
- [ ] Setiap percabangan menyebut **urutan evaluasinya**.
- [ ] Dokumennya punya **versi, tanggal, pemilik, dan status**.

---

## Sembilan perbaikan, ringkas

Kalau sebuah spesifikasi keluaran sudah ada dan tinggal disempurnakan, sembilan pola ini yang biasanya kurang.

| # | Biasanya tertulis | Sebaiknya | Yang dicegah |
| --- | --- | --- | --- |
| 1 | Angka didaftarkan sebagai input, formulanya diberikan juga | Satu pemilik, disebut di kolomnya | Dua implementasi yang menyimpang |
| 2 | Rentang dimulai dari nilai terendah yang pernah terlihat | Lantai, atap, dan baris di luar rentang | Nilai tanpa kategori di produksi |
| 3 | Pembagi ditulis `10` | Ditulis `komponen × bobot maksimum` | Salah diam-diam saat komponennya bertambah |
| 4 | "ROUND(x,0)" | "setengah dibulatkan ke atas" | Dua bahasa, dua angka |
| 5 | Kasus batas hanya untuk daftar kosong | Termasuk null, di luar rentang, tidak terukur | Agregat yang salah tanpa gejala |
| 6 | "memenuhi kondisi yang ditentukan" | Kondisinya, atau `Q-` kalau belum tahu | Placeholder yang lolos review |
| 7 | "narasi diambil dari library" | Tabel inventaris dengan rumus jumlahnya | Jalur kritis yang tidak terlihat |
| 8 | Contoh sepotong-sepotong | Satu contoh terhitung penuh | Salah paham yang tidak terbukti |
| 9 | Tanpa versi dan pemilik | Kepala dokumen terisi | "Ikut spek" yang tidak bisa dirujuk |

Yang **sudah** biasanya benar dan jangan dihilangkan saat merapikan: menyebut batas dokumen (logika, bukan
desain), urutan section yang mengikat, larangan menyamakan nomor halaman dengan logika, larangan mem-hardcode
nilai contoh dari mockup, dan pernyataan bahwa data identitas tidak pernah memengaruhi perhitungan.
