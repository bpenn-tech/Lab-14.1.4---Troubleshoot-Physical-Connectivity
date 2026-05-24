# Lab-14.1.4---Troubleshoot-Physical-Connectivity
IT support simulation: traced an office network outage to a broken patch cable in a networking closet and restored connectivity with 100% lab score.
Platform: CompTIA A+ Core 1 CertMaster Perform (v15)
Completed by: Bianca Penn
Program: Per Scholas AI-Enabled IT Support — National Capital Region
Score: 100%

Scenario
A network technician for a small corporate network receives a complaint: the user in Office 1 cannot communicate with the computer in Office 2. The task is to diagnose the fault, fix it using available spare parts, and confirm the repair.
Network Layout
Location
Computer
IP Address
Networking Closet
CorpServer
192.168.0.10
Office 1
Office1
192.168.0.30
Office 2
Office2
192.168.0.31
IT Administration
ITAdmin
192.168.0.33
Executive Office
Exec
192.168.0.34


All devices share the same subnet (192.168.0.0/24), meaning communication between them is direct — no routing required. A connectivity failure between two machines on the same subnet points to a physical layer fault.


Troubleshooting Methodology
Step 1 — Understand the environment
Reviewed the Floor 1 network map to identify all rooms, devices, and the central Networking Closet where the switch and patch panel are housed.

💭 My thinking: Before touching anything, I wanted to understand the full layout. Knowing where every device lived and how they connected to the central closet gave me a mental map of where the fault could be. You can't troubleshoot what you haven't visualized first.


Step 2 — Inspect Office 1 (the reporting machine)
Checked all physical cable connections on the workstation
Confirmed the Cat6a RJ45 ethernet cable was seated and connected
Observed a green blinking activity light on the NIC — confirming Layer 1 was functional on this end

💭 My thinking: The complaint came from Office 1, so I started here — but my goal wasn't to find the problem, it was to rule it out. A green blinking NIC light means the network card is detecting a live signal. If Layer 1 is working here, the fault has to be somewhere further down the line.

Conclusion: Office 1 is not the source of the fault.


Step 3 — Inspect Office 2 (the unreachable machine)
Identified a laptop (10/100BaseTX Ethernet) as the Office 2 device
The laptop status showed "Connected" in the sim — but the screen was black/off
Wall panel inspection revealed a missing or disconnected cable on the third port
Used Check Answers to surface the Required Action:

❌ In the Networking Closet, replace the patch cable for Office 2

💭 My thinking: This is where it got interesting. The laptop said "Connected" — so my first instinct was to assume it was fine. But I've learned that "connected" just means the port recognized something. It doesn't mean data is flowing. The black screen and the 10/100BaseTX spec (wired only, no Wi-Fi) told me to look harder. When I saw the wall panel, I knew the fault wasn't in this room either — it was upstream, at the closet.


Step 4 — Inspect the Networking Closet
Located the rack containing the Cisco SG300-28P 28-port gigabit PoE managed switch and a 24-port patch panel
Identified a Cat6a Cable, BROKEN in the patch panel port assigned to Office 2
The cable showed as "Used in workspace / Connected" — meaning it was physically seated but internally damaged

💭 My thinking: This is the trickiest kind of physical fault — a cable that looks fine but isn't. It was plugged in on both ends, so nothing looked wrong from the outside. This is exactly why ping matters: a broken cable will fail a ping even when the port light says connected. Seeing "BROKEN" on the selected item label confirmed what the ping test would have caught anyway. I had found the root cause.


Step 5 — Replace the faulty cable
Selected a Cat5e RJ45 cable from the available spare parts shelf
Connected End 1 to the patch panel port for Office 2 (Port 4)
Connected End 2 to the switch port labeled "Off 2" on the Cisco switch faceplate
Confirmed both connectors showed as fully connected in the workspace

