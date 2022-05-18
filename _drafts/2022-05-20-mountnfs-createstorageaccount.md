---
layout: post
title: "Title"
categories: [Category]
---

## Contents

## What is?

## The Demo Environment

## Prerequisites

## Create NFS enabled Storage Account

### Configure Basic Settings

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-basics.jpg)

### Configure Advanced Configuration

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-advanced.jpg)

### Configure Networking

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-networking.jpg)

## Configure Data Protection

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-dataprotection.jpg)

## Configure Encryption

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-encryption.jpg)

## Configure Tags

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-tags.jpg)

## Create

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-create.jpg)

## Deployment

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-deployment.jpg)

## Storage Account Overview

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-storageaccount-overview.jpg)




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