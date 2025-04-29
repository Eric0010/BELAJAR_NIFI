# Apache NiFi - Minggu 2 Hari 3: Filtering dan Conditional Flow

Dokumen ini berisi langkah-langkah lengkap latihan Apache NiFi untuk membuat routing berdasarkan multiple attributes menggunakan RouteOnAttribute.

## Tujuan

- Membuat flow percabangan berdasarkan nilai attribute `status`
- Mengarahkan FlowFile ke jalur berbeda (`is_new`, `is_processing`, `is_completed`)
- Melihat hasil filtering dengan banyak GenerateFlowFile

## Langkah-Langkah Praktik

### 1. Buat Tiga Processor GenerateFlowFile

- Tambahkan tiga processor `GenerateFlowFile`:
  - GenerateFlowFile (New)
    - Custom Text:
      ```json
      {"status": "new"}
      ```
  - GenerateFlowFile (Processing)
    - Custom Text:
      ```json
      {"status": "processing"}
      ```
  - GenerateFlowFile (Completed)
    - Custom Text:
      ```json
      {"status": "completed"}
      ```

### 2. Buat Tiga Processor UpdateAttribute

- Tambahkan tiga processor `UpdateAttribute`, masing-masing untuk satu GenerateFlowFile:
  - UpdateAttribute (New)
    - Property:
      - Name: `status`
      - Value: `new`
  - UpdateAttribute (Processing)
    - Property:
      - Name: `status`
      - Value: `processing`
  - UpdateAttribute (Completed)
    - Property:
      - Name: `status`
      - Value: `completed`

### 3. Merge ke RouteOnAttribute

- Hubungkan ketiga UpdateAttribute ke satu processor `RouteOnAttribute`.

### 4. Konfigurasi RouteOnAttribute

- Tambahkan Dynamic Properties:
  - Property Name: `is_new`
    - Value: `${status:equals('new')}`
  - Property Name: `is_processing`
    - Value: `${status:equals('processing')}`
  - Property Name: `is_completed`
    - Value: `${status:equals('completed')}`
- Routing Strategy: `Route to Property name`

### 5. Buat Tiga Processor LogAttribute

- Tambahkan tiga processor `LogAttribute`:
  - LogAttribute (New)
  - LogAttribute (Processing)
  - LogAttribute (Completed)

### 6. Hubungkan RouteOnAttribute

- Hubungkan:
  - Relationship `is_new` ke `LogAttribute (New)`
  - Relationship `is_processing` ke `LogAttribute (Processing)`
  - Relationship `is_completed` ke `LogAttribute (Completed)`

### 7. Jalankan Flow

- Start semua processor
- Tunggu FlowFile mengalir

### 8. Cek Provenance

- Klik kanan setiap LogAttribute
- Pilih View Data Provenance
- Periksa attribute `status` dan jalur mana yang dilewati (`is_new`, `is_processing`, atau `is_completed`)

## Visual Diagram Flow

![image](https://github.com/user-attachments/assets/0588bee4-802e-49f4-bd13-6a8c53aa5c27)











## Checklist Hari 3

- [x] Membuat tiga FlowFile dengan status berbeda
- [x] Menambahkan attributes status menggunakan UpdateAttribute
- [x] Membuat banyak kondisi di RouteOnAttribute
- [x] Membuat LogAttribute untuk setiap jalur
- [x] Melihat hasil filtering di Provenance

## Catatan Tambahan

- FlowFile yang tidak cocok dengan kondisi apapun akan otomatis masuk ke `unmatched`.
- Provenance dapat digunakan untuk melihat jalur aliran FlowFile.
- RouteOnAttribute adalah dasar membuat decision tree di NiFi.

