# Understand Systemd & Service Management

## 🧭 Konteks
Step ini saya lakuin buat belajar manajemen service di Linux pakai `systemctl` — mulai dari kontrol dasar (start/stop/restart/reload), auto-start saat boot, debugging service yang gagal, sampai bikin unit file `.service` sendiri dari nol dan menjalankannya sebagai service custom.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `systemctl`, `journalctl`

## 📋 Yang Saya Praktikkan

### 1. Kontrol dasar service
```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
```

### 2. Auto-start saat boot
```bash
systemctl enable nginx
systemctl disable nginx
systemctl is-active --quiet nginx
systemctl is-enabled nginx
```

### 3. Cek target/mode boot
```bash
systemctl get-default
systemctl list-units --type=target
```

### 4. Reload konfigurasi systemd
```bash
sudo systemctl daemon-reload
```

### 5. Debugging service yang gagal
```bash
systemctl status nginx
journalctl -u nginx -xe
journalctl -u nginx -f
```

### 6. Bikin service custom dari nol

Simulasi proses long-running yang di-manage systemd — script nulis timestamp ke log tiap 10 detik tanpa henti, dipakai buat latihan bikin unit file dari nol sekaligus nanti buat latihan debugging di section 8.

**Script long-running (`/home/kaks/latihan-systemd/monitor.sh`):**
```bash
#!/bin/bash
set -euo pipefail

while true; do
        echo "$(date): script masih hidup" >> /home/kaks/latihan-systemd/log.txt
        sleep 10
done
```

**Unit file (`/etc/systemd/system/monitor-ky.service`):**
```ini
[Unit]
Description=monitoring test punya ky

[Service]
ExecStart=/home/kaks/latihan-systemd/monitor.sh
Type=simple
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 7. Menjalankan & verifikasi service custom
```bash
sudo systemctl daemon-reload
sudo systemctl reset-failed monitor-ky.service
sudo systemctl enable --now monitor-ky.service
sudo systemctl disable --now monitor-ky.service
systemctl status monitor-ky.service
```

### 8. Debugging service custom yang sengaja dibuat gagal
```bash
tail -f log.txt
journalctl -u monitor-ky.service -xe
```

## 🧩 Catatan

**Proses debugging service yang sengaja dibuat gagal**

Saya sengaja ubah `ExecStart` di `monitor-ky.service` dari path yang bener (`/home/kaks/latihan-systemd/monitor.sh`) jadi path yang nggak ada (`/home/kaks/latihan-systemd/tidakTersedia`), sebagai latihan proses debugging service yang crash. Pas dicek pakai `tail -f log.txt`, nggak ada baris baru yang masuk (tanda script-nya nggak jalan sama sekali). Baru ketemu akar masalahnya setelah cek `journalctl -u monitor-ky.service -xe` — informasi errornya jelas nunjukin masalah "directory/file not found" karena path di `ExecStart` nggak valid.

**Baru tau ada konsep unit file `[Unit]`/`[Service]`/`[Install]`**

Sebelumnya saya nggak tau kalau ada config semacam ini yang ngatur gimana suatu script dijalankan sebagai service. Awalnya bingung anatomi tiga section ini (`[Unit]`, `[Service]`, `[Install]`) itu kerjanya gimana dan buat apa masing-masing. Sempat juga susah nerima konsepnya — kayak "masa iya cuma dengan nulis `[Unit]` di satu baris, terus detail di bawahnya, sistem bisa ngerti itu section apa dan kenapa harus dijalankan dengan cara tertentu", padahal itu cuma teks polos yang dipisah per baris. Setelah dipelajari satu-satu, baru paham ini emang cara kerja standar kalau mau bikin suatu script auto-running setiap boot — beda jalur dari `crontab -e` yang sebelumnya saya pakai buat otomasi terjadwal.

**Baru paham `enable` itu sebenarnya cuma bikin symlink**

Awalnya saya kira `systemctl enable` itu semacam tombol ajaib yang langsung "menyalakan" service, mirip klik tombol Run di VSCode buat jalanin file Python. Ternyata yang beneran terjadi di baliknya cuma pembuatan symlink dari unit file ke folder `.wants/` (sesuai target yang ditulis di `WantedBy`) — dan itu cuma ngatur biar service-nya ikut jalan otomatis pas boot berikutnya, bukan langsung menyalakan sekarang juga. Makanya saya pakai `enable --now` di praktik saya, biar sekalian di-enable buat boot berikutnya dan langsung di-start juga saat itu.

## 📸 Screenshot

**1. `status` → `enable` → `is-enabled` nginx — pesan `Created symlink` muncul jelas saat `enable`, sesuai insight symlink di Catatan:**

<img width="1046" height="399" alt="image" src="https://github.com/user-attachments/assets/05bc4e1c-7340-48bc-b999-e3cb4712c6ee" />

**2. Unit file `monitor-ky.service` (isi lengkap 3 section) + status service `active (running)`, proses `monitor.sh` dan `sleep 10` terlihat di CGroup:**

<img width="805" height="413" alt="image" src="https://github.com/user-attachments/assets/75646af5-e73a-452f-8c07-ed4078f0de70" />

**3. `log.txt` — bukti `monitor.sh` berjalan terus-menerus, baris baru muncul tiap 10 detik sesuai logika script:**

<img width="527" height="182" alt="image" src="https://github.com/user-attachments/assets/26db9ef8-28a3-4d39-b24f-4cc4224e7fe5" />

**4. Debugging service gagal — `status` menunjukkan `failed (exit-code)`, `journalctl` mengungkap akar masalah (`No such file or directory` karena path `ExecStart` salah), serta terlihat systemd otomatis mencoba restart beberapa kali sesuai `Restart=on-failure`:**

<img width="1272" height="699" alt="image" src="https://github.com/user-attachments/assets/4bd2c912-5457-4f19-af36-2afcc85c5fd9" />

