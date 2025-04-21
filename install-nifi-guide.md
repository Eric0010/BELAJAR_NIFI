# Panduan Instalasi Apache NiFi di Linux (Ubuntu)

## 1. Prasyarat

Sebelum menginstal NiFi, pastikan sistem memiliki:

- Java 17 atau lebih baru
- `unzip`, `curl`, dan akses root/sudo

## 2. Download dan Ekstrak Apache NiFi

```bash
wget https://downloads.apache.org/nifi/2.3.0/nifi-2.3.0-bin.zip
unzip nifi-2.3.0-bin.zip
mv nifi-2.3.0 /opt/nifi
cd /opt/nifi
```

## 3. Konfigurasi Minimal

Edit file `conf/nifi.properties`:

```bash
nano conf/nifi.properties
```

Set konfigurasi berikut:

```properties
nifi.web.http.host=0.0.0.0
nifi.web.http.port=8080
nifi.remote.input.secure=false

# Nonaktifkan HTTPS (komentari baris-baris berikut jika ada)
# nifi.web.https.port=8443
# nifi.web.https.host=localhost
# nifi.web.https.application.protocols=h2 http/1.1
```

Simpan dan keluar.

## 4. Menjalankan NiFi

```bash
./bin/nifi.sh start
```

Cek status:

```bash
./bin/nifi.sh status
```

Jika berhasil, akan muncul:

```
NiFi is currently running
```

## 5. Akses Antarmuka Web

Buka browser dan akses:

```
http://<IP-Server-Kamu>:8080/nifi
```

Contoh: `http://10.100.13.205:8080/nifi`

Pastikan firewall membuka port 8080:

```bash
sudo ufw allow 8080
```

## 6. Menonaktifkan atau Mengulang NiFi

Stop NiFi:

```bash
./bin/nifi.sh stop
```

Start ulang:

```bash
./bin/nifi.sh restart
```

## 7. Troubleshooting

Cek log jika gagal jalan:

```bash
tail -n 50 logs/nifi-app.log
tail -n 50 logs/nifi-bootstrap.log
```

---

## Referensi

- https://nifi.apache.org/docs.html
