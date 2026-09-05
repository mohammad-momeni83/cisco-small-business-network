# Cisco Small Business Network

A small business network designed and implemented in **Cisco Packet Tracer**, focusing on VLAN segmentation, inter-VLAN routing, network services, wireless connectivity, and basic network security.

> **Project Status:** Phase 1 — Internal Network ✅ Completed

> **Project Status:** Phase 2 — Network Services, Wireless & Security ✅ Completed

---

## 📌 Project Overview

ABC Company requires a structured, segmented, and manageable internal network for different departments and users.

The network is divided into multiple VLANs to provide:

* Logical network segmentation
* Reduced broadcast domains
* Department isolation
* Better network organization
* Inter-VLAN communication
* Centralized DHCP services
* Internal DNS resolution
* Wireless connectivity
* Guest network isolation
* Basic network access control

The network uses a **Cisco Switch** and **Cisco Router** with a **Router-on-a-Stick** architecture.

A dedicated server provides **DHCP and DNS services**, while the router performs DHCP relay between VLANs.

---

# 🏗️ Network Topology

The network consists of:

![Network Topology](topology/network-topology.png)

---

# 🧩 VLAN Design

| VLAN ID | Department / Purpose | Network           | Default Gateway |
| ------: | -------------------- | ----------------- | --------------- |
|      10 | IT                   | `192.168.10.0/24` | `192.168.10.1`  |
|      20 | Sales                | `192.168.20.0/24` | `192.168.20.1`  |
|      30 | HR                   | `192.168.30.0/24` | `192.168.30.1`  |
|      40 | Servers              | `192.168.40.0/24` | `192.168.40.1`  |
|      50 | Management           | `192.168.50.0/24` | `192.168.50.1`  |
|      60 | Staff Wi-Fi          | `192.168.60.0/24` | `192.168.60.1`  |
|      70 | Guest Wi-Fi          | `192.168.70.0/24` | `192.168.70.1`  |

![Network Topology](topology/vlan-simulation.gif)
---

# 💻 End Devices

## IT — VLAN 10

| Device   | IP Address      |
| -------- | --------------- |
| IT-PC-01 | `192.168.10.10` |
| IT-PC-02 | `192.168.10.11` |

## Sales — VLAN 20

| Device      | IP Address      |
| ----------- | --------------- |
| Sales-PC-01 | `192.168.20.20` |
| Sales-PC-02 | `192.168.20.21` |
| Sales-PC-03 | `192.168.20.22` |
| Sales-PC-04 | `192.168.20.23` |
| Sales-PC-05 | `192.168.20.24` |

## HR — VLAN 30

| Device   | IP Address      |
| -------- | --------------- |
| HR-PC-01 | `192.168.30.30` |

## Servers — VLAN 40

| Device    | IP Address      |
| --------- | --------------- |
| Server-01 | `192.168.40.10` |

**Server-01 provides:**

* DHCP Server
* DNS Server

## Management — VLAN 50

| Device        | IP Address      |
| ------------- | --------------- |
| Manager-PC-01 | `192.168.50.40` |
| Manager-PC-02 | `192.168.50.41` |

## Wireless Networks

| VLAN | Network           | Purpose                |
| ---: | ----------------- | ---------------------- |
|   60 | `192.168.60.0/24` | Staff Wireless Network |
|   70 | `192.168.70.0/24` | Guest Wireless Network |

---

# ⚙️ Technologies & Networking Concepts

The project implements the following technologies and concepts:

* IPv4 Addressing
* Subnetting
* VLANs
* Layer 2 Switching
* Layer 3 Routing
* Access Ports
* 802.1Q Trunking
* Router-on-a-Stick
* Router Subinterfaces
* Inter-VLAN Routing
* Default Gateways
* DHCP
* DHCP Relay
* `ip helper-address`
* DNS
* Wireless Networking
* Staff and Guest VLANs
* Extended ACLs
* Guest Network Isolation
* ICMP / Ping Testing
* Cisco IOS CLI
* Cisco Packet Tracer

---

# 🔧 Implementation

## 1. VLAN Configuration

VLANs were created on the switch to logically separate different departments and network segments.

