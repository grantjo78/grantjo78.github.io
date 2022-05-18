---
layout: post
title: "Title"
categories: [Category]
---

## Contents

## What is?

## The Demo Environment

## Prerequisites

### Extenstions

```
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```



## Cluster extensions


```
az k8s-extension create --name azuremonitor-containers  /
                        --extension-type Microsoft.AzureMonitor.Containers /
                        --scope cluster /
                        --cluster-name <clusterName> /
                        --resource-group <resourceGroupName> /
                        --cluster-type connectedClusters
```


```
Example

az k8s-extension create --name azuremonitor-containers /
                         --extension-type Microsoft.AzureMonitor.Containers /
                         --scope cluster /
                         --cluster-name "k3s-cluster-01" /
                         --resource-group "rg-arc-kubernetes-hyperv" /
                         --cluster-type connectedClusters
```

sudo az k8s-extension create --name azuremonitor-containers \
                         --extension-type Microsoft.AzureMonitor.Containers \
                         --scope cluster \
                         --cluster-name "k3s-cluster-01" \
                         --resource-group "rg-arc-kubernetes-hyperv" \
                         --cluster-type connectedClusters


                         sudo az k8s-extension list --cluster-name "k3s-cluster-01" \
                         --resource-group "rg-arc-kubernetes-hyperv" \
                         --cluster-type connectedClusters