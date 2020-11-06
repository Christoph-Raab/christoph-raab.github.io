

- sql server dient nur als Kontrollmechanismus
	- sql server und sql db können in unterschiedlichen Regionen liegen
	
## main features

- system and service configurations handled by microsoft
	
- scalable storage

- performance scaling with AI

- Einfaches Konfigurieren von Netzwerk- und Zugangsbeschränkungen

## Share Responsibility für PaaS

- Anwender muss ich nur kümmern um...
	- Daten
	- Wer kann auf die Daten zugreifen
	- Wie kann man auf die Daten zugreifen
	
- Microsfot kümmert sich um... (ua)
	- Patching
	- Maintenance
	- Backups
	
- Möglichkeit zu Erweitungen, zB eigene Backup Policy
	
- Hyperscale
	- Datenbanken kann auf bis zu 100 TB wachsen
	- Unendliche Backups
	- Instant Recovery
	- Read/Write Replicas
	
## Pricing Models

- Nur die Datenbank verursacht Kosten, Server nicht

- DTUs
	- Database Transaction Units
	- Kaufen und Nutzen auf per Unit Basis
	- Am besten vor vorhersagbare Workloads
	- eDTU für Elastic Pools, die von allen Datenbanken im Pool geteilt und genutzt werden können
	
- vCore 
	- Pay as you go
	- Gut für schwer vorhersagbare Workloads
	- Feste Kosten, die nur auf Basis der Storage Skalierung wachsen

- Serverless
	- Pay for resources in use only
	- Kosten variieren basieren auf dem Workload
	- Voraussetzung ist ein echtes Verständnis des Workloads
	
- Hybrid Benefit
	- Man zahlt nur für die Hardware und bringt seine eigene SQL Lizenz

## Vor-/Nachteile

- Vorteil
	- Man ist immer auf der latest Version von MSSQL (Bugfixes, Security Bugs, Verbesserungen, "Lebenslanger Support")
	- Hohe Flexibilität in Scaling In/Out
	- Einfache Sicherheit, da nur Daten und Nutzer
	- Weniger Kosten als OnPrem, va für Management
	- Immer auf dem neusten Stand
	
- Nachteil
	- Weniger "fine-tuning" Kontrolle über Konfiguration der Server Instanz
	- Kosten können durch Out Skalierung aus dem Ruder laufen
	- Code und Feature Deprecation durch konstante, unvorhergesehene Updates
	- Kompatibilität könnte Probleme verursachen, da in der Cloud einige Dinge anders funktionieren