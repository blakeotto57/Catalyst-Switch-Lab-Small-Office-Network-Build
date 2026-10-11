## Conclusion

This whole process took way longer than I anticipated. I wanted to take networking concepts I had learned, read about and practiced in Cisco Packet Tracer and apply them to real Cisco hardware. I had Claude draft a project based on the gear I bought off Facebook Marketplace. With the information I provided, it drafted a project guide for a collapsed-core office network on three Catalyst switches: a Layer 3 core handling all routing, gateways and DHCP, and two Layer 2 access switches with VLAN segmentation, EtherChannel uplinks, a redundant cross-link and controlled spanning tree roles. I then ran the nine failure tests it called for while pinging continuously, and eight passed, one partial (test 8), although the internet part of the ACL test couldn't be checked because of the hardware limitations below.

**What the testing taught me.**
The most valuable part of the project was finding problems I wouldn't have noticed if I had only checked that things "worked," such as:

- Remote `enable` failed because no enable secret was set. Console access had hidden it.
- Telnet was still open on vty lines 5–15, even though I had restricted 0–4 to SSH.
- The web management server was on by default.
- SW2 became root for the management VLAN because I had set R1's priority on only some VLANs.
- A switch reloaded with an unsaved config and silently lost the cross-link.
- My Ubuntu server VM lost its network connection when the VirtualBox bridge to my USB adapter stopped working.

I want to be honest and note that I relied heavily on Claude, not only to create the guide but also to help me troubleshoot and configure the different devices. I used Claude as a tool to create an environment where I could learn, understand why things went wrong, and see how physical devices communicate with each other. All of my testing and troubleshooting is in the logs. 

Going forward, I want to verify a network the way a user, an admin and an attacker would actually interact with it, and to save after every verified change. I also want to understand the capabilities of the equipment I'm using up front, so I can plan around them instead of running into a wall when a requirement turns out to be limited by the hardware.

**Working with real hardware.**
Setting up this old hardware was tedious and frustrating at times. It was hard to keep track of which device I was configuring at any given moment. SW3 couldn't meet some of the requirements because it has no LACP, no Rapid PVST+, no DHCP snooping and no SSH, so I used a static EtherChannel, disabled its remote access entirely, and measured the cost of legacy 802.1D directly: losing SW2's uplinks took about 36 seconds to fail over, where Rapid PVST+ would typically take a second or two. Lab hosts also couldn't reach the internet, because the core switch can't do NAT and my ISP gateway can't take a static route. In simulators like Cisco Packet Tracer, that's never really an issue. Everything is organized and clean. After this experience, I have a much better understanding of what working with real equipment is like.

**What I would do differently.**
Replace the 3548 XL with a switch that supports LACP, Rapid PVST+, SSH and DHCP snooping. Add an edge router performing NAT so lab hosts get internet access. Configure NTP from the start so every log timestamp is accurate. Use separate machines for the test client and the server. That's a big ask, and in a perfect world I would have all of those. But given my limitations, I believe I'm better for it, because struggling through the problems and finding workarounds made me a stronger troubleshooter and a better networker.

**Result.**
The project produced a working, documented network: sanitized configurations, verification evidence for most requirements, nine failure tests (eight passed, one in part) with measured results, and a troubleshooting log built from real problems. More importantly, it gave me hands-on experience diagnosing issues layer by layer on physical equipment, which is the skill I want to keep building in a network engineering role.
