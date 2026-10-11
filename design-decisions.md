# Design decisions

The project brief defined the requirements. These are the implementation choices I made to meet them, and why.

**Collapsed core with a single Layer 3 switch.** R1 is the only device that routes. Keeping SW2 and SW3 at Layer 2 (management IP and default gateway only) means every inter-VLAN path and every gateway is in one place, which makes routing, DHCP and the ACL easy to reason about and troubleshoot.

**Spanning tree root placement.** R1 is root for every lab VLAN (priority 24576) and SW2 is the secondary root (28672) on VLANs 10, 20, 99, 900 and 999. Setting priorities explicitly for *every* VLAN matters: an early version only covered VLANs 10 and 20, and SW2 became root for the management VLAN. With SW2 as secondary, SW2's end of the cross-link forwards and SW3's end blocks, so the backup path is predictable.

**Native VLAN 900 and parking VLAN 999.** No hosts live in the native VLAN, so untagged traffic on a trunk can't land in a user or server network. Every unused port is in VLAN 999 and shut down, so plugging into a spare port gets you nothing.

**Trunks are static and pruned.** Every trunk (except for SW3's due to hardware limitations) uses `switchport mode trunk` with `nonegotiate` (no DTP) and allows only 10, 20, 99 and 900.

**LACP where possible, static where not.** SW2's uplink uses LACP (`mode active`) so mismatched bundles are detected. SW3's 3548 XL has no LACP, so its uplink is a static channel (`mode on` on R1, `port group` on SW3).

**ACL outbound on the routed uplink.** `LAB_BLOCK_HOME` denies each lab subnet to 10.0.0.0/24 with `log`, then permits everything else. Because R1 does no address translation, lab source addresses are still intact when packets leave Fa0/5, so one outbound ACL at the single exit toward the home network covers every VLAN. Per-subnet deny lines give per-VLAN hit counters for verification.

**DHCP snooping trust includes the cross-link.** SW2 trusts Po1 *and* the cross-link (Gi0/4). If SW2 loses both uplinks, R1's DHCP replies arrive over the cross-link; leaving it untrusted would break DHCP during exactly the failure the cross-link exists for. Rogue DHCP servers sit on edge ports, which stay untrusted.

**Option 82 insertion disabled.** R1 is the DHCP server directly (not a relay), and IOS rejects snooped requests that carry Option 82 with a zero gateway address, so `no ip dhcp snooping information option` keeps DHCP working.

**Edge port protection.** User ports use PortFast and BPDU Guard, and SW2's user port allows one sticky-learned MAC with violation mode shutdown.

**Management.** SSH version 2 only with local accounts on all 16 vty lines; HTTP/HTTPS management disabled. SW3 has no SSH support, so rather than allow Telnet I disabled remote access to it entirely (`transport input none`) and manage it from the console. All switches log to SRV1 and back up their configs to it over TFTP, sourced from VLAN 99.
