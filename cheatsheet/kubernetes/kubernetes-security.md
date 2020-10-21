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
