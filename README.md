# Proxmox Home Lab

## Phase 2 implementation record

I built this lab on one Intel N95 mini-PC to run a segmented network, host useful services, and investigate what happens when the network, storage, or an application stops cooperating. Proxmox hosts an OPNsense firewall VM and Linux containers for DNS filtering, media, file storage, game hosting, and testing.

**Current project phase: Phase 2. Documentation checkpoint: 13 October 2026.**

The Phase 1 foundation is in use. Phase 2 adds workloads, backup operations, recovery investigations, and a partially implemented selective-routing experiment. This is a record of work supported by implementation notes and observed results. It is not a claim that every recovery or security check has passed, and it is not a live audit.

One small computer. Several services. An excellent way to learn why dependencies matter.

## What has been built

| Area | Implemented work | Evidence boundary |
| --- | --- | --- |
| Virtualization | Proxmox host; OPNsense VM; Linux service and test containers | Relevant inventory confirmed on 12 October |
| Network separation | MGMT VLAN 10, SERVERS VLAN 20, and an untagged CLIENTS network | CLIENTS is not an implemented VLAN 30 |
| Network services | OPNsense routing and Kea DHCP; downstream AP in bridge mode; Pi-hole filtering | Ordinary external DNS tests passed; encrypted DNS and IPv6 coverage remain unproven |
| Media and storage | Jellyfin with Intel graphics access and QSV configuration; ext4 data disk; Samba NAS | QSV configuration is recorded; a retained hardware-transcoding test is missing |
| Remote access | Tailscale on Proxmox and OPNsense; SERVERS subnet routing; service-specific access policy | Final removal of temporary benchmark permissions is not established |
| Game hosting | Crafty container and working Minecraft server; later Fabric execution | Crafty stale-lock recovery recorded; final memory tuning not established |
| Backup operations | Scheduled weekly Proxmox backups to a dedicated logical volume | Target shares the system SSD; secondary copy was incomplete; no proven restore |
| Routing experiment | VPN container, test-client routing components, NAT, and a blackhole route | No completed end-to-end YouTube-only routing validation |

## Documentation

| Read | Covers |
| --- | --- |
| [Architecture and inventory](docs/01-architecture.md) | Hardware, bridges, zones, workloads, and management-state conflict |
| [Network implementation](docs/02-network.md) | OPNsense, VLANs, DHCP, DNS, firewall boundaries, and performance correction |
| [Remote access](docs/03-remote-access.md) | Tailscale on FreeBSD, the unexpected ping, and access-control lessons |
| [Services and storage](docs/04-services-storage.md) | Pi-hole, Jellyfin, NAS, Crafty, Minecraft, and Kali |
| [Phase 2 operations](docs/05-phase2-operations.md) | Backups, outages, application recovery, and observed limits |
| [Selective routing experiment](docs/06-selective-routing.md) | Components implemented, tests observed, and where the experiment stopped |
| [Validation and evidence](docs/07-validation-evidence.md) | Read-only checks, implementation chronology, and source confidence |
| [Publication notes](PUBLICATION.md) | Sanitization choices and boundaries of this public release |

## Phase labels

Earlier documents used several phase plans and sometimes called deployed services “Phase 1 Part 2.” This edition keeps the original work in the foundation history and uses **Phase 2** for the current project. Renaming the phase does not turn an unfinished test into a completed feature.

The 16 September master runbook is the historical foundation checkpoint. Later dated evidence updates it: management addressing observed on 17 September, game hosting from 20 September, late-September and early-October recovery work, backup evidence on 2 October, selective-routing work through 8 October, and the 12 October workload inventory.

## Public example addresses

Every lab address shown here is a **replacement example**, not a live endpoint. The documentation uses `10.42.10.0/24`, `10.42.20.0/24`, and `10.42.30.0/24` to preserve the relationships between zones. Tailnet identities, upstream addresses, accounts, MAC addresses, disk identifiers, and VPN endpoints are omitted.

Guest IDs and generic filesystem paths are retained because they make the runbook understandable. Commands require local context; the example addresses are not deployment instructions.

## What this release does not claim

There is no established deployment of a home-lab IDS/SIEM, completed encrypted-DNS containment, validated independent remote recovery, complete off-host backup and restore, or finished selective-routing automation. A running container proves that the container is running; its application still needs its own test.

The lab is useful, and it has taught useful lessons. “Everything is fully hardened” is not one of the recorded results.
