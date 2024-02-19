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

- Konfiguration mit yml file (Referenz in der ConfigMap) mit
  - globaler Konfiguration (scrape_interval, scrape_timeout)
  - scrape Konfiguration (jobs mit statischer/dynamischer Service Discovery)
  - alerting Konfiguration (Konfiguration für AlertManager)
- Service Discovery in Kubernetes nutzt Kubernetes API um Targets zu finden
- Mit ``relabel_configs`` können bestimmte Endpoints gefilter oder umgeschrieben werden
- Mehr in den [Docs](https://prometheus.io/docs/prometheus/latest/configuration/configuration/)

### NodeExporter

- Software, die auf dem Node installiert wird
- Prometheus kann damit Hardware und OS Metriken abfragen, zB CPU, Memory, DiskSpace, Kernel Metriken, ...
- Nicht als root laufen lassen, sondern neuer User zB prometheus
- Als Service auf dem Node anlegen
  - service file in ``/etc/systemd/system/node_exporter_service``
  - Enthält Directory, wo das Node Exporter binary liegt und dessen Nutzer
  - systemclt daemon reloaden, service enablen und starten
- Mehr in den [Docs](https://prometheus.io/docs/guides/node-exporter/)

### Grafische Oberfläche

- Dort kann man
  - Konfiguration einsehen
  - Installierte Version einsehen
  - Alerts, Rules und Targets einsehen
  - Expression Browser ermöglicht ausführen von Query mit PromQL und anzeigen des Ergebnisses + Visualisierung

### Grafana

- Setup mit Deployment und Service
- Neue data source 'Prometheus' anlegen
- Dashboard aufsetzen:
  - Von [Grafana Dashboards](https://grafana.com/grafana/dashboards) importieren (Manchmal aber veraltete Versionen!)
  - Besser Grafana Dashboard als Vorlage verwenden und daraus eigenes Dashboard erstellen
