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
& "$env:ProgramW6432\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "rg-arc-servers-hyperv" --tenant-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --location "australiaeast" --subscription-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --cloud "AzureCloud" --tags "Datacenter=Hyperv-01,City=Perth,StateOrDistrict=WA,CountryOrRegion=Australia" --correlation-id "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

if($LastExitCode -eq 0){Write-Host -ForegroundColor yellow "To view your onboarded server(s), navigate to https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines"}
```



```

```