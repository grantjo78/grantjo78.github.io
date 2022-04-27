---
layout: post
title: "Nested Virtualization on Azure - Part 1: Deploying the Virtual Machine"
categories: [Hyper-V]
---

This week I had a requirement to enable Hyper-V within a virtual machine in Azure. I've done this many time before but I've never documented the process and I always seem to forget a step.

Before enabling Hyper-V on a virtual machine you to ensure that it supports Nested Virtualisation (not all virtual machine series support nested virtualisation).

To determine if the virtual machine series supports nested virtualisation, you can got to the [Azure Virtual Machine documentation](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-general) and look at the capability section for the series. Look for the *Nested Virtualiztion* capability can check that it says *Supported*

Below is an example of the B-series VM's which *does not* support nested virtualisation

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-NotSupported.jpg)

Below is an example of the Dv5-series VM's which *does* support nested virtualisation.

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-Supported.jpg)

I'll quickly running through the deployment of a virtual machine in Azure and point out areas that you will need to pay addition to.

## Basics

Within the *Basic* blade of the deployment the following areas require consideration:

*Image*

Depending on the virtual machines processor, the below Operating System requirements should be considered:
    1. If your Hyper-V host is running an Intel processor with VT-x and EPT, the Operating System for the host must be Windows Servers 2016 or greater
    2. If your Hyper-V host is running an AMD EPYC/Ruzen processor or later, the Operating System for the host must be Windows Server 2022 or greater

*Size*

As discussed previously, select a virtual machine series that supports nested virtualisation.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Basics.jpg)

## Disk

Within the *Disk* blade of the deployment, I've added an additonal disk where I will be storing the Hyper-V guest disks.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Disk.jpg)

## Networking

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Networking.jpg)

## Management

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Management.jpg)

## Advanced

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Advanced.jpg)

## Tags

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Tags.jpg)

## Review + Create

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Review.jpg)

## Resources

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Completed.jpg)