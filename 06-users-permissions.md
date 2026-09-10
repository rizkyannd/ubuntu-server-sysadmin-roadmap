# Manage Users & Permissions Securely

## 🧭 Context
I did this step to practice managing multi-user access on a server — creating new users & groups, controlling who has access to which folders/files, and how to properly grant or revoke permissions. The basics are simple, but during my first practice run I still mixed up a few things (explained in the Notes section).

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Tools:** `adduser`, `addgroup`, `usermod`, `deluser`, `chmod`, `chown`, `chgrp`

## 📋 What I Practiced

**1. Create a new user & group:**
```bash
sudo adduser budi
sudo addgroup mahasiswa
```

**2. Add user to group:**
```bash
sudo usermod -aG mahasiswa budi
```

**3. Verify user is in the group:**
```bash
cat /etc/group | grep budi
```
Result: `budi` is listed under the `mahasiswa` group.

**4. Delete user & group:**
```bash
sudo deluser nama-user
sudo groupdel nama-group
```

**5. Change permissions & ownership (example on the `tugas` folder):**
```bash
sudo chmod -R 777 tugas
sudo chmod ugo+rwx tugas
sudo chown kaks:mahasiswa tugas
```
> `chmod 777` is used here to demonstrate the most permissive access level (full owner/group/others access). This is not a production configuration — for a more realistic shared folder setup, `750` or `770` would be more appropriate.

**6. Sticky bit:**
```bash
sudo chmod 1777 nama-folder
sudo chmod +t nama-folder
sudo chmod -t nama-folder
```

## ⚙️ Verification
```bash
ls -l tugas
```

## 🧩 Notes — Things That Confused Me

**Mixing up permission numbers (4/2/1)**

Early on, I often forgot/mixed up which letter (`r`, `w`, `x`) converts to which number. After forcing myself to always read the letter order first before converting to numbers (instead of just memorizing the numbers directly), I rarely mix it up anymore.

**Mixing up the argument order in `usermod -aG`**

The command `sudo usermod -aG GROUP_NAME USER_NAME` — I kept writing the order wrong multiple times, thinking "I'm adding a **user** to a group, so the user should come first." Turns out the order is reversed: **group first, then user**. Now I remember it by reading the command as a sentence: "append-to-Group [group name] [user name]".

## 📸 Screenshots

**1. `cat /etc/group | grep budi` — verifying `budi` is in the `mahasiswa` group:**
<img width="1287" height="151" alt="image" src="https://github.com/user-attachments/assets/c6322228-8ae8-4e9c-afca-6785c82a6dd5" />

**2. `ls -l` before & after `chmod 777 tugas` — folder permissions changed:**
<img width="1079" height="259" alt="image" src="https://github.com/user-attachments/assets/15b86a96-86c2-4496-99bb-294716b6fc7e" />
