# Device Configuration

Running-configuration exports for every device in the Khumo Advertising Billboards network.

**These exports reflect the FINAL rebuilt topology** (21 September 2026). All six devices were rebuilt from clean state during Milestone 2 to remove password locks and ensure marker accessibility. No passwords are configured on any device.

## Contents

| File | Device | Role |
|---|---|---|
| `CoreSW-running-config.txt` | Core Layer 3 switch | Inter-VLAN routing, SVIs, DHCP server, dual-ISP default routes (CR15) |
| `SW-Admin-running-config.txt` | Access switch | VLAN 10 (Admin-Data) + VLAN 40 (Voice); trunk on Fa0/1 |
| `SW-Creative-running-config.txt` | Access switch | VLAN 20 (Creative-Data) + VLAN 50 (Wireless); trunk on Fa0/2 |
| `SW-Prod-running-config.txt` | Access switch | VLAN 30 (Production-Data); trunk on Fa0/3 |
| `EdgeR1-running-config.txt` | Edge router | Primary ISP uplink, static routes |
| `EdgeR2-running-config.txt` | Edge router | Backup ISP uplink, static routes |

## Key configuration notes

- **CoreSW** uses `GigabitEthernet1/0/x` port naming (3560 multilayer switch model in Packet Tracer). Its old `FastEthernet0/x` naming in earlier exports was from the pre-rebuild topology.
- **CR15 floating static default routes** on CoreSW:
  - Primary: `ip route 0.0.0.0 0.0.0.0 172.16.0.2 10` (EdgeR1, admin distance 10)
  - Backup: `ip route 0.0.0.0 0.0.0.0 172.16.0.6 100` (EdgeR2, admin distance 100)
- **Voice separation (design constraint):** SW-Admin ports Fa0/5–7 use `switchport voice vlan 40`, with PC ports Fa0/2–4 on `switchport access vlan 10`.
- **Wireless isolation:** SW-Creative port Fa0/5 carries VLAN 50 (Wireless) for the WAP.
- **All devices are password-free** for marker review. This is a deliberate choice — see `../08-reflection/` for the trade-off discussion.

## Wireless Access Point

The AP (`AP-Creative`) is configured with:
- SSID: `Khumo-Creative`
- Security: WPA2-PSK
- Encryption: AES
- Evidence: `../04-packet-tracer/build-progress/wpa2-psk-ap-config.png`

## Related documentation

- Testing evidence: [`../06-testing/testing-summary.md`](../06-testing/testing-summary.md)
- Troubleshooting log: [`../07-troubleshooting/troubleshooting-log.md`](../07-troubleshooting/troubleshooting-log.md)
- Final Packet Tracer file: [`../04-packet-tracer/Khumo_Network.pkt`](../04-packet-tracer/Khumo_Network.pkt)