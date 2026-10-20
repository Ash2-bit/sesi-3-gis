# Deteksi "Blind Spot" Fasilitas Publik Berbasis Analisis Spasial (GIS)

## Deskripsi

Proyek ini merupakan studi kasus penerapan *Spatial Analytics* menggunakan Sistem Informasi Geografis (GIS) untuk mengidentifikasi area *blind spot* (titik buta) fasilitas pelayanan publik.
Proyek ini mengadaptasi prinsip pengolahan data geospasial tingkat lanjut yang membahas transformasi proyeksi peta, *geoprocessing* (pembuatan radius layanan), dan *spatial join* untuk mendeteksi kesenjangan infrastruktur secara objektif tanpa harus melakukan survei lapangan manual.

## Tujuan

Tujuan proyek ini adalah:
1. Memproses titik koordinat mentah (Garis Lintang/Bujur) menjadi data geospasial yang dapat dihitung secara matematis.
2. Membuat model poligon radius pelayanan berdasarkan jarak nyata (dalam satuan meter) menggunakan transformasi *Coordinate Reference System* (CRS).
3. Menganalisis sebaran titik permukiman warga untuk mendeteksi mana yang berada di dalam cakupan layanan dan mana yang berstatus *blind spot*.
4. Menghasilkan visualisasi peta analitik dan rasio keterjangkauan sebagai landasan pengambilan keputusan tata kota.

## Studi Kasus

Pemerintah daerah Kota Bengkulu berencana mengalokasikan dana APBD untuk membangun fasilitas kesehatan (Klinik/Puskesmas) baru. Namun, mereka tidak mengetahui secara pasti wilayah permukiman mana yang saat ini paling tidak terlayani.
Pendekatan visual murni (melihat peta secara kasat mata) sering kali memicu bias dan keputusan politik yang keliru. Oleh karena itu, proyek ini difungsikan sebagai *Decision Support System* berbasis data keruangan. Sistem akan mengalkulasi secara otomatis jarak geometris setiap blok permukiman terhadap fasilitas eksisting, guna merekomendasikan lokasi pembangunan infrastruktur baru secara presisi.

## Dataset

Dataset yang digunakan merupakan dataset geospasial sintetis yang dibuat menggunakan Python (`GeoPandas` dan `NumPy`).
Dataset terdiri dari dua komponen:
1.  **Data Fasilitas Eksisting:** 4 titik koordinat klinik utama yang tersebar di wilayah kota.
2.  **Data Titik Permukiman:** 300 titik sampel permukiman warga yang di- *generate* secara acak di dalam *bounding box* (batas wilayah) kota.

## Metode

Tahapan utama yang dilakukan:
1. *Spatial Data Generation* (Pembuatan koordinat sintetis).
2. *CRS Transformation* (Konversi format WGS84 / EPSG:4326 ke format Universal Transverse Mercator / EPSG:32748 agar perhitungan radius menggunakan satuan meter, bukan derajat bumi).
3. *Geoprocessing - Buffering* (Penciptaan poligon radius pelayanan sejauh 2 Kilometer dari setiap klinik).
4. *Spatial Intersection* (Pengecekan topologi matematis untuk melihat titik permukiman mana yang beririsan dengan poligon layanan).

## Evaluasi

Performa tata ruang dievaluasi menggunakan metrik pelayanan:
*   **Service Coverage Ratio (Rasio Cakupan Layanan):** Persentase permukiman yang berada di dalam radius aman (< 2 km).
*   **Blind Spot Count:** Jumlah klaster warga yang tidak terjangkau infrastruktur sama sekali.
Metrik ini jauh lebih bernilai dari sekadar *Accuracy* model, karena langsung menjawab permasalahan kesetaraan (*equity*) pelayanan publik.

## Teknologi

*   Python
*   Google Colab / Jupyter Notebook (`.ipynb`)
*   GeoPandas
*   Shapely
*   Matplotlib

## Cara Menjalankan

1. Proyek dapat dijalankan menggunakan Google Colab.
2. Buka file `spatial_blindspot_analysis.ipynb` menggunakan Google Colab.
3. Pastikan *library* geospasial terinstal (biasanya sudah bawaan di Colab, jika belum gunakan `!pip install geopandas shapely`).
4. Jalankan *notebook* dari awal hingga akhir.
5. Peta analisis spasial lengkap dengan *legend* (keterangan cakupan) akan digambar secara otomatis pada tahap akhir.

## Kesimpulan
Proyek ini mendemonstrasikan bahwa dalam perumusan kebijakan publik, penggunaan Machine Learning tabular saja tidak cukup jika masalah yang dihadapi bersifat keruangan.
Dengan menerapkan algoritma geospasial, kelemahan analisis tata ruang manual dapat dieliminasi. Peta luaran dari analisis ini tidak lagi sekadar menjadi ilustrasi visual, melainkan bertransformasi menjadi dokumen saintifik yang melegitimasi di mana tepatnya pemerintah harus memfokuskan pembangunan selanjutnya.

## Struktur Repository

```text
sesi-3-gis/
├── spatial_blindspot_analysis.ipynb
├── README.md
└── requirements.txt
