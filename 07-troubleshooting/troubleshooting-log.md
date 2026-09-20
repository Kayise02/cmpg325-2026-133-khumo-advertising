
---

## Issue 9: Third IP phone never appeared in the DHCP binding table

**Symptom:** After confirming two of the three IP phones on SW-Admin had successfully received DHCP addresses in the Voice VLAN range, the third phone never appeared in `show ip dhcp binding` on CoreSW, despite being physically connected and powered.

**Diagnosis:** CoreSW's console log showed repeated `%DHCPD-4-PING_CONFLICT` messages referencing addresses `192.168.56.102` and `192.168.56.103` — indicating the DHCP server's conflict-detection ping (a standard Cisco DHCP safety check before offering an address) had received a reply from something already using those addresses, so it withheld the offer.

Ruled out a duplicated-device MAC address as the cause by cross-referencing `show mac address-table` on SW-Admin against the DHCP binding table — all three phones showed genuinely distinct MAC addresses (`0006.2a5d.0720`, `00e0.f74a.69a1`, `00d0.d369.0259`), each correctly mapped to its own switch port (Fa0/5, Fa0/6, Fa0/7). This confirmed the conflict was a transient timing issue during initial boot-time DHCP negotiation (multiple devices requesting leases in close succession), not a configuration or duplication fault.

**Resolution:**
1. Cleared the stale conflict record: `clear ip dhcp conflict *` on CoreSW.
2. Forced the affected phone to re-request a lease by unplugging and reconnecting its power adapter module (Cisco IP phones in Packet Tracer have no manual power switch, so cycling the power source itself is the equivalent of a restart).
3. Re-checked `show ip dhcp binding` after allowing time to boot — the phone successfully obtained `192.168.56.102`, bringing the total to three valid voice-VLAN leases (`.102`, `.103`, `.104`), matching all three physical phones.

**Verification:** `show ip dhcp binding` on CoreSW confirmed all three phone MAC addresses present with valid leases in the `192.168.56.97–126` (VLAN 40, Voice) range, alongside all PC and wireless client leases in their respective VLAN ranges — confirming full DHCP coverage across every department and traffic type on the network.

---

## Post-implementation hardening

After core functionality (VLANs, routing, CR15, WPA2-PSK) was fully verified, the following hardening measures were added across all six network devices to move beyond the minimum requirement:

- **Administrative security:** `enable secret`, console password, and VTY password configured on every router and switch (previously, devices had no administrative password at all — a meaningful gap for a production-representative design).
- **Login banners:** a `banner motd` warning added to every device, consistent with standard professional network documentation practice.
- **Port security:** applied to all 12 end-device-facing access ports across the three access switches (`switchport port-security`, maximum 1 MAC address, sticky learning, restrict violation mode) — locking each port to the first device it learns and preventing an unauthorised device from being connected to an existing data/voice/wireless port without deliberate reconfiguration.

These were deliberate additions beyond the brief's explicit requirements, intended to reflect a more complete and defensible security posture for a real deployment.
