BTS Touring Master Rack EdgeRouter Documentation
================================================

The BTS FoH master touring rack contains a Ubiquiti EdgeRouter. This performs both as a DHCP server for the touring racks' VLANs (see _bts_touring_switches_details.md_),
and also as a router and firewall.

Terminology:
------------

`WAN` =   Wide Area Network. In this context, it is the name of a router's interface which faces a higher-level public network. In the case of a home router, it is the
          port which connects to the DSL/cable connection from the street. In the case of the touring EdgeRouter, it is the interface which links the
          touring networks (specifically, the Internet VLAN; see the _bts_touring_switches_details.md_ file) to the wider university network so that
          internet access from within the touring network is possible.
          
`LAN` = Local Area Network. In this context, it is the name of a router's interface which faces the internal, local, network for which it is responsible. In the case
        of a home router, it would correspond to the internal wireless antenna to which all devices are connected. In the case of larger networks,
        such as those in the BTS touring racks, it is common for the LAN to actually consist of multiple small networks (often implemented as
        VLANs, trunked within a physical interface), all on the router's
        local trusted side. Having multiple separate networks allows for for more granular control of what each network can and can't access.
        
`Firewall`  = A means of regulating network traffic between devices and networks.

Configuration accessibility:
----------------------------

It is easiest to configure the EdgeRouter from its web interface. Connect an ethernet cable between a management switch port (see _bts_touring_switches_details.md_ file)
and a computer, wait for the computer to receive a DHCP lease, and enter the IP address `10.0.2.254` into the computer's web browser. A login prompt apppears. The credentials are as follows:

Username: [REDACTED]\
Password: [REDACTED]

Upon successful login, one is greeted with an overall management page. Whilst a little unintuitive, the various tabs along the top right 
of the management page allow for various aspects of the router's functionality to be configured.

**IMPORTAANT: The EdgeRouter is arguably the most important piece of kit in the touring setup. Without it in a functional state, the touring VLANs
would have no routing and no DHCP server. It is therefore imperative that configuration changes be documented here when they are made, and that 
no changes be made without sufficient time to test their ramifications in a non-show-critical environment.**

Interface assignments:
----------------------

The following physical interfaces on the EdgeRouter are set to correspond to specific networks:

- `Eth2`  = WAN. This connects to the BUCS university campus network.

- `Eth0`  = LAN. In the router's software, this is further split into VLANs - each corresponding to one on the touring switches ( "touring_racks"
            file). A VLAN on Eth0 is designated in the EdgeRouter UI as `Eth0.xx`, where xx is the VLAN ID.

Firewall:
---------

The EdgeRouter has firewall functionality built into it. This means that it can allow or block traffic between networks (both physical or VLAN-based).
Using "default block incoming" policies, the configuration is set such that local inter-VLAN traffic (e.g. between LX, Sound, Video networks) is 
prohibited, and only traffic from the Internet VLAN is allowed through to the router's WAN interface. Uninitiated traffic into the WAN connection 
(i.e. that which isn't in reponse to any outgoing connection established from the local side of the router) interface is blocked by default.

The only inter-VLAN exception, in terms of routing, is an "allow" rule for traffic from the VID CTRL VLAN (VLAN 100) to the MGMT VLAN (VLAN 2). This
is for the sake of convenience, so that the head of Video (who's also generally tasked with looking after the network as a whole) can access management
links for the switches/APs without having to plug the Video PC(s) (which would normally be on VID CTRL VLAN switch ports) into physically different 
switch ports to make these management connections.

DHCP server:
------------

A DHCP server instance is configured for most VLANs on the LAN interface. This allows devices connected, through the touring switches, to automatically
negotiate an IP address. DHCP address ranges are restricted to allow certain address ranges to always remain free for devices which require a self-assigned
static IP address.

Note that not all VLANs have DHCP servers active on them. This is either because certain VLANs simply don't need them (e.g. the 101-106 VID ISOL VLANs 
for cheap multicast baluns) or because some VLANs operate more reliably over link-local IP addressing (e.g. Sound 2), which is negotiated by the end 
devices themselves.

Examples of devices needing/using a self-assigned static IP address are as follows:

- BirdDog IP NDI baluns;
- BTS ArtNet node;
- Older pieces of video gear;
- Any item which is also used in a router-less networking setup elsewhere.

The following subnets are configured:

- `10.10.201.0/24`  = General VLAN. No DHCP server.
- `10.10.34.0/24`   = Internet VLAN. DHCP server lease pool: `10.10.34.101-10.10.34.199`.
- `10.10.60.0/24`   = Lighting VLAN. DHCP server lease pool: `10.10.60.101-10.10.60.199`.
- `10.10.50.0/24`   = Sound VLAN 1. DHCP server lease pool: `10.10.50.101-10.10.50.199`.
- `10.10.51.0/24`   = Sound VLAN 2. No DHCP server.
- `10.0.2.0/24`     = Switch Management VLAN. DHCP server lease pool: `10.0.2.101-10.0.2.199`.
- `10.10.10x.0/24`  = Video Balun VLAN 10x. No DHCP server.
- `10.10.100.0/24`  = Video Control VLAN. DHCP server lease pool: `10.10.100.101-10.10.100.199`.

NAT:
----

A NAT "masquerade" rule is set on the ETH2 WAN interface to allow all traffic emerging from it to appear as though it is coming from the router itself,
rather than from the various local network devices from which is actually emanating.