💭 My thinking: I had to make a judgment call here — only Cat5e was available, not Cat6a. Cat5e maxes out at 1 Gbps, which is more than enough for standard office traffic. Using a known good spare of a lower category is acceptable as a temporary fix. In a real job I'd document this and flag it for a like-for-like Cat6a replacement. For the port selection: I used the switch faceplate labels to identify "Off 2" as the correct switch port, and matched it to the corresponding patch panel port below it. That's how a patch cable works — it bridges the patch panel (wall jack termination) to the switch (network access).

Why Cat5e instead of Cat6a?
Cat5e supports up to 1 Gbps — sufficient for standard office LAN traffic. Using a known good spare of a lower category is accepted practice when a like-for-like replacement is unavailable. A Cat6a replacement is recommended as the permanent fix.


Step 6 — Confirm the fix with ping
Ran ping 192.168.0.31 from the Office 1 workstation (PowerShell):

Pinging 192.168.0.31 with 32 bytes of data:

Reply from 192.168.0.31: bytes=32 time<1ms TTL=128

Reply from 192.168.0.31: bytes=32 time<1ms TTL=128

Reply from 192.168.0.31: bytes=32 time<1ms TTL=128

Reply from 192.168.0.31: bytes=32 time<1ms TTL=128

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)

Minimum = 0ms, Maximum = 0ms, Average = 0ms

✅ Full connectivity restored. Zero packet loss.

💭 My thinking: I never close a ticket without verifying the fix. Replacing the cable should work — but "should" isn't good enough. Pinging Office 2's IP from Office 1 is the fastest way to confirm end-to-end connectivity is restored. Four packets sent, four received, zero loss, sub-1ms response time — that's a clean bill of health. Fix confirmed.


Root Cause
A broken Cat6a patch cable in the Networking Closet disconnected Office 2's patch panel port from the switch. The cable was physically seated in both ports, making it invisible to casual inspection — only systematic troubleshooting and ping testing revealed the fault.

OSI Layer affected: Layer 1 — Physical


Key Concepts Demonstrated
Physical layer troubleshooting methodology (isolate → identify → fix → verify)
Patch panel and switch port relationships in a structured cabling system
Reading switch port labels and network documentation to identify correct port assignments
NIC activity light interpretation
Use of ping to confirm Layer 1 and Layer 3 connectivity
Known good spare substitution (Cat5e for Cat6a)
Difference between "connected" status and actual signal integrity


Screenshots
#
File
Description
01
01-lab-instructions.png
Lab scenario, IP table, and task list
02
02-floor-plan-overview.png
Corporate floor layout — all rooms and devices
03
03-office1-workstation.png
Office 1 workstation and wall panel
04
04-office1-rear-panel-connections.png
Cat6a confirmed connected, green NIC activity light
05
05-office2-devices.png
Office 2 workstation and laptop
06
06-office2-check-answers-required-actions.png
Required action revealed — patch cable fault
07
07-networking-closet-overview.png
Full rack: switch, patch panel, servers
08
08-networking-closet-broken-cat6a-cable.png
Broken Cat6a cable identified
09
09-networking-closet-patch-panel-zoomed.png
Zoomed rack showing switch port labels
10
10-networking-closet-cable-replaced-connected.png
New Cat5e cable installed and confirmed
11
11-office1-ping-success-192-168-0-31.png
Ping success — 4/4 packets, 0% loss
12
12-lab-score-100-percent.png
Final score: 100%



Tools & Technologies
CompTIA A+ CertMaster Perform simulation environment
Cisco SG300-28P 28-port gigabit PoE managed switch
24-port RJ45 patch panel
Cat6a / Cat5e ethernet cabling (RJ45)
Windows PowerShell — ping command
Structured cabling concepts: patch cables, wall jacks, patch panels


CompTIA A+ Exam Objectives Covered
220-1101 (Core 1) — Domain 2: Networking

2.2 Compare and contrast common networking hardware
2.8 Given a scenario, use networking tools — ping

220-1101 (Core 1) — Domain 5: Hardware and Network Troubleshooting

5.7 Given a scenario, troubleshoot problems with wired and wireless networks



Part of an ongoing IT support portfolio built during the Per Scholas AI-Enabled IT Support program.
