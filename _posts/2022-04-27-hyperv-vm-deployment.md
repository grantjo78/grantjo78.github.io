---
layout: post
title: "Nested Virtualization on Azure - Part 1: Virtual Machine Requirements"
categories: [Hyper-V]
---

This week I had a requirement to enable Hyper-V within a virtual machine on Azure. I've done this many times before but I've never documented the process and I always seem to forget a step. 

To start off with, I'm going to look at the following:
1. Nested Virtualisation Support
2. Creation of a Virtual Machine 

## Nested Virtualisation Support
Before enabling Hyper-V on a virtual machine, you need to ensure that it supports **nested virtualisation** (not all virtual machine series support nested virtualisation).

To determine if the virtual machine series supports nested virtualisation, you can go to the [Azure Virtual Machine documentation](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-general) and look at the capability section for the series. Look for the **Nested Virtualization** capability and confirm that it says **Supported**

Below is an example of the [B-series virtual machines](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-b-series-burstable) which **do not** support nested virtualisation. 

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-NotSupported.jpg)

Below is an example of the [Dv5-series virtual machines](https://docs.microsoft.com/en-us/azure/virtual-machines/dv5-dsv5-series) which **do** support nested virtualisation.

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-Supported.jpg)

## Creation of a Virtual Machine

I'll quickly running through the deployment of a virtual machine and highlight areas that you will need to consider.

### Basics

Within the **Basics** blade of the deployment the following areas require consideration:

1. **Image**. Depending on the virtual machine's processor, the below Operating System requirements should be considered:
- If your Hyper-V host is running an Intel processor with VT-x and EPT, the Operating System for the host must be Windows Servers 2016 or greater
- If your Hyper-V host is running an AMD EPYC/Ruzen processor or later, the Operating System for the host must be Windows Server 2022 or greater
2. **Size**. As discussed previously, select a virtual machine series that supports nested virtualisation.

Once you have complete populating the fields, click on **Next: Disks >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Basics.jpg)

### Disks

Within the **Disks** blade I've added an additional disk where I will be storing the Hyper-V guest disks.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Disk.jpg)

Once you have complete populating the fields, click on **Next: Networking >** to continue.

## Networking

Within the **Networking** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Networking.jpg)

Once you have complete populating the fields, click on **Next: Management >** to continue.

### Management

Within the **Management** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Management.jpg)

Once you have complete populating the fields, click on **Next: Advanced >** to continue.

### Advanced

Within the **Advanced** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Advanced.jpg)

Once you have complete populating the fields, click on **Next: Review + create >** to continue.

### Tags

Within the **Tags** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Tags.jpg)

Once you have complete populating the fields, click on **Next: Disks >** to continue.

### Review + Create

Click on **Create** to begin the virtual machine deployment.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Review.jpg)

### Resources

Now that the VM deployment has completed, click on **Go to resource** to go to your newly created virtual machine.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Completed.jpg)

Now that I've deployed a virtual machine that supports nested virtualisation, let's move onto Enabling the Hyper-V Role.