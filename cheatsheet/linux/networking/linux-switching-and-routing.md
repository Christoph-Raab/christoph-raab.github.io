---
layout: topic
title: Switching And Routing
parent: linux-networking
---

**Hinweis**

Alle ``ip addr/route add ...`` Befehle überleben einen Restart des Hosts nicht. 
Die Konfiguration muss in ``/etc/network/interfaces`` eingetragen werden.

## Switching

- Verbindung von zwei Geräten (Laptop, VM, ...) über Switch
- Switch erzeugt Netzwerk, in dem die zwei Geräte enthalten sind
- Beide Hosts haben ein Interface (physikalisch, virtuell)
	- Abfragen mit ``ip link``
	- Jeder Host bekommt IP aus dem Netzwerk ``ip addr add <ip> dev eth0`` 
- Switch kann nur Daten zwischen Hosts übertragen, die im kleinen Netzwerk sind

## Routing

- Router verbindet Netzwerke (= Zusammenschluss von Geräten über Switch)

## Gateway / Default Gateway

- Tor aus einem Netzwerk raus (in ein anderes Netzwer, Internet, ...)
- Route wird benötigt, um Netzwerke über Router miteinander zu verbinden
	- ``ip route add <cidr des Zielnetzwerks> via <ip des Gateway>`` 
- Traffic aus einem Netzwerk ins Internet würde viele Gateways benötigen
	- Default Gateway als Gateway für jeden Traffic, der unbekanntes Ziel hat
	- ``ip route add default via <ip des router>``
	- ``default`` kann auch mit ``0.0.0.0`` gesetzt werden -> jede IP Destination
	
## Public und Private Networking

- Netzwerk (Switch) ist mit zwei Router verbunden
- Ein Router führt zu anderen Netzwerke (Switch) -> private Network
- Der andere Router führt ins Internet -> public
- Zwei Gateways im Netzwerk:
	- ``ip route add default via <ip des public router zum Internet>``
	- ``ip route add <cidr des Zielnetzwerks> via <ip des private router>``
	
## Linux Host als Router nutzen

- Host 0 mit zwei Interfaces eth0 und eth1
- Host A mit eth0 über Route mit Host 0 auf eth0 verbunden
- Host B mit eth0 über Route mit Host 0 auf eth1 verbunden
- Auf Host 0 im File ``/etc/sysctl.conf`` Wert ``net.ipv4.ip_forward = 1`` setzen -> Daten werden von eth0 zu eth1 weitergeleitet
--> Verbindung zwischen Host A zu Host B über Host 0 aufgebaut