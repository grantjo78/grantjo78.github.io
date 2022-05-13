---
layout: post
title: "Azure Arc-enabled Kubernetes - Part 4: Monitoring Cluster Performance"
categories: [Arc for Kubernetes]
---
In [Part 3: Viewing Cluster Resources]({% post_url 2022-05-04-arc-k8s-resources%}) I expolered viewing a clusters resources via Arc. In this post I'll dive into monitoring a clusters performance. Out of the box, the monitoring of an Arc-enabled Kubernetes environment is limited. To get the rich monitoring of the environment,[Container Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-overview) needs to be configured.
- [Prerequisites](#prerequisites)
- [Monitoring - Insights ](#monitoring---insights)
- [What's Next](#whats-next)

## Prerequisites 

### Log Analytics Workspace

Monitoring an Arc-enabled Kubernetes environment requires a Log Analytics Workspace. If one is not available, create one using these [step](https://docs.microsoft.com/en-us/azure/azure-monitor/logs/quick-create-workspace)

## Monitoring - Insights 

Container insights is a feature to monitor the performance of container workloads deployed to Arc-enabled Kubernetes environments. Insights provides performance visibility by collecting memory and processor metrics from controllers, nodes, and containers that are available in Kubernetes through the Metrics API. Unfortunately this is not enabled by default. I will walk through enabling Container insights with the following steps:
- [Step 1: Overview](#step-1-overview)
- [Step 2: Insights](#step-2-insights)
- [Step 3: Configure Azure Monitor](#step-3-configure-azure-monitor)
- [Step 4: Onboarding](#step-4-onboarding)

### Step 1: Overview

Under **Monitoring**, select **Insights**.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-overview.jpg)

### Step 2: Insights

Insights needs to be configured. Select **Configure azure monitor**.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-configuration.jpg)

### Step 3: Configure Azure Monitor

Select the **Log Analytics Workspace** that was created as part of the [Prerequisites](#prerequisites) and select **Configure**.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-law.jpg)

### Step 4: Onboarding

Insights will now be configured for the environment.

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-onboarding.jpg)

Once the onboarding has completed, performance information on the environment will be visible. 

![](/docs/assets/images/2022-05-06-arc-k8s-monitoring/arc-k8s-monitoring-insights-reporting.jpg)

## What's Next