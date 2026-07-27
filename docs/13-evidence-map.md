# 13. Evidence Map

This table separates what the current archive proves from what should be recaptured.

| Test | Included evidence | Confidence | Needed replacement evidence |
|---|---|---:|---|
| WAN configuration | `01-wan-status.jpg` | High | None |
| Interface assignment | `02-interface-assignments.jpg` | High | None |
| DHCP range | `03-dhcp-scope.jpg` | High | Add a client lease page if desired |
| DNS diagnostic | `04-opnsense-dns-lookup.jpg` | High | None |
| DNS host override and client lookup | `07-dns-override-validation.jpg` | High | None |
| NAT port forward | `06-nat-port-forward-validation.jpg` | High | Add Live View rule label for an even stronger proof |
| HTTP allow/block | `08-http-policy-validation.jpg` | Medium/low | Apply changes, correct order, use plain HTTP, capture Live View block |
| Firewall Live View | `09-firewall-live-view.jpg` | High | Capture filters and labels more tightly |
| State inspection | `10-state-detail.jpg` | High | None |
| FTP | `11-ftp-test-server.jpg`, `12-protocol-log-validation.jpg` | Medium | Add client session and rule in one frame |
| Telnet | Protocol/log screenshot | Low | Add controlled connection plus rule and log |
| Traffic shaping | No user-generated evidence in archive | Procedure only | Add pipe, queue, rule, and before/after throughput |
| IDS/IPS EICAR alert | None in ZIP | Procedure only | Settings, rule, alert action |
| IDS/IPS EICAR drop | None in ZIP | Procedure only | Failed request and drop action |
| DVWA CSRF | Three screenshots | High | Optionally capture request parameters in browser DevTools |
| DVWA reflected/stored/DOM XSS | General DVWA screenshots | Procedure only | Payload, security level, alert, source comparison |
| DVWA SQL injection | Troubleshooting screenshot | Procedure only | Manual comparison and sanitized sqlmap summary |

## Screenshot review

The original ZIP contained 73 screenshots. Every filename and its review disposition is listed in [`screenshot-inventory.csv`](screenshot-inventory.csv). Unrelated game/application captures and the separate Windows Event Viewer exercise were not copied into the public repository.

## Public-repository hygiene

The selected evidence was cropped to remove most browser tabs, bookmarks, and taskbars. Before publishing, perform one final visual review for usernames, email addresses, tokens, cookies, or unrelated company bookmarks.
