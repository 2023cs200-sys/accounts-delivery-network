# IP Addressing Table

This document defines the IPv4 addressing plan used in the **Accounts & Delivery Network**.

The original network `192.168.40.0/24` was subnetted into two `/25` subnets to separate the Accounts and Delivery departments.

---

## 1. Network Summary

| Parameter               | Value                |
| ----------------------- | -------------------- |
| Original Network        | `192.168.40.0/24`    |
| New Subnet Mask         | `255.255.255.128`    |
| CIDR                    | `/25`                |
| Number of Subnets       | `2`                  |
| Addresses per Subnet    | `128`                |
| Usable Hosts per Subnet | `126`                |
| Subnetting Method       | IPv4 CIDR Subnetting |

---

## 2. Subnet Allocation

| Department | Network Address  | CIDR  | Subnet Mask       | Default Gateway  | Usable Host Range                 | Broadcast Address |
| ---------- | ---------------- | ----- | ----------------- | ---------------- | --------------------------------- | ----------------- |
| Accounts   | `192.168.40.0`   | `/25` | `255.255.255.128` | `192.168.40.1`   | `192.168.40.1 - 192.168.40.126`   | `192.168.40.127`  |
| Delivery   | `192.168.40.128` | `/25` | `255.255.255.128` | `192.168.40.129` | `192.168.40.129 - 192.168.40.254` | `192.168.40.255`  |

---

## 3. Network Address Details

### Accounts Subnet

```text
Network Address : 192.168.40.0
Subnet Mask     : 255.255.255.128
CIDR            : /25
First Host      : 192.168.40.1
Last Host       : 192.168.40.126
Broadcast       : 192.168.40.127
Default Gateway : 192.168.40.1
```

### Delivery Subnet

```text
Network Address : 192.168.40.128
Subnet Mask     : 255.255.255.128
CIDR            : /25
First Host      : 192.168.40.129
Last Host       : 192.168.40.254
Broadcast       : 192.168.40.255
Default Gateway : 192.168.40.129
```

---

## 4. Device Addressing

The following table provides the IP addressing for the network devices. The Router interface entries match the captured `router-config.txt`.

The end-device entries are the documented addressing plan; end-device configuration is not included in the router capture.

| Device           | Department | Interface | IP Address       | Subnet Mask       | Default Gateway  |
| ---------------- | ---------- | --------- | ---------------- | ----------------- | ---------------- |
| Router           | Accounts   | `G0/0`    | `192.168.40.1`   | `255.255.255.128` | N/A              |
| Router           | Delivery   | `G0/1`    | `192.168.40.129` | `255.255.255.128` | N/A              |
| Accounts PC 1    | Accounts   | NIC       | `192.168.40.10`  | `255.255.255.128` | `192.168.40.1`   |
| Accounts PC 2    | Accounts   | NIC       | `192.168.40.11`  | `255.255.255.128` | `192.168.40.1`   |
| Accounts Printer | Accounts   | NIC       | `192.168.40.20`  | `255.255.255.128` | `192.168.40.1`   |
| Delivery PC 1    | Delivery   | NIC       | `192.168.40.140` | `255.255.255.128` | `192.168.40.129` |
| Delivery PC 2    | Delivery   | NIC       | `192.168.40.141` | `255.255.255.128` | `192.168.40.129` |
| Delivery Printer | Delivery   | NIC       | `192.168.40.150` | `255.255.255.128` | `192.168.40.129` |

---

## 5. Address Allocation Rules

The following rules are used when assigning addresses:

* The **network address** is reserved to identify the subnet.
* The **broadcast address** is reserved for broadcast communication.
* The **default gateway** is assigned to the router interface connected to the corresponding subnet.
* Host devices are assigned addresses from the usable host range.
* Each device must have a unique IP address within its subnet.
* Devices in different subnets use their default gateway when communicating with another network.

---

## 6. Subnetting Calculation

The original network is:

```text
192.168.40.0/24
```

Two separate departmental networks are required.

### Required Subnets

```text
Required Subnets = 2
```

Using:

```text
2ⁿ ≥ Required Subnets
```

```text
2¹ ≥ 2
```

Therefore:

```text
Borrowed Bits = 1
```

The original prefix changes from:

```text
/24
```

to:

```text
/25
```

### New Subnet Mask

```text
/25 = 255.255.255.128
```

### Host Bits

```text
32 - 25 = 7 host bits
```

### Total Addresses per Subnet

```text
2⁷ = 128 addresses
```

### Usable Host Addresses

```text
128 - 2 = 126 usable addresses
```

The two reserved addresses are:

```text
Network Address
Broadcast Address
```

---

## 7. Subnet Ranges

The `/25` subnet mask creates a block size of:

```text
256 - 128 = 128
```

Therefore, the subnet boundaries are:

```text
192.168.40.0
192.168.40.128
```

### Subnet 1

```text
192.168.40.0/25
```

Range:

```text
Network     → 192.168.40.0
Usable      → 192.168.40.1 - 192.168.40.126
Broadcast   → 192.168.40.127
```

### Subnet 2

```text
192.168.40.128/25
```

Range:

```text
Network     → 192.168.40.128
Usable      → 192.168.40.129 - 192.168.40.254
Broadcast   → 192.168.40.255
```

---

## 8. Gateway Assignment

Each subnet uses a router interface as its default gateway.

```text
Accounts
    │
    └── Default Gateway → 192.168.40.1
```

```text
Delivery
    │
    └── Default Gateway → 192.168.40.129
```

The router provides communication between the two departmental subnets.

---

## 9. DNS Configuration

DNS is not required for basic IP connectivity testing in this project.

If DNS configuration is added later, the DNS server address can be documented here.

```text
Preferred DNS : Not configured
Alternate DNS : Not configured
```

---

## 10. Connectivity Testing

Connectivity should be verified using ICMP `ping`.

### Same-Subnet Test

Example:

```bash
ping 192.168.40.11
```

This verifies connectivity between two devices in the Accounts subnet.

### Inter-Subnet Test

From an Accounts device:

```bash
ping 192.168.40.140
```

This verifies communication from the Accounts subnet to the Delivery subnet through the router.

### Gateway Test

From an Accounts device:

```bash
ping 192.168.40.1
```

From a Delivery device:

```bash
ping 192.168.40.129
```

Successful replies indicate that the corresponding default gateway is reachable.

---

## 11. Addressing Summary

```text
Original Network
192.168.40.0/24

                SUBNETTING
                    │
                    ▼

        ┌───────────────────────┐
        │                       │
        ▼                       ▼

 Accounts Subnet          Delivery Subnet
 192.168.40.0/25         192.168.40.128/25

 Gateway: .1             Gateway: .129

 Hosts:                   Hosts:
 .1 - .126               .129 - .254

 Broadcast: .127         Broadcast: .255
```

---

## 12. Notes

* All IP addresses in this document belong to the private IPv4 address space used for the simulated network.
* The addressing plan is designed for the Cisco Packet Tracer topology included in this repository.
* IP addresses should not be duplicated between devices.
* Any changes made to the Packet Tracer topology should also be reflected in this document.
* The final addressing table should match the actual configuration stored in the `.pkt` file.
