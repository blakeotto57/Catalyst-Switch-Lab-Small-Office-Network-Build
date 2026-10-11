# Troubleshooting log

Problems I hit during the build and testing, how I diagnosed them, and what I changed.

| # | Symptom | Diagnosis | Fix | Lesson |
|---|---|---|---|---|
| 1 | SRV1 reported "Destination Host Unreachable" even for its own gateway | `ip neigh` showed the gateway as FAILED (no ARP reply). SW3 learned the host PC's MAC on Fa0/3 but never the VM's, so the VirtualBox bridge to the USB NIC had stopped passing traffic | Fully powered off the VM, replugged the USB adapter, cold-booted | Check each layer outward from the host; the MAC table tells you whether frames reach the switch |
| 2 | ACL had no effect | Applied `out` on an SVI, which filters traffic *into* that VLAN — destinations were never 10.0.0.0/24 | Applied outbound on the routed uplink Fa0/5 | ACL direction is from the router's point of view |
| 3 | Lab hosts couldn't reach the internet | No return route on the ISP gateway, and the core switch has no NAT (`ip nat` unrecognized) | Documented as a limitation | Verify platform features before designing around them |
| 4 | Planned cross-link port on SW3 was already the server port; allowed-VLAN list had typos (90, 999) | Compared the plan to `show interfaces status` | Moved the cross-link to Fa0/4; allowed 10,20,99,900 | Check the live port map before configuring |
| 5 | R1 and SW2 reported `protocol ieee` / `pvst mode` | Rapid PVST+ had never been enabled | `spanning-tree mode rapid-pvst` | Verify the STP mode, not just the topology |
| 6 | SW2 elected itself root for VLAN 99 | R1's root priority covered only VLANs 10 and 20 | Set priorities for 10,20,99,900,999 on R1 and SW2 | Configure root priority for every VLAN |
| 7 | Cross-link vanished from spanning tree; SW3 logged `RECV_1Q_NON_TRUNK` and PVID inconsistencies | SW3 had reloaded with an unsaved config, so Fa0/4 reverted to an access port | Reapplied the trunk config and saved it | Save after every verified change |
| 8 | Remote `enable` returned "% No password set" | No enable secret was configured; console access had hidden it | Added `enable secret` on all devices | Test management the way an admin actually connects |
| 9 | Telnet to SW2 showed "Open" | `transport input ssh` only on vty 0–4; vty 5–15 still allowed Telnet | `line vty 0 15` with `transport input ssh` and `login local` | Lock down every vty line |
| 10 | HTTP/HTTPS management enabled | `ip http server` / `secure-server` on by default | Disabled both | Review defaults, not just what you configured |
| 11 | SSH failed: "no matching key exchange method" then "no matching MAC" | Older IOS only offers legacy algorithms | Enabled them on the client for that connection only | Production fix is upgrading IOS, not weakening clients |
| 12 | SW3 stored the local password in plain text | Old IOS `username … password 0` | `service password-encryption` | Review every config for credentials before publishing |
| 13 | Convergence time looked shorter than reality | Windows `ping -t` waits 4 s per timeout | Used `ping -t -w 1000` afterward | Know your measurement tool's timing |
| 14 | Port security shut down the laptop's port | The server VM shared the laptop's USB adapter, so a second MAC appeared on Gi0/3 | Kept the VM off while on SW2; used it deliberately for test 9 | Shared adapters look like multiple devices to the switch |
