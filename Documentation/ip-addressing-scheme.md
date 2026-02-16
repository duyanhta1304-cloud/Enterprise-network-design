# Complete IP Addressing Scheme

## Overview

This document provides the complete IP addressing plan for the multi-site enterprise network, including VLAN assignments, point-to-point links, HSRP configurations, and addressing best practices.

## Design Philosophy

**Addressing Strategy:**
- Main office: 192.168.x.0/24 range (x = 10-99)
- Branch office: 192.168.x.0/24 range (x = 50-60)
- Infrastructure links: 10.0.x.0/30 (point-to-point /30 subnets)
- HSRP virtual IPs: .1 address in each subnet
- Physical multilayer switch IPs: .2 and .3 addresses in each subnet
- DHCP pools: .10 through .254

**Benefits of This Design:**
- Easy to identify location by IP range
- Consistent gateway addressing (.1)
- Efficient use of address space (/30 for P2P links)
- Room for growth within each VLAN
- Clear separation between infrastructure and user networks

---

## Main Office - VLAN Addressing

### VLAN 10 - Management
**Purpose**: Network management devices, administrative PCs  
**Security Level**: High - restricted access

| Parameter | Value |
|-----------|-------|
| Network | 192.168.10.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.10.1 - 192.168.10.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.10.1 |
| MLS1 (Active) SVI | 192.168.10.2 (Priority 110) |
| MLS2 (Standby) SVI | 192.168.10.3 (Priority 100) |
| DHCP Range | 192.168.10.4 - 192.168.10.254 |



---

### VLAN 20 - IT
**Purpose**: IT department and technical staff  
**Security Level**: High - access to infrastructure

| Parameter | Value |
|-----------|-------|
| Network | 192.168.20.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.20.1 - 192.168.20.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.20.1 |
| MLS1 (Standby) SVI | 192.168.20.2 (Priority 110) |
| MLS2 (Active) SVI | 192.168.20.3 (Priority 100) |
| DHCP Range | 192.168.20.4 - 192.168.20.254 |

---

### VLAN 30 - Research
**Purpose**: Research and development team  
**Security Level**: Medium-High

| Parameter | Value |
|-----------|-------|
| Network | 192.168.30.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.30.1 - 192.168.30.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.30.1 |
| MLS1 (Active) SVI | 192.168.30.2 (Priority 100) |
| MLS2 (Standby) SVI | 192.168.30.3 (Priority 110) |
| DHCP Range | 192.168.30.4 - 192.168.30.254 |


---

### VLAN 40 - HR
**Purpose**: Human resources department  
**Security Level**: High - contains sensitive personnel data

| Parameter | Value |
|-----------|-------|
| Network | 192.168.40.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.40.1 - 192.168.40.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.40.1 |
| MLS1 (Standby) SVI | 192.168.40.2 (Priority 100) |
| MLS2 (Active) SVI | 192.168.40.3 (Priority 110) |
| DHCP Range | 192.168.40.4 - 192.168.40.254 |

---

### VLAN 50 - Sales
**Purpose**: Sales department workstations and resources  
**Security Level**: Medium

| Parameter | Value |
|-----------|-------|
| Network | 192.168.50.0/24 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.50.1 |
| Usable IPs | 192.168.50.1 - 192.168.50.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.50.1 |
| DHCP Range | 192.168.50.4 - 192.168.50.254 |

---

### VLAN 60 - Support
**Purpose**: Customer support  
**Security Level**: Medium

| Parameter | Value |
|-----------|-------|
| Network | 192.168.60.0/24 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.60.1 |
| Usable IPs | 192.168.60.1 - 192.168.60.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.60.1 |
| DHCP Range | 192.168.60.4 - 192.168.60.254 |

---

### VLAN 70 - Guest
**Purpose**: Visitor and contractor temporary access  
**Security Level**: Low - isolated from internal networks

| Parameter | Value |
|-----------|-------|
| Network | 192.168.70.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.70.1 - 192.168.70.254 |
| Total Hosts | 254 |
| HSRP Virtual IP (Gateway) | 192.168.70.1 |
| MLS1 (Active) SVI | 192.168.70.2 (Priority 110) |
| MLS2 (Standby) SVI | 192.168.70.3 (Priority 100) |
| DHCP Range | 192.168.70.4 - 192.168.70.254 |

**Special Considerations:**
- Isolated from all internal VLANs via ACLs
- Internet-only access
- Shorter DHCP lease time
- Wireless AP and guest devices

---

### VLAN 99 - Servers
**Purpose**: Infrastructure servers (DHCP, DNS, file servers)  
**Security Level**: Very High - critical infrastructure

| Parameter | Value |
|-----------|-------|
| Network | 192.168.99.0/24 |
| Subnet Mask | 255.255.255.0 |
| Usable IPs | 192.168.99.1 - 192.168.99.254 |
| Total Hosts | 254 |
| Default Gateway |192.168.99.1 |
| DHCP Server | 192.168.99.10 (Static) |
| DNS Server (future) | 192.168.99.11 (Reserved) |
| File Server (future) | 192.168.99.12 (Reserved) |
| Management Server | 192.168.99.13 (Reserved) |

**Note**: This VLAN uses static IP assignments only - no DHCP pool configured here.

**Static Assignments:**
- .10: DHCP Server (active)
- .11-.20: Reserved for additional servers
- .21-.254: Available for future servers

---



## Infrastructure Point-to-Point Links



---

### Main Office - Router 1 to MLS1 (Primary)

| Parameter | Value |
|-----------|-------|
| Network | 10.0.0.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.0.1 - 10.0.0.2 |
| Router0 Interface | GigabitEthernet0/0/0: 10.0.0.1/30 |
| MLS1 Interface | GigabitEthernet1/0/1: 10.0.0.2/30 |
| Purpose | Routing path to WAN |
| OSPF Cost | 1 |

