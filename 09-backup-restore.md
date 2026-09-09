# Backup & Restore Files

## 🧭 Context
In this step, I learned how to back up and restore files/folders on a server—both using `tar` (compressing into a single archive file) and `rsync` (synchronizing between directories/servers). This includes automating backups using `crontab` and verifying file integrity of backup results.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `tar`, `rsync`, `crontab`, `sha256sum`

## 📋 Hands-On Practice

### 1. Backup & Restore Using `tar`
```bash
sudo tar -czvf sticky-backup-$(date +%F).tar.gz -C /home/kaks archive
tar -tzvf filename
sudo tar -xzvf sticky-backup-2026-07-21.tar.gz -C /destination/directory
```
Backed up the `archive` directory into a compressed file with an automatically formatted date name, inspected archive contents without extracting, and restored to the destination directory.

### 2. Backup File Integrity Verification
```bash
sha256sum FILENAME
```
Compared file hash values to ensure there was no data corruption.

### 3. Synchronization & Backup Using `rsync`
```bash
rsync -avhz SOURCE DESTINATION
rsync -avh -e "ssh -p 2222" sticky user@SERVER_IP:/backup/
rsync -avh --exclude='*.log' --exclude='cache/' /var/www/ /backup/www-backup/
rsync -avh --dry-run --delete /var/www/ /backup/www-backup/
rsync -avh --delete /var/www/ /backup/www-backup/
```
Synchronized directories locally and across remote servers over SSH with file exclusion rules, executing a dry run (`--dry-run`) prior to applying actual deletion commands (`--delete`).

### 4. Scheduled Backup Automation
```bash
crontab -e
crontab -l
```
Created and listed scheduled automated tasks.

## 🧩 Key Takeaways

**Understanding `crontab` 5-Column Syntax**

Initially, configuring scheduled jobs via `crontab -e` was confusing because of the 5 sequential time fields (minute, hour, day of month, month, day of week). Misunderstanding field positions or formatting can cause scheduled tasks to execute incorrectly or fail entirely. Taking time to understand the underlying logic rather than just memorizing order—such as setting `0 * * * *` to run a job at minute 0 of every hour—made configuring cron expressions much clearer.

**Discovering Task Automation with `crontab`**

I hadn't previously used a dedicated mechanism for scheduling automated commands in Linux, and discovering `crontab` provided the exact solution needed for routine tasks. Instead of running `tar` or `rsync` commands manually, scheduling them via `crontab -e` enables hands-free, reliable background execution at specified intervals.

**Understanding Hashes for File Integrity Verification**

Before learning about `sha256sum`, my method for checking backup success was simply verifying file existence using `ls`. However, verifying presence alone doesn't guarantee data integrity, as files can be partially damaged or corrupted silently. Using `sha256sum` introduced the concept of cryptographic file hashing: every file has a unique hash fingerprint based on its raw content. Matching hashes between the original and backup files confirms bit-for-bit identity, offering a far more robust verification strategy than basic file checks.

## 📸 Screenshots

**1. `tar -czvf` + `tar -tzvf` — creating a backup archive into a target folder, then inspecting archive contents without extracting:**

<img width="1084" height="77" alt="image" src="https://github.com/user-attachments/assets/77ac7e53-78e6-4c1b-9582-982cc911c424" />

**2. `ls -l` archive contents + `sha256sum` — generating checksum, saving to file, and verifying file integrity (`sha256sum -c`) resulting in `OK`:**

<img width="985" height="214" alt="image" src="https://github.com/user-attachments/assets/4931e703-408f-4edd-8c81-53c3a6e868c1" />

**3. `rsync -avh --dry-run --delete` — simulating file synchronization and deletion before execution, verified by the `(DRY RUN)` output on the final line:**

<img width="803" height="189" alt="image" src="https://github.com/user-attachments/assets/12338564-65f7-49b2-984d-2cd1a0bf8cc3" />

**4. `crontab -l` — scheduling automated `rsync` backups daily at 2:00 AM using 5-column cron syntax (minute, hour, day of month, month, day of week):**

<img width="882" height="500" alt="image" src="https://github.com/user-attachments/assets/e063c101-12da-4fb4-b1a2-fd6470b83637" />
