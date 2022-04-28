---
layout: post
title: "Nested Virtualization on Azure - Part 3: Networking"
categories: [Hyper-V]
---

Since my last post in this series, I've deploy a virtual machine (VM1) onto the Hyper-V host.

VM1's has been allocated to the virtual switch that was created during the enabling of the Hyper-V role (Microsoft Hyper-V Network Adapter - Virtual Switch).

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

 "Microsoft Hyper-V Network Adapter - Virtual Switch" configuration indicates that it has been configured as an [external virtual switch](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/plan/plan-hyper-v-networking-in-windows-server#switch-and-network-adapter-choices). This won't allow for guest egress traffic in Azure.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-vSwitch-Original.jpg)

If we look at the guest's IP configuration, you can see that it does not have a valid IP address or default gateway.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-Guest-IP.jpg)

In order to allow the guest virtual machine to route traffic externally, a **NAT Virtual Network** needs to be created.


## How to create a NAT Virtual Network

From the Hyper-V host, open an Administrator PowerShell session and run the below command.

```
Get-VMSwitch
```

The output will show the virtual switches that have been created.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-OriginalSwitch.jpg)


To create the NAT Virtual Switch, follow the below commands:

### Step 1: Create a new Internal Virtual Switch

Run the below command to create a new Internal virtual switch.

````
New-VMSwitch -SwitchName "SwitchName" -SwitchType Internal

Example:

New-VMSwitch -SwitchName "Azure" -SwitchType Internal
```

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-AzureSwitch.jpg)

### Step 2: Identity New Switches ifIndex Number


```
Get-NetAdapter
```

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-ifIndex.jpg)

```
New-NetIPAddress -IPAddress <NAT Gateway IP> -PrefixLength <NAT Subnet Prefix Length> -InterfaceIndex <ifIndex>

Example:

New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 18
```
![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-NATIP.jpg)

```
New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>

Example:

New-NetNat -Name AzureNAT -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-NewNat.jpg)

## Guest VM Changes

### Virtual Switch

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Azure.jpg)

### Guest Machine IP Address

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-IP.jpg)