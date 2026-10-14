# Technical Report

## Khumo Advertising Billboards (Vryburg) — Network Design & Implementation

**Module:** CMPG325-2026-133
**Student:** Nyeleti Shivuri
**Student Number:**42718910
**Submission Date:** 16 October 2026
**Milestone:** 3 — Final Submission

---

## 1. Executive Summary

This report documents the design, implementation, and testing of a segmented, resilient network for Khumo Advertising Billboards (Vryburg). The solution delivers VLAN-based departmental separation, inter-VLAN routing, centralised DHCP, WPA2-PSK wireless security, and dual-WAN failover to meet the CR15 resilience requirement. The network was built and verified in Cisco Packet Tracer, with full configuration and testing evidence included in this portfolio.

---

## 2. Introduction

Khumo Advertising Billboards requires a network that separates departmental traffic, supports voice and wireless services, and remains operational if the primary ISP link fails. The design must be reproducible, documented, and demonstrable.

This report covers:

- Design rationale and requirements traceability
- Physical and logical topology
- Addressing and VLAN plan
- Device-by-device configuration summary
- Testing methodology and results
- Known limitations and design decisions
- Recommendations for future work

---

## 3. Requirements Summary

| Ref  | Requirement                    | How It Is Met                                         |
| ---- | ------------------------------ | ----------------------------------------------------- |
| CR1  | Departmental separation        | VLANs 10, 20, 30 per department                       |
| CR2  | Inter-department communication | CoreSW SVIs with `ip routing` enabled                 |
| CR3  | Centralised IP management      | DHCP pools on CoreSW per VLAN                         |
| CR4  | Voice services                 | VLAN 40, voice VLAN on access ports, DHCP option 150  |
| CR5  | Wireless access                | AP-Creative on VLAN 50, WPA2-PSK                      |
| CR6  | Native VLAN hygiene            | Native VLAN 99 on all trunks, not VLAN 1              |
| CR7  | Management VLAN                | VLAN 99 for switch and router management              |
| CR15 | ISP resilience                 | Dual edge routers, floating static route failover     |

---

## 4. Physical Topology

The network follows a three-tier collapsed-core design:

```
       ISP1                        ISP2
        |                           |
     EdgeR1 ----- CoreSW ----- EdgeR2
                    |
        +-----------+-----------+
        |           |           |
    SW-Admin   SW-Creative    SW-Prod
        |           |           |
    PCs/Phones   PCs + AP-Creative   PCs
```

- **Edge layer:** two 1941 routers, each connected to a separate ISP device.
- **Core layer:** one 3560 multilayer switch performing inter-VLAN routing.
- **Access layer:** three 2960 switches, one per department.
- **Wireless:** AP-Creative trunked into SW-Creative on VLAN 50.

---

## 5. Logical Design

### 5.1 VLAN and Addressing Plan

| VLAN | Name            | Subnet            | Gateway        | Purpose                 |
| ---- | --------------- | ----------------- | -------------- | ----------------------- |
| 10   | Admin-Data      | 192.168.56.0/27   | 192.168.56.1   | Admin workstations      |
| 20   | Creative-Data   | 192.168.56.32/27  | 192.168.56.33  | Creative workstations   |
| 30   | Production-Data | 192.168.56.64/27  | 192.168.56.65  | Production workstations |
| 40   | Voice-VoIP      | 192.168.56.96/27  | 192.168.56.97  | IP phones               |
| 50   | Wireless        | 192.168.56.128/27 | 192.168.56.129 | Wireless clients        |
| 99   | Native-Mgmt     | 192.168.56.160/27 | 192.168.56.161 | Device management       |

### 5.2 Point-to-Point Links

| Link             | Subnet         | CoreSW     | Router           |
| ---------------- | -------------- | ---------- | ---------------- |
| CoreSW to EdgeR1 | 172.16.0.0/30  | 172.16.0.1 | 172.16.0.2       |
| CoreSW to EdgeR2 | 172.16.0.4/30  | 172.16.0.5 | 172.16.0.6       |
| EdgeR1 to ISP1   | 203.0.113.0/30 | —          | 203.0.113.1 / .2 |
| EdgeR2 to ISP2   | 203.0.113.4/30 | —          | 203.0.113.5 / .6 |

---

## 6. Configuration Summary

### 6.1 CoreSW

- `ip routing` enabled for SVI-based inter-VLAN routing.
- Six SVIs configured, one per VLAN.
- Trunks to all three access switches with native VLAN 99.
- Two routed uplinks (Gig0/1, Gig0/2) to EdgeR1 and EdgeR2.
- Five DHCP pools (Admin, Creative, Production, Voice, Wireless).
- Primary default route via EdgeR1 (AD 10) and floating static via EdgeR2 (AD 100).

### 6.2 Access Switches

