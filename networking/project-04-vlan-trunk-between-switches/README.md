# Project 04 - VLAN Trunk Between Two Switches

Basic VLAN trunking lab in Cisco Packet Tracer.

This project shows how two switches can carry the same VLANs over one trunk link. Hosts in the same VLAN can communicate across switches. Hosts in different VLANs stay separated because no router or Layer 3 switch is used in this lab.

## Topology

```text
PC0 ---- SW0 ---- trunk ---- SW1 ---- PC2
VLAN 10         Gi0/1       Gi0/1    VLAN 10

PC1 ---- SW0 ---- trunk ---- SW1 ---- PC3
VLAN 20         Gi0/1       Gi0/1    VLAN 20
```

## VLAN and IP Plan

| Device | VLAN | IP Address | Subnet Mask | Default Gateway |
|---|---:|---|---|---|
| PC0 | 10 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 |
| PC1 | 20 | 192.168.20.10 | 255.255.255.0 | 192.168.20.1 |
| PC2 | 10 | 192.168.10.20 | 255.255.255.0 | 192.168.10.1 |
| PC3 | 20 | 192.168.20.20 | 255.255.255.0 | 192.168.20.1 |

No router is used in this lab. The gateway addresses are included in the IP plan, but gateway tests are expected to fail until routing is added in a later project.

## Configuration Summary

- VLAN 10: IT
- VLAN 20: Sales
- PC ports: access ports
- Switch-to-switch link: trunk port
- Trunk interface: `GigabitEthernet0/1`
- Allowed VLANs on trunk: `10,20`

## Verification

The configuration was checked with:

```text
show vlan brief
show interfaces trunk
ping
```

Expected result:

- VLAN 10 hosts on different switches can communicate.
- VLAN 20 hosts on different switches can communicate.
- VLAN 10 and VLAN 20 cannot communicate without routing.

## Documentation

```text
docs/Lab_04_VLAN_Trunk_Between_Two_Switches_CLEAN_No_BreakFix.pdf
screenshots/
```

## Result

The lab worked as expected. Same-VLAN communication across switches was successful, and different VLANs stayed isolated because no router or Layer 3 switch was configured.

## Key Concept

VLAN separation is logical, not physical. Devices on different switches can communicate if they are in the same VLAN and the trunk carries that VLAN. Devices on the same switch can still be separated if they are placed in different VLANs.
