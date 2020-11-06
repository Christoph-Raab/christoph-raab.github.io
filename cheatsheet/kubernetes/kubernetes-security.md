---
layout: topic
title: security
parent: kubernetes
---


## Security

### TLS on kubernetes

- Pubic Key Infrastructure:
  - Asymmetric TLS encryption mit Private/Public KeyPair
  - Public Key:
    - .crt oder .pem
  - Private Key: 
    - .key oder *-key.pem

- Kommunikation zum kube-apiserver:
  - Mit kubectl
  - Innerhalb des Clusters zw. den Komponenten
  - etcd hat eigene Zertifikate
  
### KubeConfig

- Clusters
	- Unterschiedliche Cluster (Dev, Prod, Kunde ABC, ...)
	- Server Certificate
- Contexts
	- Maps Cluster und User (Admin@Kunde ABC, Dev@Prod, ...)
- Users  
	- Nutzer, die Zugriff haben (Admin, Dev, ...)
	- Bestehende Nutzer mit Rechten
	- User Keys und Certificates
- Wird genutzt von kubectl	
	- Nutzt CurrentContext
- über 'namespace' Feld im Context kann auch der Namespace gesetzt werden

### RBAC

- APIGroups sind Untergruppen vom kube-apiserver, die zB mit curl direkt angesprochen werden können
	- /apis, /api, /version, /healthz, /metrics, /logs
	- /api = core (/v1 -> namespace, pod, pvc, secret, ...)
	- /apis = named (neu, /apps, /storage.k8s.io, ...)
		- Alle neuen Resources sind named apis (Deployments, ReplicaSets, ...)
- kubectl proxy, um lokal ohne Auth auf kube-apiserver zuzugreifen
- Verbs (get, list, create, ...) beschreiben erlaubte Methoden auf dem Objekt

- Role definiert Zugriffsrechte als Rolle
	- Gelten für einen Namespace (metadata.namespace)
	- Gelten für namespace Resourcen (Pods, Jobs, Roles, PVC, Secret, ConfigMap, ...)
		- ``kubectl api-resources --namespaced=true``
	- apiGroups ([""] = core)
	- resources: zB ["pods"]
	- verbs: zB ["list", "get"]
	- optional: resourceNames: ["my-app1", "my-app2"] (Erlaubt nur Zugriff auf Pods mit diesen Namen)
- RoleBinding mappt Role mit Nutzern (über subjects und roleRef)

- Um herauszufinden, was ich als Nutzer kann:
	- ``kubectl auth can-i <verb> <resource> --namespace <namespace>``
	- ``kubectl auth can-i create deployments`` (im ns default)
	- --as <name> um als Admin Permissions von anderem Nutzer zu testen

- ClusterRole/ClusterRoleBindings
	- Gelten für clusterweite Ressourcen (zB Node, PV, Namespace, ...)
		- ``kubectl api-resources --namespaced=false``
	- Sonst wie Roles und RoleBindings
	- Können aber auch für namespaced resources genutzt werden

### Image Security

- Zugang zu privater Docker Registry:
	- Secret mit Type docker-registry
		- --docker-server=
		- --docker-username=
		- --docker-password=
		- --docker-email=
	- Im pod als imagePullSecret setzen
	
### Security Context

- Definieren von run as user/capability für Pod oder Container
- Falls auf Containerebene und auf Podebene eingestellt, überschreibt Containter Config die vom Pod
- run as user: pod/container
- capabilities: container

### Certificate Setup/Health

- "Hard Way" 
	- im Service, zB kube-apiserver
	- Flags im Service (tls-cert-file, ...)
- kubeadm
	- Command Flag im Static Pod File
	- Standardmaäßig unter /etc/kubernetes/manifests/kube-apiserver.yaml

- Health:
	- Zertifikate validieren
	
	``openssl x509 -in /etc/kubernetes/pki/apiserver.crt -text -noout``
	
	- Testen der Felder
		- Subject
		- Alternative Name
		- Validity
		- Issuer
	- Logs prüfen mit ``kubectl logs ...`` oder falls kubectl nicht verfügbar mit docker
	- [HealthCheck Checkliste](https://github.com/mmumshad/kubernetes-the-hard-way/tree/master/tools)
	
### Manage Certificates

- Kubernetes Master ist auch Zertifikat-Server
- Läuft alles über Controller Manager
	- Enhält dementsprechend cluster-signing-cert/key-file
- CertificateSigningRequest Object in Kubernetes mit CSR vom Nutzer (base64) anlegen 
- Über API Call CRS von User, der von Admin geprüft und freigebenen wird
	- ``kubectl get csr``
	- ``kubectl certificate approve <name>``
- Im CSR wird nach dem Approve ein Zertifikate in base64 angehängt (status.certificate)

### Secure kube-apiserver
- Kommunikation zwischen kube-apiserver und den anderen Komponenten ist ssl verschlüsselt

- Authentication for Cluster Management:
  - Files 
    - Username/Password
    - csv file mit pwd,usrname,uuid[,group]
    - als --basic-auth-file param bei kube-apiserver Installation angeben oder static Pod anpassen (kubeadm Installation)
    - Basic Auth Header an Request anhängen (curl)
  - Files - Username/Token
    - csv file mit pwd,usrname,uuid[,group]
    - als --token-auth-file param bei kube-apiserver Installation angeben oder static Pod anpassen (kubeadm Installation)
    - Token als Bearer Token an den Request anhängen (curl)
  - Certificates
    - create key/pem/crt + Certificate Auth. pem
    - configure servers/clients mit den key/pem und CA pem
    - curl https://kube-apiserver:6443/api/v1/pods --key x.key --cert y.crt --cacert ca.crt
  - External Providers (LDPA)
  - Service Accounts (für Pods)


- Authorization for Cluster Management:
  - RBAC (Groups with Permissions)
  - ABAC
  - Node Authorizatio
  - Webhook Mode
  
### NetworkPolicy

- Kommunikation zw. Pods ist by default nicht eingeschränkt
- Mit NetworkPolicy können Ingress und Egress Regeln erstellt werden
- Genutzt werden Labels (zB app oder role) und ports
- Werden von der Network Plugin erzwungen, die im Cluster installiert sind
	- Unterstützt NetworkPolicy (Auszug):
		- Kube-router
		- Calico
		- Romana
		- Weave-net
	- Nicht untersützt:
		- Flannel
