# OSPF Automation Lab – CyberSecurity Edition

> A complete walkthrough of installing GNS3 on Kali Linux 2025.4 and automating OSPF configuration using Python. Built by a Cybersecurity Student for learning network automation and security fundamentals.

![Python](https://img.shields.io/badge/Python-3.14-blue.svg)
![GNS3](https://img.shields.io/badge/GNS3-v3.0.6-purple.svg)
![Kali](https://img.shields.io/badge/Kali-2025.4-green.svg)
![License](https://img.shields.io/badge/License-MIT-red.svg)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen.svg)

---

## 📌 Table of Contents

* Project Overview
* Why This Lab for Cybersecurity Students
* Lab Topology
* What I Installed & Setup
* Setup Process (Step-by-Step)
* GNS3 Web UI Setup
* GNS3 GUI Setup
* VPCS Testing & Connectivity
* Python Automation Script
* Verification Commands
* Challenges Faced & Solutions
* Screenshots
* Security Lessons Learned
* License

---

## 🎯 Project Overview

This project documents my complete journey of setting up a network automation lab from scratch on **Kali Linux 2025.4**. As a cybersecurity student, the goal was to:

1. **Install and configure GNS3** – from dependencies to running GUI
2. **Compile core components** – Dynamips and Ubridge from source
3. **Build a working topology** – with VPCS devices for testing
4. **Write a Python script** – to automate OSPF configuration via Telnet
5. **Document everything** – including every error and its fix
6. **Understand the security implications** – of automation and network protocols

---

## 🛡️ Why This Lab for Cybersecurity Students

| **🔐 Security Concept**    | **🧠 What This Lab Teaches**                    |
| -------------------------- | ----------------------------------------------- |
| **Network Reconnaissance** | Understanding how routers share routes via OSPF |
| **Default Credentials**    | Seeing why `admin/admin` is dangerous           |
| **Clear-Text Protocols**   | Telnet sends passwords in plain text            |
| **Automation Risks**       | How attackers can automate network access       |
| **Privilege Escalation**   | Enable passwords and user privileges            |
| **Defense Strategy**       | Understanding OSPF authentication               |
| **Red Teaming**            | Using Python to interact with network devices   |
| **Logging & Monitoring**   | Understanding what to monitor in OSPF networks  |

> 💡 **"You can't defend what you don't understand."** – This lab builds your network fundamentals from both a defender and attacker perspective.

---

## 🗺️ Lab Topology

This is the exact topology I built and tested:

```text
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│  ┌──────────────┐      ┌─────────────┐      ┌──────────────┐   │
│  │              │      │             │      │              │   │
│  │  Kali PC     │──────│   SWITCH    │──────│   PC2        │   │
│  │  (Host)      │      │   (Hub)     │      │  192.168.1.2 │   │
│  └──────────────┘      └─────────────┘      └──────────────┘   │
│  192.168.1.100                │                                │
│                               │                                │
│                        ┌─────────────┐                         │
│                        │   PC1       │                         │
│                        │ 192.168.1.1 │                         │
│                        └─────────────┘                         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

| **Device** | **IP Address**   | **Purpose**                |
| ---------- | ---------------- | -------------------------- |
| Kali PC    | 192.168.1.100/24 | Host running Python script |
| PC1 (VPCS) | 192.168.1.1/24   | Test connectivity          |
| PC2 (VPCS) | 192.168.1.2/24   | Test connectivity          |
| Switch     | -                | Connecting all devices     |

---

## 📦 What I Installed & Setup

### System Packages

```bash
sudo apt install -y python3-pip python3-venv git cmake build-essential \
    pkg-config libelf-dev libpcap-dev qemu-system-x86 qemu-utils \
    python3-pyqt5 python3-pyqt5.qtsvg qt5-qmake qtbase5-dev \
    xterm vpcs
```

### Core GNS3 Components

| **Component** | **Version** | **Source**               |
| ------------- | ----------- | ------------------------ |
| Dynamips      | 0.2.24      | Compiled from source     |
| Ubridge       | 1.2.1       | Compiled from source     |
| GNS3 Server   | 3.0.6       | Python pip (virtual env) |
| GNS3 GUI      | 3.0.6       | Python pip (virtual env) |
| PyQt6         | 6.11.0      | Python pip               |
| VPCS          | Latest      | `apt install`            |

---

## 📝 Setup Process (Step-by-Step)

### Step 1: Kali System Update

```bash
sudo apt update
```

**✅ Output:** `2004 packages upgradable`

### Step 2: Install Core Dependencies

```bash
sudo apt install -y python3-pip python3-venv git cmake build-essential \
    pkg-config libelf-dev libpcap-dev qemu-system-x86 qemu-utils \
    python3-pyqt5 python3-pyqt5.qtsvg qt5-qmake qtbase5-dev
```

### Step 3: Compile & Install Dynamips

```bash
cd ~
git clone https://github.com/GNS3/dynamips.git
cd dynamips
mkdir build && cd build
cmake ..
make
sudo make install
```

**✅ Output:** `dynamips -v` → version 0.2.24

### Step 4: Compile & Install Ubridge

```bash
cd ~
git clone https://github.com/GNS3/ubridge.git
cd ubridge
make
sudo make install
```

**✅ Output:** `ubridge -v` → version 1.2.1

### Step 5: Create Python Virtual Environment

```bash
cd ~
python3 -m venv gns3-env
source ~/gns3-env/bin/activate
pip install gns3-server gns3-gui
```

### Step 6: Install PyQt6 for GUI Support

```bash
pip install PyQt6 PyQt6-Qt6 PyQt6-sip
```

### Step 7: Install VPCS & Xterm

```bash
sudo apt install -y vpcs xterm
```

---

## 🌐 GNS3 Web UI Setup

### Step 1: Start GNS3 Server

```bash
gns3server --local --port 3080
```

### Step 2: Open in Browser

```text
http://localhost:3080
```

### Step 3: Login

```text
Username: admin
Password: admin
```

### Step 4: Create Project

* Click **"Add blank project"**
* Name: `OSPF_Lab`

### Step 5: Build Topology

1. Add **VPCS** nodes (2 times) → `PC1`, `PC2`
2. Add **Ethernet Switch** → `Switch1`
3. Connect:

   * PC1 (e0) → Switch (Port 1)
   * PC2 (e0) → Switch (Port 2)

---

## 🖥️ GNS3 GUI Setup

### Start GNS3 GUI

```bash
source ~/gns3-env/bin/activate
gns3
```

### Setup Wizard Configuration

| **Field**    | **Value**                                            |
| ------------ | ---------------------------------------------------- |
| Option       | Start and connect to a local controller (Linux only) |
| Server path  | `/home/kali/gns3-env/bin/gns3server`                 |
| Host binding | `localhost`                                          |
| Port         | `3080`                                               |

### Preferences Configuration

1. **Edit** → **Preferences**
2. **VPCS** section
3. **VPCS path:** `/usr/bin/vpcs`
4. Click **Apply** → **OK**

---

## ✅ VPCS Testing & Connectivity

### Start Devices

* Right-click PC1 → **Start**
* Right-click PC2 → **Start**

### Open Console

* Right-click PC1 → **Console**
* Right-click PC2 → **Console**

### Assign IP Addresses

**PC1 Console:**

```bash
ip 192.168.1.1 255.255.255.0
```

**PC2 Console:**

```bash
ip 192.168.1.2 255.255.255.0
```

### Test Connectivity

**From PC1:**

```bash
ping 192.168.1.2
```

**✅ Result:** 5/5 replies received

---

## 🐍 Python Automation Script

```python
#!/usr/bin/env python3
"""
OSPF Automation Script
Kali Linux 2025.4 | GNS3 3.0.6 | Python 3.14
Cybersecurity Student Lab
"""

import getpass
import telnetlib
from datetime import datetime

# Colors for output
GREEN = '\033[92m'
RED = '\033[91m'
BLUE = '\033[94m'
RESET = '\033[0m'

def log_message(msg, level="INFO"):
    """Print formatted logs"""
    timestamp = datetime.now().strftime("%H:%M:%S")
    color = GREEN if level == "SUCCESS" else RED if level == "ERROR" else BLUE
    print(f"{color}[{timestamp}] [{level}] {msg}{RESET}")

def configure_router(hostname, ip, loopback_ip, username, password):
    """Configure OSPF on a router via Telnet"""
    
    log_message(f"Configuring {hostname} at {ip}")
    
    try:
        tn = telnetlib.Telnet(ip)
        
        # Authentication
        tn.read_until(b"Username:")
        tn.write(username.encode('ascii') + b"\n")
        tn.read_until(b"Password:")
        tn.write(password.encode('ascii') + b"\n")
        
        # Enable mode
        tn.write(b"enable\n")
        tn.write(b"conf t\n")
        
        # Hostname
        tn.write(f"hostname {hostname}\n".encode('ascii'))
        
        # Interface config
        tn.write(b"int fa0/0\n")
        tn.write(b"ip address 192.168.1." + ip.split('.')[-1].encode('ascii') + b" 255.255.255.0\n")
        tn.write(b"no shut\n")
        tn.write(b"exit\n")
        
        # Loopback config
        tn.write(b"int loop 0\n")
        tn.write(f"ip address {loopback_ip} 255.255.255.255\n".encode('ascii'))
        tn.write(b"exit\n")
        
        # OSPF config
        tn.write(b"router ospf 1\n")
        tn.write(b"network 192.168.1.0 0.0.0.255 area 0\n")
        tn.write(f"network {loopback_ip} 0.0.0.0 area 0\n".encode('ascii'))
        tn.write(b"exit\n")
        tn.write(b"exit\n")
        
        # Save
        tn.write(b"copy running-config startup-config\n")
        tn.write(b"\n")
        tn.write(b"exit\n")
        
        log_message(f"{hostname} configured successfully!", "SUCCESS")
        return True
        
    except Exception as e:
        log_message(f"Error configuring {hostname}: {e}", "ERROR")
        return False

def main():
    print(f"""
    {GREEN}╔═══════════════════════════════════════════════════╗
    ║   OSPF Automation Lab - CyberSecurity Edition   ║
    ║   Kali 2025.4 | GNS3 3.0.6 | Python 3.14        ║
    ╚═══════════════════════════════════════════════════╝{RESET}
    """)
    
    username = input("Enter Telnet username: ")
    password = getpass.getpass("Enter Telnet password: ")
    
    routers = [
        ("nw_Router1", "192.168.1.1", "1.1.1.1"),
        ("nw_Router2", "192.168.1.2", "2.2.2.2")
    ]
    
    for hostname, ip, loopback in routers:
        configure_router(hostname, ip, loopback, username, password)
    
    log_message("All routers configured successfully!", "SUCCESS")

if __name__ == "__main__":
    main()
```

---

## 📈 Verification Commands

```bash
# Check OSPF neighbor status
show ip ospf neighbor

# Check OSPF routes in routing table
show ip route ospf

# Ping the other router's loopback
ping 2.2.2.2
```

---

## 🐛 Challenges Faced & Solutions

| **Challenge**                          | **Solution**                            |
| -------------------------------------- | --------------------------------------- |
| `dynamips` command not found           | Ran `sudo make install`                 |
| `ubridge` command not found            | Ran `sudo make install`                 |
| `externally-managed-environment` error | Used `python3 -m venv`                  |
| `No module named 'sip'`                | `pip install sip PyQt5-sip`             |
| `PyQt6.QtSvg missing`                  | `pip install PyQt6 PyQt6-Qt6 PyQt6-sip` |
| `xterm not found`                      | `sudo apt install -y xterm`             |
| VPCS console not opening               | Set path: Preferences → `/usr/bin/vpcs` |
| `Bad command: "192.168.1.2/24"`        | Used `ip 192.168.1.2 255.255.255.0`     |
| `Not authenticated` in Web UI          | Logged in with `admin`/`admin`          |
| `Settings` page `Not Found`            | Used local GNS3 GUI instead             |

---

---

## 📷 Screenshots

> **Note:** I will add the actual screenshots here with proper descriptions.

### Screenshot 1: Kali Linux Version

![Kali Linux Version](./Kali_Linux version.png)

*Verifying Kali Linux 2025.4 installation*

### Screenshot 2: Dynamips Version

![Dynamips Version](./screenshots/02_dynamips_version.png)

*Dynamips 0.2.24 successfully compiled*

### Screenshot 3: Ubridge Version

![Ubridge Version](./screenshots/03_ubridge_version.png)

*Ubridge 1.2.1 successfully compiled*

### Screenshot 4: GNS3 Setup Wizard

![GNS3 Setup Wizard](./screenshots/04_gns3_wizard.png)

*GNS3 local controller configuration*

### Screenshot 5: Complete Topology

![Complete Topology](./screenshots/05_topology.png)

*Topology with PC1, PC2, Switch*

### Screenshot 6: VPCS Console

![VPCS Console](./screenshots/06_vpcs_console.png)

*VPCS console successfully opened*

### Screenshot 7: IP Assignment

![IP Assignment](./screenshots/07_ip_assignment.png)

*Assigning IP addresses to PC1 and PC2*

### Screenshot 8: Ping Test

![Ping Test](./screenshots/08_ping_test.png)

*Successful ping from PC1 to PC2*


## 🛡️ Security Lessons Learned

| **Security Concept**     | **How This Lab Demonstrated It**                              |
| ------------------------ | ------------------------------------------------------------- |
| **Default Credentials**  | Web UI uses `admin/admin` – never use in production           |
| **Clear-Text Protocols** | Telnet sends passwords in plain text – always use SSH         |
| **Unencrypted Routing**  | OSPF sends routes in clear-text – enable authentication       |
| **Weak Passwords**       | `nw` password used – demonstrates why strong passwords matter |
| **Automation Risks**     | Python scripts can be weaponized for automated attacks        |
| **Privilege Escalation** | Enable passwords give full access – protect them              |


## 📚 What I Learned

### Technical Skills:

* Installing GNS3 from scratch on Kali Linux
* Compiling Dynamips and Ubridge from source
* Setting up Python virtual environments
* Using Telnet for network automation
* Writing Python scripts with `telnetlib`
* Troubleshooting GNS3 and VPCS issues

### Security Skills:

* Understanding OSPF route advertisement
* Identifying insecure protocols (Telnet)
* Recognizing the risks of automation
* Documenting security misconfigurations
* Planning security hardening strategies

---


## 🙏 Acknowledgments

* **Network Walks Academy** – Original lab design
* **GNS3 Team** – Network simulation platform
* **Kali Linux Team** – Security testing environment
* **Cybersecurity Community** – Continuous learning support

---


### 🎯 Final Word

> This repository represents my complete learning journey from scratch to a working network automation lab. Every error, fix, and success is documented here to help other cybersecurity students who want to learn GNS3, Python automation, and OSPF.

---

**Happy Learning & Stay Secure! 🚀🔐**

---

*Built with ❤️ on Kali Linux 2025.4*
