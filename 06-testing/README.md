# Testing

Testing evidence for Milestone 2.

## Primary document

[`testing-summary.md`](./testing-summary.md) is the master testing summary — it lists every connectivity and security test performed, the method used, the outcome, and the corresponding screenshot.

## Coverage

| Area | Tested | Evidence location |
|---|---|---|
| VLAN and SVI configuration | ✅ | `../04-packet-tracer/build-progress/phase2-vlans-svis-trunking.png` |
| DHCP pools and leases | ✅ | `../04-packet-tracer/build-progress/phase2-dhcp-pools.png` |
| Trunk configuration (all 3 access switches) | ✅ | `phase3-sw-admin-config.png`, `phase3-sw-creative-config.png`, `phase3-sw-prod-config.png` |
| DHCP assignment per department | ✅ | `phase4-intervlan-routing-verified.png` |
| Inter-VLAN routing (live traffic) | ✅ | `phase4-intervlan-routing-verified.png` |
| Voice VLAN separation (config level) | ✅ | `phase3-voice-vlan-isolation-verified.png` |
| CR15 dual-ISP failover (route table) | ✅ | `phase4-cr15-failover-test-SUCCESS.png` |
| CR15 failover (live traffic) | ✅ | `phase4-cr15-failover-success.png` |
| CR15 restore to primary | ✅ | `phase4-cr15-primary-restored.png` |
| WPA2-PSK AP configuration | ✅ | `wpa2-psk-ap-config.png` |
| WPA2-PSK successful authentication | ✅ | `phase5-wpa2-psk-client-connected.png` |
| WPA2-PSK incorrect passphrase rejected | ✅ | `phase5-wpa2-psk-wrong-password-rejected.png` |
| Voice VLAN traffic separation (Simulation mode) | ✅ | `phase3-voip-simulation-*.png` |
| Full VLAN-to-VLAN ping matrix | ✅ | `phase4-ping-matrix-*.png` |

## Known limitations

- IP SLA object tracking is not supported on the simulated 3560 IOS image, so CR15 failover is triggered by link-down rather than upstream-only outage. Documented in `../07-troubleshooting/troubleshooting-log.md` (Issue 6).
- WPA2-Enterprise was listed as an optional extension in the brief and was not attempted; WPA2-PSK hardening was completed and demonstrated instead.