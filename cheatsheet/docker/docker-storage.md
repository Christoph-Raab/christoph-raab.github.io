---
layout: topic
title: storage
parent: docker
---

## Docker Storage Basics

- Docker speichert Dateien im Filesystem unter /var/lib/docker
	- .../aufs (default storage driver bei ubuntu)
	- .../containers
	- .../image
	- .../volumes
- Daten werden in Layern gespeichert
	- Ein Layer je Befehl im Dockerfile
	- Jedes Layer beinhaltet die Änderungen aus dem Befehl
	- Mehrfach genutzte Layer werden aus dem Cache genutzt
- Beim Ausführen des Container wird ein "Runtime" Layer hinzugefügt
	- enthält Daten/Änderungen, die zur Laufzeit entstehen
	- alle anderen Layer sind ReadOnly
	- Copy-On-Write: Bei Änderungen an Files aus den ReadOnly Layern wird Kopie erstellt
	- Daten/Änderungen gehen verloren, wenn Container zerstört wird
- Änderungen persitieren durch Volumes
	- mountet spezifischen Path in einem docker volume (volume mount)
	- auch Directory des Host-File können als Speicherort genutzt werden (bind mount)
