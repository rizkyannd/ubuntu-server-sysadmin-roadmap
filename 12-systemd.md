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
Dipakai setelah edit unit file secara manual, supaya systemd baca ulang perubahan dari disk.

### 5. Debugging service yang gagal
```bash
systemctl status nginx
journalctl -u nginx -xe
journalctl -u nginx -f
```

### 6. Bikin service custom dari nol
- Script long-running: `/home/kaks/latihan-systemd/monitor.sh`, log ke `/home/kaks/latihan-systemd/log.txt`
- Unit file: `/etc/systemd/system/monitor-ky.service`

<!-- ISI FILE monitor.sh DAN monitor-ky.service DI SINI -->

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
Sengaja bikin `monitor-ky.service` gagal, lalu telusuri akar masalahnya lewat log real-time dan log detail systemd.

## 🧩 Catatan
<!-- belum diisi -->

## 📸 Screenshot
<!-- belum diisi -->
