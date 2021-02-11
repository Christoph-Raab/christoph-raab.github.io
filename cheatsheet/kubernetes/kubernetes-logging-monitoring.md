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

### Node Metrics Helper

```bash
alias k8snodeinfo='kubectl get nodes --no-headers | awk '\''{print $1}'\'' | xargs -I {} sh -c '\''echo {} ; kubectl describe node {} | grep Allocated -A 5 | grep -ve Event -ve Allocated -ve percent -ve -- ; echo '\'''

```

Usage: 

``k8snodeinfo``

Output:

```bash
<node1>
  Resource                       Requests      Limits
  cpu                            2149m (55%)   2149m (55%)
  memory                         4031Mi (31%)  5442Mi (43%)

<node2>
  Resource                       Requests      Limits
  cpu                            1710m (44%)   1710m (44%)
  memory                         1982Mi (15%)  6266Mi (49%)
```

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
