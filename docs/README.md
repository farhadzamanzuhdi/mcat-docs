# Dokumentasi Engauge

Bagaimana sebuah permintaan berubah jadi kode yang terverifikasi ada di **[workflow.md](workflow.md)**
(diagramnya) dan **[workflow-example.md](workflow-example.md)** (satu contoh lengkap, dengan templatnya).
Berkas ini menjelaskan tempat tiap dokumen tinggal.

Satu folder, satu sumbu pemisah: **apakah tulisan ini masih benar besok?**

| Berkas | Isinya | Sifatnya |
| --- | --- | --- |
| `product/**/README.md`, `backend.md`, `frontend.md`, `acceptance-criteria.md` | Bagaimana sistem bekerja **sekarang** | Selalu diperbarui bersama kodenya. Tidak ada riwayat di dalamnya |
| `product/**/decisions.md` | **Kenapa** bentuknya begini, beserta yang ditolak | Bertanggal, hanya bertambah ke bawah, tidak pernah disunting ulang |

Keduanya bertetangga, di folder layar yang sama. Yang memisahkan bukan foldernya, melainkan nama berkasnya.

## Folder mengikuti menu

Struktur `product/` adalah **cermin dari sidebar CMS**. Sidebar itu sendiri didefinisikan di
[`apps/api/src/db/local/seed.ts`](../apps/api/src/db/local/seed.ts) sebagai `LOCAL_MENUS`.

```
Item Management  →  docs/product/item-management/
  MCAT           →  docs/product/item-management/mcat/
    Dimensions   →  docs/product/item-management/mcat/dimensions/
```

Aturannya satu kalimat: **ada di `LOCAL_MENUS` berarti dapat folder; tidak ada berarti tidak dapat folder.**
Sesuatu yang bukan item menu — bahasa konten, tata letak soal — ditulis di dalam layar yang memuatnya, bukan
diberi folder sendiri. Satu-satunya pengecualian adalah **alur** yang punya dialog atau wizard sendiri; itu
boleh jadi subfolder di bawah layarnya, seperti [`item-bank/import/`](product/item-management/mcat/item-bank/import/README.md).

Akibat yang dituju: tidak pernah ada lagi pertanyaan "berkas ini taruh di mana". Jawabannya selalu "di mana
menunya".

## Bentuk satu layar

```
<layar>/
├── README.md               ← dari sisi pengguna. Untuk PO, BA, siapa pun
├── backend.md              ← untuk engineer
├── frontend.md             ← untuk engineer
└── acceptance-criteria.md  ← untuk QA
```

Satu area (`mcat/`) menambahkan tiga berkas yang melintasi layar-layarnya: `README.md`, `data-model.md`,
`architecture.md`, dan satu `decisions.md`.

Aturan menulis lengkapnya ada di **[`product/TEMPLATE.md`](product/TEMPLATE.md)**. Salin dari sana.

## Yang bukan layar

Dua hal tidak punya entri menu, jadi tidak punya tempat di cermin menu, dan karena itu tinggal di luar
cermin — tepat dua, tidak boleh ada yang ketiga:

| | Tempatnya | Keadaan |
| --- | --- | --- |
| Product PRD dan Roadmap | `product/README.md`, `product/ROADMAP.md` | Belum ada — lihat [workflow.md §5](workflow.md) |
| Keluaran: laporan PDF, ekspor, dokumen terbit | [`product/reports/`](product/reports/README.md) | Siap dipakai |

Keluaran memakai templatnya sendiri, **[`product/TEMPLATE-OUTPUT.md`](product/TEMPLATE-OUTPUT.md)**, karena
layar yang bingung menampilkan pesan sedangkan PDF yang bingung tetap terbit. Satu bagian ditelusuri penuh dari
prosa mentah sampai kode tes ada di [`product/reports/EXAMPLE.md`](product/reports/EXAMPLE.md).

## Daftar slug ID

Tiap layar punya slug tetap. Slug itu yang dipakai menomori kebutuhan (`FR-`), aturan bisnis (`BR-`), kriteria
penerimaan (`AC-`), pertanyaan terbuka (`Q-`), dan keputusan (`DEC-`).

**Slug tidak pernah berubah dan tidak pernah didaur ulang.** Slug baru ditambahkan ke tabel ini sebelum
dipakai; `docs-check` menolak ID yang slug-nya tidak terdaftar di sini.

| Slug | Layar | Folder |
| --- | --- | --- |
| `MCAT` | Area MCAT, hal yang melintasi layar-layarnya | [`product/item-management/mcat/`](product/item-management/mcat/README.md) |
| `DIM` | Dimensions | [`product/item-management/mcat/dimensions/`](product/item-management/mcat/dimensions/README.md) |
| `ITM` | Item Bank | [`product/item-management/mcat/item-bank/`](product/item-management/mcat/item-bank/README.md) |
| `IMP` | Impor soal, alur di dalam Item Bank | [`product/item-management/mcat/item-bank/import/`](product/item-management/mcat/item-bank/import/README.md) |
| `SET` | Question Sets | [`product/item-management/mcat/question-sets/`](product/item-management/mcat/question-sets/README.md) |
| `ADP` | Adaptive Setups | [`product/item-management/mcat/adaptive-setups/`](product/item-management/mcat/adaptive-setups/README.md) |
| `PKG` | Packages | [`product/item-management/mcat/packages/`](product/item-management/mcat/packages/README.md) |
| `TLC` | Tool Component | [`product/item-management/tool-component/`](product/item-management/tool-component/README.md) |

Satu grep memberi seluruh jejak satu layar — kebutuhan, aturan, kriteria, pertanyaan, keputusan, dan tesnya:

```
grep -rn "DIM-" docs apps e2e
```

## Memeriksa docs

```
bun scripts/docs-check.ts
```

Enam pemeriksaan: ID kembar, AC tanpa induk, AC tercentang tanpa tes, menu tanpa folder, tautan relatif mati,
slug tak terdaftar. Jalankan sebelum commit yang menyentuh `docs/`.

## Area yang sudah ada

| Area | Keadaan |
| --- | --- |
| [Item Management → MCAT](product/item-management/mcat/README.md) | Lengkap, lima layar |
| [Item Management → Tool Component](product/item-management/tool-component/README.md) | Lengkap |
| [Dashboard](product/dashboard/README.md) | Kerangka |
| [Access Control](product/access-control/README.md) | Kerangka |
| [Master Data](product/master-data/README.md) | Kerangka |
