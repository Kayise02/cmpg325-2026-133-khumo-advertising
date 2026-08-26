# VLAN Design & IP Addressing Plan

## VLAN table

| VLAN ID | VLAN name | Purpose / traffic type | Assigned department |
|---|---|---|---|
| 10 | Admin-Data | Management and administration PCs (data) | Management / Admin |
| 20 | Creative-Data | High-bandwidth design and rendering workstations (data) | Creative / Design |
| 30 | Production-Data | Logistics and operations PCs (data) | Production / Logistics |
| 40 | Voice (VoIP) | Dedicated IP phone traffic (fulfils the design constraint) | Management / Admin |
| 50 | Wireless | WAP management and client traffic (WPA2-PSK) | Creative / Design & meeting rooms |
| 99 | Native / Mgmt | Native VLAN for trunking and switch management | Network infrastructure |

## Logical segmentation strategy

- **Data segregation:** VLANs 10, 20, and 30 keep departmental data traffic separate, reducing unnecessary broadcast traffic between departments and allowing department-specific security policies.
- **Voice separation (constraint):** VLAN 40 is dedicated exclusively to IP phones in Management/Admin. This isolates voice traffic from office PC data traffic and supports future QoS prioritisation, directly fulfilling the mandatory design constraint.
- **Wireless isolation:** VLAN 50 is dedicated to the Wireless Access Point, separating wireless clients from the wired network. This isolates the WPA2-PSK security domain and simplifies management of wireless clients — the assigned networking challenge is configured and demonstrated on this VLAN.
- **Core routing:** the Layer 3 core switch, using SVIs, acts as the gateway for every VLAN and routes between departments where required.

## IP addressing plan

The assigned block `192.168.56.0/24` is subnetted into `/27` blocks (30 usable hosts each), giving sufficient capacity for each VLAN with two blocks reserved for future growth.

| VLAN | Name | Subnet | Usable range | Gateway (SVI) |
|---|---|---|---|---|
| 10 | Admin-Data | 192.168.56.0/27 | .1 – .30 | 192.168.56.1 |
| 20 | Creative-Data | 192.168.56.32/27 | .33 – .62 | 192.168.56.33 |
| 30 | Production-Data | 192.168.56.64/27 | .65 – .94 | 192.168.56.65 |
| 40 | Voice (VoIP) | 192.168.56.96/27 | .97 – .126 | 192.168.56.97 |
| 50 | Wireless | 192.168.56.128/27 | .129 – .158 | 192.168.56.129 |
| 99 | Native/Mgmt | 192.168.56.160/27 | .161 – .190 | 192.168.56.161 |
| — | Reserved | 192.168.56.192/27, .224/27 | — | future expansion |

### WAN links

WAN-facing links use addressing outside the assigned `/24`, since ISP handoffs are provisioned by the ISP rather than drawn from the customer's internal block.

- Edge Router 1 ↔ ISP 1: `203.0.113.0/30` (router-side address: `.1`)
- Edge Router 2 ↔ ISP 2: `203.0.113.4/30` (router-side address: `.5`)

### Trunk configuration per access switch

| Access switch | VLANs carried | Native VLAN |
|---|---|---|
| Mgmt/Admin switch | 10 (data), 40 (voice) | 99 |
| Creative switch | 20 (data), 50 (wireless) | 99 |
| Production switch | 30 (data) | 99 |

### Resilience routing (CR15)

- **Default route via Edge Router 1** (primary ISP) — administrative distance 1.
- **Floating static default route via Edge Router 2** (backup ISP) — higher administrative distance, activating automatically if the primary route disappears.
