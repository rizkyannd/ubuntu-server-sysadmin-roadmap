# Monitor System Resources

## 🧭 Konteks
Step ini saya lakuin buat belajar cara mantau kondisi server dari sisi CPU, memory, disk, dan network — baik secara real-time maupun lewat history log. Termasuk juga cara identifikasi proses yang bermasalah dan cara menghentikannya kalau perlu.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `htop`, `glances`, `vmstat`, `mpstat`, `iostat`, `iftop`, `nload`, `sar`, `tcpdump`, `mtr`, `ss`, `ps`, `watch`

## 📋 Yang Saya Praktikkan

### CPU & Load
```bash
htop
uptime
vmstat 1 5
nproc
mpstat -P ALL 1 5
```
`htop` buat monitoring core CPU & load process secara real-time. `uptime` nampilin load average. `vmstat 1 5` ngambil snapshot statistik CPU & memory tiap 1 detik, diulang 5x. `nproc` nampilin jumlah core CPU di server. `mpstat -P ALL 1 5` nampilin rincian CPU per-core, lebih detail dibanding `htop`.

**Manual adjust priority proses:**
```bash
sleep 300 &
sudo renice -n 10 -p <PID>
```
PID didapat langsung dari output `sleep 300 &` (contoh: `[1] 3114`).
Range nice: -20 (prioritas tertinggi) sampai 19 (prioritas terendah), 0 = normal.

### Memory
```bash
free -h
cat /proc/meminfo
```
`free -h` buat monitoring memory secara umum. `cat /proc/meminfo` buat monitoring memory yang lebih detail.

### Disk
```bash
df -h
du -sh
iostat -x 1 5
lsblk
```
`df -h` buat lihat space disk terpakai/tersisa. `du -sh` buat lihat total ukuran satu folder spesifik. `iostat -x 1 5` buat lihat seberapa sibuk disk I/O (kecepatan baca/tulis dan beban kerja disk):
- 0–30% → disk santai
- 30–70% → disk mulai kerja, masih wajar
- 70–100% → disk jadi bottleneck (tanda I/O tinggi)

`lsblk` buat lihat struktur disk/partisi.

### Network
```bash
ss -tulnp
ip -s link
sudo iftop
sudo tcpdump
nload
ping google.com
traceroute google.com
mtr google.com
```
`ss -tulnp` buat lihat port yang listen. `ip -s link` buat statistik traffic per interface. `iftop` nampilin bandwidth real-time per koneksi (IP) — dipakai buat cari tahu proses/user mana yang makan bandwidth banyak. `tcpdump` buat debugging masalah spesifik atau investigasi keamanan. `nload` versi lebih simpel dari monitoring bandwidth real-time. `ping` buat cek konektivitas dasar. `traceroute` buat lacak jalur paket data ke tujuan. `mtr` gabungan `ping` + `traceroute` — bisa lihat hop mana yang packet loss-nya tinggi secara konsisten, bukan cuma sekali coba.

### Process Management
```bash
ps aux
ps aux | grep apache2
ps aux --sort=-%cpu | head
ps aux --sort=-%mem | head
pidof apache2
kill -9 PID
kill -15 PID
pkill apache2
pkill -u kaks
```
`ps aux` nampilin semua proses dari semua user yang online. `ps aux --sort=-%cpu | head` urutkan proses berdasarkan %CPU dari besar ke kecil (tanda `-` di depan), ambil 10 baris teratas. Konsep yang sama berlaku buat `--sort=-%mem`. `pidof` buat cari PID dari nama proses. `kill -9` matikan proses secara paksa, `kill -15` matikan secara baik-baik. `pkill` matikan proses by nama tanpa perlu tahu PID — otomatis matikan semua proses dengan nama itu (baik-baik). `pkill -u kaks` matikan semua proses milik user tertentu.

### Overview & History Log
```bash
glances
ls -lh /var/log/sysstat/
sar -u -f /var/log/sysstat/sa10
watch -n 2 'free -h'
```
`glances` buat overview kondisi server secara keseluruhan dalam satu tampilan. `ls -lh /var/log/sysstat/` nampilin histori file log CPU usage per 10 menit selama server aktif. `sar -u -f /var/log/sysstat/sa10` buat baca isi file history log tersebut. `watch -n 2 'free -h'` buat jalanin command secara otomatis berulang tiap interval tertentu (di sini 2 detik) tanpa perlu ngetik ulang manual.

## ⚙️ Verifikasi
Cek usage CPU, Memory, I/O Disk, dan Network — pastikan semua command di atas menghasilkan output yang sesuai dengan kondisi server saat itu.


## 🧩 Catatan

**Bingung baca output command monitoring di awal**
Karena sebelumnya saya belum pernah pakai command-command monitoring kayak `vmstat`, `mpstat`, `iostat`, `sar`, dll, pas pertama kali jalanin outputnya kerasa berat — banyak kolom/istilah yang muncul sekaligus, beda sama command yang lebih familiar kayak `ls` atau `df -h` yang outputnya straightforward.

**`htop` — butuh waktu paling lama buat dipahami**
Dari semua command monitoring, `htop` yang paling lama saya pelajari karena outputnya paling detail dan section-section-nya banyak (ada bagian CPU per-core di atas, memory/swap bar, sampai list proses di bawah dengan banyak kolom sekaligus). Saya perlu waktu buat ngerti tiap section itu ngasih info apa dan gimana cara bacanya secara keseluruhan, bukan cuma lihat satu angka doang.

**Baru paham konsep `nice` (prioritas proses)**
Saya sempat berhenti beberapa menit khusus buat mahamin konsep `nice`. Sebelumnya saya nggak kepikiran sama sekali kalau proses di Linux itu dijalankan berdasarkan prioritas — saya kira semua proses diperlakukan "sama rata" sama sistem. Ternyata ada mekanisme `nice value` (-20 sampai 19) yang nentuin proses mana yang didahulukan CPU-nya, dan itu bisa diatur manual pakai `renice`. Ini insight baru yang cukup mengubah cara saya mikir soal gimana OS sebenarnya ngatur eksekusi proses di belakang layar.

**`glances` jauh lebih cepat dipahami**
Berbeda dengan `htop` yang butuh waktu lama, begitu sampai ke `glances` saya jadi lebih cepat paham karena tampilannya mirip konsepnya sama `htop` (overview CPU, memory, proses dalam satu layar) — jadi pemahaman yang udah saya bangun dari `htop` sebelumnya kepake lagi di sini, tinggal menyesuaikan sama layout dan section tambahan yang beda.

## 📸 Screenshot
**1. `htop` — overview CPU per-core, memory, load average, dan list proses:**
<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/83401c33-8b43-4347-8196-dc413e715b16" />

**2. `renice` — mengubah prioritas proses `sleep` dari nice value 0 ke 10, diverifikasi lewat `ps -o pid,ni,comm`:**
<img width="1111" height="252" alt="image" src="https://github.com/user-attachments/assets/fe5a99dc-30f9-4201-a351-91cbec07ed91" />

**3. `glances` — overview lebih lengkap dari `htop` (nambah info disk I/O per-partisi, network Rx/Tx, filesystem usage, sampai sensor) dalam satu layar:**
<img width="1314" height="867" alt="image" src="https://github.com/user-attachments/assets/b009fb50-a7aa-4314-8f17-562d811d813c" />

**4. `iostat -x 1 5` — snapshot I/O disk 5x berturut-turut, kolom `%util` jadi acuan seberapa sibuk disk:**
<img width="1917" height="933" alt="image" src="https://github.com/user-attachments/assets/99627d3a-880b-459c-b6a8-d58999ab35bc" />





