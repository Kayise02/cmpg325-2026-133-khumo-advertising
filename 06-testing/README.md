# Testing

Testing evidence for Milestone 2.

## Primary document

[`testing-summary.md`](./testing-summary.md) is the master testing summary — it lists every connectivity and security test performed, the method used, the outcome, and the corresponding screenshot.

## Coverage

| Area | Tested | Evidence location |
|---|---|---|
| VLAN and SVI configuration | ✅ | `phase2-vlans-svis-trunking.png` |
| DHCP pools and leases | ✅ | `phase2-dhcp-pools.png` |
| Trunk configuration (all 3 access switches) | ✅ | `phase3-sw-admin-config.png`, `phase3-sw-creative-config.png`, `phase3-sw-prod-config.png` |
| DHCP assignment per department | ✅ | `phase3-pc-admin-ipconfig.png`, `phase3-pc-creative-ipconfig.png`, `phase3-pc-prod-ipconfig.png` |
| Inter-VLAN routing (live traffic) | ✅ | `phase4-intervlan-routing-verified.png`, `phase4-intervlan-ping-admin-to-creative.png` |
| Voice VLAN separation (config level) | ✅ | `phase3-voice-vlan-isolation-verified.png` |
| CR15 dual-ISP failover (route table) | ✅ | `phase4-cr15-failover-test-SUCCESS.png` |
| CR15 failover (live traffic) | ✅ | `phase4-cr15-failover-success.png` |
| CR15 restore to primary | ✅ | `phase4-cr15-primary-restored.png` |
| WPA2-PSK AP configuration | ✅ | `wpa2-psk-ap-config.png` |
| WPA2-PSK successful authentication | ✅ | `phase5-wpa2-psk-client-connected.png` |
| WPA2-PSK incorrect passphrase rejected | ✅ | `phase5-wpa2-psk-wrong-password-rejected.png` |
| Full VLAN-to-VLAN ping matrix | ✅ | `phase4-ping-matrix-admin.png`, `phase4-ping-matrix-creative.png`, `phase4-ping-matrix-prod.png` |
| CoreSW verification bundle | ✅ | `phase4-coresw-route-and-dhcp-bindings.png`, `phase4-coresw-verification-bundle.png` |

## Folder contents by phase

### Phase 1 — Device placement and cabling
- `phase1-cabling-complete.png`
- `phase1-device-placement.png`
- `phase1-full-topology-cables-green.png`

### Phase 2 — Core configuration
- `phase2-dhcp-pools.png`
- `phase2-vlans-svis-trunking.png`

### Phase 3 — Access layer and end devices
- `phase3-all-three-phones-dhcp-confirmed.png`
- `phase3-logical-topology-view.png`
- `phase3-pc-admin-ipconfig.png`
- `phase3-pc-creative-ipconfig.png`
- `phase3-pc-prod-ipconfig.png`
- `phase3-sw-admin-config.png`
- `phase3-sw-creative-config.png`
- `phase3-sw-prod-config.png`
- `phase3-voice-vlan-isolation-verified.png`

### Phase 4 — Routing and CR15 resilience
- `phase4-coresw-route-and-dhcp-bindings.png`
- `phase4-coresw-verification-bundle.png`
- `phase4-cr15-failover-success.png`
- `phase4-cr15-failover-test-SUCCESS.png`
- `phase4-cr15-primary-restored.png`
- `phase4-intervlan-ping-admin-to-creative.png`
- `phase4-intervlan-routing-verified.png`
- `phase4-ping-matrix-admin.png`
- `phase4-ping-matrix-creative.png`
- `phase4-ping-matrix-prod.png`

### Phase 5 — Wireless security (WPA2-PSK)
- `phase5-wpa2-psk-client-connected.png`
- `phase5-wpa2-psk-wrong-password-rejected.png`
- `wpa2-psk-ap-config.png`

### Summary
- `testing-summary.md` — consolidated test results and observations

## Known limitations

- IP SLA object tracking is not supported on the simulated 3560 IOS image, so CR15 failover is triggered by link-down rather than upstream-only outage. Documented in `../07-troubleshooting/Troubleshooting_Log.md` (Issue 6).
- WPA2-Enterprise was listed as an optional extension in the brief and was not attempted; WPA2-PSK hardening was completed and demonstrated instead.