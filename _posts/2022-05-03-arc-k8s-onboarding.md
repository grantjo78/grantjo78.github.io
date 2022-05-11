---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 1: Onboarding"
categories: [Arc for Kubernetes]
---

In this series I'm going to be exploring Azure Arc-enabled Kubernetes.

[Part 1: Onboarding]({% post_url 2022-05-03-arc-k8s-onboarding%})

[Part 2: Cluster Resources]({% post_url 2022-05-04-arc-k8s-resources%})

[Part 3: Monitoring]({% post_url 2022-05-06-arc-k8s-monitoring%})

[Part 4: GitOps - Coming Soon]

This article will cover: 
1. [What is Azure Arc-enabled Kubernetes?](#what-is-azure-arc-enabled-kubernetes)
2. [The Environment](#the-environment)
3. [Prerequisites](#prerequisites)
4. [Arc-enabled Kubernetes - Azure Configuration](#arc-enabled-kubernetes---azure-configuration)
5. [Arc-enabled Kubernetes - Agent Deployment](#arc-enabled-kubernetes---agent-deployment)

## What is Azure Arc-enabled Kubernetes?

[Azure Arc-enabled Kubernetes allows you to attach and configure Kubernetes clusters running anywhere. You can connect your clusters running on other public cloud providers (such as GCP or AWS) or clusters running on your on-premise data center (such as VMware vSphere or Azure Stack HCI) to Azure Arc.](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/overview)

## The Environment

The environment that I will be working with is described below:
1. [Hyper-V Host](#hyper-v-host)
2. [Hyper-V Guests](#hyper-v-guests)
3. [Kubernetes Environment](#kubernetes-environment)
4. [Sample Voting Application](#sample-voting-application)

### Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Hyper-V Guests

Within the Hyper-V host, I've deployed two virtual machines:
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
### Kubernetes Environment

For the Kubernetes environment, I'm utilising [K3s](https://k3s.io/) because it's very quick to setup and lightweight. If you're interested in how to deploy K3s and configure a cluster you can follow the [quickstart guide](https://rancher.com/docs/k3s/latest/en/quick-start/).

My cluster consists of 2 nodes, a master (k3s-1) and a worker (k3s-2).

```
k3s-admin@k3s-1:~$ kubectl get nodes

NAME    STATUS   ROLES                  AGE   VERSION
k3s-1   Ready    control-plane,master   22h   v1.22.7+k3s1
k3s-2   Ready    <none>                 22h   v1.22.7+k3s1
```

### Sample Voting Application

I've deployed the below sample appliaction into the **Azure** namespace.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-votingapp.jpg)

Below is the [sample yaml code](https://docs.microsoft.com/en-us/azure/aks/learn/quick-kubernetes-deploy-cli#deploy-the-application) for the application (with some slight modifications for K3s)

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
        env:
        - name: ALLOW_EMPTY_PASSWORD
          value: "yes"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 8080
  - targetPort: 80
  selector:
    app: azure-vote-front
```

The below illustrates the sample application resources running within the K3s environment.

```
k3s-admin@k3s-1:~$ sudo kubectl get all  -n voting -o wide
NAME                                    READY   STATUS    RESTARTS   AGE    IP           NODE    NOMINATED NODE   READINESS GATES
pod/azure-vote-front-5f4d7db9c8-tgx9l   1/1     Running   0          41m    10.42.1.6    k3s-2   <none>           <none>
pod/azure-vote-back-59cb7dc555-nxnws    1/1     Running   0          41m    10.42.1.7    k3s-2   <none>           <none>
pod/svclb-azure-vote-front-hx5kn        1/1     Running   0          40m    10.42.1.8    k3s-2   <none>           <none>
pod/svclb-azure-vote-front-hx8gp        1/1     Running   0          40m    10.42.0.35   k3s-1   <none>           <none>
pod/azure-vote-front-5f4d7db9c8-hpt5l   1/1     Running   0          2m7s   10.42.1.9    k3s-2   <none>           <none>
pod/azure-vote-back-59cb7dc555-554fm    1/1     Running   0          2m7s   10.42.0.36   k3s-1   <none>           <none>

NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP                 PORT(S)          AGE   SELECTOR
service/azure-vote-back    ClusterIP      10.43.160.59    <none>                      6379/TCP         41m   app=azure-vote-back
service/azure-vote-front   LoadBalancer   10.43.239.137   192.168.0.51,192.168.0.52   8080:32633/TCP   41m   app=azure-vote-front

NAME                                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE   CONTAINERS    IMAGES                      SELECTOR
daemonset.apps/svclb-azure-vote-front   2         2         2       2            2           <none>          41m   lb-tcp-8080   rancher/klipper-lb:v0.3.5   app=svclb-azure-vote-front

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS         IMAGES                                            SELECTOR
deployment.apps/azure-vote-front   2/2     2            2           41m   azure-vote-front   mcr.microsoft.com/azuredocs/azure-vote-front:v1   app=azure-vote-front
deployment.apps/azure-vote-back    2/2     2            2           41m   azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8         app=azure-vote-back

NAME                                          DESIRED   CURRENT   READY   AGE   CONTAINERS         IMAGES                                            SELECTOR
replicaset.apps/azure-vote-front-5f4d7db9c8   2         2         2       41m   azure-vote-front   mcr.microsoft.com/azuredocs/azure-vote-front:v1   app=azure-vote-front,pod-template-hash=5f4d7db9c8
replicaset.apps/azure-vote-back-59cb7dc555    2         2         2       41m   azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8         app=azure-vote-back,pod-template-hash=59cb7dc555

```

## Prerequisites

### Azure Resource Providers

The following resource providers need to be enabled within the subscription that Arc-enabled Kubernetes will be deployed to:
- Microsoft.Kubernetes
- Microsoft.KubernetesConfiguration
- Microsoft.ExtendedLocation

From Azure CLI execute the below commands to register the providers.

```
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ExtendedLocation
```

To confirm that the providers have been successfully registered, execute the below commands.

```
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
az provider show -n Microsoft.ExtendedLocation -o table
```

### Kubernetes Node

For the master node, the [Azure Cli](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-linux?pivots=apt) needs to be installed.


## Arc-enabled Kubernetes - Azure Configuration

To enable a Kubernetes cluster for Azure Arc, I'm going to walk through the steps via the Azure Portal.

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

### Step 8: Log into Azure

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

### Step 9: Set Azure subscription

Execute the command in the script under **Set Azure Subscription**. 

```
az account set --subscription <subscription ID>
```

```
Example:

k3s-admin@k3s-1:~$ az account set --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx
```

### Step 10: Create connected cluster

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
### Step 9: Run script

Switch back to the Azure Portal and select **Next: Verification >**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-run-script.jpg)

### Step 10: Verification

The Kubernetes cluster should show as successfully connected to Azure. Select **Go to the cluster**.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-validation.jpg)

### Step 11: Cluster overview
An overview of the cluster that was onboarded should be visible.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-arc-cluster.jpg)

### Step 12: Kubernetes clusters

Switching back to the **Azure Arc - Infrastructure** view, the new cluster is also visible from here.

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-k8s-onboard-newcluster.jpg)


