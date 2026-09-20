# CMPG325 Individual Network Project Portfolio

**Student:** Shivuri, Nyeleti
**Student number:** 42718910
**Project ID:** CMPG325-2026-133
**Client ID:** CLI-133
**Assigned organisation:** Khumo Advertising Billboards (Vryburg)
**Industry:** Media

---

## Project overview

This repository is the portfolio of evidence for my CMPG325 individual network project. The brief assigns Khumo Advertising Billboards (Vryburg) as the client, with a fixed addressing block, a mandatory design constraint separating VoIP and data traffic, an assigned wireless security challenge (WPA2-PSK hardening), and a resilience change request requiring a second internet connection. Where the brief did not specify organisational detail, I made and documented reasonable network engineering assumptions, consistent with my lecturer's guidance for this project.

## Repository structure

| Folder | Contents |
|---|---|
| `01-client-requirements/` | Client requirements and documented engineering assumptions |
| `02-topology/` | Physical and logical topology diagrams, including editable draw.io source files |
| `03-ip-addressing/` | VLAN table, IP addressing plan, WAN links, and trunk configuration |
| `04-packet-tracer/` | Packet Tracer project file and a full build progress screenshot log |
| `05-configuration/` | Full running configuration export for every device, plus the wireless access point's security configuration |
| `06-testing/` | Testing evidence summary covering all connectivity and security tests |
| `07-troubleshooting/` | Detailed troubleshooting log documenting issues encountered and resolved |
| `08-reflection/` | Final project reflection |

## Project status

**Milestone 1, Client Design Review: complete.** Client requirements, documented assumptions, physical topology, logical topology, VLAN design, IP addressing plan, and the initial GitHub repository were all submitted.

**Milestone 2, Client Implementation Review: complete.** The following has been built, configured, and verified:

- All devices placed, labelled, and cabled according to the physical topology
- Core Layer 3 switch configured with VLANs, switch virtual interfaces, trunking, and DHCP pools
- All three access switches configured with correct trunk and access ports
- Edge routers configured with WAN uplinks to both internet service providers
- Dual ISP resilience tested in both directions, with live traffic confirmed during failover and after the primary link was restored
- Inter-VLAN routing verified with live pings between all three departments
- All PCs and IP phones confirmed on DHCP with correct department based addressing
- Voice VLAN separation confirmed at the port level and in the DHCP binding table
- WPA2-PSK with AES encryption configured on the Creative wireless access point, with both a successful connection and a rejected incorrect password tested
- Full running configuration exported for all six network devices
- Administrative passwords, login banners, and port security added across all devices as additional hardening
- Testing evidence summary and troubleshooting log completed

Not yet attempted: the WPA2-Enterprise optional extension mentioned in the brief.

**Milestone 3, Final Evaluation: not yet started.**

## Design summary

### Topology

The network follows a three tier hierarchical design: an edge layer with two independent internet service provider connections, a core Layer 3 switch handling all inter-VLAN routing, and an access layer of three switches serving each department separately. This structure satisfies the dual ISP resilience requirement and keeps departmental traffic cleanly separated down to the access layer.

**Physical topology diagram:**

![Physical topology](./02-topology/physical-topology.png)

**Logical topology diagram:**

![Logical topology](./02-topology/logical-topology.png)

The editable draw.io source files for both diagrams are in `02-topology/`.

### VLAN design

| VLAN ID | Name | Department |
|---|---|---|
| 10 | Admin-Data | Management and Admin |
| 20 | Creative-Data | Creative and Design |
| 30 | Production-Data | Production and Logistics |
| 40 | Voice (VoIP) | Management and Admin |
| 50 | Wireless | Creative and Design, and meeting rooms |
| 99 | Native/Mgmt | Network infrastructure |

The full justification for this VLAN design is in `03-ip-addressing/ip-addressing-plan.md`.

### IP addressing

The assigned block, 192.168.56.0/24, is subnetted into /27 blocks across six VLANs. The full addressing table with gateways and usable ranges is in `03-ip-addressing/ip-addressing-plan.md`.

## Client requirements and assumptions

The full requirements list and the engineering assumptions made where the brief left detail unspecified, covering departments, user counts, wireless and VoIP placement, and the redundancy design, are documented in `01-client-requirements/client-requirements.md`.

## Academic integrity

This project is my own individual work, developed according to the CMPG325-2026-133 project brief. Any AI assistance used in preparing documentation or diagrams complied with the applicable NWU AI Policy. I remain responsible for the correctness, understanding, and academic integrity of everything submitted.
