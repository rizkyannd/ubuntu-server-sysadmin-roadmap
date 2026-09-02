# Read & Understand Logs

## 🧭 Konteks
Step ini saya lakuin buat belajar cara baca dan analisis log system — baik lewat `journalctl` (systemd) maupun file log tradisional di `/var/log/`. Termasuk cara filter log berdasarkan service, waktu, dan level severity, sampai cara nyari pola tertentu (misal percobaan login gagal) di dalam file log yang panjang.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `journalctl`, `tail`, `grep`, `less`

## 📋 Yang Saya Praktikkan

### Filter log by service (unit)
```bash
journalctl -u ssh
journalctl -u nginx
```
Fokus ke log satu service tertentu tanpa keganggu log service lain.

### Filter log by severity & waktu
```bash
journalctl -p err -b
journalctl -u ssh -p err -b
journalctl --since today
journalctl --since "1 hour ago"
journalctl -b
journalctl -f
```
Filter berdasarkan level severity (error only), boot session, dan rentang waktu tertentu. `-f` buat monitoring log real-time.

### Baca file log tradisional
```bash
tail -f /var/log/auth.log
tail -f /var/log/syslog
sudo tail -50 /var/log/apache2/error.log
```
`/var/log/auth.log` merekam semua percobaan login dan penggunaan `sudo`. `-f` buat monitoring real-time, `tail -50` buat lihat 50 baris terakhir tanpa follow.

### Cari pola spesifik di log
```bash
grep "Failed password" /var/log/auth.log
grep "Failed password" /var/log/auth.log | wc -l
grep -i "error" /var/log/syslog | tail -20
```
Nyari dan ngitung pattern tertentu di log (contoh: jumlah percobaan login gagal).

### Baca file panjang lewat pager
```bash
less /var/log/syslog
```
Baca file log panjang secara bertahap per halaman — mekanisme yang sama dipakai `journalctl` di balik layar buat nampilin output panjangnya.

## 🧩 Catatan

**Bingung baca output `journalctl` yang kelihatan "belum selesai"**
Pertama kali jalanin `journalctl -u ssh`, saya sempat bingung karena tampilannya kelihatan kayak macet atau belum selesai — padahal itu cuma nampilin baris 1-50 dari total ribuan baris log yang ada, dan nunggu input dari saya buat lanjut. Ternyata itu mekanisme **pager**: output panjang ditahan dan ditampilin per halaman, bukan langsung ditampilkan semua sekaligus. Setelah paham konsep ini, saya baru ngerti cara navigasinya: `space` buat scroll turun, `b` buat scroll naik, `/kata-kunci` lalu Enter buat search dan `n` buat lompat ke kecocokan berikutnya, dan `q` buat keluar. Insight ini langsung kepake pas belajar `less` di command lain, karena ternyata mekanismenya sama persis.

**Bingung milih antara `tail -f`, `journalctl -f`, dan `less` — kapan cocok dipakai yang mana**
Ketiga command ini kelihatan nampilin hal yang mirip (isi log), jadi awalnya saya bingung nentuin mana yang paling relevan dipakai di kondisi tertentu. Setelah dicoba satu-satu, saya baru ngerti bedanya ada di **konteks pemakaian**, bukan sekadar tampilan.


## 📸 Screenshot

**1. `journalctl -u ssh` — output kepotong pager (`Lines 1-50` di pojok kiri bawah), berisi riwayat SSH service start/stop antar-boot session dan beberapa authentication failure:**

<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/c3e1c19f-788a-418f-b718-0c0c27b689c6" />

**2. `journalctl -u ssh -f` — mode real-time (follow), otomatis nampilin log baru begitu ada koneksi SSH masuk, tanpa indikator `Lines 1-50` seperti mode pager biasa:**

<img width="1604" height="195" alt="image" src="https://github.com/user-attachments/assets/806465a7-3542-4687-a7dc-060b5c7a314f" />

**3. `journalctl -u ssh | grep -i "failed password"` — mencari & menghitung percobaan login gagal (23 kali), termasuk 3 percobaan berturut-turut dari subnet berbeda (`10.126.120.22`) dalam rentang waktu singkat:**

<img width="1201" height="495" alt="image" src="https://github.com/user-attachments/assets/7f9140c3-222c-442e-bcd0-85d15ae83dd9" />

**4. `less /var/log/syslog` — file log dibuka lewat pager, indikator nama file muncul di pojok kiri bawah (`/var/log/syslog`), mekanisme serupa dengan `journalctl`:**

<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/5b714728-8f65-4ef3-9142-88c5a7df3e7a" />


