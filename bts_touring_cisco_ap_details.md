Documentation for the BTS Cisco 3702 Touring APs
================================================

The BTS 3702 APs are set up to be able to access VLANs for sound (Dante Primary), lighting (LX),
video control, and internet through a set of different SSID, each mapped to a specific VLAN.

For the APs to work correctly, they must:
a) Be connected to a PoE-capable switch or PoE injector;
b) Be connected to a trunk port, so that all the necessary VLANs for the SSIDs can be accessed.

The native vlan for the APs is set to 2. This is the same as that of the touring switches, and
means that all the networking gear can be configured over the same VLAN by connecting to the "management" port on a given touring switch.

Credentials
-----------

For management access

Username: admin\
Password: [REDACTED]

SSID Details
------------

|SSID             |Password         |Associated VLAN        |Notes                                                            |
|-----------------|-----------------|-----------------------|-----------------------------------------------------------------|
|   BTSRoam       |   [REDACTED]     |   Internet (34)       |   Sent through router-NATTed link to the BUCS Docking network   |
|   BTSSound      |   [REDACTED]     |   Sound Primary (50)  |   Dante Primary network used for management                     |
|   BTSLighting   |   [REDACTED]     |   Lighting (60)       |   Same network as used for ArtNet                               |
|   BTSVideo      |   [REDACTED]     |   Video Control (100) |                                                                 |

Running Configuration
---------------------

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
