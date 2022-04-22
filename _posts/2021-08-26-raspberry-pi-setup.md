---
title: "Creating a Kubernetes Cluster with Raspberry Pi Devices - Part 2: Setting up the Raspberry Pi"
---

Once my hardware arrived, the first thing I needed to do was to install an operating system onto the MicroSD card. Raspberry have provided the Raspberry Pi Imager tool to assist with this process. They've even provided a short video to walk you through the process of how to install an operating system.

I decided to go with the Ubuntu Server 21.04 for my Kubernetes cluster, the latest and greatest available at the time.

Ubuntu have done a great job on documenting how to install Ubuntu for a Raspberry device, so I won't document what I did as I followed the instructions pretty much line by line.

The one thing I did want to call out is that I've chosen to configure my cluster to use my Wi-Fi network rather than ethernet, mainly because I don't have an ethernet switch available to plug my devices into. 

After installing the operating system onto my MicroSD card and inserting into my Raspberry Pi, I connected the USB-C power connector and booted the device (at this point I have not insert the USB keyboard or the Micro HDMI adapter). I went onto my home Wi-Fi network to see if the device had successfully picked up an IP address on my Wi-Fi network. Sure enough it had, so I simply SSH to the device (turns out I didn't need the USB keyboard and Micro HDMI adapter after all!).

Now you're probably thinking, how can I SSH to the device when I didn't set any credentials. This is covered in the Ubuntu setup documentation. The default login ID is "ubuntu" and the password is also "ubuntu".

When you login for the first time you will be asked to change your password. Once you have done that, you will have access to the Raspberry Pi.

Before we move onto installing Kubernetes onto my master node, I'm going to change my hostname and update the host file so that name resolution will work.

Run the below command to show the devices current hostname.

```
hostnamectl
```

To permanently change the hostname run the below command.

```
sudo hostnamectl set-hostname newNameHere
```

Next I updated the /etc/hosts file to replace any reference to the old hostname and add the new hostnames FQDN with corresponding IP address.  

```
sudo nano /etc/hosts
```

Once you have saved the changes reboot the device using the below command.

```
sudo reboot
```

After the device has rebooted, SSH back in and run the hostnamectl command. You will have noticed that the hostname has changed.

Now that we have our OS configured, we can move onto Installing Kubernetes onto the Master Node.

If you have more than one Raspberry Pi, you can prepare the other devices using the previous steps.
