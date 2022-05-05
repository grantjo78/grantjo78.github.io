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

## Monitoring - Insights 

## Step 1: 

## Step 2:

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-configuration.jpg)

## Step 3: 

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-law.jpg)

## Step 4:

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-onboarding.jpg)

## Step 5: 

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-insights-reporting.jpg)

