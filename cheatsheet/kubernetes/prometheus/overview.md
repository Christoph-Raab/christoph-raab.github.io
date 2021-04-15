## Prometheus

- OpenSource metrics based monitoring system
- Alerting inklusive für Benachrichtigungen an (beliebigen) Service

### Features

- Multidimensionales Datenmodel, wodurch mal eine Zeitreihe nicht nur anhand des Namens, sondern auch anhand von Labeln (Key-Value-Pairs) identifizieren kann
- Eigene, flexible Query Language (PromQL)
- Sammeln der Zeitreihen erfolgt durch Pull via HTTP
- Sehr kurzlebige Anwendungen können Metriken auch über ein Push-Gateway gepusht werden
- Ziele können via statische Konfig oder via Service Discovery konfiguriert werden
- Visualisierung und Dashboards werden ebenfalls unterstützt

### Inhalte

[Architecture](architecture.html)
[Konfig in Kubernetes](kubernetes-config.html)
[PromQL](promql.html)
[Alerting](alerting.html)
[Monitoring](monitoring.html)
