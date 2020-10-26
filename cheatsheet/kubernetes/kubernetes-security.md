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
