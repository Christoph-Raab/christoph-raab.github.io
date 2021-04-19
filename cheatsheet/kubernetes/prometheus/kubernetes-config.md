## Prometheus in Kubernetes

### Setup

- Eigener Namespace
- ConfigMap mit prometheus.yml (siehe unten)
- Deployment mit 
  - Prometheus Containre mit
    - Reference auf prometheus.yml (``--config.file=``) 
    - Einstellung, wo Prometheues Data gespeichert werden (``--storage.tsdb.path=``)
    - Volume für ConfigMap
    - Volume für Storage
  - Watch Container
    -  Weaveworks:Watch image
    -  Erkennt Änderungen in der ConfigMap
-  Service mit Annotations
  - ``prometheus.io/scrape: 'true'``
  - ``prometheus.io/port: '9090'``
- ClusterRole
  - Gibt Zugriff auf die ``/metrics`` urls mit GET

Damit jeder Pod/Service/Node, der die Annotation ``prometheus.io/scrape: 'true'`` hat, von Prometheus gescrapt.

[Kube State Metrics](https://github.com/kubernetes/kube-state-metrics) als Helper, um Stats über alle Ressourcen im Cluster zu erhalten.

### Konfiguration


### Grafana


### NodeExporter


### Expression Browser
