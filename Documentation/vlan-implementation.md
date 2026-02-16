# VLAN Configuration

## Main Office VLANs

| VLAN | Name | Network | Gateway | Ports | Devices |
|------|------|---------|---------|-------|---------|
| 10 | Management | 192.168.10.0/24 | 192.168.10.1 | Fa0/1-4 (Switch0) | Admin PCs, NOC |
| 20 | IT | 192.168.20.0/24 | 192.168.20.1 | Fa0/5-12 (Switch1) | IT and SOC team |
| 30 | Research | 192.168.30.0/24 | 192.168.30.1 | Fa0/13-20 (Switch2) | R&D team |
| 40 | HR | 192.168.40.0/24 | 192.168.40.1 | Fa0/1-6 (Switch3) | HR department |
| 70 | Guest | 192.168.70.0/24 | 192.168.70.1 | Fa0/21-24 (Switch0) | Visitors, contractors |
| 99 | Servers | 192.168.99.0/24 | 192.168.99.1 | Fa0/1 (Switch0) | DHCP server |

## Branch Office VLANs

| VLAN | Name | Network | Gateway | Ports | Devices |
|------|------|---------|---------|-------|---------|
| 50 | Branch-Sales | 192.168.50.0/24 | 192.168.50.1 | Fa0/1-12 (Switch5) | Branch sales team |
| 60 | Branch-Support | 192.168.60.0/24 | 192.168.60.1 | Fa0/1-12 (Switch6) | Support staff |

## Trunk Ports

| Device | Interface | Connected To | Allowed VLANs | Native VLAN |
|--------|-----------|--------------|---------------|-------------|
| Switch0 | Gi0/1 | MLS1 Gi1/0/1 | 10,20,30,40,50,60,70,99 | 999 |
| Switch0 | Gi0/2 | MLS2 Gi1/0/2 | 10,20,30,40,50,60,70,99 | 999 |
| Switch1 | Gi0/1 | MLS1 Gi1/0/3 | 10,20,30,40,50,60,70,99 | 999 |
| Switch1 | Gi0/2 | MLS2 Gi1/0/4 | 10,20,30,40,50,60,70,99 | 999 |
| Switch2 | Gi0/1 | MLS1 Gi1/0/5 | 10,20,30,40,50,60,70,99 | 999 |
| Switch2 | Gi0/2 | MLS2 Gi1/0/6 | 10,20,30,40,50,60,70,99 | 999 |
| Switch3 | Gi0/1 | MLS1 Gi1/0/7 | 10,20,30,40,50,60,70,99 | 999 |
| Switch3 | Gi0/2 | MLS2 Gi1/0/8 | 10,20,30,40,50,60,70,99 | 999 |
| MLS1 | Gi1/0/23 | MLS2 Gi1/0/23 | 10,20,30,40,50,60,70,99 | 999 |


## Verification Commands
```
Check VLAN database
show vlan briefCheck trunk status
show interfaces trunkCheck SVI status
show ip interface briefCheck HSRP status
show standby briefCheck specific port VLAN assignment
show interfaces fa0/5 switchport
```