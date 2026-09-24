# Access Control

| | |
| --- | --- |
| **Menu** | Access Control |
| **Rute** | — (node grup) |

> **Kerangka.** Ketiga layar di bawah ada di `LOCAL_MENUS` tapi belum didokumentasikan. Tiap layar nanti dapat
> foldernya sendiri berisi empat berkas, mengikuti [TEMPLATE.md](../TEMPLATE.md), dan slug-nya didaftarkan di
> [`docs/README.md`](../../README.md) sebelum ID pertama ditulis.

## Anak menu

| Menu | Rute | Keadaan |
| --- | --- | --- |
| Menu Management | `/menu-management` | Belum didokumentasikan |
| Role Management | `/role-management` | Belum didokumentasikan |
| Manage User Role | `/manage-user-role` | Belum didokumentasikan |

Kodenya ada di `apps/api/src/modules/access-control/` dan `apps/cms/src/features/access-control/`. Satu hal
yang sudah pasti dan perlu masuk dokumen nanti: penjaga akses mencocokkan **awalan path**, jadi halaman yang
tidak berada di bawah url menunya tidak akan terbuka.
