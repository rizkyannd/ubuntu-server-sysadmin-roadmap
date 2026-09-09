# Monitor System Resources

## 🧭 Context
In this step, I learned how to monitor server conditions regarding CPU, memory, disk, and network performance—both in real-time and through historical logs. This also covers identifying problematic processes and terminating them when necessary.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `htop`, `glances`, `vmstat`, `mpstat`, `iostat`, `iftop`, `nload`, `sar`, `tcpdump`, `mtr`, `ss`, `ps`, `watch`

## 📋 Hands-On Practice

### CPU & Load
```bash
htop
uptime
vmstat 1 5
nproc
mpstat -P ALL 1 5
```
`htop` provides real-time monitoring of CPU cores and process load. `uptime` displays load averages. `vmstat 1 5` captures CPU and memory stat snapshots every 1 second, repeating 5 times. `nproc` displays the total number of CPU cores available on the server. `mpstat -P ALL 1 5` outputs detailed per-core CPU metrics, offering more granularity than `htop`.

**Manually Adjusting Process Priority:**
```bash
sleep 300 &
sudo renice -n 10 -p <PID>
```
The PID is obtained directly from the output of `sleep 300 &` (e.g., `[1] 3114`).
Nice value range: -20 (highest priority) to 19 (lowest priority), where 0 represents the default/normal priority.

### Memory
```bash
free -h
cat /proc/meminfo
```
`free -h` provides a general overview of memory usage. `cat /proc/meminfo` delivers detailed memory usage breakdowns.

### Disk
```bash
df -h
du -sh
iostat -x 1 5
lsblk
```
`df -h` inspects used and remaining disk space. `du -sh` calculates total file sizes within a specific directory. `iostat -x 1 5` analyzes disk I/O activity (read/write speeds and workload load):
- 0–30% → idle disk
- 30–70% → moderate workload
- 70–100% → I/O bottleneck indicator (high I/O load)

`lsblk` inspects the overall block device and partition structures.

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
`ss -tulnp` lists open listening ports. `ip -s link` displays interface traffic statistics. `iftop` shows real-time bandwidth consumption per IP connection—useful for identifying processes or users consuming high bandwidth. `tcpdump` aids in packet debugging and security investigations. `nload` offers a simpler real-time bandwidth visualization. `ping` verifies basic connectivity. `traceroute` tracks network hop paths to a target destination. `mtr` combines `ping` and `traceroute` into a continuous tool to spot specific hops suffering from consistent packet loss.

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
`ps aux` prints active processes across all users. `ps aux --sort=-%cpu | head` sorts processes by CPU utilization in descending order (denoted by the `-` prefix) and returns the top 10 rows. The same sorting logic applies to `--sort=-%mem`. `pidof` retrieves the PID associated with a process name. `kill -9` forcefully kills a process, while `kill -15` terminates it gracefully. `pkill` targets processes by name without needing a PID—automatically killing all matching instances gracefully. `pkill -u kaks` terminates all active processes owned by a specified user.

### Overview & History Logs
```bash
glances
ls -lh /var/log/sysstat/
sar -u -f /var/log/sysstat/sa10
watch -n 2 'free -h'
```
`glances` renders a comprehensive server overview on a single screen. `ls -lh /var/log/sysstat/` lists historical 10-minute CPU usage log files generated during system activity. `sar -u -f /var/log/sysstat/sa10` reads specific historical log contents. `watch -n 2 'free -h'` repeatedly runs a command at a set interval (every 2 seconds) to avoid manual re-execution.

## ⚙️ Verification
Check CPU, Memory, Disk I/O, and Network utilization—ensure all the commands above produce output matching the server's real-time system state.

## 🧩 Key Takeaways

**Initial Confusion Reading System Monitoring Outputs**

Because I hadn't previously used monitoring tools like `vmstat`, `mpstat`, `iostat`, `sar`, etc., analyzing the initial output felt overwhelming. They display multiple metric columns and technical parameters simultaneously, unlike simpler, more familiar tools such as `ls` or `df -h`.

**`htop` Required the Most Time to Master**

Of all the monitoring commands, `htop` took the longest time to navigate because of its density and section layouts—including per-core CPU bars at the top, memory/swap utilization bars, and a multi-column process table below. Understanding what each section represented and reading them holistically required dedicated practice.

**Understanding Process Priorities via `nice` Values**

I took time to focus specifically on process priorities. I hadn't considered that Linux handles process scheduling based on priority levels, assuming instead that all processes received equal system treatment. Discovering the `nice value` mechanism (-20 to 19)—which allows users to manually influence CPU scheduling via `renice`—offered valuable insight into kernel-level process management.

**Grasping `glances` Quickly After `htop`**

In contrast to `htop`, picking up `glances` went smoothly because both tools share a similar structural design (rendering CPU, memory, and process overviews on a single dashboard). The core concepts learned from `htop` applied directly, requiring only minor adjustments to adapt to the layout and additional interface panels.

## 📸 Screenshots

**1. `htop` — overview of per-core CPU utilization, memory bars, load averages, and active process lists:**

<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/83401c33-8b43-4347-8196-dc413e715b16" />

**2. `renice` — adjusting the priority of a `sleep` process from nice value 0 to 10, verified via `ps -o pid,ni,comm`:**

<img width="1111" height="252" alt="image" src="https://github.com/user-attachments/assets/fe5a99dc-30f9-4201-a351-91cbec07ed91" />

**3. `glances` — a broader dashboard than `htop` (adding per-partition disk I/O, network Rx/Tx rates, filesystem space, and hardware sensors) on a single screen:**

<img width="1314" height="867" alt="image" src="https://github.com/user-attachments/assets/b009fb50-a7aa-4314-8f17-562d811d813c" />

**4. `iostat -x 1 5` — capturing 5 consecutive disk I/O snapshots, utilizing the `%util` column to assess disk workload activity:**

<img width="1917" height="933" alt="image" src="https://github.com/user-attachments/assets/99627d3a-880b-459c-b6a8-d58999ab35bc" />
