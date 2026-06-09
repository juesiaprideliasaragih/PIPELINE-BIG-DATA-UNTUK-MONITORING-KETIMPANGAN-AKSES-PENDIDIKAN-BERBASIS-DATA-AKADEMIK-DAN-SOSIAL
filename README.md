# Dashboard Pemantauan Ketimpangan Pendidikan Indonesia 2025

## Deskripsi Project

Project ini merupakan implementasi End-to-End Big Data Pipeline menggunakan pendekatan Medallion Architecture (Bronze, Silver, Gold) untuk mengolah data Rapor Pendidikan Indonesia 2025.

Pipeline dibangun menggunakan:

- Apache Spark (PySpark)
- Apache Airflow
- MinIO Data Lake
- PostgreSQL Data Warehouse
- Apache Superset
- Docker

Tujuan utama project adalah menghasilkan dashboard analitik yang mampu memantau ketimpangan pendidikan antar wilayah di Indonesia secara otomatis dan terintegrasi.

---

## Arsitektur Sistem

Dataset XLSX
↓
Python ETL
↓
MinIO Bronze Layer
↓
PySpark Silver Layer
↓
PySpark Gold Layer
↓
PostgreSQL Data Warehouse
↓
Apache Superset Dashboard

---

## Teknologi yang Digunakan

| Komponen | Teknologi |
|-----------|------------|
| Data Lake | MinIO |
| Processing Engine | Apache Spark |
| ETL Language | PySpark |
| Orchestrator | Apache Airflow |
| Data Warehouse | PostgreSQL |
| BI Dashboard | Apache Superset |
| Containerization | Docker |

---

## Medallion Architecture

### Bronze Layer

Fungsi:

- Menyimpan data mentah
- Konversi XLSX menjadi CSV
- Penyimpanan awal ke MinIO

Output:

```text
s3://bronze/
```

### Silver Layer

Fungsi:

- Cleaning Data
- Standardisasi kolom
- Imputasi Missing Value
- Data Validation

Output:

```text
s3://silver/
```

### Gold Layer

Fungsi:

- Agregasi Analitik
- Perhitungan KPI Pendidikan
- Data Siap Dashboard

Output:

```text
s3://gold/
```

dan

```text
PostgreSQL
```

---

## Struktur Pipeline

### Phase 2 - Bronze

Script:

```text
convert_xlsx_to_csv.py
```

Tugas:

- Membaca file XLSX
- Flatten Multi Header
- Transform Wide to Long
- Upload ke MinIO

---

### Phase 5 - Silver

Script:

```text
phase5_silver.py
```

Tugas:

- Data Cleaning
- Standardisasi Kolom
- Missing Value Handling
- Simpan ke Silver Layer

---

### Phase 6 - Gold

Script:

```text
phase6_gold.py
```

Tugas:

- Distribusi Label Mutu
- Performa Wilayah
- Ketimpangan Wilayah
- Export ke PostgreSQL

---

### Phase 7 - Airflow

DAG:

```text
medallion_pipeline_dag.py
```

Dependency:

```text
Bronze
   ↓
Silver
   ↓
Gold
```

---

## Dashboard Analytics

Dashboard menampilkan:

1. Distribusi Label Mutu Pendidikan
2. Ranking Performa Provinsi
3. Ketimpangan Pendidikan Antar Wilayah
4. Jumlah Isu Kritis per Daerah
5. Tren Kualitas Pendidikan

---

## Menjalankan Project

### Clone Repository

```bash
git clone https://github.com/USERNAME/dashboard-pemantauan-ketimpangan-pendidikan-2025.git
```

### Menjalankan Docker

```bash
docker compose up -d
```

### Airflow

```text
http://localhost:8081
```

Username:

```text
admin
```

Password:

```text
admin
```

### Superset

```text
http://localhost:8088
```

Username:

```text
admin
```

Password:

```text
admin
```

### MinIO

```text
http://localhost:9001
```

Username:

```text
admin
```

Password:

```text
password123
```

---

## Data Lineage

```text
Rapor Pendidikan 2025
        ↓
Python ETL
        ↓
Bronze Layer (MinIO)
        ↓
Silver Layer (Cleaning)
        ↓
Gold Layer (Aggregation)
        ↓
PostgreSQL
        ↓
Superset Dashboard
```
