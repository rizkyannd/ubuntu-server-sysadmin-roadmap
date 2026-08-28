# Setup & Manage SSH Keys

## 🎯 Tujuan
Mengonfigurasi autentikasi SSH berbasis key-pair (public/private key) untuk login ke Ubuntu Server, menggantikan autentikasi berbasis password dengan metode yang lebih aman.

## 🛠️ Environment
- **Algoritma:** ED25519
- **Client:** Windows (host laptop)
- **Server:** Ubuntu Server (VirtualBox)

## 📋 Proses Setup

**1. Generate key pair di laptop (host/Windows)**
```bash
ssh-keygen -t ed25519 -C "label_nama"
```

**2. Salin isi public key**
```bash
type ed25519.pub
```

**3. Login ke server (password, untuk setup awal)**
```bash
ssh username@ipaddress
```

**4. Tambahkan public key ke server**
```bash
nano ~/.ssh/authorized_keys
```


## ⚙️ Verifikasi
Login via SSH key: **berhasil**, langsung masuk tanpa password akun.

## 🧩 Catatan
Autentikasi berbasis SSH key berhasil dikonfigurasi dan berfungsi dengan baik.

## 📸 Screenshot
<img width="960" height="891" alt="image" src="https://github.com/user-attachments/assets/25ab69c1-3df0-46de-af26-4205c09ed22e" />
