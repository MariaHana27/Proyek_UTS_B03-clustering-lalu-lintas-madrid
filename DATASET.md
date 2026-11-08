# Dokumentasi Sumber Dataset

## Identitas Dataset

| Informasi | Keterangan |
| --- | --- |
| Nama dataset | Enriched Traffic Datasets for Madrid (Madrid Traffic Dataset), versi 4, subset |
| Penulis | Iván Gómez dan Sergio Ilarri (Universidad de Zaragoza) |
| Penerbit | Mendeley Data, Mei 2026 |
| Tautan | https://data.mendeley.com/datasets/697ht4f65b |
| DOI | 10.17632/697ht4f65b.4 |
| Artikel pendamping | Gómez, I., & Ilarri, S. (2025). IEEE Open Journal of Intelligent Transportation Systems, 6, 1539-1550. DOI 10.1109/OJITS.2025.3637305 |
| Lisensi | Creative Commons Attribution 4.0 (CC BY 4.0) |
| Sumber data asli | Madrid Open Data Portal (lalu lintas dan koordinat sensor) dan OpenStreetMap (atribut jalan) |

Dataset ini telah disetujui dosen untuk dipakai pada proyek.

## Subset yang Dipakai

- File: `MTD_complete_data.csv` dari folder **Subset dataset** (sekitar 790 MB). Folder Complete dataset (sekitar 10 GB) tidak dipakai.
- Cakupan: 300 sensor, 1 Juni 2022 sampai 31 Oktober 2023, per jam.
- Jumlah observasi: 3.699.308 baris dan 17 kolom.
- Unit observasi: satu sensor pada satu jam.
- Target: tidak ada (unsupervised).
- Missing value: 0. Duplikat: 0.

## Deskripsi Kolom

| Kolom | Tipe | Keterangan |
| --- | --- | --- |
| id | int64 | Kode sensor (300 sensor unik) |
| date | datetime | Waktu pengukuran per jam |
| longitude, latitude | float | Koordinat sensor |
| traffic_intensity | float | Jumlah kendaraan per interval (indikator kepadatan) |
| day_type | object | Working day, Sunday, Holiday, Saturday |
| wind, temperature, precipitation | float | Variabel cuaca |
| original_point, closest_point | object (WKT) | Titik asli sensor dan titik terdekat pada jaringan jalan |
| oneway | bool | Ruas satu arah atau bukan |
| lanes | float | Jumlah lajur |
| name | object | Nama ruas jalan (176 nama unik) |
| highway | object | Kelas jalan OpenStreetMap (11 kategori) |
| maxspeed | float | Batas kecepatan (km per jam) |
| length | float | Panjang ruas (meter) |

## Cara Mendapatkan Data

1. Buka https://data.mendeley.com/datasets/697ht4f65b.
2. Unduh file `MTD_complete_data.csv` pada folder **Subset dataset**.
3. Letakkan di Google Drive atau di folder proyek, lalu sesuaikan variabel `PATH` pada sel Setup dan Load Data di notebook.

Data **tidak disertakan** di repository ini karena ukurannya melebihi batas 100 MB GitHub.

## Catatan Kualitas Data

Sembilan temuan kualitas data beserta penanganannya dijelaskan pada Bagian B.4 laporan. Dua yang paling penting adalah label `day_type` untuk hari Sabtu yang tidak konsisten (94,6% hari Sabtu berlabel Working day) sehingga hari diturunkan dari kolom `date`, dan nilai cuaca yang tampak sudah diolah penulis dataset.

## Atribusi

Gómez, I., & Ilarri, S. (2026). *Enriched Traffic Datasets for Madrid* (Version 4) [Data set]. Mendeley Data. https://doi.org/10.17632/697ht4f65b.4
