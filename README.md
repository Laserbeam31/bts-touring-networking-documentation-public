BTS Networking Gear Documentation
=================================

This is a central public repository of documentation regarding the Backstage Technical Services networking gear.

This included VLAN configuration, router configuration, switch configuration, and Access point configuration, for both touring and static networks

For network gear with this functionality, the _running configuration_ is also included. This is a file containing all the configuration commands
which make up the way in which a switch or other network device is currently set to operate.

For security reasons, encryption keys, sensitive infomation, and passwords must be redacted from configuration files/details before being uploaded
to this repository.

List of BTS networking gear covered by this document:
-----------------------------------------------------

1. Touring rack switches
2. Touring rack Ubiquiti EdgeRouter
3. Small touring HP PoE-capable switches
4. Cisco 3702 autonomous wireless access points (APs)

Formatting and nomenclature conventions:
----------------------------------------

Please follow the following conventions when contributing to the documentation:

1. Files covering touring gear are named "bts_touring_xxxxx";
2. Files covering install gear are named "bts_install_[location of install]_xxxx";
3. Document headers are entirely capitalised and of the "====" markdown size;
4. Document sub-titles only have their first word capitalised, are terminated with a colon, and are of the "----" markdown size;
5. Commands, key technical terms, and configuration files should be formatted as code by preceding and proceding with "```".
