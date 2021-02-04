---
layout: topic
title: troubleshooting
parent: kubernetes
---

## Troubleshoot kubernetes

### Helper pod

```bash
Kubectl run util --image=busybox --rm -it --restart=Never -- sh
```

### Applicatoin Failure

[Debug Application](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/)

Test all layers
- service
- pod events
- pod logs

### Controll Plane Failure

[Debug Cluster](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/)

Check nodes 
- kubectl get nodes

mit kubeadm
- pods und events -n kube-system
- pod logs -n kube-sytem

Services
- Service Status
	Master Node:
	- service kube-apiserver status
	- service kube-controller-manager status
	- service kube-scheduler status
	Worker Node:
	- service kubelet status
	- service kube-proxy status
- Service Logs
	- zB mit journalctl: sudo journalctl -u kube-apiserver
	
### Worker Node Failure

Check nodes -> Describe
- Siehe ``Conditions`` Tabelle 
	-> Status Spalte
	-> LastHeartbeatTime Spalte
	
Auf dem Node
- top -> Prozesse
- df -h -> Freier Festplaten-Platz

Check Status und Logs von kubelet
- service kubelet status
- sudo journalctl -u kubelet

Check kubelet Certificates
- openssl x509 -in /var/lib/kubelet/worker-1.crt -text
-> Check CA Issuer
-> Check Subject
-> Check Not Before/After