```cisco
enable
configure terminal

vlan 10
name IT

vlan 20
name SALES

vlan 30
name HR

vlan 40
name SERVERS

vlan 50
name MANAGEMENT

vlan 60
name STAFF_WIFI

vlan 70
name GUEST_WIFI
```

---

## 2. Access Port Configuration

End-device ports are configured as access ports and assigned to their corresponding VLANs.

Example:

```cisco
interface fastEthernet 0/1
switchport mode access
switchport access vlan 10
no shutdown
```

This assigns the interface to VLAN 10.

---

## 3. 802.1Q Trunk Configuration

The connection between the switch and router is configured as an **802.1Q trunk**.

```cisco
interface gigabitEthernet 0/1
switchport mode trunk
no shutdown
```

The trunk allows traffic from multiple VLANs to travel over a single physical connection.

---

# 🔀 Router-on-a-Stick

A single physical router interface is divided into multiple logical subinterfaces.

Example:

```cisco
interface gigabitEthernet 0/0
no shutdown

interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
```

The router uses separate subinterfaces as default gateways for each VLAN.

```text
G0/0.10 → VLAN 10 → 192.168.10.1
G0/0.20 → VLAN 20 → 192.168.20.1
G0/0.30 → VLAN 30 → 192.168.30.1
G0/0.40 → VLAN 40 → 192.168.40.1
G0/0.50 → VLAN 50 → 192.168.50.1
G0/0.60 → VLAN 60 → 192.168.60.1
G0/0.70 → VLAN 70 → 192.168.70.1
```

---

# 🔀 Inter-VLAN Routing

Before routing was configured, devices in different VLANs were isolated at Layer 2.

After implementing Router-on-a-Stick, the router provides Layer 3 communication between the VLANs.

Example:

```text
IT PC
192.168.10.10
      │
      ▼
192.168.10.1
      │
   Router
      │
192.168.20.1
      │
      ▼
Sales PC
192.168.20.20
```
![Network Topology](topology/inter-vlan-routing-simulation.gif)

This allows communication between different VLANs through the router.

---

# 📡 DHCP

A centralized DHCP server was implemented on **Server-01 (****`192.168.40.10`****)**.

The DHCP server provides IP configuration to clients across multiple VLANs.

DHCP provides clients with:

* IP Address
* Subnet Mask
* Default Gateway
* DNS Server

Separate DHCP pools are configured for each client VLAN.

Example:

```text
VLAN 10
Network:        192.168.10.0/24
Default Gateway: 192.168.10.1

VLAN 20
Network:        192.168.20.0/24
Default Gateway: 192.168.20.1

VLAN 30
Network:        192.168.30.0/24
Default Gateway: 192.168.30.1

VLAN 50
Network:        192.168.50.0/24
Default Gateway: 192.168.50.1

VLAN 60
Network:        192.168.60.0/24
Default Gateway: 192.168.60.1

VLAN 70
Network:        192.168.70.0/24
Default Gateway: 192.168.70.1
```

---

# 🔁 DHCP Relay — `ip helper-address`

Because DHCP Discover messages are broadcast traffic, they normally do not cross router boundaries.

The router is therefore configured as a **DHCP Relay Agent**.

Example:

```cisco
interface gigabitEthernet 0/0.10
ip helper-address 192.168.40.10
```

The same configuration is applied to the other client VLANs.

```text
Client
  │
  │ DHCP Discover
  ▼
VLAN Gateway
192.168.X.1
  │
  │ DHCP Relay
  │ ip helper-address
  ▼
DHCP Server
192.168.40.10
```

The router receives the DHCP broadcast and forwards the request to the centralized DHCP server.

---

# 🌐 DNS

A DNS service was configured on **Server-01 (****`192.168.40.10`****)**.

DNS provides name resolution inside the network.

Instead of accessing a device only by its IP address:

```text
192.168.40.10
```

a hostname can be resolved to the corresponding IP address.

Example:

```text
server.abc.local
        ↓
192.168.40.10
```

Clients receive the DNS server address through DHCP.

---

# 📶 Wireless Networking

