# Project 04 - VLAN Trunk Between Two Switches

This project demonstrates a basic VLAN trunk configuration between two Cisco switches in Cisco Packet Tracer.

The goal was to allow the same VLANs to work across separate switches using one trunk link, while keeping different VLANs isolated from each other.

## Project Scope

- Create VLAN 10 for IT
- Create VLAN 20 for Sales
- Configure access ports for end devices
- Configure a trunk link between the switches
- Allow VLAN 10 and VLAN 20 across the trunk
- Verify the configuration using show commands and ping tests

## Technologies Used

- Cisco Packet Tracer
- Cisco IOS switch CLI
- VLANs
- Access ports
- 802.1Q trunking
- Basic network troubleshooting

## Network Summary

| VLAN | Name | Subnet | Purpose |
|---|---|---|---|
| 10 | IT | 192.168.10.0/24 | IT hosts |
| 20 | Sales | 192.168.20.0/24 | Sales hosts |

The switches are connected using a trunk link on `GigabitEthernet0/1`. The trunk allows VLAN 10 and VLAN 20.

## Verification

The project was verified using:

```text
show vlan brief
show interfaces trunk
ping
```

Expected results:

- Hosts in VLAN 10 on different switches can communicate.
- Hosts in VLAN 20 on different switches can communicate.
- Hosts in VLAN 10 and VLAN 20 cannot communicate without a router or Layer 3 switch.

## Result

The trunk configuration worked as expected. Same-VLAN communication across switches was successful, and cross-VLAN communication failed as expected because inter-VLAN routing was not configured.

## Documentation

Full documentation with topology, configuration, screenshots, and verification results should be stored in this project folder as a PDF.
