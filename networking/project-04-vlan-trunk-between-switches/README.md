# Project 04 - VLAN Trunk Between Two Switches

Basic VLAN trunking lab in Cisco Packet Tracer.

The purpose of this project is to show how two switches can carry the same VLANs over one trunk link. Hosts in the same VLAN should communicate across switches. Hosts in different VLANs should stay separated until inter-VLAN routing is added.

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

No router is used in this lab. The gateway addresses are included for the IP plan, but gateway tests are expected to fail until routing is added in a later project.

## Switch Configuration Summary

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

Expected results:

- VLAN 10 hosts on different switches can communicate.
- VLAN 20 hosts on different switches can communicate.
- VLAN 10 and VLAN 20 cannot communicate without a router or Layer 3 switch.

## Screenshots

Screenshot evidence should be uploaded to this folder:

```text
networking/project-04-vlan-trunk-between-switches/screenshots/
```

Recommended screenshot names:

```text
topology.png
sw0-show-interfaces-trunk.png
sw1-show-interfaces-trunk.png
vlan10-ping-success.png
vlan20-ping-success.png
cross-vlan-ping-failed.png
```

## Documentation

Full documentation can be stored here:

```text
docs/project-04-vlan-trunk-between-two-switches.pdf
```

## Result

The lab worked as expected:

- VLAN 10 worked across the trunk.
- VLAN 20 worked across the trunk.
- VLAN 10 and VLAN 20 stayed isolated because no router or Layer 3 switch was configured.

## Key Concept

VLAN separation is logical, not physical. Devices on different switches can communicate if they are in the same VLAN and the trunk carries that VLAN. Devices on the same switch can still be separated if they are placed in different VLANs.
