## Prometheus Architecture

[Interactive Diagram](https://lucid.app/lucidchart/3da24eca-0c4d-4f03-b202-ba8f455783d2/view?page=0_0#)

- Storage -> PV
- Scraping via HTTP endpoint/Exporter
- Scrape with Service Discovery (Kubernetes, EC2, ...)
- Rules
  - Recording Rules: Mit PromQL Ausdruck formulieren, dessen Output evaluiert wird und in Storage gespeichert
  - Alerting Rules: Mit PromQL Ausdruck formulieren, die, falls sie erfüllt wird, einen Alarm auslöst
- Alert Manager versendet Notifications
  - Per Mail, Ticket, ... basierend auf dessen Severity (zb niedrig: Ticket, hoch: Mail)
- Dashboard:
  - Prometheus eigenes Dashboard (Expression Browser)
  - Grafana (gehört nicht u Prometheus, sondern visualisiert dessen Metriken)

### Client Libraries & Exporter

#### Client Library

- Teil der Application (zB SpringBoot App)
- Erzeugt ``/metrics`` Endpunkt

#### Exporter

- Werden genutzt, um Anwendungen zu monitoren, deren Source Code man nicht bearbeiten kann
- Unterschiedliche Typen:
  - Datenbank Exporter
  - Hardware/Software Metriken -> Node Exporter
  - Messaging/Storage/HTTP
  - Vielen API Endpoints haben auch Exporter (AWS, Azure, Docker, ...)

### Service Discovery

- Prometheus greift auf die Kubernetes API zu, um neue Pods/Services/Nodes zu erkennen

### Scraping

- Pull based System, dh Prometheus entscheidet aufgrund seiner Konfiguration, wann welche Ressourcen abgefragt werden
- Scraping = HTTP Request nach den Metriken von der Ressource
- Antwort auf den Request wird geparst und in Storage gespeichert
- Außerdem werden weitere Metriken hinzugefügt, zB ob der Request erfolgreich war und wie lange er gebraucht hat 
