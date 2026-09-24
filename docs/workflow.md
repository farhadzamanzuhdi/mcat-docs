# Dari kebutuhan produk ke kode yang terverifikasi

Bagaimana sebuah permintaan berubah jadi pekerjaan, dan di mana AI ikut membantu.

> **AI membantu prosesnya. Manusia yang memiliki keputusannya.**

Satu permintaan nyata ditelusuri dari awal sampai selesai — beserta templat Business Request dan Requirement
Analysis yang bisa dipakai ulang — ada di **[workflow-example.md](workflow-example.md)**.

---

## 1. Alur utama

```mermaid
flowchart TD
    subgraph PRODUCT["① PRODUCT — apa yang harus dicapai produk"]
        PRD["<b>Product PRD</b><br/>Dokumen berumur panjang.<br/>Tidak dibuat ulang tiap ada permintaan."]
        ROADMAP["<b>Product Roadmap</b><br/>Apa yang dikerjakan berikutnya,<br/>beserta urutannya."]
    end

    subgraph PLANNING["② PLANNING — dari mana pekerjaan datang"]
        REQ["<b>Business Request</b><br/>Permintaan baru dari PO<br/>atau dari klien.<br/>Datang sebagai <i>masalah</i>."]
        SPEC["<b>Spesifikasi</b><br/>Aturan yang sudah diputuskan<br/>bisnis, ditulis PO atau klien.<br/>Datang sebagai <i>aturan</i>."]
        ANALYSIS["<b>Requirement Analysis</b><br/>BA membaca permintaan itu<br/>terhadap Product PRD."]
        EXISTS{"Kebutuhannya sudah ada<br/>di Product PRD?"}
        UPDATE["<b>Kebutuhan Produk<br/>baru / diperbarui</b><br/>PRD-nya ikut berubah."]
        REOPEN["<b>Kembali ke PO</b><br/>dengan ongkosnya.<br/>Kalau dibalik, entri DEC- dulu."]
    end

    subgraph FEATURE["③ FEATURE — apa yang harus dicapai perubahan ini"]
        EPIC["<b>Feature / Epic</b><br/>Satu perubahan bernama."]
        STORY["<b>User Story</b><br/>Pekerjaan yang dikerjakan."]
        AC["<b>Acceptance Criteria</b><br/>Perilaku yang dijanjikan,<br/>dalam bentuk yang bisa diuji."]
        DESIGN["<b>Technical Design</b><br/>Bagaimana sistem mendukungnya.<br/><i>Hanya kalau rumit.</i>"]
    end

    subgraph DEV["④ DEVELOPMENT — pekerjaannya"]
        FE["<b>Frontend</b>"]
        BE["<b>Backend</b>"]
    end

    subgraph QA["⑤ QA — apakah janjinya ditepati"]
        VERIFY["<b>QA Verification</b><br/>Diperiksa terhadap<br/>Acceptance Criteria."]
    end

    PRD --> ROADMAP
    ROADMAP -->|"pekerjaan terencana"| EPIC

    REQ --> ANALYSIS
    SPEC --> ANALYSIS
    ANALYSIS --> EXISTS
    EXISTS -->|"SUDAH ADA — bug, bukan fitur"| DEV
    EXISTS -->|"BELUM ADA"| UPDATE
    EXISTS -->|"SUDAH DIPUTUSKAN TIDAK"| REOPEN
    REOPEN -.->|"kalau PO membalik"| UPDATE
    UPDATE -->|"PRD diperbarui"| PRD
    UPDATE --> EPIC

    EPIC --> STORY
    STORY --> AC
    AC -->|"kalau perlu"| DESIGN
    AC -->|"kalau sederhana"| FE
    DESIGN --> FE
    DESIGN --> BE
    FE --> VERIFY
    BE --> VERIFY
    AC -.->|"jadi alat ukurnya"| VERIFY
```

**Tiga pintu masuk, satu jalur keluar.** Pekerjaan terencana masuk lewat Roadmap. Permintaan baru masuk lewat
Business Request. Aturan yang sudah diputuskan bisnis masuk lewat Spesifikasi. Begitu kebutuhannya tercatat,
ketiganya bertemu di jalur yang sama.

**Business Request dan Spesifikasi berbeda di bentuk, bukan di tujuan.** Business Request menyerahkan sebuah
masalah dan membiarkan solusinya dianalisis. Spesifikasi menyerahkan aturannya langsung — bobot, ambang batas,
urutan tampilan — karena keputusannya memang bukan milik tim pengembang. Keduanya tetap lewat Requirement
Analysis yang sama.

Satu aturan yang membedakan penanganannya: **spesifikasi berwenang atas aturan bisnisnya, tidak berwenang atas
kelengkapannya.** Angka dan ambang batas di dalamnya diterima apa adanya. Input yang tidak disebut sumbernya
tetap jadi pertanyaan terbuka, tidak ditebak.

**Kotak keputusan itu punya tiga keluaran, bukan dua.** Kebutuhan yang **sudah ada** tidak melahirkan Feature /
Epic sama sekali — kalau sistem tidak berperilaku seperti yang tertulis, itu bug, dan `AC-` yang sudah ada jadi
alat ukurnya. Yang **belum ada** memperbarui PRD lebih dulu. Yang **sudah diputuskan tidak** kembali ke PO
beserta ongkosnya, karena yang berubah prioritas, bukan kemampuan teknis. Ketiganya diuraikan di
[workflow-example.md](workflow-example.md) §Tahap 3.

**PRD tidak dibuat ulang.** Kebutuhan baru menambah isinya — satu dokumen yang tumbuh, bukan tumpukan dokumen
sekali pakai.

