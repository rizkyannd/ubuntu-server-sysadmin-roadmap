# Install & Configure Web Server (Apache/Nginx)

## 🧭 Context
The web server setup started with Apache standalone — HTTP access worked fine, but HTTPS failed because SSL wasn't enabled yet. After fixing that, I moved to the second phase: setting up Nginx as a reverse proxy in front of Apache (Apache moved to backend port 8080), and it turned out a similar SSL issue came up again on the Nginx side — it needed to be set up from scratch specifically for Nginx.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Web server:** Apache2 (initially standalone, then became the backend) + Nginx (reverse proxy)
- **Firewall:** UFW
- **SSL:** Self-signed certificate, configured separately on Apache and Nginx

---

## 📋 Hands-On Practice

### Phase 1 — Apache Standalone: HTTPS Fails

**1. Initial access — HTTP works, HTTPS fails**
Server just had default Apache installed. HTTP access worked normally, but trying HTTPS threw an error — Apache's SSL module wasn't enabled at all.

**2. Enable the SSL module:**
```bash
sudo a2enmod ssl
```

**3. Enable the default-ssl site (listens on 443):**
```bash
sudo a2ensite default-ssl
```

**4. Restart Apache:**
```bash
sudo systemctl restart apache2
```

After this, HTTPS access to Apache worked.

---

### Phase 2 — Nginx Reverse Proxy Setup (Apache as Backend)

**1. Install Nginx & open firewall access:**
```bash
sudo apt install nginx
sudo ufw allow 'Nginx HTTP'
systemctl status nginx
```

**2. Create a folder & generate a self-signed certificate specifically for Nginx:**
```bash
sudo mkdir -p /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/nginx/ssl/nginx-selfsigned.key \
  -out /etc/nginx/ssl/nginx-selfsigned.crt
```

**3. Write the reverse proxy config (HTTP first):**
```bash
sudo nano /etc/nginx/sites-available/nginx-proxy
```
```nginx
server {
    listen 80;
    listen [::]:80;
    server_name 192.168.1.73;
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;
    }
}
server {
    listen 443 ssl;
    listen [::]:443 ssl;
    server_name 192.168.1.73;
    ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;
    location / {
        proxy_pass http://127.0.0.1:8080;
        include proxy_params;
    }
}
```

**4. Enable the config & reload:**
```bash
sudo ln -s /etc/nginx/sites-available/nginx-proxy /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

**5. Test config & reload:**
```bash
sudo nginx -t
sudo systemctl reload nginx
```

**6. Verify the firewall:**
```bash
sudo ufw status verbose
```

## 🧩 Key Takeaways
An interesting part of this process: the SSL fix I made on Apache (Phase 1) didn't automatically "carry over" to Nginx, even though both run on the same server. In Phase 1, I fixed Apache's SSL using `a2enmod ssl` + `a2ensite default-ssl` — at that point I only knew Apache, hadn't thought about reverse proxies at all, so I got the SSL fully working there.

Once I learned the reverse proxy concept and put Nginx in front of Apache, I realized Nginx needs its own certificate and SSL config from scratch — it can't "borrow" the SSL that's already active on Apache. This time I did it in a cleaner order: generate the self-signed certificate first with OpenSSL, then write the Nginx config with both server blocks (port 80 and port 443) in a single file at once. Since the cert was already in place before the config got reloaded, `nginx -t` was valid right away, and both HTTP and HTTPS worked as soon as Nginx reloaded.

The insight: in a reverse proxy architecture, SSL termination is usually handled by the front-most layer (Nginx here) — communication from Nginx to Apache on the backend (port 8080) stays plain HTTP internally. So the SSL config I set up on Apache during Phase 1 effectively became unused from the client's perspective, since the client now talks to Nginx, not directly to Apache. If I'd understood this concept from the start, I probably wouldn't have needed to spend time setting up SSL on standalone Apache first — but since I did Phase 1 before even considering a reverse proxy, that's just how it played out, and it's exactly what taught me the difference in SSL's role at each layer.

⚠️ Follow-up: since the Phase 1 Apache SSL config is now redundant in the final setup, I haven't yet checked whether it's better to just disable Apache's SSL module, or leave it on standby in case direct access to Apache is ever needed again.

## 📸 Screenshots
**1. Output of `a2enmod ssl` + `a2ensite default-ssl` (Apache):**
<img width="1071" height="853" alt="image" src="https://github.com/user-attachments/assets/9b1df7c0-dae5-4211-8867-f00f90c6b1cc" />

**2. `systemctl status nginx`:**
<img width="1057" height="359" alt="image" src="https://github.com/user-attachments/assets/86e7cf47-9ebd-426d-8917-e93d60bb7309" />

**3. `nginx -t` — config valid:**
<img width="1278" height="145" alt="image" src="https://github.com/user-attachments/assets/5069aee9-b2cd-45e6-a199-94613a301220" />

**4. `ufw status verbose`:**
<img width="1066" height="269" alt="image" src="https://github.com/user-attachments/assets/e268dba6-4113-44db-ad5d-a3761ff47256" />

**5. HTTPS access to Nginx succeeds (reverse proxy forwarding to the Apache backend):**
<img width="953" height="1026" alt="image" src="https://github.com/user-attachments/assets/01218c60-ad69-4fd2-bfed-8a8051f0423b" />
