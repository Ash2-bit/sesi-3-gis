# Sesi 3: Sistem Informasi Geografis (GIS) & Spatial Analytics
**Automasi Deteksi "Blind Spot" Fasilitas Publik Berbasis Algoritma Spasial**

## 📖 Deskripsi Studi Kasus
Dalam perencanaan kota pintar (*Smart City*) atau mitigasi bencana, peta visual statis tidak lagi memadai. Pengambil kebijakan membutuhkan analisis spasial (*Spatial Analytics*) yang mampu menghitung secara matematis area mana saja yang belum terjangkau oleh fasilitas esensial—seperti klinik kesehatan, pos pemadam kebakaran, atau menara telekomunikasi. 

Studi kasus ini mendemonstrasikan automasi analisis *"Blind Spot"* (titik buta layanan). Menggunakan pustaka geospasial Python, sistem tidak hanya memetakan titik koordinat, tetapi secara otomatis melakukan proyeksi sistem referensi koordinat (CRS) ke dalam satuan meter, membangun poligon radius jangkauan layanan (*buffer*), dan mengidentifikasi titik permukiman mana yang berstatus rawan karena berada di luar cakupan fasilitas terdekat.

## 🎯 Tujuan Eksekusi
1. **Transformasi Proyeksi Koordinat (CRS):** Mengubah koordinat geografis (Derajat Lintang/Bujur - EPSG:4326) menjadi sistem proyeksi metrik (UTM - EPSG:32748) untuk akurasi perhitungan jarak nyata.
2. **Geoprocessing (Buffering):** Membangun poligon radius pelayanan sejauh 2 kilometer secara presisi mengelilingi setiap titik fasilitas.
3. **Analisis Topologi (Spatial Intersect/Contains):** Mengevaluasi titik-titik permukiman warga terhadap poligon jangkauan untuk mendeteksi status pelayanan (Terjangkau vs. Blind Spot).

## 💻 Implementasi Kode (Python - GeoPandas & Shapely)
Berikut adalah skrip analisis geospasialnya yang dapat dijalankan melalui file `spatial_blindspot_analysis.py`.

```python
import pandas as pd
import geopandas as gpd
from shapely.geometry import Point

print("=== AUTOMASI ANALISIS BLIND SPOT LAYANAN PUBLIK ===\n")

# 1. Menyiapkan Data Spasial: Fasilitas Publik (Misal: Klinik di Bengkulu)
data_klinik = {
    'nama_fasilitas': ['Klinik Sentral', 'Klinik Utara'],
    # Titik Koordinat Lintang/Bujur (Longitude, Latitude)
    'geometry': [Point(102.2600, -3.8000), Point(102.2700, -3.8400)]
}
gdf_klinik = gpd.GeoDataFrame(data_klinik, crs="EPSG:4326")

# 2. Menyiapkan Data Spasial: Permukiman Warga (Titik Uji)
data_warga = {
    'nama_wilayah': ['Desa Alpha', 'Desa Beta', 'Desa Gamma'],
    'geometry': [
        Point(102.2650, -3.8050), # Dekat dengan Klinik Sentral
        Point(102.2680, -3.8350), # Dekat dengan Klinik Utara
        Point(102.3000, -3.9000)  # Terlalu jauh (Simulasi Blind Spot)
    ]
}
gdf_warga = gpd.GeoDataFrame(data_warga, crs="EPSG:4326")

# 3. Transformasi Sistem Referensi Koordinat (CRS)
# Mengubah derajat Lintang/Bujur ke UTM Zone 48S (EPSG:32748) yang berbasis meter
gdf_klinik_meter = gdf_klinik.to_crs(epsg=32748)
gdf_warga_meter = gdf_warga.to_crs(epsg=32748)

# 4. Geoprocessing: Membuat Radius Pelayanan (Buffer 2 Kilometer / 2000 meter)
print("[PROSES] Menghitung radius cakupan layanan (2 Km) untuk setiap fasilitas...")
gdf_klinik_meter['area_layanan'] = gdf_klinik_meter.geometry.buffer(2000)

# Menyatukan (Dissolve/Unary Union) semua radius menjadi satu poligon cakupan gabungan
cakupan_total = gdf_klinik_meter['area_layanan'].unary_union

# 5. Analisis Spasial: Deteksi Blind Spot
print("[PROSES] Mengevaluasi status keterjangkauan permukiman...\n")
hasil_analisis = []

for index, baris in gdf_warga_meter.iterrows():
    titik_desa = baris['geometry']
    nama_desa = baris['nama_wilayah']
    
    # Cek apakah titik desa berada di dalam poligon cakupan_total
    if cakupan_total.contains(titik_desa):
        status = "✅ AMAN (Terjangkau Fasilitas)"
    else:
        # Menghitung jarak ke fasilitas terdekat jika di luar cakupan
        jarak_ke_fasilitas = gdf_klinik_meter.geometry.distance(titik_desa).min()
        status = f"❌ BLIND SPOT (Di luar radius! Jarak terdekat: {jarak_ke_fasilitas/1000:.2f} Km)"
        
    hasil_analisis.append({'Wilayah': nama_desa, 'Status Pelayanan': status})

# 6. Menampilkan Laporan Eksekutif
df_laporan = pd.DataFrame(hasil_analisis)
print("--- LAPORAN STATUS PELAYANAN WILAYAH ---")
for idx, row in df_laporan.iterrows():
    print(f"- {row['Wilayah']:<12}: {row['Status Pelayanan']}")
