# Medallion Architecture Data Pipeline - Analisis Big Data

![Airflow](https://img.shields.io/badge/Apache_Airflow-017CEE?style=for-the-badge&logo=apache-airflow&logoColor=white) ![Spark](https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white) ![Postgres](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white) ![Superset](https://img.shields.io/badge/Apache_Superset-1BAE9F?style=for-the-badge&logo=apache-superset&logoColor=white) ![MinIO](https://img.shields.io/badge/MinIO-C7202C?style=for-the-badge&logo=minio&logoColor=white)

Proyek ini adalah implementasi tugas besar Analisis Big Data yang menggunakan pendekatan **Medallion Architecture** (Bronze -> Silver -> Gold). Data source berupa file berukuran besar dari Rapor Pendidikan Indonesia tahun 2025.

## 🚀 Fitur Utama
1. **Containerized Pipeline**: Seluruh infrastruktur (Airflow, Spark Cluster, MinIO, PostgreSQL, Superset) berjalan dalam Docker Compose yang terorkestrasi.
2. **Automated ETL**: DAG Airflow (`tubes_abd_medallion_pipeline`) secara otomatis menjalankan ekstraksi, pembersihan, dan agregasi data secara end-to-end.
3. **Data Lake**: MinIO (S3-compatible) digunakan sebagai penyimpanan Object Storage untuk layer Bronze, Silver, dan Gold.
4. **Data Warehouse**: Hasil analitik layer Gold disinkronisasi ke PostgreSQL agar kompatibel dengan BI Tools.
5. **Dashboarding**: Apache Superset digunakan untuk memvisualisasikan data interaktif.

## 🏗️ Arsitektur Medallion

### 1. Bronze Layer (Raw & Flattened)
- **Proses**: Ekstraksi 39 sheet provinsi dari XLSX (21MB), membuang metadata yang tidak relevan, menormalisasi Multi-level Header, dan mengubah bentuknya dari *Wide Format* ke *Long Format*.
- **Tools**: Pandas / Airflow Worker.
- **Output**: `combined_data.csv` (100MB+) di bucket `bronze`.

### 2. Silver Layer (Cleaned & Standardized)
- **Proses**: Pembersihan string (lowercase, hapus spasi berlebih), imputasi *missing values* menjadi "tidak diketahui", validasi kolom esensial, penghapusan duplikasi.
- **Tools**: PySpark (Distributed).
- **Output**: File `Parquet` berpartisi berdasarkan `provinsi` di bucket `silver`.

### 3. Gold Layer (Aggregated Analytics)
- **Proses**: Pembuatan 3 tabel agrerat sesuai kebutuhan *business logic* Dashboard:
  - `gold_distribusi_label`
  - `gold_ketimpangan_wilayah`
  - `gold_performa_wilayah`
- **Tools**: PySpark (Distributed).
- **Output**: File `Parquet` di bucket `gold` dan tersinkronisasi ke PostgreSQL Database (`airflow` schema).

## 🖥️ Cara Menjalankan Project
1. Pastikan Docker Desktop berjalan.
2. Jalankan `JALANKAN_DOCKER.bat` (atau `docker compose up -d`).
3. Tunggu hingga semua services `healthy`.
4. Buka Airflow Web UI di `http://localhost:8081` (admin/admin).
5. Trigger DAG `tubes_abd_medallion_pipeline`.
6. Buka Superset di `http://localhost:8088` (admin/admin) untuk merakit visualisasi menggunakan koneksi Database `postgresql+psycopg2://airflow:airflow@postgres/airflow`.

## 📂 Struktur Repositori
- `/dags`: Definisi DAG Airflow
- `/jobs`: Source code ETL (Python dan PySpark)
- `/data`: Direktori input raw file (Excel)
- `docker-compose.yml`: Konfigurasi layanan docker
- `Dockerfile`: Image custom Airflow dengan instalasi Spark & Boto3.