---

## 2. Tiga tingkat dokumentasi

```mermaid
flowchart LR
    subgraph L1["PRODUCT LEVEL"]
        direction TB
        A1["Product PRD"]
        A2["Product Roadmap"]
        A3["<i>Menentukan apa yang<br/>harus dicapai produk.</i>"]
    end

    subgraph L2["FEATURE LEVEL"]
        direction TB
        B1["Feature Requirement"]
        B2["Acceptance Criteria"]
        B3["System Design"]
        B4["Data Model / API<br/><i>kalau perlu</i>"]
        B5["<i>Menentukan apa yang harus dicapai<br/>satu perubahan, dan bagaimana<br/>sistem mendukungnya.</i>"]
    end

    subgraph L3["WORK LEVEL"]
        direction TB
        C1["User Story"]
        C2["Task"]
        C3["Bug"]
        C4["<i>Menentukan pekerjaan<br/>yang benar-benar dikerjakan.</i>"]
    end

    L1 --> L2 --> L3
```

---

## 3. Di mana AI ikut

```mermaid
flowchart TD
    AI(["<b>AI</b>"])

    subgraph BOLEH["AI BOLEH — membantu"]
        Y1["Menyusun draf"]
        Y2["Mengubah satu dokumen<br/>jadi bentuk lain"]
        Y3["Menemukan informasi<br/>yang hilang"]
        Y4["Menemukan hal<br/>yang ambigu"]
        Y5["Memeriksa konsistensi"]
        Y6["Mengusulkan kasus uji"]
        Y7["Menyusun rencana<br/>implementasi"]
    end

    subgraph TIDAK["AI TIDAK BOLEH — memutuskan"]
        N1["Mengarang kebutuhan"]
        N2["Mengambil keputusan bisnis"]
        N3["Menimpa kebutuhan<br/>yang sudah disetujui"]
    end

    AI --> BOLEH
    AI -.->|"dilarang"| TIDAK

    style TIDAK stroke-dasharray: 5 5
```

AI menyentuh **tiap tahap** di §1 — menyusun draf PRD, memecah Epic jadi Story, mengusulkan Acceptance
Criteria, menulis rencana implementasi, mengusulkan kasus uji QA. Yang tidak berubah di tahap mana pun:
persetujuan selalu tindakan manusia, dan informasi yang tidak ada ditandai `OPEN QUESTION:`, tidak ditebak.

---

## 4. Legenda

| Dokumen | Menjawab apa |
| --- | --- |
| **PRD** | Menentukan **produknya**. |
| **Feature** | Menentukan **perubahannya**. |
| **Story** | Menentukan **pekerjaannya**. |
| **Acceptance Criteria** | Menentukan **perilaku yang diharapkan**. |
| **Technical Design** | Menentukan **bagaimana sistem mendukungnya**. |
| **QA** | **Memverifikasi** perilakunya. |
| **Change Request** | Bukan dokumen. Business Request yang cabangnya **SUDAH ADA** atau **SUDAH DIPUTUSKAN TIDAK**. |

---

## 5. Bagaimana ini memetakan ke repo Engauge

Diagram di atas generik. Di repo ini, tiap kotak punya berkasnya sendiri:

| Kotak di diagram | Berkasnya di sini | Keadaan |
| --- | --- | --- |
| Product PRD | `docs/product/README.md` | **Belum ada** |
| Product Roadmap | `docs/product/ROADMAP.md` | **Belum ada** |
| Feature / Epic | `<layar>/README.md` §1–§3 — mis. [Dimensions](product/item-management/mcat/dimensions/README.md) | Ada |
| Acceptance Criteria | `<layar>/acceptance-criteria.md` | Ada, 304 ID |
| Technical Design | `<layar>/backend.md`, `<layar>/frontend.md`, dan `<area>/architecture.md` | Ada |
| Data Model | `<area>/data-model.md` | Ada |
| Keputusan + yang ditolak | `<area>/decisions.md` | Ada |
| Pertanyaan terbuka | `<layar>/README.md` §6, ber-ID `Q-` | Ada |
| QA Verification | Nama tes memuat ID AC-nya, diperiksa `bun scripts/docs-check.ts` | Ada, 56 AC terikat tes |
| Business Request / Spesifikasi | Issue tracker, berkas aslinya dilampirkan | Di luar repo |
| User Story / Task / Bug | Issue tracker | Di luar repo |

**Satu hal yang hilang, dan bukan kebetulan.** Struktur `docs/product/` mencerminkan sidebar CMS: ada di
`LOCAL_MENUS` berarti dapat folder. Aturan itu menjawab "dokumen layar ini taruh mana" tanpa perlu berpikir —
tapi **niat produk tidak punya entri menu**, jadi tidak ada tempat untuknya.

Karena itu Product PRD dan Roadmap adalah **satu-satunya dua berkas yang boleh berada di atas cermin menu**:

```
docs/product/
├── README.md      ← Product PRD      di luar cermin menu
├── ROADMAP.md     ← Product Roadmap  di luar cermin menu
├── TEMPLATE.md    ← aturan menulis
└── <menu>/…       ← mulai dari sini, folder = node menu
```

Batas itu perlu ditulis eksplisit di [`TEMPLATE.md`](product/TEMPLATE.md), supaya tidak ada berkas ketiga yang
ikut memanjat ke atas cermin menu dan mengembalikan pertanyaan "ini taruh mana".

Sebelum `docs/product/README.md` ada, dokumen paling tinggi yang menyatukan fitur adalah
[`product/item-management/README.md`](product/item-management/README.md) — dan itu satu grup menu, bukan
produk.
