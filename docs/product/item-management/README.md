# Item Management

Menu induk tempat konten tes disusun. Ia sendiri bukan halaman — di `LOCAL_MENUS` url-nya `#` — jadi isinya
hanya pengantar dan daftar anaknya.

| | |
| --- | --- |
| **Menu** | Item Management |
| **Rute** | — (node grup) |

## 1. Ringkasan

Dua anak, dan keduanya bertemu di satu titik. **MCAT** menyusun tes kemampuan kognitif sampai jadi sebuah
Package. **Tool Component** menyusun instrumen tes yang akhirnya sampai ke peserta, dan salah satu tipe item di
dalamnya adalah item MCAT — yang isinya tidak lain sebuah Package.

```
MCAT  ──►  Package  ──►  item bertipe MCAT  ──►  Tool Component  ──►  peserta
```

Arah bacanya satu: MCAT menghasilkan Package, Tool Component memakainya. Tidak ada arah sebaliknya — MCAT tidak
pernah tahu tes mana yang memakainya, kecuali saat menolak penghapusan.

## 2. Anak menu

| Menu | Isinya |
| --- | --- |
| [MCAT](mcat/README.md) | Tes kemampuan kognitif: dimensi, soal, kumpulan soal, algoritma adaptif, paket |
| [Tool Component](tool-component/README.md) | Instrumen tes: folder, blok, halaman, dan item-itemnya |
