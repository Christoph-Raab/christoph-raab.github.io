## Idle Timeout
InBound LoadBalancer hat als Standard (zumindest wenn von Istio erzeugt) eine default IdleTimeout von 4 Minuten (=Minimum).

Standard OutBound LoadBalancer (von kubernetes erzeugt) hingehen hat ein default IdleTimeout von 30 Minuten (= Maximum).

## SSH into Node mit WSL

Besser ``kube-exec`` nutzen:

https://github.com/mohatb/kubectl-exec

### Install openssh-client

``
sudo apt install openssh-client -y
``

### Private key muss im HOME Verzeichnis liegen, sonst Permissions Problem

``
sudo cp id_rsa /ssh-keys/custom/id_rsa
``

### Key Nutzen für SSH

``
ssh -i /ssh-keys/custom/id_rsa <user>@<ip>
``

--> Bei Passwort Abfrage: Root Passwort mit dem der Private Key erzeugt wurde.

## Netzwerk Regeln die für AKS notwendig sind

Reference: https://docs.microsoft.com/en-us/azure/aks/limit-egress-traffic#azure-global-required-network-rules
