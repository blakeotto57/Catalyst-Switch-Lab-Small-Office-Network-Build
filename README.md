# Catalyst-Switch-Lab-Small-Office-Network-Build
I bought 3 Cisco Catalyst switches to gain experience with hands-on networking experience. I know the CLI from Cisco Packet tracer and have done numerous simulate labs via that program but I wanted to gain as much real-world experince and practice as I can to get a better feel of real-world applications of these networking concepts.
The hardware/resources I used:
- 1 Cisco Catalyst 2560 Series (Layer 2 Switch), (SW2), Version:
- 1 Cisco Catalyst 3500 Series XL (Layer 2 Switch), (SW3), Version:
- 1 Cisco Catalyst 3560G Series (Layer 3 Switch), (R1), Version: 
- Numerous ethernet cables
- Claude/Google to troubleshoot commands


Order of Operations:
1. I asked Claude to generate me a list of requirements for this Small Office Network Build and the attached link is what it generated for me to do. It covers Networking concepts like: VLANs, 802.1Q trunking, LACP EtherChannel, Rapid PVST+, SSH-only management, DHCP, syslog and config backups, and inter-VLAN routing.
The file in the repository titled "Three-Switch Office Network Lab Project Brief.docx" is the guide Claude generated for me that I used to follow my procedure and completed requirements.

2. I made a copy of the guide Claude created me called "Annotated Three-Switch Office Network Lab Project Brief.docx" where I made comments following the document of the commands I initially thought I would need to do and how they would accomplish the requirements the lab gave me. I did not test these commands but I did use Cisco Packet tracer alongside the process of annotating the document to just help me remember the syntax of certain commands to more accurately annotate commands.

3. After I completed my annotated Claude guide. I created a Word document for taking photos and writing my thought process while completing each steps for documentation's sake titled "Documented Three-Switch Office Network Lab Project Brief.docx" which is a copy of "Annotated Three-Switch Office Network Lab Project Brief.docx" but I document with photos and thoughts (comments in red text) after attempting each step (the commented parts of text) I initially thought would satisfy the requirements for the lab. The final full document of my process and comments and overall messy process can be found at this link: https://docs.google.com/document/d/1s2t-YMH0AEi6pULD4k1bQ0-tn5BNO2qe/edit?usp=sharing&ouid=106332296600475574405&rtpof=true&sd=true
Note that this document isn't in this repository fully but I prompted Claude to analyze the document and section off parts of it to be more easily digestiable for end users, (i.e. Requirements-Verification.pdf, Validation-and-Failure-Testing.pdf, etc)

4. After finishing the lab, I wrote a conclusion (conclusion.md) with my final thoughts on the process. Not every requirement was fully met: several were limited by the hardware, and requirement 10 was only partly met. The Requirements table below shows the status of each.



## Requirements

| # | Requirement | Status |
|---|---|---|
| 1 | Clean baseline | Met |
| 2 | Secure management | Met (SW3 has no SSH; remote access disabled) |
| 3 | Segmentation | Met |
| 4 | Redundant core uplinks | Met with limitation (SW3 uplink is static, no LACP) |
| 5 | Access cross-link | Met |
| 6 | Spanning tree | Met with limitation (SW3 is 802.1D only) |
| 7 | Centralized routing | Met |
| 8 | DHCP | Met |
| 9 | Logging and backups | Met |
| 10 | Internet access | Partially met (no NAT on core, no static routes on ISP gateway) |
| 11 | Access-layer security | Met on SW2; SW3 has no DHCP snooping |


## Failure tests

8 of 9 tests passed, and test 8 passed in part. The ACL blocked lab-to-home traffic (hit counters and log messages are in the PDF), but the "internet still works" half couldn't be tested because lab hosts have no path to the internet (see requirement 10). The test laptop and the server VM shared one network adapter, so most tests pinged SW3's management IP (10.10.99.3) instead of the server. That traffic takes the same path through R1.

Highlights: pulling one EtherChannel link caused no visible packet loss; losing both of SW2's uplinks failed over across the cross-link in about 36 seconds (9 timed-out pings at Windows' 4-second default), because SW3 runs 802.1D; moving the root bridge moved the blocked port exactly as the path costs predicted.

## More

- [Design decisions](design-decisions.md)
- [Troubleshooting log](troubleshooting.md)
- [Hardware limitations](limitations.md)
- Sanitized configs: `R1-sanitized.txt`, `SW2-sanitized.txt`, `SW3-sanitized.txt`
