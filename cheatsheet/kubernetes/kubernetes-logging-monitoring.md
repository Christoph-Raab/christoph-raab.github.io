---
layout: topic
title: logging-monitoring
parent: kubernetes
---


## Logging and Monitoring


### Read logs of pod

```bash
kubectl logs <pod> <container> --tail=20 --since=1h -f
```
