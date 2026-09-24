# Keluaran

Apa pun yang **dihasilkan** sistem dan dibaca di luar layar: laporan PDF, ekspor CSV, dokumen terbit,
sertifikat, payload webhook.

## Kenapa foldernya di sini, bukan di bawah menu

Sisa `docs/product/` mencerminkan sidebar CMS: ada di `LOCAL_MENUS` berarti dapat folder. Aturan itu menjawab
"dokumen layar ini taruh mana" tanpa perlu berpikir.

Keluaran bukan layar. Ia tidak punya entri menu, tidak punya rute, dan tidak punya pengguna yang bisa bertanya
saat bingung — jadi cermin menu tidak punya slot untuknya.

Karena itu `docs/product/` punya tepat **tiga** hal di luar cermin menu, dan tidak boleh ada yang keempat:

```
docs/product/
├── README.md        ← Product PRD          di atas cermin        (belum ada)
├── ROADMAP.md       ← Product Roadmap      di atas cermin        (belum ada)
├── reports/         ← keluaran, bukan layar                      berkas ini
└── <menu>/…         ← mulai sini, folder = node menu
```

## Bentuk satu keluaran

```
reports/<nama-keluaran>/
├── README.md               logika isinya — §1–§10
├── data-model.md           dari mana tiap angka datang, kalau rumit
└── acceptance-criteria.md  AC- yang terikat nama tes
```

Aturan menulisnya ada di **[TEMPLATE-OUTPUT.md](../TEMPLATE-OUTPUT.md)**, terpisah dari
[TEMPLATE.md](../TEMPLATE.md) yang dipakai layar. Bedanya satu kalimat: layar yang bingung menampilkan pesan,
PDF yang bingung tetap terbit — jadi aturan yang di layar boleh implisit, di keluaran harus tertulis.

Satu bagian ditelusuri penuh, dari prosa mentah sampai kode tes, ada di [EXAMPLE.md](EXAMPLE.md).

## Menambah keluaran baru

1. Daftarkan slug-nya di [`docs/README.md`](../../README.md) sebelum ID pertama ditulis.
2. Salin kerangka [TEMPLATE-OUTPUT.md](../TEMPLATE-OUTPUT.md).
3. Isi §12 daftar periksa sampai tidak ada lagi yang "tidak" sebelum diserahkan ke tim teknis.
4. `bun scripts/docs-check.ts`.

## Yang sudah ada

Belum ada. Spesifikasi keluaran yang datang dari PO dalam bentuk lain — `.docx`, Google Doc — dikonversi ke
bentuk ini sebelum dikerjakan, bukan dikerjakan langsung dari bentuk aslinya. Alasannya di §12 template:
dokumen asal biasanya lengkap di isi dan bolong di tepi, dan tepi itulah yang jadi bug.
