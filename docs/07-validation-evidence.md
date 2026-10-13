# Validation and evidence

[Back to the project](../README.md)

## Evidence labels

This edition reconciles four private runbooks and recovered project history through 13 October 2026. No live connection to the lab was made while preparing it.

**Recorded** means a supplied document or recovered user command result establishes the statement at its checkpoint. **Reported** means the detail comes from a recovered session summary without the full result. **Unconfirmed** means the available evidence is missing, contradictory, or limited to a proposed step.

Public addresses have been replaced consistently. The original screenshots, configuration exports, and transcripts remain outside this repository. Product documentation explains behavior; it does not prove a local deployment.

## Source register

| ID | Private source reviewed | Contribution |
| --- | --- | --- |
| D1 | Historical Proxmox Phase 1 OPNsense Summary Runbook | Initial bridges, VM, VLAN activation, VirtIO correction, and management tests |
| D2 | Historical Proxmox Phase 1 Part 2 Implementation Summary | Management migration, AP, Kea, Pi-hole, SERVERS, and Jellyfin |
| D3 | Historical Tailscale OPNsense FreeBSD Routing Documentation | Unexpected ping, firewall-originated traffic, and remote access model |
| D4 | Proxmox Home Lab Phase 1 Master Runbook, through 16 September | Consolidated services, storage, policy gaps, and earlier recovery status |
| H1 | Recovered 17 September host networking results | Later upstream address/default gateway observation contradicting D2/D4 |
| H2 | Recovered 20–23 September game-hosting history | Crafty network, Minecraft startup, Fabric process, heap failure; pregeneration completion reported |
| H3 | Recovered late-September–2 October recovery history | Service access failures, storage/graphics observations, backup evidence, and Crafty lock recovery |
| H4 | Recovered 30 September–8 October routing history | Kali/CT 107 configuration, Dnsmasq query, tunnel tests, and unfinished routing integration |
| H5 | Recovered 12 October host inventory | Relevant VM and LXC presence and run state |

The source IDs refer to private evidence, not publicly downloadable files. The original runbooks also include earlier proposed steps; only supported implementations were carried into the accomplishment descriptions.

## Chronology and confidence

| Checkpoint | Supported change or finding | Basis |
| --- | --- | --- |
| 31 August–early September | WAN/CLIENTS foundation; virtual trunk; MGMT activation; NIC model correction | D1 recorded |
| By 8 September | Kea, AP bridge mode, Pi-hole, SERVERS, and Jellyfin configured; management cutover recorded | D2 recorded |
| By 16 September | NAS, Samba, Tailscale SERVERS routing and service access documented | D3/D4 recorded; some details inherited from summaries |
| 17 September | Upstream host address/default gateway present alongside MGMT; return-route persistence unresolved | H1 user output recovered |
| 20 September | Crafty network and Minecraft server startup succeeded | H2 user results recovered |
| 23 September | Fabric execution and heap exhaustion observed; later Chunky completion reported | H2 mixed user output and summary |
| Late September–2 October | Host outage investigation; SSD-path errors and graphics observations | H3 user results and recovered summaries |
| 2 October | Weekly backup artifacts verified; secondary copy incomplete | H3 user results recovered |
| 2 October | Stale Crafty lock identified; service recovery reported | H3 user failure/process evidence; recovery summary |
| Through 8 October | VPN/DNS/routing components tested; final selective routing incomplete | H4 user output and summary |
| 12 October | Relevant VM/LXC inventory recorded | H5 user output recovered |

## Read-only checks for a later refresh

These commands are provided to refresh the record, **not as tests executed for this release**. Outputs can contain private identifiers. Keep raw results private and publish only reviewed summaries. Example addresses throughout the repository are replacements.

### Proxmox host

```bash
pveversion -v
ip -br addr
ip route
bridge vlan show
qm list
qm config 100
pct list
pct config 101
pct config 102
pct config 103
pct config 104
pct config 106
pct config 107
pvesm status
findmnt -T /mnt/storage -o SOURCE,TARGET,FSTYPE,OPTIONS
findmnt -T /mnt/backup-ssd -o SOURCE,TARGET,FSTYPE,OPTIONS
lsblk -o NAME,SIZE,TYPE,FSTYPE,MOUNTPOINTS
journalctl --list-boots
```

Compare runtime addresses and routes with the persistent host network configuration privately. A complete current export would resolve the management gateway conflict. Check guest `onboot` and startup fields before describing the startup order as implemented.

`findmnt -T` can identify a parent filesystem when the intended data disk is absent. Check that the result's target and source are actually the intended mounted filesystem; output appearing on the screen is not automatically a passed mount check.

### OPNsense shell

```sh
opnsense-version
ifconfig
netstat -rn -f inet
pfctl -sr
pfctl -sn
tailscale version
```

The loaded rules, interface assignments, and saved policy need to agree. Do not place the raw OPNsense configuration export in this repository: it may include secrets and operational identities.

### Guest-local checks

Run each group in the named container, through its console or the appropriate Proxmox `pct` context.

```bash
# Jellyfin CT 102
ls -l /dev/dri
id jellyfin
vainfo --display drm --device /dev/dri/renderD128
systemctl status jellyfin --no-pager

# NAS CT 103
testparm -s
systemctl status smbd --no-pager
ss -lntp

# Crafty CT 104
systemctl status crafty.service --no-pager
ss -lntp

# Kali CT 106
ip -br link
ip -br addr
ip route

# VPN test CT 107
ip -br addr
ip rule show
ip route show table 200
nft list ruleset
```

Samba configuration, nftables rules, interface output, and logs can reveal account names and private network details even without a password. A read-only command is not automatically publication-safe output.

## What would close the current gaps

This table defines evidence requirements; it does not claim that the checks passed.

| Question | Needed evidence |
| --- | --- |
| Is local management restricted as intended? | Allowed administrator connection and denied ordinary-client connection to a listening target, with matching rule evidence |
| Are local NAS permissions separate from remote access? | Local access test with the test client's Tailscale path disabled, plus actual CLIENTS rule scope |
| Is remote cleanup finished? | Saved policy, remaining listeners, allowed-service tests, and denied-path results |
| Does Jellyfin use hardware transcoding? | A workload requiring transcoding plus dashboard/FFmpeg evidence of hardware use |
| Are backups recoverable? | Exact guest/data coverage, completed copies, and a restore with a usable recovered guest or file |
| Does startup survive a host reboot? | Mounted data disk, intended guest startup, and functional service paths after controlled restart |
| Is independent remote recovery real? | Outside-network access demonstrated while OPNsense is stopped |
| Is selective routing finished? | Confirmed destination population, YouTube/non-YouTube path separation, failure behavior, and persistence |

## Technical references

- [Tailscale kernel and userspace subnet routing](https://tailscale.com/docs/reference/kernel-vs-userspace-routers) explains the FreeBSD connection handling used in the remote-access analysis.
- [Tailscale subnet-router setup](https://tailscale.com/docs/features/subnet-routers/how-to/setup) separates advertisement, approval, access rules, and client route use.
- [Official Proxmox container documentation source](https://github.com/proxmox/pve-docs/blob/master/pct.adoc) documents the backup boundary for bind mount contents.

## Updating this record

For the next implemented change, retain a private dated result, identify the object changed, record the observed outcome, and update the matching public section. Keep unsuccessful results when they explain a limit. Replace an uncertainty only when new evidence answers it.

The best change log occasionally says, “That did not work, and here is what we learned.” It should not quietly promote the next suggestion to a completed feature.
