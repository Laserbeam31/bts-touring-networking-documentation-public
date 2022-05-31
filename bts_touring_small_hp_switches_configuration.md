BTS Small HP PoE Touring Switches Documentation
===============================================

The small HP PoE-capable switches are useful for a number of BTS applications. They are especially useful for large events involving the
touring racks, because the Cisco switches in the touring racks (see "touring racks" file) cannot supply PoE.

Due to their versatility, these switches do not have a particular permanent configuration setup. But a couple of common scenarios are discussed below.

Credentials:
------------

For management access

Username: manager\
Password [REDACTED]

Configuration access:
---------------------

To access the switch configuration, a number of options are available:

1. SSH            - This is the most secure method. Connect a computer to an untagged port, on a VLAN on which the switch is set to receive an IP address,                     and SSH into the switch. The IP address of the switch can be ascertained by running an IP scan;
         
2. Telnet         - This is much the same as SSH, except unencrypted;

3. Serial console - This involves connecting a serial console cable to the RJ-45 port on the left-hand side of a switch's front panel. This serial cable is                     then connected to a computer by means of an RS232 connection.
                   
Upon login, one is typically greeted with a command prompt which ends in `>`. To actuallly access useful settings from here, type `enable`.
Enter the password by the prompt which appears. Upon entry of a correct password, a command prompt appears, ending in `#`. From this command prompt, useful commands can be run:

`sh run`          - Shows the current configuration of the switch, in a user-friendly text file format. To back up the switch configuration, simply copy                       and paste the command's output into a notepad file, or some equivalent thereof.
           
 `conf t`         - Short for "configure terminal". Enters the command mode in which configuration commands may be entered, to change the switch
                    settings.
                    
 `int xx/xx`      - In configuration mode, this has the effect of selecting a specific port for more detailed access.

Note that the configuration commands for HP switches are somewhat different to those for Cisco switches. The most notable difference one is likely
to encounter is the way in which VLANs are allocated to ports and trunk lines. Whereas on a Cisco switch, a trunk port may be allocated simply
by selecting the interface and stating `switchport mode trunk`, HP switches require that VLANs be tagged to a specific port - which implicitly makes the port in question a trunk line. Untagged non-trunking "access" ports are allocated by _untagging_ VLANs to specific interfaces. For detailed example commands, see the configuration examples below.

When finishing entering configuration commands, ensure the following actions are done:

1. Run `end` followed by `reload` to ensure the settings are saved and therefore persist across the next reboot of the switch;

2. **Update the documentation according to the new configuration changes!!** At very least, the documentation should have its copy of the running
   config updated. If a major change has been made, the expolanatory must also be updated.

Basic setup:
------------

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

Setup for use with BTS touring racks:
-------------------------------------

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
