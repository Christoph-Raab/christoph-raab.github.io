---
layout: topic
title: multi-container-pods
parent: kubernetes
---

## Multi Container

Mehrere Container in einem Pod, die Lebenszyklus, Netzwerk (Zugriff über localhost möglich) und Storage teilen.

Design Patterns:
- Sidecar
- Adapter
- Ambassador

### Sidecar

Zum Beispiel: 
Ein Logging Agent, der die Logs an einen Log Server schickt.

### Adapter

Zum Beispiel: 
Ein Logging Agent, der die Logs **richtig formatiert** und an einen Log Server schickt.

### Ambassador

Zum Beispiel:
Eine Anwendung soll sich je nach Stage mit einer bestimmten Datenbank verbinden. 
Diese Logik wird in einen Ambassador Container ausgelagert, sodass die Anwendung selbst davon nicht weiß.
Anfrage geht einfach an localhost, Ambassador Container fungiert als Proxy.