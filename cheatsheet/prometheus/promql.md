## PromQL

[Docs zum Querying](https://prometheus.io/docs/prometheus/latest/querying/basics/)

### Basics

- 4 Data Types
  - Instant Vector
  - Range Vector
  - Scalar
  - String
- Für Graphen muss die Daten entweder als Instant Vector oder als Scalar zu Verfügung stehen
- Daten kommen mit Label in Prometheus an, dh man kann danach filtern (job, mode, instance, ...)

### Operations und Functions

[Docs zu Operators](https://prometheus.io/docs/prometheus/latest/querying/operators/)

- Standard Operatoren (+, -, *, /, %, ^)
- Logical Operators (and, or, unless)
- Aggregation Operators (sum, min, max, avg, ...)

[Docs zu Functions](https://prometheus.io/docs/prometheus/latest/querying/functions/)

- irate(): 
  - Berechnet per-second instant rate of increase of time series in a range vector
  - Kann zB Graph erzeugen, wenn es sonst nicht möglich wäre
- absend():
  - Gibt 1 zurück, wenn es für ein Query keine Ergebnisse gibt

- Auch Gruppierung (by) und Verschachtelungen (avg(irate()) möglich

### Recording Role

[Recording Rules](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/)

- rules_file kann in ConfigMap referenziert werden
- rules_files können dann als ConfigMap erzeugt und als Volume gemountet werden
- Recording Rules definieren häufig genutzte Queries als Regel
- Ergebnis wird in einer TimeSeries gespeichert
