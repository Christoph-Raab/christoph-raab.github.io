---
layout: topic
title: certification-hints
parent: kubernetes
---

## Certification Hints

### Imperative vs Declarative

#### Imperative

- Definition von Schritten, die ausgeführt werden sollen
- Mit oder ohne Manifest (yaml File)
- Vor allem im Examen wichtig, da es Zeit spart
- Nutzen, um Manifest zu erzeugen (-o yaml)
- Befehle:
	- run
	- create
	- replace
	- expose
	- edit
	- scale
	- set

#### Declarative

- Definition von Ergebnis, das erreicht/erhalten werden soll
- Nur mit Manifest (yaml File)
- Befehl:
	- apply
- 'Last applied config' wird in das Kubernetes Objekt als Annotation geschrieben:

```bash
kubect.kubernetes.io/last-applied-configuration: {"apiVersion": "v1", "kind": ... }
```
### Create yml

#### Pod

```bash
kubectl run nginx --image=nginx --dry-run=client -o yaml > pod.yml
```

Get yml of running pod:

```bash
kubectl get <pod> -o yaml > pod.yml
```

#### Deployment

```bash
kubectl create deployment --image=nginx nginx --dry-run=client -o yaml > deploy.yml
```

#### Service

Type: ClusterIP

```bash
kubectl expose pod redis --port=6379 --name redis-service --dry-run=client -o yaml > svc.yml
```

or

```bash
kubectl create service clusterip redis --tcp=6379:6379 --dry-run=client -o yaml > svc.yml
```

Type: NodePort

```bash
kubectl expose pod nginx --port=80 --name nginx-service --type=NodePort --dry-run=client -o yaml > svc.yml
```

or

```bash
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080 --dry-run=client -o yaml > svc.yml
```

