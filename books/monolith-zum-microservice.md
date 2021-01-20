

## 1 Nur so viel Microservice wie möglich

### Vor der Entscheidung wie erstmal die Frage stellen ob

- Welches Ziel will man mit Microservies erreichen? 
- Welche Anzahl an einzelnen Anwendungen kann das Team/die Firma managen? 
- Kann das Team das Drumrum (Orchestration, Monitoring, ...) abbilden?
- Gibt es einen anderen, weniger komplexen Ansatz?

### Monolith vs Microservice

Monolith ist eine große Codebasis, die gemeinsam deployt werden muss. 
Es gibt auch verteilte Monolithen, dh mehrere Anwendungen, die aber immer gleichzeitig deployt werden müssen.

Microservices sind dagegen voneinander unabhängig deploybare Anwendungen. 

### Übergang

Extraktion sollte auf DDD Vorgehen basieren.
Sie ist abgeschlossen, sobald der neue Microservice im Produktivbetrieb genutzt wird.
Geeigneter erster Kandidat über Eventstorming: Von wo nach wo fließen welche Events? Wo sind wenige Abhängigkeiten?

Ideal ist ein Featurefreeze für den Bereich, der gerade extrahiert wird. 

### Designtipps

- API Endpunkte sollten klug sein, die Verbindungen dazwischen dumm (keine Logic im Netzwerk (zB mit Proxy)).
- Lieber erstmal schauen ob es auch serverless geht

## 2 Mögliche Probleme durch Microservices

- Lokale Entwicklererfahrung
  Bei der Entwicklung müssen viele MS gestartet werden. Orchestrierung mit Environment Injection / Secrets erschwert das.
  - Tools wie [Telepresence](https://www.telepresence.io/)
  - IDE Plugins erleichtern die Arbeit.
  
- Ownership
  Wem gehört die Codebasis? 
  - Strong Code Ownership: Von außerhalb des Teams nur mit Genehmigung über PR.
  - Weak Code Ownership: Von außerhalb des Teams nur mit Absprache.
  - Collective Code Ownership: Jeder kann machen, was er für richtig hält.

- Disruptive Veränderung
  Höherer Abstimmungsbedarf. Es besteht Gefahr, dass man einen anderen Service kaputt macht, weil die Schnittstelle sich geändert hat.
  - Schemata nutzen
  - Contract Driven Tests
  - Rückwärtskompatible APIs

- Reporting
  Daten sind über verschiedenste Microservices verteilt
  - Gemeinsame Reporting Datenbank, die über Syncjobs gefüllt wird

- Monitoring & Troubleshooting
  Verteilte Software erschwert Fehlersuche
  - Log Aggregation
  - Tracing 
  - Observability mit Istio + [Kiali](https://kiali.io/) 

- Globale vs Lokale Optimierung
  Im Team reversible Entscheidungen treffen, irreversible Entscheidungen global treffen.

- Robustheit & Resilienz
  Ausfall und Unerreichbarkeit durch verteilte Software
  - Replicas laufen lassen
  - Autoskalierung und Self-Healing

- Verwaiste Services
  Evtl. nicht mehr gepflegte Services
  - Gutes Management Board
