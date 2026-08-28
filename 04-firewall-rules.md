# Configure Firewall Rules Properly

## 🎯 Tujuan
Mengonfigurasi dan memahami firewall (UFW) di Ubuntu Server untuk mengontrol lalu lintas jaringan yang masuk, termasuk cara mengizinkan/melarang service tertentu dan memverifikasi status port menggunakan tools tambahan seperti Nmap dan Telnet.

## 🛠️ Environment
- **Firewall:** UFW (Uncomplicated Firewall)
- **Tools tambahan:** Nmap, Telnet, cURL

## 📋 Konfigurasi UFW

**Cek status firewall & default rules:**
```bash
sudo ufw status verbose
```
Menampilkan status aktif/tidak, port yang di-allow, serta default policy firewall.

**Lihat aplikasi yang punya UFW profile bawaan:**
```bash
sudo ufw app list
```

**Mengizinkan service tertentu (contoh: Apache Full):**
```bash
sudo ufw allow "Apache Full"
```

**Mencabut izin yang sudah diberikan:**
```bash
sudo ufw delete allow "Apache Full"
```

**Mengaktifkan/menonaktifkan firewall:**
```bash
sudo ufw enable
sudo ufw disable
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
*(opsional — ceritain kalau ada kendala, misal port yang salah di-allow, atau hasil scanning Nmap yang menarik)*

## 📸 Screenshot
*(tambahkan di sini)*
