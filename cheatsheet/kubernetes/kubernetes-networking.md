---
layout: topic
title: networking
parent: kubernetes
---

## Networking in Kubernetes

### CNI Container Networking Interface

Networking Konfiguration wird in [Linux](/cheatsheet/linux/networking/linux-network-namespaces.md) 
und in [Docker](/cheatsheet/docker/docker-networking.md) ähnlich vorgenommen.

CNI standardisiert, wie Netzwerk in einer Container Runtime erzeugt und konfiguriert wird und wie ein Netzwerk Plugin geschrieben werden muss.

--> Docker implementiert aber Container Network Model (CNM), darum jetzt auch nicht mehr Standard in Kubernetes, sondern containerd.


Einziger Link in der Kubernetes Dokumentation, in der beschrieben wird, wie Netzwerk Plugin installiert wird:
[Install Kubernetes Netzwerk Plugin - Step 2](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/#steps-for-the-first-control-plane-node)

### Cluster Networking Configuration

Für Kommunikation müssen bestimmte Ports auf Master und den Worker Nodes offen sein:
[kubernetes docs](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#check-required-ports)

Befehle zum Konfigurieren in der Übersicht
- ip link
- cat /etc/network/interfaces
- ipconfig [ -a | <interface> ]
- ip addr
- ip addr add <cidr> <name> <interface>
- ip route
- ip route add <cidr> via <ip>
- cat /proc/sys/net/ipv4/ip_forward -> 1
- arp
- netstat -natulp

### Pod Networking

Anforderungen
- Jeder Pod bekommt eine IP Adresse
- Jeder Pod kann mit jedem Pod auf dem gleichen Node kommunizieren
- Jeder Pod kann ohne NAT mit jedem Pod auf den anderen Nodes kommunizieren

CNI gibt kubelet eine Art Blaupause, mit der neue Pods zum Netzwerk hinzugefügt und alte entfernt werden können.
Außerdem werden zwei Plugins zur Verfügung gestellt, die von den CNI Plugins genutzt werden können, um Pods IPs zuzuweisen.
- DHCP
- host-local
--> Kann über ipam Konfiguration in ``/etc/cni/net.d/net-script.conf`` definiert werden.

Kubelet Argumente:
- --network-plugin -> cni
- --cni-bin-dir Speicherort aller verfügbaren Plugins als Executables
- --cni-conf-dir Beschreibt, welches Plugin genutzt wird (das erste im Alphabet wird genutzt, falls es mehr sind)

--> Einsehen über static pod file oder über ps -aux -> grep kubelet

### CNI Plugins

Übersicht über (Netzwerk) Plugins:
[Kubernetes Addons](https://kubernetes.io/docs/concepts/cluster-administration/addons/)

[Kubernetes Netzwerk Plugins](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model)

Anforderungen
- Container Runtime muss Netzwerk Namespace erzeugen
- Muss das Netzwerk identifizieren, an das ein neuer Container hinzugefügt werden muss
- Container Runtime muss Network Plugin (bridge) nutzen, um Container hinzuzufügen und zu entfernen
- Muss Pods IPs zuweisen
- Netzwerk Konfiguration muss im JSON Format vorliegen

### Beispiel weaveworks

Agents werden auf jedem Node installiert. 
Agents fungieren als eine Art Middlerware für Kommunikation zwischen den Nodes.
Agent auf dem Sendernode wrappt die Packages einer Nachricht und leitet sie an den Agent auf dem Zielnode weiter.
Agent auf dem Zielnode unwrappt sie und leitet sie an den Pod weiter.

Installieren in laufendem Cluster als Pods in einem Daemonset mit ``kubectl apply -f <link-zu-weave-yaml>``

Nimmt im Standard die CIDR 10.32.0.0/12 und teilt diese auf die Nodes auf. 
Jeder Pod hat dann eine IP aus dem Subnet seines Nodes.

### Service Networking

Service Arten:
- ClusterIP: Für Kommunikation im Cluster
- NodePort: Interne Anwendungen und externe Anwendungen/Users können die Anwendung über <jede-Node-IP>:<Node-Port> erreichen

Services sind Cluster-wide und nur virtuell.

``kube-proxy`` erzeugt auf jedem Node Forwarding Rules, sodass jede Anfrage an einen Pod über dessen Service geht.
Im Default wird ip tables genutzt, es gibt aber weitere Möglichkeiten.

Service CIDR wird im ``kube-api-server`` über ``--service-cluster-ip-range=`` gesetzt und ist im Default ``10.0.0.0/24``.
Sollte sich nicht mit dem POD Network CIDR überschneiden!

Befehle:
- ps aux | grep kube-api-server -> CIDR Konfiguration
- iptables -L -t net | grep <service-name> -> zeigt forwarding rule
- cat /var/log/kube-proxy.log -> zeigt forwarding rule

### DNS in Kubernetes

Immer wenn service erzeugt wird, wird von Kubernetes ein DNS Name erzeugt.
Der Name des Service wird dabei auf dessen IP gemappt. -> ``curl http://my-service``

Äquivalente Namen:
- `my-service`
- `my-service.<namespace>`
- `my-service.<namespace>.svc`
- `my-service.<namespace>.svc.cluster.local`

Für Pods werden standardmäßig keine DNS Records erzeugt. Kann aber aktiviert werden.
zB für Pod mit ip 10.244.2.5:
- `10-244-2-5`
- `10-244-2-5.<namespace>`
- `10-244-2-5.<namespace>.pod`
- `10-244-2-5.<namespace>.pod.cluster.local`

### CoreDNS in Kubernetes

Jeder Pod hat einen Eintrag in ``/etc/resolv.conf`` der auf einen DNS nameserver zeigt.
Neue Pod mit Service wird Service IP im namespace hinzugefügt.

CoreDNS server läuft als Deployment mit zwei Replicas im kube-system Namespace deployt.
Im Corefile in ``/etc/coredns/Corefile`` wird das  ``kubernetes`` plugin konfiguriert.
Hier steht auch die Toplevel-Domain, standardmäßig ``cluster.local``.
Mit ``pods insecure`` kann ein DNS Record für Pods erzeugt werden.
Wird als ConfigMap in den Pod gemountet, kann also einfach konfiguriert werden.

kubelet kennt die IP und den Toplevel-Domain Name des Clusters: 
``cat /var/lib/kubelet/config.yaml`` -> ``clusterDNS``, ``clusterDomain``

### Ingress

Zugriff ins Cluster mit Services only:

OnPremise Setup
Browser -> Proxy (Port 80) -> Service Type NodePort (> 3000) -> Pod

Cloud Provider
- zB mit Type LoadBalancer --> Cloud Provider erzeugt LB als Proxy

--> Problem: Für jeden Service Type LoadBalancer entsteht ein LB, der Kosten verursacht. 
Außerdem kein Routing, zB example.com/videos nicht möglich, Proxy muss aufwändig konfiguriert werden. 

Ingress als einzelner Ort zur Konfiguration inklusive SSL. Wird dann über einen Service (NodePort oder LoadBalancer) zugänglich gemacht.

Kann manuell gemacht werden (zB mit NGINX Server + manueller Konfiguration). 
Besser allerdings mit Ingress Controller mit Ingress Resources.

zB mit NGINX Ingress Controller:
- Deployment mit image ``nginx-ingress-controller`` + arg ``/nginx-ingress-controller`` + env ``POD_NAME``, ``POD_NAMESPACE``
- ConfigMap (kann anfangs leer sein)
- Service (Type NodePort) 
- ServiceAccount mit Roles and -Bindings

Ingress Resources:
- ``Kind: Ingress`` mit Backend, das den ``ServiceName`` und ``ServicePort definiert.
- Enthält Regeln für Routing basierend auf Domainname, Pfad, ...
- Im ``rules`` Array definiert:
	- ``paths`` Pfad - Backend Paar: Binding an Service
	- ``host``: Binding an Domain
- Default Backened wird standardmäßig gesetzt, muss also auch deployt werden.
