# Master Data

| | |
| --- | --- |
| **Menu** | Master Data |
| **Rute** | — (node grup) |

> **Kerangka.** Ketiga layar di bawah ada di `LOCAL_MENUS` tapi belum didokumentasikan. Tiap layar nanti dapat
> foldernya sendiri berisi empat berkas, mengikuti [TEMPLATE.md](../TEMPLATE.md), dan slug-nya didaftarkan di
> [`docs/README.md`](../../README.md) sebelum ID pertama ditulis.

## Anak menu

| Menu | Rute | Keadaan |
| --- | --- | --- |
| Manage Company | `/manage-company` | Belum didokumentasikan |
| Manage Department | `/manage-department` | Belum didokumentasikan |
| Manage Position | `/manage-position` | Belum didokumentasikan |

Kodenya ada di `apps/api/src/modules/local/master-data.ts` dan rute CMS-nya di
`apps/cms/src/routes/_app/manage-*`. Departemen dan posisi bersarang di bawah perusahaan; hubungan itu yang
perlu digambar lebih dulu saat dokumennya ditulis.
