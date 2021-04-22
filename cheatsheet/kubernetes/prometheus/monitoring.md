## Monitoring

### Instrumentation

- Code in der App definiert den Metrik Endpoint
- Prometheus stellt bestimmte [Client Liraries bereit](https://prometheus.io/docs/instrumenting/clientlibs/)

### Collecting Application Metrics

- Im Deployment müssen annotations gesetzt sein
  - prometheus.io/scrape: "true"
  - prometheus.io/path: "path/to/metrics-endpoint"
  - prometheus.io/port: "3000"
- Auf dem Service braucht man dann keine Annotations, sonst werden die Metrics dupliziert
