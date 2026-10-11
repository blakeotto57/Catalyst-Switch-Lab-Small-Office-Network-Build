# Hardware and environment limitations

I documented these instead of working around them silently. Each one lists what I'd do in production.

| Limitation | Effect in this lab | Production fix |
|---|---|---|
| R1 (WS-C3560-48TS) has no NAT, and the ISP gateway (Xfinity) has no static routes | Lab hosts cannot reach the internet — return traffic has no path back to 10.10.0.0/16. R1 itself can. (Requirement 10 partially met.) | Edge router or firewall performing NAT between the core and the ISP |
| SW3 (WS-C3548-XL) has no LACP | Static EtherChannel to R1 instead of LACP | Replace with a switch that supports LACP |
| SW3 runs 802.1D only (no Rapid PVST+) | Cross-link failover took ~40 s instead of ~1–2 s | Rapid PVST+ on every switch |
| SW3 has no DHCP snooping | Snooping only on SW2 (SW3 has no VLAN 10 users) | Snooping on every access switch |
| SW3 has no SSH | Remote access to SW3 disabled; console only | SSHv2-capable image |
| SW3 cannot remove VLAN 1 / 1002–1005 from trunks | Extra VLANs listed on SW3's trunks (inert, since R1 and SW2 don't allow them) | Modern IOS |
| SW3 stores local passwords as type 7 | Weak, reversible encoding | `username … secret` (type 8/9) |
| Older IOS SSH uses legacy algorithms (diffie-hellman-group1-sha1, ssh-rsa, hmac-sha1) | Modern clients need per-connection overrides | Current IOS supporting modern key exchange |
| Enable secrets are MD5 (type 5) | Weaker hashing than current options | Type 8/9 secrets |
| No NTP | Device clocks drift; the syslog server's own timestamps are the reliable ones | NTP on every device |
| Test laptop and server VM shared one USB network adapter | Most failure tests pinged SW3's management IP (same path through R1) instead of the server | Separate test hosts |
