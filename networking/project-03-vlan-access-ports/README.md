# Project 03 - VLAN Access Ports

Cisco Packet Tracer portfolio documentation.

## Scenario

Three PCs are connected to the same switch.

PC0 and PC1 should be able to communicate with each other. PC2 should be isolated, even though all three PCs are connected to the same physical switch.

This lab demonstrates how VLAN access ports separate traffic logically on a Layer 2 switch.

## Goal

- Create two VLANs on one switch.
- Place PC0 and PC1 in VLAN 10.
- Place PC2 in VLAN 20.
- Use the same IP subnet on all PCs on purpose.
- Prove that VLAN membership controls Layer 2 communication.

## Topology

```text
PC0 ----\
         Switch0
PC1 ----/
PC2 ----/
```

## VLAN and Addressing Plan

| Device | Switch Port | VLAN | IP Address | Subnet Mask | Gateway |
|---|---|---:|---|---|---|
| PC0 | Fa0/1 | 10 - STAFF | 192.168.1.10 | 255.255.255.0 | — |
| PC1 | Fa0/3 | 10 - STAFF | 192.168.1.20 | 255.255.255.0 | — |
| PC2 | Fa0/2 | 20 - MANAGEMENT | 192.168.1.30 | 255.255.255.0 | — |

All three PCs are in the same IP subnet: `192.168.1.0/24`.

No default gateway is needed in this lab because there is no router. The test is only about local VLAN communication.

## Switch Configuration

```text
enable
conf t
hostname SW0
no ip domain-lookup

vlan 10
 name STAFF
exit

vlan 20
 name MANAGEMENT
exit

interface fa0/1
 description PC0_STAFF
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

interface fa0/3
 description PC1_STAFF
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

interface fa0/2
 description PC2_MANAGEMENT
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

end
write memory
```

## Verification

The configuration was checked with:

```text
show vlan brief
ping
```

Expected `show vlan brief` result:

```text
VLAN  Name          Status    Ports
10    STAFF         active    Fa0/1, Fa0/3
20    MANAGEMENT    active    Fa0/2
```

## Test Results

| Test | VLAN Relationship | Same IP Subnet? | Result |
|---|---|---|---|
| PC0 → PC1 `192.168.1.20` | Same VLAN 10 | Yes | Success |
| PC0 → PC2 `192.168.1.30` | Different VLANs | Yes | Failed |

Confirmed result:

```text
PC0 ping 192.168.1.20 = success
PC0 ping 192.168.1.30 = failed
```

## Explanation

PC0 and PC2 are both using the `192.168.1.0/24` subnet, but they are not in the same VLAN.

PC0 is in VLAN 10. PC2 is in VLAN 20.

A switch does not forward normal Layer 2 traffic between different VLANs. PC0's ARP request for PC2 never reaches PC2 because VLAN 10 and VLAN 20 are separate broadcast domains.

## Result

The lab worked as expected:

- PC0 and PC1 communicated successfully because both are in VLAN 10.
- PC0 could not reach PC2 because PC2 is in VLAN 20.
- The switch separated traffic logically using VLAN access ports.

## Documentation

Full documentation can be stored here:

```text
docs/project-03-vlan-access-ports.pdf
```

Screenshots can be stored here:

```text
screenshots/
```

Recommended screenshot names:

```text
topology.png
pc0-ip-configuration.png
pc1-ip-configuration.png
pc2-ip-configuration.png
show-vlan-brief.png
pc0-ping-pc1-success.png
pc0-ping-pc2-failed.png
```

## Key Concept

VLANs separate devices logically on the same physical switch. Devices in the same VLAN can communicate locally. Devices in different VLANs need routing before they can communicate.

## Next Lab

Project 04 - VLAN Trunk Between Two Switches
