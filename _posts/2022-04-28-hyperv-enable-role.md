---
layout: post
title: "Nested Virtualization on Azure - Part 2: Enabling the Hyper-V Role"
categories: [Hyper-V]
---

Now that we have a virtual machine deployed that supports nested virtualisation, I'm going to walk you through enabling the Hyper-V role on a standalone instance of Windows Server 2022.

## Enabling Hyper-V role

### Step 1: Server Manager 

From the **Server Manager** select the **Add roles and features option**.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Add.jpg)

### Step 2: Before you begin

Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-BeforeBegin.jpg)

### Step 3: Installation type

Select **Role-based or feature based installation** and then click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-InstallType.jpg)

### Step 4: Server Selection

Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-ServerSelection.jpg)

### Step 5: Server Role

Click on the **Hyper-V** role.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-SelectHyperVRole.jpg)

### Step 6: Add Features

Click **Add Features** within the pop.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-AddFeatures.jpg)

### Step 7: Server Role

No additional roles are required. Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-AddFeatures-Next.jpg)

### Step 8: Features

No additional features are required. Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Features-Next.jpg)

### Configure Hyper-V

#### Step 9: Hyper-V

Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Config.jpg)

#### Step 10: Virtual Switch

Select the **Microsoft Hyper-V Adapter** and click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Config-VirtualSwitch.jpg)

#### Step 11: Migration

Because I am sending up a standalone Hyper-V instance, no migration configuration is required. Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Config-Migration.jpg)

#### Step 12: Default Stores

The **Default Stores** configuration set's the default location where your Hyper-V guest VM configuration and virtual hard disks files will be stored. I have added an additional disk to the virtual machine for the storage of these files (E drive). Update the paths for the virtual hard disk and configuration files to reflect your configuration.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Config-DefaultStores.jpg)

### Step 13: Confirmation

Click **Next** to continue.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Confirmation.jpg)

### Step 14: Reboot

Once the installation of the role has been completed, reboot the virtual machine.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Role-Restart.jpg)

## Hyper-V Manager

## Step 15: Hyper-V Administration Tool

Once the server has restarted, from the **Server Manager**, select **Tools**, **Hyper-V Manager**.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Manager-Host.jpg)

## Step 16: Hyper-V Host

Click on the Hyper-V Host to get an overview of the current configuration.

![](/docs/assets/images/2022-04-28-hyperv-enable-role/HyperV-Manager-Host-Overview.jpg)

Now that I've enabled the Hyper-V role on the virtul machines, let's move onto [Networking]({% post_url 2022-04-29-hyperv-networking%}).