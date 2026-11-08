# B03 - Kepadatan Lalu Lintas Berdasarkan Waktu dan Lokasi

Pengelompokan kondisi lalu lintas kota Madrid dengan clustering klasik. Repository ini memuat artefak **UTS Milestone 1** mata kuliah Pembelajaran Mesin.

## Identitas Proyek

| Item | Keterangan |
| --- | --- |
| Project ID | B03 |
| Topik | Kepadatan Lalu Lintas Berdasarkan Waktu dan Lokasi |
| Nama kelompok | Kelompok Delapan (VIII) |
| Mata kuliah | Pembelajaran Mesin |
| Fokus metode | Clustering (unsupervised learning, classical machine learning) |
| Semester | Ganjil TA. 2026-2027 |

## Anggota Kelompok

| No | Nama | NIM |
| --- | --- | --- |
| 1 | Daniel Pangihutan Siregar | 231112256 |
| 2 | Arisanti Nurlaili | 231112294 |
| 3 | Maria Hana Gultom | 231112124 |

## Deskripsi Masalah

Pengelola lalu lintas perlu mengetahui kapan dan di ruas mana lalu lintas tinggi, sedang, atau rendah. Data sensor per jam berjumlah jutaan baris dan tidak memiliki label kepadatan, sehingga pelabelan manual tidak praktis. Proyek ini mengelompokkan kondisi lalu lintas dengan clustering untuk menjawab empat pertanyaan berikut.

1. Bagaimana distribusi intensitas lalu lintas pada sensor-sensor di Madrid?
2. Apakah kombinasi waktu dan lokasi membentuk kelompok kepadatan yang berbeda?
3. Berapa cluster yang terbentuk dan apa karakteristik tiap cluster?
4. Apakah cluster lebih ditentukan oleh waktu atau oleh lokasi?

## Dataset

Enriched Traffic Datasets for Madrid (versi 4, subset), Gómez dan Ilarri, Mendeley Data. 300 sensor, 3.699.308 observasi per jam, Juni 2022 sampai Oktober 2023. Lisensi CC BY 4.0. Rincian lengkap ada di [DATASET.md](DATASET.md).

Data tidak disertakan di repository karena berukuran sekitar 790 MB (batas GitHub 100 MB). Tautan: https://data.mendeley.com/datasets/697ht4f65b (DOI 10.17632/697ht4f65b.4).

## Metode Singkat

- Pembagian data **hold-out temporal**: latih 1 Juni 2022 sampai 30 Juni 2023, uji 1 Juli sampai 31 Oktober 2023.
- Preprocessing dengan `ColumnTransformer` (imputer median, StandardScaler, one-hot), tanpa kebocoran data.
- Feature engineering: `log_intensity`, `intensity_per_lane`, `rel_intensity`, `hour_sin`, `hour_cos`, `is_non_working`, `highway_group`, `is_rain`.
- Model utama K-Means, pembanding Gaussian Mixture, Agglomerative Ward, dan DBSCAN.
- Baseline B0 sampai B3 (label acak, aturan periode waktu, aturan kelas jalan, aturan gabungan).
- Metrik: Silhouette, Davies-Bouldin, Calinski-Harabasz, dan Adjusted Rand Index.

## Hasil Eksperimen Awal

| Model | Silhouette latih | Silhouette uji | Davies-Bouldin uji |
| --- | --- | --- | --- |
| B0 Label acak (K = 3) | -0,0019 | -0,0011 | 295,17 |
| B1 Aturan periode waktu | 0,0847 | 0,0826 | 2,7791 |
| B2 Aturan kelas jalan | 0,0801 | 0,0827 | 2,9935 |
| B3 Aturan jam sepi dan motorway atau trunk | 0,1528 | 0,1460 | 2,1137 |
| **K-Means K = 3** | **0,2175** | **0,2055** | **1,6502** |
| K-Means K = 4 | 0,1759 | 0,1714 | 1,8428 |
| Gaussian Mixture K = 3 | 0,0833 | 0,0838 | 2,6924 |
| Agglomerative Ward K = 3 | 0,2152 | 0,2040 | 1,6592 |

Tiga cluster yang terbentuk dari K-Means K = 3:

| Cluster | Proporsi | Tafsiran |
| --- | --- | --- |
| 0 | 40,4% | Lengang pada malam sampai dini hari |
| 1 | 53,3% | Aktif pada siang sampai sore di jalan umum |
| 2 | 6,3% | Arteri atau jalan tol panjang berkecepatan tinggi yang padat sepanjang hari |

