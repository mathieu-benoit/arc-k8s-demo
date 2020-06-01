Demo about Azure Arc enabled Kubernetes

## Prerequisites

- Azure CLI, [installation here](https://docs.microsoft.com/cli/azure/install-azure-cli)
- Helm 3, [installation here](https://helm.sh/docs/intro/install/)
- Azure CLI extensions `ConnectedK8s` and `KubernetesConfiguration`, [installation here](https://docs.microsoft.com/azure/azure-arc/kubernetes/connect-cluster#install-azure-cli-extensions)

## Setup

Deploy an AKS cluster in `EastUS` with `1.16.9`:
```
name=mabenoiteastus
location=eastus
az group create \
  -n $name \
  -l $location
az aks create \
  -l $location \
    -n $name \
    -g $name \
    --no-ssh-key \
    -k 1.16.9 \
    -s Standard_B2s \
    -c 1 \
    --enable-managed-identity

# Create a Connected Kubernetes service
export HELMREGISTRY=azurearcfork8s.azurecr.io/batch1/stable/azure-arc-k8sagents:0.2.4
az connectedk8s connect \
  -n $name \
  -g $name
  
# Onboard the Connected Kubernetes service with Azure Monitor for containers
curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
connectedk8sId=$(az connectedk8s show \
  -n $name \
  -g $name \
  --query id \
  -o tsv)
bash onboarding_azuremonitor_for_containers.sh $connectedk8sId $name
  
# Create a Kubernetes Configuration (GitOps) hooked to the azure-vote-app
az k8sconfiguration create \
  -n azure-vote-config \
  -c $name \
  -g $name \
  --operator-instance-name azure-vote-config \
  --operator-namespace azure-vote-config \
  -u https://github.com/mathieu-benoit/azure-arc-k8s-demo \
  --scope cluster \
  --cluster-type connectedClusters \
  --operator-params '--git-readonly --git-poll-interval=30s --git-path=azure-vote-app'
```

## Demo

TODO

## Cleanup for Demo

TODO

## Full cleanup

```
az group delete -n $name
```
