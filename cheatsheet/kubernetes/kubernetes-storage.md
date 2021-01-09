---
layout: topic
title: storage
parent: kubernetes
---

## Storage

### Storage in docker

Basics siehe [Docker Storage Basics](/cheatsheet/docker/docker-storage.html)

## Storage Anbindung in Kubernetes

- Container Storage Interface (CSI) ermöglicht Anbindung von unterschiedlichen externen Storage Providern (Azure, ...)
- Volumes können wie in Docker erzeugt und von einem Pod gemountet werden
- Verschiedene Mount Optionen:
	- hostPath: Nur bei Single Node Cluster
	- provider spezifische Optionen (AWS, Azure, ...)

## PersitentVolumes / PersitentVolumeClaims

- Bessere Alternative zu Volume
- Clusterwide Pool of Storage Volumes, die von einem Administrator zentral konfiguriert und von Nutzern in Application Pods gemountet werden
- AccessModes von PV:
	- ReadWriteOnce: Volume kann von genau einem Node mit Read und Write Rechten gemountet werden
	- ReadOnlyMany: Volume kann von mehreren Nodes mit Read Rechten gemountet werden
	- ReadWriteMany: Volume kann von mehreren Nodes mit Read und Write Rechten gemountet werden
- PersitentVolumeClaims werden genutzt, um einen Teil eines PersitentVolumes zu beanspruchen und im Pod einzubinden
	- PVC wird an genau ein PV mit gebunden (binding), das Kapazität hat um den Claim zu erfüllen
	- Binding kann durch Labels konfigurert werden
	- Nach dem Binding kann kein anderer PVC mit dem PV gebunden werden, selbst wenn der PVC den Speicher vom PV nicht ganz ausreizt
	- Falls keine PV vorhanden sind, bleibt der PVC in einem ``Pending`` State
- Nach dem Löschen eines PVC definiert die ``ReclaimPolicy`` des PV was mit dem PV passiert
	- Retain: PV bleibt bestehen und muss manuell gelöscht werden, kann aber auch von anderen PVC nicht genutzt werden (default)
	- Delete: PV wird automatisch gelöscht
	- Recycle: Daten auf dem PV werden gelöscht, danach ist PV wieder nutzbar für andere PVC
	
## StorageClass

- Erzeugt automatisch PV (und damit auch physikalischen Storage beim Provider (zB Azure))
- Dazu StorageClassName im PVC angeben, der genutzt werden soll (zB aws, AzureFile, ...)
- volumeBindingMode ermöglich binding Konfiguration für unterschiedliche Usecases
	- Immediate: Beim Erzeugen (default)
	- WaitForFirstConsumer: Sobald der erste Pod kommt, der das nutzt 
