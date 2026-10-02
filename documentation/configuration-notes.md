# Configuration Notes

This document describes the configuration used to implement the **Accounts & Delivery Network** in Cisco Packet Tracer.

The network uses IPv4 subnetting to separate the Accounts and Delivery departments into two different subnets. A router is used to provide communication between the two networks.

---

# 1. Network Design

The original network is:

```text
192.168.40.0/24
```

The network is divided into two `/25` subnets.

```text
Accounts
192.168.40.0/25

Delivery
192.168.40.128/25
```

The router provides connectivity between these two subnets.

```text
                       Router
                  ┌─────────────┐
                  │             │
   G0/0           │             │           G0/1
192.168.40.1      │             │      192.168.40.129
                  │             │
                  └──────┬──────┘
                         │
              ┌──────────┴──────────┐
              │                     │
           Accounts              Delivery
         192.168.40.0/25      192.168.40.128/25
              │                     │
           Switch                 Switch
              │                     │
             PCs                   PCs
```

---

# 2. Subnet Configuration

## Accounts Subnet

```text
Network Address : 192.168.40.0
CIDR            : /25
Subnet Mask     : 255.255.255.128
Gateway         : 192.168.40.1
Broadcast       : 192.168.40.127
Host Range      : 192.168.40.1 - 192.168.40.126
```

## Delivery Subnet

```text
Network Address : 192.168.40.128
CIDR            : /25
Subnet Mask     : 255.255.255.128
Gateway         : 192.168.40.129
Broadcast       : 192.168.40.255
Host Range      : 192.168.40.129 - 192.168.40.254
```

---

# 3. Router Configuration

The captured router configuration identifies the device as a Cisco 2911 running IOS 15.1 with the hostname `Router`.

The router has two configured interfaces.

Each interface belongs to a different subnet and acts as the default gateway for that subnet.

## Accounts Interface

The Accounts-side router interface is configured with:

```text
IP Address   : 192.168.40.1
Subnet Mask  : 255.255.255.128
```

Captured interface configuration:

```cisco
interface GigabitEthernet0/0
 ip address 192.168.40.1 255.255.255.128
 duplex auto
 speed auto
```

The running configuration does not contain a `shutdown` command for this interface, so it is not administratively disabled.

---

## Delivery Interface

The Delivery-side router interface is configured with:

```text
IP Address   : 192.168.40.129
Subnet Mask  : 255.255.255.128
```

Captured interface configuration:

```cisco
interface GigabitEthernet0/1
 ip address 192.168.40.129 255.255.255.128
 duplex auto
 speed auto
```

The running configuration does not contain a `shutdown` command for this interface, so it is not administratively disabled.

`GigabitEthernet0/2` has no IP address and is administratively shut down. `Vlan1` also has no IP address and is shut down.

---

# 4. Saving the Router Configuration

The captured configuration ends with `end`. The router capture does not show whether the configuration was copied to startup-config.

```cisco
end
copy running-config startup-config
```

When prompted for the destination filename, press `Enter` to accept the default.

The following command can also be used to verify the configuration:

```cisco
show running-config
```

---

# 5. Verifying Router Interfaces

The status of the router interfaces can be checked using:

```cisco
show ip interface brief
```

An expected result is similar to:

```text
Interface              IP-Address       Status       Protocol

GigabitEthernet0/0     192.168.40.1     up           up
GigabitEthernet0/1     192.168.40.129   up           up
```

The exact interface names may differ depending on the router model used in Cisco Packet Tracer.

---

# 6. Switch Configuration

The switches provide Layer 2 connectivity between the end devices and the router.

For this project, the switches are used for basic Ethernet connectivity.

No VLAN configuration is required for the basic version of this project because each department is represented by a separate IP subnet connected through the router.

---

## Accounts Switch

The Accounts PCs and other local devices are connected to the Accounts switch.

Example basic switch configuration:

```cisco
enable
configure terminal

hostname Accounts-Switch

end
copy running-config startup-config
```

---

## Delivery Switch

The Delivery PCs and other local devices are connected to the Delivery switch.

Example:

```cisco
enable
configure terminal

hostname Delivery-Switch

end
copy running-config startup-config
```

The exact switch port configuration depends on the physical topology created in the `.pkt` file.

---

# 7. End Device IP Configuration

End devices are configured with static IPv4 addresses for this project.

## Accounts Devices

Example:

```text
IP Address      : 192.168.40.10
Subnet Mask     : 255.255.255.128
Default Gateway : 192.168.40.1
```

Another Accounts device:

```text
IP Address      : 192.168.40.11
Subnet Mask     : 255.255.255.128
Default Gateway : 192.168.40.1
```

The Accounts devices must use addresses from:

```text
192.168.40.1 - 192.168.40.126
```

---

## Delivery Devices

Example:

```text
IP Address      : 192.168.40.140
Subnet Mask     : 255.255.255.128
Default Gateway : 192.168.40.129
```

Another Delivery device:

```text
IP Address      : 192.168.40.141
Subnet Mask     : 255.255.255.128
Default Gateway : 192.168.40.129
```

The Delivery devices must use addresses from:

```text
192.168.40.129 - 192.168.40.254
```

