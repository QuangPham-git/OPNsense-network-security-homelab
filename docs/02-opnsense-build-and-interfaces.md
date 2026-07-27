# 2. OPNsense Build and Interface Setup

## Goal

Install or import OPNsense, assign the two virtual NICs correctly, and establish a stable management path from the LAN.

## Procedure

1. Create an OPNsense VM with two virtual NICs.
2. Boot the installer or the provided lab image.
3. At the console, assign interfaces:
   - WAN: `em0`
   - LAN: `em1`
4. Set the LAN IPv4 address to the selected static gateway address. The archived configuration used `192.168.237.100/24`.
5. Disable LAN DHCP temporarily if another DHCP server is still active; enable it only after VMware DHCP is disabled.
6. Configure the WAN for DHCP unless the WAN network requires a static address.
7. When the WAN itself uses RFC1918 space in a lab, clear **Block private networks** and **Block bogon networks** on the WAN. Those protections are correct for a public WAN but can break an intentionally private VMware WAN.
8. Apply changes and open the GUI from a LAN-connected system.

## Verification checklist

- [ ] `em0` is WAN and has a valid lease or static address.
- [ ] `em1` is LAN and has the chosen static address.
- [ ] The OPNsense GUI is reachable only through the intended management side.
- [ ] OPNsense can resolve DNS and reach the Internet when rule updates are required.
- [ ] The LAN target can ping its OPNsense gateway.

## Evidence

![WAN status](../screenshots/opnsense/01-wan-status.jpg)

![Interface assignments](../screenshots/opnsense/02-interface-assignments.jpg)
