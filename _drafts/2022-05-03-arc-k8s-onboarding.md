---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 1: Onboarding"
categories: [Arc for Kubernetes]
---

In this series I'm going to be exploring Azure Arc-enabled Kuberneres.

[Part 1:]

This article will cover: 
1. [What is Azure Arc-enabled Kubernetes?](#what-is-azure-arc-enabled-kubernetes)
2. [The Environment](#the-environment)
3. [Arc-enabled Kubernetes - Azure Configuration](#arc-enabled-kubernetes---azure-configuration)
4. [Arc-enabled Kubernetes - Agent Deployment](#arc-enabled-kubernetes---agent-deployment)


## What is Azure Arc-enabled Kubernetes?

[Azure Arc-enabled Kubernetes allows you to attach and configure Kubernetes clusters running anywhere. You can connect your clusters running on other public cloud providers (such as GCP or AWS) or clusters running on your on-premise data center (such as VMware vSphere or Azure Stack HCI) to Azure Arc.](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/overview)

## The Environment

Before I get started, I wanted to explain the environment that I will be working with.

### The Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Virtual Machines

Within the Hyper-V host, I've deployed a two virtual machines:
- k3s-1
- k3s-2

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-guest-k3s.jpg)

Both virtual machines are running Ubuntu 20.04.4 LTS.

```
 Welcome to Ubuntu 20.04.4 LTS (GNU/Linux 5.4.0-109-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue 03 May 2022 06:48:21 AM UTC

  System load:  0.17              Processes:             123
  Usage of /:   7.3% of 61.51GB   Users logged in:       0
  Memory usage: 38%               IPv4 address for cni0: 10.42.1.1
  Swap usage:   0%                IPv4 address for eth0: 192.168.0.21


28 updates can be applied immediately.
To see these additional updates run: apt list --upgradable
```
### Kubenetes Environment

For the Kubernetes environment, I'm utilising [K3s](https://k3s.io/) because it's very quick to setup and lightweight. If you're interested in how to deploy K3s and configure a cluster you can follow the [quickstart guide](https://rancher.com/docs/k3s/latest/en/quick-start/).

My cluster consists of 2 nodes, a master (k3s-1) and a worker (k3s-2).

```
k3s-admin@k3s-1:~$ kubectl get nodes

NAME    STATUS   ROLES                  AGE   VERSION
k3s-1   Ready    control-plane,master   22h   v1.22.7+k3s1
k3s-2   Ready    <none>                 22h   v1.22.7+k3s1
```

## Arc-enabled Kubernetes - Azure Configuration

### Step 1: Azure Arc Service

From the Azure Portal select the **Azure Arc** service.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-service.jpg)

### Step 2: Azure Arc Overview

Under the **Infrastructure** section, select **Kubernetes clusters**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-overview.jpg)

### Step 3: Kubernetes clusters

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-add-cluster.jpg)

### Step 4: Prerequisites

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-prerequisites.jpg)

### Step 5: Cluster details

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-cluster-details.jpg)

### Step 6: Tags

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-tags.jpg)

### Step 7: Run script

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-run-script-stop.jpg)

## Arc-enabled Kubernetes - Agent Deployment

### Step 8: Agent deployment

```
k3s-admin@k3s-1:~$ az login
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code xxxxxxxx to authenticate.
```

```
k3s-admin@k3s-1:~$ az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx
k3s-admin@k3s-1:~$
```

```
k3s-admin@k3s-1:~$ az connectedk8s connect --name k3s-cluster-01 --resource-group rg-arc-kubernetes-hyperv --location australiaeast --tags Datacenter=Hyperv-01 City=Perth StateOrDistrict=WA CountryOrRegion=Australia --kube-config "/etc/rancher/k3s/k3s.yaml"
This operation might take a while...

Downloading helm client for first time. This can take few minutes...
Failed to validate if the active namespace exists on the kubernetes cluster. Exception: Invalid kube-config file. No configuration found.
{
  "agentPublicKeyCertificate": """",
  "agentVersion": null,
  "connectivityStatus": "Connecting",
  "distribution": "k3s",
  "id": "/subscriptions/xxxxxc-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-arc-kubernetes-hyperv/providers/Microsoft.Kubernetes/connectedClusters/k3s-cluster-01",
  "identity": {
    "principalId": ",
    "tenantId": "",
    "type": "SystemAssigned"
  },
  "infrastructure": "generic",
  "kubernetesVersion": null,
  "lastConnectivityTime": null,
  "location": "australiaeast",
  "managedIdentityCertificateExpirationTime": null,
  "name": "k3s-cluster-01",
  "offering": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "rg-arc-kubernetes-hyperv",
  "systemData": {
    "createdAt": "2022-05-03T09:17:18.193778+00:00",
    "createdBy": "",
    "createdByType": "User",
    "lastModifiedAt": "2022-05-03T09:17:18.193778+00:00",
    "lastModifiedBy": "",
    "lastModifiedByType": "User"
  },
  "tags": {
    "City": "Perth",
    "CountryOrRegion": "Australia",
    "Datacenter": "Hyperv-01",
    "StateOrDistrict": "WA"
  },
  "totalCoreCount": null,
  "totalNodeCount": null,
  "type": "microsoft.kubernetes/connectedclusters"
}
k3s-admin@k3s-1:~$
```

### Step 9: Run script

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-run-script.jpg)

### Step 10: Verification

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-validation.jpg)

### Step 10: 

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-arc-cluster.jpg)



### Troubleshooting





