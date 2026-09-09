# Configure Basic Database

## 🧭 Context
In this step, I learned the basics of database administration using MariaDB—ranging from installing & setting up the service, creating databases/users with specific privileges, and performing basic CRUD operations, to backing up and restoring databases.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Database:** MariaDB
- **Tools:** `mysql`/`mariadb` CLI, `mysqldump`

## 📋 Hands-On Practice

### 1. Install & Setup MariaDB
```bash
sudo apt install mariadb-server -y
sudo systemctl status mariadb
sudo mariadb-secure-installation
```
Installed the service, verified that it was active, and configured basic security options using the interactive `mariadb-secure-installation` wizard.

### 2. Create Database & User
```sql
CREATE DATABASE mabarpay_db;
SHOW DATABASES;
CREATE USER 'user1'@'localhost' IDENTIFIED BY '123';
GRANT ALL PRIVILEGES ON mabarpay_db.* TO 'user1'@'localhost';
FLUSH PRIVILEGES;
USE mabarpay_db;
SHOW TABLES FROM mabarpay_db;
```
Created the `mabarpay_db` database along with a new user (`user1`) granted full privileges restricted to that specific database. Executed `FLUSH PRIVILEGES` to reload permissions without restarting the service.

> **Security Note:** I also tested running `GRANT ALL PRIVILEGES ON *.* TO 'user1'@'localhost';`—which grants root-equivalent access across all databases rather than restricting scope to `mabarpay_db`. This was tested strictly to observe privilege behavior, not for production use. Similarly, the password `'123'` was used purely for local testing demonstrations.

### 3. Inspect Table Structure
```sql
SHOW TABLES;
DESC transaksi;
```
Verified tables and column schemas before moving on to CRUD operations—distinct from CRUD operations because this reads structural metadata rather than table data records.

### 4. CRUD Operations
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
Practiced basic CRUD functionality: inserting new records, querying data using various filters (`WHERE`, `ORDER BY`, `LIMIT`, and combinations), updating existing entries, and deleting records from the `transaksi` table.

### 5. Database Backup & Restore
```bash
mysqldump -u user1 -p mabarpay_db > mabarpay_db_backup.sql
sudo mysql -e "CREATE DATABASE restore_test_db;"
mysql -u user1 -p restore_test_db < mabarpay_db_backup.sql
```
Backed up `mabarpay_db` into a `.sql` file, created a clean database (`restore_test_db`) for the restoration test, and restored the backup content into the new database.

## 🧩 Key Takeaways

**Background Knowledge from Prior Projects**

Prior to this step, I had worked on a Python project synced with a local database via Laragon. Because of that experience, SQL syntax felt familiar and intuitive (resembling natural English phrasing), which helped me understand the underlying database concepts more deeply rather than just memorizing syntax.

**Understanding Differences in `SHOW DATABASES` Output Between Root and Unprivileged Users**

Initially, I was confused about why executing `SHOW DATABASES` as `user1` displayed fewer databases compared to running it as `root`. I learned that `SHOW DATABASES` only lists databases that the active user has permissions to access. If a user lacks privileges for a database, system security hides it from the output list even if it exists on the server.

**Rationale Behind the `mabarpay_db` Name**

I intentionally named the database `mabarpay_db` to match a Java application project I previously developed in Apache NetBeans—an e-commerce store handling in-game currency purchases (Free Fire, Mobile Legends, Roblox, PUBG Mobile). This aligns the database hands-on work with potential future application integrations.

## 📸 Screenshots

**1. `apt install mariadb-server` + `systemctl status mariadb` — installation completed, service active and ready to accept connections:**

<img width="1290" height="524" alt="image" src="https://github.com/user-attachments/assets/3e299aaa-bca6-4027-81c4-d8a2e9efc838" />

**2. `SHOW DATABASES` — root views 6 databases, whereas `user1` sees only 3 (reflecting assigned user privileges):**

<img width="1288" height="705" alt="image" src="https://github.com/user-attachments/assets/6671c09d-ec99-47c5-8238-098cdea5f2ae" />

**3. `DESC transaksi` + CRUD operations — table structure, initial data, new record insertion, and `ORDER BY harga DESC` query output:**

<img width="1287" height="628" alt="image" src="https://github.com/user-attachments/assets/8ac41436-4017-4593-a4a4-b53abf4e5684" />

**4. Executing `mysqldump` and restoring into `restore_test_db`, confirming all records (including newly inserted rows) transferred intact:**

<img width="1276" height="694" alt="image" src="https://github.com/user-attachments/assets/60ef1fa6-de45-45ce-90f3-2c45b0c4c4ed" />

<img width="1290" height="441" alt="image" src="https://github.com/user-attachments/assets/03fc8b01-6d85-4d43-8b4f-ad3ba784bbb8" />
