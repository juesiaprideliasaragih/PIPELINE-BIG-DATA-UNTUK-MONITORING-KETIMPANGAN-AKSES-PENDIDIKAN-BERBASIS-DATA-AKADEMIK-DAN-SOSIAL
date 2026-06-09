# Panduan Setup Proyek Akhir — Analisis Big Data
> Apache Spark + Apache Airflow + MinIO + PostgreSQL + Apache Superset · Medallion Architecture · Docker

> **Catatan:** Proyek ini adalah Dashboard Pemantauan Ketimpangan Pendidikan Indonesia 2025. Seluruh layanan berjalan secara terisolasi menggunakan Docker Containers.

---

## Daftar Isi

1. [Prasyarat](#1-prasyarat)
2. [Struktur Folder](#2-struktur-folder)
3. [Arsitektur Pipeline (Medallion)](#3-arsitektur-pipeline-medallion)
4. [Cara Menjalankan Kontainer](#4-cara-menjalankan-kontainer)
5. [Mengakses Layanan Web UI](#5-mengakses-layanan-web-ui)
6. [Tugas Anggota Kelompok](#6-tugas-anggota-kelompok)

---

## 1. Prasyarat

Pastikan seluruh perangkat lunak berikut sudah terpasang sebelum memulai.

| Perangkat Lunak | Versi Minimum | Keterangan |
|---|---|---|
| Docker Desktop | 4.25 / Engine 24.0 | Wajib berjalan di *background* |
| Docker Compose | 2.20 | Terintegrasi dengan Docker Desktop |
| Git | 2.x | Untuk melakukan clone repositori |
| RAM Tersedia | 8 GB | Dibutuhkan untuk menjalankan 5 kontainer sekaligus |

---

## 2. Struktur Folder

Berikut struktur folder lengkap repositori yang digunakan dalam proyek ini:

```text
TUBES_ABD_FINAL_READYY/               ← root repositori
├── docker-compose.yml                ← definisi 5 service kontainer (MinIO, Spark, Airflow, Postgres, Superset)
├── Dockerfile                        ← image kustom untuk Apache Airflow
├── JALANKAN_DOCKER.bat               ← script jalan pintas Windows untuk start container
├── raw_data.csv                      ← dataset mentah awal (Kemdikbud)
├── superset_config.py                ← file konfigurasi keamanan Superset
├── superset.db.backup                ← file backup metadata dashboard Superset
├── dags/
│   └── medallion_pipeline_dag.py     ← file DAG Airflow untuk orkestrasi jadwal
└── jobs/
    ├── convert_xlsx_to_csv.py        ← skrip tahap Bronze (Ingestion)
    ├── phase5_silver.py              ← skrip tahap Silver (Cleansing)
    ├── phase6_gold.py                ← skrip tahap Gold (Aggregation)
    ├── inspect_xlsx.py               ← utilitas inspeksi data
    └── test_melt.py                  ← utilitas testing bentuk tabel
```

---

## 3. Arsitektur Pipeline (Medallion)

Sistem ini memproses data menggunakan **Medallion Architecture** (Bronze ➔ Silver ➔ Gold):

1. **Bronze (Raw):** Menyimpan salinan data mentah `raw_data.csv` ke dalam MinIO bucket.
2. **Silver (Cleansed):** Apache Spark melakukan standarisasi, mengubah tipe data, dan mengisi *missing values*. Data disimpan kembali ke MinIO.
3. **Gold (Aggregated):** Apache Spark melakukan *Group By* menjadi 3 tabel metrik siap pakai (Performa Wilayah, Distribusi Label, Ketimpangan). Data akhirnya dikirim ke PostgreSQL.

Seluruh proses di atas diatur jalurnya oleh **Apache Airflow** agar berjalan berurutan dan otomatis.

---

## 4. Cara Menjalankan Kontainer

### Opsi 1: Menggunakan Terminal (Direkomendasikan)
Buka Terminal / Command Prompt di dalam folder proyek ini, lalu jalankan:
```bash
docker compose up --build -d
```

### Opsi 2: Pengguna Windows (Jalan Pintas)
Cukup klik ganda (*double-click*) pada file `JALANKAN_DOCKER.bat`.

> **Catatan:** Tunggu sekitar 2-3 menit sampai seluruh kontainer berstatus *Healthy* di Docker Desktop sebelum mengakses UI.

---

## 5. Mengakses Layanan Web UI

Setelah semua mesin berjalan hijau (*Running*), Anda bisa membuka *browser* dan mengakses antarmuka berikut:

| Layanan | URL Akses | Username | Password |
|---|---|---|---|
| **Apache Superset** (Dashboard Final) | `http://localhost:8088` | `admin` | `admin` |
| **Apache Airflow** (Orkestrasi) | `http://localhost:8081` | `admin` | `admin` |
| **MinIO Console** (Data Lake) | `http://localhost:9001` | `admin` | `password123` |
| **Apache Spark** (Master UI) | `http://localhost:8080` | - | - |

---

## 6. Tugas Anggota Kelompok

Proyek ini dibangun secara kolaboratif oleh:
- **Lutfia** – *Infrastructure Engineer* (Setup Docker & Konfigurasi Lingkungan)
- **Refa** – *Data Engineer* (Tahap Ingestion Bronze & Cleansing Silver)
- **Feby** – *Data Engineer* (Tahap Agregasi Gold & Dokumentasi)
- **Jue** – *Analytics & Orchestration Engineer* (Airflow DAG & Metadata Superset)
