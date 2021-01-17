---
layout: topic
title: design-install
parent: kubernetes
---

### Hosting Production  Applications

- HA with multiple node cluster with multiple master nodes
- Use kubeadm or Provider
- Up to 5000 Nodes
- Up to 150000 Pods
- Up to 300000 Container
- Up to 100 Pods per Node

### Performance Storage

- High Performance -> SSD based Storage
- Multiple Concurrent Connections -> Network based Storage
- Persistent shared volumes for shared access across multiple Pods
- Label nodes with specific disk types
- Use node selectors to assign applications to nodes with specific disk types

### Nodes

- Virtual or Physical Machines
- Minimum 4 Node Cluster 
- Master vs Worker Nodes
- Linux OS
- Don't deploy workloads on master nodes
- Evtl. ETCD Cluster separat hosten

### Infrastruktur

- Laptop (Single/Multi Node Cluster mit: Kubeadm, Minikube, [Kind](https://kind.sigs.k8s.io/), ...)
- Physical / Virtual Servers 
- Cloud
	- Turnkey Solutions 
		- Mehr oder weniger Selbst gehostetes Cluster
		- Openshift, Cloud Foundry Container Runtime, VMware Cloud PKS, Vagrant, ...
		- Unterstützen beim Konfiguratieren, es müssen quasi nur VMs gestellt werden
	- Managed Service
		- Viel Management wird abgenommen, es gehen aber auch Freiheiten verloren
		- GKE, OpenShift Online, AKS, EKS, ...
		
### High Availability

Problemstellung: Wenn Master Node abstürtzt und ein Pod crasht, kann der Ausfall nicht behoben werden.
--> Mehrere Master Nodes

Mehrere Master Node, wobei jeder alle Kubernetes Komponenten installiert hat (ETCD, Scheduler, ...).
LoadBalancer teilt den Traffik zwischen den unterschiedlichen API Servern auf.
Controller Manager und Scheduler laufen im Active/StandBy Modus mit einer Leader/Follower Konfiguration (``leader-elect true``).

### ETCD in High Availability

- Read/Write mit mehreren, verteilten Servern
	- Write: Leader/Follower mit Read Replicas
	- ``RAFT`` zur Auswahl der Leader Instanz
	- Write geht nur mit Quorum = N/2 + 1
	- Minimum 3, ideal 5 ETCD Nodes 
	- eine ungerade Zahl, damit immer Quorum erreicht wird, wenn es Netzwerk Probleme gibt
	- In ``initial-cluster`` alle Instanzen benennen