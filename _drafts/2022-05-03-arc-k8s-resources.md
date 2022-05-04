---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 2: Kubernetes Resources"
categories: [Arc for Kubernetes]
---
This article is going to cover exploring Kubernetes resources for an onboarded Arc-enabled cluster.

This article will cover: 
1. [The Environment](#the-environment)
2. [Service Account Tokens](#service-account-tokens)

## The Environment

Before I get started, I wanted to describe the environment that I will be working with.

### The Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Virtual Machines

Within the Hyper-V host, I've deployed a two virtual machines:
- k3s-1
- k3s-2

![](/docs/assets/images/2022-05-03-arc-k8s-onboarding/arc-hyperv-guest-k3s.jpg)



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


## Service Account Tokens

```
kubectl create serviceaccount admin-user

Example:

k3s-admin@k3s-1:~$ kubectl create serviceaccount admin-user
serviceaccount/admin-user created
```

```
k3s-admin@k3s-1:~$ kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
clusterrolebinding.rbac.authorization.k8s.io/admin-user-binding created
```

```
k3s-admin@k3s-1:~$ SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
k3s-admin@k3s-1:~$
```

```
k3s-admin@k3s-1:~$ TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
```

```
eyJhbGciOiJSUzI1NiIsImtpZCI6Illoc0ZvaXVndHRYbmI0NWlOcmd0N0o0ckF6cEthSEpfeWMtSHZnMnRGYmsifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImFkbWluLXVzZXItdG9rZW4tNWc5OWoiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiYWRtaW4tdXNlciIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6ImRiNjczMjExLTI4MDQtNDA2Yy1hMzQ4LTg3MmRjYzMzM2YyOSIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmFkbWluLXVzZXIifQ.TAwJNN2hw3WZQ2tUJbqAa_SpeFgPHjjGiuSuw9lKWBQk3zjU8AFq-EOgcBa9WmqokBC00pXmB9e7vDGYtOb_xGb-L3F2Ib_rT5bmr_-TUeexdpk7zXvzHVU931Z1rR_C-zcYEiND0DctYdOcv2xSUk18xbvfAyaxao4DudvVkVhMDRuLcWIDksc6XH_hzjbleCAOYnXcDo0D3pYPDi7oAeI-zce5J-HqMeCk6lQE0N9a_bYjETwXBQvKaby4l4ZoKTnfeapil2WNU2KyvDTY822fDYY9Z8EOrtZ9WBuh9WYYJJVv8Kp7QLv2tAk5QmTL58m_s9xIVjSF7ggpeqM7cA

```