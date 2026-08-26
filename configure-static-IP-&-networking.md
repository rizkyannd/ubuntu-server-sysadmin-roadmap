# Step 2: Configure Static IP & Networking

## 🎯 Tujuan
Mengonfigurasi static IP pada interface jaringan Ubuntu Server menggunakan Netplan, serta memahami tools dasar untuk diagnosa dan monitoring koneksi jaringan.

## 🛠️ Environment
- **Interface:** enp0s3
- **MAC Address:** 08:00:27:b6:d2:56
- **Netplan version:** 2

## 📋 Konfigurasi Netplan

File konfigurasi: `/etc/netplan/00-installer-config.yaml` (nama file bisa beda, sesuaikan)

Berikut config awal (masih DHCP, dibuat otomatis oleh 'subiquity' saat instalasi):
```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3:
      dhcp4: yes
      dhcp6: yes
      match:
        macaddress: 08:00:27:b6:d2:56
      set-name: enp0s3
  version: 2
```

> ⚠️ **TODO:** Tambahkan konfigurasi static IP final di sini (contoh format di bawah), lalu jelaskan IP/gateway/DNS yang dipakai.
```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.x.x/24]
      gateway4: 192.168.x.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
      match:
        macaddress: 08:00:27:b6:d2:56
      set-name: enp0s3
  version: 2
```

Setelah edit file, apply dengan:
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
Config Netplan awal masih menggunakan DHCP (default hasil instalasi). *(Tambahkan cerita singkat proses mengubahnya ke static IP, kendala yang ditemui, dan hasil akhirnya di sini.)*
