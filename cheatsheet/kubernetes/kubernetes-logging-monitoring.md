---
layout: topic
title: logging-monitoring
parent: kubernetes
---


## Logging and Monitoring

### Performance Metrics

Metrics-Server bekommt Daten von kubelet über cAdvisor

Ermöglicht den kubectl top node / pod command

[Kubernetes Metrics Deep Dive Series](https://blog.freshtracks.io/a-deep-dive-into-kubernetes-metrics-66936addedae)

### Logging

StdOut/Err gehen an docker logging driver

```bash
kubectl logs <pod> <container> --tail=20 --since=1h -f
```
