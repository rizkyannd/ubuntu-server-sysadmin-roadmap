# Understand Systemd & Service Management

## 🧭 Context
In this step, I learned Linux service management using `systemctl`—ranging from basic control commands (start/stop/restart/reload) and enabling auto-start on boot, to debugging failed services, and creating a custom `.service` unit file from scratch to run my own process.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `systemctl`, `journalctl`

## 📋 Hands-On Practice

### 1. Basic Service Controls
```bash
systemctl status nginx
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx
```

### 2. Auto-Start on Boot
```bash
systemctl enable nginx
systemctl disable nginx
systemctl is-active --quiet nginx
systemctl is-enabled nginx
```

### 3. Check Boot Target / Mode
```bash
systemctl get-default
systemctl list-units --type=target
```

### 4. Reload Systemd Configuration
```bash
sudo systemctl daemon-reload
```

### 5. Debugging Failed Services
```bash
systemctl status nginx
journalctl -u nginx -xe
journalctl -u nginx -f
```

### 6. Creating a Custom Service from Scratch

Simulating a long-running process managed by systemd—a script that appends timestamps to a log file every 10 seconds indefinitely. This was used to practice building unit files from scratch and to serve as a test case for debugging in section 8.

**Long-running script (`/home/kaks/latihan-systemd/monitor.sh`):**
```bash
#!/bin/bash
set -euo pipefail

while true; do
        echo "$(date): script still running" >> /home/kaks/latihan-systemd/log.txt
        sleep 10
done
```

**Unit file (`/etc/systemd/system/monitor-ky.service`):**
```ini
[Unit]
Description=ky's test monitoring service

[Service]
ExecStart=/home/kaks/latihan-systemd/monitor.sh
Type=simple
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### 7. Running & Verifying the Custom Service
```bash
sudo systemctl daemon-reload
sudo systemctl reset-failed monitor-ky.service
sudo systemctl enable --now monitor-ky.service
sudo systemctl disable --now monitor-ky.service
systemctl status monitor-ky.service
```

### 8. Debugging an Intentionally Failed Custom Service
```bash
tail -f log.txt
journalctl -u monitor-ky.service -xe
```

## 🧩 Key Takeaways

**Debugging an Intentionally Broken Service**

I purposely modified `ExecStart` in `monitor-ky.service` from the correct path (`/home/kaks/latihan-systemd/monitor.sh`) to a non-existent path (`/home/kaks/latihan-systemd/tidakTersedia`) to practice troubleshooting a crashing service. Running `tail -f log.txt` showed no new log entries (indicating the script was not running at all). Pinpointing the root cause was straightforward using `journalctl -u monitor-ky.service -xe`, which clearly logged a "directory/file not found" error due to the invalid `ExecStart` path.

**Understanding `[Unit]`, `[Service]`, and `[Install]` Sections**

I previously didn't realize that unit files existed to configure how scripts run as background services. At first, the anatomy of these three sections (`[Unit]`, `[Service]`, `[Install]`) felt confusing—it seemed surprising that plain key-value text in a file could instruct systemd to handle a process so specifically. Once broken down step-by-step, it made total sense as the standard Linux method for running boot-persistent services, distinct from scheduled jobs managed via `crontab -e`.

**Demystifying `enable`: It's Just Creating Symlinks**

I originally assumed `systemctl enable` acted as a magical switch that immediately started a service, similar to pressing a Run button in an IDE. In reality, all it does under the hood is create a symbolic link (symlink) from the unit file to a `.wants/` directory (defined by `WantedBy`). This configures the service to trigger automatically on the next system boot rather than launching it right away. This is why using `enable --now` is so practical—it enables the service for future boots and starts it immediately in one step.

## 📸 Screenshots

**1. Running `status` → `enable` → `is-enabled` for Nginx — the `Created symlink` output aligns directly with the symlink concept noted above:**

<img width="1046" height="399" alt="image" src="https://github.com/user-attachments/assets/05bc4e1c-7340-48bc-b999-e3cb4712c6ee" />

**2. The `monitor-ky.service` unit file structure alongside an `active (running)` status, displaying both `monitor.sh` and `sleep 10` processes within the CGroup tree:**

<img width="805" height="413" alt="image" src="https://github.com/user-attachments/assets/75646af5-e73a-452f-8c07-ed4078f0de70" />

**3. Output from `log.txt` verifying that `monitor.sh` runs continuously, appending new timestamps every 10 seconds:**

<img width="527" height="182" alt="image" src="https://github.com/user-attachments/assets/26db9ef8-28a3-4d39-b24f-4cc4224e7fe5" />

**4. Debugging the failed service — `status` shows `failed (exit-code)` while `journalctl` exposes the root issue (`No such file or directory` due to the bad `ExecStart` path), as well as systemd's automatic restart attempts per `Restart=on-failure`:**

<img width="1272" height="699" alt="image" src="https://github.com/user-attachments/assets/4bd2c912-5457-4f19-af36-2afcc85c5fd9" />
