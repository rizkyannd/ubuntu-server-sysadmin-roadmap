# Configure Static IP & Networking

## 🧭 Context
I conducted this hands-on lab to practice static IP addressing and network interface configuration on an Ubuntu Server environment using Netplan. The objective was to transition a server instance from dynamic host allocation (DHCP) to a predictable static network configuration, while leveraging standard Linux CLI diagnostic utilities to inspect routing tables, active sockets, and network boundary connectivity.

## 🛠️ Environment
- **OS:** Ubuntu Server
- **Network Interface:** `enp0s3`
- **MAC Address:** `08:00:27:xx:xx:xx`
- **Configuration Manager:** Netplan (YAML schema version 2)

## 📋 Netplan Configuration

**Initial Subiquity-generated Configuration (DHCP default):**
`/etc/netplan/00-installer-config.yaml`
```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    enp0s3:
      dhcp4: yes
      dhcp6: yes
      match:
        macaddress: 08:00:27:xx:xx:xx
      set-name: enp0s3
  version: 2
```

**Final Hardened Configuration (Static Addressing):**
Modified the schema to assign a explicit CIDR subnet mask, default gateway, and public DNS resolvers:
```yaml
network:
  ethernets:
    enp0s3:
      dhcp4: no
      addresses: [192.168.1.73/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
      match:
        macaddress: 08:00:27:xx:xx:xx
      set-name: enp0s3
  version: 2
```

**Applied the changes to the runtime system:**
```bash
sudo netplan apply
```

## 🔍 Network Diagnostics Reference

| Utility | Technical Purpose |
|---|---|
| `ping -c 4 [TARGET]` | Validates ICMP reachability and link latency to a remote target |
| `traceroute [TARGET]` | Traces layer 3 packet hop-by-hop pathing toward a destination |
| `ss -tuln` / `netstat -tuln` | Displays active listening sockets (`t`=TCP, `u`=UDP, `l`=listening, `n`=numeric IP/port) |
| `ip route` / `route -n` | Inspects kernel routing table entries and default gateway assignments |
| `nslookup [DOMAIN]` | Queries configured DNS resolvers for domain-to-IP resolution |
| `ethtool [INTERFACE]` | Queries Physical Layer (L1/L2) interface hardware attributes and link state |

## ⚙️ Verification
```bash
ip a                # Confirmed assigned static address 192.168.1.73/24 on enp0s3
ping -c 4 8.8.8.8   # Validated external L3 ICMP outbound connectivity
```

## 🧩 Key Takeaways

**Static IP Binding vs. Subnet Portability**

Assigning a hardcoded static IP (`192.168.1.73/24` with Gateway `192.168.1.1`) guarantees consistent host accessibility within the home Wi-Fi local area network (LAN). However, switching the VM's bridged network connection to a mobile hotspot network resulted in complete loss of inbound SSH connectivity and outbound routing failures.

This failure mode highlighted a critical networking principle: static IP parameters are explicitly bound to a specific layer 3 broadcast domain and gateway topology. When attached to a foreign network with a different subnet (e.g., `192.168.43.0/24`), the server cannot route traffic through an unreachable gateway (`192.168.1.1`). This demonstrated why infrastructure environments rely on static assignments only within dedicated management subnets or utilize DHCP reservations (MAC-to-IP binding) for portable environments.

## 📸 Screenshots

**1. Netplan execution, static IP verification, and network connectivity testing:**
<img width="1282" height="854" alt="image" src="https://github.com/user-attachments/assets/bcc9f618-2b1d-40ad-b336-0bac6b4771b4" />

*Verification logs showing Netplan configuration deployment, `ip a` output confirming static IP binding, and successful ICMP response from Google Public DNS.*
