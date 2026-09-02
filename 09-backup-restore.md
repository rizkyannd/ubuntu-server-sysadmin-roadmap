# Backup & Restore Files

## 🧭 Konteks
Step ini saya lakuin buat belajar cara backup dan restore file/folder di server — baik lewat `tar` (kompresi jadi satu file arsip) maupun `rsync` (sinkronisasi antar folder/server). Termasuk cara otomasi backup pakai `crontab` dan cara verifikasi integritas file hasil backup.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `tar`, `rsync`, `crontab`, `sha256sum`

## 📋 Yang Saya Praktikkan

### 1. Backup & restore pakai `tar`
```bash
sudo tar -czvf sticky-backup-$(date +%F).tar.gz -C /home/kaks archive
tar -tzvf nama_file
sudo tar -xzvf sticky-backup-2026-07-21.tar.gz -C /direktori/tujuan
```
Backup folder `archive` jadi satu file terkompresi dengan nama otomatis berdasarkan tanggal, cek isi arsip tanpa extract, dan restore ke direktori tujuan.

### 2. Verifikasi integritas file backup
```bash
sha256sum NAMA_FILE
```
Bandingkan hash file backup buat mastiin nggak ada corruption.

### 3. Sinkronisasi & backup pakai `rsync`
```bash
rsync -avhz SOURCE DESTINATION
rsync -avh -e "ssh -p 2222" sticky user@IP_SERVER:/backup/
rsync -avh --exclude='*.log' --exclude='cache/' /var/www/ /backup/www-backup/
rsync -avh --dry-run --delete /var/www/ /backup/www-backup/
rsync -avh --delete /var/www/ /backup/www-backup/
```
Sinkronisasi folder ke lokasi lain (termasuk server remote lewat SSH), dengan filter file yang di-exclude, dan simulasi (`--dry-run`) sebelum eksekusi `--delete` yang beneran.

### 4. Otomasi backup terjadwal
```bash
crontab -e
crontab -l
```
Bikin dan cek jadwal command otomatis.

## 🧩 Catatan
<!-- belum diisi -->

## 📸 Screenshot
<!-- belum diisi -->
