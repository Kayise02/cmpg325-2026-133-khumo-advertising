# CMPG325 Individual Network Project Portfolio

| | |
|---|---|
| **Student** | Shivuri, Nyeleti |
| **Student number** | 42718910 |
| **Project ID** | CMPG325-2026-133 |
| **Client ID** | CLI-133 |
| **Client** | Khumo Advertising Billboards (Vryburg) |
| **Industry** | Media |

---

## Project Overview

This repository is the portfolio of evidence for my CMPG325 individual network project. The brief assigns Khumo Advertising Billboards (Vryburg) as the client and sets the following requirements:

- A fixed addressing block
- A mandatory design constraint separating VoIP and data traffic
- A wireless security challenge (WPA2-PSK hardening)
- A resilience change request requiring a second internet connection

Where the brief did not give organisational detail, I made reasonable network engineering assumptions and documented them, in line with my lecturer's guidance.

---

## Repository Structure

| Folder | Contents |
|---|---|
| `01-client-requirements/` | Client requirements and documented assumptions |
| `02-topology/` | Physical and logical topology diagrams, including editable draw.io files |
| `03-ip-addressing/` | VLAN table, IP addressing plan, WAN links, and trunk configuration |
| `04-packet-tracer/` | Packet Tracer project file (`Khumo_Network.pkt`) |
| `05-configuration/` | Running configuration of every device and the wireless access point security settings |
| `06-testing/` | Testing evidence screenshots and a summary of all tests |
| `07-troubleshooting/` | Log of issues encountered and how they were resolved |
| `08-reflection/` | Final project reflection |
| `09-report/` | Full technical report in Markdown, with embedded figures |

---

## Project Status

### Milestone 1: Client Design Review (Complete)

Submitted the client requirements, documented assumptions, physical and logical topologies, VLAN design, IP addressing plan, and the initial GitHub repository.

### Milestone 2: Client Implementation Review (Complete)

Built, configured, and verified the following:

- All devices placed, labelled, and cabled according to the physical topology
- Core Layer 3 switch configured with VLANs, switch virtual interfaces (SVIs), trunking, and DHCP pools
- All three access switches configured with the correct trunk and access ports
- Edge routers configured with WAN uplinks to both internet service providers
- Dual ISP resilience tested in both directions, with live traffic confirmed during failover and after the primary link was restored
- Inter-VLAN routing verified with pings between all three departments
- All PCs and IP phones confirmed on DHCP with correct department-based addresses
- Voice VLAN separation confirmed at port level and in the DHCP binding table
- WPA2-PSK with AES encryption configured on the Creative wireless access point, with a successful connection and a rejected incorrect password both tested
- Running configuration exported for all six network devices
- Administrative passwords and login banners applied to all devices

### Milestone 3: Final Evaluation (Complete)

- Technical report finalised and submitted as `09-report/Technical_Report.md`
- All testing evidence consolidated in `06-testing/` with a summary file
- Troubleshooting log finalised in `07-troubleshooting/`
- Final Packet Tracer file verified after a fresh close and reopen
- Video demonstration recorded and linked below

**Not attempted:** the optional WPA2-Enterprise extension mentioned in the brief. It is documented as a future recommendation in the technical report.

---

## Design Summary

### Network Design

The network uses a three-tier hierarchical design:

- **Edge layer:** two independent ISP connections for resilience
- **Core layer:** one Layer 3 switch that handles all inter-VLAN routing
- **Access layer:** three switches, one for each department

This keeps departmental traffic separated down to the access layer and meets the dual ISP requirement. The topology diagrams are in `02-topology/`.

### VLAN Design

| VLAN ID | Name | Department |
|---|---|---|
| 10 | Admin-Data | Management and Admin |
| 20 | Creative-Data | Creative and Design |
| 30 | Production-Data | Production and Logistics |
| 40 | Voice (VoIP) | Management and Admin |
| 50 | Wireless | Creative and Design, and meeting rooms |
| 99 | Native/Mgmt | Network infrastructure |

The full justification for this design is in `03-ip-addressing/ip-addressing-plan.md`.

### IP Addressing

The assigned block, `192.168.56.0/24`, is divided into `/27` subnets across the six VLANs. The full table with gateways and usable ranges is in `03-ip-addressing/ip-addressing-plan.md`.

### Client Requirements and Assumptions

The requirements list and the assumptions I made where the brief was unclear are in `01-client-requirements/client-requirements.md`. They cover departments, user counts, wireless and VoIP placement, and the redundancy design.

---

## How to Open the Project

1. Install Cisco Packet Tracer 8.x.
2. Open `04-packet-tracer/Khumo_Network.pkt`.
3. Wait 60 seconds for spanning-tree convergence and DHCP leases.
4. All PCs will receive their addresses automatically.
5. Follow `06-testing/testing-summary.md` to reproduce the test results.

---

## Video Demonstration

[Watch the demonstration video](https://efundi.nwu.ac.za/access/content/group-user/1b4c43e2-0741-423b-a0bf-eeb2446e3735/42718910/KHUMO_PRESENTATION_N_SHIVURI.mp4)

---

## Technical Report

The full report is at [`09-report/Technical_Report.md`](09-report/Technical_Report.md). It covers the design rationale, configuration summary, test results, troubleshooting, limitations, and future work.

---

## Academic Integrity

This project is my own individual work, developed according to the CMPG325-2026-133 project brief. Any AI assistance used in preparing documentation or diagrams complied with the applicable NWU AI Policy. I remain responsible for the correctness, understanding, and academic integrity of everything I submit.
