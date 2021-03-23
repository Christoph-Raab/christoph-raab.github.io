---
layout: topic
title: Switching And Routing
parent: docker
---

## Networking in Docker

``docker run --network <option> <container>``

options:
- none: Container ist mit keinem Netzwerk verbunden
- host: Container ist an das Host Netzwerk angeschlossen
- bridge: Internal private Network wird erzeugt und der Container ist daran angeschlossen

### Bridge

Bei Installation von Docker wird ein Default private VNet erzeugt "docker0" bzw. bridge.

Anzeigen mit ``docker network ls``. Wird ähnlich zu einem Namespace auf dem Host hinzugefügt. Mit ``docker inspect <name>`` untersuchen.

Bridge ist ein Interface auf dem Host und ein Switch für die Docker Container (Namespaces) zugleich.

Bridge Option erzeugt Pip zwischen Ddockers Bridge VNet und dem Container.

Damit ist der Container von innerhalb des Hosts erreichbar. Für Erreichbarkeit von Außerhalb wird Portmapping benötigt:
``-p <Host-Port>:<Container-Port>``

