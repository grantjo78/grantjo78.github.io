---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 3: Monitoring"
categories: [Arc for Kubernetes]
---
Out of the box, the monitoring of an Arc-enabled Kubernetes environment is limited. To get rich monitoring of the environment [Container Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview) needs to be configured, which I will be doing today. 

This article will cover: 
1. [The Environment](#the-environment)
2. [Prerequisites](#prerequisites)
3. [Monitoring - Insights ](#monitoring---insights)

## The Environment

Before I get started, I wanted to describe the environment that I will be working with.

### The Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-05-0-arc-k8s-onboarding/arc-hyperv-host.jpg)

### Virtual Machines

Within the Hyper-V host, I've deployed two virtual machines:
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

## Prerequisites 

### Log Analytics Workspace

Monitoring an Arc-enabled Kubernetes environment requires a Log Analytics Workspace. If one is not available, create one using these [step](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/quick-create-workspace)

## Monitoring - Insights 

Container insights is a feature to monitor the performance of container workloads deployed to Arc-enabled Kubernetes environments. Insights provides performance visibility by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Unfortunately this is not enabled by default.

### Step 1: Overview 

Under **Monitoring**, select **Insights**.

### Step 2: Insights

Insights needs to be configured. Select **Configure azure monitor**.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-configuration.jpg)

### Step 3: Configure Azure Monitor

Select the **Log Analytics Workspace** that was created as part of the [Prerequisites](#prerequisites) and select **Configure**.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-law.jpg)

### Step 4: Onboarding

Insights will now be configured for the environment.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-onboarding.jpg)

Once it has completed being onboarded, performance information on the environment will now be visible. 

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-insights-reporting.jpg)

