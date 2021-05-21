# Environment Setup

## Prerequisites

- Azure CLI
- Kubectl
- Helm, install steps:
   ```
   curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
   sudo apt-get install apt-transport-https --yes
   echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
   sudo apt-get update
   sudo apt-get install helm
   ```

## Create AKS with NodePools
- create AKS
```
az aks create
    --resource-group aks-tests \
    --name amlgpu \
    --node-vm-size Standard_D2s_v3 \
    --node-count 3 \
    --location westus2 \
    --kubernetes-version 1.18.4
    --generate-ssh-keys
```

- Follow steps to enable Preview GPU VM Image for AKS cluster https://docs.microsoft.com/en-us/azure/aks/gpu-cluster#use-the-aks-specialized-gpu-image-on-existing-clusters-preview

- Create GPU Enabled SPOT VM Node Pool
```
az aks nodepool add \
    --resource-group aks-tests \
    --cluster-name amlgpu \
    --name spotgpuvhd \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --node-vm-size Standard_NC12 \
    --aks-custom-headers UseGPUDedicatedVHD=true,usegen2vm=true \
    --no-wait
```    

- Create CPU NodePool for running ETCD
```
az aks nodepool add \
    --resource-group aks-tests \
    --cluster-name amlgpu \
    --name workers \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --node-vm-size Standard_D2s_v3 \
    --no-wait
``` 

- Apply Taints To System Nodes to prevent scheduling on them

```
kubectl get nodes
kubectl taint nodes <node name> CriticalAddonsOnly=true:NoSchedule
```
- Verify Taints

```
kubectl get nodes -o json | jq '.items[].spec.taints'
```

See the output like this for Spot and System nodes taints:
```json
[
  {
    "effect": "NoSchedule",
    "key": "CriticalAddonsOnly",
    "value": "true"
  }
]
[
  {
    "effect": "NoSchedule",
    "key": "kubernetes.azure.com/scalesetpriority",
    "value": "spot"
  }
]
```

## Install NVidia driver (Optional if NOT using GPU VM Image)


## Install Torch Elastic 

### Install etcd
- Deploy an etcd server. This will expose a Kubernetes service etcd-service with port 2379.
```
 kubectl apply -f kube/etcd.yaml
```

### Install Torch Elstic Controller

```
kubectl apply -k kube/config/default
```

Verify that the ElasticJob custom resource is installed
```
kubectl get crd
```

## Install Blob CSI Driver

```
helm repo add blob-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/blob-csi-driver/master/charts
helm install blob-csi-driver blob-csi-driver/blob-csi-driver --namespace kube-system --version v1.1.0 
```


## Create Storage and Volumes

- Create Storage account, SAS token (with delete,write,read,list permissions) and container with name `workerdata`

- Create Kubernetes secret to access the Blob

```
kubectl create secret generic azure-blobsecret --from-literal azurestorageaccountname=<STORAGE ACCOUNT NAME> --from-literal azurestorageaccountsastoken="<SAS TOKEN>" --type=Opaque
```

- Apply K8S manifests for Persistent Volume and Volume Claim pointing to Storage account

```
kubectl apply -f kube/azure-blobfuse-pv.yaml
```