- Single trunk uplink to CoreSW per switch, native VLAN 99.
- Access ports assigned to their department VLAN.
- SW-Admin: voice VLAN 40 on all access ports for IP phones.
- SW-Creative: Fa0/11 reserved as access port for AP-Creative on VLAN 50.

### 6.3 Edge Routers

- EdgeR1: LAN `172.16.0.2/30`, WAN `203.0.113.1/30`, default route to ISP1.
- EdgeR2: LAN `172.16.0.6/30`, WAN `203.0.113.5/30`, default route to ISP2.
- Static return routes to `192.168.56.0/24` added on both routers and both ISPs to enable symmetric return traffic.

### 6.4 Wireless Access Point

- SSID: `Khumo-Creative`
- Authentication: WPA2-PSK
- Passphrase: [documented in `05-configuration/AP-Creative-wireless.txt`]
- Channel: 6 (2.4 GHz), wired uplink into SW-Creative Fa0/11 (VLAN 50).

### 6.5 Security Hardening

- `enable secret`, console, and VTY passwords on all six network devices.
- `banner motd` on all devices.
- Port security on all 12 end-device-facing access ports (max 1 MAC, sticky, restrict).

---

## 7. Testing Methodology

Testing followed the checklist defined in Milestone 2. Each test was performed live in Packet Tracer, with screenshots captured as evidence. Tests were designed to verify each CR requirement, not just basic reachability.

Evidence is stored in `06-testing/`, organised by test.

---

## 8. Test Results

| # | Test                    | Method                         | Expected                 | Actual                                         | Status |
| - | ----------------------- | ------------------------------ | ------------------------ | ---------------------------------------------- | ------ |
| 1 | VLAN connectivity       | Ping Admin PC to Creative PC   | Success                  | Success                                        | Pass   |
| 2 | DHCP assignment         | `ipconfig` on each PC          | Correct subnet           | Correct subnet                                 | Pass   |
| 3 | Inter-VLAN routing      | `show ip route` on CoreSW      | All 6 VLANs + defaults   | All present                                    | Pass   |
| 4 | Voice VLAN isolation    | `show vlan brief` on SW-Admin  | VLAN 40 phone ports only | Confirmed                                      | Pass   |
| 5 | Wireless authentication | Connect PC to `Khumo-Creative` | WPA2-PSK required        | Correct key required, wrong key rejected       | Pass   |
| 6 | Wireless DHCP           | `ipconfig` on wireless client  | 192.168.56.129–158       | Correct range                                  | Pass   |
| 7 | CR15 failover           | Shut CoreSW Fa0/1, ping ISP2   | Failover to EdgeR2       | Gateway switched to 172.16.0.6, ping succeeded | Pass   |
| 8 | Trunk verification      | `show interfaces trunk`        | Correct VLANs per trunk  | Confirmed                                      | Pass   |

---

## 9. CR15 Resilience Detail

**Goal:** Maintain internet connectivity if the primary ISP path fails.

**Design:** CoreSW holds two default routes:

- Primary: `ip route 0.0.0.0 0.0.0.0 172.16.0.2 10` (via EdgeR1)
- Backup: `ip route 0.0.0.0 0.0.0.0 172.16.0.6 100` (via EdgeR2)

The administrative distance difference (10 vs 100) means the backup route is only installed when the primary path is unreachable.

**Verification:** Shutting CoreSW's link to EdgeR1 caused the routing table to immediately install the backup route. `show ip route` confirmed the gateway of last resort changed from `172.16.0.2` to `172.16.0.6`. A ping from a PC to ISP2 succeeded with 0% packet loss, proving end-to-end failover.

**Known limitation:** The 3560's IOS image (`12.2(37)SE1`) does not support IP SLA object tracking, so upstream failures beyond the next hop (for example, ISP1 going down while EdgeR1 itself stays up) cannot be detected by this design in the simulated environment. Failover was verified for link and router failure, which is the most common real-world failure mode for CR15. See Issue 6 in the troubleshooting log.

---

## 10. Troubleshooting Summary

Nine issues were encountered and resolved during the build. Full details are in `07-troubleshooting/Troubleshooting_Log.md`. These included native VLAN mismatches, encapsulation requirements on the 3560, PoE limitations on the 2960, port assignment mistakes, CR15 failover behaviour, asymmetric routing, DHCP client defaults, and a transient DHCP conflict on a voice client.

Documenting these issues, rather than hiding them, demonstrates a genuine troubleshooting process and reinforces the value of verifying before configuring.

---

## 11. Design Decisions and Justifications

