Documentation for the BTS Touring Rack Network Switches
=======================================================

The managed BTS switches use VLANs (Virtual Local Area Networks) to logically segregate traffic. Each VLAN can be thought of as its own virtual network. For 
example, unless you deliberately bridge VLAN 50 and 60, devices on VLAN 50 can all see each other but can't see anything on V60 even though they're both using
the same switches and cable.

When data enters the network at a port on a switch, the port is configured to 'tag' it as belonging to a particular VLAN. This tagged traffic can be passed 
to any other switch that works with IEEE 802.1q ('dot1q'), even if the switch is from a different manufacturer. These inter-switch 802.1q links are commonly
termed "trunks". When the traffic leaves the switch by a port that isn't connected to another switch, the switch removes the VLAN tag and this has the benefit
of ensuring more basic devices aren't confused by it.

VLANs on the switches:
----------------------

The VLANs currently assigned within BTS are as follows. Note that BTS also have VLAN 400 on the BUCS network; this is provisioned on request and should 
be considered entirely separately to these numbers:

|No.|Usage|
|---|---|
|1|Default VLAN on some switches - often treated uniquely by switches (e.g. unique limitations), so avoid using|
|2|Switch management (ssh or web portal access)|
|34|Internet (mnemonic: rule 34 of the Internet)|
|50|Sound primary (mnemonic: 5 ~= S)|
|51|Sound secondary|
|52-59|Reserved for future sound use. Not configured on switches.|
|60|Lighting (mnemonic: LX == 60 in Roman numerals)|
|61-69|Reserved for future lighting use. Not configured on switches.|
|100|Video control (i.e. web interfaces for projectors, matrixers, etc)|
|101-108|Video isolated baluns|
|109-119|Reserved for future video use. Not configured on switches.|
|201|General usage (e.g. touring FOH engineer wants a connection to stage, or Event Control want to hop over our network)|
|203-219|Reserved for future general use. Not configured on switches.|

Each VLAN is configured to receive DHCP from the EdgeRouter in the master network rack. The EdgeRouter also provides routing between VLANs and 
networks, where necessary. For more information, see the "touring_edgerouter" file.

Trunk link configuration:
-------------------------

The trunk links between the touring switches carry combined traffic for multiple VLANs between each switch/rack. When network trafffic
enters the switch on a trunk connection, it is sent to its relevant VLAN. Likewise, when traffic from a specific VLAN leaves the switch
on a trunk link, it is tagged as belonging to a specific VLAN before being sent along a trunk, so that receiving switches know to which VLAN to allocate 
the traffic, thus preventing it from ending up on an incorrect other VLAN.

The touring racks use _two_ gigabit trunk lines: A and B. Each trunk is responsible for carrying ("trunking") a different selection of VLANs, thus minimising 
load per trunk. Each switch in each rack therefore includes trunk connections for _Trunk A_ and _Trunk B_. If only one trunk is connected, not all VLANs will 
transmit between switches.

To connect up trunk links in their most basic configuration, loop in and out of each switch on trunk ports A and B in a linear switch-to-switch 
configuration:

Switch 1 (Trunk A ports) -> Switch 2 (Trunk A ports) -> Switch 3 (Trunk A ports) -> [etc in a daisy-chain configuration through all switches] 

Switch 1 (Trunk B ports) -> Switch 2 (Trunk B ports) -> Switch 3 (Trunk B ports) -> [etc in a daisy-chain configuration through all switches]

