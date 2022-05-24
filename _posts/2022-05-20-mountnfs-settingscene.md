---
layout: post
title: "Mounting a Blob Container via NFS - Part 1: Setting the Scene"
categories: [Storage Accounts, NFS]
---
In this series I'll be exploring how to mount a Storage Account Blob container as an NFS volume within a Linux virtual machine.

[Part 1: Setting the Scene]({% post_url 2022-05-20-mountnfs-settingscene%})

*Part 2: Creating a NFS enabled Storage Account - Coming Soon*

*Part 3: Creating a NFS enabled Container - Coming Soon* 

*Part 4: Mounting a Container as a NFS Volume - Coming Soon*

## What is Network File System (NFS) 3.0 protocol support for Azure Blob Storage?

The addition of NFS 3.0 protocol support for Azure Blob Storage allows for the mounting of Blob containers as a volume onto a Linux virtual machine.

["It's always been a challenge to run large-scale legacy workloads, such as High Performance Computing (HPC) in the cloud. One reason is that applications often use traditional file protocols such as NFS or Server Message Block (SMB) to access data. Also, native cloud storage services focused on object storage that have a flat namespace and extensive metadata instead of file systems that provide a hierarchical namespace and efficient metadata operations."](https://docs.microsoft.com/en-us/azure/storage/blobs/network-file-system-protocol-support)

Before I move on to illustrating how to mount a Blob containers as a NFS volume, there are some security concerns that need to be considered. [Currently, the only way to secure the data in your storage account is by using a virtual network and other network security settings. Any other tools used to secure data, including account key authorization, Azure Active Directory (Azure AD) security, and access control lists (ACLs), are not yet supported in accounts that have the NFS 3.0 protocol support enabled on them.](https://docs.microsoft.com/en-us/azure/storage/blobs/network-file-system-protocol-support-how-to#step-2-configure-network-security)

To secure the Storage Account for NFS access, review the [Network Security Recommendations for Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/security-recommendations#networking).

## The Demo Environment

The environment that I will be working with is pretty simple.

![](/docs/assets/images/2022-05-20-mountnfs-settingscene/mountnfs-environment.jpeg)

### Network

The network will consist of:
- A single virtual network with a subnet (ApplicationSubnet) for my virtual machine

![](/docs/assets/images/2022-05-20-mountnfs-settingscene/mountnfs-environment-vnet.jpg)

- Service Endpoints enabled for Microsoft Storage on the ApplicationSubnet

![](/docs/assets/images/2022-05-20-mountnfs-settingscene/mountnfs-environment-applicationsubnet.jpg)

### Virtual Machine

- A single Linux virtual machine deployed into the ApplicationSubnet. This virtual machine will be used to mount a Blob container via the NFS 3.0 protocol

 ![](/docs/assets/images/2022-05-20-mountnfs-settingscene/mountnfs-environment-azurevm.jpg)

### Storage Account

- A single NFS enabled Storage Account (I'll be creating this in the next post). This Storage Account will be used by the virtual machine to mount the NFS container.