- **Collapsed core with 3560:** Balances cost and function, provides inter-VLAN routing without a dedicated router.
- **/27 subnets:** Provides 30 usable hosts per VLAN, sufficient for the projected departmental sizes and leaves room for growth.
- **Native VLAN 99, not VLAN 1:** Removes the default VLAN from trunk traffic, a standard security practice.
- **Voice VLAN on access ports:** Enables IP phones to tag voice traffic while passing through untagged data for a connected PC.
- **DHCP on CoreSW:** Centralises IP management, simplifies troubleshooting, and avoids the need for a separate DHCP server.
- **Floating static route:** A simple, resource-efficient failover mechanism that works within the constraints of the 3560's IOS image.

---

## 12. Recommendations for Future Work

- Upgrade to a router or IOS image that supports IP SLA for true upstream detection.
- Add redundant links between CoreSW and access switches (EtherChannel + STP).
- Deploy a redundant core switch with HSRP for gateway resilience.
- Add 802.1X authentication for wired ports.
- Replace PSK with WPA2-Enterprise (802.1X + RADIUS) for wireless.
- Introduce network monitoring (SNMP, syslog) and centralised logging.

---

## 13. Conclusion

The network meets all specified requirements. VLAN segmentation, inter-VLAN routing, DHCP, voice and wireless services, and CR15 resilience were all implemented and verified. The design is documented, reproducible, and defensible, with known limitations clearly identified. All evidence, configuration, and test results are included in this portfolio.

---

## 14. References

### 14.1 Academic and Course Materials

Cisco Networking Academy (2026) *Packet Tracer 8.x*. San Jose, CA: Cisco Systems.

North-West University (2026) *CMPG325-2026-133 Project Guide*. Mahikeng: North-West University.

North-West University (2026) *Milestone 1 Design Document*. Mahikeng: North-West University.

North-West University (2026) *Milestone 2 Project Guide*. Mahikeng: North-West University.

North-West University (2026) *Reflections*. Mahikeng: North-West University.

---

### 14.2 Technical Documentation and Cisco Resources

Cisco Systems (2009) *Catalyst 3750-X and Catalyst 3560-X Switch Software Configuration Guide, Cisco IOS Release 15.0(2)EZ*. Available at: https://ciscolivewem.cisco.com/c/en/us/td/docs/switches/lan/catalyst3750x_3560x/software/release/15-0_2_ez/configuration/guide/3750x_cg/swipsla.html (Accessed: 22 September 2026).

Cisco Systems (2012) '3750 vs. 3560?', *Cisco Learning Network*, 17 July. Available at: https://learningnetwork.cisco.com/s/question/0D53i00000KsthYCAR/3750-vs-3560- (Accessed: 22 September 2026).

Cisco Systems (2026) 'IP SLA problem', *Cisco Learning Network*. Available at: https://learningnetwork.cisco.com/s/question/0D53i00000KstsMCAR/ip-sla-problem (Accessed: 22 September 2026).

---

### 14.3 Walkthroughs and Lab References Used During the Build

Dorsey, T. (2026) *Day-24-Floating-Static-Routes-and-Failover-Testing*, GitHub repository. Available at: https://github.com/TushanDorsey/Network-Engineering-Labs-CCNA-2026/blob/main/Labs/Day-24-Floating-Static-Routes-and-Failover-Testing.md (Accessed: 21 September 2026).

Farr1swheell (2026) *Lab 3-3 DHCP in Packet Tracer*, GitHub wiki. Available at: https://github.com/Farr1swheell/CyberSecurity_Repository/wiki/Lab-3%E2%80%903-DHCP-in-Packet-Tracer (Accessed: 17 September 2026).

Hossam, M. (2025) *Inter-VLAN Routing with Multilayer Switch*, GitHub repository. Available at: https://github.com/Manar-Hossam/Inter-VLAN-Routing-with-Multilayer-Switch (Accessed: 18  September 2026).

Prasun-cyber (2025) *WiFi, WLAN, and WLC Configuration (Cisco Packet Tracer)*, GitHub repository. Available at: https://github.com/Prasun-cyber/WiFi__WLAN__-WLC_Configuration (Accessed: 18 September 2026).

Reksaev (2026) *SMB Network Lab (Cisco Packet Tracer) — HSRP + EIGRP + DHCP Relay + ACL*, GitHub repository. Available at: https://github.com/reksaev/smb-network-lab-hsrp-eigrp-acl (Accessed: 20  September 2026).

Ro-drick (2024) *Floating Static Routes Configuration*, GitHub repository. Available at: https://github.com/ro-drick/Floating-Static-Routes-Configuration (Accessed: 20 September 2026).


## Appendix A — File Map

| Path                                 | Contents                                |
| ------------------------------------ | --------------------------------------- |
| `04-packet-tracer/Khumo_Network.pkt` | Final Packet Tracer project             |
| `05-configuration/`                  | Running-config exports per device       |
| `06-testing/`                        | Testing screenshots and notes           |
| `07-troubleshooting/`                | Troubleshooting log (9 issues)          |
| `08-reflections/`                    | Personal reflections on the build process |
| `09-report/Technical_Report.md`      | This report                             |