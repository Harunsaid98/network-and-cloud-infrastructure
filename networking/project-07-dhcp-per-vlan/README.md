# Project 07 - DHCP Per VLAN

This project demonstrates how a router can automatically assign IP addresses to devices in different VLANs using DHCP.

The lab builds on VLANs, trunking, and router-on-a-stick. Instead of manually configuring every PC with an IP address, subnet mask, default gateway, and DNS server, the router provides those settings from separate DHCP pools.

## Topology

```text
PC0 ---- SW0 ---- R0
PC1 ---- SW0 ---- R0
PC2 ---- SW0 ---- R0
PC3 ---- SW0 ---- R0

SW0 Gi0/1 ---- trunk ---- R0 Gi0/0
```

## VLAN and Addressing Plan

| VLAN | Name | Switch Ports | Network | Default Gateway |
|---:|---|---|---|---|
| 10 | IT | Fa0/1 - Fa0/2 | 192.168.10.0/24 | 192.168.10.1 |
| 20 | Sales | Fa0/3 - Fa0/4 | 192.168.20.0/24 | 192.168.20.1 |

Reserved addresses:

| VLAN | Reserved Range | DHCP Starts From |
|---:|---|---|
| 10 | 192.168.10.1 - 192.168.10.10 | 192.168.10.11 |
| 20 | 192.168.20.1 - 192.168.20.10 | 192.168.20.11 |

## Switch Configuration Summary

```text
vlan 10
 name IT
vlan 20
 name SALES

interface range fa0/1 - 2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
 no shutdown

interface range fa0/3 - 4
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
 no shutdown

interface gi0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
```

## Router Configuration Summary

```text
interface g0/0
 no shutdown

interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
```

## DHCP Configuration

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.10
ip dhcp excluded-address 192.168.20.1 192.168.20.10

ip dhcp pool VLAN10_IT
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

ip dhcp pool VLAN20_SALES
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
```

## Verification

The configuration should be checked with:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show ip dhcp binding
show ip dhcp pool
ipconfig
ping
```

Expected results:

- VLAN 10 PCs receive addresses from the 192.168.10.0/24 network.
- VLAN 20 PCs receive addresses from the 192.168.20.0/24 network.
- PCs receive the correct default gateway for their VLAN.
- PCs can ping their own gateway.
- Inter-VLAN communication works because router-on-a-stick is configured.

## Screenshots

Screenshot evidence should be uploaded to this folder:

```text
networking/project-07-dhcp-per-vlan/screenshots/
```

Recommended screenshot names:

```text
topology.png
show-vlan-brief.png
show-interfaces-trunk.png
show-ip-interface-brief.png
show-ip-dhcp-binding.png
show-ip-dhcp-pool.png
pc0-dhcp-ipconfig.png
pc2-dhcp-ipconfig.png
inter-vlan-ping-success.png
```

## Documentation

Full documentation can be stored here:

```text
docs/project-07-dhcp-per-vlan.pdf
```

## Result

The lab shows how DHCP works with multiple VLANs. Each VLAN needs its own DHCP pool because each VLAN uses a different subnet and gateway.

## Key Concept

DHCP does not remove the need for correct VLANs, trunks, or routing. DHCP only gives IP settings automatically. The underlying network still needs the correct access ports, trunk link, router subinterfaces, and gateway addresses.