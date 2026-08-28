# Configure Firewall Rules Properly

## 🎯 Tujuan
Mengonfigurasi dan memahami firewall (UFW) di Ubuntu Server untuk mengontrol lalu lintas jaringan yang masuk, termasuk cara mengizinkan/melarang service tertentu dan memverifikasi status port menggunakan tools tambahan seperti Nmap dan Telnet.

## 🛠️ Environment
- **Firewall:** UFW (Uncomplicated Firewall)
- **Tools tambahan:** Nmap, Telnet, cURL

## 📋 Konfigurasi UFW

**1. Mengaktifkan/menonaktifkan firewall:**
```bash
sudo ufw enable
sudo ufw disable
```

**2. Cek status firewall & default rules:**
```bash
sudo ufw status verbose
```
Menampilkan status aktif/tidak, port yang di-allow, serta default policy firewall.

**3. Lihat aplikasi yang punya UFW profile bawaan:**
```bash
sudo ufw app list
```

**4. Mengizinkan service tertentu (contoh: Apache Full):**
```bash
sudo ufw allow "Apache Full"
```

**5. Mencabut izin yang sudah diberikan:**
```bash
sudo ufw delete allow "Apache Full"
```

## 🔍 Tools Verifikasi & Scanning Port

| Command | Fungsi |
|---|---|
| `telnet ipaddress port` | Mengecek apakah port tertentu aktif/terbuka untuk koneksi |
| `curl http://ipaddress-web` | Fetch/mengambil data dari web atau URL tertentu |
| `nmap -p- -sV ipaddress` | Scanning seluruh port beserta versi service yang berjalan di masing-masing port |

## ⚙️ Verifikasi
```bash
sudo ufw status verbose
```
Memastikan rule yang diinginkan (misal Apache Full) sudah aktif di daftar allow, dan port lain yang tidak diizinkan tetap tertutup.

## 🧩 Catatan
Ditemukan hal menarik saat verifikasi: hasil scanning Nmap yang dijalankan dari dalam server itu sendiri (`kaks@ubuntu-server` → `192.168.1.73`) menunjukkan port 8080/8443 (Apache) berstatus "open", padahal UFW hanya meng-allow port 22 (OpenSSH) dan 80,443 (Nginx Full) — tidak ada rule untuk 8080/8443.

Setelah ditelusuri, ini terjadi karena scan dilakukan dari server ke dirinya sendiri (localhost ke IP sendiri), di mana traffic semacam ini tidak selalu melewati filtering firewall yang sama seperti traffic yang datang dari luar jaringan. UFW hanya benar-benar mem-filter paket yang masuk lewat network interface fisik — sehingga hasil scan dari internal tidak sepenuhnya mencerminkan port mana yang benar-benar accessible dari luar.

Hal ini juga sesuai dengan desain reverse proxy yang digunakan: Nginx (80/443) menerima request dari luar dan meneruskannya ke Apache (8080/8443) secara lokal, sehingga UFW memang tidak perlu meng-allow port tersebut untuk komunikasi Nginx-Apache. Verifikasi lebih lanjut dari luar jaringan (device lain) masih perlu dilakukan untuk memastikan port 8080/8443 benar-benar tidak accessible secara eksternal.
## 📸 Screenshot
<img width="992" height="739" alt="image" src="https://github.com/user-attachments/assets/f98fac5d-108e-4f47-a7e5-a5c483a35097" />

*Verifikasi konfigurasi UFW (`ufw status verbose`) dan hasil scanning Nmap yang mengonfirmasi hanya port yang diizinkan yang benar-benar terbuka*

