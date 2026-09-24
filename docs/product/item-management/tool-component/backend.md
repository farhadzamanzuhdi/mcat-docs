# Tool Component — Backend

| | |
| --- | --- |
| **Modul** | `apps/api/src/modules/local/tool-component.ts`, `tool-component-mcat.ts` |
| **Tabel** | `master_tool_component`, `ref_tool_component_folder`, `ref_tool_component_type`; struktur sebagai JSON di `master_tool_component.structure` |

Bentuk payload-nya mengikuti DTO `engauge-services` (Java), supaya CMS bisa bicara ke layanan lama maupun ke
mode lokal tanpa tahu bedanya. Karena itu nama field-nya `camelCase` bergaya Java, bukan `snake_case` seperti
endpoint MCAT.

## 1. Endpoint

Semua di bawah `/admin/secured`.

| Method | Path | Catatan |
| --- | --- | --- |
| GET | `/tool-component-folder?search=&isPaging=` | Tanpa `isPaging` seluruh folder dikembalikan sekaligus |
| POST | `/tool-component-folder` | |
| GET PUT DELETE | `/tool-component-folder/:folderId` | Hapus ditolak kalau folder masih berisi Tool Component |
| GET | `/tool/component/find-all?search=&folderId=` | `folderId` opsional; tanpa itu mencari lintas folder |
| GET | `/tool/component/find-by-id/:id` | Beserta tipe dan bahasanya |
| GET | `/block/find-by-tool-component/:id` | Seluruh struktur: blok, halaman, item |
| GET | `/tool/component/type/lov` | Daftar tipe untuk dropdown |
| PUT | `/tool/component/delete/:id` | Hapus memakai `PUT`, mengikuti bentuk layanan lama |
| POST | `/tool/component/duplicate/:id` | |
| PUT | `/tool/component/move-folder` | |
| PUT | `/block/page/item/mcat/:itemId` | Item MCAT menyimpan `{ packageId, session1Shuffle }` |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-TLC-01 — nama folder unik | Nama yang sudah ada → 400 |
| BR-TLC-01 — folder masih berisi | Hapus folder yang masih berisi Tool Component → 409 |
| BR-TLC-05 — item MCAT wajib punya paket | `validateAssignment()`; item tanpa `packageId` → 400 |
| BR-TLC-06 — paket masih dipakai | Dijaga dari sisi Packages lewat `usageOf()` → 409 |

## 3. Catatan implementasi

**Struktur disimpan sebagai satu JSON, bukan enam tabel.** Sistem lama memecah blok, halaman, item, pilihan,
label, dan pernyataan ke tabel-tabel terpisah dengan primary key komposit. Mode lokal menyimpannya sebagai satu
kolom `structure` supaya satu Tool Component dibaca dan ditulis dalam satu operasi. Konsekuensinya: tidak ada
query yang bisa menanyakan "item bertipe X ada di mana saja" langsung ke SQL — yang membutuhkannya menjelajah
JSON-nya, seperti `usageOf()` di Packages.

**Pemasangan MCAT tidak punya tabel.** Ia satu field di dalam item block page itu. Bentuk lengkapnya ada di
[data-model MCAT §5](../mcat/data-model.md#5-di-luar-tabel-mcat).

**Header dan footer adalah blok biasa.** Keduanya dibuat otomatis saat Tool Component dibuat, dan dibedakan
lewat `block_type_id`. Data lama yang tidak punya kolom itu dikenali dari `block_sort_order` `1` dan `99`
(BR-TLC-02).
