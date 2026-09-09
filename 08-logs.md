# Read & Understand Logs

## 🧭 Context
In this step, I learned how to read and analyze system logs—both using `journalctl` (systemd) and traditional log files under `/var/log/`. This includes filtering logs by service, time, and severity level, as well as searching for specific patterns (e.g., failed login attempts) within long log files.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `journalctl`, `tail`, `grep`, `less`

## 📋 Hands-On Practice

### Filter Logs by Service (Unit)
```bash
journalctl -u ssh
journalctl -u nginx
```
Focuses on logs from a specific service without noise from other system services.

### Filter Logs by Severity & Time
```bash
journalctl -p err -b
journalctl -u ssh -p err -b
journalctl --since today
journalctl --since "1 hour ago"
journalctl -b
journalctl -f
```
Filters log outputs based on severity levels (errors only), current boot sessions, and specific time windows. The `-f` flag enables real-time log tailing.

### Reading Traditional Log Files
```bash
tail -f /var/log/auth.log
tail -f /var/log/syslog
sudo tail -50 /var/log/apache2/error.log
```
`/var/log/auth.log` records all login attempts and `sudo` executions. The `-f` flag provides live monitoring, while `tail -50` displays the last 50 lines without continuous following.

### Search Specific Patterns in Logs
```bash
grep "Failed password" /var/log/auth.log
grep "Failed password" /var/log/auth.log | wc -l
grep -i "error" /var/log/syslog | tail -20
```
Searches and counts specific log patterns (e.g., counting failed login attempts).

### View Long Log Files with a Pager
```bash
less /var/log/syslog
```
Reads large log files page by page—the exact same pager mechanism used under the hood by `journalctl` to render lengthy terminal outputs.

## 🧩 Key Takeaways

**Confused by `journalctl` Output Appearing "Unfinished"**

When running `journalctl -u ssh` for the first time, the terminal output seemed stuck or incomplete—displaying lines 1-50 of thousands of log entries while waiting for user interaction. I learned this is the standard **pager** behavior: lengthy outputs are buffered and displayed page-by-page rather than dumped all at once. Understanding this made navigation intuitive: `space` to scroll down, `b` to scroll up, `/keyword` then Enter to search, `n` to jump to the next match, and `q` to quit. This insight transferred directly when using `less` in other shell workflows.

**Choosing Between `tail -f`, `journalctl -f`, and `less`**

Because these tools display similar log content, deciding which command to use in specific scenarios was initially confusing. Testing each command highlighted that selection depends on **usage context** rather than visual layout alone.

## 📸 Screenshots

**1. `journalctl -u ssh` — output paginated (`Lines 1-50` at bottom left), showing SSH service start/stop history across boot sessions along with several authentication failures:**

<img width="1919" height="1029" alt="image" src="https://github.com/user-attachments/assets/c3e1c19f-788a-418f-b718-0c0c27b689c6" />

**2. `journalctl -u ssh -f` — real-time follow mode, automatically printing new SSH log entries upon connection without `Lines 1-50` pager indicators:**

<img width="1604" height="195" alt="image" src="https://github.com/user-attachments/assets/806465a7-3542-4687-a7dc-060b5c7a314f" />

**3. `journalctl -u ssh | grep -i "failed password"` — searching and counting failed login attempts (23 total), including 3 consecutive attempts from a different subnet (`10.126.120.22`) within a short timeframe:**

<img width="1201" height="495" alt="image" src="https://github.com/user-attachments/assets/7f9140c3-222c-442e-bcd0-85d15ae83dd9" />

**4. `less /var/log/syslog` — log file opened via pager, showing the filename indicator at the bottom left (`/var/log/syslog`), using the same underlying mechanics as `journalctl`:**

<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/5b714728-8f65-4ef3-9142-88c5a7df3e7a" />
