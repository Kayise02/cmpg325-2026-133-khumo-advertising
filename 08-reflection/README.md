# Reflection

## Overview

This project took Khumo Advertising Billboards (Vryburg) from a written client brief to a working, tested Cisco Packet Tracer network, documented end-to-end in this GitHub portfolio. Reflecting on the process, three things stand out: the design decisions I'd keep, the challenges that taught me the most, and what I'd do differently next time.

## Design decisions I'd keep

**The three-tier topology.** Separating edge, core, and access layers made the network easy to reason about. When debugging the CR15 failover, I could isolate the problem to a single layer rather than guessing at a flat design.

**/27 subnets across six VLANs.** The assigned block of `192.168.56.0/24` gave room for six `/27` subnets with two reserved for growth. This matched the SMB scale (about 30 users) without wasting address space or forcing awkward allocations.

**Floating static default routes for CR15.** With only two ISPs and no dynamic routing protocol requirement, floating statics provided resilience with minimal complexity. The primary route (admin distance 10) and backup route (admin distance 100) switched automatically and cleanly during testing.

## What was hard — and what it taught me

**The password lockout.** During an early hardening step, I configured `enable secret`, console, and VTY passwords on every device as a security best practice. Later in the build, this became a serious obstacle: I could not recall the passwords I had set, and every device was effectively locked. In Packet Tracer, the standard Cisco password-recovery procedure (ROMMON break via Ctrl+C) worked reliably on the routers but not on the switches, whose ROMMON window in PT is difficult to hit.

My resolution was to rebuild the locked switches from a saved config, exporting each running-config beforehand and re-applying it to a fresh device. This worked every time, but it was slow and forced me to re-verify connectivity after each rebuild.

The lesson: **for a submission artefact that a marker must inspect, password protection is counterproductive.** A working network with no passwords is more valuable than a locked network that follows security best practice on paper. I removed all passwords from the final topology.

**DHCP conflict detection.** A Cisco DHCP server pings an address before offering it. During a period where three IP phones were requesting leases in quick succession, this triggered repeated false conflicts, and one phone never appeared in the binding table. Diagnosing this required reading the console log carefully (`%DHCPD-4-PING_CONFLICT`), ruling out duplicate MAC addresses via `show mac address-table`, and finally clearing stale conflict records with `clear ip dhcp conflict *`. The lesson: **DHCP is not a black box — its conflict-detection behaviour is documented and predictable once you understand it.**

**Native VLAN mismatches.** After each switch rebuild, console output filled with `%CDP-4-NATIVE_VLAN_MISMATCH` warnings until I confirmed both ends of every trunk were on native VLAN 99. This taught me to verify trunk configuration on **both** sides of every link, not just the access switch.

## IP SLA limitation

The original CR15 design intended to use IP SLA object tracking for default-route failover, which would have detected upstream-only failures (e.g., ISP1 going down while EdgeR1 remained reachable). The 3560 IOS image in Packet Tracer does not support IP SLA. I re-scoped the test to link-state failure only, documented the limitation, and verified that the floating static route still performs correctly under the failure modes Packet Tracer can simulate.

## What I'd do differently

1. **Save the `.pkt` file after every change.** Several hours were lost re-doing work after a crash or unexpected reset.
2. **Do not add passwords during development.** Add them only if the rubric explicitly rewards it, and even then, document them clearly in the README.
3. **Export running-configs immediately after each device is configured**, not at the end. When I rebuilt devices, the exports in `05-configuration/` quickly became stale.
4. **Test the failover path early.** I verified primary connectivity first and only tested failover near the end. Testing both paths earlier would have surfaced configuration issues sooner.

## What went well

The final network works end-to-end: all six devices are configured, every VLAN is reachable across every other VLAN, DHCP assigns correctly per subnet, CR15 failover switches to the backup ISP and back, and WPA2-PSK wireless authentication succeeds with the correct passphrase and rejects the incorrect one.

The documentation trail (design → addressing → configs → tests → troubleshooting) mirrors how a real network project would be delivered, which I think is the most valuable outcome of this project.

## Conclusion

The project reinforced that **network engineering is as much about documentation, testing, and iteration as it is about configuration.** The configuration itself was, in most cases, the easy part. Diagnosing why something that *should* work didn't — and building the discipline to save, document, and re-test after every change — was where most of the learning happened.