---
layout: post
title: "Mounting a Blob Container via NFS - Part 2: Creating a NFS enabled Storage Account"
categories: [Storage Accounts, NFS]
---

In this artcile I'll be creating a NFS enabled Storage Account. 
- [Create a NFS Enabled Storage Account](#create-nfs-enabled-storage-account)
- *Create a Container - Coming Soon*
- [What's Next](#whats-next)

*At the time of writing this artcile, [NFS can't be enabled on existing storage accounts](https://docs.microsoft.com/en-us/azure/storage/blobs/network-file-system-protocol-known-issues#nfs-30-support)*

## Create a NFS Enabled Storage Account

I'm going to walk through the steps to create a NFS enabled Storage Account via the Azure Portal:
- [Step 1: Azure Portal](#step-1-azure-portal)
- [Step 2: Create Storage Account](#step-2-create-storage-account)
- [Step 3: Configure Basic Settings](#step-3-configure-basic-settings)
- [Step 4: Configure Advanced Settings](#step-4-configure-advanced-settings)
- [Step 5: Configure Network Settings](#step-5-configure-network-settings)
- [Step 6: Configure Encryption Settings](#step-6-configure-data-protection-settings)
- [Step 7: Configure Encryption Settings](#step-7-configure-encryption-settings)
- [Step 8: Configure Tags Settingst](#step-8-configure-tags-settings)
- [Step 9: Create Storage Account](#step-9-create-storage-account)
- [Step 10: Monitor Deployment](#step-10-monitor-deployment)
- [Step 11: Storage Account Overview](#step-11-storage-account-overview)

### Step 1: Azure Portal

From the Azure Portal home screen screen select **Create a resource**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-home.jpg)

### Step 2: Create Storage Account

Select **Create** under Storage account.

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-createresource.jpg)

### Step 3: Configure Basic Settings

Populate the fields and select **Next: Advanced >**.

- **Subscription**: The subscription where the storage account will be deployed into.
- **Resource group**: The resource group where the storage account will be deployed into.
- **Storage account name**: The name that will be given to the storage account.
- **Region**: The region where the storage account will be deployed into (e.g. Australia East). 
- **Performance**: The [performance tier](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-overview#types-of-storage-accounts) for the storage account.
- **Redundancy**: The level of [redundancy](https://docs.microsoft.com/en-us/azure/storage/common/storage-redundancy) required for the data.

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-basics.jpg)

### Step 4: Configure Advanced Settings

To enable NFS on a storage account first select **Enable hierarchial namespace** and then **Enable network file system v3***.

Select **Next: Networking >** 

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-advanced.jpg)

### Step 5: Configure Network Settings

As I'm going to use a Service Endpoint to connect to the storage account, select **Enable public access from selected virtual network and IP addresses**.

Populate the following fields:

- **Virtual network subscription**: The subscription where the virtual network exists.
- **Virtual network**: The virtual network that contains the subnet that will have the enabled Service EnndPoint.
- **Subnet**: The subnet with the Service Endpoint enabled on.

Select **Next: Data Protection >**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-networking.jpg)

### Step 6: Configure Data Protection Settings

No changes need to be here at this point in time.

Select **Next: Encryption >**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-dataprotection.jpg)

### Step 7: Configure Encryption Settings

No changes need to be here at this point in time.

Select **Next: Tags >**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-encryption.jpg)

### Step 8: Configure Tags Settings
No changes need to be here at this point in time.

Select **Next: Review + Create >**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-tags.jpg)

### Step 9: Create Storage Account

Select **Create**

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-create.jpg)

### Step 10: Monitor Deployment

Once the deployment has completed successfully select **Go to resource**.

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-deployment.jpg)

### Step 11: Storage Account Overview

You can now view the storage account that has been created.

![](/docs/assets/images/2022-05-20-mountnfs-createstorageaccount/mountnfs-storageaccount-overview.jpg)

## What's Next

Now that we have a NFS enabled storage account, the next step is to create a container.