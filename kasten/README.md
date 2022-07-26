# Kasten
## Generic Storage Backup and Restore
### Requirement
### Enable Kanister Sidecar Injection while installing k10
### K3 cluster Installation :
`url -sfL https://get.k3s.io | sh -`
`export KUBECONFIG=/etc/rancher/k3s/k3s.yaml`
`sudo chmod 644 $KUBECONFIG`
### K10 installation:
#### Prerequisites :
`helm repo add kasten https://charts.kasten.io/`
`kubectl create namespace kasten-io`
