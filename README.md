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

4. After I completed the lab and was able to meet all of the requirements in "Three-Switch Office Network Lab Project Brief.docx" I created a conclusion document based on my final thoughts and the overall process of starting, doing, and completing this lab, title conclusion.pdf



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

All 9 passed. Highlights: an EtherChannel link failure lost no pings; losing both of SW2's uplinks failed over across the cross-link in ~40 s (SW3 runs 802.1D); moving the root bridge moved the blocked port exactly as path costs predicted. 

## More

- [Design decisions](design-decisions.md)
- [Troubleshooting log](troubleshooting.md)
- [Hardware limitations](limitations.md)
- Sanitized configs: `R1-sanitized.txt`, `SW2-sanitized.txt`, `SW3-sanitized.txt`
