---
layout: topic
title: troubleshooting
parent: kubernetes
---

## Troubleshoot kubernetes

### Helper pod

```bsah
Kubectl run util --image=busybox --rm -it --restart=Never -- sh
```
