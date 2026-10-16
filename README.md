# CMPG325 Individual Network Project Portfolio

**Student:** Shivuri, Nyeleti  
**Student number:** 42718910  
**Project ID:** CMPG325-2026-133  
**Client ID:** CLI-133  
**Client:** Khumo Advertising Billboards (Vryburg)  
**Industry:** Media

---

## Project Overview

This repository is the portfolio of evidence for my CMPG325 individual network project. The brief assigns Khumo Advertising Billboards (Vryburg) as the client and sets four main requirements:

- A fixed addressing block
- A mandatory design constraint separating VoIP and data traffic
- A wireless security challenge (WPA2-PSK hardening)
- A resilience change request requiring a second internet connection

Where the brief did not give organisational detail, I made reasonable network engineering assumptions and documented them, in line with my lecturer's guidance. These cover departments, user counts, wireless and VoIP placement, and the redundancy design, and are listed in [`client-requirements.md`](01-client-requirements/client-requirements.md).

### What I Built

I designed and built a secure, resilient network for Khumo Advertising Billboards in Cisco Packet Tracer. It uses a three-tier hierarchical design:

- **Edge layer:** two independent ISP connections, so the company stays online if one provider fails
- **Core layer:** one Layer 3 switch that handles all inter-VLAN routing
- **Access layer:** three switches, one for each department (Admin, Creative and Production)

This keeps departmental traffic separated down to the access layer and meets the dual ISP requirement. The physical and logical topology diagrams are in [`02-topology/`](02-topology/).

### VLAN Design

The network has six VLANs, including a dedicated voice VLAN so that VoIP traffic stays apart from data traffic.

| VLAN ID | Name | Department |
|---|---|---|
| 10 | Admin-Data | Management and Admin |
| 20 | Creative-Data | Creative and Design |
| 30 | Production-Data | Production and Logistics |
| 40 | Voice (VoIP) | Management and Admin |
| 50 | Wireless | Creative and Design, and meeting rooms |
| 99 | Native/Mgmt | Network infrastructure |

### IP Addressing

The assigned block, `192.168.56.0/24`, is divided into `/27` subnets across the six VLANs. The full table with gateways and usable ranges, and the justification for the VLAN design, are in [`ip-addressing-plan.md`](03-ip-addressing/ip-addressing-plan.md).

### Security and Testing

The Creative wireless network is protected with WPA2-PSK and AES encryption. Administrative passwords and login banners are applied to all devices. I tested the network from start to finish: DHCP addressing, inter-VLAN routing, voice VLAN separation, wireless security (including a rejected incorrect password), and failover between the two ISPs in both directions. The results are in [`testing-summary.md`](06-testing/testing-summary.md).

### Technical Report

The full report is [`Technical_Report.md`](09-report/Technical_Report.md). It covers:

- Design rationale
- Configuration summary
- Test results
- Troubleshooting
- Limitations
- Future work, including the optional WPA2-Enterprise extension, which I did not attempt

---

## Quick Links

- [Video demonstration](https://efundi.nwu.ac.za/access/content/group-user/1b4c43e2-0741-423b-a0bf-eeb2446e3735/42718910/KHUMO_PRESENTATION_N_SHIVURI.mp4)
- [Technical report](09-report/Technical_Report.md)
- [Packet Tracer project file (`Khumo_Network.pkt`)](04-packet-tracer/Khumo_Network.pkt)
- [Testing summary](06-testing/testing-summary.md)
- [IP addressing plan](03-ip-addressing/ip-addressing-plan.md)
- [Client requirements and assumptions](01-client-requirements/client-requirements.md)

---

## Repository Structure

- [`01-client-requirements/`](01-client-requirements/): client requirements and documented assumptions
- [`02-topology/`](02-topology/): physical and logical topology diagrams, including editable draw.io files
- [`03-ip-addressing/`](03-ip-addressing/): VLAN table, IP addressing plan, WAN links, and trunk configuration
- [`04-packet-tracer/`](04-packet-tracer/): Packet Tracer project file (`Khumo_Network.pkt`)
- [`05-configuration/`](05-configuration/): running configuration of every device and the wireless access point security settings
- [`06-testing/`](06-testing/): testing evidence screenshots and a summary of all tests
- [`07-troubleshooting/`](07-troubleshooting/): log of issues encountered and how they were resolved
- [`08-reflection/`](08-reflection/): final project reflection
- [`09-report/`](09-report/): full technical report in Markdown, with embedded figures

---

## Project Status

### Milestone 1: Client Design Review (Complete)

Submitted the following:

- Client requirements and documented assumptions: [`01-client-requirements/`](01-client-requirements/)
- Physical and logical topologies: [`02-topology/`](02-topology/)
- VLAN design and IP addressing plan: [`03-ip-addressing/`](03-ip-addressing/)
- The initial GitHub repository

### Milestone 2: Client Implementation Review (Complete)

Built, configured, and verified the following:

- All devices placed, labelled, and cabled according to the physical topology: [`04-packet-tracer/Khumo_Network.pkt`](04-packet-tracer/Khumo_Network.pkt)
- Core Layer 3 switch configured with VLANs, switch virtual interfaces (SVIs), trunking, and DHCP pools
- All three access switches configured with the correct trunk and access ports
- Edge routers configured with WAN uplinks to both internet service providers
- Dual ISP resilience tested in both directions, with live traffic confirmed during failover and after the primary link was restored
- Inter-VLAN routing verified with pings between all three departments
- All PCs and IP phones confirmed on DHCP with correct department-based addresses
- Voice VLAN separation confirmed at port level and in the DHCP binding table
- WPA2-PSK with AES encryption configured on the Creative wireless access point, with a successful connection and a rejected incorrect password both tested
- Running configuration exported for all six network devices: [`05-configuration/`](05-configuration/)
- Administrative passwords and login banners applied to all devices

Test evidence for all of the above is in [`06-testing/`](06-testing/).

### Milestone 3: Final Evaluation (Complete)

- Technical report finalised and submitted: [`09-report/Technical_Report.md`](09-report/Technical_Report.md)
- All testing evidence consolidated with a summary file: [`06-testing/`](06-testing/) and [`testing-summary.md`](06-testing/testing-summary.md)
- Troubleshooting log finalised: [`07-troubleshooting/`](07-troubleshooting/)
- Final reflection written: [`08-reflection/`](08-reflection/)
- Final Packet Tracer file verified after a fresh close and reopen: [`Khumo_Network.pkt`](04-packet-tracer/Khumo_Network.pkt)
- Video demonstration recorded: [watch the video](https://efundi.nwu.ac.za/access/content/group-user/1b4c43e2-0741-423b-a0bf-eeb2446e3735/42718910/KHUMO_PRESENTATION_N_SHIVURI.mp4)

**Not attempted:** the optional WPA2-Enterprise extension mentioned in the brief. It is documented as a future recommendation in the [technical report](09-report/Technical_Report.md).

---

## How to Open the Project

1. Install Cisco Packet Tracer 8.x.
2. Open [`04-packet-tracer/Khumo_Network.pkt`](04-packet-tracer/Khumo_Network.pkt).
3. Wait 60 seconds for spanning-tree convergence and DHCP leases.
4. All PCs will receive their addresses automatically.
5. Follow [`06-testing/testing-summary.md`](06-testing/testing-summary.md) to reproduce the test results.

---

## Academic Integrity

This project is my own individual work, developed according to the CMPG325-2026-133 project brief. Any AI assistance used in preparing documentation or diagrams complied with the applicable NWU AI Policy. I remain responsible for the correctness, understanding, and academic integrity of everything I submit.
