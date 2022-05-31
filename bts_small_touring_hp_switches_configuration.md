BTS Small HP PoE Touring Switches Documentation
===============================================

The small HP PoE-capable switches are useful for a number of BTS applications. They are especially useful for large events involving the
touring racks, because the Cisco switches in the touring racks (see "touring racks" file) cannot supply PoE.

Due to their versatility, these switches do not have a particular permanent configuration setup. But a couple of common scenarios are discussed below.

Management credentials
----------------------

username: manager\
password [REDACTED]

Basic setup
-----------

In their most basic use-case, the switches' ports are set to the same VLAN. This has the effect of making the switch act as a dumb switch, allowing
traffic to be forwarded between any ports.

Example running configuration for use as a standalong dumb switch:

```
hostname "HP-2520G-8-PoE"
snmp-server community "public" unrestricted
vlan 1
   name "DEFAULT_VLAN"
   untagged 1-8
   ip address dhcp-bootp
   exit
password manager
```

Setup for use with BTS touring racks
------------------------------------

In this case, one or two ports are assigned to be trunk ports. These uplink with a trunk port on one of the main touring switches
in one of the racks. VLANs which
are desirable to be accessed/trunked through the small switch are "tagged" to the small switch's trunk port(s). Some VLANs may also be brought out on other
switch ports by "untagging" them to those particular ports.

_Only VLANs which are tagged on a trunk port of the small switch may be accesed by or trunked through that switch._

Example running configuration for touring rack use:

```
hostname "HP-2520G-8-PoE"
snmp-server community "public" unrestricted
vlan 1
   name "DEFAULT_VLAN"
   untagged 1-2
   ip address dhcp-bootp
   exit
vlan 2
   name "management"
   tagged 1-2
   ip address dhcp-bootp
   exit
vlan 34
   name "internet"
   tagged 1-2
   no ip address
   exit
vlan 50
   name "sound primary"
   tagged 1-2
   no ip address
   exit
vlan 51
   name "sound secondary"
   tagged 1-2
   no ip address
   exit
vlan 60
   name "lighting"
   untagged 3-4
   tagged 1-2
   no ip address
   exit
vlan 100
   name "video control"
   untagged 7-8
   tagged 1-2
   no ip address
   exit
vlan 101
   name "video vlan #1"
   untagged 5-6
   tagged 1-2
   no ip address
   exit
vlan 102
   name "VLAN102"
   tagged 1-2
   no ip address
   exit
vlan 103
   name "VLAN103"
   tagged 1-2
   no ip address
   exit
vlan 104
   name "VLAN104"
   tagged 1-2
   no ip address
   exit
vlan 105
   name "VLAN105"
   tagged 1-2
   no ip address
   exit
vlan 106
   name "VLAN106"
   tagged 1-2
   no ip address
   exit
vlan 107
   name "VLAN107"
   tagged 1-2
   no ip address
   exit
vlan 108
   name "VLAN108"
   tagged 1-2
   no ip address
   exit
password manager
```
