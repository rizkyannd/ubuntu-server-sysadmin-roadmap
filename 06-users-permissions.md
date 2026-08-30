# Manage Users & Permissions Securely

## 🧭 Konteks
Step ini saya lakuin buat latihan ngatur akses multi-user di server — bikin user & group baru, ngatur siapa punya akses ke folder/file apa, dan gimana cara ngasih atau nyabut permission dengan benar. Basicnya sederhana, tapi pas pertama kali praktik saya masih sering ketuker beberapa hal (dijelasin di bagian Catatan).

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `adduser`, `addgroup`, `usermod`, `deluser`, `chmod`, `chown`, `chgrp`

## 📋 Yang Saya Praktikkan

**1. Bikin user & group baru:**
```bash
sudo adduser budi
sudo addgroup mahasiswa
```

**2. Cek user & group yang terdaftar:**
```bash
cat /etc/passwd
cut -d: -f1 /etc/passwd
cat /etc/group
cut -d: -f1 /etc/group
```

**3. Masukin/keluarin user dari group:**
```bash
sudo usermod -aG mahasiswa budi
sudo deluser budi mahasiswa
```

**4. Hapus user & group:**
```bash
sudo deluser nama-user
sudo groupdel nama-group
```

**5. Ubah permission & ownership:**
```bash
sudo chmod -R 777 nama-file-atau-folder
sudo chmod ugo+rwx nama-file-atau-folder
sudo chown kaks:mahasiswa ubsi
```

**6. Sticky bit:**
```bash
sudo chmod 1777 nama-folder
sudo chmod +t nama-folder
sudo chmod -t nama-folder
```

## ⚙️ Verifikasi
```bash
ls -l nama-file-atau-folder
```

## 🧩 Catatan — Hal yang Bikin Saya Bingung

**1. Ketuker angka permission (4/2/1)**
Awal belajar, saya sering lupa/ketuker mana yang `r`, `w`, `x` pas dikonversi ke angka. Akhirnya saya biasain hafalin urutannya sebagai satu pola tetap: **r-w-x selalu dibaca dari kiri ke kanan = 4-2-1**, jadi kalau ada permission `rwx` tinggal jumlahin 4+2+1=7. Kalau cuma `rw-` berarti 4+2=6 (posisi `x` kosong = 0). Setelah saya paksa selalu baca urutan hurufnya dulu sebelum konversi ke angka (bukan langsung hafalin angkanya doang), jadi jarang ketuker lagi.

**2. Ketuker urutan argumen di `usermod -aG`**
Command `sudo usermod -aG NAMA_GROUP NAMA_USER` — saya berkali-kali salah nulis urutannya, kepikiran "kan mau nambahin **user** ke group, jadi user disebut duluan." Padahal urutan command-nya kebalik: **group dulu, baru user**. Karena flag `-aG` artinya "append ke Group ini", jadi argumen pertama setelah flag itu **objek yang dituju** (group), argumen kedua **subjek yang ditambahin** (user). Sekarang saya ingatnya dengan baca command-nya sebagai kalimat: "tambahkan-ke-Group [nama group] [nama user]".

## 📸 Screenshot
<img width="1072" height="134" alt="image" src="https://github.com/user-attachments/assets/3210591a-8e82-4b86-ac27-943653c62532" />
<img width="1079" height="259" alt="image" src="https://github.com/user-attachments/assets/15b86a96-86c2-4496-99bb-294716b6fc7e" />


<!-- ls -l sebelum/sesudah chmod, dan output cat /etc/group sebelum/sesudah usermod -aG -->