---

### Main Office - Router 1 to MLS2 (Secondary)

| Parameter | Value |
|-----------|-------|
| Network | 10.0.1.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.2.1 - 10.0.2.2 |
| Router0 Interface | GigabitEthernet0/0/1: 10.0.1.1/30 |
| MLS2 Interface | GigabitEthernet1/0/1: 10.0.1.2/30 |
| Purpose | Redundant routing path |
| OSPF Cost | 1 |

---

### WAN Link - MLS1 to ISP Router (Primary)

| Parameter | Value |
|-----------|-------|
| Network | 10.0.4.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.4.1 - 10.0.4.2 |
| ISP Router Interface | GigabitEthernet0/2: 10.0.4.1/30 |
| MLS1 Interface | GigabitEthernet0/1/0: 10.0.4.2/30 |
| Purpose | Internet connectivity |

---
### WAN Link - MLS2 to ISP Router (Secondary)

| Parameter | Value |
|-----------|-------|
| Network | 10.0.5.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.5.1 - 10.0.5.2 |
| ISP Router Interface | GigabitEthernet0/0/1: 10.0.5.1/30 |
| MLS2 Interface | GigabitEthernet0/2: 10.0.5.2/30 |
| Purpose | Internet connectivity redundancy |

**Special Configuration:**
- NAT/PAT configured on Router0
- Default route: `ip route 0.0.0.0 0.0.0.0 10.0.4.1`
- Anti-spoofing ACL applied inbound
 ---

### WAN Link - Main to Branch

| Parameter | Value |
|-----------|-------|
| Network | 10.0.2.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.2.1 - 10.0.2.2 |
| Router1 Interface | GigabitEthernet0/0/1: 10.0.2.1/30 |
| Router2 Interface | GigabitEthernet1/0/1: 10.0.2.2/30 |
| Purpose | Connecting Main to Branch |
| OSPF Cost | 1 |


---

### Branch Office - Router to MLS3

| Parameter | Value |
|-----------|-------|
| Network | 10.0.3.0/30 |
| Subnet Mask | 255.255.255.252 |
| Usable IPs | 10.0.3.1 - 10.0.3.2 |
| Router2 Interface | GigabitEthernet0/0/1: 10.0.3.1/30 |
| MLS3 Interface | GigabitEthernet1/0/1: 10.0.3.2/30 |
| Purpose | Branch office routing |
| OSPF Cost | 1 |

---

## HSRP Configuration Summary

### Load Balancing Strategy

**MLS1 is Active for:**
- VLAN 10 (Management) - Priority 110
- VLAN 20 (IT) - Priority 110
- VLAN 70 (Guest) - Priority 110

**MLS2 is Active for:**
- VLAN 30 (Research) - Priority 110
- VLAN 40 (HR) - Priority 110

**Benefits:**
- Distributes load across both core switches
- Utilizes both switches' forwarding capacity
- Maintains redundancy for all VLANs

### HSRP Timers (All VLANs)
- Hello: 3 seconds (default)
- Hold: 10 seconds (default)
- Preempt: Enabled on both switches
- Authentication: None (not required for internal network)

---

## OSPF Configuration Summary

### Networks Advertised

**Router1:**
```
network 10.0.0.0 0.0.0.3 area 0
network 10.0.1.0 0.0.0.3 area 0
network 10.0.2.0 0.0.0.3 area 0
```

**MLS1:**
```
network 192.168.10.0 0.0.0.255 area 0
network 192.168.20.0 0.0.0.255 area 0
network 192.168.30.0 0.0.0.255 area 0
network 192.168.40.0 0.0.0.255 area 0
network 192.168.70.0 0.0.0.255 area 0
network 192.168.99.0 0.0.0.255 area 0
network 10.0.0.0 0.0.0.3 area 0
network 10.0.4.0 0.0.0.3 area 0
```

**MLS2:**
```
network 192.168.10.0 0.0.0.255 area 0
network 192.168.20.0 0.0.0.255 area 0
network 192.168.30.0 0.0.0.255 area 0
network 192.168.40.0 0.0.0.255 area 0
network 192.168.70.0 0.0.0.255 area 0
network 192.168.99.0 0.0.0.255 area 0
network 10.0.2.0 0.0.0.3 area 0
network 10.0.5.0 0.0.0.3 area 0
```

**Router2 (Branch):**
```
network 10.0.2.0 0.0.0.3 area 0
network 10.0.3.0 0.0.0.3 area 0
```

**MLS3 (Branch):**
```
network 192.168.50.0 0.0.0.255 area 0
network 192.168.60.0 0.0.0.255 area 0
network 10.0.3.0 0.0.0.3 area 0
```

---

## NAT Configuration

### Inside Interfaces (Router0)
- GigabitEthernet0/0/0 (to MLS1): `ip nat inside`
- GigabitEthernet0/0/1 (to MLS2): `ip nat inside`

### Outside Interface (Router0)
- GigabitEthernet0/1/0 (to ISP): `ip nat outside`

### NAT ACL
```
access-list 10 permit 192.168.10.0 0.0.255.255
access-list 10 permit 192.168.20.0 0.0.255.255
access-list 10 permit 192.168.30.0 0.0.255.255
access-list 10 permit 192.168.40.0 0.0.255.255
access-list 10 permit 192.168.50.0 0.0.255.255
access-list 10 permit 192.168.60.0 0.0.255.255
```
This permits all internal networks except for Server Vlan for NAT.

### PAT Configuration
```
ip nat inside source list 10 interface GigabitEthernet0/1/0 overload
```

**Result**: All internal hosts share the single public IP (10.0.4.2) using different port numbers.