# Panduan Aliran NiFi: GetFile ke PutFile

## Prasyarat
- Apache NiFi versi 2.3.0 terinstal dan berjalan.
- Direktori input: `/home/nifi/nifi-2.3.0/input_files/`
- Direktori output: `/home/nifi/nifi-2.3.0/output_files/`
- File data: `cities.csv` (contoh unduhan via perintah `wget` di bawah).

## Langkah 1: Unduh File `cities.csv`
Jalankan perintah berikut di server NiFi untuk menempatkan `cities.csv` ke direktori input:

```bash
wget https://people.sc.fsu.edu/~jburkardt/data/csv/cities.csv   -P /home/nifi/nifi-2.3.0/input_files/
```

## Langkah 2: Konfigurasi Processor `GetFile`
1. Buka antarmuka web NiFi (misalnya `http://<host>:8080/nifi`).
2. Tambahkan Processor **GetFile**:
   - Klik tombol “+” dan pilih “GetFile”.
   - Klik ganda icon processor.
   - Di tab **Settings**, beri nama misalnya `GetFile Cities`.
   - Di tab **Properties**, atur:
     - **Input Directory**: `/home/nifi/nifi-2.3.0/input_files/`
     - **File Filter**: `[^\.].*`
     - **Keep Source File**: `true`
     - **Batch Size**: `10`
     - **Recurse Subdirectories**: `true`
   - Simpan konfigurasi.

## Langkah 3: Konfigurasi Processor `PutFile`
1. Tambahkan Processor **PutFile**:
   - Klik tombol “+” dan pilih “PutFile”.
   - Klik ganda icon processor.
   - Di tab **Settings**, beri nama misalnya `PutFile Cities`.
   - Di tab **Properties**, atur:
     - **Directory**: `/home/nifi/nifi-2.3.0/output_files/`
     - **Conflict Resolution Strategy**: `replace`
     - **Create Missing Directories**: `true`
   - Simpan konfigurasi.

## Langkah 4: Hubungkan Processor
1. Drag dari port keluar (`success`) `GetFile Cities` ke port masuk `PutFile Cities`.
2. Pastikan hubungan terbentuk dan statusnya **CONNECTED**.

## Langkah 5: Jalankan Aliran
- Klik `Start` pada masing‑masing processor atau pilih canvas dan tekan `Start`.
- NiFi akan secara otomatis memindahkan `cities.csv` dari direktori input ke direktori output.

## Troubleshooting
- **Hak Akses**: Pastikan user yang menjalankan NiFi memiliki izin baca/tulis pada direktori input dan output.
- **Log NiFi**: Periksa `logs/nifi-app.log` untuk pesan error.
- **Filter File**: Pastikan regex **File Filter** sesuai nama file.

---

*Dokumen ini dibuat oleh ChatGPT untuk membantu konfigurasi aliran sederhana NiFi.*