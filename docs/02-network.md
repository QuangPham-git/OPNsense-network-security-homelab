# Network implementation

[Back to the project](../README.md)

## OPNsense and the internal networks

The foundation deployed OPNsense as VM 100 with Q35/OVMF, two virtual CPU cores, 4 GB RAM, and a 32 GB system disk. Three virtual NICs connect WAN, CLIENTS, and the internal VLAN trunk.

| Proxmox NIC | Bridge | OPNsense interface | Role |
| --- | --- | --- | --- |
| `net0` | `vmbr0` | `vtnet0` | WAN, DHCP from upstream router |
| `net1` | `vmbr1` | `vtnet1` | CLIENTS, untagged |
| `net2` | `vmbr2` | `vtnet2` | Parent for MGMT and SERVERS VLANs |

MGMT VLAN 10 and SERVERS VLAN 20 were created on the internal parent. The parent itself does not have the internal gateway address. MGMT uses a static address without DHCP; SERVERS hosts statically addressed services.

The MGMT VLAN initially existed in saved configuration without the expected runtime interface. After an OPNsense reboot, the VLAN device and connected route appeared. Proxmox then resolved the gateway's MAC address, while ICMP remained blocked until a temporary diagnostic firewall rule was enabled. That rule was subsequently disabled.

The useful distinction was configuration saved, interface active, neighbor resolved, and traffic permitted. Those were four separate observations. ARP had arrived at the party; ICMP was still outside checking the guest list.

## The virtual NIC bottleneck

Both physical Ethernet links negotiated at 1 Gb/s full duplex. Despite that, the E1000 virtual NIC path delivered about 100 Mbps with high firewall CPU use. All three VM NICs were changed to VirtIO while retaining their bridge assignments and MAC addresses.

The subsequent Internet test reached approximately 480 Mbps, with peak OPNsense CPU around 78%. The recorded checksum-offload, TSO, and LRO disable settings were retained. These are historical measurements on this path, not guaranteed performance or an isolated benchmark of every variable.

Before replacing a cable, check whether the virtual NIC is doing an impression of an older computer.

## Management access

The documented migration created Proxmox management on VLAN 10 and allowed the designated `ADMIN_CLIENTS` source to reach TCP 8006. Authorized access was tested. While the host still used its upstream gateway, a return route for CLIENTS through OPNsense enabled the response path.

The later address/gateway conflict is described in [Architecture](01-architecture.md#management-addressing-changed-after-the-master-runbook). A complete current ruleset and fresh unauthorized-client test were not retained, so the source's “management restriction complete” label is not promoted to a claim of comprehensive current enforcement.

## Protected Wi-Fi and DHCP

The downstream MX2000 was changed to bridge/AP mode. OPNsense Kea supplied CLIENTS addressing, gateway information, and the designated DNS path. DHCP reservations were started for devices requiring stable addresses.

The expected client result in the baseline was a CLIENTS address, the OPNsense CLIENTS gateway, and Pi-hole as resolver. Existing Wi-Fi association alone does not establish that routing or DNS is working. When the firewall is unavailable, a client may retain an address while losing inter-network connectivity.

Dnsmasq appears again in the later routing experiment as a DNS component. That does not establish that it replaced Kea as the working DHCP service.

## Pi-hole and DNS enforcement

Pi-hole runs in CT 101 on CLIENTS. HaGeZi Multi PRO was adopted as the primary filtering list. Normal resolution through Pi-hole worked while direct queries to public resolvers on ordinary DNS port 53 timed out.

The evidence supports filtering and the tested external DNS restriction. It does not prove universal prevention of DNS bypass. The exact final upstream resolver, complete rule order, and separate TCP/UDP test coverage were not recovered.

Because Pi-hole and CLIENTS share a subnet, their ordinary DNS exchange can remain at Layer 2 without crossing OPNsense. Firewall rules on OPNsense govern routed attempts to other networks; they do not automatically inspect every same-subnet DNS packet. The Pi-hole upstream path also needs to remain permitted.

DoH, DoT, and IPv6 behavior were unresolved in the records. The later Dnsmasq test is documented separately rather than replacing this known-working baseline with an unvalidated all-client design.

Blocking port 53 is useful. It does not make every application promise to behave.

## Local access policy and its evidence

This table records the implemented baseline and its known limits. It is not an export of live rule order.

| Path | Recorded permission or behavior | Limit |
| --- | --- | --- |
| Administrative CLIENTS to Proxmox | TCP 8006 allow; successful GUI access | Final alias membership and negative test missing |
| Other CLIENTS to MGMT | Restriction recorded | Current complete rules not recovered |
| CLIENTS to Jellyfin | TCP 8096 allow; service reached | Does not prove all other SERVERS traffic is denied |
| CLIENTS to other SERVERS traffic | Restricted baseline recorded | Later local SMB exception scope unresolved |
| CLIENTS to Pi-hole | Working DNS service | Same-subnet path normally avoids router filtering |
| CLIENTS to public DNS | Direct ordinary DNS attempts timed out | Encrypted DNS and IPv6 not covered by that result |
| Local CLIENTS to NAS | NAS access exists in history | Local firewall scope must be distinguished from Tailscale access |

Services on SERVERS VLAN 20 can communicate within the same Layer 2 network. VLAN separation from CLIENTS does not establish isolation between every pair of server containers. Similarly, an IP reservation is useful address management, not proof of device identity.

No public management or SMB port-forwarding setup is documented as an implemented feature. A fresh WAN/NAT rule export would be needed to make a stronger statement about current exposure.

## Corrected setup failures

A SERVERS subnet-mask error was corrected to the intended `/24`, restoring outbound connectivity. A later resolver correction allowed package updates. These results support the addressing and DNS fixes; they do not establish an undocumented custom NAT rule.

The foundation also demonstrated why saved settings, current routes, firewall states, and application listeners must be checked separately. The test is the path the packet actually takes, not the path drawn from memory.
