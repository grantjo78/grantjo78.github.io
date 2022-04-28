---
layout: post
title: "Creating a Kubernetes Cluster with Raspberry Pi Devices  - Part 1: The Shopping List"
categories: [Arc]
---

```
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12

# Download the installation package
Invoke-WebRequest -Uri "https://aka.ms/azcmagent-windows" -TimeoutSec 30 -OutFile "$env:TEMP\install_windows_azcmagent.ps1"

# Install the hybrid agent
& "$env:TEMP\install_windows_azcmagent.ps1"
if($LASTEXITCODE -ne 0) {
    throw "Failed to install the hybrid agent"
}

# Run connect command
& "$env:ProgramW6432\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "rg-arc-servers-hyperv" --tenant-id "72f988bf-86f1-41af-91ab-2d7cd011db47" --location "australiaeast" --subscription-id "ed26a14c-d56c-4b07-b182-dd6fd6f1dbcf" --cloud "AzureCloud" --tags "Datacenter=Hyperv-01,City=Perth,StateOrDistrict=WA,CountryOrRegion=Australia" --correlation-id "cfe9ef68-d5e5-4c37-b452-d351f3e25003"

if($LastExitCode -eq 0){Write-Host -ForegroundColor yellow "To view your onboarded server(s), navigate to https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines"}
```