# Testing Evidence — Milestone 2

Summary of all connectivity and security testing performed during the Packet Tracer build. Screenshots referenced below are stored in [`../04-packet-tracer/build-progress/`](../04-packet-tracer/build-progress).

| # | Test | Method | Result | Evidence |
|---|---|---|---|---|
| 1 | VLAN/SVI configuration | `show vlan brief`, `show ip interface brief` on CoreSW | All 6 VLANs active, all SVIs up/up with correct gateway IPs | `phase2-vlans-svis-trunking.png` |
| 2 | DHCP pool configuration | `show ip dhcp pool` on CoreSW | All 5 pools correctly scoped, exclusions applied | (Phase 2 CLI output) |
| 3 | Trunk configuration | `show interfaces trunk` on CoreSW and all 3 access switches | Native VLAN 99, correct allowed VLANs on every trunk | `phase3-sw-admin-config.png`, `phase3-sw-creative-config.png`, `phase3-sw-prod-config.png` |
| 4 | DHCP address assignment | `ipconfig` on PCs in all 3 departments | Correct subnet per department (Admin `.1–.30`, Creative `.33–.62`, Production `.65–.94`) | `phase4-intervlan-routing-verified.png` |
| 5 | Inter-VLAN routing (live traffic) | `ping` from an Admin PC to a Creative PC and a Production PC | Both succeeded, `TTL=127` confirming traffic passed through the CoreSW router | `phase4-intervlan-routing-verified.png` |
| 6 | Voice VLAN separation | `show vlan brief` on SW-Admin — phone ports (Fa0/5–7) confirmed in VLAN 40 (Voice), separate from PC ports (Fa0/2–4) in VLAN 10 (Admin-Data) | Confirmed: voice and data occupy distinct VLANs/broadcast domains on the same physical switch, satisfying the design constraint | (Phase 3 CLI output, SW-Admin) |
| 7 | CR15 dual-ISP failover | Shut down CoreSW's link to EdgeR1 (`FastEthernet0/1`); checked `show ip route` | Gateway of last resort switched automatically from `172.16.0.2` (EdgeR1) to `172.16.0.6` (EdgeR2) | `phase4-cr15-failover-test-SUCCESS.png` |
| 8 | CR15 failover — live traffic | `ping 203.0.113.6` from a PC while EdgeR1's link was down | 100% success, proving real traffic routed through the backup path end-to-end | `phase4-cr15-failover-success.png` |
| 9 | CR15 restore | Brought CoreSW's link to EdgeR1 back up; checked `show ip route` | Gateway of last resort automatically reverted to `172.16.0.2` (primary), confirming correct preference for the primary ISP when healthy | `phase4-cr15-primary-restored.png` |
| 10 | WPA2-PSK configuration | AP-Creative Config → Port 1 | SSID `Khumo-Creative`, WPA2-PSK authentication, AES encryption | `wpa2-psk-ap-config.png` |
| 11 | WPA2-PSK — successful authentication | Connected a wireless client using the correct passphrase | Client associated successfully, received IP `192.168.56.134` (VLAN 50, Wireless range `.129–.158`), correct gateway | `phase5-wpa2-psk-client-connected.png` |
| 12 | WPA2-PSK — incorrect passphrase rejected | Attempted connection with a deliberately wrong passphrase | Association failed, confirming the security is genuinely enforced | `phase5-wpa2-psk-wrong-password-rejected.png` |

## Known limitation

**IP SLA object tracking is not supported** on the simulated 3560's IOS image (`12.2(37)SE1`), meaning CR15's floating static route can only detect failure of the router/link itself (e.g. EdgeR1 or its LAN-side link going down), not a genuine upstream-only outage (e.g. ISP1's connection failing while EdgeR1 itself stays reachable on the LAN side). This is documented in detail, along with the reasoning and re-scoped test used instead, in [`../07-troubleshooting/troubleshooting-log.md`](../07-troubleshooting/troubleshooting-log.md) (Issue 6).

## Outstanding for full test coverage

- Full department-to-department ping matrix (all 6 VLAN pairs, not just the 2 tested above)
- Voice VLAN traffic isolation confirmed via live packet capture or simulation mode (currently confirmed only via VLAN assignment, not live traffic separation)
- WPA2-Enterprise extension testing (if attempted)
