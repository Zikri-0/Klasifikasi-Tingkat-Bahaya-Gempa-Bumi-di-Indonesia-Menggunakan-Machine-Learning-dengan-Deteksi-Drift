# Klasifikasi Tingkat Bahaya Gempa Bumi Indonesia dengan Stream Mining

Project ini membahas penerapan **stream mining** untuk mengklasifikasikan tingkat bahaya gempa bumi di Indonesia menggunakan **Hoeffding Tree** dan **Adaptive Random Forest (ARF)**.
Karena data gempa terus bertambah berdasarkan waktu, proses analisis dilakukan secara bertahap seperti aliran data. Selain klasifikasi, project ini juga menggunakan **ADWIN** untuk mendeteksi perubahan pola atau *concept drift* selama proses streaming berlangsung.

## Dataset
Dataset yang digunakan adalah **Earthquakes in Indonesia** dari Kaggle.
Data difokuskan pada periode:
**1 Januari 2015 – 30 September 2025**
Beberapa informasi gempa yang digunakan antara lain:
* Latitude
* Longitude
* Depth
* Phase Count
* Datetime
* Magnitude

Target klasifikasi dibuat berdasarkan nilai magnitudo gempa menjadi 4 kategori:

| Kategori | Magnitudo |
| -------- | --------- |
| Rendah   | < 3.0     |
| Sedang   | 3.0 – 4.9 |
| Tinggi   | 5.0 – 6.9 |
| Ekstrem  | ≥ 7.0     |

## Data Preprocessing

Sebelum digunakan untuk stream mining, data melalui beberapa tahap preprocessing:

* Filtering data berdasarkan periode waktu
* Pengecekan data duplikat
* Penanganan missing value
* Penanganan outlier
* Validasi domain koordinat
* Pengurutan data berdasarkan `datetime`
* Pembuatan label tingkat bahaya
* Encoding label menjadi nilai numerik

Data kemudian dipertahankan dalam urutan kronologis agar sesuai dengan konsep **data stream**.

## Stream Mining

Data diproses satu per satu menggunakan `generator`, sehingga model tidak langsung menggunakan seluruh dataset sekaligus.

Setiap data yang masuk akan melalui proses:

```text
Data Gempa
    ↓
Prediksi
    ↓
Evaluasi
    ↓
Deteksi Concept Drift
    ↓
Model Learning
    ↓
Data Berikutnya
```

Pendekatan ini digunakan untuk mensimulasikan kondisi ketika data gempa terus masuk secara real-time.

## Model yang Digunakan

### 1. Hoeffding Tree

Hoeffding Tree merupakan decision tree yang dirancang untuk data stream.

Pada project ini, model melakukan **incremental learning**, sehingga model dapat terus belajar ketika data baru masuk tanpa harus melakukan training ulang dari awal.

Model juga dipantau menggunakan **ADWIN** untuk melihat apakah terjadi perubahan pola pada data.

Hasil eksperimen pada notebook:

* Accuracy: **72,1%**
* Concept drift terdeteksi: **25 kali**
* Total nodes: **41**

### 2. Adaptive Random Forest

Model kedua menggunakan **Adaptive Random Forest (ARF)**, yaitu ensemble model yang terdiri dari beberapa decision tree dan dirancang untuk menangani data stream.

Konfigurasi yang digunakan:

```text
n_models = 20
max_features = 1.0
delta = 0.1
seed = 42
```

ARF juga menggunakan ADWIN untuk mendeteksi perubahan pola selama proses streaming.

Hasil eksperimen pada notebook:

* Accuracy: **81,83%**
* Concept drift terdeteksi: **7 kali**
* Jumlah decision tree: **20**

## Evaluasi Model

Performa model dianalisis menggunakan beberapa metrik:

* Accuracy
* Precision
* Recall
* Macro F1
* Error Rate
* Waktu komputasi
* Jumlah concept drift

Selain metrik numerik, hasil prediksi juga divisualisasikan untuk melihat distribusi kategori tingkat bahaya yang dihasilkan oleh masing-masing model.

## Analisis Concept Drift

Salah satu bagian utama project ini adalah penggunaan **ADWIN (Adaptive Windowing)**.

ADWIN digunakan untuk mendeteksi perubahan distribusi atau pola data selama stream berjalan. Setiap drift yang terdeteksi kemudian dicatat berdasarkan step dan waktu kejadian.

Project ini juga mencoba melihat hubungan antara waktu terdeteksinya drift dengan beberapa kejadian gempa besar, seperti:

* Gempa Pidie Jaya
* Gempa Lombok
* Gempa Palu
* Gempa Majene
* Gempa Cianjur

## Analisis Geospasial

Selain proses klasifikasi, project ini juga melakukan analisis berdasarkan lokasi gempa di Indonesia.

Wilayah dikelompokkan menjadi:

* Sumatera
* Jawa-Bali
* Kalimantan
* Sulawesi
* Maluku
* Papua

Analisis yang dilakukan meliputi jumlah kejadian gempa, distribusi tingkat bahaya, serta visualisasi persebaran gempa berdasarkan koordinat latitude dan longitude.

Project juga menampilkan **10 gempa dengan magnitudo terbesar** dalam dataset.

## Tech Stack

* Python
* Pandas
* NumPy
* Scikit-learn
* River
* Matplotlib
* Seaborn
* GeoPandas
* Cartopy
* Jupyter Notebook

## Project Structure

```text
.
├── katalog_gempa_v2.tsv
├── Source_Code_Kelompok_5.ipynb
└── README.md
```

## Fokus Project
Project ini menggabungkan beberapa konsep dalam satu analisis:
**Data Cleaning → EDA → Stream Mining → Hoeffding Tree → Adaptive Random Forest → Concept Drift Detection → Evaluasi Model → Analisis Geospasial**
Project ini juga menjadi latihan untuk memahami bagaimana model machine learning dapat bekerja pada data yang datang secara bertahap, bukan hanya pada dataset statis.

## Team
**Kelompok 5 — S1 Sains Data, Universitas Negeri Surabaya**
* Ayda' Nur Salzabillah
* Gresya Maranatha Sihaloho
* Muhammad Zikri Widiandra
