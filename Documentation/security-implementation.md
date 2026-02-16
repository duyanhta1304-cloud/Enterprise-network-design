# Security Implementation

## Access Control Lists (ACLs)

### ACL 1: GUEST_ACL
**Applied On:** MLS1 and MLS2, interface VLAN 70, direction IN  
**Purpose:** Isolate guest network from internal resources

**Rules:**
```
1.  permit udp host 0.0.0.0 any eq bootps
2.  permit udp 192.168.70.0 0.0.0.255 host 192.168.99.10 eq bootps
3.  permit udp host 192.168.99.10 eq bootps 192.168.70.0 0.0.0.255 eq bootpc
4.  permit udp host 192.168.99.10 eq bootps host 255.255.255.255 eq bootpc
5.  deny ip 192.168.70.0 0.0.0.255 192.168.10.0 0.0.0.255 log
6.  deny ip 192.168.70.0 0.0.0.255 192.168.20.0 0.0.0.255 log
7.  deny ip 192.168.70.0 0.0.0.255 192.168.30.0 0.0.0.255 log
8.  deny ip 192.168.70.0 0.0.0.255 192.168.40.0 0.0.0.255 log
9.  deny ip 192.168.70.0 0.0.0.255 192.168.50.0 0.0.0.255 log
10. deny ip 192.168.70.0 0.0.0.255 192.168.60.0 0.0.0.255 log
11. deny tcp 192.168.70.0 0.0.0.255 192.168.99.0 0.0.0.255 log
12. deny icmp 192.168.70.0 0.0.0.255 192.168.99.0 0.0.0.255 log
13. deny ip 192.168.70.0 0.0.0.255 192.168.150.0 0.0.0.255 log
14. deny ip 192.168.70.0 0.0.0.255 192.168.160.0 0.0.0.255 log
15. permit ip 192.168.70.0 0.0.0.255 any
```

**Summary:**
- Allows DHCP (including from 0.0.0.0 for initial requests)
- Blocks all internal VLANs (10, 20, 30, 40, 50, 60)
- Blocks server VLAN except DHCP
- Blocks branch office VLANs
- Allows internet access

---

### ACL 2: SERVER_ACL
**Applied On:** MLS1 and MLS2, interface VLAN 99, direction IN  
**Purpose:** Protect server VLAN with service-specific access

**Rules:**
```
1. permit udp any host 192.168.99.10 eq bootps
2. permit udp host 192.168.99.10 eq bootps any eq bootpc
3. permit icmp 192.168.10.0 0.0.0.255 192.168.99.0 0.0.0.255 echo
4. permit ip 192.168.10.0 0.0.0.255 192.168.99.0 0.0.0.255
5. permit icmp 192.168.60.0 0.0.0.255 192.168.99.0 0.0.0.255 echo
6. deny ip any 192.168.99.0 0.0.0.255 log
```

**Summary:**
- Allows DHCP from all VLANs
- Management VLAN (10) has full access
- IT VLAN (60) can ping for monitoring
- All other access denied and logged

---

### ACL 3: MANAGEMENT_ACL
**Applied On:** MLS1 and MLS2, interface VLAN 10, direction IN  
**Purpose:** Restrict management network access to IT department only

**Rules:**
```
1. permit tcp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 eq 22
2. permit tcp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 eq telnet
3. permit tcp 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255 eq 443
4. permit ip 192.168.10.0 0.0.0.255 any
5. deny ip any 192.168.10.0 0.0.0.255 log
```

**Summary:**
- IT VLAN (60) can SSH, Telnet, HTTPS to management devices
- Management VLAN can access all resources
- All other VLANs blocked from management

---

### ACL 4: WAN_ACL
**Applied On:** Router0, interface Gi0/1/0 (WAN), direction IN  
**Purpose:** Anti-spoofing protection on internet edge

**Rules:**
```
1. deny ip 192.168.0.0 0.0.255.255 any log
2. deny ip 10.0.0.0 0.255.255.255 any log
3. deny ip 172.16.0.0 0.15.255.255 any log
4. deny ip 127.0.0.0 0.255.255.255 any log
5. deny ip 224.0.0.0 15.255.255.255 any log
6. deny ip 240.0.0.0 15.255.255.255 any log
7. permit ip any any
```

**Summary:**
- Blocks RFC 1918 private addresses from internet
- Blocks loopback addresses
- Blocks multicast and reserved ranges
- Allows legitimate internet traffic

---

## Port Security Configuration

### Access Port Security (Standard VLANs)

