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

For a specific component:


or more granular: 

```bash
kubectl exec -it <pod-name> -c istio-proxy -- curl -X POST http://localhost:15000/logging?<component>=<level>
```

with component and level:

```
admin: <level>
aws: <level>
assert: <level>
backtrace: <level>
cache_filter: <level>
client: <level>
config: <level>
connection: <level>
conn_handler: <level>
decompression: <level>
dubbo: <level>
envoy_bug: <level>
ext_authz: <level>
rocketmq: <level>
file: <level>
filter: <level>
forward_proxy: <level>
grpc: <level>
hc: <level>
health_checker: <level>
http: <level>
http2: <level>
hystrix: <level>
init: <level>
io: <level>
jwt: <level>
kafka: <level>
lua: <level>
main: <level>
matcher: <level>
misc: <level>
mongo: <level>
quic: <level>
quic_stream: <level>
pool: <level>
rbac: <level>
redis: <level>
router: <level>
runtime: <level>
stats: <level>
secret: <level>
tap: <level>
testing: <level>
thrift: <level>
tracing: <level>
upstream: <level>
udp: <level>
wasm: <level>
```

e.g. jwt to trace:

```bash
kubectl exec -it calculation-0 -c istio-proxy -- curl -X POST http://localhost:15000/logging?jwt=trace
```


## Investigation

### Proxy state and version

``
istioctl proxy-status
``
