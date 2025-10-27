# Lab Environment Setup Guide

> Building a professional Red Team laboratory for safe and effective security research.

## 🎯 Overview

A proper lab environment is essential for developing, testing, and practicing offensive security techniques safely and legally. This guide will help you set up a comprehensive Red Team lab.

---

## 📋 Table of Contents

1. [Hardware Requirements](#hardware-requirements)
2. [Virtualization Setup](#virtualization-setup)
3. [Network Architecture](#network-architecture)
4. [Target Systems](#target-systems)
5. [Attacker Machines](#attacker-machines)
6. [Monitoring & Detection](#monitoring--detection)
7. [Best Practices](#best-practices)

---

## 💻 Hardware Requirements

### Minimum Specifications

```
CPU: Quad-core processor with VT-x/AMD-V support
RAM: 16GB (32GB recommended)
Storage: 500GB SSD
Network: Gigabit Ethernet
```

### Recommended Specifications

```
CPU: 8+ core processor with VT-x/AMD-V
RAM: 64GB+ for multiple VMs
Storage: 1TB+ NVMe SSD
Network: Dual NICs (management + lab network)
GPU: Optional for password cracking
```

### Budget-Friendly Alternative

**Use Cloud Resources**:
- AWS, Azure, GCP free tiers
- DigitalOcean droplets
- Vultr instances
- Self-hosted on spare hardware

---

## 🖥️ Virtualization Setup

### Hypervisor Options

#### 1. **VMware Workstation Pro** (Recommended for beginners)
- **Pros**: User-friendly, stable, snapshot support
- **Cons**: Commercial (free for personal use)
- **Platform**: Windows, Linux

```bash
# Installation on Linux
sudo apt install vmware-workstation
# or download from VMware website
```

#### 2. **VirtualBox** (Free & Open Source)
- **Pros**: Free, cross-platform, good community
- **Cons**: Slightly less performant
- **Platform**: Windows, Linux, macOS

```bash
# Installation on Debian/Ubuntu
sudo apt install virtualbox virtualbox-ext-pack

# Installation on Arch
sudo pacman -S virtualbox virtualbox-host-modules-arch
```

#### 3. **KVM/QEMU** (Advanced, Linux only)
- **Pros**: Native Linux performance, flexible
- **Cons**: Steeper learning curve
- **Platform**: Linux only

```bash
# Installation on Ubuntu/Debian
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virt-manager

# Add user to libvirt group
sudo usermod -aG libvirt $USER

# Enable and start libvirtd
sudo systemctl enable --now libvirtd
```

#### 4. **Proxmox VE** (Enterprise-grade, free)
- **Pros**: Web UI, clustering, professional features
- **Cons**: Requires dedicated machine
- **Platform**: Bare-metal installation

---

## 🌐 Network Architecture

### Recommended Lab Network Topology

```
┌──────────────────────────────────────────────────────────────┐
│  Host Machine (Your Computer)                                │
│  ├─ Management Network (192.168.1.0/24)                      │
│  └─ NAT / Bridge to Internet                                 │
└──────────────────────────────────────────────────────────────┘
                      │
        ┌─────────────┴─────────────┐
        │                           │
┌───────▼────────┐         ┌────────▼──────┐
│  Attacker Net  │         │  Target Net   │
│  10.0.1.0/24   │         │  10.0.2.0/24  │
└────────────────┘         └───────────────┘
        │                           │
   ┌────┴─────┐             ┌───────┴────────┐
   │          │             │                │
┌──▼──┐   ┌──▼──┐      ┌───▼───┐      ┌────▼────┐
│Kali │   │Win  │      │Ubuntu │      │Windows  │
│Linux│   │10   │      │Server │      │Server   │
└─────┘   └─────┘      └───────┘      └─────────┘
```

### Network Segmentation

#### Management Network
- **Purpose**: Access VMs, management interfaces
- **Subnet**: 192.168.1.0/24
- **Isolation**: Separate from attack/target networks
- **Access**: SSH, RDP, Web interfaces

#### Attacker Network
- **Purpose**: Your offensive tools and machines
- **Subnet**: 10.0.1.0/24
- **Isolation**: Can route to target network
- **Machines**: Kali, Parrot, Windows attack box

#### Target Network
- **Purpose**: Vulnerable systems for testing
- **Subnet**: 10.0.2.0/24
- **Isolation**: Isolated from internet, accessible from attacker network
- **Machines**: Vulnerable VMs, intentionally insecure applications

### Network Configuration Examples

#### VMware Workstation

```bash
# Create custom networks
# Edit → Virtual Network Editor

# Attacker Network (VMnet2)
- Type: Host-only
- Subnet: 10.0.1.0/24
- DHCP: Disabled

# Target Network (VMnet3)
- Type: Host-only
- Subnet: 10.0.2.0/24
- DHCP: Enabled (range: 10.0.2.100-200)

# Internet Access (NAT)
- VMnet8 (default NAT)
```

#### VirtualBox

```bash
# Create networks via VBoxManage

# Attacker Network
VBoxManage natnetwork add --netname AttackerNet --network "10.0.1.0/24" --enable --dhcp off

# Target Network
VBoxManage natnetwork add --netname TargetNet --network "10.0.2.0/24" --enable --dhcp on
```

#### KVM/Libvirt

```xml
<!-- /etc/libvirt/qemu/networks/attacker-net.xml -->
<network>
  <name>attacker-net</name>
  <forward mode='nat'/>
  <bridge name='virbr1'/>
  <ip address='10.0.1.1' netmask='255.255.255.0'>
  </ip>
</network>

<!-- /etc/libvirt/qemu/networks/target-net.xml -->
<network>
  <name>target-net</name>
  <forward mode='nat'/>
  <bridge name='virbr2'/>
  <ip address='10.0.2.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.0.2.100' end='10.0.2.200'/>
    </dhcp>
  </ip>
</network>
```

```bash
# Define and start networks
sudo virsh net-define /etc/libvirt/qemu/networks/attacker-net.xml
sudo virsh net-start attacker-net
sudo virsh net-autostart attacker-net

sudo virsh net-define /etc/libvirt/qemu/networks/target-net.xml
sudo virsh net-start target-net
sudo virsh net-autostart target-net
```

---

## 🎯 Target Systems

### Intentionally Vulnerable VMs

#### 1. **DVWA (Damn Vulnerable Web Application)**
- **Type**: Web application
- **Skills**: Web exploitation, SQL injection, XSS
- **Download**: https://github.com/digininja/DVWA

#### 2. **Metasploitable 2/3**
- **Type**: Linux server
- **Skills**: Network services exploitation, privilege escalation
- **Download**: https://sourceforge.net/projects/metasploitable/

#### 3. **VulnHub VMs**
- **Type**: Various (OSCP-like)
- **Skills**: CTF-style challenges
- **Download**: https://www.vulnhub.com/

#### 4. **HackTheBox Retired Machines**
- **Type**: Various
- **Skills**: Real-world scenarios
- **Access**: https://www.hackthebox.com/

#### 5. **TryHackMe Labs**
- **Type**: Guided learning paths
- **Skills**: Structured learning
- **Access**: https://tryhackme.com/

### Building Custom Vulnerable Targets

```bash
# Ubuntu Server with known vulnerabilities
# Install specific vulnerable services

# 1. FTP (vsFTPd 2.3.4 - backdoor)
sudo apt install vsftpd=2.3.4-1ubuntu1

# 2. SSH (weak configuration)
# Edit /etc/ssh/sshd_config
PermitRootLogin yes
PasswordAuthentication yes
PermitEmptyPasswords yes

# 3. Samba (old version with known exploits)
sudo apt install samba=2:4.3.11+dfsg-0ubuntu0.16.04.23

# 4. Apache with vulnerable web apps
sudo apt install apache2 php libapache2-mod-php php-mysql

# 5. MySQL with weak passwords
sudo apt install mysql-server
# Set root password to 'password'
```

---

## ⚔️ Attacker Machines

### Primary Attack Platform: Kali Linux

```bash
# Download
wget https://cdimage.kali.org/kali-latest/kali-linux-amd64.iso

# Or use pre-built VM
# https://www.kali.org/get-kali/#kali-virtual-machines
```

**Post-Installation Setup**:
```bash
# Update system
sudo apt update && sudo apt full-upgrade -y

# Install additional tools
sudo apt install -y \
    seclists wordlists \
    python3-pip golang \
    bloodhound neo4j \
    chisel proxychains4 \
    empire powershell-empire \
    covenant

# Install S1BGr0up tools (when available)
git clone https://github.com/S1b-Team/tools.git
cd tools && ./install.sh
```

### Secondary: Windows 10/11 Attack Box

**Purpose**: Testing Windows-specific exploits and tools

**Setup**:
```powershell
# Install Chocolatey
Set-ExecutionPolicy Bypass -Scope Process -Force
iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

# Install tools
choco install -y python git vscode
choco install -y sysinternals procexp procmon
choco install -y wireshark nmap

# Install PowerShell modules
Install-Module -Name PowerSploit -Force
Install-Module -Name Nishang -Force
```

### Alternative: Parrot Security OS

- Lighter than Kali
- Good for resource-constrained systems
- Similar toolset
- Download: https://www.parrotsec.org/

---

## 📊 Monitoring & Detection

### Blue Team Perspective

**Set up monitoring to practice evasion**:

#### 1. **Security Onion** (SIEM/NSM)
- **Purpose**: Network monitoring, IDS/IPS
- **Download**: https://github.com/Security-Onion-Solutions/securityonion

#### 2. **Wazuh** (Host-based monitoring)
- **Purpose**: HIDS, log analysis, compliance
- **Setup**:
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | apt-key add -
echo "deb https://packages.wazuh.com/4.x/apt/ stable main" | tee /etc/apt/sources.list.d/wazuh.list
apt update && apt install wazuh-manager
```

#### 3. **Splunk** (Log Management)
- **Purpose**: Centralized logging and SIEM
- **Free Version**: 500MB/day limit
- **Download**: https://www.splunk.com/

#### 4. **ELK Stack** (Elasticsearch, Logstash, Kibana)
- **Purpose**: Log aggregation and analysis
- **Setup**:
```bash
# Install Elasticsearch
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update && sudo apt install elasticsearch logstash kibana
```

---

## ✅ Best Practices

### 1. **Snapshots & Backups**

```bash
# Take snapshots before testing
# VMware
vmrun snapshot "/path/to/vm.vmx" "Before exploit test"

# VirtualBox
VBoxManage snapshot "VM Name" take "Before exploit test"

# KVM/Libvirt
virsh snapshot-create-as --domain vmname --name "Before exploit test"
```

### 2. **Isolated from Production**

**Critical**: NEVER connect lab to production networks!

- Use host-only or internal networks
- No bridge to physical network (unless necessary)
- Separate physical hardware if possible
- Use firewall rules to prevent accidental exposure

### 3. **Document Everything**

```bash
# Keep a lab notebook
# Example structure:
~/lab-notes/
├── 2025-01-15-metasploitable-testing.md
├── 2025-01-20-dvwa-sql-injection.md
└── configs/
    ├── network-topology.drawio
    └── vm-inventory.xlsx
```

### 4. **Resource Management**

```bash
# Don't run all VMs at once
# Start only what you need
# Allocate RAM appropriately:
# - Attacker VMs: 4-8GB
# - Target VMs: 2-4GB
# - Monitoring: 4-8GB
```

### 5. **Legal Considerations**

**Always ensure**:
- All systems are owned by you
- No connection to unauthorized networks
- No testing of third-party services
- Comply with software licenses
- Be aware of local laws regarding security tools

---

## 🎓 Next Steps

### Learning Path

1. **Week 1-2**: Set up basic lab with Kali + Metasploitable
2. **Week 3-4**: Practice reconnaissance and scanning
3. **Week 5-6**: Exploitation exercises
4. **Week 7-8**: Privilege escalation and persistence
5. **Week 9+**: Advanced topics (AD attacks, web exploitation)

### Recommended Practice

- **HackTheBox**: Active machines and challenges
- **TryHackMe**: Guided learning paths
- **PentesterLab**: Web application security
- **PortSwigger Academy**: Web security (free)
- **VulnHub**: Downloadable vulnerable VMs

---

## 🤝 Contributing

Have lab setup improvements or configurations to share? See [CONTRIBUTING.md](../../CONTRIBUTING.md).

---

```
[*] A good lab is the foundation of great skills
[*] Practice safely. Learn constantly. Test ethically.
[*] S1BGr0up - Building Tomorrow's Red Teamers
```

**Last Updated**: October 27, 2025
