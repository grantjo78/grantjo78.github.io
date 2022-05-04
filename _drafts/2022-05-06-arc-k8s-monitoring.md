---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 2: Monitoring"
categories: [Arc for Kubernetes]
---
This article will explore Kubernetes resources for an Arc-enabled cluster.

This article will cover: 
1. [The Environment](#the-environment)
2. [Service Account Tokens](#service-account-tokens)

## The Environment

Before I get started, I wanted to describe the environment that I will be working with.

### The Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-0-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Virtual Machines

Within the Hyper-V host, I've deployed a two virtual machines:
- k3s-1
- k3s-2

![](/docs/assets/images/2022-05-04-arc-k8s-onboarding/arc-hyperv-guest-k3s.jpg)


### Kubernetes Environment

For the Kubernetes environment, I'm utilising [K3s](https://k3s.io/) because it's very quick to setup and lightweight. If you're interested in how to deploy K3s and configure a cluster you can follow the [quickstart guide](https://rancher.com/docs/k3s/latest/en/quick-start/).

My cluster consists of 2 nodes, a master (k3s-1) and a worker (k3s-2).

```
k3s-admin@k3s-1:~$ kubectl get nodes

NAME    STATUS   ROLES                  AGE   VERSION
k3s-1   Ready    control-plane,master   22h   v1.22.7+k3s1
k3s-2   Ready    <none>                 22h   v1.22.7+k3s1
```

### Arc-enabled Cluster

In [Part 1]({% post_url 2022-05-03-arc-k8s-onboarding%}) of this series I covered the onboarding of a Kubernetes cluster into Arc. This same cluster will be used in this article.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-cluster-overview.jpg)

## Service Account Tokens

When you select one of the resources under **Kubernetes resources** (e.g. Namespaces), it will prompt you to enter a **Service account bearer token**.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-namespace-signin.jpg)

The steps below will walk through the creatation of a token for Arc to utilise, providing visibility to the clusters resources.

### Step 1: Create Service Account

In order to obtain a bear token a [Service Account](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#service-account-tokens) needs to be created.

Execute the below command to create a service account called **admin-user**.

```
kubectl create serviceaccount admin-user

Example:

k3s-admin@k3s-1:~$ kubectl create serviceaccount admin-user
serviceaccount/admin-user created
```

### Step 2: Create Cluster Role Binding

Once the admin-user service account has been created it needs to be bound to a [role](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding).

Execute the below command to bind the admin-user service account to the **cluster-admin** role.

```
kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user

Example:

k3s-admin@k3s-1:~$ kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
clusterrolebinding.rbac.authorization.k8s.io/admin-user-binding created
```

### Step 3: Extract Secret Name

In this step I'm going to extract the Secret Name from the service account using the below command.

```
SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
echo $SECRET_NAME

Example:
k3s-admin@k3s-1:~$ SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')

k3s-admin@k3s-1:~$ echo $SECRET_NAME
k3s-admin@k3s-1:~$ admin-user-token-5g99j

```

### Step 4: Obtain Token

To obtain the token to be used, execute the below command.

```
TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
echo $TOKEN

Example:

k3s-admin@k3s-1:~$ TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
k3s-admin@k3s-1:~$ echo $TOKEN
```
![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-token.jpg)

### Step 5: Paste Token

Switch back to the Azure Portal and paste the token into the **Sign in** box.

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-token-paste.jpg)

Select **Sign in**.

### Step 6: Resources

Once the sign in has completed, the resources from the cluster will be visible.

#### Namespaces

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-namespace-resources.jpg)

#### Workloads

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-workloads.jpg)

#### Service and ingress

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-services.jpg)

#### Storage

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-storage.jpg)

#### Configuration

![](/docs/assets/images/2022-05-04-arc-k8s-resources/arc-k8s-resources-configuration.jpg)