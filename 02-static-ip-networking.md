# Configure Static IP & Networking

## 🎯 Tujuan
Mengonfigurasi static IP pada interface jaringan Ubuntu Server menggunakan Netplan, serta memahami tools dasar untuk diagnosa dan monitoring koneksi jaringan.

## 🛠️ Environment
- **Interface:** enp0s3
- **MAC Address:** 08:00:27:xx:xx:xx
- **Netplan version:** 2

## 📋 Konfigurasi Netplan
**File konfigurasi:** `/etc/netplan/00-installer-config.yaml`

Config awal (default DHCP, dibuat otomatis oleh `subiquity` saat instalasi):
```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3:
      dhcp4: yes
      dhcp6: yes
      match:
        macaddress: 08:00:27:xx:xx:xx
      set-name: enp0s3
  version: 2
```


Config final (static IP):
```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.1.73/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
      match:
        macaddress: 08:00:27:xx:xx:xx
      set-name: enp0s3
  version: 2
```

Diterapkan dengan:
```bash
sudo netplan apply
```

## 🔍 Tools Diagnostik Jaringan yang Dipelajari

| Command | Fungsi |
|---|---|
| `ping google.com` / `ping 8.8.8.8` | Mengecek konektivitas jaringan aktif atau tidak |
| `traceroute google.com` | Melacak jalur/hop yang dilalui packet menuju tujuan |
| `netstat -tuln` / `ss -tuln` | Melihat port yang sedang terbuka/listening (`t`=TCP, `u`=UDP, `l`=listening socket, `n`=numeric) |
| `route -n` | Melihat kernel routing table (jalur keluar/masuk paket) |
| `nslookup google.com` | Mencari IP address dari sebuah domain (atau sebaliknya) |
| `ethtool enp0s3` | Melihat detail interface jaringan di level fisik/hardware |

## ⚙️ Verifikasi
```bash
ip a                    # Cek IP address aktif di interface
ping -c 4 8.8.8.8        # Tes konektivitas ke internet
```

## 🧩 Catatan
Config Netplan awal masih menggunakan DHCP (default hasil instalasi). Proses perubahan ke static IP berjalan lancar tanpa kendala teknis. 

Namun ditemukan pemahaman baru: static IP (`192.168.1.73`) yang di-set berdasarkan gateway WiFi rumah hanya berfungsi selama VM terhubung ke jaringan yang sama. Saat mencoba menghubungkan VirtualBox ke jaringan hotspot HP (subnet berbeda), koneksi SSH gagal karena IP dan gateway yang di-set tidak sesuai dengan subnet jaringan hotspot tersebut. Ini memperjelas bahwa static IP terikat pada satu jaringan spesifik, berbeda dengan DHCP yang otomatis menyesuaikan saat berpindah jaringan.


## 📸 Screenshot
<img width="1282" height="854" alt="image" src="https://github.com/user-attachments/assets/bcc9f618-2b1d-40ad-b336-0bac6b4771b4" />
