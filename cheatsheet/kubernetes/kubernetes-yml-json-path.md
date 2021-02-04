---
layout: topic
title: yaml-and-json-path
parent: kubernetes
---

## JSON Path in Kubernetes

Json Path Basics, siehe unten.

Mit kubectl Daten von Kubernetes Ressourcen abfragen. 
Um komplexere Abfragen einfach ausführen zu können, unterstützt kubectl auch json path.
``$`` ist nicht notwendig -> .items[0]...
Hochkomma und geschweifte Klammern werden benötigt.

**Beispiel**

Anforderung: Welches Image verwendet der erste Container vom erste Pod im default Namespace

``kubectl get pods -o=jsonpath='{.items[0].spec.containers[0].image}'``

### Kombinieren von Abfragen

Queries können auch miteinander kombiniert werden.

``kubectl get pods -o=jsonpath='{xyz}{abc}'``

**Beispiel**

Anforderung: Alle Node Namen und deren CPU Capacity anzeigen

``kubectl get nodes -o=jsonpath='{.items[*].metadata.name}{.items[*].status.capacity.cpu}'``

Output:

``master node01 4 4``

Schöner mit Newline ``{\n}`` und Tab ``{\t}``

``kubectl get nodes -o=jsonpath='{.items[*].metadata.name}{"\n"}{.items[*].status.capacity.cpu}'``

Output:

```
master node01
4      4
```

### Loops - Ranges

Loop kann genutzt werden, um über eine Liste zu iterieren und Eigenschaft von jedem Element auszugeben.

**Beispiel**

Anforderung: Für jeden Node den Name und die CPU Capacity anzeigen, mit Format: <name> <cpu> als Zeile

| Pseudocode                                 | JSON Path               |
| ---------- --------------------------------| ----------------------------------- |
| For EACH NODE                              | '{range .items[*]}
|     Print Node Name \t Print CPU Count \n  |      {.metadata.name}{"\t"}{.status.capacity.cpu}{"\n"}
| End For                                    | {end}'
|                                            |

Befehl:

``kubectl get nodes -o=jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.status.capacity.cpu}{"\n"}{end}'``

Output:

```
master 4
node01 4
```

### Custom Columns

``kubectl get nodes -o=custom-columns=<COLUMN NAME>:<JSON PATH>``

Erzeugt automatisch eine Tabelle mit Spalten für jedes Element in der Liste.
.items[*] wird deswegen nicht benötigt.

**Beispiel**

Anforderung: Für jeden Node Namen und CPU Capacity anzeigen in Spalten mit Header NODE und CPU

``kubectl get nodes -o=custom-columns=NODE:.metadata.name,CPU:status.capacity.cpu``

Output:

```
NODE   CPU
master  4
node01  4
```

### Sortieren des Outputs

``kubectl get nodes --sort-by=.metadata.name``

## YAML

### Kommentare

```yaml
# Kommentar
Kein Kommentar
```

Form um Daten darzustellen. 

### Key Value Pair

```yaml
Frucht: Apfel
Fleisch: Huhn
[...]
```

### Array/List

- Geordnete Collection -> Zwei Listen mit gleichen Werten in unterschiedlichen Reihenfolgen sind verschieden.
- Speichern einer Eigenschaft von mehrere Objekte des gleichen Typs


Beispiel:

```yaml
Früchte:
- Orange
- Apfel
- Banane
[...]
```

### Dictionary/Map

- Ungeordnete Collection -> Zwei Maps mit gleichen Werten in unterschiedlichen Reihenfolgen sind gleich.
- Speichern von Eigenschaften eines einzelnen Objekts.

Beispiel:

```yaml
Banane:
	Kalorien: 75
	Fett: 15
```

### Liste von Dictionaries

Speichern von Objekten des gleichen Typs mit allen Eigenschaften.

```yaml
Früchte:
- 	Name: Orange
	Nährstoffe:
		Fett: 15
		Eiweis: 5
	Kalorien: 75
	Preis: 2.5
-   Name: Apfel
	Nährstoffe:
		Fett: 5
		Eiweis: 9
	Kalorien: 63
	Preis: 2.1
```

## Json

### Kommentare

### Key Value Pair

Wie oben.

```json
{
	"Frucht": "Apfel",
	"Fleisch": "Huhn"
}
```

### Array/List

Wie oben.

```json
{
	"Früchte": [
		"Orange",
		"Apfel",
		"Banane"
	]
}
```

### Dictionary/Map

Wie oben.

```json
{
	"Banane": {
		"Kalorien": 75,
		"Fett": 15
	}
}
```

### Liste von Dictionaries

Wie oben.

```json
{
	"Früchte": [
	 	{
 	        "Name": "Orange",
		    "Kalorien": 75,
		    "Preis": 2.5
		}, 
	    {
	        "Name": "Apfel",
			"Kalorien": 63,
			"Preis": 2.1
		}
	]
}
```

### Json Path

- Query Language um aus Daten im Jsonformat abzufragen.
- ``$`` für Root Element (außenliegende Klammern ``{ }``)
- Ergebnis kommt als Array zurück (mit Klammern ``[ ]``)

#### Beispiel mit Dictionary:

```json
{
	"Auto": {
		"Farbe": "Rot",
		"Preis": 15000
	},
	"Bus": {
		"Farbe": "Grün",
		"Preis": 25000
	}
}
```

Query: $.Auto
Query: $.Auto.Farbe

#### Beispiel mit List

Liste mit Index 0, 1, ...
Index vom letzten Element gezählt -1, -2, ...

Bsp:
```
- Apfel (0 / -2)
- Birne (1 / -1)
- Brot (2 / -1)
```

- Erstes Element: $[0]
- Drittes Element: $[2]
- Erstes und drittes Element: $[0, 2] 
- Erstes bis drittes Element: $[0:3] (3 ist nicht enthalten)
- Jedes x. Element der ersten 8: $[0:8:x]
- Letzter Eintrag: $[-1:]
- Letzte 3 Einträge: $[-3:]


#### Beispiel mit Dictionary und List

```json
{
	"Früchte": [
	 	{
 	        "Name": "Orange",
		    "Nährstoffe": {
			    "Fett": 15,
			    "Eiweis": 5
		    },
		    "Kalorien": 75,
		    "Preis": 2.5
		}, 
	    {
	        "Name": "Apfel",
			"Nährstoffe": {
				"Fett": 5,
				"Eiweis": 9
			},
			"Kalorien": 63,
			"Preis": 2.1
		}
	]
}
```

Anforderung: Eiweis von der ersten Frucht
Query: ``$.Früchte[0].Nähstoffe.Eiweis``

#### Kriterien

- ``?()`` Kriterium
- ``@`` Alle Items, die Kriterium erfüllen
Operatoren:
	- ==
	- !=
	- >
	- <
	- in [x,y,z] 
	- nin [x,y,z]

Zahlen
	Anforderung: Alle Zahlen größer als 10
	-> Query: ``$[?(@ > 10)]``
Strings
	Anforderung: Finde Preis von Frucht, deren Eiweis "5g" ist
	-> Query: ``$.Früchte[?(@.Nährstoffe.Eiweis == "5g")].Preis``

#### Wildcards

Anforderung: Alle Farben von allen Autos
Query: 
	Map: $.*.Farbe
	Liste: $.[*].property
