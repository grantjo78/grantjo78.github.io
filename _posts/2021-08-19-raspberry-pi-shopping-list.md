---
layout: post
title: "Kubernetes Cluster with Raspberry Pi Devices  - Part 1: The Shopping List"
categories: [Raspberry Pi]
---

Lately I've been playing around with Kubernetes (mainly on Azure) but I have to admit, there is just something about having something physical with flashing lights to look at. So I thought, why not build a Kubernetes cluster out of Raspberry Pi devices (apparently all the cool kids are doing it). I did have another reason, which was to  extend Azure, via [Azure Arc](https://docs.microsoft.com/en-us/azure/azure-arc/kubernetes/overview#:~:text=Azure%20Arc%20enabled%20Kubernetes%20supports%20these%20scenarios%3A%201,Apply%20policies%20by%20using%20Azure%20Policy%20for%20Kubernetes.), to the Raspberry Pi Kubernetes Cluster, but that will be a different series of articles.

Before I could build the cluster I needed to spend some cash as I don't have any Raspberry Pi devices or  accessories. After doing some research I thought I'd start small and if need be add to the list down the track.

# The Initial Shopping List

2 x [Raspberry Pi 4 Model B](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/). I went with the 8GB model in the end, you can never have enough RAM in my experience. 

![](/docs/assets/images/raspberry-pi/shopping-list/raspberry-pi-4.png)

1 x [Raspberry Pi Keyboard and Hub](https://www.raspberrypi.com/products/raspberry-pi-keyboard-and-hub/). Full disclosure here, I don't have a USB keyboard or mouse and I'm pretty sure my Bluetooth Apple Magic Keyboard and Logitech MX Master 2s would not work out of the box. Plus, the Raspberry Red and White keyboard does look pretty cool.

![](/docs/assets/images/raspberry-pi/shopping-list/Keyboard.jpeg)

1 x [SanDisk Extreme 32GB MicroSD Card](https://www.amazon.com.au/Sandisk-SDSQXA1-256G-GN6MA-Extreme-microSD-Adapter/dp/B06XWMQ81P/ref=sr_1_1_sspa?dchild=1&keywords=SanDisk%2BExtreme%2B32GB%2BMicroSD%2BCard&qid=1629278289&s=computers&sr=1-1-spons&smid=A1E8JZNQ4REMVH&spLa=ZW5jcnlwdGVkUXVhbGlmaWVyPUEzN1FVOEozSlMyV0kxJmVuY3J5cHRlZElkPUEwOTI0MjM2MkFYQVJMUEVXWURMViZlbmNyeXB0ZWRBZElkPUFDMjIxMTM1UjYxS1Umd2lkZ2V0TmFtZT1zcF9hdGYmYWN0aW9uPWNsaWNrUmVkaXJlY3QmZG9Ob3RMb2dDbGljaz10cnVl&th=1). Just in case you weren't aware, the Raspberry Pi doesn't come with any persistent storage, so where do you install the operating system you ask? The Raspberry Pi does come with a MicroSD card slot and you can use a MicroSD card to provide the persistent storage for the operating system. There is [guidance](https://www.raspberrypi.com/documentation/computers/getting-started.html#sd-cards) on the Raspberry Pi site  for selecting a MicroSD Card if you're interested. 

![](/docs/assets/images/raspberry-pi/shopping-list/SanDisk.jpg)

1 x [UGREEN Micro HDMI Adapter Micro HDMI to HDMI Female Cable 4K Compatible for Raspberry Pi 4](https://www.amazon.com.au/gp/product/B00B2HORKE/ref=ppx_yo_dt_b_asin_title_o06_s00?ie=UTF8&psc=1). The Raspberry Pi 4 has [2 x Micro HDMI Ports](https://www.raspberrypi.com/products/raspberry-pi-4-model-b/specifications/) which is pretty cool. I just don't have a Micro HDMI Adapter, hence the purchase.

![](/docs/assets/images/raspberry-pi/shopping-list/UGreenMicroHDMI.jpg)

1 x [GeeekPi Raspberry Pi Cluster Case Raspberry Pi Case with Cooling Fan and Raspberry Pi Heatsink for Raspberry Pi 3 Model B+ Raspberry Pi 3/2 Model B](https://www.amazon.com.au/gp/product/B07MW3GM1T/ref=ppx_yo_dt_b_asin_title_o04_s00?ie=UTF8&psc=1). As I build out my cluster of Raspberry Pi devices, this case looked like a great way to keep things nice and tidy.

![](/docs/assets/images/raspberry-pi/shopping-list/GeeekPi.jpg)

1 x [Satechi 108W PRO USB-C PD DESKTOP CHARGER](https://satechi.net/products/108w-pro-usb-c-pd-desktop-charger?variant=32118454386776). I didn't want to purchase [individual power supplies](https://www.raspberrypi.com/products/type-c-power-supply/) for my devices and thought I could consolidate them onto this desktop hub. Hopefully it has enough juice.

![](/docs/assets/images/raspberry-pi/shopping-list/Satechi.jpg)

Once my new toys arrived I was able to move onto the next step, [Setting up the Raspberry Pi]({% post_url 2021-08-26-raspberry-pi-setup %}).
