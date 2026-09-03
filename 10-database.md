# Configure Basic Database

## 🧭 Konteks
Step ini saya lakuin buat belajar dasar administrasi database pakai MariaDB — install & setup service, bikin database/user beserta hak aksesnya, operasi CRUD dasar, sampai cara backup & restore database.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Database:** MariaDB
- **Tools:** `mysql`/`mariadb` CLI, `mysqldump`

## 📋 Yang Saya Praktikkan

### 1. Install & setup MariaDB
```bash
sudo apt install mariadb-server -y
sudo systemctl status mariadb
sudo mariadb-secure-installation
```
Install service, cek status jalan atau nggak, lalu konfigurasi keamanan dasar lewat wizard interaktif (`mariadb-secure-installation`).

### 2. Bikin database & user
```sql
CREATE DATABASE mabarpay_db;
SHOW DATABASES;
CREATE USER 'user1'@'localhost' IDENTIFIED BY '123';
GRANT ALL PRIVILEGES ON mabarpay_db.* TO 'user1'@'localhost';
FLUSH PRIVILEGES;
USE mabarpay_db;
SHOW TABLES FROM mabarpay_db;
```
Bikin database `mabarpay_db`, user baru (`user1`) dengan akses penuh khusus ke database itu, lalu `FLUSH PRIVILEGES` supaya perubahan langsung aktif tanpa restart service.

> **Catatan keamanan:** sempat juga dicoba `GRANT ALL PRIVILEGES ON *.* TO 'user1'@'localhost';` — ini setara akses root ke semua database, bukan cuma `mabarpay_db`. Dipakai di sini cuma buat lihat efeknya, bukan konfigurasi yang saya pakai di produksi. Password `'123'` juga cuma buat demo lokal di lingkungan testing ini.

### 3. Cek struktur tabel
```sql
SHOW TABLES;
DESC transaksi;
```
Verifikasi tabel dan struktur kolom sebelum masuk ke operasi CRUD — beda dari CRUD karena ini baca metadata/struktur, bukan isi data di dalamnya.

### 4. Operasi CRUD
```sql
INSERT INTO transaksi (nama_pembeli, item, harga, metode_bayar)
VALUES ('Budi Santoso', 'Mobile Legends Diamond 15000', 200000.00, 'QRIS');

SELECT * FROM transaksi;
SELECT nama_pembeli, item, harga FROM transaksi;
SELECT * FROM transaksi WHERE harga > 200000;
SELECT * FROM transaksi WHERE metode_bayar = 'QRIS';
SELECT * FROM transaksi ORDER BY harga DESC;
SELECT * FROM transaksi LIMIT 2;
SELECT * FROM transaksi ORDER BY id DESC LIMIT 2;

UPDATE transaksi SET metode_bayar = 'DANA' WHERE nama_pembeli = 'Haris mantap';

DELETE FROM transaksi WHERE nama_pembeli = 'Haris mantap';
```
Praktik CRUD dasar: insert data baru, baca data dengan berbagai filter (`WHERE`, `ORDER BY`, `LIMIT`, kombinasi keduanya), update, dan hapus data di tabel `transaksi`.

### 5. Backup & restore database
```bash
mysqldump -u user1 -p mabarpay_db > mabarpay_db_backup.sql
sudo mysql -e "CREATE DATABASE restore_test_db;"
mysql -u user1 -p restore_test_db < mabarpay_db_backup.sql
```
Backup database `mabarpay_db` ke file `.sql`, bikin database kosong buat simulasi restore (`restore_test_db`), lalu restore isi backup ke database itu.

## 🧩 Catatan

**Modal awal dari pengalaman project sebelumnya**

Sebelum belajar step ini, saya udah pernah coba bikin project Python yang disinkronkan ke database pakai Laragon. Jadi pas masuk materi MariaDB ini, dasar SQL-nya kerasa nggak asing lagi (mirip kalimat bahasa Inggris biasa), dan justru bikin saya lebih paham logika di balik command-command-nya secara lebih dalam, bukan cuma sekadar hafal syntax.

**Bingung kenapa `SHOW DATABASES` beda hasil antara user baru dan root**

Sempat bingung kenapa pas jalanin `SHOW DATABASES` pakai `user1` (user baru yang saya buat), hasilnya beda dari pas login pakai akun root — daftar database yang muncul lebih sedikit. Ternyata `SHOW DATABASES` itu cuma nampilin database yang user tersebut punya hak akses/privilege atasnya, bukan semua database yang ada di server. Kalau user nggak dikasih permission ke suatu database, database itu nggak bakal muncul di output, meskipun database-nya beneran ada di server.

**Alasan pakai nama `mabarpay_db`**

Nama database ini sengaja disamain dengan nama project Java yang sebelumnya saya buat di Apache NetBeans (`mabarpay_db`) — project itu toko pembelian diamond/UC/Robux untuk game (Free Fire, Mobile Legends, Roblox, PUBG Mobile). Tujuannya biar ke depannya bisa diintegrasikan antara project Java itu dengan praktik database yang saya pelajari di step ini.

## 📸 Screenshot
**1. `apt install mariadb-server` + `systemctl status mariadb` — instalasi berhasil, service aktif dan siap menerima koneksi:**

<img width="1290" height="524" alt="image" src="https://github.com/user-attachments/assets/3e299aaa-bca6-4027-81c4-d8a2e9efc838" />


**2. `SHOW DATABASES` — root melihat 6 database, `user1` cuma melihat 3 (sesuai privilege yang diberikan):**

<img width="1288" height="705" alt="image" src="https://github.com/user-attachments/assets/6671c09d-ec99-47c5-8238-098cdea5f2ae" />


**3. `DESC transaksi` + CRUD — struktur tabel, data awal, insert data baru, dan hasil query `ORDER BY harga DESC`:**

<img width="1287" height="628" alt="image" src="https://github.com/user-attachments/assets/8ac41436-4017-4593-a4a4-b53abf4e5684" />


**4. `mysqldump` + restore ke `restore_test_db`, diverifikasi datanya (termasuk baris terbaru) berhasil pindah utuh:**

<img width="1276" height="694" alt="image" src="https://github.com/user-attachments/assets/60ef1fa6-de45-45ce-90f3-2c45b0c4c4ed" />

<img width="1290" height="441" alt="image" src="https://github.com/user-attachments/assets/03fc8b01-6d85-4d43-8b4f-ad3ba784bbb8" />






