# 1. Architecture and Addressing

## Objective

Create an isolated, reproducible network in which OPNsense is the routing and security boundary between a WAN-side test workstation and vulnerable or service-hosting systems on a private LAN.

## Components

| Component | Role |
|---|---|
| Windows host | Runs VMware, browsers, PuTTY/PSCP, and validation commands |
| OPNsense VM | Firewall, router, DHCP, DNS, NAT, state tracking, and IDS/IPS |
| Damn Small Linux VM | Internal web/FTP/Telnet test host |
| DVWA VM/server | Intentionally vulnerable Apache/PHP/MySQL application |
| VMware WAN network | NAT or isolated WAN-side segment connected to OPNsense `em0` |
| VMware LAN network | Host-only segment connected to OPNsense `em1` |

## Addressing captured in the archive

| Interface/system | Address or range | Notes |
|---|---|---|
| OPNsense LAN | `192.168.237.100/24` | Default gateway and DNS server for the lab LAN |
| LAN network | `192.168.237.0/24` | Isolated host-only network |
| DHCP pool | `192.168.237.30-192.168.237.64` | Issued by OPNsense |
| OPNsense WAN | `192.168.109.129/24` | Captured in interface-status evidence |
| WAN gateway/DNS | `192.168.109.2` | VMware NAT service in that lab iteration |
| NAT validation | WAN-side address on port `2456` | Later evidence shows a different WAN address after readdressing |

## Important design rule

Only the OPNsense VM should connect to both networks. Vulnerable targets should have one NIC on the isolated LAN. This prevents a target from bypassing the firewall and prevents VMware or the physical network from becoming an unintended alternate gateway.

## VMware configuration

1. Create or select one WAN-side virtual network. VMware NAT is acceptable for updating packages and rules.
2. Create a host-only LAN network.
3. Disable VMware's DHCP service on the LAN network so OPNsense is the only DHCP server.
4. Give OPNsense two adapters:
   - Adapter 1: WAN network
   - Adapter 2: isolated LAN
5. Give the Linux and DVWA targets only the isolated LAN adapter.
6. Take VM snapshots before major changes.

## Verification

From a LAN target:

```bash
ip addr
ip route
cat /etc/resolv.conf
ping -c 4 192.168.237.100
```

Expected result:

- Address is inside the OPNsense DHCP pool.
- Default route points to the OPNsense LAN address.
- DNS points to the OPNsense LAN address.
- No second interface provides a bypass route.
