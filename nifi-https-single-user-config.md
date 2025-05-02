# Konfigurasi NiFi dengan HTTPS dan Single User

Dokumen ini menjelaskan langkah-langkah lengkap untuk mengaktifkan HTTPS pada Apache NiFi versi 2.x dengan Single User Login Identity Provider.

---

## 1. Prasyarat

- Java 21 (OpenJDK 21)
- Apache NiFi 2.3.0
- Akses root atau user dengan hak istimewa pada server
- `keytool` tersedia (`openjdk-21-jdk-headless` atau paket serupa)

## 2. Membuat Keystore (PKCS12)

```bash
cd /home/nifi/nifi-2.3.0
keytool -genkeypair   -alias nifi   -keyalg RSA -keysize 2048   -storetype PKCS12 -keystore conf/keystore.p12   -validity 3650   -dname "CN=kafka1, OU=IT, O=MyOrg, L=City, ST=State, C=ID"   -ext SAN=DNS:kafka1,IP:10.100.13.205
```

- **Alias**: `nifi`
- **Keystore**: `conf/keystore.p12`
- **Password**: (ingat password yang dimasukkan)

## 3. Membuat Truststore (PKCS12)

Untuk single-user NiFi, truststore bisa sama dengan keystore. Jika perlu truststore terpisah, ekspor sertifikat lalu import:

```bash
keytool -exportcert   -alias nifi   -storetype PKCS12 -keystore conf/keystore.p12   -file conf/ca-cert.pem

keytool -importcert   -alias nifi-ca   -file conf/ca-cert.pem   -storetype PKCS12 -keystore conf/truststore.p12   -noprompt
```

## 4. Konfigurasi `nifi.properties`

Buka file `conf/nifi.properties` dan sesuaikan bagian berikut:

```properties
# Web HTTPS
nifi.web.https.host=0.0.0.0
nifi.web.https.port=8443
nifi.web.https.network.interface.default=
nifi.web.https.application.protocols=h2 http/1.1

# Lokasi KeyStore dan TrustStore
nifi.security.keystore=/home/nifi/nifi-2.3.0/conf/keystore.p12
nifi.security.keystoreType=PKCS12
nifi.security.keystorePasswd=<KeystorePassword>
nifi.security.keyPasswd=<KeyPassword>

nifi.security.truststore=/home/nifi/nifi-2.3.0/conf/truststore.p12
nifi.security.truststoreType=PKCS12
nifi.security.truststorePasswd=<TruststorePassword>
```

Ganti `<KeystorePassword>`, `<KeyPassword>`, dan `<TruststorePassword>` sesuai yang Anda gunakan.

## 5. Konfigurasi Single User Login Identity Provider

Edit file `conf/login-identity-providers.xml`, ubah menjadi:

```xml
<provider>
    <identifier>single-user-provider</identifier>
    <class>org.apache.nifi.authentication.single.user.SingleUserLoginIdentityProvider</class>
    <property name="Username">admin</property>
    <property name="Password">$2b$12$5JJnzeLVYcICJcAtT4UJ.Oaq6RtVVo1h3DC8oHADjRDJQ6QbQtrF6</property>
</provider>
```

- **Username**: `admin`
- **Password**: masukkan hasil hash bcrypt (contoh di atas untuk `P@ssw0rd`)

### Menghasilkan Hash bcrypt (opsional)

Jika ingin membuat hash baru:

```bash
python3 - << 'EOF'
import bcrypt
pw = b"P@ssw0rd"
print(bcrypt.hashpw(pw, bcrypt.gensalt()).decode())
EOF
```

## 6. Restart NiFi

```bash
cd /home/nifi/nifi-2.3.0
./bin/nifi.sh stop
./bin/nifi.sh start
```

Pastikan tidak ada error di `logs/nifi-app.log`.

## 7. Verifikasi

- Akses `https://<nama-ip>:8443/nifi/`
- Login dengan:
  - **Username**: `admin`
  - **Password**: `P@ssw0rd`

Jika berhasil, UI NiFi akan terbuka.

## 8. Troubleshooting

- **Invalid SNI**: pastikan `nifi.web.https.host` diisi dengan host yang sesuai (DNS atau FQDN).
- **keystore password was incorrect**: periksa kembali password di `nifi.properties`.
- **Login gagal**: periksa kembali hash password dan username di `login-identity-providers.xml`.
