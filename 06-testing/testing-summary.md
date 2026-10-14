# Testing Evidence — Milestone 2

Summary of all connectivity and security testing performed during the Packet Tracer build. Screenshots referenced below are stored in [`./`](./) (this folder).

The topology was fully rebuilt during Milestone 2 (all six devices reconfigured from clean state to ensure reproducibility and marker accessibility). All test results below reflect the final rebuilt topology, captured and verified on **21 September 2026**.

## Connectivity and configuration tests

| # | Test | Method | Result | Evidence |
|---|---|---|---|---|
| 1 | VLAN/SVI configuration | `show vlan brief`, `show ip interface brief` on CoreSW | All 6 VLANs active, all SVIs up/up with correct gateway IPs | `phase2-vlans-svis-trunking.png` |
| 2 | DHCP pool configuration | `show ip dhcp pool` on CoreSW | All 5 pools correctly scoped, exclusions applied | `phase2-dhcp-pools.png` |
| 3 | Trunk configuration — SW-Admin | `show interfaces trunk` | Fa0/1 trunking, native VLAN 99, allowed VLANs 10,40,99 | `phase3-sw-admin-config.png` |
| 4 | Trunk configuration — SW-Creative | `show interfaces trunk` | Fa0/2 trunking, native VLAN 99, allowed VLANs 20,50,99 | `phase3-sw-creative-config.png` |
| 5 | Trunk configuration — SW-Prod | `show interfaces trunk` | Fa0/3 trunking, native VLAN 99, allowed VLANs 30,99 | `phase3-sw-prod-config.png` |
| 6 | DHCP address assignment | `ipconfig` on PCs in all 3 departments | Correct subnet per department | `phase4-intervlan-routing-verified.png` |
| 7 | Inter-VLAN routing (live traffic) | `ping` between Admin, Creative, Production PCs | All pairs succeeded with TTL=127 | `phase4-intervlan-routing-verified.png` |
| 8 | Voice VLAN separation | `show vlan brief` on SW-Admin — phone ports Fa0/5–7 confirmed in VLAN 40, separate from PC ports Fa0/2–4 in VLAN 10 | Confirmed: voice and data occupy distinct VLANs/broadcast domains on the same physical switch, satisfying the design constraint | `phase3-voice-vlan-isolation-verified.png` |
| 9 | Full topology — all links up | Visual inspection of canvas | All cables green, all devices reachable | `phase1-full-topology-cables-green.png` |

## Full department-to-department ping matrix

All 6 pairs of department VLANs confirmed with live ICMP traffic. TTL=127 confirms traffic routed through the Core L3 switch.

| Source | Target | Result | Evidence |
|---|---|---|---|
| Admin (192.168.56.8) | Creative (192.168.56.40) | ✅ 4/4 | `phase4-ping-matrix-admin.png` |
| Admin (192.168.56.8) | Production (192.168.56.70) | ✅ 4/4 | `phase4-ping-matrix-admin.png` |
| Creative (192.168.56.40) | Admin (192.168.56.8) | ✅ 4/4 | `phase4-ping-matrix-creative.png` |
| Creative (192.168.56.40) | Production (192.168.56.70) | ✅ 4/4 | `phase4-ping-matrix-creative.png` |
| Production (192.168.56.70) | Admin (192.168.56.8) | ✅ 4/4 | `phase4-ping-matrix-prod.png` |
| Production (192.168.56.70) | Creative (192.168.56.40) | ✅ 4/4 | `phase4-ping-matrix-prod.png` |

**Note on Voice and Wireless VLAN ping behaviour:** IP phones in Packet Tracer do not respond to ICMP from PCs (expected — they are not general-purpose hosts), and the `AccessPoint-PT-N` model does not expose a routable management IP. Voice VLAN separation is instead proven at the configuration level (dedicated VLAN 40 on switch ports, dedicated DHCP pool) and via the config evidence in `phase3-voice-vlan-isolation-verified.png`.

## CR15 — Dual-ISP resilience tests

| # | Test | Method | Result | Evidence |
|---|---|---|---|---|
| 10 | Primary route active | `show ip route` on CoreSW | `S* 0.0.0.0/0 [10/0] via 172.16.0.2` — EdgeR1 primary | `phase4-cr15-primary-restored.png` |
| 11 | CR15 failover — route table | Shut down CoreSW `Gig1/0/1`, checked `show ip route` | Gateway of last resort switched automatically to `172.16.0.6` (EdgeR2), `[100/0]` | `phase4-cr15-failover-test-SUCCESS.png` |
| 12 | CR15 failover — live traffic | `ping 203.0.113.6` while EdgeR1 link down | Reply received — traffic routed through backup path end-to-end | `phase4-cr15-failover-success.png` |
| 13 | CR15 restore | Brought CoreSW `Gig1/0/1` back up; checked `show ip route` | Gateway of last resort reverted to `172.16.0.2` (primary), `[10/0]` | `phase4-cr15-primary-restored.png` |

## WPA2-PSK wireless security tests

| # | Test | Method | Result | Evidence |
|---|---|---|---|---|
| 14 | WPA2-PSK configuration | AP-Creative Config → Port 1 | SSID `Khumo-Creative`, WPA2-PSK authentication, AES encryption | `wpa2-psk-ap-config.png` |
| 15 | WPA2-PSK — successful authentication | Connected a wireless client using the correct passphrase | Client associated, received IP in VLAN 50 range (`.129–.158`), correct gateway | `phase5-wpa2-psk-client-connected.png` |
| 16 | WPA2-PSK — incorrect passphrase rejected | Attempted connection with a deliberately wrong passphrase | Association failed — security is genuinely enforced | `phase5-wpa2-psk-wrong-password-rejected.png` |

## Known limitations

**IP SLA object tracking is not supported** on the simulated 3560's IOS image (`12.2(37)SE1`), meaning CR15's floating static route can only detect failure of the router/link itself (e.g. EdgeR1 or its LAN-side link going down), not a genuine upstream-only outage (e.g. ISP1's connection failing while EdgeR1 itself stays reachable on the LAN side). This is documented in detail, along with the reasoning and re-scoped test used instead, in [`../../07-troubleshooting/troubleshooting-log.md`](../../07-troubleshooting/troubleshooting-log.md) (Issue 6).

**Voice VLAN live packet capture (Simulation mode)** was not performed. The design constraint requiring VoIP traffic to be separated from data traffic is satisfied by:
- Dedicated VLAN 40 on SW-Admin ports Fa0/5–7 (`switchport voice vlan 40`)
- PC ports Fa0/2–4 remain in VLAN 10 (`switchport access vlan 10`)
- Separate DHCP pool with scope 192.168.56.97–126
- Three IP phones confirmed with valid leases in that range
- Config-level evidence in `phase3-voice-vlan-isolation-verified.png`

**WPA2-Enterprise extension** (listed as optional in the brief) was not attempted. WPA2-PSK hardening was fully completed and demonstrated.

## Topology version note

The topology was rebuilt during Milestone 2 to remove device-level password locks that had blocked marker review, and to ensure the entire network could be demonstrated from clean state. All six devices (CoreSW, EdgeR1, EdgeR2, SW-Admin, SW-Creative, SW-Prod) were rebuilt and re-verified. **All screenshots and test results above reflect the final rebuilt topology.** No passwords are configured on any device, ensuring full marker accessibility.