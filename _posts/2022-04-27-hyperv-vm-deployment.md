---
layout: post
title: "Nested Virtualization on Azure - Part 1: Deploying the Virtual Machine"
categories: [Hyper-V]
---

This week I had a requirement to enable Hyper-V within a virtual machine in Azure. I've done this many times before but I've never documented the process and I always seem to forget a step. To start off with, we are going to look at the following:
1. Selecting a Virtual Machine Series that Support Nested Virtualisation
2. Creation of a Virtual Machine 

## Selecting a Virtual Machine Series that Support Nested Virtualisation

Before enabling Hyper-V on a virtual machine, you need to ensure that it supports **nested virtualisation** (not all virtual machine series support nested virtualisation).

To determine if the virtual machine series supports nested virtualisation, you can go to the [Azure Virtual Machine documentation](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-general) and look at the capability section for the series. Look for the **Nested Virtualization** capability and confirm that it says **Supported**

Below is an example of the [B-series VM's](https://docs.microsoft.com/en-us/azure/virtual-machines/sizes-b-series-burstable) which **does not** support nested virtualisation. 

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-NotSupported.jpg)

Below is an example of the [Dv5-series VM's](https://docs.microsoft.com/en-us/azure/virtual-machines/dv5-dsv5-series) which **does** support nested virtualisation.

![Nested Virtualisation Not Supported](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-SKU-NestedV-Supported.jpg)


## Creation of a Virtual Machine

I'll quickly running through the deployment of a virtual machine and highlight areas that you will need to consider.

### Basics

Within the **Basic** blade of the deployment the following areas require consideration:

1. **Image**. Depending on the virtual machine's processor, the below Operating System requirements should be considered:
- If your Hyper-V host is running an Intel processor with VT-x and EPT, the Operating System for the host must be Windows Servers 2016 or greater
- If your Hyper-V host is running an AMD EPYC/Ruzen processor or later, the Operating System for the host must be Windows Server 2022 or greater
2. **Size**. As discussed previously, select a virtual machine series that supports nested virtualisation.

Once you have complete populating the fields, click on **Next: Disks >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Basics.jpg)

### Disk

Within the **Disk** blade I've added an additional disk where I will be storing the Hyper-V guest disks.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Disk.jpg)

Once you have complete populating the fields, click on **Next: Networking >** to continue.

## Networking

Within the **Networking** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

Once you have complete populating the fields, click on **Next: Management >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Networking.jpg)

### Management

Within the **Management** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

Once you have complete populating the fields, click on **Next: Advanced >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Management.jpg)

### Advanced

Within the **Advanced** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

Once you have complete populating the fields, click on **Next: Review + create >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Advanced.jpg)

### Tags

Within the **Tags** blade I haven't identified anything that would impact the enabling of Hyper-V within the guest.

Once you have complete populating the fields, click on **Next: Disks >** to continue.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Tags.jpg)

### Review + Create

Click on **Create** to begin the virtual machine deployment.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Review.jpg)

### Resources

Now that the VM deployment has completed, click on **Go to resource** to go to your newly created virtual machine.

![](/docs/assets/images/2022-04-27-hyperv-vm-deployment/VM-Create-Completed.jpg)