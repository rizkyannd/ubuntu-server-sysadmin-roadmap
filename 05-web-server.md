# Install & Configure Web Server (Apache/Nginx)

## 🧭 Konteks
Setup web server dimulai dengan Apache standalone — akses HTTP jalan normal, tapi HTTPS gagal karena SSL belum diaktifkan. Setelah itu berhasil dibenerin, lanjut ke tahap kedua: pasang Nginx sebagai reverse proxy di depan Apache (Apache pindah ke backend port 8080), dan ternyata masalah SSL serupa muncul lagi di sisi Nginx — perlu di-setup ulang dari awal khusus untuk Nginx.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Web server:** Apache2 (awalnya standalone, lalu jadi backend) + Nginx (reverse proxy)
- **Firewall:** UFW
- **SSL:** Self-signed certificate, dikonfigurasi terpisah di Apache dan di Nginx

---

## 📋 Fase 1 — Apache Standalone: HTTPS Gagal

**1. Akses awal — HTTP jalan, HTTPS gagal**
Server baru diinstall Apache default. Akses via HTTP normal, tapi begitu dicoba HTTPS, muncul error — SSL module Apache belum aktif sama sekali.

**2. Enable module SSL:**
```bash
sudo a2enmod ssl
```

**3. Aktifkan site default-ssl (config listen 443):**
```bash
sudo a2ensite default-ssl
```

**4. Restart Apache:**
```bash
sudo systemctl restart apache2
```

Setelah ini, akses HTTPS ke Apache berhasil.

---

## 📋 Fase 2 — Setup Nginx Reverse Proxy (Apache Jadi Backend)

**1. Install Nginx & buka akses firewall:**
```bash
sudo apt install nginx
sudo ufw allow 'Nginx HTTP'
systemctl status nginx
```

**2. Bikin folder & generate self-signed certificate khusus Nginx:**
```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/nginx-selfsigned.key \
  -out /etc/nginx/ssl/nginx-selfsigned.crt
```

**3. Bikin config reverse proxy (HTTP dulu):**
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

**4. Aktifkan config & reload:**
```bash
sudo ln -s /etc/nginx/sites-available/nginx-proxy /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

**5. Test config & reload:**
```bash
sudo nginx -t
sudo systemctl reload nginx
```

**6. Verifikasi firewall:**
```bash
sudo ufw status verbose
```

## 🧩 Catatan
Hal menarik dari proses ini: masalah SSL yang saya benerin di Apache (Fase 1) ternyata nggak otomatis "nurun" ke Nginx, meskipun keduanya jalan di server yang sama. Di Fase 1, saya benerin SSL Apache pakai a2enmod ssl + a2ensite default-ssl — waktu itu saya cuma tahu Apache, belum kepikiran soal reverse proxy sama sekali, jadi SSL-nya saya tuntaskan langsung di situ.

Begitu saya belajar konsep reverse proxy dan pasang Nginx di depan Apache, saya baru paham Nginx butuh certificate dan config SSL-nya sendiri dari nol — nggak bisa "pinjam" dari SSL yang udah aktif di Apache. Kali ini saya coba lakuin dengan urutan yang lebih rapi: generate self-signed certificate dulu pakai OpenSSL, baru tulis config Nginx dengan kedua server block (port 80 dan port 443) sekaligus dalam satu file. Karena cert-nya udah tersedia duluan sebelum config di-reload, nginx -t langsung valid dan HTTP maupun HTTPS dua-duanya langsung jalan begitu Nginx di-reload.

Insight-nya: di arsitektur reverse proxy, SSL termination biasanya dipegang oleh layer paling depan (di sini Nginx) — komunikasi Nginx ke Apache di backend (port 8080) tetap plain HTTP internal. Jadi config SSL yang saya pasang di Apache pas Fase 1 sebenarnya jadi nggak kepake lagi dari sudut pandang client, karena client sekarang ngobrol sama Nginx, bukan langsung ke Apache. Kalau saya udah paham konsep ini dari awal, mungkin nggak perlu buang waktu setup SSL di Apache standalone dulu — tapi karena Fase 1 saya lakuin sebelum kepikiran pasang reverse proxy, jadi kejadian aja, dan justru dari situ saya belajar perbedaan peran SSL di tiap layer.

⚠️ Follow-up: karena Apache SSL config Fase 1 udah nggak relevan (redundant) di setup akhir, belum sempat dicek apakah lebih baik di-disable aja modul SSL-nya di Apache, atau dibiarkan standby buat jaga-jaga kalau suatu saat akses ke Apache langsung diperlukan lagi.

## 📸 Screenshot
**1. Output `a2enmod ssl` + `a2ensite default-ssl` (Apache):**
<img width="1071" height="853" alt="image" src="https://github.com/user-attachments/assets/9b1df7c0-dae5-4211-8867-f00f90c6b1cc" />

**2. `systemctl status nginx`:**
<img width="1057" height="359" alt="image" src="https://github.com/user-attachments/assets/86e7cf47-9ebd-426d-8917-e93d60bb7309" />

**3. `nginx -t` — config valid:**
<img width="1278" height="145" alt="image" src="https://github.com/user-attachments/assets/5069aee9-b2cd-45e6-a199-94613a301220" />

**4. `ufw status verbose`:**
<img width="1066" height="269" alt="image" src="https://github.com/user-attachments/assets/e268dba6-4113-44db-ad5d-a3761ff47256" />

**5. Akses HTTPS ke Nginx berhasil (reverse proxy meneruskan ke Apache backend):**
<img width="953" height="1026" alt="image" src="https://github.com/user-attachments/assets/01218c60-ad69-4fd2-bfed-8a8051f0423b" />

