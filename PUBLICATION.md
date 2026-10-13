# Public release notes

## Scope of this package

This release contains Proxmox home-lab documentation in Markdown. It consolidates the foundation history and supported Phase 2 work as of 13 October 2026. It contains no live configuration bundle, deployment script, guest image, or raw diagnostic export.

The original Word documents remain private source material. Their screenshots and embedded media are not included. The public diagrams are newly written descriptions of the documented network relationships.

## Sanitization choices

| Material | Public treatment |
| --- | --- |
| Operational LAN addresses | Consistently replaced with the `10.42` example network model |
| Public WAN and VPN endpoints | Omitted |
| Tailnet addresses, account identities, personal email addresses, and membership | Omitted |
| Passwords, authentication keys, API tokens, certificates, and private keys | Not included |
| MAC addresses, storage UUIDs, disk serials, and WWNs | Omitted |
| Original screenshots, logs, packet captures, and configuration exports | Not packaged |
| Profanity and crude analogies | Rewritten while retaining the troubleshooting humor |
| Unsupported success claims | Replaced with dated observations or explicit evidence limits |

Generic paths, workload IDs, VLAN tags, protocol ports, and historical performance measurements remain because they explain the implementation. The public address model is illustrative; it is not a configuration to apply to the running lab.

## Publication boundary

The accompanying `.gitignore` helps exclude common private exports and credentials from future additions. It does not inspect file contents, remove files already tracked by Git, or erase an existing repository's history.

This package was prepared separately from the raw sources. Its review covers the delivered package, not an existing GitHub repository or its earlier commits. Do not add the private source documents alongside these files. Review any future screenshot or export separately before including it.
