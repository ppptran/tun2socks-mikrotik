# tun2socks for MikroTik (Container)

🔀 A lightweight containerized tun2socks forwarder designed to run on
MikroTik RouterOS v7 containers.

This image allows you to route selected traffic through a SOCKS5 proxy
using a TUN interface.

---

## 🙏 Credits

Huge thanks to **xjasonlyu / tun2socks** for creating such a great tool.  
This image is simply adapted for MikroTik Container usage.

---

## ⚠ IMPORTANT NOTICE (January 2026)

Starting from **MikroTik RouterOS v7.20.7 (Long-term)**,  
MikroTik changed container networking behavior.

🚨 A new environment variable is now **REQUIRED**:

**VETH**

Without this variable, the container will **not work correctly**.

---

## 📦 Requirements

- MikroTik RouterOS v7.20.7 or newer
- Container feature enabled
- A working SOCKS5 server
- Basic MikroTik networking knowledge  
  (VETH, bridge, routing / mangle)

---

## 🚀 How to Use

### 🔧 Step 1: Create a VETH Interface

Create a VETH interface and assign it an IP address.

Example:

VETH name: veth4  
IP address: 172.18.1.4/24  
Gateway: 172.18.1.1
/images/Step1-.png

---

### 🌉 Step 2: Create Docker Bridge

1. Create a bridge (example: bridge-docker)
2. Add the VETH interface to this bridge
3. Assign an IP address to the bridge

Example (Winbox → IP → Addresses):

Address: 172.18.1.1/24  
Network: 172.18.1.0  
Interface: bridge-docker

---

### 🧩 Step 3: Configure Environment Variables

Go to:

Container → ENV → Add

You may name the environment list anything you want.  
Example name used below: **tun**

**Environment variables:**

**SOCKS5_SVR**  
IP address of your SOCKS5 server

**SOCKS5_PORT**  
Port of your SOCKS5 server

**SOCK5_USER**  
Username for SOCKS5 authentication

**SOCK5_PASS**  
Password for SOCKS5 authentication

**CT_GATE**  
Gateway IP of the VETH bridge  
Example: 172.18.1.1

**VETH**  
Name of the VETH interface used by the container  
Example: veth4

**LC_ROUTE1**  
Subnet of LAN network 1  
Example: 192.168.81.0

**LC_ROUTE2** (optional)  
Subnet of LAN network 2  
Example: 192.168.25.0

---

### 📦 Step 4: Create the Container

Container → Add

Interface: veth4  
Remote Image: ppptran/tun2socks-mikrotik:latest  
Envlist: tun  
Root Dir: Tun2socks  

Enable the following options:
- Logging
- Start On Boot

⚠ **Important**

Do **NOT** manually create the `Tun2socks` folder.  
MikroTik will create it automatically.

If this folder already exists, delete it first.  
Otherwise, you may encounter **Error 255**.

Apply and start the container.

---

## 🔁 Routing Traffic

To route traffic through tun2socks:

- Use **mangle rules** and set gateway to:
  172.18.1.4

OR

- Use **MikroTik routing rules** to forward traffic
  via the container gateway

The IP address **172.18.1.4** is the exit gateway  
of the tun2socks container.

---

## 📝 Notes

- Designed for MikroTik RouterOS v7 containers
- **VETH variable is mandatory** on RouterOS v7.20.7+
- Traffic routing can be done via mangle or routing rules

---

## 🧑‍💻 Upstream Project

tun2socks by xjasonlyu  
https://github.com/xjasonlyu/tun2socks
