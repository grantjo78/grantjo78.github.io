---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 2: Onboarding a Kubernetes Cluster"
categories: [Arc for Kubernetes]
---

In this post I'll be onboarding the Kubernetes Cluster from my demo environment into Azure Arc. This post will cover the following sections:
- [Arc-enabled Kubernetes - Azure Configuration](#arc-enabled-kubernetes---azure-configuration)
- [Arc-enabled Kubernetes - Agent Deployment](#arc-enabled-kubernetes---agent-deployment)
- [What's Next](#whats-next)

## Arc-enabled Kubernetes - Azure Configuration

To enable a Kubernetes cluster for Azure Arc, I'm going to walk through the steps via the Azure Portal:
- [Step 1: Azure Arc Service](#step-1-azure-arc-service)
- [Step 2: Azure Arc Overview](#step-2-azure-arc-overview)
- [Step 3: Kubernetes clusters](#step-3-kubernetes-clusters)
- [Step 4: Prerequisites](#step-4-prerequisites)
- [Step 5: Cluster details](#step-5-cluster-details)
- [Step 6: Tags](#step-6-tags)
- [Step 7: Run script](#step-7-run-script)
### Step 1: Azure Arc Service

From the Azure Portal select the **Azure Arc** service.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-service.jpg)

### Step 2: Azure Arc Overview

Under the **Infrastructure** section, select **Kubernetes clusters**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-overview.jpg)
### Step 3: Kubernetes clusters

Select **Add a Kubernetes cluster with Azure Arc**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-add-cluster.jpg)
### Step 4: Prerequisites

Select **Next: Cluster details > **

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-prerequisites.jpg)
### Step 5: Cluster details

Populate the below fields. 
- **Subscription**: The subscription where the Arc resources will be managed from.
- **Resource group**: The resource group where the Arc resources will be managed from.
- **Cluster name**: The name that you would like to give the cluster.
- **Region**: The region where the Arc resources metadata will be stored (e.g. Australia East). 
- **Outbound proxy**: Proxy configuration should Azure Cli and Kubernetes need to route via an outbound proxy.

 Select **Next: Tags**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-cluster-details.jpg)
### Step 6: Tags

Populate the fields with data that will be useful and select **Next: Run script**

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-tags.jpg)
### Step 7: Run script

Copy/download the script.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-arc-run-script-stop.jpg)
## Arc-enabled Kubernetes - Agent Deployment

In this section I'll be deploying the Arc agent onto the master node using the script that was copied/downloaded.

- [Step 1: Log into Azure](#step-1-log-into-azure)
- [Step 2: Set Azure subscription](#step-2-run-script)
- [Step 3: Create connected cluster](#step-3-create-connected-cluster)
- [Step 4: Run script](#step-4-run-script)
- [Step 5: Deployment Verification](#step-5-deployment-verification)
- [Step 6: Cluster overview](#step-6-cluster-overview)
- [Step 7: Kubernetes clusters](#step-7-kubernetes-clusters)

### Step 1: Log into Azure

SSH to the master node (e.g. k3s-1) and execute the command in the script under **Log into Azure***. 

```
az login
```

```
Example:

k3s-admin@k3s-1:~$ az login
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code xxxxxxxx to authenticate.
```

This will prompt you to perform a device login.
### Step 2: Set Azure subscription

Execute the command in the script under **Set Azure Subscription**. 

```
az account set --subscription <subscription ID>
```

```
Example:

k3s-admin@k3s-1:~$ az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx
```
### Step 3 Create connected cluster

To deploy and configure the Arc agent execute the command in the script under **Create connected cluster**.

```
az connectedk8s connect --name <cluster name> --resource-group <resource group name> --location <Azure region> --tags Datacenter=<datacenter name> City=<city> StateOrDistrict=<state> CountryOrRegion=<country>
```

```
Example:

az connectedk8s connect --name k3s-cluster-01 --resource-group rg-arc-kubernetes-hyperv --location australiaeast --tags Datacenter=Hyperv-01 City=Perth StateOrDistrict=WA CountryOrRegion=Australia --kube-config "/etc/rancher/k3s/k3s.yaml"
```

I've used an additional parameter that wasn't included in the script:
- **--kube-config**: Path to the kube config file. With K3s the kube config file is not stored with the user profile, which the agent deployment looks for.

```
Example:

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
```
### Step 4: Run script

Switch back to the Azure Portal and select **Next: Verification >**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-run-script.jpg)
### Step 5: Deployment Verification

The Kubernetes cluster should show as successfully connected to Azure. Select **Go to the cluster**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-validation.jpg)
### Step 6: Cluster overview

An overview of the cluster that was onboarded should be visible.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-arc-cluster.jpg)
### Step 7: Kubernetes clusters

Switching back to the **Azure Arc - Infrastructure** view, the new cluster is also visible from here.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-onboard-newcluster.jpg)

## What's Next

Now that have onboarded a Kubernetes cluster into Azure Arc we can move onto [Part 3: Viewing Cluster Resources]({% post_url 2022-05-04-arc-k8s-resources%}).
