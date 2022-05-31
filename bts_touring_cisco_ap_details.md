Documentation for the BTS Cisco 3702 Touring APs
================================================

The BTS 3702 APs are set up to be able to access VLANs for sound (Dante Primary), lighting (LX),
video control, and internet through a set of different SSID, each mapped to a specific VLAN.

For the APs to work correctly, they must:
a) Be connected to a PoE-capable switch or PoE injector;
b) Be connected to a trunk port, so that all the necessary VLANs for the SSIDs can be accessed.

The APs have capability for 2.4GHz as well as the newer 5GHz WiFi bands. In their current configuration, the 5GHz capability is disabled because it can occasionally cause MAC-address-related issues on upstream routers/firewalls due to the fact that client devices tend to have two separate MAC addresses - one for their 2.4GHz radio and one for their 5GHz radio. 2.4GHz also provides better coverage that 5GHz - which is an important consideration when these APs are ised for touring purposes.

Credentials:
------------

For management access

Username: admin\
Password: [REDACTED]

SSID details:
-------------

|SSID             |Password         |Associated VLAN        |Notes                                                            |
|-----------------|-----------------|-----------------------|-----------------------------------------------------------------|
|   BTSROAM       |   [REDACTED]     |   Internet (34)       |   Sent through router-NATTed link to the BUCS Docking network   |
|   BTSSound      |   [REDACTED]     |   Sound Primary (50)  |   Dante Primary network used for management                     |
|   BTSLighting   |   [REDACTED]     |   Lighting (60)       |   Same network as used for ArtNet                               |
|   BTSVideo      |   [REDACTED]     |   Video Control (100) |                                                                 |

Configuration access:
---------------------

To access the AP configuration, a number of options are available:

1. SSH            - This is the most secure method. Temporarily connect the AP to a non-trunk port on a touring switch with an active DHCP server.
                    It will receive a DHCP lease. Find this IP address (IP scan, or view the EdgeRouter DHCP lease table) and use it to establish
                    an SSH connection to the AP.
         
2. Telnet         - This is much the same as SSH, except unencrypted;

3. Serial console - This involves connecting a serial console cable to the RJ-45 port on the back of an AP. This serial cable is then
                    connected to a computer by means of an RS232 connection;
                    
4. Web interface  - Whilst ostensibly nice and simple, these Cisco APs' web interfaces are buggy and slow. Avoid at all costs! Configuring via the CLI
                    may seem daunting, but with a little reading of the manual, saves much time and head-scratching in the long term! If the web interface
                    must be accessed, follow the same procedure as outlined for an SSH connection, but use the assigned IP address to establish an
                    HTTP connection instead.
                    
Upon login, one is typically greeted with a command prompt which ends in `>`. To actuallly access useful settings from here, type `enable`.
Enter the password by the prompt which appears. Upon entry of a correct password, a command prompt appears, ending in `#`. From this command prompt, useful commands can be run:

- `sh run`          - Shows the current configuration of the switch, in a user-friendly text file format. To back up the switch configuration, simply copy                       and paste the command's output into a notepad file, or some equivalent thereof.
           
- `conf t`          - Short for "configure terminal". Enters the command mode in which configuration commands may be entered, to change the switch
                      settings.
                    
When finishing entering configuration commands, ensure the following actions are done:

1. Run `end` followed by `reload` to ensure the settings are saved and therefore persist across the next reboot of the switch;

2. **Update the documentation according to the new configuration changes!!** At very least, the documentation should have its copy of the running
   config updated. If a major change has been made, the expolanatory must also be updated. Configuration changes made via the web interface have the
   effect, under the hood, of changing the running config. Using the web interface to make configuration changes is therefore no excuse for not
   updating this documentation's copy of the cunning config!

Running configuration:
----------------------

NB: Replace "hostname BTS-AP-1" with whichever number the AP in question actually is designated

