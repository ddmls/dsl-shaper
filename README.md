# dsl-shaper
Accurate traffic shaping, outbound and inbound, for ADSL, VDSL and other connections. It is intended for transferring traffic shaping to your Linux box while using an external router, keeping latency low while the connection is fully utilized. It uses HFSC, CoDel and IFB. It is also possible to use HTB if you prefer.

## Configuration
You need some information about your connection that you can get by logging in to your router, namely speed and connection type. Then you can enter these settings as follows:

```
# VDSL, PPPoE, LLC/SNAP
overhead=26 
linklayer=ethernet
```

```
# ADSL, PPPoA, LLC/SNAP
overhead=0
linklayer=adsl
```

```
# ADSL, PPPoA, VC/Mux
overhead=-4
linklayer=adsl # ADSL
```

These are some typical examples. Notice that the overhead value can be negative. linklayer is set to "adsl" for ADSL connections only, other types of connections use "ethernet" instead. You can find more values of overhead at the end of the page.

Also set the ports that will be considered foreground traffic, say web browsing or a ssh connection while anything else will be considered bulk background traffic. These settings apply to both TCP and UDP ports.

```
ports="80 443 53 22"
```

Assuming the device name eth0, an upstream speed of 5000 Kbps and a downstream speed of 50000 Kbps, start the script.

```
# Shape upstream only
sudo dsl-shaper eth0 5000
# Shape both upstream and downstream
sudo dsl-shaper eth0 5000 50000
# Clear shaping
sudo dsl-shaper eth0 clear
```

Shaping upstream is "free", that is it will not reduce your upload speeds, just the latency. Shaping downstream however has a cost. For best results is it recommended to enable it anyway.

## Overhead parameter
These values come from https://web.archive.org/web/20150606220856/http://ace-host.stuart.id.au/russell/files/tc/tc-atm/ where you can find details on the rationale.

When an external router handles PPP, values in the last column should be used. It's unlikely that your connection uses FCS.

Connection | Overhead int | Overhead ext
-----------|------------|-----------
IPoA, VC/Mux | 8 | -6
IPoA, LLC/SNAP | 16 | 2
Bridged, VC/Mux | 24 | 10
Bridged, VC/Mux+FCS | 28 | 14
Bridged, LLC/SNAP | 32 | 18
Bridged, LLC/SNAP+FCS | 36 | 22
PPPoA, VC/Mux | 10 | -4
PPPoA, LLC/SNAP | 14 | 0
PPPoE, VC/Mux | 32 | 18
PPPoE, VC/Mux+FCS | 36 | 22
PPPoE, LLC/SNAP | 40 | 26
PPPoE, LLC/SNAP+FCS | 44 | 30

## Improvements
- Could add an extra class for real time traffic.
- Detection of varying connection speed of WiFi connections.
