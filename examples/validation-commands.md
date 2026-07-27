# Validation Command Reference

Replace bracketed values with private lab addresses.

## Windows

```powershell
ipconfig /all
route print
nslookup www.dslos.com <OPNSENSE_LAN_IP>
Test-NetConnection <OPNSENSE_WAN_IP> -Port 2456
curl.exe -I http://<OPNSENSE_WAN_IP>:2456/
```

## Linux client

```bash
ip addr
ip route
cat /etc/resolv.conf
nslookup www.dslos.com <OPNSENSE_LAN_IP>
curl -I http://<OPNSENSE_WAN_IP>:2456/
```

## Service checks

```bash
ss -lntup
curl -I http://127.0.0.1/
```

## Capture checklist

For each test, record:

1. Configuration screen
2. Client command or browser result
3. OPNsense Live View entry
4. State-table entry where applicable
5. Timestamp and expected outcome
