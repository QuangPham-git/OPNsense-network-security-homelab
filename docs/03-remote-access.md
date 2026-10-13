# Remote access and the unexpected ping

[Back to the project](../README.md)

## Implemented access paths

Tailscale was installed on the Proxmox host and on OPNsense. OPNsense advertised the SERVERS subnet, and remote service access through that route was recorded. Direct Proxmox access uses its own tailnet endpoint. Final advertisement of MGMT or CLIENTS was not established.

| Destination | Recorded steady-service target | Control to consider |
| --- | --- | --- |
| Proxmox tailnet endpoint | TCP 8006 | Tailnet permission, host firewall, application listener |
| OPNsense tailnet endpoint | TCP 443 | Tailnet permission, OPNsense host-access rule, WebGUI |
| Jellyfin on SERVERS | TCP 8096 | Tailnet service permission and subnet-router path |
| NAS on SERVERS | TCP 445 | Tailnet service permission, subnet-router path, Samba authentication |

The September history reports a saved grants policy preserving those four services and temporary TCP 5201 benchmark access. The final full policy and cleanup results are missing. Later troubleshooting included changing connectivity observations, so this repository does not claim that SSH or every other port is currently denied.

## A reachable peer was not a reachable WebGUI

An early Tailscale ping succeeded while a TCP 443 connection to OPNsense failed. Assigning and enabling the Tailscale interface and permitting authorized host access addressed the GUI path; later evidence shows access through the tailnet endpoint.

The historical screenshot's first host-access rule was broader than the prose's intended TCP 443 rule. It proves that a broad rule existed at that checkpoint, not that HTTPS-only narrowing was completed afterward.

Ping answered. The web interface did not. Networking had successfully completed step one of several.

## Why the NAS answered an unexpected ping

The original investigation expected the remote client source to traverse an ordinary forwarded firewall path. Instead, the recorded OPNsense log showed outbound SERVERS traffic sourced from the firewall's own SERVERS address. The firewall-host outbound rule allowed it.

Tailscale documents FreeBSD subnet routing as userspace netstack operation. For TCP and UDP, the router accepts a connection and opens another toward the destination. Echo requests are recreated as well. This explains the observed lab path; a changed source address alone would not distinguish proxying from ordinary source NAT. See [Tailscale's kernel and netstack reference](https://tailscale.com/docs/reference/kernel-vs-userspace-routers).

The original remote identity was therefore not present in the server-facing flow in the form the interface-rule design assumed. The unexpected ping did not demonstrate a broken default deny.

The firewall was not ignoring the guest list. We were checking the wrong doorway.

## Put each check on the path it controls

| Traffic | Relevant boundary in this implementation |
| --- | --- |
| Local CLIENTS to MGMT or SERVERS | Normal OPNsense inter-network firewall rules |
| Remote connection terminating on OPNsense | Tailnet access policy plus OPNsense host-access rules |
| Remote connection to SERVERS through the subnet router | Tailnet identity/service policy; OPNsense originates the service-facing flow |
| Authentication to NAS or a management application | The destination application's credentials and permissions |

This does not mean OPNsense has no control over its own output, or that every interface rule is useless. It means the original inbound subnet-service rules did not enforce the flows in the way expected. A separate local-output filtering design is not established here.

The working lesson is to distinguish route reachability, remote access permission, firewall handling, and application authentication. See the official [subnet-router documentation](https://tailscale.com/docs/features/subnet-routers/how-to/setup) for the routing and approval model. Those references describe product behavior; they are not proof of this lab's configuration.

## Cleanup status

The project retained Tailscale after understanding the behavior. A completed replacement with native WireGuard or a Linux subnet router is not part of this record.

Temporary benchmark grants/listeners and redundant historical service rules were identified for cleanup. Final evidence that every one was removed was not recovered. A failed connection to a closed port would not, by itself, prove a permission was removed: a meaningful deny test needs a listening target and the relevant policy evidence.

Two Tailscale icons still live on one physical computer. They do not create two independent recovery systems. Whether direct host access survives an OPNsense outage also depends on the host's actual Internet route, which changed during this project.

## Historical performance observations

| Path | Recorded result | Interpretation limit |
| --- | --- | --- |
| Remote client to NAS through OPNsense | About 32 Mbps with one stream; 48 Mbps with four | Different endpoint and forwarding path from direct host test |
| Remote client to Proxmox directly | About 155 Mbps with one stream; 113 Mbps with four | Does not isolate one cause for the difference |
| NAS to remote client in reverse test | About 17 Mbps | Consistent with the reported roughly 20 Mbps home upload |
| Proxmox reverse test with four streams | About 1.10 Mbps | Unresolved anomaly |
| Proxmox to NAS local baseline | Test reported unsuccessful | No dependable local throughput baseline retained |

The results make the subnet-router path worth investigating, but do not prove that FreeBSD userspace processing was the sole bottleneck. Units and direction matter: MB/s and Mbps are different, and a fast download plan does not turn the upload arrow around.
