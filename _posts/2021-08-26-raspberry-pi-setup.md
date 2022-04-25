---
layout: post
title: "Creating a Kubernetes Cluster with Raspberry Pi Devices - Part 2: Setting up the Raspberry Pi"
categories: [Raspberry Pi]
---

Once my hardware arrived, the first thing I needed to do was to install an operating system onto the MicroSD card. Raspberry have provided the [Raspberry Pi Imager](https://www.raspberrypi.com/software/) tool to assist with this process. They've even provided a short video to walk you through the process of how to install an operating system.

I decided to go with the Ubuntu Server 21.04 for my Kubernetes cluster, the latest and greatest available at the time.

![Raspberry Pi Image Builder - OS Selection](/docs/assets/images/2021-08-26-raspberry-pi-setup/OSSelection.jpg)

Ubuntu have done a great job on documenting how to install [Ubuntu for a Raspberry device](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card), so I won't document what I did as I followed the instructions pretty much line by line.

The one thing I did want to call out is that I've chosen to configure my cluster to use my [Wi-Fi](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-wifi-or-ethernet) network rather than ethernet, mainly because I don't have an ethernet switch available to plug my devices into. 

After installing the operating system onto the MicroSD card and inserting into my Raspberry Pi, I connected the USB-C power connector and booted the device (at this point I have not insert the USB keyboard or the Micro HDMI adapter). I went onto my home Wi-Fi network to see if the device had successfully picked up an IP address on my Wi-Fi network. Sure enough it had, so I simply SSH to the device (turns out I didn't need the USB keyboard and Micro HDMI adapter after all!).

Now you're probably thinking, how can I SSH to the device when I didn't set any credentials. This is covered in the [Ubuntu setup documentation](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#4-boot-ubuntu-server). The default login ID is "ubuntu" and the password is also "ubuntu".

When you login for the first time you will be asked to change your password. Once you have done that, you will have access to the Raspberry Pi.

![Ubuntu login](/docs/assets/images/2021-08-26-raspberry-pi-setup/UbuntuLogin.jpg)

Before we move onto installing Kubernetes onto my master node, I'm going to change my hostname and update the host file so that name resolution will work.

Run the below command to show the devices current hostname.

```
hostnamectl
```
![Obtaining hostname](/docs/assets/images/2021-08-26-raspberry-pi-setup/ObtainHostName.jpg)

To permanently change the hostname run the below command.

```
sudo hostnamectl set-hostname newNameHere
```

![Change hostname](/docs/assets/images/2021-08-26-raspberry-pi-setup/ChangeHostName.jpg)

Next I updated the /etc/hosts file to replace any reference to the old hostname and add the new hostnames FQDN with corresponding IP address.  

```
sudo nano /etc/hosts
```
![Updating host file](/docs/assets/images/2021-08-26-raspberry-pi-setup/UpdateHostFile.jpg)

Once you have saved the changes reboot the device using the below command.

```
sudo reboot
```
![Reboot host](/docs/assets/images/2021-08-26-raspberry-pi-setup/RebootHost.jpg)

After the device has rebooted, SSH back in and run the hostnamectl command. You will have noticed that the hostname has changed.

![Confirm host name](/docs/assets/images/2021-08-26-raspberry-pi-setup/ConfirmHostName.jpg)

Now that we have our OS configured, we can move onto [Installing Kubernetes onto the Master Node](/_posts/2021-08-27-raspberry-pi-k8s-setup.md).

If you have more than one Raspberry Pi, you can prepare the other devices using the previous steps.
