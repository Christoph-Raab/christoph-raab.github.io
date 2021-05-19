## Alerting

### Alertmanager

- Kann als ConfigMap angelegt werden
- Es können verschiedene Receiver konfiguriert werden (Mail, Slack, ...)
- Wird als Deployment mit Service mit Referenz auf ConfigMap/Volume gestartet
- In Prometheus wird der Alertmanager dann über Service Discovery gefunden

### Alerting Rules

[Alerting Rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/)

- Können über rules_file referenziert werden
- Hat ein Query, das als Basis dient
- Über ``for`` wird eine Zeit angegeben, die der Fehler bestehen muss, damit ein Alert ausgelöst wird
- Über labels können unterschiedliche Routings angesprochen werden, sodass zB unterschiedliche Schweregrade an unterschiedliche Receiver geschickt werden
- Annotations können zB Inhalt für First Responder enthalten
