# Prediksi Total Bayar Penjualan dengan Pandas dan Scikit-learn

Proyek belajar machine learning (regresi) untuk memprediksi nilai `total_bayar` dari data transaksi penjualan toko online fiktif. Alur yang dipraktikkan: eksplorasi data dengan **pandas**, visualisasi dengan **matplotlib**, dan pemodelan dengan **scikit-learn**.

## Tujuan

- Belajar alur kerja ML dari awal sampai akhir: persiapan data, encoding, pembagian data latih/uji, pelatihan model, dan evaluasi.
- Membandingkan model **Linear Regression** dan **Random Forest Regressor**.
- Memahami pengaruh *feature engineering* terhadap performa model.

## Dataset

File: `data/data_penjualan.xlsx` (data sintetis, dibuat secara acak untuk keperluan belajar).

**Sheet `Penjualan`** (300 baris)

| Kolom | Keterangan |
|---|---|
| id_transaksi | ID unik transaksi |
| tanggal | Tanggal transaksi (tahun 2025) |
| id_pelanggan | Kunci ke sheet Pelanggan |
| produk, kategori | Produk dan kategorinya (Elektronik, Fashion, Makanan, Rumah Tangga) |
| harga_satuan, jumlah, diskon | Harga, jumlah beli, dan diskon |
| sales | Nama sales |
| metode_bayar | Transfer Bank, E-Wallet, Kartu Kredit, COD |
| rating | Rating 1 sampai 5 |
| total_bayar | Target prediksi: harga × jumlah × (1 − diskon) |

**Sheet `Pelanggan`** (25 baris): `id_pelanggan`, `nama_pelanggan`, `kota`, `umur`, `jenis_kelamin`.

Data sengaja memuat nilai kosong (12 pada `rating`, 6 pada `metode_bayar`) untuk latihan penanganan *missing values*.

## Langkah Pengerjaan

1. Membaca kedua sheet dan menggabungkannya dengan `merge` pada `id_pelanggan`.
2. Membuat fitur baru: `bulan` (dari `tanggal`) dan `harga_x_jumlah`.
3. Mengisi nilai kosong pada kolom kategori dengan label "Tidak diketahui".
4. Mengubah kolom teks menjadi angka dengan `OneHotEncoder`.
5. Membagi data: 80% latih, 20% uji (`random_state=42`).
6. Melatih `LinearRegression` dan `RandomForestRegressor` memakai `Pipeline`.
7. Evaluasi dengan MAE dan R², lalu visualisasi prediksi vs nilai asli dan fitur paling berpengaruh.

Kolom `rating` tidak dipakai sebagai fitur karena baru tersedia setelah transaksi selesai (menghindari *data leakage*). Kolom `harga_satuan` awalnya tidak dipakai karena sudah ditentukan oleh produk.

## Hasil

| Model | Fitur | MAE | R² |
|---|---|---|---|
| Linear Regression | Tanpa `harga_x_jumlah` | sekitar Rp 948 ribu | 0,845 |
| Random Forest | Tanpa `harga_x_jumlah` | sekitar Rp 187 ribu | 0,992 |
| Linear Regression | Dengan `harga_x_jumlah` | Rp 167 ribu | 0,993 |
| Random Forest | Dengan `harga_x_jumlah` | Rp 137 ribu | 0,992 |

Pada fitur terakhir, Random Forest memiliki kesalahan relatif rata-rata sekitar 3,7%, dan sekitar 90% prediksinya meleset tidak lebih dari 10%.

### Temuan utama

- Menambahkan fitur `harga_x_jumlah` membuat Linear Regression naik drastis, karena model linear tidak dapat membentuk perkalian antar fitur dengan sendirinya.
- Metrik rata-rata bisa menyembunyikan kesalahan pada kasus tertentu. Prediksi satu transaksi uji (Smartphone, jumlah 2, diskon 10%) meleset sekitar 11% pada Random Forest, meski R² keseluruhan tinggi.
- Fitur yang paling berpengaruh pada Random Forest: produk (terutama Laptop), kategori Elektronik, dan jumlah.

## Keterbatasan

- Data bersifat **sintetis** dan `total_bayar` dihitung langsung dari rumus, sehingga skor setinggi ini tidak mencerminkan kasus dunia nyata. Pada data nyata, R² mendekati 1 justru perlu dicurigai sebagai kebocoran data.
- Data uji hanya 60 baris, jadi hasil dapat bergeser jika `random_state` atau ukuran data diubah.
- Kolom seperti `kota`, `sales`, dan `metode_bayar` diacak, sehingga pengaruhnya pada hasil sebenarnya tidak ada.

## Cara Menjalankan

1. Clone repositori:

   ```bash
   git clone https://github.com/USERNAME/NAMA-REPO.git
   cd NAMA-REPO
   ```

2. Pasang library:

   ```bash
   pip install -r requirements.txt
   ```

3. Buka notebook di `notebooks/` dengan Jupyter Notebook, JupyterLab, atau Google Colab, lalu jalankan semua cell secara berurutan.

## Struktur Repositori

```
.
├── README.md
├── requirements.txt
├── data/
│   └── data_penjualan.xlsx
└── notebooks/
    └── prediksi_total_bayar.ipynb
```

## Teknologi

Python, pandas, matplotlib, scikit-learn, openpyxl.

## Rencana Pengembangan

- Menambahkan fitur `potongan` (harga × jumlah × diskon) dan membandingkan hasilnya.
- Mencoba `GradientBoostingRegressor`.
- Tugas lanjutan yang lebih realistis: prediksi rating pelanggan dan segmentasi pelanggan dengan K-Means.
