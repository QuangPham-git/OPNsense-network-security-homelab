# Phase 2 operations and recovery

[Back to the project](../README.md)

## What changed after the foundation runbook

The 16 September master runbook left backups, recovery proof, and further workloads open. Later evidence establishes meaningful progress: Crafty and test containers existed, scheduled Proxmox backup files were present, host and application outages were investigated, and Crafty was recovered after a stale-lock failure.

Some original closeout items remain open. Keeping them visible is part of documenting the work accurately.

## Scheduled backups were implemented

The 2 October user results establish a weekly Sunday 01:00 `vzdump` schedule targeting `backup-ssd`. The record includes backup artifacts dated 13, 20, and 27 September. The source directory contained 36 files totaling about 5.7 GB. Those are artifact counts, not a claim of 36 protected guests. The scheduler timezone and exact per-guest job coverage were not recovered.

The target at `/mnt/backup-ssd` was a roughly 300 GB logical volume on the same physical SSD as Proxmox and its guest storage. This updates the earlier master runbook, which had only established local baseline configuration copies and backup plans.

These backups can be useful for logical recovery when their storage remains available. They do not provide independence from system-SSD failure.

The folder called backup-ssd was real. The second physical SSD was not.

## Secondary copy was incomplete

The later copy toward the separate Toshiba/NAS storage showed 19 of 36 files and approximately 2.0 GB at the destination, compared with about 5.7 GB at the source. A dry run listed 17 missing files. No subsequent complete copy or successful restore result was recovered.

This was a separate-disk copy attempt within the same host environment. It is not described as off-host backup. The public record distinguishes:

| Capability | Evidence as of the recovered checkpoint |
| --- | --- |
| Weekly backup schedule | Recorded |
| Backup artifacts on the system SSD | Recorded |
| Secondary copy to the separate data disk | Started; incomplete at last documented comparison |
| Complete off-host copy | Not established |
| Successful guest or file restore | Not established |
| Protection of all external bind-mounted data | Not established |

Proxmox's container documentation states that `vzdump` does not back up the contents of bind mount points. Restoring the mount configuration is not the same as restoring the files underneath it. The external NAS data therefore needs its own verified protection. See the [official Proxmox container documentation source](https://github.com/proxmox/pve-docs/blob/master/pct.adoc).

## Host outage investigation

Late-September troubleshooting found a misleading mix of symptoms: some TCP reachability checks succeeded while the Proxmox web application or SSH session still failed to become usable. A local reset was followed by Proxmox and OPNsense returning to service.

Subsequent kernel evidence included ATA read/write timeouts, link resets, freezes, and I/O errors affecting the system SSD path across boots. SMART reported a passing result. These observations support investigating the storage path; they do not isolate the SSD, connection, controller, power delivery, or another component as the proven cause.

The project also recorded i915/DRM warnings and occasions when the expected Intel render device was missing. HDMI connection timing was discussed and changes in behavior were observed. A permanent graphics fix or a proven causal link between HDMI and the broader outages was not established.

The storage and graphics observations are retained as separate lines of evidence. No firmware update, drive replacement, kernel workaround, or permanent power-recovery fix is claimed here without a completed record.

SMART said “passed.” The kernel still had things to say.

## Crafty stale-lock recovery

On 2 October, CT 104 was available but Crafty was not listening on TCP 8443 and `crafty.service` had failed. The container root filesystem was only about 10% used and inode use about 1%, so simple capacity exhaustion did not explain that failure.

The investigation compared the lock's process reference with the real process. PID 100 belonged to `/sbin/agetty`, not Crafty. Source inspection identified the application's `app/config/session.lock` file. The recovered session summary reports that moving the stale lock aside and restarting the service restored an active Crafty process and IPv4/IPv6 listeners on port 8443.

The successful recovery is summary-level evidence, supported by the preceding user-provided failure and process observations. This is not a recommendation to delete lock files indiscriminately. The important step was establishing that the referenced process was not the application before treating the lock as stale.

Crafty had reserved a seat for a process that was no longer attending.

## Recovery boundaries

The lab has experienced real restarts and recovery. That does not establish a controlled unattended boot test covering every current guest, external mount, policy, and application.

The upstream Proxmox management path was present in the later 17 September record, but an outside-network test with OPNsense stopped was not established. The direct host Tailscale endpoint needs a working underlay route, and both Tailscale nodes share the same physical host.

Physical console access remains the documented fallback used during troubleshooting. A proposed temporary emergency subnet from earlier notes is not included as an implemented interface.

## Remaining evidence gaps

These are limits on the documented implementation, not completed tasks or a newly expanded project plan.

| Item | What is still missing |
| --- | --- |
| Independent remote recovery | Successful access from outside the home network while OPNsense is stopped |
| Current host management path | Final live and persistent gateway/address configuration |
| Whole-host startup | Controlled restart with storage mounts, services, and access verified afterward |
| Backup completion | Finished secondary copy, known job coverage, and usable restore evidence |
| External disk dependencies | Verified mount-before-service behavior and missing-disk handling |
| Remote policy cleanup | Final saved grants, listener cleanup, and meaningful denied-path tests |
| Hardware fault resolution | Evidence isolating the cause and verifying a durable correction |

The lab has made progress. A reboot that eventually works is useful recovery evidence; it is not automatically an unattended-recovery design.
