# Kasten
## Generic Storage Backup and Restore
### Requirement
### K3 cluster Installation :
```
$url -sfL https://get.k3s.io | sh -
$export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
$sudo chmod 644 $KUBECONFIG 
$kubectl get node
```
### Enable Kanister Sidecar Injection while installing k10
### K10 installation:
#### Prerequisites :
```
$helm repo add kasten https://charts.kasten.io/
$kubectl create namespace kasten-io 
$helm install k10 kasten/k10 --namespace=kasten-io \
   --set injectKanisterSidecar.enabled=true \
   --set-string injectKanisterSidecar.namespaceSelector.matchLabels.k10/injectKanisterSidecar=true # optional
```   
 ### Validating the install
*$kubectl get pods --namespace kasten-io –watch*
#### kasten dashboard access
*$kubectl --namespace kasten-io port-forward service/gateway 8080:8000* 
#### Deploy the application
*Create a namespace for deployment:*
```
$kubectl create namespace <namespace>
```
*If injectKanisterSidecar.namespaceSelector labels are set while installing K10, add the labels to namespace to match namespaceSelector*
```
*$kubectl label namespace <namespace> k10/injectKanisterSidecar=true*
```
#### Deploying in a specific namespace
*$kubectl apply --namespace=<namespace> -f deployment.yaml*
#### List pods
*$kubectl get pods --namespace=<namespace> | grep demo-app*  
