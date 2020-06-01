# arc-k8s-demo

Demo about Azure Arc enabled Kubernetes

## Setup

Deploy an AKS cluster in EastUS with 1.16.9:
```
name=mabenoiteastus
location=eastus
az group create -n $name -l $location
az aks create -l $location -n $name -g $name --no-ssh-key -k 1.16.9 -s Standard_B2s -c 1 --enable-managed-identity
az aks get-credentials -g $name -n $name
export HELMREGISTRY=azurearcfork8s.azurecr.io/batch1/stable/azure-arc-k8sagents:0.2.4
az connectedk8s connect -n $name -g $name
```

Deploy an AKS cluster in WestEurope with 1.14.7:'
```
name=mabenoitwesteurope
location=westeurope
az group create -n $name -l $location
az aks create -l $location -n $name -g $name --no-ssh-key -k 1.14.7 -s Standard_B2s -c 1 --enable-managed-identity
export HELMREGISTRY=azurearcfork8s.azurecr.io/batch1/stable/azure-arc-k8sagents:0.2.4
az connectedk8s connect -n $name -g $name
az k8sconfiguration create \
  -n azure-vote-config \
  -c $AKS \
  -g $AKS \
  --operator-instance-name azure-vote-config \
  --operator-namespace azure-vote-config \
  -u https://github.com/Azure/arc-k8s-demo \
  --scope cluster \
  --cluster-type connectedClusters \
  --operator-params '--git-readonly --git-path=cluster-apps'
```

+ minikube?

## Demo's scripts

