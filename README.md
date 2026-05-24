# 🖧 Lab 14.1.4 — Troubleshoot Physical Connectivity

> **CompTIA A+ Core 1 | CertMaster Perform v15 | Per Scholas AI-Enabled IT Support — NCR**

![Score](https://img.shields.io/badge/Score-100%25-brightgreen) ![Layer](https://img.shields.io/badge/OSI%20Layer-Physical%20(L1)-blue) ![Status](https://img.shields.io/badge/Status-Complete-success) ![Program](https://img.shields.io/badge/Per%20Scholas-NCR-orange)

---

## 📋 Scenario

A corporate network technician receives a complaint:

> *"The employee in Office 1 says they can't communicate with the computer in Office 2."*

My job: **diagnose the fault, fix it using available spare parts, and confirm the repair.**

### 🗺️ Network Layout

| Location | Computer | IP Address |
|:---|:---|:---|
| Networking Closet | CorpServer | `192.168.0.10` |
| Office 1 | Office1 | `192.168.0.30` |
| Office 2 | Office2 | `192.168.0.31` |
| IT Administration | ITAdmin | `192.168.0.33` |
| Executive Office | Exec | `192.168.0.34` |

> All devices are on the same subnet `192.168.0.0/24` — no routing required.  
> A failure between two machines on the same subnet = **physical layer fault**.

---

## 🔍 Troubleshooting Methodology

### Step 1 — Understand the Environment

Reviewed the Floor 1 network map to identify all rooms, devices, and the central Networking Closet where the switch and patch panel are housed.

> 💭 **My thinking:** Before touching anything, I wanted to understand the full layout. Knowing where every device lived and how they connected to the central closet gave me a mental map of where the fault *could* be. You can't troubleshoot what you haven't visualized first.

---

### Step 2 — Inspect Office 1 *(the reporting machine)*

- ✅ Cat6a RJ45 ethernet cable — seated and connected
- ✅ Green blinking activity light on NIC — Layer 1 functional
- ✅ Power cables confirmed

> 💭 **My thinking:** The complaint came *from* Office 1, so I started here — but my goal was to **rule it out**, not find the problem. A green blinking NIC light means the network card is detecting a live signal. If Layer 1 is working on this end, the fault has to be somewhere further down the line.

**✅ Conclusion: Office 1 is not the source of the fault.**

---

### Step 3 — Inspect Office 2 *(the unreachable machine)*

- ⚠️ Laptop spec: `10/100BaseTX Ethernet` — wired only, no Wi-Fi
- ⚠️ Status showed "Connected" — but screen was black and wall panel had a missing cable
- ❌ Required Action revealed:
  In the Networking Closet, replace the patch cable for Office 2
> 💭 **My thinking:** This is where it got interesting. The laptop said "Connected" — so my first instinct was to assume it was fine. But I've learned that *connected* just means the port recognized something. It doesn't mean data is flowing. The black screen and the wired-only spec told me to look harder. When I saw the wall panel, I knew the fault wasn't in this room — it was upstream at the closet.

**✅ Conclusion: Fault is in the Networking Closet, not Office 2.**

---

### Step 4 — Inspect the Networking Closet

Equipment identified in rack:
- **Cisco SG300-28P** — 28-port gigabit PoE managed switch
- **24-port RJ45 patch panel**
- CorpServer + CorpData (both operational)

**Finding:**

| Item | Status |
|:---|:---|
| Cat6a Cable — Office 2 patch port | ❌ **BROKEN** |
| Physical appearance | Seated in both ports (looked fine) |
| Actual signal | Not passing data |

> 💭 **My thinking:** This is the trickiest kind of physical fault — a cable that *looks* fine but isn't. It was plugged in on both ends, so nothing looked wrong from the outside. This is exactly why `ping` matters: a broken cable will fail a ping even when the port light says connected. The "BROKEN" label on the selected item confirmed the root cause.

---

### Step 5 — Replace the Faulty Cable

| | Old Cable | New Cable |
|:---|:---|:---|
| Type | Cat6a (BROKEN) | Cat5e (known good spare) |
| Max Speed | 10 Gbps | 1 Gbps |
| Sufficient for office LAN? | N/A — broken | ✅ Yes |
| End 1 | Patch Panel Port 4 (Office 2) | Patch Panel Port 4 (Office 2) |
| End 2 | Switch port "Off 2" | Switch port "Off 2" |

> 💭 **My thinking:** I had to make a judgment call — only Cat5e was available, not Cat6a. Cat5e maxes at 1 Gbps, which is more than enough for standard office traffic. Using a known good spare of a lower category is acceptable as a temporary fix. In a real job I'd document this and flag it for a like-for-like Cat6a replacement. For port selection: I used the **switch faceplate labels** to identify "Off 2" as the correct switch port, then matched it to the corresponding patch panel port below it.

---

### Step 6 — Confirm the Fix with Ping

Ran from Office 1 workstation via **Windows PowerShell:**

```powershell
PS C:\Users\Administrator> ping 192.168.0.31
```
Pinging 192.168.0.31 with 32 bytes of data:
Reply from 192.168.0.31: bytes=32 time<1ms TTL=128
Reply from 192.168.0.31: bytes=32 time<1ms TTL=128
Reply from 192.168.0.31: bytes=32 time<1ms TTL=128
Reply from 192.168.0.31: bytes=32 time<1ms TTL=128
Ping statistics for 192.168.0.31:
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
Approximate round trip times in milli-seconds:
Minimum = 0ms, Maximum = 0ms, Average = 0ms

> 💭 **My thinking:** I never close a ticket without verifying the fix. Replacing the cable *should* work — but "should" isn't good enough. Pinging Office 2's IP from Office 1 is the fastest way to confirm end-to-end connectivity is restored. Four packets sent, four received, zero loss, sub-1ms response — that's a clean bill of health. **Fix confirmed.**

---

## 🎯 Root Cause Summary

| | |
|:---|:---|
| **Fault** | Broken Cat6a patch cable in Networking Closet |
| **Location** | Between Office 2's patch panel port and switch port "Off 2" |
| **Why it was hard to spot** | Cable was physically seated — looked connected from the outside |
| **Fix** | Replaced with Cat5e RJ45 known good spare |
| **Verified by** | `ping 192.168.0.31` — 4/4 packets, 0% loss |
| **OSI Layer** | Layer 1 — Physical |

---

## 🧠 Key Concepts Demonstrated

| Concept | Applied |
|:---|:---|
| Physical layer troubleshooting | Isolate → Identify → Fix → Verify |
| Patch panel + switch port relationships | Matched patch panel Port 4 to switch "Off 2" |
| NIC activity light interpretation | Green blinking = Layer 1 live |
| `ping` for connectivity verification | Confirmed 0% packet loss post-fix |
| Known good spare substitution | Cat5e for Cat6a — documented difference |
| "Connected" ≠ working | Broken cable appeared seated but passed no data |
| Switch port label documentation | Used faceplate labels to identify correct port |

---

## 📸 Screenshot Log

| # | File | What it shows |
|:---|:---|:---|
| 01 | `01-lab-instructions.png` | Scenario, IP table, task list |
| 02 | `02-floor-plan-overview.png` | Full corporate floor layout |
| 03 | `03-office1-workstation.png` | Office 1 workstation and wall panel |
| 04 | `04-office1-rear-panel-connections.png` | Cat6a connected, green NIC light |
| 05 | `05-office2-devices.png` | Office 2 workstation and laptop |
| 06 | `06-office2-check-answers-required-actions.png` | Required action revealed |
| 07 | `07-networking-closet-overview.png` | Full rack view |
| 08 | `08-networking-closet-broken-cat6a-cable.png` | Root cause identified |
| 09 | `09-networking-closet-patch-panel-zoomed.png` | Switch port labels visible |
| 10 | `10-networking-closet-cable-replaced-connected.png` | New cable installed |
| 11 | `11-office1-ping-success-192-168-0-31.png` | Ping success — 0% loss |
| 12 | `12-lab-score-100-percent.png` | Final score: 100% |

---

## 🛠️ Tools & Technologies

`Cisco SG300-28P` &nbsp;|&nbsp; `24-port RJ45 Patch Panel` &nbsp;|&nbsp; `Cat6a / Cat5e RJ45` &nbsp;|&nbsp; `Windows PowerShell` &nbsp;|&nbsp; `ping` &nbsp;|&nbsp; `CompTIA CertMaster Perform`

---

## 📚 CompTIA A+ Exam Objectives Covered

**220-1101 Core 1**
- `2.2` — Compare and contrast common networking hardware
- `2.8` — Given a scenario, use networking tools (`ping`)
- `5.7` — Troubleshoot problems with wired and wireless networks

---

*Part of an ongoing IT support portfolio built during the Per Scholas AI-Enabled IT Support — National Capital Region program.*