Pengelompokan cukup reproduktif (ARI 0,854 sampai 0,958), tetapi strukturnya lemah (Silhouette sekitar 0,2), sehingga hasilnya dibaca sebagai kecenderungan dan bukan kelompok yang terpisah tegas. Cuaca dan PCA tidak memperbaiki hasil, sedangkan jam adalah fitur yang paling berpengaruh. Penjelasan lengkap ada pada laporan.

## Struktur Repository

```
.
├── README.md
├── DATASET.md                     Dokumentasi sumber dataset
├── requirements.txt
├── .gitignore
├── notebook/
│   └── Project_B03_UTS_Final_v3.ipynb
├── laporan/
│   └── Laporan_Milestone1_UTS_B03_Final.docx
├── hasil/                         hasil_eksperimen_awal.csv, ablasi_fitur.csv, data_experiment_log.csv
├── figs/                          13 gambar EDA dan hasil eksperimen
└── data/                          
```

Folder `figs` dan `hasil` berisi keluaran dari eksekusi notebook yang dipakai untuk menyusun laporan, sehingga angkanya sama dengan laporan. Menjalankan ulang notebook pada lingkungan atau sampel yang berbeda dapat menghasilkan angka yang sedikit berbeda.

## Petunjuk Penggunaan

Eksperimen dijalankan di Google Colab.

1. Unduh `MTD_complete_data.csv` dari folder **Subset dataset** di Mendeley Data (lihat [DATASET.md](DATASET.md)).
2. Unggah file ke Google Drive, lalu sesuaikan nilai `PATH` pada sel Setup dan Load Data di notebook.
3. Jika memori tidak cukup, buat sampel acak 1% (hasil akan berbeda dari data penuh), lalu arahkan `PATH` ke `sample.csv`.
   ```python
   import pandas as pd
   chunks = pd.read_csv('MTD_complete_data.csv', chunksize=500_000)
   pd.concat(c.sample(frac=0.01, random_state=42) for c in chunks).to_csv('sample.csv', index=False)
   ```
4. Buka `notebook/Project_B03_UTS_Final_v3.ipynb` lalu jalankan semua sel berurutan (Runtime, Run all). Memori sekitar 12 GB diperlukan untuk data penuh.
5. Keluaran tersimpan di folder `figs`, folder `data`, serta `hasil_eksperimen_awal.csv`, `ablasi_fitur.csv`, dan `data_experiment_log.csv`.

Untuk menjalankan di komputer lokal, pasang dependensi dengan `pip install -r requirements.txt`.

**Parameter tetap:** `SEED = 42`, `CUTOFF = 2023-07-01`, `N_MODEL = N_TEST = 100.000`.
**Versi:** Python 3, scikit-learn 1.6.1, pandas 2.2.3, numpy 2.4.6.

## Keterbatasan

Struktur cluster lemah, hold-out hanya satu pembagian waktu, seluruh metrik bersifat internal, dan Hierarchical serta DBSCAN hanya dijalankan pada sub-sampel. Rencana perbaikan sampai UAS ada pada Bagian I.3 laporan.

## Referensi

[1]	A. Ramadhan, F. Achmad, I. Zulkarnain, and M. Aritsugi, “Evaluation of K-Means, DBSCAN, and Hierarchical Clustering for Strategic Segmentation of Tourism SMEs in Rembang, Indonesia,” J. Tek. Inform., vol. 6, no. 3, pp. 1605–1630, Jul. 2025, doi: 10.52436/1.jutif.2025.6.3.4602.
[2]	I. Gómez and S. Ilarri, “Enriched Traffic Datasets for Madrid,” vol. 1, 2024, doi: 10.17632/697HT4F65B.1.
[3]	I. Gómez and S. Ilarri, “Advanced Prediction of Traffic at Different Temporal Scales Using Heterogeneous Data Sources,” IEEE Open J. Intell. Transp. Syst., vol. 6, pp. 1539–1550, 2025, doi: 10.1109/OJITS.2025.3637305.
[4]	T. Hermawan and E. Zuliarso, “Perbandingan Metode Recurrent Neural Network (RNN) dan Long Short-Term Memory (LSTM) untuk Prediksi Curah Hujan,” Build. Informatics, Technol. Sci., vol. 7, no. 2, pp. 1450–1463, Sep. 2025, doi: 10.47065/bits.v7i2.8099.
[5]	M. F. E. Yuri, F. S. Pasaribu, A. B. Subuh, M. H. Naibaho, and A. Piliang, “Evaluasi Performa Gaussian Mixture Model dan K-Means terhadap Ketidakseimbangan Data pada Clustering,” J. Ilmu Komput. dan Inform. | E-ISSN  3063-9026, vol. 2, no. 4 SE-Articles, pp. 311–316, Apr. 2026, [Online]. Available: https://jurnal.globalscients.com/index.php/jiki/article/view/1252


