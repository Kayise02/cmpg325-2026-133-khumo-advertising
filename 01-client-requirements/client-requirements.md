# Client Requirements

**Organisation:** Khumo Advertising Billboards (Vryburg)
**Industry:** Media
**Project ID:** CMPG325-2026-133 · **Client ID:** CLI-133

## Requirements specified in the project brief

- Assigned organisation: Khumo Advertising Billboards (Vryburg)
- Industry: Media
- Assigned addressing block: `192.168.56.0/24`
- Client change request (**CR15**): a second internet connection is added for resilience and must be integrated.
- Design constraint: VoIP traffic must be separated from data traffic.
- Assigned networking challenge: Wireless Security (WPA2-PSK hardening; Enterprise optional extension) — to be configured, verified, and demonstrated within the client network.
- Network design requirements: implement in Cisco Packet Tracer; design an appropriate topology; configure all necessary routers, switches, and end devices; demonstrate successful connectivity and testing.

## Engineering assumptions

The project brief does not specify organisational detail such as departments, user counts, or device placement. Where this information is not provided, the following network-engineering assumptions have been made, each justified against the assigned scenario.

### Departments

The brief identifies the client as a Media-industry organisation. Three logical departments are assumed to reflect a realistic small billboard advertising business: **Management/Admin** (administration and client liaison), **Creative/Design** (billboard content design), and **Production/Logistics** (installation and maintenance coordination).

### Users and devices

An SMB environment of approximately 30 users is assumed, distributed across desktop workstations, IP phones, and wireless-capable devices. This reflects a single-site office rather than a multi-branch enterprise, consistent with the brief not indicating additional locations.

### Wireless

Wireless access is assumed to be required for the Creative/Design department and shared meeting rooms, served by a dedicated Wireless Access Point. This gives a concrete, defensible location for the assigned WPA2-PSK hardening challenge and reflects the mobility needs of design staff moving between workstations and meeting spaces.

### VoIP

To satisfy the design constraint separating VoIP traffic from data traffic, IP phones are assumed to be deployed in the Management/Admin department only, on a dedicated Voice VLAN. Management/Admin is the natural point of client contact for billboard bookings and enquiries, so voice traffic is concentrated there rather than distributed across every department. Creative/Design and Production/Logistics are assumed to rely on data services only, keeping the voice footprint small and clearly scoped, which is appropriate for a business of this size.

### Redundancy (CR15)

A dual-homed ISP setup — two independent edge routers, each terminating a separate internet connection — is assumed to satisfy the change request for resilience. This allows the network to fail over automatically if the primary connection is lost, keeping cloud-based scheduling, design, and communication tools available.
