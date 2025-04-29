# Apache NiFi - FlowFile dan Attributes

Dokumen ini berisi langkah-langkah lengkap latihan Apache NiFi untuk memahami FlowFile dan menggunakan processor UpdateAttribute.

## Tujuan

- Memahami struktur FlowFile (Content dan Attributes)
- Menambahkan attributes baru menggunakan UpdateAttribute
- Melihat hasil perubahan melalui LogAttribute

## Langkah-Langkah Praktik

### 1. Buat Processor GenerateFlowFile

- Tambahkan processor `GenerateFlowFile`
- Konfigurasi Properties:
  - Custom Text:
    ```json
    {"message": "Hello from NiFi!"}
    ```
  - Run Schedule: 5 sec (opsional)

### 2. Buat Processor UpdateAttribute

- Tambahkan processor `UpdateAttribute`
- Konfigurasi Properties:
  - Tambahkan Property baru:
    - Name: `flow_type`
    - Value: `learning`
  - Tambahkan Property baru:
    - Name: `project`
    - Value: `nifi_study`

### 3. Buat Processor LogAttribute

- Tambahkan processor `LogAttribute`
- Tidak perlu perubahan konfigurasi

### 4. Hubungkan Processor

- Buat koneksi dari `GenerateFlowFile` ke `UpdateAttribute`
- Buat koneksi dari `UpdateAttribute` ke `LogAttribute`
- Pilih Relationship: `success` saat membuat koneksi

### 5. Jalankan Flow

- Start semua processor
- Tunggu beberapa saat agar FlowFile mengalir

### 6. Cek Hasil di Provenance

- Klik kanan processor `LogAttribute`
- Pilih View Data Provenance
- Pilih salah satu FlowFile event
- Klik tombol ▶ untuk melihat detail
- Klik View Attributes untuk melihat attributes baru:
  - `flow_type = learning`
  - `project = nifi_study`
- Klik View Content untuk melihat isi:
  - `{"message": "Hello from NiFi!"}`

## Visual Diagram Flow

```
[GenerateFlowFile]
    ↓
[UpdateAttribute]
    ↓
[LogAttribute]
```

## Checklist Hari 1

- [x] Membuat FlowFile menggunakan GenerateFlowFile
- [x] Menambahkan attributes menggunakan UpdateAttribute
- [x] Melihat hasil attributes dan content FlowFile melalui LogAttribute
- [x] Memahami Data Provenance untuk tracing data di NiFi

## Catatan Tambahan

- Attributes bersifat metadata untuk FlowFile.
- Content FlowFile tetap utuh kecuali diubah oleh processor lain seperti UpdateRecord atau ReplaceText.
- Provenance di NiFi sangat berguna untuk melihat riwayat aliran data.

