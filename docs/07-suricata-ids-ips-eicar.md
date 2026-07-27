# 7. Suricata IDS/IPS and EICAR Testing

## Objective

Configure OPNsense's Suricata-based intrusion detection/prevention capability, generate a benign signature match, and compare alert-only behavior with inline drop behavior.

## Preparation

1. Snapshot the OPNsense VM.
2. Confirm the WAN can reach the rule-update source.
3. Disable hardware checksum, TCP segmentation, and large receive offloading when required by the virtual NIC/IPS mode.
4. Use only a controlled lab server and the standard benign EICAR test file.

## IDS/IPS settings

1. Open **Services → Intrusion Detection → Administration/Settings**. Menu names vary by OPNsense version.
2. Enable intrusion detection.
3. Select both WAN and LAN for the lab exercise.
4. Enable promiscuous mode when required by the virtual lab design.
5. Enable **IPS mode** for the prevention test.
6. Save and apply.

## Rule setup

1. Open the rule-download or rules tab.
2. Update the available rules.
3. Locate the OPNsense test/EICAR rule.
4. First configure the rule for **Alert**.
5. Apply and restart the service if required.

## Alert test

1. Host the benign EICAR test file on a controlled HTTP server.
2. From the opposite side of the firewall, request the file through OPNsense.
3. Open the IDS Alerts page.
4. Confirm the rule signature, source, destination, interface, timestamp, and action `alert`.

## Drop test

1. Change the matching policy to `drop` or enable the IPS drop action for the rule.
2. Apply/reload the rules.
3. Repeat the same controlled request.
4. Confirm the transfer fails or is interrupted.
5. Confirm the alert now shows the drop action.

## Evidence checklist for a replacement capture

Capture four screenshots:

1. IDS settings showing enabled, IPS mode, promiscuous mode, and selected WAN/LAN interfaces.
2. Rules view showing the test rule enabled in alert mode.
3. Alerts view showing the EICAR match with action `alert`.
4. Alerts view plus failed client request showing action `drop`.

## Evidence status

The original EICAR screenshots were not present in the supplied ZIP. The sequence is documented from the completed lab workflow, but new screenshots should be captured before the repository describes the result as independently evidenced.
