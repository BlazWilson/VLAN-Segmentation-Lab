# VLAN-Segmentation-Lab

Overview

This lab demonstrates the design, implementation, and troubleshooting of a small enterprise-style network. The goal was to segment departments using VLANs, enable controlled communication between them, and secure traffic using a Cisco ASA 5505 firewall.

This project intentionally started simple and evolved through troubleshooting, which resulted in a deeper understanding of VLANs, routing, and firewall behavior.

Network Design
<img width="898" height="325" alt="image" src="https://github.com/user-attachments/assets/0d930c4b-4c29-4d51-9f53-a4c2cf40fb4c" />


Departments

HR → VLAN 10 → 192.168.10.0/24
IT → VLAN 20 → 192.168.20.0/24

Devices Used

1 × Cisco Switch (VLAN segmentation)
1 × Cisco Router (inter-VLAN routing)
1 × Cisco ASA 5505 (firewall)
2 × PCs (end users)

Logical Flow

PCs → Switch (VLANs) → Router (Routing) → ASA Firewall → Outside Network

What I Implemented

VLAN Segmentation (Switch)
Created VLAN 10 (HR) and VLAN 20 (IT)
Assigned PC switch ports as access ports (any traffic that comes to this port belongs to X department)
Verified VLAN isolation (HR and IT could not communicate initially)

Trunking & Inter-VLAN Routing
Configured a trunk link between the switch and router. (allows multi vlan traffic on one cable by tagging the traffic and telling it where to go)
Implemented router-on-a-stick using subinterfaces:

VLAN 10 → 192.168.10.1
VLAN 20 → 192.168.20.1
Set correct default gateways on PCs
Verified successful inter-VLAN communication

Firewall Placement (ASA 5505)
*Placed the ASA between the router and the outside network
Configured:

Inside security zone
Outside security zone
Implemented basic NAT for outbound traffic
Verified connectivity through the firewall

What Went Wrong (And Why)

As simple as this lab looks, it did not come without its fair share of troubles.
Issue 1: Treating the ASA Like a Router

Initially tried assigning IP addresses directly to ASA Ethernet ports
Learned that ASA 5505 uses VLAN interfaces, not routed ports
Physical Ethernet ports act as switchports

Issue 2: Missing nameif

VLAN interfaces without nameif do not function on an ASA
Even with IP addresses configured, traffic failed
Learned that nameif defines security zones and enables routing/NAT

Issue 3: ASA 5505 License Limitation

The base ASA 5505 license only allows two active interfaces with nameif
An unused VLAN (VLAN 1) consumed a license slot
This prevented assigning nameif inside to the correct VLAN

How I Troubleshot the Problems

Verified interface state using show run interface vlan X

Identified which VLANs had nameif assigned

Removed the unused VLAN 1 configuration

Correctly assigned:

VLAN 2 → inside
VLAN 3 → outside

Retested connectivity incrementally using ICMP (ping)

Once licensing and interface roles were corrected, traffic flowed successfully.

Verification & Results

VLAN isolation verified before routing
Inter-VLAN routing tested successfully
Router-to-firewall connectivity confirmed
Final ICMP tests returned 5/5 successful replies

Key Lessons Learned

VLANs isolate traffic by default
Trunking allows multiple VLANs to share a single cable
Inter-VLAN routing requires a Layer 3 device (router, layer 3 switch, or firewall with routing capabilities)
ASA 5505 uses VLAN interfaces not routed Ethernet ports
nameif is mandatory for ASA interface functionality
Firewall licensing can directly impact configuration options
Troubleshooting/ trial and error are the best teachers

Tools & Technologies

Cisco Packet Tracer
VLANs
802.1Q Trunking
Inter-VLAN Routing
Cisco ASA 5505
ICMP Testing

Final Notes

The troubleshooting process was the most valuable part of the project as it deepened my understanding of certain topics like trunking and inter routing and significantly improved my confidence working with networking and firewall technologies. All in all it was fun. :)
