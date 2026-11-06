# Slamdad SentinelDC: Secure Enterprise Data Center

<img width="1423" height="776" alt="image" src="https://github.com/user-attachments/assets/c4d06bbc-c8d7-4e5e-81af-389cdb2a98a0" />


A comprehensive Cisco Packet Tracer lab simulating a secure enterprise data center network with DMZ segmentation, stateful firewall inspection, and SOC monitoring. 

## Architecture Overview

SentinelDC is designed with a defense-in-depth approach, utilizing a three-tier firewall architecture (Outside, Inside, DMZ) and strict VLAN segmentation to protect internal assets from external threats and lateral movement.

### Topology
- **Internet Edge:** ATTACKER (simulated external host), ISP Router
- **Perimeter:** EDGE Router, Cisco ASA 5506-X Firewall
- **Campus Core:** Layer 3 Multilayer Switch (CORE)
- **Access Layer:** Dedicated switches for Office (SW-Office), Servers (SW-Server), Security Operations (SW-SOC), and DMZ (SW-DMZ)
- **Endpoints:** Finance, HR, and IT PCs; Web, Database, File, and SIEM Servers

## Network Design

### VLAN Segmentation
- **VLAN 10:** Finance
- **VLAN 20:** Human Resources
- **VLAN 30:** IT
- **VLAN 40:** Server LAN
- **VLAN 50:** DMZ (Web Server)
- **VLAN 60:** SOC (Security Operations Center)
- **VLAN 99:** Management (Out-of-Band)

### IP Addressing & Routing
- **WAN/Edge:** Public IP space (198.51.100.0/30, 203.0.113.0/28)
- **Core:** Inter-VLAN routing via SVIs; Routed uplink to the Firewall (10.0.0.0/30)
- **DHCP:** Centralized on the CORE switch for Office and SOC VLANs (VLAN 40 is static only)
- **Firewall:** Static routes direct internal traffic to the CORE, default route points to the ISP. 

## Security & Policy Enforcement

### Firewall (ASA 5506-X)
- **Stateful Inspection:** Security levels defined for Outside (0), DMZ (50), and Inside (100).
- **NAT/PAT:** Dynamic PAT for internal subnets; Static NAT mapping the public IP `203.0.113.10` to the internal Web Server.
- **Access Control:** Strict inbound ACLs allowing only HTTP traffic to the Web Server from the internet. Explicit ACLs restricting lateral movement from the DMZ back to the internal network.

### Core Switch ACLs
Stateless extended ACLs applied inbound on the SVIs to enforce zero-trust between departments:
- **Finance:** Blocked from HR and Database.
- **HR:** Blocked from Finance and Database.
- *(All other traffic permitted to allow IT/DB/File access)*

## Hardening & Monitoring

- **Management Plane:** SSH enabled with local authentication and enable secrets. Dedicated Management VLAN (99).
- **Layer 2 Security:** Port security enabled on access ports (sticky MAC, restrict violation). Unused ports administratively shut down.
- **DHCP Snooping:** Enabled on all access switches; trunk ports explicitly trusted.
- **Logging & Time:** Centralized Syslog and NTP configured to point to the SOC SIEM server (192.168.60.10) for event correlation.

## Test Plan / Verification

The network is validated against the following policy matrix:
- [x] Internal PCs receive DHCP leases correctly.
- [x] IT can reach the Database; HR/Finance cannot.
- [x] Finance and HR can reach the File Server.
- [x] All endpoints can reach the SIEM for logging.
- [x] SOC has unrestricted visibility.
- [x] External attackers can reach the Web Server via HTTP.
- [x] External attackers are blocked from internal VLANs and the Database.
- [x] The Web Server cannot initiate connections to the internal network.
- [x] Internal users can reach public internet resources.

## Repository Contents

- `.pkt` file: The complete, fully functional Cisco Packet Tracer topology.
- `README.md`: Network documentation and policy overview.

<img width="1423" height="776" alt="image" src="https://github.com/user-attachments/assets/c7fe53bc-f164-421e-a9ed-45358b2277b4" />


