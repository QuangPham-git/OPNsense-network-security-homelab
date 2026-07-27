# 12. Troubleshooting and Lessons Learned

## 1. Two DHCP servers on the LAN

**Symptom:** Clients receive unexpected addresses, gateways, or DNS servers.

**Cause:** VMware DHCP and OPNsense DHCP are both active on the host-only segment.

**Fix:** Disable VMware DHCP for the isolated LAN and renew the client lease.

## 2. WAN has a private address but cannot pass traffic

**Symptom:** OPNsense receives a VMware NAT address but cannot communicate correctly.

**Cause:** **Block private networks** or **Block bogon networks** is enabled on an intentionally private WAN.

**Fix:** Disable those options only for the closed lab WAN.

## 3. Host route bypasses OPNsense

**Symptom:** The Windows host communicates directly through an on-link VMware route rather than through the firewall.

**Fix:** Inspect `route print`, remove the conflicting route, and add a route that points the target subnet to the OPNsense WAN address. Record the original route so it can be restored after the lab.

```powershell
route print
route delete <TARGET_SUBNET>
route add <TARGET_SUBNET> mask <MASK> <OPNSENSE_WAN_IP> metric 2 if <INTERFACE_ID>
```

## 4. NAT rule exists but the service is unreachable

Check:

- Rule was applied.
- Correct interface is selected.
- Destination is **WAN address**.
- Redirect target alias contains the current internal address.
- Associated firewall rule exists.
- Internal service is listening on the expected port.
- Host firewall permits the service.

## 5. Block rule appears ineffective

OPNsense uses first-match evaluation. Move a specific block rule above broad default allow rules and apply changes. Confirm the rule label in Live View rather than relying only on a browser error.

## 6. Legacy browser HTTP/HTTPS behavior

The Damn Small Linux browser may fail because a modern site requires TLS it cannot support. Use a controlled plain-HTTP service when proving a firewall allow/block result.

## 7. FTP works inconsistently

FTP uses separate control and data channels. Active/passive behavior can require additional ports or an FTP helper. Prefer a controlled test server with an explicit passive range and document every allowed port.

## 8. IPS does not alert or drop

Check:

- Correct interface selected
- IPS mode enabled for the drop test
- Rule enabled and policy applied
- Rule updates completed
- Hardware offloading disabled as required
- Traffic actually crosses OPNsense rather than using an alternate route

## 9. DVWA/sqlmap redirects to login

Refresh the authenticated session cookie, verify `security=low`, and confirm the request targets the SQL Injection page rather than the blind-SQL page.

## 10. MySQL file access fails

`LOAD_FILE` and file writes depend on database privileges, filesystem permissions, and `secure_file_priv`. Treat errors as expected controls unless the lab specifically requires those privileges.

## 11. Lab addresses changed between screenshots

The environment was redeployed and readdressed during different modules. Use aliases, DHCP reservations, and an addressing table so the documentation remains valid when IP addresses change.
