# Analisis Operasional Pengiriman JNE

Capstone Project Module 3 — Business & Data Analyst (JC BDA).
Analisis 300.000 pengiriman JNE periode Mei–Oktober 2023 dengan fokus pada SLA, RTS, dan kualitas data.

## Business Questions

1. Cabang atau sorting hub mana yang menjadi bottleneck keterlambatan?
2. Bagaimana perbandingan RTS antara COD dan Non-COD?
3. Seberapa besar estimasi kebocoran pendapatan akibat anomali berat paket?
4. Layanan mana yang paling tidak konsisten penulisannya?
5. Berapa banyak paket berstatus Delivered yang memiliki `delivered_date` sebelum `pickup_date`?
6. Berapa persentase data pengirim yang kosong dan menghambat cross-selling?

## Temuan Utama

**RTS COD lebih tinggi daripada Prepaid.** RTS COD 24,96% dan Prepaid 4,96% atau sekitar 5,03 kali lebih tinggi. Perbedaan tetap terlihat ketika data dibandingkan berdasarkan wilayah, layanan, dan tipe pelanggan. Temuan ini menunjukkan hubungan yang konsisten, bukan bukti sebab-akibat.

**Tidak ada cabang bottleneck yang terbukti.** Uji chi-square SLA miss antar cabang menghasilkan p-value 0,5191. Rentang SLA miss 63,48%–69,22% dengan simpangan baku 1,17 poin persentase. Perbaikan sebaiknya diarahkan pada proses yang berlaku di seluruh jaringan, bukan hanya cabang tertentu.

**Anomali berat menunjukkan masalah validasi input.** Terdapat 1.500 baris anomali yang hanya terdiri dari empat nilai berulang: -5,0; -1,5; 999,0; dan 9.999,0. Nilai rupiah tidak dihitung karena dataset tidak memiliki tarif.

**Kualitas data perlu diperbaiki.** Ditemukan 7.473 paket dengan timeline tidak valid dan 4.579 customer tanpa `customer_type`. Baris tetap dipertahankan, diberi penanda, atau diisi `Unknown` sesuai jenis masalahnya.

**Keterbatasan penting:** distribusi durasi pengiriman tampak merata, sehingga analisis durasi dan SLA perlu dibaca sebagai temuan kualitas data, bukan dasar utama keputusan operasional.

## Cara Menjalankan

```bash
pip install pandas numpy scipy matplotlib seaborn jupyter
jupyter notebook notebooks/02_analisis_jne.ipynb
```

Jalankan seluruh sel berurutan. Notebook membaca tiga file CSV dari `data/raw/`, menggabungkan data shipment, customer, dan branch, menulis dataset bersih ke `data/processed/`, serta menyimpan grafik ke `outputs/`.

## Struktur

```
├── data/raw/           Dataset mentah (3 CSV + studi kasus)
├── data/processed/     Dataset bersih (dihasilkan notebook)
├── notebooks/          02_analisis_jne.ipynb — analisis end-to-end
├── outputs/            Grafik hasil analisis dan file pendukung
├── docs/               Insight, issue tree, dan panduan cleaning
├── scripts/            Script pembantu dan referensi
└── input/              Materi modul dan brief capstone
```

## Metode

Cleaning: normalisasi `service_type` dari 9 varian menjadi 3 kategori, penandaan anomali berat, penandaan timeline tidak valid, imputasi median berat per layanan, dan pengisian `customer_type` kosong menjadi `Unknown`.

Statistik: statistik deskriptif, chi-square, dan Kruskal-Wallis. Uji statistik digunakan bersama perbandingan persentase dan konteks kualitas data.

## Dataset

| File | Ukuran | Isi |
| --- | --- | --- |
| `jne_shipments.csv` | 300.000 x 10 | Fakta pengiriman |
| `jne_customers.csv` | 30.000 x 3 | Data pengirim |
| `jne_branches.csv` | 150 x 3 | Data cabang |

Periode: 2023-05-01 sampai 2023-10-27.

Dataset bersih gabungan diekspor notebook ke `data/processed/jne_merged_clean.csv` dan memiliki satu baris per shipment.