Wireless connectivity was added to the network using separate VLANs for staff and guest users.

## Staff Wi-Fi — VLAN 60

```text
SSID: Staff-WiFi
VLAN: 60
Network: 192.168.60.0/24
Gateway: 192.168.60.1
```

Staff wireless clients receive their network configuration through DHCP.

## Guest Wi-Fi — VLAN 70

```text
SSID: Guest-WiFi
VLAN: 70
Network: 192.168.70.0/24
Gateway: 192.168.70.1
```

Guest traffic is separated from internal company networks using an extended ACL.

---

# 🔐 Network Security — ACL

An extended ACL was implemented to isolate the Guest VLAN from internal company networks.

The Guest network is prevented from accessing:

* IT
* Sales
* HR
* Servers
* Management
* Staff Wi-Fi

Example:

```cisco
ip access-list extended GUEST-FILTER

deny ip 192.168.70.0 0.0.0.255 192.168.10.0 0.0.0.255
deny ip 192.168.70.0 0.0.0.255 192.168.20.0 0.0.0.255
deny ip 192.168.70.0 0.0.0.255 192.168.30.0 0.0.0.255
deny ip 192.168.70.0 0.0.0.255 192.168.40.0 0.0.0.255
deny ip 192.168.70.0 0.0.0.255 192.168.50.0 0.0.0.255
deny ip 192.168.70.0 0.0.0.255 192.168.60.0 0.0.0.255

permit ip any any
```

The ACL is applied inbound on the Guest VLAN interface:

```cisco
interface gigabitEthernet 0/0.70
ip access-group GUEST-FILTER in
```

### Security Objective

```text
Guest VLAN 70
      │
      ├── ❌ IT
      ├── ❌ Sales
      ├── ❌ HR
      ├── ❌ Servers
      ├── ❌ Management
      └── ❌ Staff Wi-Fi
```

This provides basic network segmentation and guest isolation.

---

# 🧪 Network Testing & Validation

The network was tested using ICMP, DHCP, DNS, and ACL validation.

## Gateway Tests

```text
IT PC → 192.168.10.1       ✅
Sales PC → 192.168.20.1    ✅
HR PC → 192.168.30.1       ✅
Server → 192.168.40.1      ✅
Management → 192.168.50.1  ✅
```

## Inter-VLAN Tests

```text
IT → Sales          ✅
IT → HR             ✅
IT → Server         ✅
IT → Management     ✅
```

## DHCP Tests

```text
IT Client → DHCP Address      ✅
Sales Client → DHCP Address  ✅
HR Client → DHCP Address     ✅
Wi-Fi Client → DHCP Address  ✅
```

## DNS Tests

```text
Client → DNS Server          ✅
Hostname → IP Resolution    ✅
```

## ACL Tests

```text
Guest → Internal Networks   ❌ Blocked
Guest → Guest Network       ✅
```

The successful tests confirm that:

* VLANs are correctly assigned
* The trunk link is functioning
* Router subinterfaces are operational
* Default gateways are correctly configured
* Inter-VLAN routing is working
* DHCP pools are functioning
* DHCP Relay is working
* DNS resolution is available
* Wireless clients can obtain network configuration
* Guest traffic is isolated using ACLs

---

# 🔍 Cisco Verification Commands

The following Cisco IOS commands can be used to verify and troubleshoot the network.

### VLANs

```cisco
show vlan brief
```

### Trunk

```cisco
show interfaces trunk
```

### MAC Address Table

```cisco
show mac address-table
```

### Router Interfaces

```cisco
show ip interface brief
```

### Routing Table

```cisco
show ip route
```

### ACL

```cisco
show access-lists
```

### Connectivity

```cisco
ping 192.168.10.1
ping 192.168.40.10
```

These commands are useful for identifying VLAN, trunk, routing, addressing, and ACL configuration issues.

---

# 🧠 Troubleshooting Experience

During the implementation, several configuration issues were identified and resolved.

One example was an incorrect server IP address.

The server was initially configured with:

```text
192.168.1.1
```

while the Server VLAN was:

```text
192.168.40.0/24
```

The server was therefore moved to:

```text
192.168.40.10
```