---

# 8. Default Gateway Configuration

The default gateway tells an end device where to send traffic when the destination is outside its local subnet.

## Accounts

```text
Default Gateway:
192.168.40.1
```

This is the IP address of the router interface connected to the Accounts subnet.

## Delivery

```text
Default Gateway:
192.168.40.129
```

This is the IP address of the router interface connected to the Delivery subnet.

---

# 9. Inter-Subnet Routing

No additional static routes are required for this topology because both networks are directly connected to the same router.

The router has:

```text
192.168.40.0/25
```

directly connected to one interface, and:

```text
192.168.40.128/25
```

directly connected to the other interface.

The router therefore knows how to reach both networks.

The routing table can be checked using:

```cisco
show ip route
```

The routing table should contain directly connected networks similar to:

```text
C    192.168.40.0/25
C    192.168.40.128/25
```

`C` represents a directly connected network.

---

# 10. Connectivity Testing

Connectivity should be tested after configuring all devices.

## Test 1 — Accounts PC to Gateway

From an Accounts PC:

```bash
ping 192.168.40.1
```

Expected result:

```text
Reply from 192.168.40.1
```

This confirms that the Accounts PC can reach its default gateway.

---

## Test 2 — Delivery PC to Gateway

From a Delivery PC:

```bash
ping 192.168.40.129
```

This confirms that the Delivery PC can reach its default gateway.

---

## Test 3 — Same Subnet Communication

For example:

```bash
ping 192.168.40.11
```

This tests communication between two Accounts devices.

---

## Test 4 — Inter-Subnet Communication

From an Accounts PC:

```bash
ping 192.168.40.140
```

The packet should travel through the router:

```text
Accounts PC
192.168.40.10
      |
      | 1
      v
Router
192.168.40.1
      |
      | 2
      v
Router
192.168.40.129
      |
      | 3
      v
Delivery PC
192.168.40.140
```

A successful response demonstrates communication between the two subnets.

---

# 11. Troubleshooting Checklist

If a device cannot communicate with another device, the following checks should be performed.

### Check 1 — IP Address

Verify that the device has the correct IP address.

```text
IP Address
```

### Check 2 — Subnet Mask

Verify:

```text
255.255.255.128
```

is configured on the device.

### Check 3 — Default Gateway

Accounts devices should use:

```text
192.168.40.1
```

Delivery devices should use:

```text
192.168.40.129
```

### Check 4 — Router Interface Status

Run:

```cisco
show ip interface brief
```

The required interfaces should show:

```text
Status   : up
Protocol : up
```

### Check 5 — Cable Connections

Verify that the correct interfaces are connected in Cisco Packet Tracer.

### Check 6 — Routing Table

Run:

```cisco
show ip route
```

Verify that both `/25` networks are present.

### Check 7 — Ping Test

Test connectivity step by step:

```text
PC → Default Gateway
        ↓
Gateway → Router
        ↓
Router → Destination Network
        ↓
Destination PC
```

---

# 12. Configuration Verification Commands

The following commands are useful when verifying the configuration.

### Router

```cisco
show running-config
```

Displays the current router configuration.

```cisco
show ip interface brief
```

Displays interface IP addresses and status.

```cisco
show ip route
```

Displays the router's routing table.

```cisco
show interfaces
```

Displays detailed interface information.

---

### Switch

```cisco
show running-config
```

Displays the current switch configuration.

```cisco
show interfaces status
```

Displays the status of switch ports.

```cisco
show mac address-table
```

Displays MAC addresses learned by the switch.

---

# 13. Configuration Summary

The final configuration can be summarized as follows:

```text
                    ROUTER
              ┌────────────────┐
              │                │
              │ G0/0   G0/1    │
              │ .1     .129    │
              └───┬──────┬─────┘
                  │      │
                  │      │
             Accounts   Delivery
             /25        /25
                  │      │
               Switch  Switch
                  │      │
                 PCs    PCs
```

### Accounts

```text
Network  : 192.168.40.0/25
Gateway  : 192.168.40.1
```

### Delivery

```text
Network  : 192.168.40.128/25
Gateway  : 192.168.40.129
```

---

# 14. Configuration Notes

* Static IPv4 addressing is used for the current project.
* The two departments are separated using IP subnetting.
* The router provides inter-subnet communication.
* Each subnet has a dedicated default gateway.
* The switches provide local Layer 2 connectivity.
* No dynamic routing protocol is required because both networks are directly connected to the same router.
* No DHCP server is configured in the basic implementation.
* No VLAN configuration is required for the basic implementation.
* Any changes made to the Packet Tracer topology should be reflected in the project documentation.

---

## Configuration Status

| Component            | Status         |
| -------------------- | -------------- |
| IPv4 Addressing      | Configured     |
| CIDR Subnetting      | Configured     |
| Router Interfaces    | Configured     |
| Default Gateways     | Configured     |
| Layer 2 Switching    | Configured     |
| Inter-Subnet Routing | Configured     |
| Static IP Addressing | Configured     |
| DHCP                 | Not configured |
| VLANs                | Not configured |
| OSPF                 | Not configured |
| Connectivity Testing | Performed      |
