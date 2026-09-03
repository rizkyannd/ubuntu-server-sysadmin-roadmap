# Write Simple Bash Script untuk Automation

## 🧭 Konteks
Step ini saya lakuin buat belajar dasar bash scripting — mulai dari error handling, input/output, variable, conditional, loop, function, command line arguments, sampai exit code.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `bash`, text editor (`nano`)

## 📋 Yang Saya Praktikkan

### 1. Shebang & error handling
```bash
#!/bin/bash
set -u
set -e
set -o pipefail
```

### 2. Input & output
```bash
read -p "Masukkan username: " USERNAME
echo "Hello, sysadmin!"
echo "Hallo, $USERNAME"
```

### 3. Variable & pemanggilan
```bash
TODAY=$(date +%Y-%m-%d)
echo "Hari ini: $TODAY"
```

### 4. Conditional (if/elif/else)
```bash
DISK_USAGE=$(df / | awk 'NR==2 {print$5}' | tr -d '%')
if [ "$DISK_USAGE" -gt 80 ]; then
        echo "Disk usage: [WARNING]$DISK_USAGE% !!!"
elif [ "$DISK_USAGE" -gt 60 ]; then
        echo "Disk usage: [CAUTION]$DISK_USAGE%"
else
        echo "Disk usage: [OK]$DISK_USAGE%"
fi

if [ -d "test/backup" ]; then
        echo "folder backup ada"
else
        mkdir -p test/backup
        echo "folder backup dibuat"
fi
```
Cek disk usage server dengan threshold bertingkat, dan pastikan folder backup tersedia sebelum dipakai.

### 5. Loop (for & while)
```bash
for FILE in /home/kaks/*; do
        echo "FILE - {$FILE}"
done

COUNT=1
while [ $COUNT -lt 6 ]; do
        echo "Count: $COUNT"
        COUNT=$((COUNT + 1))
done
```

### 6. Function
```bash
backup_folder() {
        local SOURCE=$1
        local DEST=$2
        tar -czf "$DEST/backup-$(date +%Y-%m-%d).tar.gz" "$SOURCE"
        echo "Backup selesai: $DEST"
}

read -p "Source backup: " sour
read -p "Destination backup: " dest
backup_folder "$sour" "$dest"
```
Function `backup_folder` compress folder source jadi arsip `.tar.gz` di lokasi destination, nama file otomatis pakai tanggal.

### 7. Command line arguments
```bash
echo "Nama file     : $0"
echo "Argument ke-1 : $1"
echo "Argument ke-2 : $2"
echo "Argument ke-3 : $3"
echo "Semua argument: $@"
echo "Jumlah argument: $#"
```

### 8. Exit code
```bash
read -p "Nama folder baru: " FOLDERNEW
mkdir "$FOLDERNEW"
if [ $? -eq 0 ]; then
        echo "==> FOLDER '$FOLDERNEW' BERHASIL DIBUAT"
else
        echo "==> GAGAL MEMBUAT FOLDER"
        exit 1
fi
```

## 🧩 Catatan

**Kaget ternyata bash nggak otomatis stop kalau ada error**

Awalnya saya kira bash itu bakal berperilaku sama kayak Python di VSCode — kalau ada baris yang error, program otomatis berhenti dan nunjukin pesan errornya. Asumsi ini muncul karena command bash biasa (yang saya jalanin manual di terminal) emang selalu ngasih informasi kalau ada yang salah, misalnya "direktori tidak ditemukan" atau "command tidak ditemukan". Ternyata itu beda konteks — command yang saya jalanin manual di terminal memang langsung nunjukin errornya, tapi kalau dijalanin di dalam script, defaultnya bash tetap lanjut ke baris berikutnya meskipun ada command sebelumnya yang gagal. Dari situ saya baru ngerti kenapa `set -u`, `set -e`, dan `set -o pipefail` perlu ditambahin secara eksplisit di awal script.

**Baru tau konsep exit code (`$?`)**

Sebelum ini saya nggak kepikiran kalau tiap command yang dijalanin itu punya "kode hasil" tersendiri yang bisa dicek. Awalnya bingung gimana cara tau suatu command beneran berhasil atau nggak, selain cuma liat pesan error yang muncul di layar. Ternyata ada `$?` yang nyimpen exit code dari command sebelumnya, dan yang bikin saya baru sadar itu ternyata bisa dipakai buat perkondisian (`if`/`else`) buat nentuin command apa yang dijalanin selanjutnya — misalnya kalau `mkdir` berhasil (`$? -eq 0`), baru lanjut ke command berikutnya, tapi kalau gagal, program bisa langsung `exit` atau ngasih pesan error yang lebih jelas.

**Bash nggak wajib indentation**

Beda dari Python yang mewajibkan indentasi sebagai bagian dari struktur kode, di bash indentation itu sifatnya opsional. Meskipun begitu saya tetap konsisten pakai indentation di script biar lebih gampang dibaca dan dipahami. Dari segi penulisan, syntax bash juga kerasa sedikit lebih teknis dibanding Python. Tapi karena logika dasarnya (variable, conditional, loop, function) udah saya kenal dari Python dan Java sebelumnya, jadi nggak terlalu kesulitan memahami cara penulisan di bash ini.

## 📸 Screenshot
<!-- belum diisi -->
