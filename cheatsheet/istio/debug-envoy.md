# Debug envoy

## General tips

- Check config dump via `curl localhost:15000/config_dump`
- Add missing fields via `EnvoyFiler` and debug with config dump
- TLS problem: Check listeners

## Deep dive

### Check if proxy is there

- `istioctl ps | grep <name>`
- Shows status of sidecar proxy
- See [docs](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-status) on istio.io

### Analyze with istioctl

- `istioctl analyze` 
- Shows errors in the config, e.g. `VirtualService` points to subset but it is never defined
- Doesn't show valid but wrong config, e.g. `VirtualService` points to wrong port -> no error
- See [docs](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-analyze) on istio.io

### Check proxy config

- `istioctl pc [all|clusters|listeners|routes|endpoints|bootstrap|log|secret] <workload>.<namespace>`
- Shows sidecar config for a given workload
- `-o json` does show more information
- Use [envoyctl](https://github.com/djannot/envoyctl) to visualize as table
- Use [envoyui by solo.io](https://envoyui.solo.io) for a browser based client side analyzing tool for the json output
- Have a script that can be run before/after changes were applied to show what changed in the config
- See [docs](https://istio.io/latest/docs/reference/commands/istioctl/#istioctl-proxy-config) on istio.io

### Debug access logs

- Can be added per workload/namespace or mesh wide, see [docs](https://istio.io/latest/docs/tasks/observability/logs/access-log/)
- Use the [engarde-viewer](https://github.com/GregHanson/engarde-viewer) to analyze an access log entry
- E.g. enable for current namespace (incl. VMs in that namespace)

```bash
cat <<EOF | kubectl apply -f -
apiVersion: telemetry.istio.io/v1alpha1
kind: Telemetry
metadata:
  name: enable-access-logs
spec:
  accessLogging:
  - providers:
    - name: envoy
EOF
```
