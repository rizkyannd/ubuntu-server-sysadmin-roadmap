# Step 1: Install Ubuntu Server dari Nol

## 🎯 Tujuan
Menginstal Ubuntu Server dari awal di lingkungan virtual (VirtualBox) sebagai fondasi environment untuk seluruh roadmap sysadmin ini — mulai dari bare OS hingga siap dikonfigurasi lebih lanjut (networking, SSH, firewall, dst).

## 🛠️ Environment
- **Hypervisor:** VirtualBox
- **OS:** Ubuntu Server (isi versi yang kamu pakai, misal 22.04 LTS / 24.04 LTS)
- **Spesifikasi VM:** (isi RAM, CPU, storage yang dialokasikan)
- **Hostname:** ubuntu-server
- **Username:** kaks

## 📋 Langkah-Langkah
1. Download ISO Ubuntu Server dari situs resmi ubuntu.com
2. Buat Virtual Machine baru di VirtualBox (alokasi RAM, CPU, disk)
3. Mount ISO ke virtual optical drive
4. Boot VM dan mulai proses instalasi
5. Pilih bahasa & keyboard layout
6. Konfigurasi network (DHCP saat instalasi awal)
7. Setup partisi disk (guided/manual — sebutkan mana yang dipakai)
8. Buat user & set hostname (`kaks` / `ubuntu-server`)
9. Install OpenSSH server saat instalasi (jika dicentang saat itu)
10. Selesaikan instalasi & reboot
11. Login pertama kali dan update sistem (`sudo apt update && sudo apt upgrade`)

## ⚙️ Verifikasi
```bash
lsb_release -a        # Cek versi Ubuntu Server
hostnamectl            # Cek hostname
whoami                 # Cek user aktif
```

## 🧩 Catatan
Step ini dilakukan sebelum roadmap 12-step ini dibuat, jadi tidak ada log troubleshooting detail. VM hasil instalasi ini menjadi basis untuk seluruh step selanjutnya (2–12).

## 📸 Screenshot
*(tambahkan screenshot proses instalasi jika ada, atau screenshot hasil `neofetch`/`hostnamectl` sebagai bukti environment)*
