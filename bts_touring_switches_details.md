Configuration of the BTS Touring Network Switches
=================================================

The managed BTS switches use VLANs to logically segregate traffic into virtual networks. Each VLAN can be thought of as its own virtual network. For example, unless you deliberately bridge VLAN 50 and 60, devices on V50 can all see each other
but can't see anything on V60 even though they're both using the same switches and cable.

When data enters the network at a port on a switch, the port is configured to 'tag' it as belonging to a particular VLAN. This tagged traffic can be passed to any other switch that works with IEEE 802.1q ('dot1q'), even if the switch is from a different manufacturer. When the traffic leaves the switch by a port that isn't connected to another switch, the switch removes the VLAN tag and this has the benefit of ensuring more basic devices aren't confused by it.

VLANs on the switches
---------------------

The VLANs currently assigned within BTS are as follows. Note that BTS also have VLAN 400 on the BUCS network; this is provisioned on request and should be considered entirely separately to these numbers:

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
|201-202|General usage (e.g. touring FOH engineer wants a connection to stage, or event control want to hop over our network)|
|203-219|Reserved for future general use. Not configured on switches.|

\* 'Not configured on switches' refers to switches which require VLANs to be explicitly configured on the switch in order to pass through traffic on it - some switches can be configured to allow all VLANs to pass through.

Each VLAN is configured to receive DHCP from the EdgeRouter in the master network rack. The EdgeRouter also provides routing between VLANs and networks,
where necessary.
For more information, see the "touring_edgerouter" file.

Trunk link configuration
------------------------

The trunk links between the touring switches in all the racks carry combined traffic for all VLANs between each rack. When network trafffic
enters the switch on a trunk connection, it is sent to its relevant VLAN. Likewise, when traffic from a specific VLAN leaves the switch
on a trunk link, it is tagged as belonging to a specific VLAN, so that receiving switches know to which VLAN to allocate the traffic, thus
preventing it from ending up on an incorrect other VLAN.

For the sake of redundancy, the trunk links are configured with _Rapid Spanning Tree Protocol_ (RSTP). 
In essence, RSTP prevents loops of trunk lines becoming active on a network - only allowing certain sections of
any loop to be active at any one time, and only switching over to different parts of the loop if the
original sections become unusable. This allows for more than one
trunk cable to be run between two or more pairs of trunk ports on two or more switches. 
If one of these trunk cables breaks, the switches automatically change
over to using another physical trunk link, thus minimising downtime. RSTP ensures that no more than one physical trunk link out of a
redundant inter-switch set is used at any one time. Having more than one trunk link active at any one time between the same set of switches
would result in a _broadcast storm_ whereby traffic flows indefinitely through the duplicate links and inhibits the switches' ability
to pass traffic at all.

Untagged link configuration
---------------------------

As per the touring racks' labelling, groups of ports are assigned particular departmental VLANs. The process whereby ports are allocated from being a
trunk link to belonging to a specific VLAN is known as _untagging_. Ports which are untagged - as the name suggests - remove the VLAN tag from their
outbound traffic, and apply it to their inbound traffic.

The spanning tree settings for untagged ports are a little different from those of the _tagged_ trunk links. This is because trunk links are often (and indeed, should) be connected up in a redundant topology to improve resilience, whereas this is far less common in the case of untagged ports. This means that untagged ports are configured with the _portfast_ spanning tree setting enabled. The portfast setting bypasses some of the initial spanning tree negotiation stages (namely Listening and Learning) such that, upon connection of a client device, the Ethernet link comes up instantly. Only if a loop is _subsequently_ detected will an untagged port shut itself down to avoid a broadcast storm. This is in contrast to the behaviour of a trunk port: a trunk port, upon connectiion of an inter-switch trunk link, initially holds off from activating the link untiil it is certain that a problematic loop is not present. The advantage of enabling portfast is that it allows newly-connected links to come up faster; the disadvantage is that, since it bypasses the initial negotiation stages of the connection, and only subsequently shuts off a port if a redundant loop is detected, it increases the chance of a momentary broadcast loop.

Configuration access
--------------------

To access the switch configuration, a number of options are available:

1. SSH            - This is the most secure method. Connect a computer to the "management" port on a touring switch, and SSH into the switch. The IP                           addresses of the switches can be ascertained by running an IP scan;
         
