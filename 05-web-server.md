# Install & Configure Web Server (Apache/Nginx)

## 🧭 Konteks
Setup Nginx sebagai reverse proxy di depan Apache (Apache jalan di backend port 8080, Nginx terima request dari luar). Awalnya cuma setup HTTP, terus pas dicoba akses pakai HTTPS ternyata gagal — proses ini yang jadi fokus dokumentasi ini: kenapa gagal dan gimana cara benerinnya.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Web server:** Apache (backend, port 8080) + Nginx (reverse proxy)
- **Firewall:** UFW
- **SSL:** Self-signed certificate (OpenSSL), terpasang di sisi Nginx

## 📋 Yang Saya Praktikkan

**1. Install Nginx & buka akses firewall:**
```bash
sudo apt install nginx
sudo ufw allow 'Nginx HTTP'
systemctl status nginx
```

**2. Bikin config reverse proxy (HTTP dulu):**
```bash
sudo nano /etc/nginx/sites-available/[nama-file]
```
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name [ip-address-server];
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;
    }
}
```

**3. Aktifkan config & reload:**
```bash
sudo ln -s /etc/nginx/sites-available/[nama-file] /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
Di titik ini, akses **HTTP** ke server sudah berhasil.

**4. Coba akses HTTPS — gagal**
Belum ada listener untuk port 443 sama sekali di config Nginx, jadi request HTTPS nggak ada yang handle.

**5. Bikin folder & generate self-signed certificate:**
```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/nginx-selfsigned.key \
  -out /etc/nginx/ssl/nginx-selfsigned.crt
```

**6. Update config: tambahkan server block untuk port 443:**
```bash
sudo nano /etc/nginx/sites-available/nginx-proxy
```
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name 192.168.1.73;
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;
    }
}
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name 192.168.1.73;
    ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;
    }
}
```

**7. Test config & reload:**
```bash
sudo nginx -t
sudo systemctl reload nginx
```

**8. Verifikasi firewall:**
```bash
sudo ufw status verbose
```

## 🧩 Catatan
Awalnya HTTPS gagal total padahal HTTP jalan normal. Ternyata penyebabnya sederhana tapi sempat bikin bingung: **config Nginx yang pertama cuma punya server block untuk port 80** — belum ada `listen 443 ssl` sama sekali, jadi Nginx nggak tau harus ngapain kalau ada request masuk ke port 443. HTTP jalan karena listener-nya memang cuma disiapkan untuk itu.

Solusinya: generate self-signed certificate pakai OpenSSL, lalu tambahkan **server block kedua** khusus untuk `listen 443 ssl` dengan `ssl_certificate` dan `ssl_certificate_key` mengarah ke file cert yang baru dibuat. SSL di sini dihandle sepenuhnya di sisi Nginx (SSL termination) — Apache di backend tetap komunikasi biasa lewat HTTP di port 8080, nggak perlu tau soal SSL sama sekali. Ini pola umum di reverse proxy setup: proxy yang urus enkripsi, backend fokus urus aplikasi.

⚠️ **Follow-up:** karena pakai self-signed cert (bukan dari CA resmi kayak Let's Encrypt), browser/curl bakal warning "not trusted" tiap akses HTTPS — perlu tambahin flag `-k` di curl buat testing, atau accept warning manual di browser. Belum coba ganti ke Let's Encrypt buat hilangin warning ini.

## 📸 Screenshot
**1. `systemctl status nginx` setelah install:**
<img width="1050" height="355" alt="image" src="https://github.com/user-attachments/assets/cbb7aac8-c2dc-44ca-ba5a-df70c76506db" />

<!-- ss -->

**2. Akses HTTP berhasil (curl atau browser):**
<img width="1050" height="700" alt="image" src="https://github.com/user-attachments/assets/1944c230-720e-4dc3-8a94-20627ca99909" />

<!-- ss -->

**3. 🔍 Akses HTTPS gagal — sebelum cert dibuat:**
<!-- ss error message -->

**4. Proses `openssl req` generate certificate:**
<!-- ss -->

**5. `nginx -t` — konfirmasi config valid setelah tambah server block 443:**
<!-- ss -->

**6. 🔍 Akses HTTPS berhasil setelah cert terpasang (dengan warning not trusted):**
<!-- ss -->

**7. `ufw status verbose` — konfirmasi port yang di-allow:**
<img width="1050" height="300" alt="image" src="https://github.com/user-attachments/assets/55170e97-62bb-4574-8bc6-1ca880d23402" />

<!-- ss -->
