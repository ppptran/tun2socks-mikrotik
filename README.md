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
IP address: 172.18.0.4/24  
Gateway: 172.18.0.1
![Step1](/images/Step1-.png)


---

### 🌉 Step 2: Create Docker Bridge

1. Create a bridge (example: bridge-docker)
![Step2-1](/images/step2-1.png)
2. Add the VETH interface to this bridge
![Step2-2](/images/step2-2.png)
3. Assign an IP address to the bridge
![Step2-3](/images/step2-3.png)

Example (Winbox → IP → Addresses):

Address: 172.18.0.1/24  
Network: 172.18.0.0  
Interface: bridge-docker



---

### 🧩 Step 3: Configure Environment Variables

Go to:

Container → ENV → Add

You may name the environment list anything you want.  
Example name used below: **masque**

![Step3](/images/step3final.png)

**Environment variables:**

**VETH**  
Name of the VETH interface used by the container  
Example: veth4

**LC_ROUTE2** (optional)  
Subnet of LAN network 2  
Example: 192.168.25.0

**LC_ROUTE1**  
Subnet of LAN network 1  
Example: 192.168.81.0

**CT_GATE**  
Gateway IP of the VETH bridge  
Example: 172.18.0.1

**SOCK5_PASS**  
Password for SOCKS5 authentication

**SOCKS5_SVR**  
IP address of your SOCKS5 server

**SOCK5_USER**  
Username for SOCKS5 authentication

**SOCKS5_PORT**  
Port of your SOCKS5 server



### 📦 Step 4: Create the Container

![Step4](/images/step4.png)

Container → Add

Interface: veth4  
Remote Image: ppptran/tun2socks-mikrotik:2.6.0-amd64 
Envlist: masque 
Root Dir: Tun2socks  

Enable the following options:
- Logging
- Start On Boot

⚠ **Important**

Do **NOT** manually create the `Tun2socks` Root Dir folder.  
MikroTik will create it automatically.

If this folder already exists, delete it first.  
Otherwise, you may encounter **Error 255**.

Apply and start the container.

---

## 🔁 Routing Traffic

To route traffic through tun2socks:

- Use **mangle rules** and set gateway to:
  172.18.0.4

OR

- Use **MikroTik routing rules** to forward traffic
  via the container gateway

The IP address **172.18.0.4** is the exit gateway  
of the tun2socks container.

---
Step4-1 : Create a routing table: 

![Step4-1](/images/Step4-1-create-routing-table.png)

Step 4-2: Make a destination list (IP -> Firewall -> Address List)

Create a new address list, in this example i am going to use telegram destination IP.
Which mean that whenever, i use telegram, Mikrotik will route traffic to this tun2socks container.

![Step4-2-1](/images/Step4-new-teleip.png)

![Step4-2-2](/images/Step4-TeleIP-AddressList.png)

Step 4-3: Routing to the gateway of tun2socks ( IP -> Routes)

Dest.Address: 0.0.0.0/0

Gateway: 172.18.0.4 ( Importance, must type in the ip number instead of selecting veth4)

Routing Table : select -> tele ( from the dropped down list)

![Step4-2-2](/images/step4-routing-rule.png)

## 📝 Notes

- Designed for MikroTik RouterOS v7 containers
- **VETH variable is mandatory** on RouterOS v7.20.7+
- Traffic routing can be done via mangle or routing rules

---

## 🧑‍💻 Upstream Project

tun2socks by xjasonlyu  
https://github.com/xjasonlyu/tun2socks
