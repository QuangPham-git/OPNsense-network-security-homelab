# 5. Firewall Policy and Validation

## Objective

Create destination-specific policy controls and prove the result using both client behavior and OPNsense telemetry.

## Rule design

Example test rules:

1. Pass LAN TCP/80 to an allowed destination alias.
2. Block LAN TCP/80 to a denied destination alias.
3. Place specific rules above broad allow rules because OPNsense evaluates interface rules from top to bottom on a first-match basis.
4. Enable logging on the explicit allow and block rules.

## Procedure

1. Create aliases for the allowed and blocked destinations.
2. Open **Firewall → Rules → LAN**.
3. Add the block rule and pass rule with meaningful descriptions.
4. Move both above the default broad LAN allow rule.
5. Apply changes before testing.
6. Test plain HTTP endpoints so a legacy browser TLS limitation is not mistaken for a firewall block.
7. Open **Firewall → Log Files → Live View** and filter by the client address or rule description.
8. Open **Firewall → Diagnostics → States** to inspect the resulting state entries.

## Evidence and limitation

![HTTP policy validation](../screenshots/opnsense/08-http-policy-validation.jpg)

The archived screenshot shows the rule definitions and browser results, but it also contains an unapplied-changes banner and a legacy-browser security-protocol warning. That image alone is not conclusive proof of the deny rule. A clean retest must apply the rules, put the deny above the broad pass rule, and confirm a red/block record with the expected rule label in Live View.

![Firewall live view](../screenshots/opnsense/09-firewall-live-view.jpg)

![State detail](../screenshots/opnsense/10-state-detail.jpg)

## Strong evidence checklist

- [ ] Rule is enabled and applied.
- [ ] Specific rule is above the default LAN allow rule.
- [ ] Client successfully reaches the allowed HTTP target.
- [ ] Client fails to reach the blocked HTTP target.
- [ ] Live View shows a matching block action and rule label.
- [ ] State table shows or omits the connection as expected.
