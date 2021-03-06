---
layout: post
title: "Azure Arc-enabled Servers - Part 1: Onboarding (Single Server)"
categories: [Arc for Servers]
---

In this series I'm going to be exploring Azure Arc-enabled servers. 

[Part 1: Onboarding (Single Server)]({% post_url 2022-04-29-arc-servers-onboarding%})

This article will cover: 
- [What is Azure Arc-enabled Servers?](#what-is-azure-arc-enabled-servers)
- [The Environment](#the-environment)
- [Arc-enabled Servers - Azure Configuration](#arc-enabled-servers---azure-configuration)
- [Arc-enabled Servers - Agent Deployment](#arc-enabled-servers---agent-deployment)

## What is Azure Arc-enabled Servers?

[Azure Arc-enabled servers lets you manage Windows and Linux physical servers and virtual machines hosted outside of Azure, on your corporate network, or other cloud provider. This management experience is designed to be consistent with how you manage native Azure virtual machines, using standard Azure constructs such as Azure Policy and applying tags.](https://docs.microsoft.com/en-us/azure/azure-arc/servers/overview)

[When a hybrid machine is connected to Azure, it becomes a connected machine and is treated as a resource in Azure. Each connected machine has a Resource ID enabling the machine to be included in a resource group.](https://docs.microsoft.com/en-us/azure/azure-arc/servers/overview)

## The Environment

### Hyper-V Host

To simulate a server that is outside of Azure, I'm going to leverage nested virtualisation within an Azure virtual machine by enabling the Hyper-V role. If you would like to know how I did this, you can review my previous post [Nested Virtualization on Azure]({% post_url 2022-04-27-hyperv-vm-deployment%}).

![](/docs/assets/images/2022-04-29-arc-servers/arc-hyperv-host.jpg)

### Guest Virtual Machine

Within the Hyper-V host, I've deployed a single virtual machine called VM1. VM1 is running Windows Server 2022 and is attached to a NAT Gateway enabled virtual switch.

[](/docs/assets/images/2022-04-29-arc-servers/arc-hyperv-guest-vm1.jpg)

## Arc-enabled Servers - Azure Configuration

### Step 1: Azure Arc Service

From the Azure Portal select the **Azure Arc** service.

![](/docs/assets/images/2022-04-29-arc-servers/arc-service.jpg)

### Step 2: Overview

Under the **Infrastructure** section, select **Servers**.

![](/docs/assets/images/2022-04-29-arc-servers/arc-overview.jpg)

### Step 2: Servers

Select **Add**.

![](/docs/assets/images/2022-04-29-arc-servers/arc-servers-add.jpg)

### Step 3: Onboarding Method

Under **Add a single server** select **Generate script**.

![](/docs/assets/images/2022-04-29-arc-servers/arc-servers-single-script.jpg)

### Step 4: Prerequisites

Select **Next**.

![](/docs/assets/images/arc-servers/onboarding/arc-servers-single-prerequisites.jpg)

### Step 5: Resource Details

Populate the fields and select **Next**.

- **Subscription**: The subscription where the Arc resources will be managed from.
- **Resource group**: The resource group where the Arc resources will be managed from.
- **Region**: The region where the Arc resources metadata will be stored (e.g. Australia East). 
- **Operating system**: The operating system of the server.
- **Connectivity method**: How the Azure Connected Machine agent should connect to the internet.

![](/docs/assets/images/arc-servers/onboarding/arc-servers-single-resourcedetails.jpg)

### Step 6: Tags

Populate the fields with data that will be useful and select **Next**

![](/docs/assets/images/arc-servers/onboarding/arc-servers-single-tags.jpg)

### Step 7:

Copy or download the script. This will be used to deploy and configure the Arc agent on the target server.

![](/docs/assets/images/arc-servers/onboarding/arc-servers-single-downloadscript.jpg)

## Arc-enabled Servers - Agent Deployment

In this section I will be deploying the Arc agent onto the target server.

### Step 8: Agent Deployment

On the server where the Arc agent is to be install on, open an Administrator PowerShell session and run the downloaded script. 

```
Example: 

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12

# Download the installation package
Invoke-WebRequest -Uri "https://aka.ms/azcmagent-windows" -TimeoutSec 30 -OutFile "$env:TEMP\install_windows_azcmagent.ps1"

# Install the hybrid agent
& "$env:TEMP\install_windows_azcmagent.ps1"
if($LASTEXITCODE -ne 0) {
    throw "Failed to install the hybrid agent"
}

# Run connect command
& "$env:ProgramW6432\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "xx-xxx-xxxxxx-xxxxx" --tenant-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --location "australiaeast" --subscription-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --cloud "AzureCloud" --tags "Datacenter=Hyperv-01,City=Perth,StateOrDistrict=WA,CountryOrRegion=Australia" --correlation-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

if($LastExitCode -eq 0){Write-Host -ForegroundColor yellow "To view your onboarded server(s), navigate to https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines"}
```

The below request will appear, requiring authentication to the environment so that the server can be onboared onto Azure. 

```
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code xxxxxxxx authenticate.
```

Once authication has been completed successfully, the installation will complete with the below message.

```
time="xxxx-xx-xxxxx:xx:xx-xx:xx" level=info msg="Successfully Onboarded Resource to Azure" VM Id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
```

### Step 9: Azure Portal

Switching back to the Azure Portal, the server that had the Arc agent installed onto should now appear within the Azure Arc Servers blade.

![](/docs/assets/images/arc-servers/onboarding/arc-vm-onboarded.jpg)

