BTS Networking Gear Documentation
=================================

NOTE: This repository has been deprecated. Documentation for BTS network switches now lives on the new BTS Wiki: https://wiki.bts-crew.com/index.php/BTS_Touring_Networking_Gear

This is a central public repository of documentation regarding the Backstage Technical Services touring networking gear.

This includes VLAN configuration, router configuration, switch configuration, and Access point configuration, for both touring and static networks

For network gear with this functionality, the _running configuration_ is also included. This is a file containing all the configuration commands
which make up the way in which a switch or other network device is currently set to operate.

For security reasons, encryption keys, sensitive infomation, and passwords must be redacted from configuration files/details before being uploaded
to this repository.

List of BTS networking gear covered by this repository:
-------------------------------------------------------

1. Touring rack switches
2. Touring rack Ubiquiti EdgeRouter
3. Small touring HP PoE-capable switches
4. Cisco 3702 autonomous wireless access points (APs)
5. BUCS Founders' Hall interface switch

Formatting conventions:
-----------------------

Please follow the following conventions when contributing to the documentation:

1. Files covering touring gear are named "bts_touring_xxxxx";
2. Files covering install gear are named "bts_install_[location of install]_xxxx";
3. Document headers are entirely capitalised and of the "====" markdown size;
4. Document sub-titles only have their first word capitalised, are terminated with a colon, and are of the "----" markdown size;
5. Commands and technical terms should be formatted as code;
6. When introducing new documentation files for new pieces of gear, please include a suitable reference in the above master list;
7. Text-based switch/AP configs should be placed in the "config_backups" folder in this repository.

Useful further reading/watching:
--------------------------------

For those wishing to learn more about fundamental networking principles, the following resources are highly informative:

- https://www.youtube.com/c/PowerCertAnimatedVideos (for good visual introductions to various networking infrastructure principles)

- https://www.youtube.com/c/NetworkChuck (for in-depth discussion and tutorials of networking software and hardware. The "free CCNA" series is particularly   worth watching)

- https://www.youtube.com/results?search_query=My+Playhouse (An extremely varied channel, but with some good networking videos. Tends to approach networking concepts from a slightly different angle compared with the average IT YouTube channel - which is beneficial in the context of their use for backstage purposes, which center around theatrical effects rather than computer networks)

- https://www.cisco.com/c/en/us/support/all-products.html (Useful for Cisco devices)

- https://asp.arubanetworks.com/downloads;productIds=UHJvZHVjdDoxNDU4MDJlMi0yZTAwLTExZTgtYmY4Yy0zMzhmOGQwMWM1MjE%3D (Useful for HP (now Aruba) networking gear)

- https://www.audinate.com/learning/training-certification/dante-certification-program (A very useful introduction to Dante, made by its makers: Audinate)
