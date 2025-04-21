# Panduan Mengirim Data dari Apache NiFi ke Kafka (Confluent Platform)

Dokumen ini menjelaskan cara membuat alur sederhana di Apache NiFi untuk mengirim data ke Kafka self-hosted (Confluent Platform).

---

## 1. Prasyarat

- Apache NiFi sudah terinstal dan berjalan di port 8080
- Kafka self-hosted (Confluent Platform) sudah berjalan dan dapat diakses oleh NiFi
- Topik Kafka sudah dibuat, atau gunakan `auto.create.topics.enable=true` di Kafka untuk membuat otomatis

---

## 2. Buat Alur NiFi: Generate Data ke Kafka

### A. Tambahkan Processor `GenerateFlowFile`

- Buka NiFi UI
- Tambahkan processor `GenerateFlowFile`
- Masuk ke **Configure > Properties**
- Ubah `Custom Text` menjadi:

```json
{"status": "hello from NiFi"}
```

- Ubah `Run Schedule` menjadi `5 sec` (opsional)

---

### B. Tambahkan Processor `PublishKafkaRecord_2_6`

- Tambahkan processor `PublishKafkaRecord_2_6`
- Masuk ke **Configure > Settings**
- Atur:
  - Kafka Brokers: `10.100.13.239:9092`
  - Topic Name: `demo-topic`

- Masuk ke tab **Settings > Auto-Terminate Relationships**
  - Centang `success`
  - Centang `failure`

---

### C. Tambahkan Controller Services

#### 1. `JsonTreeReader`
- Klik tombol + (di bagian bawah processor)
- Pilih `JsonTreeReader`
- Klik ikon pensil:
  - Schema Access Strategy: `Infer Schema`
- Enable service

#### 2. `JsonRecordSetWriter`
- Tambahkan `JsonRecordSetWriter`
- Klik ikon pensil:
  - Schema Write Strategy: `Do Not Write Schema`
  - Pretty Print: `true`
- Enable service

- Setelah itu, assign kedua controller service tersebut ke processor `PublishKafkaRecord_2_6`

---

## 3. Jalankan dan Verifikasi

- Sambungkan `GenerateFlowFile` → `PublishKafkaRecord_2_6`
- Klik Start untuk kedua processor
- Cek Kafka consumer:

```bash
kafka-console-consumer.sh --bootstrap-server 10.100.13.239:9092 --topic demo-topic --from-beginning
```

Harus muncul:

```json
{"status":"hello from NiFi"}
```

---

## 4. Troubleshooting

- Jika Read/Write = 0 bytes: pastikan `Custom Text` diisi di `GenerateFlowFile`, bukan di `Comment`
- Jika Kafka gagal connect: pastikan `advertised.listeners` Kafka sesuai, atau tambahkan `server-eric` ke `/etc/hosts`

---

## 5. Referensi

- https://nifi.apache.org/docs.html
- https://docs.confluent.io/
