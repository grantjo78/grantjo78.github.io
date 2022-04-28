---
layout: post
title: "Nested Virtualization on Azure - Part 3: Networking"
categories: [Hyper-V]
---

Since my last post in this series, I've deploy a virtual machine (VM1) onto the Hyper-V host.

Looking at VM1's virtual switch allocation, I've allocated the guest to the virtual switch that was created during the enabling of the Hyper-V role (Microsoft Hyper-V Network Adapter - Virtual Switch).

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

Looking at the virtual switch configuration for the *"Microsoft Hyper-V Network Adapter - Virtual Switch"*, you can see that irt is configured as an external virtual switch *(Connects to a wired, physical network by binding to a physical network adapter)*. This won't allow for guest traffic egress in Azure.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-vSwitch-Original.jpg)

If we look at the guest's IP configuration, you can see that it does not have a valid IP address or default gateway.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-Guest-IP.jpg)

In order to allow the guest virtual machine to route traffic externally, a NAT Virtual Network needs to be created.


## How to create a NAT Virtual Network


```
Get-VMSwitch
```

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-OriginalSwitch.jpg)


````
New-VMSwitch -SwitchName "SwitchName" -SwitchType Internal

Example:

New-VMSwitch -SwitchName "Azure" -SwitchType Internal
```

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-AzureSwitch.jpg)


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