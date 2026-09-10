# Configure Firewall Rules Properly

## 🧭 Context
I performed this exercise to gain hands-on experience in configuring host-based firewall rules on a Linux server using UFW (Uncomplicated Firewall). The objective was to practice managing inbound traffic policies, exposing required application ports while denying unauthorized access, and validating active listening services using network analysis tools like Nmap and Telnet.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Firewall:** UFW (Uncomplicated Firewall)
- **Validation Tools:** Nmap, Telnet, cURL

## 📋 Hands-On Practice

**1. Enable & verify firewall default policies:**
```bash
sudo ufw enable
sudo ufw status verbose
```
Default incoming policy is set to deny, while outgoing policy remains allowed.

**2. Inspect pre-configured application profiles:**
```bash
sudo ufw app list
```

**3. Grant inbound traffic to specific service profile:**
```bash
sudo ufw allow "Apache Full"
```

**4. Revoke access rule:**
```bash
sudo ufw delete allow "Apache Full"
```

**5. Port validation and testing reference:**
* `telnet [IP_ADDRESS] [PORT]` — Tests active TCP handshake on a target port.
* `curl http://[IP_ADDRESS]` — Validates HTTP response directly over the network.
* `nmap -p- -sV [IP_ADDRESS]` — Scans all 65,535 TCP ports to identify open ports and service versions.

## ⚙️ Verification
```bash
sudo ufw status verbose
```
Rule list confirmed that only intended services (e.g., OpenSSH, Nginx Full) are actively permitted, enforcing strict boundary filtering.

## 🧩 Key Takeaways

**Discrepancy Between Local Nmap Scans and Firewall Boundaries**

During post-configuration verification, executing an Nmap scan locally (`kaks@ubuntu-server` targeted at `192.168.1.73`) revealed ports `8080` and `8443` (Apache) as "open". However, UFW rules only explicitly allowed port `22` (OpenSSH) and ports `80`/`443` (`Nginx Full`), with no rules defining `8080`/`8443`.

Investigating this behavior clarified that loopback and local interface traffic bypassing ingress interface checks do not get evaluated by UFW's netfilter/iptables rules in the same way external traffic does. Because the local system directly contacts its own IP, internal sockets show as listening despite being blocked from external interface incoming packets.

**Architectural Alignment for Reverse Proxies**

This discovery directly matches the underlying reverse proxy architecture on the machine: Nginx handles public-facing ingress traffic on ports `80`/`443` and proxies requests internally to Apache running on `8080`/`8443`. Keeping `8080` and `8443` hidden from UFW rule declarations ensures Apache remains completely isolated from the external network while maintaining local communication with Nginx. Verification from a separate external client confirmed ports `8080` and `8443` were unreachable outside the host.

## 📸 Screenshots

**1. UFW configuration verification & local Nmap scan analysis:**
<img width="992" height="739" alt="image" src="https://github.com/user-attachments/assets/f98fac5d-108e-4f47-a7e5-a5c483a35097" />

*Verification of UFW configuration (`ufw status verbose`) alongside Nmap scan results confirming internal socket binding vs. external exposure.*
