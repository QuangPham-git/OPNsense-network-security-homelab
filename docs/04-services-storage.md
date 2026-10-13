# Services and storage

[Back to the project](../README.md)

## Pi-hole

CT 101 provides DNS filtering on CLIENTS. The recorded primary list is HaGeZi Multi PRO. DNS resolution through Pi-hole and failed direct public-resolver queries are part of the implemented network baseline.

The final OS version, resource allocation, upstream resolver selection, and complete enabled-list export were not retained. The later Dnsmasq selective-routing work does not establish a completed replacement of the baseline resolver path. [Network implementation](02-network.md) describes the enforcement limits.

## Jellyfin and Intel graphics

CT 102 hosts Jellyfin on SERVERS at public example `10.42.20.10`, TCP 8096. Correcting the SERVERS subnet mask restored network access, and a subsequent DNS correction allowed package updates.

The Intel render device `/dev/dri/renderD128` was made available to the container. The record includes Intel iHD support from `vainfo` and Jellyfin configured for Intel Quick Sync Video. This is shared host graphics-device access inside an LXC; it is not evidence of an exclusively assigned PCI GPU in a VM.

Device visibility, driver capability, selected acceleration settings, and a completed hardware-transcoding workload are different checks. The first three are recorded. A retained application-level hardware-transcoding test is not. Direct playback alone would not settle that question.

The planned media bind was host `/mnt/storage/Media` to container `/media`, read-only. The exact final mount and library configuration were not recovered, so that bind is not presented as confirmed. This also leaves open whether a particular media read depends on the NAS application or directly on the host filesystem.

The GPU was invited to help. That does not mean every video actually asked it to do any work.

## External data disk and NAS

The separate 16 TB disk originally had a Windows partition layout and was reformatted to ext4 during the storage work. The recorded host mount is `/mnt/storage`. CT 103 exposes that data through `/srv/storage` and Samba on SERVERS, at public example `10.42.20.20`.

| Share | Recorded container path | Purpose |
| --- | --- | --- |
| `Media` | `/srv/storage/Media` | Media collection with Movies, TV, and Music folders |
| `Shares` | `/srv/storage/Shares` | General files |
| `Downloads` | `/srv/storage/Downloads` | Staging directory |
| `Backups` | `/srv/storage/Backups` | Backup destination directory |

Windows share access was recorded. Troubleshooting also addressed retained SMB sessions and stale credentials. Selecting a different drive letter did not necessarily create a new authentication session to the same server.

The share definitions were reported as writable with forced user/group ownership. Final numeric ownership, masks, and full Samba settings are not published as verified configuration. The account name and credentials are omitted. Exact mount identifiers and final `fstab` options are also omitted rather than reconstructed.

An empty directory named `/mnt/storage` is not proof that the external disk is mounted. The distinction matters because a service can otherwise write onto the underlying system disk. A completed missing-disk startup guard is not established in the record.

The `Backups` folder also shares its disk with the other data. Naming a folder Backups is excellent organization. It is not a new failure domain.

## Crafty and Minecraft in Phase 2

Crafty was added in CT 104 on SERVERS. Its recorded address corresponds to public example `10.42.20.30/24` with the SERVERS gateway. Connectivity was tested successfully during setup. Later records identify the container as Debian 13.

The 20 September Minecraft startup output showed world generation, the server listening on TCP 25565, and successful startup. A later 23 September Java command used `fabric.26.3.jar`, with Fabric also visible in stack traces. The filename is preserved as the observed local artifact name; it is not used to infer an unrecorded loader or mod version.

The observed launch used a 1,000 MB initial Java heap and 4,000 MB maximum heap. Chunk pregeneration encountered Java heap exhaustion while saving chunks. A larger container allocation and 6,000 MB maximum heap were suggested, but successful application of those changes was not recovered.

The recovered session summary reports completion of a 5,000-block-radius Chunky pregeneration run. That is summary-level evidence; the full completion output is not included in this public record. A full Minecraft mod inventory and final tuning configuration are not established.

Before the Vanilla-to-Fabric work, a Crafty Full Zip Vault backup configuration was discussed and accepted, with shutdown and compression enabled and no exclusions. The recovered record does not include a completed archive verification or restore result. This is kept separate from the later proven existence of Proxmox backup files.

The Crafty web interface later failed because of a stale application lock. The recovery evidence is covered in [Phase 2 operations](05-phase2-operations.md#crafty-stale-lock-recovery).

The server successfully hosted a world. The JVM also provided a practical demonstration that a heap limit is a limit, not an opening offer.

## Kali test workloads

VM 105 and CT 106 appear in the later inventory. VM 105 was stopped on 12 October; CT 106 was running. The retained CT 106 configuration shows an unprivileged container, nesting enabled, two cores, 2 GB RAM, and an interface on `vmbr1`/CLIENTS.

CT 106 was used as the single-client scope for the selective-routing experiment. Its recorded address corresponds to public example `10.42.30.4`. Troubleshooting found its `eth0` down; bringing the link up corrected that immediate condition. Later attempts encountered routing issues, including an absent IPv4 default route. No persistent final fix for all client connectivity issues was established.

Nesting is a container feature setting, not evidence of faster application performance. Similarly, Kali's presence does not establish a completed penetration test. This record documents the deployed test environment and the work actually observed.

## Service status is layered

The 12 October inventory shows the relevant service containers running. That is useful evidence that the workloads exist and were started. It does not establish every application listener, storage dependency, access restriction, or reboot behavior.

The source material does not establish deployed media automation tools, an NVR/camera system, RAID, or a second physical virtualization host. Those ideas are not included in the implemented service inventory.
