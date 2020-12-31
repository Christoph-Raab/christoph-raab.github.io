---
layout: topic
title: namespaces
parent: linux-networking
---

## Network Namespaces

Logische Trennung zur Netzwerk Isolation. 

Host -> Haus
Namespace -> Raum

Host kann alles sehen, was im Netzwerk passiert.
Container im Namespace kann nur sehen, die im Namespace passiert und weiß auch nichts von anderen Namespaces.
Verbindung zwischen Namespaces kann hergestellt werden.

Container Namespace hat eigene Routing Table und Address Resolution (ARP) Table und eigenes Netzwerk Interface, getrennt vom Host.

View namespace configuration
- Namespaces auf host auflisten ``ip netns``
- Neuen Namespace erzeugen ``ip netns add <name>``
- Auflisten der interfaces
	- vom host: ``ip link``
	- im namespace: ``ip netns exec <name> ip link`` oder ``ip -n <name> link``
	  -> sichtbar nur die Interfaces im namespace

View Address Resolution Protocol (ARP)
- Auflisten
	- auf Host: ``arp``
	- im Nammesapce: ``ip netns exec <name> arp``
	
Routing Table
- Auflisten
	- auf Host: ``route``
	- im Nammesapce: ``ip netns exec <name> route``
	
## Verbinden von Namespaces

1) Pip erzeugen ``ip link add <name-a> type veth peer name <name-b>``
2) Interfaces an den entsprechenden Namespace anfügen ``ip link set <name-a> netns <name-ns-a>`` und ``-b``
3) Interface im Namespace IP Adresse zuweisen ``ip -n <name-ns-a> addr add <ip-a> dev <name-a>`` und ``-b``
4) Interfaces aktivieren ``ip -n <name-ns-a> link set <name-a> up`` und ``-b``
5) Test Connection ``ip netns <name-a> ping <ip-b>``

Über Virtual Switch mehrere Namespaces einfach untereinander verbinden, zB mit Linux Bridge
1) vSwitch erzeugen ``ip link add <vnet-name> type bridge`` und ``ip set link <vnet-name> up``
2) Connect Namespaces mit Bridge verbinden: wie oben, nur b wird mit der Bridge verbunden ``ip link set <name-b> master <vnet-name>``
3) Verbindung zwischen Namespaces etablieren: ``ip addr add <vnet-cidr (zb /24) dev <vnet-name>``
4) Bisher alles private Vnet. Für Zugang nach Außen zu schaffen, muss Gateway in die Route Table eingetragen werden. ``ip netns exec <name-a> ip route add <cidr> via <ip>``
5) Über NAT in der Route Table Verbindung zwischen VNet und Host Interface schaffen ``iptables -t -nat -A POSTROUTING -s <vnet-cidr> -j MASQUERADE``
6) Default Gateway für Zugriff auf externe IPs ``ip netns exec <name-a> ip route add default via <ip>``
7) Verbindung von Außen ins VNet: Port Forwarding Rule auf Host einrichten: ``iptables -t -nat -A PREROUTING --dport 80 --to-destination <ip>:80 -j DNAT``