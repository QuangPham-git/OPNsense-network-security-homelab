# Selective YouTube routing experiment

[Back to the project](../README.md)

**Status: partially implemented; end-to-end validation incomplete.**

This Phase 2 experiment aimed to send YouTube destinations through a VPN gateway while keeping other traffic on the ordinary WAN path. The intended final scope was all CLIENTS. The actual test scope remained one Kali container in the `YOUTUBE_VPN_CLIENTS` source alias.

The alias name describes the temporary test source group. It does not change the destination-selective objective into whole-device VPN routing.

## Implemented components and observed results

| Component | Supported implementation or observation | What it does not establish |
| --- | --- | --- |
| CT 107 | Linux VPN gateway container on SERVERS; public example address `10.42.20.40/24` | Reliable VPN exit service |
| OpenVPN | `tun0` appeared and initialization completed during manual testing | Continued tunnel usability after that moment |
| Manual egress test | Recovered summary reports a tunnel-forced exit differing from normal WAN | Successful selective routing from CLIENTS |
| Forwarding and NAT | Forwarding and masquerade through `tun0` recorded in the recovered configuration summary | Persistence or successful packet flow across the entire path |
| Policy table 200 | Source-specific selection for the Kali test client; tunnel default used during testing | A validated all-client policy |
| Blackhole default | `blackhole default metric 32767` observed in table 200 | A proven leak-free failure test across every route and protocol |
| DNS test | Query to the OPNsense Dnsmasq test listener on port 53053 returned a YouTube A record | Automatic destination-table population or traffic steering |
| Kali client | CLIENTS test container; link-state troubleshooting performed | Final persistent client routing health |

The gateway container's own ordinary traffic was deliberately kept on the normal path so management and future provider retrieval would not depend on the experimental tunnel. The complete persistent routing and firewall export was not recovered.

## Where validation stopped

The manually selected VPN endpoint later stopped providing usable connectivity. Tunnel-forced requests timed out and reconnection attempts did not establish a final working exit. Later route-table output, after removal of the tunnel default during troubleshooting, showed only the blackhole default in table 200.

For traffic actually selected into that table, the recorded state provided no usable default forwarding path. The presence of the blackhole route is useful implementation evidence, but it is not a substitute for a full leak test or proof that every relevant packet selected that table.

A running OpenVPN process and a `tun0` interface did not settle the question. The useful test was whether traffic could leave through the tunnel and receive a response.

The tunnel had a name, an interface, and confidence. The exit connectivity was less convincing.

## DNS and OPNsense integration remain unfinished

The intended design used Dnsmasq and a changing set of YouTube domains to populate destination addresses for policy routing. One successful DNS answer proves that the query path worked for that test. It does not prove that an OPNsense destination alias was populated, refreshed, selected by the correct firewall rule, or used by a client connection.

No completed evidence establishes all of the following together: YouTube traffic using the VPN, unrelated traffic using the normal WAN, appropriate behavior when the VPN fails, and correct behavior after restart.

The test scope was not expanded from Kali to the entire CLIENTS subnet. The known-working Pi-hole baseline is therefore retained in the main network documentation, with this alternate DNS/routing path documented as an experiment.

## Automation boundary

Playwright-based provider retrieval and credential rotation were designed for CT 107. A runnable, successfully tested automation was not established. No scraper, scheduler, refresh script, provider profile, or credential bundle is included in this publication.

A proposal to automate the next step is not an implemented automation. A DNS answer is not a routing certificate. These boundaries keep a useful experiment from becoming an invented finished feature.

## Recorded checkpoint

The relevant routing work is recovered through 8 October. CT 107 and the Kali container were still listed as running in the 12 October host inventory. That later inventory does not supersede the unresolved VPN exit and selective-routing results.

Provider endpoints, public exit addresses, keys, certificates, and fetched configuration contents are omitted. Exact domain-policy contents and persistent rule definitions are not reconstructed where the final configuration is missing.
