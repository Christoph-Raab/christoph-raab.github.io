---
layout: topic
title: logging-monitoring
parent: kubernetes
---


## Logging and Monitoring

Welche Metriken sind verfügbar
	- Node Level Metrics
	- Pod Level Metrics

Wie kann man diese aggregieren
	- EFK
	- Prometheus
  
Metric-server <-- oft verwendet

### Performance Metrics

Metrics-Server bekommt Daten von kubelet über cAdvisor

Ermöglicht den kubectl top node / pod command

[Kubernetes Metrics Deep Dive Series](https://blog.freshtracks.io/a-deep-dive-into-kubernetes-metrics-66936addedae)

### Logging

StdOut/Err gehen an docker logging driver

```bash
kubectl logs <pod> <container> 
```

args
- --tail=20 -> show last 20 lines
- --since=1h -> show last hour
- -f -> follow
- --previous -> show logs of previous pod (bei Restart)
