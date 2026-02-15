# Multi-Site Enterprise Network Design

A comprehensive enterprise network infrastructure project demonstrating routing, switching, security, and redundancy concepts using Cisco technologies.

## Project Overview

Designed and implemented a dual-site enterprise network serving 150+ endpoints across 8 VLANs with full redundancy, dynamic routing, and layered security controls.

**Key Highlights:**
- Multi-site connectivity via WAN routing
- Redundant core with HSRP failover (<3 second convergence)
- OSPF dynamic routing with authenticated adjacencies
- Comprehensive security policies (ACLs, port security, DHCP snooping)
- Centralized network services (DHCP, NAT/PAT)

## Network Architecture

### Main Office
- **Core Layer**: Dual multilayer switches (3560-24PS) with HSRP
- **Access Layer**: 4 access switches (2960-24TT) serving 6 VLANs
- **Services**: Centralized DHCP, NAT/PAT gateway
- **Redundancy**: Dual-homed connections, HSRP load balancing

### Branch Office
- **Core**: Single multilayer switch (3560-24PS)
- **Access**: 2 access switches (2960-24TT) for 2 VLANs
- **Connectivity**: WAN link to main office via ISR4331 routers

## Technologies Implemented

### Routing & Switching
- **VLANs**: 802.1Q trunking, VLAN segmentation (8 VLANs)
- **Inter-VLAN Routing**: Layer 3 switching with SVIs
- **OSPF**: Area 0 backbone, passive interfaces, default route propagation
- **HSRP**: Load-balanced gateway redundancy across VLANs
- **Spanning Tree**: Rapid-PVST+ optimization

### Security
- **Access Control Lists**: Role-based security policies on 4 VLANs
  - Guest network isolation
  - Server protection (DHCP-aware)
  - Management access control
- **Port Security**: MAC address limiting with sticky learning (96 ports)
- **DHCP Snooping**: Rogue DHCP server prevention
- **Anti-Spoofing**: WAN edge filtering for RFC 1918 addresses

### Network Services
- **DHCP**: Centralized server with IP helper-address configuration
- **NAT/PAT**: Single public IP shared across 150+ hosts
- **NTP**: Hierarchical time synchronization
- **Syslog**: Centralized logging for monitoring

## IP Addressing Scheme

| VLAN | Name | Network | Gateway (HSRP) | Active Switch |
|------|------|---------|----------------|---------------|
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 | MLS1 |
| 20 | Sales | 192.168.20.0/24 | 192.168.20.1 | MLS2 |
| 30 | Research | 192.168.30.0/24 | 192.168.30.1 | MLS1 |
| 40 | HR | 192.168.40.0/24 | 192.168.40.1 | MLS2 |
| 50 | Finance | 192.168.50.0/24 | 192.168.50.1 | MLS1 |
| 60 | IT | 192.168.60.0/24 | 192.168.60.1 | MLS2 |
| 70 | Guest | 192.168.70.0/24 | 192.168.70.1 | MLS1 |
| 99 | Servers | 192.168.99.0/24 | 192.168.99.1 | MLS1 |


**Point-to-Point Links:**
- MLS1 ↔ MLS2: 10.0.5.0/30
- Router ↔ MLS1: 10.0.0.0/30
- Router ↔ ISP: 10.0.4.0/30
- Main ↔ Branch WAN: 10.0.1.0/30, 10.0.2.0/30

[See complete addressing scheme](documentation/ip-addressing-scheme.md)

## Security Policies

### Guest Network (VLAN 70)
- Internet access allowed
- DHCP access to server
- Access to internal VLANs blocked
- Access to server resources blocked (except DHCP)

### Server VLAN (99)
- DHCP requests from all VLANs
- Management VLAN full access
- Direct access from user VLANs blocked

### Management VLAN (10)
- IT staff (VLAN 30) can SSH/manage
- Management can access all resources
- Other VLANs blocked from management access

[See detailed ACL configurations](documentation/security-implementation.md)

## Key Achievements

- **Zero Single Points of Failure** in main office core
- **Sub-3-Second Failover** for gateway redundancy
- **100% Network Segmentation** between departments
- **Comprehensive Security** with 4-layer defense (VLAN, ACL, port security, DHCP snooping)
- **Scalable Design** supporting future growth

## Tools Used

- **Cisco Packet Tracer** 8.2.1
- **Cisco IOS** (routers and switches)
- **Documentation**: Markdown, Diagrams.net

## Skills Demonstrated

- Network design and architecture
- Cisco IOS configuration and troubleshooting
- Routing protocols (OSPF)
- Switching technologies (VLANs, trunking, STP)
- High availability (HSRP, redundancy)
- Network security (ACLs, port security)
- IP addressing and subnetting
- NAT/PAT configuration
- Technical documentation

## How to Use This Project

1. **Download Packet Tracer file**: [enterprise-network.pkt](packet-tracer/enterprise-network.pkt)
2. **Open in Cisco Packet Tracer** (version 8.0+)
3. **Review configurations** in the `configs/` folder
4. **Follow test procedures** in [testing-results.md](documentation/testing-results.md)

## Contact

**Your Name**  
Email: duyanhta2709@gmail.com  

---

⭐ **If you found this project helpful, please consider giving it a star!**