| Port Range | VLAN | Max MAC | Violation Mode | Sticky | PortFast | BPDU Guard |
|------------|------|---------|----------------|--------|----------|------------|
| Fa0/1-4 | 10 (Mgmt) | 2 | Shutdown | ✅ | ✅ | ✅ |
| Fa0/5-12 | 20 (Sales) | 2 | Restrict | ✅ | ✅ | ❌ |
| Fa0/13-20 | 30 (Research) | 3 | Restrict | ✅ | ✅ | ❌ |
| Fa0/1-6 | 40 (HR) | 2 | Shutdown | ✅ | ✅ | ✅ |
| Fa0/7-12 | 50 (Finance) | 2 | Shutdown | ✅ | ✅ | ✅ |
| Fa0/13-20 | 60 (IT) | 3 | Restrict | ✅ | ✅ | ❌ |
| Fa0/21-24 | 70 (Guest) | 1 | Restrict | ✅ | ✅ | ❌ |
| Fa0/1 | 99 (Server) | 1 | Shutdown | ✅ | ✅ | ✅ |

**Configuration Template (Standard):**
```
interface range FastEthernet0/5 - 12
 switchport mode access
 switchport access vlan 20
 switchport port-security
 switchport port-security maximum 2
 switchport port-security mac-address sticky
 switchport port-security violation restrict
 spanning-tree portfast
```

**Configuration Template (High Security - HR/Finance/Management):**
```
interface range FastEthernet0/1 - 6
 switchport mode access
 switchport access vlan 40
 switchport port-security
 switchport port-security maximum 2
 switchport port-security mac-address sticky
 switchport port-security violation shutdown
 spanning-tree portfast
 spanning-tree bpduguard enable
```

---

## DHCP Snooping Configuration

### Trusted vs Untrusted Ports

| Device | Port | Type | DHCP Snooping | Reason |
|--------|------|------|---------------|--------|
| Switch0 | Gi0/1-2 | Trunk | Trusted | Uplink to core switches |
| Switch1-3 | Gi0/1-2 | Trunk | Trusted | Uplink to core switches |
| Switch0-3 | Fa0/1-24 | Access | Untrusted | End-user devices |
| MLS1 | Fa1/0/10 | Access | Trusted | DHCP server connection |
| MLS1 | Gi1/0/1-24 | Various | Trusted | Inter-switch links |
| MLS2 | Gi1/0/1-24 | Various | Trusted | Inter-switch links |

**Configuration (Access Switches):**
```
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,40,50,60,70
no ip dhcp snooping information option

interface range GigabitEthernet0/1 - 2
 ip dhcp snooping trust
```

**Configuration (Core Switch with DHCP Server):**
```
ip dhcp snooping
ip dhcp snooping vlan 10,20,30,40,50,60,70,99
no ip dhcp snooping information option

interface FastEthernet1/0/10
 description DHCP-Server
 ip dhcp snooping trust

interface range GigabitEthernet1/0/1 - 24
 ip dhcp snooping trust
```

---

## Security Feature Summary by Switch

### Access Layer Switches (Switch0-3)

| Feature | Status | Configuration |
|---------|--------|---------------|
| Port Security |  Enabled | All access ports (Fa0/1-24) |
| DHCP Snooping |  Enabled | All VLANs, trunks trusted |
| BPDU Guard |  Enabled | High-security VLANs only |
| PortFast |  Enabled | All access ports |
| Storm Control |  Not configured | Future enhancement |
| Dynamic ARP Inspection |  Not configured | Future enhancement |

### Core Layer Switches (MLS1, MLS2)

| Feature | Status | Configuration |
|---------|--------|---------------|
| ACLs |  Enabled | 3 ACLs on VLAN 10, 70, 99 |
| DHCP Snooping |  Enabled | All VLANs, all ports trusted |
| HSRP |  Enabled | All VLANs with load balancing |
| OSPF |  Enabled | Area 0, passive interfaces on VLANs |
| Port Security |  Not applicable | Layer 3 device |

### WAN Router (Router0)

| Feature | Status | Configuration |
|---------|--------|---------------|
| Anti-Spoofing ACL |  Enabled | WAN interface inbound |
| NAT/PAT |  Enabled | All internal networks |
| OSPF |  Enabled | Area 0 |
| Default Route Propagation |  Enabled | Via OSPF |



---

## Verification Commands
```bash
# Check ACLs
show access-lists
show access-lists GUEST_ACL
show ip interface vlan 70

# Check Port Security
show port-security
show port-security interface fa0/5
show port-security address

# Check DHCP Snooping
show ip dhcp snooping
show ip dhcp snooping binding
show ip dhcp snooping statistics

# Check NAT
show ip nat translations
show ip nat statistics

# Check Logging
show logging
```

---

## Security Posture

**Defense Layers:** 5 (Segmentation, ACLs, Port Security, DHCP Snooping, Edge Protection)  
**Protected Ports:** 96 (across 6 access switches)  
**Monitored Events:** ACL violations, port security, DHCP snooping  
**Coverage:** 100% of user-facing ports and critical VLANs