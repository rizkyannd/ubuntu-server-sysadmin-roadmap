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

**Bingung sama syntax 5 kolom di `crontab`**
Awalnya saya bingung cara nulis jadwal di `crontab -e`, karena ada 5 indikator waktu yang harus diisi berurutan (menit, jam, tanggal, bulan, hari), dan kalau salah urutan atau format bisa bikin jadwalnya nggak jalan sesuai maksud. Butuh waktu buat ngerti posisi tiap kolom itu ngatur apa — bukan cuma hafalin urutannya, tapi paham logika di baliknya (misal kolom pertama itu menit, jadi kalau mau jalanin tiap jam di menit ke-0, harus ditulis `0 * * * *`, bukan asal taruh angka di posisi manapun).

**Baru tau kalau otomasi script/command itu pakai `crontab`**
Sebelumnya saya nggak kepikiran ada mekanisme khusus buat bikin suatu file/command jalan otomatis terjadwal di Linux — ternyata `crontab` itu yang jadi jawabannya. Ini insight baru yang langsung kepake buat mikirin backup: daripada jalanin `tar`/`rsync` manual tiap kali, command itu bisa dijadwalin jalan sendiri lewat `crontab -e`, misalnya tiap hari jam tertentu.

**Baru paham konsep hash buat verifikasi integritas file**
Sebelum belajar `sha256sum`, cara saya mastiin backup "berhasil" cuma sekadar cek filenya ada atau nggak (misal lewat `ls`). Ternyata itu nggak cukup — file bisa aja ada tapi isinya corrupt atau rusak sebagian tanpa kelihatan dari luar. Dari `sha256sum` saya baru ngerti konsep hash: setiap file punya "sidik jari" unik berdasarkan isinya, jadi kalau hash dari file asli dan file hasil backup sama persis, berarti isinya benar-benar identik tanpa ada bit yang berubah. Ini cara verifikasi yang jauh lebih meyakinkan dibanding cuma ngecek keberadaan file doang.

## 📸 Screenshot

**1. `tar -czvf` + `tar -tzvf` — bikin arsip backup ke folder tujuan spesifik, lalu verifikasi isinya tanpa extract:**

<img width="1084" height="77" alt="image" src="https://github.com/user-attachments/assets/77ac7e53-78e6-4c1b-9582-982cc911c424" />

**2. `ls -l` isi folder arsip, lalu `sha256sum` — generate checksum, simpan ke file, verifikasi integritas (`sha256sum -c`) hasilnya `OK`:**

<img width="985" height="214" alt="image" src="https://github.com/user-attachments/assets/4931e703-408f-4edd-8c81-53c3a6e868c1" />

**3. `rsync -avh --dry-run --delete` — simulasi sinkronisasi & penghapusan sebelum eksekusi beneran, ditandai `(DRY RUN)` di baris terakhir:**

<img width="803" height="189" alt="image" src="https://github.com/user-attachments/assets/12338564-65f7-49b2-984d-2cd1a0bf8cc3" />

**4. `crontab -l` — jadwal otomasi backup `rsync` tiap jam 2 pagi, memanfaatkan format 5 kolom (menit, jam, tanggal, bulan, hari):**

<img width="882" height="500" alt="image" src="https://github.com/user-attachments/assets/e063c101-12da-4fb4-b1a2-fd6470b83637" />


