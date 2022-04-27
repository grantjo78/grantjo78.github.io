---
layout: post
title: "Nested Virtualization on Azure - Part 2: Enabling the Hyper-V Role"
categories: [Hyper-V]
---

Now that we have a virtual machine deployed that supports nested virtualisation, I'm going to walk you through enabling the Hyper-V role on a standalone instance of Windows Server 2022.

## Enabling Hyper-V role

### Server Manager 

From the **Server Manager** select the **Add roles and features option**.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Add.jpg)

### Before you begin

Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-BeforeBegin.jpg)

### Installation type

Select **Role-based or feature based installation** and then click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-InstallType.jpg)

### Server Selection

Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-ServerSelection.jpg)

### Server Role

Click on the **Hyper-V** role.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-SelectHyperVRole.jpg)

### Add Features

Click **Add Features** within the pop.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-AddFeatures.jpg)

### Server Role

No additional roles are required. Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-AddFeatures-Next.jpg)

### Features

No additional features are required. Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Features-Next.jpg)

### Configure Hyper-V

#### Hyper-V

Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Config.jpg)

#### Virtual Switch

Select the **Microsoft Hypere-V Adapter** and click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Config-VirtualSwitch.jpg)

#### Migration

Because I am sending up a standlone Hyper-V instance, no migration configuration is required. Click **Next** to continue.

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Config-Migration.jpg)

#### Default Stores

The **Default Stores** configuration set's the default location where your Hyper-V guest VM configuration files and virtual hard disks will be stored. 

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Config-DefaultStores.jpg)

### Confirmation

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Confirmation.jpg)

### Reboot

![](/docs/assets/images/2022-04-27-hyperv-enable-role/HyperV-Role-Restart.jpg)
