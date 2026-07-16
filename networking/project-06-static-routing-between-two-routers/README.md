# Project 06 - Static Routing Between Two Routers

Basic static routing lab in Cisco Packet Tracer.

This project shows how two routers can connect two separate LANs using manually configured static routes. Each router already knows its directly connected networks. Static routes are added so each router knows how to reach the remote LAN.

## Topology

```text
PC0 --- Switch0 --- Router0 === Router1 --- Switch1 --- PC1

Left LAN:        192.168.1.0/24
Router link:     10.0.0.0/30
Right LAN:       192.168.2.0/24
```

## IP Addressing Plan

| Device | Interface | IP Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| PC0 | Fa0 | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| Router0 | Gig0/0 LAN | 192.168.1.1 | 255.255.255.0 | - |
| Router0 | Gig0/1 to Router1 | 10.0.0.1 | 255.255.255.252 | - |
| Router1 | Gig0/1 to Router0 | 10.0.0.2 | 255.255.255.252 | - |
| Router1 | Gig0/0 LAN | 192.168.2.1 | 255.255.255.0 | - |
| PC1 | Fa0 | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |

## Static Routes

Router0 needs a route to the right LAN:

```text
ip route 192.168.2.0 255.255.255.0 10.0.0.2
```

Router1 needs a route to the left LAN:

```text
ip route 192.168.1.0 255.255.255.0 10.0.0.1
```

## Next-Hop Logic

The next hop is the neighbor router's IP address on the shared router-to-router link.

- Router0 uses `10.0.0.2` because that is Router1's IP on the shared link.
- Router1 uses `10.0.0.1` because that is Router0's IP on the shared link.

## Verification

The configuration was checked with:

```text
show ip interface brief
show ip route
ping
```

Expected result:

- Before static routes: PC0 cannot reach PC1.
- After static routes: PC0 and PC1 can ping each other.
- `show ip route` shows routes marked with `S` for static.

## Documentation

```text
docs/Lab_06_Static_Routing_Between_Two_Routers_CLEAN_No_BreakFix.pdf
screenshots/
```

## Result

The lab worked as expected. After static routes were added on both routers, traffic could pass between the two LANs.

## Key Concept

Routers know directly connected networks automatically. Remote networks must be learned using static routes or a dynamic routing protocol. In this lab, the routes were added manually with static routing.
