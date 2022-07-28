# Kasten
## Generic Storage Backup and Restore
### Requirement
### K3 cluster Installation :
```
$curl -sfL https://get.k3s.io | sh -
$export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
$sudo chmod 644 $KUBECONFIG 
$kubectl get nodes
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
 ```
$kubectl get pods --namespace kasten-io –watch
```
#### kasten dashboard access
```
$kubectl --namespace kasten-io port-forward service/gateway 8080:8000
```
**The K10 dashboard will be available at http://127.0.0.1:8080/k10/#/**

#### Deploy the application
*Create a namespace for deployment:*
```
$kubectl create namespace <namespace>
```
*If injectKanisterSidecar.namespaceSelector labels are set while installing K10, add the labels to namespace to match namespaceSelector*
```
$kubectl label namespace <namespace> k10/injectKanisterSidecar=true
```
#### Deploying in a specific namespace
``` 
$kubectl apply --namespace=<namespace> -f deployment.yaml
```
#### List pods
```
$kubectl get pods --namespace=<namespace> | grep demo-app
```
### Insert Data

#### Get pods for the demo application from its namespace

**Copy required data manually into the pod**
```   
$kubectl cp <file-name> <namespace>/<pod>:/data/
   ```
**Verify if the data was copied successfully**
```
$kubectl exec --namespace=<namespace> <pod> -- ls -l /data
```  

 ## Take backup data from kasten (create policy)
 ![image](https://user-images.githubusercontent.com/96052107/181490958-8a44cca3-f45b-47b4-b540-820e7cf0fc13.png)
 
 
 ## Create location profile : 
 ![image](https://user-images.githubusercontent.com/96052107/181493595-7e505d17-2961-46e5-ac46-0c0832b3e883.png)
 
 ## Backup data 
![image](https://user-images.githubusercontent.com/96052107/181498005-cadaf0cb-4f76-48a3-8e22-abf7f9af94ff.png)

## Destroy Data 
**To destroy the data manually, run the following command:**
```
$kubectl exec --namespace=<namespace> <pod> -- rm -rf /data/<file-name>
```

## Restore data from kasten

## Restore the data using K10 by selecting the appropriate restore point

![image](https://user-images.githubusercontent.com/96052107/181494351-19221326-dd79-4a02-adb6-2f86937fec20.png)

## Restore data from kasten dashboard

![image](https://user-images.githubusercontent.com/96052107/181495099-c159856a-5544-4ffc-9a92-10c71d3bd866.png)

## Verify Data
**After restore, you should verify that the data is intact. One way to verify this is to use MD5 checksum tool**


**MD5 on the original file copied**
```
$md5 <file-name>
```
**Copy the restored data back to local env**
```
kubectl get pods --namespace=<namespace> | grep demo-app
kubectl cp <namespace>/<pod>:/data/<filename> <new-filename>
```
**MD5 on the new file**
```
md5 <new-filename>
```

