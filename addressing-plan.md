# Addressing plan

## VLANs

| VLAN | Name | Subnet | Gateway (R1 SVI) | Purpose |
|---|---|---|---|---|
| 10 | USERS | 10.10.10.0/24 | 10.10.10.1 | User devices on either access switch; DHCP from R1 (.1–.20 excluded, DNS 8.8.8.8) |
| 20 | SERVERS | 10.10.20.0/24 | 10.10.20.1 | Ubuntu server SRV1, static 10.10.20.10 (syslog + TFTP) |
| 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 | Switch management: R1 .1, SW2 .2, SW3 .3 |
| 900 | NATIVE | — | — | Trunk native VLAN; carries no hosts |
| 999 | UNUSED | — | — | Every unused port, shut down |
| — | Uplink | 10.0.0.0/24 (home network) | Home router 10.0.0.1 | R1 Fa0/5 routed port, 10.0.0.250/24 |

## Devices

| Device | Model | Role | Management IP | Default route / gateway |
|---|---|---|---|---|
| R1 | Cisco WS-C3560-48TS | Layer 3 core, STP root, all gateways, DHCP | 10.10.99.1 | `ip route 0.0.0.0 0.0.0.0 10.0.0.1` |
| SW2 | Cisco Catalyst 3560G-48 (operated as Layer 2) | Access switch, secondary root | 10.10.99.2 | `ip default-gateway 10.10.99.1` |
| SW3 | Cisco WS-C3548-XL | Access switch | 10.10.99.3 | `ip default-gateway 10.10.99.1` |
| SRV1 | Ubuntu (VirtualBox VM, bridged NIC) | Syslog + TFTP server | 10.10.20.10 | 10.10.20.1 |
| Test laptop | Windows | Test client | DHCP (10.10.10.21) | 10.10.10.1 |

## Links

| Link | Ends | Type |
|---|---|---|
| Po1 | R1 Fa0/1–2 ↔ SW2 Gi0/1–2 | LACP EtherChannel trunk |
| Po2 | R1 Fa0/3–4 ↔ SW3 Fa0/1–2 | Static EtherChannel trunk (`mode on` / `port group`) |
| Cross-link | SW2 Gi0/4 ↔ SW3 Fa0/4 | Trunk; blocked by STP on SW3's side |
| Uplink | R1 Fa0/5 ↔ home router | Routed port, ACL `LAB_BLOCK_HOME` outbound |
| Laptop | SW2 Gi0/3 | Access VLAN 10, PortFast, BPDU Guard, port security |
| Server | SW3 Fa0/3 | Access VLAN 20, PortFast |

All trunks: allowed VLANs 10, 20, 99, 900 · native VLAN 900 · `switchport nonegotiate`.
