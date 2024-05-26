BTS Touring BUCS Interface Switch
=================================

This switch is generally installed in the FoH Auxiliary Video/Network rack.

This switch is responsible for receiving a tagged VLAN trunk from the campus network and bringing out ("untagging") specific VLANs from it
for BTS use.

One of the Founders' Hall Ethernet wall ports is typically left patched to a tagged BUCS VLAN trunk with Docking and the BTS VLAN on it. This
is connected to a _trunk_ port on the BUCS interface switch. The switch then has two banks of _access_ ports with Docking and BTS VLANs emanating
from them. This is useful for the purposes of e.g. sending video across campus on the BTS VLAN using the broadcast baluns.

Additionally, this switch has a separate bank of ports configured as "PoE injection pairs". These act exactly like standalone PoE injectors, for
sending PoE into the connection used by a device needing it. This functionality is useful because neither of the network switches in the FoH
touring rack does PoE, thus necessitating external injection of some kind.

Switch port allocations
-----------------------
|Port range|Purpose|Notes|
|----------|-------|-----|
|1-6|Docking|"Wired Eduroam"|
|7-12|BTS VLAN|VLAN 400. Dedicated BTS use across campus.|
|13-14|PoE injection pair 1||
|15-16|PoE injection pair 2||
|17-18|PoE injection pair 3||
|19-20|PoE injection pair 4||
