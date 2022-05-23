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

The addition of NFS 3.0 protocol support for Azure Blob Storage allows the mounting of a Storage Account Blob container as a volume onto a Linux Virtual Machine.

["It's always been a challenge to run large-scale legacy workloads, such as High Performance Computing (HPC) in the cloud. One reason is that applications often use traditional file protocols such as NFS or Server Message Block (SMB) to access data. Also, native cloud storage services focused on object storage that have a flat namespace and extensive metadata instead of file systems that provide a hierarchical namespace and efficient metadata operations."](https://docs.microsoft.com/en-us/azure/storage/blobs/network-file-system-protocol-support)

Before I move on to illustrating how to mount a Blob containers as a NFS volume, there are some security concerns that need to be considered:
["Currently, the only way to secure the data in your storage account is by using a virtual network and other network security settings. Any other tools used to secure data, including account key authorization, Azure Active Directory (Azure AD) security, and access control lists (ACLs), are not yet supported in accounts that have the NFS 3.0 protocol support enabled on them"](https://docs.microsoft.com/en-us/azure/storage/blobs/network-file-system-protocol-support-how-to#step-2-configure-network-security)

To secure the Storage Account for NFS access, review the [Network Security Recommendations for Blob Storage](https://docs.microsoft.com/en-us/azure/storage/blobs/security-recommendations#networking).

## The Demo Environment

The environment that I will be working with will be pretty simple:

![](/docs/assets/images/2022-05-20-storageaccount-nfs/mountnfs-environment.jpeg)

A single Linux virtual machine has been deployed into the ApplicationSubnet.

This virtual machine will be used to mount a Storage Account Blob container via the NFS 3.0 protocol.