with:

```text
Default Gateway: 192.168.40.1
```

After correcting the addressing, connectivity between the server and other VLANs was restored.

This demonstrates the importance of matching:

```text
VLAN
   ↓
Subnet
   ↓
IP Address
   ↓
Default Gateway
```

---

# 📁 Project Structure

Recommended repository structure:

```text
cisco-small-business-network/
│
├── README.md
│
├── topology/
│   ├── topology.png
│   ├── vlan-simulation.gif
│   └── inter-vlan-routing-simulation.gif
│
├── packet-tracer/
│   └── cisco-small-business-network.pkt
│
└── documentation/
    └── network-notes.md
```

---

# 🚧 Future Improvements

The current project focuses on internal networking, network services, wireless connectivity, and basic security.

Possible future improvements include:

### Phase 3 — Advanced Security

* [ ] More granular ACL policies
* [ ] Server-specific access policies
* [ ] Management VLAN restrictions
* [ ] Port Security
* [ ] DHCP Snooping
* [ ] Dynamic ARP Inspection

### Phase 4 — Network Troubleshooting

* [ ] Create intentional VLAN failures
* [ ] Create trunk configuration failures
* [ ] Test incorrect default gateways
* [ ] Test DHCP Relay failures
* [ ] Test DNS failures
* [ ] Test ACL misconfigurations
* [ ] Document troubleshooting procedures

### Phase 5 — Network Management

* [ ] SNMP
* [ ] Syslog
* [ ] Network monitoring
* [ ] Configuration backup
* [ ] Centralized network documentation

### Future Expansion

* [ ] Remote user connectivity
* [ ] VPN simulation
* [ ] Additional switches
* [ ] Redundant network design
* [ ] Server redundancy

> **Note:** NAT/PAT and Internet connectivity are intentionally not included in the current project scope.

---

# 🎯 Learning Objectives

This project was built to gain practical hands-on experience with:

* Cisco IOS CLI
* VLAN architecture
* Layer 2 switching
* Layer 3 routing
* 802.1Q trunking
* Router-on-a-Stick
* IP addressing and subnetting
* DHCP
* DHCP Relay
* DNS
* Wireless networking
* ACLs
* Network segmentation
* Guest network isolation
* Network troubleshooting
* Basic enterprise network design
* Network documentation

---

# 🛠️ Tools

* **Cisco Packet Tracer**
* **Cisco IOS CLI**

---

# 📌 Project Status

| Component                | Status          |
| ------------------------ | --------------- |
| VLANs                    | ✅ Completed     |
| Access Ports             | ✅ Completed     |
| IP Addressing            | ✅ Completed     |
| 802.1Q Trunking          | ✅ Completed     |
| Router-on-a-Stick        | ✅ Completed     |
| Inter-VLAN Routing       | ✅ Completed     |
| DHCP Server              | ✅ Completed     |
| DHCP Relay               | ✅ Completed     |
| `ip helper-address`      | ✅ Completed     |
| DNS Server               | ✅ Completed     |
| Staff Wi-Fi              | ✅ Completed     |
| Guest Wi-Fi              | ✅ Completed     |
| Guest Network Isolation  | ✅ Completed     |
| Extended ACL             | ✅ Completed     |
| NAT/PAT                  | 🚫 Out of Scope |
| Internet Access          | 🚫 Out of Scope |
| Advanced Troubleshooting | 🔄 Planned      |
| Network Monitoring       | 🔄 Planned      |

---

# 📚 Project Highlights

This project demonstrates a complete small-business internal network implementation using Cisco technologies.

The main focus areas are:

```text
VLAN Segmentation
        ↓
802.1Q Trunking
        ↓
Router-on-a-Stick
        ↓
Inter-VLAN Routing
        ↓
DHCP + DHCP Relay
        ↓
DNS
        ↓
Wireless Networking
        ↓
ACL + Guest Isolation
        ↓
Network Testing & Troubleshooting
```

---

## 👤 Author

**Mohammad Momeni**

**Network Specialist**

Interested in:

* Networking
* Network Support
* Infrastructure
* Network Administration
* Software Development

---
