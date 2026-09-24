# Adaptive Setups — Backend

| | |
| --- | --- |
| **Modul** | `modules/mcat/native/settings.ts` (konfigurasi), `sessions.ts` (menjalankan), `apps/api/src/mcat/engine/` (algoritma) |
| **Tabel** | `master_mcat_setting`, `trans_mcat_session*` — lihat [data-model.md](../data-model.md) |

## 1. Endpoint

| Method | Path | Catatan |
| --- | --- | --- |
| GET POST | `/mcat/settings` | `question_set_ids` wajib, minimal satu |
| GET PATCH | `/mcat/settings/:settingsId` | |
| POST | `/mcat/sessions` | Mulai sesi, untuk pratinjau maupun peserta |
| GET | `/mcat/sessions/:id/next-item` | Soal yang belum dijawab diberikan lagi, bukan diganti |
| POST | `/mcat/sessions/:id/respond` | Jawaban mentah; server yang menilainya |
| POST | `/mcat/sessions/:id/terminate` | |
| GET | `/mcat/sessions/:id/result` | |
| POST | `/mcat/playground/run` | Simulasi; tidak menulis apa pun |

Pilihan yang diterima tiap parameter algoritma:

| Parameter | Nilai |
| --- | --- |
| `selection_method` | `d_optimal`, `a_optimal`, `kl_information` |
| `estimation_method` | `mle`, `map`, `eap` |
| `stopping_rule` | `fixed_length`, `se_threshold`, `convergence`, `hybrid` |
| `exposure_method` | `none`, `sympson_hetter` |
| `bank_exhausted_action` | `error`, `force_stop`, `extend` |

## 2. Aturan yang ditegakkan di sini

| Aturan | Perilaku |
| --- | --- |
| BR-ADP-01, BR-ADP-02 | `checkQuestionSets()`; kosong atau berisi kumpulan Sesi 1 → 400 |
| BR-ADP-03, BR-ADP-04 | `checkDimensions()` terhadap `poolDimensions()` → 400 |
| BR-ADP-05, BR-ADP-06 | `checkConsistency()` → 400 |
| BR-ADP-07 — kolam terkunci | Mengubah `question_set_ids` saat sudah ada sesi → 409 |
| BR-ADP-08 — soal di luar cakupan | `candidates()` menyaringnya; soalnya tidak pernah masuk kandidat |

## 3. Catatan implementasi

**Kolam disebut sendiri oleh setup.** Sejak tidak ada wadah di atas Question Set, `itemsOfPool()` menyatukan
kolam kumpulan-kumpulan yang disebut `question_set_ids`, tiap soal sekali. `candidates()` lalu menyaringnya:
hanya soal aktif yang **seluruh** kemampuannya termasuk yang diukur tes ini.

**Konsistensi yang tidak bisa dinyatakan per field.** `checkConsistency()` memeriksa hal-hal yang hanya masuk
akal kalau dilihat bersamaan: panjang `prior_mean` dan `prior_cov_diag` harus sama dengan jumlah kemampuan yang
diukur, `n_min ≤ n_max`, dan — untuk estimasi `eap` — apakah jaringan kuadraturnya masih mungkin dihitung.

**Jawaban ganda.** `respond` mengunci barisnya di dalam transaksi (`WHERE response IS NULL`), jadi dua
permintaan bersamaan untuk soal yang sama hanya dihitung sekali: tidak menambah keterpaparan dua kali dan tidak
melompati nomor urut.

**Membaca soal yang sudah keluar.** Riwayat sesi memakai `itemsByIds()`, bukan kolam setup-nya — soal yang
sudah dikerjakan bisa saja sudah dikeluarkan dari kolam sesudahnya, dan riwayatnya tetap harus terbaca
(BR-ADP-10).
