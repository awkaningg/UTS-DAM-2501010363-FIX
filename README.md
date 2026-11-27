# UTS Data Acquisition & Management: Data Museum DKI Jakarta

| | |
|---|---|
| **Nama** | Clementin Christianingsi Mune Harum |
| **NIM** | 2501010363 |
| **Prodi / Kelas** | Informatika / F |
| **Institusi** | Institut Bisnis dan Teknologi Indonesia (INSTIKI) |
| **Mata kuliah** | Data Acquisition & Management |
| **Dosen** | Dr. I Gede Totok Suryawan, S.Kom., M.T. |
| **Ujian** | UTS Ganjil, Jumat 27 November 2026 |

## 1. Tujuan

Mengumpulkan dan menggabungkan data sektor **budaya (museum)** di DKI Jakarta dengan tiga cara akuisisi (berkas, API, web scraping), dengan memperhatikan etika data, provenance, dan kepatuhan `robots.txt`.

Pertanyaan yang dijawab: seberapa besar koleksi museum di tiap wilayah DKI, bagaimana cuaca wilayah itu, dan bagaimana Museum Nasional mengomunikasikan kegiatannya secara daring.

## 2. Sumber Data

| Kode | Cara | Sumber | Berkas mentah (`data/raw`) |
|---|---|---|---|
| (a) | Unduh berkas | Data Koleksi Museum, Dinas Kebudayaan DKI Jakarta (Satu Data Jakarta / katalog data.go.id), format Excel, 7.513 baris x 5 kolom, tahun 2023-2024 | `dataset_koleksi_museum_dki.xlsx` |
| (b-1) | API | Open-Meteo Historical Weather (cuaca harian per kota administrasi) | `api_openmeteo_*.json` |
| (b-2) | API | WordPress REST API Museum Nasional Indonesia (metadata artikel) | `api_posts_page*.json`, `api_categories.json` |
| (c) | Scraping | Beranda www.museumnasional.or.id (BeautifulSoup) | `scraping_beranda_museumnasional.html`, `robots_museumnasional.txt` |

Tidak ada data dari Booking, Agoda, Traveloka, tiket.com, Airbnb, Tripadvisor, Google Maps, atau media sosial. Tidak ada API key atau token di notebook.

## 3. Penggabungan Data (Soal 5)

| | Kiri | Kanan | Kunci | `validate` | Hasil |
|---|---|---|---|---|---|
| A | Dataset koleksi (berkas) | Cuaca tahunan (API) | `kunci_wilayah` + `tahun_kunci` | `many_to_one` | `data/processed/hasil_gabungan_koleksi_cuaca.csv` |
| B | Artikel beranda (scraping) | Metadata artikel (API) | `post_id` | `one_to_one` | `data/processed/hasil_gabungan_artikel_api_scraping.csv` |

Bukti tanpa duplikasi: jumlah baris hasil sama dengan tabel kiri, dan tidak ada baris duplikat baru (dicek dengan `assert`). Metadata ada di `docs/metadata_gabungan.json`.

## 4. Struktur Folder

```
.
├── UTS_DAM_2501010363_DKI_FIXED.ipynb   # notebook utama (semua soal)
├── README.md
├── data/
│   ├── raw/                  # berkas mentah, tidak diubah (dicek MD5)
│   └── processed/            # tabel bersih, ringkasan, hasil gabungan
└── docs/
    ├── provenance.csv        # URL, tanggal akses, lisensi, ukuran, MD5
    ├── data_ethics_checklist.csv / .md   # checklist 9 butir
    ├── bukti_robots_txt.csv  # bukti kepatuhan robots.txt
    ├── metadata_gabungan.json
    ├── rekap_pemeriksaan_akhir.csv
    └── charts/               # 8 grafik PNG
```

## 5. Cara Menjalankan (Google Colab)

1. Buka notebook di Google Colab.
2. Pastikan `EMAIL_KAMPUS` (sel 1) berisi email kampus sendiri.
3. Cek `TANGGAL_UNDUH` dan URL halaman dataset di sel 3.
4. Pilih **Runtime > Run all**.
5. Saat diminta, unggah berkas dataset `.xlsx` hasil unduhan (tanpa diedit).
6. Di akhir, ZIP `hasil_UTS_DAM_2501010363.zip` terunduh otomatis.

Pustaka: `pandas`, `requests`, `beautifulsoup4`, `lxml`, `openpyxl`, `matplotlib` (umumnya sudah ada di Colab; sel pertama memasang yang kurang).

## 6. Kepatuhan Aturan Request

- User-Agent berisi email kampus sendiri
- Timeout 30 detik di setiap request
- Jeda 2 detik antar-request
- Retry dengan backoff untuk status 429/5xx dan error jaringan
- `robots.txt` diperiksa dan disimpan sebagai bukti sebelum scraping dan pemanggilan API

## 7. Lisensi dan Etika

| Sumber | Ketentuan |
|---|---|
| (a) Dataset koleksi | Tanpa lisensi eksplisit di katalog; portal menyatakan data sebagai domain publik (UU 14/2008). Dipakai akademik dengan atribusi. |
| (b-1) Open-Meteo | CC BY 4.0, non-komersial, atribusi open-meteo.com |
| (b-2), (c) Museum Nasional | Hak cipta lembaga; hanya metadata yang disimpan (tanpa isi artikel dan ID penulis) |

Tidak ada PII orang hidup. Kolom `nama_koleksi` pada museum Prasasti dapat memuat nama tokoh sejarah yang telah wafat; data hanya dipakai pada tingkat agregat. Detail ada di `docs/data_ethics_checklist.md`.

## 8. Keterbatasan

- Dataset hanya museum kelolaan Pemprov DKI, bukan seluruh museum di Jakarta.
- Kolom `jumlah` bertipe teks di sumber, sehingga diparse ke angka (teks asli tetap di `jumlah_teks`).
- Cuaca ERA5 berupa grid model, bukan stasiun fisik.
- Artikel API hanya sampel terbaru; beranda hanya artikel pilihan redaksi.
- Hubungan koleksi dan cuaca (Grafik 6) bersifat ilustratif, bukan sebab-akibat.

## 9. Retensi Data

Disimpan di Colab/GitHub sampai nilai akhir semester keluar, lalu `data/raw` dan `data/processed` dihapus. Provenance dan checklist boleh disimpan sebagai dokumentasi.
