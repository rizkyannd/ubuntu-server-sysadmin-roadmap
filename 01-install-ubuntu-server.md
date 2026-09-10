# Install Ubuntu Server from Scratch

## 🧭 Context
I initiated this lab to provision a bare-metal equivalent Linux environment from scratch using Oracle VirtualBox. This installation serves as the foundational baseline infrastructure for all subsequent implementation modules across this roadmap—including advanced networking, SSH hardening, and host-based firewall configurations.

## 🛠️ Environment
- **Hypervisor:** Oracle VM VirtualBox
- **OS:** Ubuntu Server (26.04 LTS)
- **Virtual Machine Specs:** 2 vCPUs, 2 GB RAM, 20 GB Dynamically Allocated Storage
- **Hostname:** `ubuntu-server`
- **Primary User:** `kaks`

## 📋 Execution Steps

**1. Media Acquisition & VM Provisioning:**
Downloaded the official Ubuntu Server ISO image from [ubuntu.com](https://ubuntu.com/download/server) and initialized a new Virtual Machine instance in VirtualBox.

**2. Hardware Allocation & Media Mounting:**
Allocated system resources (2 vCPUs, 2048 MB RAM, 20 GB VDI disk) and mounted the bootable installer ISO into the virtual optical drive interface.

**3. System Initialization & Localization:**
Booted the VM into the Subiquity installer and configured the system language and keyboard layout parameters.

**4. Networking & Storage Partitioning:**
Accepted default DHCP networking for initial installer connectivity and applied guided storage layout partitioning across the virtual drive.

**5. System Identity & Access Provisioning:**
Configured the primary non-root administrative account (`kaks`) and system hostname (`ubuntu-server`).

**6. Service Package Selection:**
Selected the OpenSSH server package during installation to immediately enable remote shell capabilities post-first-boot.

**7. First-Boot Lifecycle & System Patching:**
Completed the installation, unmounted installation media, performed a full system reboot, and updated local package indexes and system dependencies:
```bash
sudo apt update && sudo apt upgrade -y
```

## ⚙️ Verification
```bash
lsb_release -a    # Confirmed active OS version and kernel release details
hostnamectl       # Verified host identity parameters and deployment environment
whoami            # Validated non-root user execution context
```

## 🧩 Key Takeaways

**Establishing Baseline OS Standardization**

Starting from a raw ISO installation rather than a pre-configured template allowed full control over storage layouts, initial account privilege boundaries, and core daemon selections. Installing OpenSSH during initial provisioning ensured immediate headless management capabilities, aligning with real-world datacenter and cloud deployment practices where direct console access is minimized.

## 📸 Screenshots

**1. OS deployment verification & system environment confirmation:**
<img width="612" height="531" alt="image" src="https://github.com/user-attachments/assets/dbbed726-801c-4277-83e7-01be2c74a13c" />

*Verification logs showing system release parameters (`lsb_release -a`), active hostname deployment (`hostnamectl`), and authenticated user context (`whoami`).*
