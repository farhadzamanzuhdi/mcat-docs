# Tool Component — Frontend

| | |
| --- | --- |
| **Rute** | `routes/_app/tool-component/index.tsx`, `folder/$folderId.tsx`, `$toolComponentId.tsx` |
| **Komponen** | `features/tool-component/components/` — `ExplorerLayout`, `StructureTree`, `ItemInspector`, `TypeLegend`, `ParticipantPage`, `McatItemDialog`, `FolderDialog`, `ToolComponentDialog` |
| **Domain** | `features/tool-component/domain/structure.ts`, `item-types/registry.ts` |

## 1. Layar

Tiga tingkat, tiga rute:

| Rute | Isinya |
| --- | --- |
| `/tool-component` | Kartu folder, dengan pencarian dan paginasi |
| `/tool-component/folder/:folderId` | Tool Component di dalam satu folder |
| `/tool-component/:toolComponentId` | Explorer: `StructureTree` di kiri, `ItemInspector` di kanan |

`StructureTree` menampilkan header dan footer terpisah dari blok pertanyaan. `TypeLegend` menyebut tipe item
apa saja yang ada di komponen itu beserta jumlahnya. `ParticipantPage` merender satu halaman seperti yang
dilihat peserta.

Item bertipe MCAT memakai `McatItemDialog`: satu dropdown paket, ringkasan isi paket yang dipilih, dan sakelar
acak urutan soal.

## 2. Keputusan antarmuka

**Satu registry, bukan satu `switch` per layar.** `item-types/registry.ts` memegang semua yang perlu diketahui
UI tentang satu tipe item: label, ikon, warna, apakah ia bisa dinilai, ringkasan satu barisnya, panel
pengaturannya, dan pratinjaunya. Pohon, inspector, legenda, dan pratinjau peserta membacanya. Menambah tipe
berarti menambah satu entri — empat layar mengikutinya tanpa disentuh.

**Urutan diambil dari data, bukan dari urutan baris.** `domain/structure.ts` mengurutkan blok, halaman, dan
item berdasarkan `sort_order` masing-masing sebelum apa pun dirender. Urutan yang dikembalikan database tidak
dijanjikan, dan urutan itu adalah urutan pengerjaan peserta.

**Header dan footer dipisah di tampilan walau tidak dipisah di data.** Keduanya blok biasa dengan tipe khusus;
menampilkannya berbaris bersama blok pertanyaan membuat pohonnya terbaca seolah peserta mengerjakan headernya.

**Tipe yang tidak dikenal tidak merusak layar.** Registry punya entri `UNKNOWN`; tipe yang belum punya
definisinya dirender sebagai node biasa tanpa pratinjau, bukan melempar galat.

**Item MCAT yang belum memilih paket ditandai di dua tempat** — inspector-nya dan node-nya di pohon. Satu
tempat saja berarti admin harus membuka tiap item untuk tahu mana yang belum diisi.

## 3. Catatan implementasi

Tipe respons dinamai mengikuti DTO Java-nya (`MasterToolComponentDto`, `ToolComponentFolderDto`) supaya mudah
dicocokkan saat membaca layanan lama. Model datanya dijelaskan di [backend.md](backend.md) §3.
