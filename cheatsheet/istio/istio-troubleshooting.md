---
layout: topic
title: troubleshooting
parent: istio
---

## Sidecar debugging

### Set Logging Level of the sidecar

For all components, eg. level trace

``
kubectl exec -it <pod-name> -c istio-proxy -- curl -X POST http://localhost:15000/logging?level=debug
``

For a specific component, eg. connection level to debug

``
http://localhost:15000/logging?connection=debug
``

## Investigation

### Proxy state and version

``
istioctl proxy-status
``
