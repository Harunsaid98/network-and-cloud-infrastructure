# Project 07 - DHCP Per VLAN

Cisco Packet Tracer lab using VLANs, router-on-a-stick, and DHCP.

The goal of this lab was to stop assigning IP settings manually to every PC. I created two VLANs, used one trunk link to the router, configured a gateway for each VLAN, and then used the router as the DHCP server.

## Topology

```text
PC0 --- Fa0/1
               \
PC1 --- Fa0/2 --- Switch0 Gi0/1 === Gi0/0 Router1
               /
PC2 --- Fa0/3

PC0 + PC1: VLAN 10 (IT)
PC2:       VLAN 20 (Sales)
Switch0 Gi0/1 <-> Router1 Gi0/0: 802.1Q trunk
```

## VLAN and IP plan

| Device | Switch port | VLAN | Network | Gateway |
|---|---|---:|---|---|
| PC0 | Fa0/1 | 10 - IT | 192.168.10.0/24 | 192.168.10.1 |
| PC1 | Fa0/2 | 10 - IT | 192.168.10.0/24 | 192.168.10.1 |
| PC2 | Fa0/3 | 20 - Sales | 192.168.20.0/24 | 192.168.20.1 |

## Switch configuration

I first created the VLANs and placed the PC ports in the correct VLAN.

```text
vlan 10
 name IT

vlan 20
 name Sales

interface fa0/1
 switchport mode access
 switchport access vlan 10

interface fa0/2
 switchport mode access
 switchport access vlan 10

interface fa0/3
 switchport mode access
 switchport access vlan 20
```

I then configured the uplink to the router as a trunk.

```text
interface gi0/1
 description Trunk_To_R0
 switchport mode trunk
 switchport trunk allowed vlan 10,20
 no shutdown
```

## Router subinterfaces

One physical router interface is connected to the switch, so I used one subinterface for each VLAN gateway.

```text
interface gi0/0
 no shutdown

interface gi0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface gi0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
```

The `.10` and `.20` subinterface numbers match the VLAN numbers to keep the configuration easy to read. The `encapsulation dot1Q` command associates the traffic with the correct VLAN.

## DHCP configuration

I reserved the first 20 addresses in each subnet so the gateway and other infrastructure addresses are not handed out to normal clients.

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.20.1 192.168.20.20
```

Then I created one DHCP pool for each VLAN.

```text
ip dhcp pool IT
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

ip dhcp pool Sales
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
```

## Verification

I used these commands while checking the lab:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show run | section dhcp
show ip dhcp binding
```

On the PCs:

```text
ipconfig /all
ping <gateway>
```

PC0 in VLAN 10 successfully received `192.168.10.21/24`, gateway `192.168.10.1`, and DNS `8.8.8.8` from DHCP.

## Troubleshooting I did

PC2 initially failed to get a DHCP lease and assigned itself a `169.254.x.x` APIPA address.

Instead of changing several things at once, I checked the path in order:

```text
show vlan brief
show interfaces trunk
show ip interface brief
show run | section dhcp
```

The switch checks showed that Fa0/3 belonged to VLAN 20, Gi0/1 was trunking, and VLAN 20 was allowed across the trunk. I then checked the VLAN 20 DHCP configuration on the router. DHCP started working for the Sales VLAN, and I added the missing excluded-address range so `.1` through `.20` would remain reserved.

## What I learned

The main thing I took from this lab is the configuration and troubleshooting order:

```text
Connect devices
→ Create VLANs
→ Assign access ports
→ Configure trunk
→ Enable router interface
→ Create router subinterfaces and gateways
→ Verify Layer 2 and Layer 3
→ Exclude reserved IPs
→ Create DHCP pools
→ Set clients to DHCP
→ Verify leases
→ Test connectivity
```

A trunk carries multiple VLANs over one physical link. Router subinterfaces separate the VLAN traffic and provide a gateway for each VLAN. DHCP then gives clients the correct network settings for the VLAN they belong to.

## Final checks

- [x] VLAN 10 and VLAN 20 created
- [x] Access ports assigned correctly
- [x] Trunk carries VLANs 10 and 20
- [x] Router subinterfaces configured
- [x] VLAN 10 DHCP working
- [x] VLAN 20 DHCP pool configured
- [x] Reserved IP ranges configured
- [ ] Add final PC2 lease screenshot after renewal
- [ ] Add `show ip dhcp binding` screenshot
- [ ] Add final ping-test screenshots
