# Troubleshooting Log

## Issue 1: Native VLAN mismatch warnings during trunk configuration

**Symptom:** After configuring trunk ports on CoreSW, repeated `%CDP-4-NATIVE_VLAN_MISMATCH` warnings appeared referencing SW-Admin, SW-Creative, and SW-Prod.

**Cause:** CoreSW's trunk ports were configured with native VLAN 99 before the access switches had any trunk configuration applied — they were still sitting on the factory default native VLAN 1, so the two ends of each link temporarily disagreed.

**Resolution:** No action needed — the warnings cleared automatically once each access switch's trunk side was configured to match (native VLAN 99). This confirmed the importance of configuring both ends of a trunk link, not just one.

---

## Issue 2: 3560 rejected trunk encapsulation command

**Symptom:** `switchport trunk encapsulation dot1q` returned `Command rejected` on CoreSW's FastEthernet interfaces when first attempting `switchport mode trunk`.

**Cause:** Misread of the initial error — the actual issue was that `switchport mode trunk` alone failed first because the 3560 needed an explicit encapsulation type set before trunk mode could be applied (dual-encapsulation-capable hardware, unlike the 2960s used for access switches, which only support 802.1Q and don't require this step).

**Resolution:** Ran `switchport trunk encapsulation dot1q` before `switchport mode trunk` on the 3560's interfaces. All three trunk ports came up immediately afterward, along with all six VLAN SVIs flipping to line-protocol up.

---

## Issue 3: IP phones showing "notconnect" despite visible cabling

**Symptom:** Three IP phones cabled to SW-Admin showed as physically connected on the canvas (visible cable lines) but `show interfaces status` reported "notconnect" on every port they were meant to be using.

**Cause:** The SW-Admin switch (2960-24TT model) does not support Power over Ethernet (PoE). Cisco IP phones normally draw power from the switch over the same cable; without PoE, the phones never fully powered on, so the link never came up from the switch's perspective even though the cable was physically present.

**Resolution:** Added an `IP_PHONE_POWER_ADAPTER` module to each phone's Physical configuration, giving each phone its own independent power source instead of relying on PoE. All three ports came up as "connected" immediately afterward.

---

## Issue 4: Trunk/access port assignments didn't match assumed port numbers

**Symptom:** Commands aimed at "the trunk port to CoreSW" or "the access port for the AP" were applied to the wrong interface more than once (e.g. SW-Creative's actual uplink to CoreSW turned out to be Fa0/2, not Fa0/1 as initially assumed).

**Cause:** Port numbers were assumed based on the order devices were cabled rather than verified — Packet Tracer does not guarantee cables land on sequential port numbers matching the order you draw them.

**Resolution:** Adopted a strict verify-before-configure habit: run `show cdp neighbors` (or `show interfaces status` for non-CDP devices like PCs/APs) before applying any interface-specific configuration, rather than assuming port numbers from memory or documentation. This was applied consistently for the remainder of the build.

---

## Issue 5: CoreSW's link to EdgeR2 repeatedly landed on the wrong router-side port

**Symptom:** After several attempts to cable CoreSW to EdgeR2's LAN-facing interface (Gig0/0), CDP repeatedly showed the link landing on EdgeR2's Gig0/1 instead — which is the WAN-facing interface reserved for the ISP2 connection. This caused one physical cable to incorrectly serve as both the LAN and WAN link simultaneously, leaving EdgeR2's true LAN port with no carrier at all.

**Cause:** Manual misselection of the destination port in Packet Tracer's connection dialog, repeated across multiple attempts due to visually similar port names (Gig0/0 vs Gig0/1) at low zoom level.

**Resolution:** Deleted the incorrect cable, zoomed in significantly on both devices before redrawing, and carefully verified each port name in the connection popup before clicking. Confirmed the fix immediately afterward using `show cdp neighbors` on both CoreSW and EdgeR2 rather than assuming it was correct. A second cable was then added separately for EdgeR2 ↔ ISP2 (Gig0/1 to Gig0/1), correctly separating the LAN and WAN links.

---

## Issue 6: IP SLA not supported for genuine upstream-failure detection (CR15)

**Symptom:** Shutting down EdgeR1's WAN-facing interface (simulating an ISP1 outage) did not trigger failover to the backup route via EdgeR2, even though the floating static route was correctly configured with a higher administrative distance.

**Cause:** A standard static route only tracks reachability to its immediate next hop (EdgeR1's LAN-facing interface), not reachability beyond that hop to the actual ISP. Since the CoreSW–EdgeR1 LAN link was never broken (only EdgeR1's separate WAN interface was shut down), CoreSW had no way of knowing EdgeR1 could no longer reach the internet. The correct professional solution — IP SLA object tracking — was attempted but rejected by the 3560's IOS image (`12.2(37)SE1`), which does not support the `ip sla` command set.

**Resolution and design decision:** Documented this as a known limitation of the simulated hardware/IOS version rather than a design flaw. Re-scoped the failover test to the scenario a floating static route *is* correctly designed to handle: a failure of the router or link itself (e.g. EdgeR1 going down entirely, or its LAN-side link failing), which is arguably the more common real-world failure mode for CR15's resilience requirement. Verified this scenario instead by shutting down CoreSW's FastEthernet0/1 (its link to EdgeR1) directly — this correctly triggered failover, with `show ip route` confirming `Gateway of last resort` switching from `172.16.0.2` to `172.16.0.6` automatically.

---

## Issue 7: Successful route failover, but ping to the backup ISP still failed

**Symptom:** After confirming the routing table correctly failed over to EdgeR2, a ping from CoreSW to ISP2 (`203.0.113.6`) still failed with 100% loss, even though `show ip route` showed the correct path.

**Cause:** Asymmetric routing — CoreSW had a route out to each ISP, but neither ISP1 nor ISP2 (nor the edge routers themselves) had a route back to the internal `192.168.56.0/24` LAN. Return traffic (ICMP echo replies) had nowhere to go and was silently dropped.

**Resolution:** Added static routes back to `192.168.56.0/24` on both ISP1 and ISP2 (pointing to their respective edge router), and on both EdgeR1 and EdgeR2 (pointing to CoreSW). This restored full round-trip connectivity in both directions.

---

## Issue 8: Ping to backup ISP still failed from a PC, after all routing was fixed

**Symptom:** Even after confirming every router-level route was correct and CoreSW itself could successfully ping both EdgeR2 and ISP2, a ping from a PC to ISP2 still returned 100% loss.

**Cause:** `ipconfig` on the PC revealed it had never received a DHCP address at all (`0.0.0.0` on every field) — Packet Tracer's default PC configuration is Static IP, not DHCP, regardless of whether a DHCP server/pool exists on the network.

**Resolution:** Switched the PC's IP Configuration from Static to DHCP via Desktop → IP Configuration. It immediately received a valid `192.168.56.x` address with the correct gateway. The same fix was then required across all remaining PCs on the network, none of which had been switched from the default Static setting.

**Result:** With a valid DHCP-assigned address, the same ping to `203.0.113.6` succeeded with 0% packet loss — confirming full end-to-end connectivity through the CR15 failover path, from PC to backup ISP and back.
