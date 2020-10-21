---
layout: topic
title: management
parent: kubernetes
---


## Management

### Operating System Upgrade - Take Node Offline

- Wenn ein Node für mehr als 5 Minuten offline ist:
	- Kubernetes nimmt an die Pods sind tot
	- Pods mit RepicaSet werden auf anderem Node neu gestartet
- Drain Node
	- kubectl drain <node>
	- Pods von Node werden auf anderem Node neu erzeugt
	- Nach dem Update des Nodes: 
  ``kubectl uncordan <node>``
  
- Verhindern, dass auf einem Node Pods laufen können:
   ``kubectl cordan <node>``
  --> Laufende Pods werden aber nicht gekillt

### Cluster Upgrade Process

- Einzelne Komponenten (kube-proxy, kubelet, …) können unterschiedliche Versionen haben
- Keine Komponente sollte eine höhere Version haben als kube-apiserver
- Höchstens eine Version unter kube-apiserver
	- Controller-manager
	- Kube-Scheduler
- Höchstens zwei Versionen unter kube-apiserver
	- Kubelet
	- Kube-proxy
- Kubectl kann eine Version über oder unter kube-apiserver sein
- Wenn Custer zB bei Version 1.10 und 1.14 kommt bald raus, sollte man auf 1.11 upgraden
- Erstmal Master upgraden
	- Währenddessen können keine neuen Pods deployt werden und auch keine automatischen Neustarts
- Dann Nodes upgraden
	- Alle auf einmal
	- Nacheinander
	- Neue Nodes mit neuerer Version hinzufügen

### Backup and Restore

- Backup:
	- Configuration -> Git/Subversion
	- ETCD -> ectdctl snapshot save/restore --> reconfigure static etcd pod
     siehe auch [GitHub Anleitung] (https://github.com/mmumshad/kubernetes-the-hard-way/blob/master/practice-questions-answers/cluster-maintenance/backup-etcd/etcd-backup-and-restore.md)
  - PersistentVolume