2. Telnet         - This is much the same as SSH, except unencrypted;

3. Serial console - This involves connecting a serial console cable to the RJ-45 port on the _back_ of a touring switch. This serial cable is then
                    connected to a computer by means of an RS232 connection.
                    
Upon login, one is typically greeted with a command prompt which ends in `>`. To actuallly access useful settings from here, type `enable`.
Enter the password by the prompt which appears. Upon entry of a correct password, a command prompt appears, ending in `#`. From this command prompt, useful commands can be run:

`sh run`          - Shows the current configuration of the switch, in a user-friendly text file format. To back up the switch configuration, simply copy                       and paste the command's output into a notepad file, or some equivalent thereof.
           
 `conf t`         - Short for "configure terminal". Enters the command mode in which configuration commands may be entered, to change the switch
                    settings.
                    
When finishing entering configuration commands, ensure the following actions are done:

1. Run `end` followed by `reload` to ensure the settings are saved and therefore persist across the next reboot of the switch;

2. **Update the documentation according to the new configuration changes!!** At very least, the documentation should have its copy of the running
   config updated. If a major change has been made, the expolanatory must also be updated.

Running configuration
---------------------

```
Current configuration : 9185 bytes
!
! Last configuration change at 19:47:36 UTC Tue Mar 2 1993 by admin
!
version 15.0
no service pad
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
!
hostname bts-cisco-48-2
!
boot-start-marker
boot-end-marker
!
enable secret 5 [REDACTED]
!
username admin password 7 [REDACTED]
aaa new-model
!
!
!
!
!
!
!
!
aaa session-id common
system mtu routing 1500
ip routing
ip domain-name bts-crew.com
!
!
!
!
crypto pki trustpoint TP-self-signed-[REDACTED]
 enrollment selfsigned
 subject-name cn=IOS-Self-Signed-Certificate-[REDACTED]
 revocation-check none
 rsakeypair TP-self-signed-[REDACTED]
!
!
crypto pki certificate chain TP-self-signed-[REDACTED]
 certificate self-signed 01
  [CERTIFICATE DETAILS REDACTED]
        quit
!         
!         
!         
!         
!         
!         
spanning-tree mode rapid-pvst
spanning-tree extend system-id
!         
vlan internal allocation policy ascending
lldp run  
!         
ip ssh version 2
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
interface GigabitEthernet0/1
 switchport access vlan 101
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/2
 switchport access vlan 102
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/3
 switchport access vlan 103
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/4
 switchport access vlan 104
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/5
 switchport access vlan 105
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/6
 switchport access vlan 106
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/7
 switchport access vlan 107
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/8
 switchport access vlan 108
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/9
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/10
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/11
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/12
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/13
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/14
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/15
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/16
 switchport access vlan 100
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/17
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/18
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/19
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/20
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/21
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/22
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/23
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/24
 switchport access vlan 60
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/25
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/26
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/27
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/28
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/29
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/30
 switchport access vlan 50
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/31
 switchport access vlan 51
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/32
 switchport access vlan 51
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/33
 switchport access vlan 34
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/34
 switchport access vlan 34
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/35
 switchport access vlan 34
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/36
 switchport access vlan 34
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/37
 switchport access vlan 201
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/38
 switchport access vlan 201
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/39
 switchport access vlan 202
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/40
 switchport access vlan 202
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter disable
!         
interface GigabitEthernet0/41
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/42
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/43
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/44
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/45
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/46
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/47
 switchport trunk encapsulation dot1q
 switchport mode trunk
 spanning-tree portfast
!         
interface GigabitEthernet0/48
 switchport access vlan 2
 switchport mode access
 spanning-tree portfast
 spanning-tree bpdufilter enable
!         
interface GigabitEthernet0/49
!         
interface GigabitEthernet0/50
!         
interface GigabitEthernet0/51
!         
interface GigabitEthernet0/52
!         
interface Vlan1
 no ip address
!         
interface Vlan2
 ip address 10.0.2.2 255.255.0.0
!         
ip http server
ip http secure-server
!         
!         
!         
!         
!         
!         
line con 0
line vty 0 4
 transport input ssh
line vty 5 15
!         
end    
```