Below is a diagram of a basic linear connection of the racks. The purple links represent the standing patch cables between the two switches in each rack; 
the black links represent long trunk cables between each rack. Each trunk has its own allocated ports on each switch:

 ![DAGs](https://github.com/Laserbeam31/bts-touring-networking-documentation-public/blob/main/linear_network_rack_connection.PNG)

It is important that each switch receive a separate trunk line for Trunk A and Trunk B. Failure to run both trunks will result in some VLANs being
inaccessible

For the sake of redundancy, both trunk links A and B are configured with provision for _Multiple Spanning Tree Protocol_ (MSTP). In essence, MSTP 
prevents loops of identical trunk lines from becoming active on a network, only allowing certain sections of any loop to be active at any one time,
and only switching over to different parts of the loop if the original sections become unusable. This allows for more than one trunk cable to be run
between two or more pairs of identical trunk ports on two or more switches. If one of these trunk cables breaks, the switches automatically change
over to using another physical trunk link, thus minimising downtime. MSTP ensures that no more than one physical trunk link out of a redundant 
inter-switch set is used at any one time. Having more than one identical trunk link simultaneously active between the same set of switches would
result in a _broadcast storm_ whereby packets flow indefinitely through the duplicate links and inhibit the switches' ability to pass traffic at all.

To connect up the trunk links in a redundant loop, loop in and out of each switch on trunk ports A and B, in a circular rather than linear
configuration:

Switch 1 (Trunk A ports) -> Switch 2 (Trunk A ports) -> Switch 3 (Trunk A ports) -> [etc through all switches] -> Switch 1 (Trunk A ports) 

Switch 1 (Trunk B ports) -> Switch 2 (Trunk B ports) -> Switch 3 (Trunk B ports) -> [etc through all switches] -> Switch 1 (Trunk B ports)

Below is a diagram of how the racks may be connected for redundant operation:

 ![DAGs](https://github.com/Laserbeam31/bts-touring-networking-documentation-public/blob/main/redundant_network_rack_connection.PNG)

As an example, if Trunks A and B are connected in the above redundant loop configuration, one section of each trunk's "loop" becomes disabled automatically 
by the switches. This means that the only active trunk path for both A and B is linear (much like the above basic linear example). If, however, an active 
section of (for example) Trunk A becomes broken, the switches will automatically activate the previously-deactivated section and switch to sending Trunk 
A's traffic down this instead.

It is worth noting that each switch has a few _Local Trunk_ ports. These are trunk ports which, unlike Trunk A or Trunk B, are each trunking _all_ VLANs. 
These should not be used for inter-rack trunking, but are only present for connecting devices such as the master rack's EdgeRouter (see
_bts_touring_edgerouter_details.md_) and the touring WiFi access points (see _bts_touring_cisco_ap_details.md_) which require low-bandwidth access to all 
VLANs.

Untagged link configuration:
----------------------------

As per the touring racks' labelling, groups of ports are assigned particular departmental VLANs. The process whereby ports are allocated from being a
trunk link to belonging to a specific VLAN is known as _untagging_. Ports which are untagged - as the name suggests - remove the VLAN tag from their
outgoing traffic, and apply it to their incoming traffic.

The spanning tree settings for untagged ports are a little different from those of the _tagged_ trunk links. This is because trunk links are often (and 
indeed, should) be connected up in a redundant topology to improve resilience, whereas this is far less common in the case of untagged ports. This means 
that untagged ports are configured with the _portfast_ spanning tree setting enabled. The portfast setting bypasses some of the initial spanning tree 
negotiation stages (namely _listening_ and _learning_) such that, upon connection of a client device, the Ethernet link comes up instantly. Only if a loop is 
_subsequently_ detected will an untagged port shut itself down to avoid a broadcast storm. This is in contrast to the behaviour of a trunk port: a trunk 
port, upon connectiion of an inter-switch uplink, initially holds off from activating the link until it is certain that a problematic loop is not 
present. The advantage of enabling portfast is that it allows newly-connected links to come up faster; the disadvantage is that, since it bypasses the 
initial negotiation stages of the connection, it increases the chance of a momentary loop and associated broadcast storm.

Configuration access:
---------------------

To access the switch configuration, a number of options are available:

1. SSH            - This is the most secure method. Connect a computer to the "management" port on a touring switch, and SSH into the switch. The IP
                    addresses of the switches can be ascertained by running an IP scan;
         
3. Telnet         - This is much the same as SSH, except unencrypted;

4. Serial console - This involves connecting a Cisco serial console cable to the RJ-45 port on the _back_ of a touring switch. This serial cable is then
                    connected to a computer by means of an RS232 connection. A serial console session can then be opened on the host computer
                    using a program such as PuTTY or Minicom.
                    
Upon login, one is typically greeted with a command prompt which ends in `>`. To actuallly access useful settings from here, type `enable`.
Enter the password by the prompt which appears. Upon entry of a correct password, a command prompt appears, ending in `#`. From this command
prompt, useful commands can be run:

`sh run`          - Shows the current configuration of the switch, in a user-friendly text file format. To back up the switch configuration, simply copy                                          and paste the command's output into a notepad file, or some equivalent thereof.
           
 `conf t`         - Short for "configure terminal". Enters the console mode in which _configuration_ commands may be entered, to change the switch
                    settings.
                    
When finishing entering configuration commands, ensure the following actions are done:

1. Run `end` followed by `copy running-config startup-config` to ensure the settings changes are saved and therefore persist across the next reboot of the switch;

2. Update the documentation, if any significant permanent change has been made.
