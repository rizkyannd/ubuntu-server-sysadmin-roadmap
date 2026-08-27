# Step 1: Install Ubuntu Server dari Nol

## 🎯 Tujuan
Menginstal Ubuntu Server dari awal (bare OS) di lingkungan virtual VirtualBox, sebagai fondasi untuk seluruh konfigurasi lanjutan di roadmap ini — networking, SSH, firewall, dan seterusnya.

## 🛠️ Environment
- **Hypervisor:** VirtualBox
- **OS:** Ubuntu Server (26.04 LTS)
- **Spesifikasi VM:** 2 GB RAM, 2 CPU cores, 20 GB storage (dynamically allocated)
- **Hostname:** ubuntu-server
- **Username:** kaks

## 📋 Langkah-Langkah
1. Download ISO Ubuntu Server dari situs resmi [ubuntu.com](https://ubuntu.com/download/server)
2. Klik "New" di VirtualBox untuk membuat VM baru
3. Mount ISO ke virtual optical drive
4. Alokasi RAM, CPU, dan disk untuk VM
5. Boot VM dan mulai proses instalasi
6. Pilih bahasa & keyboard layout
7. Konfigurasi network (DHCP saat instalasi awal)
8. Setup partisi disk (guided)
9. Buat user & set hostname (`kaks` / `ubuntu-server`)
10. Install OpenSSH server saat instalasi
11. Selesaikan instalasi & reboot
12. Login pertama kali dan update sistem (`sudo apt update && sudo apt upgrade`)

## ⚙️ Verifikasi
```bash
lsb_release -a         # Cek versi Ubuntu Server
hostnamectl            # Cek hostname
whoami                 # Cek user aktif
```

## 🧩 Catatan
Step ini dilakukan sebelum roadmap 12-step ini dibuat.

## 📸 Screenshot
<img width="612" height="531" alt="image" src="https://github.com/user-attachments/assets/dbbed726-801c-4277-83e7-01be2c74a13c" />

