# Packet Tracer

The completed Cisco Packet Tracer project file and screenshot evidence for all testing phases.

## Contents

- `Khumo_Network.pkt` — final working topology, with all devices configured and tested
- `build-progress/` — screenshot evidence, organised by phase:
  - `phase1-*.png` — device placement and cabling
  - `phase2-*.png` — VLAN, SVI, and DHCP configuration
  - `phase3-*.png` — access switch trunk/access port config, voice VLAN separation, VoIP simulation
  - `phase4-*.png` — inter-VLAN routing, ping matrix, CR15 failover tests
  - `phase5-*.png` — WPA2-PSK configuration and verification

The testing narrative that ties all screenshots together is in [`../06-testing/testing-summary.md`](../06-testing/testing-summary.md).

## Design references

- Topology: [`../02-topology/`](../02-topology/)
- VLAN and IP addressing plan: [`../03-ip-addressing/`](../03-ip-addressing/)