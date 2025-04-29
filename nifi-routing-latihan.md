# Apache NiFi - Latihan Routing FlowFile Berdasarkan Attribute

Dokumen ini menjelaskan latihan dasar penggunaan Apache NiFi untuk melakukan routing (cabang alur) berdasarkan nilai dari attribute menggunakan processor `RouteOnAttribute`.

## Tujuan

- Mengenal cara kerja attribute dalam FlowFile
- Membuat cabang logika menggunakan `RouteOnAttribute`
- Menangani FlowFile dengan kondisi `status = new` dan `status ≠ new` (unmatched)

---

## Arsitektur Flow

```
[GenerateFlowFile (new)] ─┬─> [UpdateAttribute (status = new)] ─┬─> [RouteOnAttribute]
                          │                                      ├── is_new ──> [LogAttribute (is_new)]
[GenerateFlowFile (old)] ─┘                                      └── unmatched ──> [LogAttribute (unmatched)]
```

---

## Langkah-langkah

### 1. Tambahkan Processor `GenerateFlowFile` (2 buah)

**GenerateFlowFile - New**
- Custom Text:
  ```json
  {"status": "new"}
  ```

**GenerateFlowFile - Old**
- Custom Text:
  ```json
  {"status": "old"}
  ```

### 2. Tambahkan Processor `UpdateAttribute` (2 buah)

**UpdateAttribute - New**
- Tambahkan Property:
  - Name: `status`
  - Value: `new`

**UpdateAttribute - Old**
- Tambahkan Property:
  - Name: `status`
  - Value: `old`

### 3. Tambahkan Processor `RouteOnAttribute`
- Routing Strategy: `Route to Property name`
- Tambahkan Dynamic Property:
  - Name: `is_new`
  - Value: `${status:equals('new')}`

### 4. Tambahkan 2 `LogAttribute` Processor

- `LogAttribute (is_new)` untuk relationship `is_new`
- `LogAttribute (unmatched)` untuk relationship `unmatched`

---

## Konfigurasi Routing

| Attribute `status` | Expression `${status:equals('new')}` | Keluar Lewat |
|--------------------|----------------------------------------|--------------|
| `new`              | `true`                                 | is_new       |
| `old`              | `false`                                | unmatched    |
| tidak ada          | `false`                                | unmatched    |

---

## Cara Melihat Hasil

1. Klik kanan processor `LogAttribute`
2. Pilih **View Data Provenance**
3. Pilih satu FlowFile dan klik `▶`
4. Klik **View Attributes** untuk melihat nilai `status`
5. Klik **View Content** untuk melihat isi FlowFile

---

## Catatan

- Processor `RouteOnAttribute` hanya mengevaluasi **attributes**, bukan isi konten file (content).
- Gunakan `UpdateAttribute` atau `EvaluateJsonPath` untuk mengubah content menjadi attribute jika perlu.

---

## Referensi

- https://nifi.apache.org/docs/nifi-docs/html/user-guide.html
- https://nifi.apache.org/docs/nifi-docs/components/org.apache.nifi/nifi-standard-nar/latest/org.apache.nifi.processors.standard.RouteOnAttribute/
