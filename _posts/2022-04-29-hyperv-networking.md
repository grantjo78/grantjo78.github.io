---
layout: post
title: "Nested Virtualization on Azure - Part 3: Networking - NAT"
categories: [Hyper-V]
---

Since my last post in this series, I've deployed a virtual machine (VM1) onto the Hyper-V host.

VM1's has been allocated to the virtual switch that was created during the enabling of the Hyper-V role (Microsoft Hyper-V Network Adapter - Virtual Switch).

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

 "Microsoft Hyper-V Network Adapter - Virtual Switch" configuration indicates that it has been configured as an [external virtual switch](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/plan/plan-hyper-v-networking-in-windows-server#switch-and-network-adapter-choices). This configuration does not allow for my guest vm to egress traffic outside of the Hyper-V host in Azure.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-vSwitch-Original.jpg)

Looking at the guest virtual machine's IP configuration, you can see that it does not received an IP address via DHCP and there is no default gateway.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-Guest-IP.jpg)

If I try to access a website on the internet (e.g. www.microsoft.com) it does not succeed.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-NoInternetAccess.jpg)

In order to allow the guest virtual machine to route traffic externally we need to create a **NAT Virtual Network**. 
