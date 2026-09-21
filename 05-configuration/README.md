# Device Configuration

Running-configuration exports for every device in the Khumo Advertising Billboards network, captured after Milestone 2 was fully configured and tested.

## Contents

| File | Device | Role |
|---|---|---|
| `CoreSW-running-config.txt` | Core Layer 3 switch | Inter-VLAN routing, SVIs, DHCP server, dual-ISP default routes (CR15) |
| `SW-Admin-running-config.txt` | Access switch | VLAN 10 (Admin-Data) + VLAN 40 (Voice) access ports; trunk to CoreSW |
| `SW-Creative-running-config.txt` | Access switch | VLAN 20 (Creative-Data) + VLAN 50 (Wireless) access ports; trunk to CoreSW |
| `SW-Prod-running-config.txt` | Access switch | VLAN 30 (Production-Data) access ports; trunk to CoreSW |
| `EdgeR1-running-config.txt` | Edge router | Primary ISP uplink, static routes |
| `EdgeR2-running-config.txt` | Edge router | Backup ISP uplink, static routes |

## Key configuration notes

- **CoreSW** runs `ip routing` with SVIs for VLANs 10, 20, 30, 40, 50, and 99, and acts as the DHCP server for all five user VLANs.
- **CR15 (dual-ISP resilience):** CoreSW has a floating static default route — primary via EdgeR1 (`172.16.0.2`, AD 10) and backup via EdgeR2 (`172.16.0.6`, AD 100). If the primary link fails, the backup route activates automatically.
- **Voice separation:** SW-Admin ports Fa0/5–7 use `switchport voice vlan 40` to separate IP-phone traffic from data traffic on VLAN 10 — satisfying the brief's design constraint.
- **Hardening:** all six devices have `enable secret`, console password, VTY password, and a login banner. All 12 end-device-facing access ports on the three access switches have port security (sticky MAC, max 1, restrict violation).

## Wireless Access Point

The WAP (`AP-Creative`) is configured with:
- SSID: `Khumo-Creative`
- Security: WPA2-PSK
- Encryption: AES
- Passphrase: per the WAP Config panel screenshot in `../04-packet-tracer/build-progress/wpa2-psk-ap-config.png`