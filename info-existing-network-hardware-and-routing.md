## Existing Networking Hardware

1. Mikrotik CRS504-4XQ

2. Mikrotik CRS312-4C-8XG

3. QNAP QSW-M2116P-2T2S

4. Ubiquiti U6-ENTERPRISE-US (3x)

5. Ubiquiti USW-PRO-24

6. Mokerlink 8 x 2.5G Ethernet + 10G SFP Switch (2x)

## Current Virtual Local Network Config

Totally okay to change this; not married to it.

| VLAN ID | Network CIDR    | Purpose/Description                                       |
| ------- | --------------- | --------------------------------------------------------- |
| 1       | 192.168.1.0/24  | Default/User Network                                      |
| 99      | 192.168.99.0/24 | BGP Management/Neighbor Network                           |
| 1016    | 10.16.0.0/24    | ESXi Distributed Switch Network 1                         |
| 1032    | 10.32.0.0/24    | ESXi Distributed Switch Network 2                         |
| 1048    | 10.48.0.0/24    | ESXi Distributed Switch Network 3 (Unused/NotImplemented) |
| 1064    | 10.64.0.0/24    | ESXi Distributed Switch Network 4 (Unused/NotImplemented) |
| 3991    | 0.0.0.0/0       | Verizon 5G WAN                                            |
| 3992    | 0.0.0.0/0       | Comcast Xfinity WAN                                       |

## Current Routing

Double Router-on-a-Stick setup where Comcast Xfinity WAN is connected to QNAP
Switch on VLAN 3992 and Verizon 5G WAN is connected to Ubiquiti USW-PRO-24 on
VLAN 3991. Both the QNAP and Ubiquiti switches are each connected via two
separate but bonded 10G fiber connections to the Mikrotik CRS504-4XQ.

Each VLAN above (3991-3992) is connected to its own virtualized OPNSense
router running on two separate ESXi hosts. Each host (there are three; one
doesn't have any routing responsibility presently) is connected to the CRS504
via two separate but boneded 25G fiber connections.

The router that handles the 10.16.0.0/24 CIDR range also handles the 192.168.1.0/24 CIDR range (everyday
household internet, management, etc). Three Ubiquiti U6E Access Points, all
connected via 2.5G to and powered by PoE from the QNAP switch. The Ubiquiti Pro
24 switch primarily handles 1G traffic between wired devices, raspberry pis,
etc.

The CRS312 is connected to the CRS504 via two separate but bonded 10G fiber
connections. Presently, the only downstream client served by the CRS312 is my
personal PC which is connected via 10G ethernet to the switch. It had been
planned that each ESXi host above would eventually be connected to the CRS312
via two separate, but bonded 10G ethernet (consuming VLANs 1048 and 1064) for
vSAN and other cluster maitenance activities (or other things). However, that
plan never came to fruition.A fourth ESXi host was also planned and would be
connected in a similar fashion; however, that machine is still being built.

Virtual Machines on VLAN 1016 access the internet primarily via the Comcast Xfinity
gateway. Virtual Machines with network interfaces on the VLAN 1032 access the
internet primarily via the Verizon 5G gateway. These networks are "virtually"
isolated; however, (very naive) BGP is setup between the two OpnSense routers to provide
LAN-based routing for machine-to-machine connections on the otherwise isolated
VLANs and to provide fallback internet access in the case of an outage.