```no service pad
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
!
hostname BTS-AP-1
!
!
logging rate-limit console 9
enable secret 5 [REDACTED]
!
no aaa new-model
no ip cef
!
!
!
!
dot11 syslog
dot11 vlan-name internet vlan 34
dot11 vlan-name lighting vlan 60
dot11 vlan-name management vlan 2
dot11 vlan-name sound-prim vlan 50
!         
dot11 ssid BTSLighting
   vlan 60
   authentication open 
   
   authentication key-management wpa version 2
   mbssid guest-mode
   wpa-psk ascii 7 [REDACTED]
!         
dot11 ssid BTSROAM
   vlan 34
   authentication open 
   authentication key-management wpa version 2
   mbssid guest-mode
   wpa-psk ascii 7 [REDACTED]
!         
dot11 ssid BTSSound
   vlan 50
   authentication open 
   authentication key-management wpa version 2
   mbssid guest-mode
   wpa-psk ascii 7 [REDACTED]
!         
dot11 ssid BTSVideo
   vlan 100
   authentication open 
   authentication key-management wpa version 2
   mbssid guest-mode
   wpa-psk ascii 7 [REDACTED]
!         
!         
dot11 guest
!         
!         
!         
username Cisco password 7 [REDACTED]
!         
!         
bridge irb
!         
!         
!         
interface Dot11Radio0
 no ip address
 !        
 encryption vlan 50 mode ciphers aes-ccm 
 !        
 encryption vlan 100 mode ciphers aes-ccm 
 !        
 encryption vlan 60 mode ciphers aes-ccm 
 !        
 encryption vlan 34 mode ciphers aes-ccm 
 !        
 ssid BTSLighting
 !        
 ssid BTSROAM
 !        
 ssid BTSSound
 !        
 ssid BTSVideo
 !        
 antenna gain 0
 stbc     
 mbssid   
 station-role root
!         
interface Dot11Radio0.2
 encapsulation dot1Q 2 native
 bridge-group 1
 bridge-group 1 subscriber-loop-control
 bridge-group 1 spanning-disabled
 bridge-group 1 block-unknown-source
 no bridge-group 1 source-learning
 no bridge-group 1 unicast-flooding
!         
interface Dot11Radio0.34
 encapsulation dot1Q 34
 bridge-group 34
 bridge-group 34 subscriber-loop-control
 bridge-group 34 spanning-disabled
 bridge-group 34 block-unknown-source
 no bridge-group 34 source-learning
 no bridge-group 34 unicast-flooding
!         
interface Dot11Radio0.50
 encapsulation dot1Q 50
 bridge-group 50
 bridge-group 50 subscriber-loop-control
 bridge-group 50 spanning-disabled
 bridge-group 50 block-unknown-source
 no bridge-group 50 source-learning
 no bridge-group 50 unicast-flooding
!         
interface Dot11Radio0.60
 encapsulation dot1Q 60
 bridge-group 60
 bridge-group 60 subscriber-loop-control
 bridge-group 60 spanning-disabled
 bridge-group 60 block-unknown-source
 no bridge-group 60 source-learning
 no bridge-group 60 unicast-flooding
!         
interface Dot11Radio0.100
 encapsulation dot1Q 100
 bridge-group 100
 bridge-group 100 subscriber-loop-control
 bridge-group 100 spanning-disabled
 bridge-group 100 block-unknown-source
 no bridge-group 100 source-learning
 no bridge-group 100 unicast-flooding
!         
interface Dot11Radio1
 no ip address
 shutdown 
 antenna gain 0
 peakdetect
 no dfs band block
 channel dfs
 station-role root
 bridge-group 1
 bridge-group 1 subscriber-loop-control
 bridge-group 1 spanning-disabled
 bridge-group 1 block-unknown-source
 no bridge-group 1 source-learning
 no bridge-group 1 unicast-flooding
!         
interface GigabitEthernet0
 no ip address
 duplex auto
 speed auto
!         
interface GigabitEthernet0.2
 encapsulation dot1Q 2 native
 bridge-group 1
 bridge-group 1 spanning-disabled
 no bridge-group 1 source-learning
!         
interface GigabitEthernet0.34
 encapsulation dot1Q 34
 bridge-group 34
 bridge-group 34 spanning-disabled
 no bridge-group 34 source-learning
!         
interface GigabitEthernet0.50
 encapsulation dot1Q 50
 bridge-group 50
 bridge-group 50 spanning-disabled
 no bridge-group 50 source-learning
!         
interface GigabitEthernet0.60
 encapsulation dot1Q 60
 bridge-group 60
 bridge-group 60 spanning-disabled
 no bridge-group 60 source-learning
!         
interface GigabitEthernet0.100
 encapsulation dot1Q 100
 bridge-group 100
 bridge-group 100 spanning-disabled
 no bridge-group 100 source-learning
!         
interface BVI1
 ip address dhcp client-id GigabitEthernet0
 ipv6 enable
!         
ip forward-protocol nd
ip http server
no ip http secure-server
ip http help-path http://www.cisco.com/warp/public/779/smbiz/prodconfig/help/eag
!         
!         
bridge 1 route ip
!         
!         
!         
line con 0
line vty 0 4
 login local
 transport input all
!         
end ```
