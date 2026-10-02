# Accounts & Delivery Network — Cisco Packet Tracer

A small enterprise-style network designed and implemented in **Cisco Packet Tracer** to demonstrate subnetting, inter-department communication, router-based inter-subnet routing, and basic network configuration.

The network separates the **Accounts** and **Delivery** departments into different IP subnets while allowing controlled communication between them through a router.

---

## 📌 Project Overview

This project simulates a simple office network with two departments:

* **Accounts**
* **Delivery**

The original network `192.168.40.0/24` is divided into two smaller subnets using **CIDR-based subnetting**.

### Network Design

```text
                         Router
                    ┌─────────────┐
                    │             │
          G0/0      │             │      G0/1
      192.168.40.1  │             │  192.168.40.129
                    │             │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              │                         │
          Accounts                   Delivery
       192.168.40.0/25          192.168.40.128/25
              │                         │
           Switch                    Switch
              │                         │
       ┌──────┴──────┐           ┌──────┴──────┐
       │             │           │             │
      PCs          Printer      PCs          Printer
```

---

## 🎯 Project Objectives

The main objectives of this project are to:

* Understand IPv4 addressing and subnetting
* Divide a `/24` network into multiple subnets
* Configure IP addresses for network devices
* Configure router interfaces
* Configure default gateways
* Connect multiple devices using switches
* Enable communication between different subnets
* Understand network and broadcast addresses
* Test connectivity using `ping`
* Document the complete network configuration

---

## 🌐 Network Addressing

The original network is:

```text
192.168.40.0/24
```

Two departments are required, so one host bit is borrowed for subnetting.

```text
2¹ = 2 subnets
```

Therefore:

```text
/24 → /25
```

The new subnet mask is:

```text
255.255.255.128
```

### Subnet 1 — Accounts

```text
Network:      192.168.40.0/25
Gateway:      192.168.40.1
Usable Range: 192.168.40.1 - 192.168.40.126
Broadcast:    192.168.40.127
```

### Subnet 2 — Delivery

```text
Network:      192.168.40.128/25
Gateway:      192.168.40.129
Usable Range: 192.168.40.129 - 192.168.40.254
Broadcast:    192.168.40.255
```

---

## 📊 IP Addressing Summary

| Department | Network             | Subnet Mask       | Default Gateway  | Usable Host Range | Broadcast |
| ---------- | ------------------- | ----------------- | ---------------- | ----------------- | --------- |
| Accounts   | `192.168.40.0/25`   | `255.255.255.128` | `192.168.40.1`   | `.1 – .126`       | `.127`    |
| Delivery   | `192.168.40.128/25` | `255.255.255.128` | `192.168.40.129` | `.129 – .254`     | `.255`    |

Each `/25` subnet provides:

```text
128 total addresses
126 usable host addresses
```

---

## 🔢 Subnetting Calculation

### Original Network

```text
192.168.40.0/24
```

The `/24` network contains:

```text
24 Network Bits
8 Host Bits
```

We need:

```text
2 Subnets
```

Using:

```text
2ⁿ ≥ Required Subnets
```

Therefore:

```text
2¹ = 2
```

So, **1 host bit is borrowed**.

```text
/24 → /25
```

### New Subnet Mask

```text
/25
=
255.255.255.128
```

### Host Bits

```text
32 - 25 = 7
```

Total addresses per subnet:

```text
2⁷ = 128
```

Usable host addresses:

```text
128 - 2 = 126
```

### Block Size

```text
256 - 128 = 128
```

Therefore, the subnet boundaries are:

```text
192.168.40.0
192.168.40.128
```

---

## 🔀 Inter-Subnet Communication

The Accounts and Delivery departments belong to different IP subnets.

```text
Accounts
192.168.40.0/25

        ↕
      Router
        ↕

Delivery
192.168.40.128/25
```

The router acts as the **default gateway** for each subnet.

### Accounts Gateway

```text
192.168.40.1
```

### Delivery Gateway

```text
192.168.40.129
```

When a device needs to communicate with a destination outside its local subnet, it forwards the packet to its configured default gateway.

---

## Router Configuration

The captured router configuration is from a Cisco 2911 running IOS 15.1 with the hostname `Router`.

| Interface | IP Address       | Subnet Mask       | State              |
| --------- | ---------------- | ----------------- | ------------------ |
| `GigabitEthernet0/0` | `192.168.40.1`   | `255.255.255.128` | Not administratively shut down |
| `GigabitEthernet0/1` | `192.168.40.129` | `255.255.255.128` | Not administratively shut down |
| `GigabitEthernet0/2` | None             | None              | Shutdown           |
| `Vlan1`              | None             | None              | Shutdown           |

The two configured GigabitEthernet interfaces are directly connected to the Accounts and Delivery subnets. No static routes are required for communication between these directly connected networks.

The complete captured configuration is available in [configs/router-config.txt](configs/router-config.txt).

---

## 🖥️ Technologies & Tools

* **Cisco Packet Tracer**
* IPv4
* CIDR
* Subnetting
* Ethernet
* Cisco IOS
* Routers
* Layer 2 Switches
* Default Gateway
* ICMP / Ping
* Basic CLI Configuration

---

## 🧪 Connectivity Testing

Connectivity is tested between devices in the same subnet and between devices in different subnets.

Example:

```text
Accounts PC
192.168.40.x
       │
       │ ping
       ↓
Delivery PC
192.168.40.x
```

Example command:

```bash
ping 192.168.40.150
```

Successful replies confirm that the devices can communicate through the configured network infrastructure.

---

## 📁 Project Structure

```text
accounts-delivery-network/
│
├── README.md
├── accounts-delivery-network.pkt
│
├── documentation/
│   ├── ip-addressing-table.md
│   └── configuration-notes.md
│
├── screenshots/
│   ├── router-config.png
│   ├── topology.png
│   ├── ip-configuration.png
│   └── connectivity-test.png
│
└── configs/
    ├── router-config.txt
    └── switch-config.txt
```

---

## 📸 Documentation

The `documentation/` directory contains the main network documentation, including:

* Network topology diagram
* IP addressing table
* Configuration notes

The `screenshots/` directory contains evidence of the configuration and connectivity tests performed in Cisco Packet Tracer.

---

## 💡 Key Networking Concepts Demonstrated

This project demonstrates practical understanding of:

### IPv4 Addressing

Understanding how IPv4 addresses are divided into network and host portions.

### CIDR

Using CIDR notation such as:

```text
192.168.40.0/25
```

to define network boundaries.

### Subnetting

Dividing:

```text
192.168.40.0/24
```

into:

```text
192.168.40.0/25
192.168.40.128/25
```

### Default Gateway

Using router interfaces as gateways for devices in each subnet.

### Routing

Allowing communication between the Accounts and Delivery subnets through a router.

### Network & Broadcast Addresses

Identifying the first and last addresses of each subnet.

### Connectivity Testing

Using ICMP `ping` to verify network connectivity.

---

## 🚀 Learning Outcomes

Through this project, I gained practical experience in:

* Designing a small enterprise network
* Performing IPv4 subnetting calculations
* Creating IP addressing plans
* Configuring Cisco routers and switches
* Understanding default gateways
* Troubleshooting connectivity issues
* Verifying communication between different subnets
* Documenting network infrastructure
* Using Cisco Packet Tracer for network simulation
