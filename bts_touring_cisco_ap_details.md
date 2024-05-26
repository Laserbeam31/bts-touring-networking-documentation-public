Documentation for the BTS Cisco 3702 Touring APs
================================================

The BTS 3702 APs are set up to be able to access VLANs for Sound (VLAN 50), Lighting (VLAN 60),
Video Control (VLAN 100), and Internet (VLAN 34) through a set of different SSIDs, each mapped to a specific VLAN.

For the APs to work correctly, they must:
1. Be connected to a PoE-capable switch or PoE injector;
2. Be connected to a "Local Trunk" port on the touring racks (or any other equivalently configured switch port), so that all
   the necessary VLANs for the SSIDs can be accessed.

The APs are intended only for use with the touring rack networks, and are not set up for standalone use!

The APs have capability for 2.4GHz as well as the newer 5GHz WiFi bands. In their current configuration, the 5GHz capability is 
disabled because it can occasionally cause MAC-address-related issues on upstream routers/firewalls due to the fact that client 
devices tend to have two separate MAC addresses - one for their 2.4GHz radio and one for their 5GHz radio. 2.4GHz also provides 
better coverage than 5GHz - which is an important consideration when these APs are used for touring purposes.

Credentials:
------------

For management access

Username: admin\
Password: [REDACTED]

SSID details:
-------------

|SSID             |Password          |Associated VLAN        |Notes                                                            |
|-----------------|------------------|-----------------------|-----------------------------------------------------------------|
|   BTSROAM       |   [REDACTED]     |   Internet (34)       |   Sent through router-NATTed link to the BUCS Docking network   |
|   BTSSound      |   [REDACTED]     |   Sound Primary (50)  |   Dante Primary network used for management                     |
|   BTSLighting   |   [REDACTED]     |   Lighting (60)       |   Same network as used for ArtNet                               |
|   BTSVideo      |   [REDACTED]     |   Video Control (100) |                                                                 |

Configuration access:
---------------------

To access the AP configuration, a number of options are available:

1. SSH            - This is the most secure method. If the AP is connected to a port with an untagged ("native") VLAN, it will receive an IP address
                    on this VLAN over DHCP. A computer on this same VLAN can then be used to SSH into the AP's console.
         
3. Telnet         - This is much the same as SSH, except unencrypted;

4. Serial console - This involves connecting a Cisco serial console cable to the second RJ-45 port on the bottom of the AP. This serial cable is then
                    connected to a computer by means of an RS232 connection. A console session may then be opened on the computer using
                    a program such as PuTTy or Minicom.;
                    
6. Web interface  - Whilst ostensibly nice and simple, these Cisco APs' web interfaces are buggy and slow. Avoid at all costs! Configuring via the CLI
                    may seem daunting, but with a little reading of the manual, it saves much time and head-scratching in the long term! If the web
                    interface must be accessed, follow the same procedure as outlined for an SSH connection, but use the assigned IP address to establish
                    an HTTP connection instead via a web browser.
                    
Upon login, one is typically greeted with a command prompt which ends in `>`. To actuallly access useful settings from here, type `enable`.
Enter the password by the prompt which appears. Upon entry of a correct password, a command prompt appears, ending in `#`. From this command prompt,
useful commands can be run:

- `sh run`          - Shows the current configuration of the AP, in a user-friendly text file format. To back up the configuration, simply copy and
                      paste the command's output into a notepad file, or some equivalent thereof.
           
- `conf t`          - Short for "configure terminal". Enters the command mode in which configuration commands may be entered, to change the device
                      settings.
                    
When finishing entering configuration commands, ensure the following actions are done:

1. Run `end` followed by `reload` to ensure that the settings are saved and therefore persist across the next reboot of the device;

2. Update this documentation, particularly if a significant permanent change has been made.
