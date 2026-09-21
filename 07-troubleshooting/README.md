# Troubleshooting Log

Detailed record of issues encountered during the build, their diagnosis, and their resolution.

## Primary document

[`troubleshooting-log.md`](./troubleshooting-log.md) contains all logged issues.

## Issues covered

| # | Issue | Status |
|---|---|---|
| 1–8 | (Earlier issues — see log for full history) | Resolved |
| 6 | IP SLA object tracking not supported on simulated 3560 IOS | Documented limitation, worked around |
| 9 | Third IP phone never appeared in DHCP binding table | Resolved — DHCP conflict detection, cleared with `clear ip dhcp conflict *` |

## Post-implementation hardening

After core functionality was verified, the following hardening was added beyond the brief:
- Administrative passwords (`enable secret`, console, VTY) on all six devices
- Login banners on every device
- Port security on all 12 end-device-facing access ports (max 1 sticky MAC, restrict violation)

Details in the main log.