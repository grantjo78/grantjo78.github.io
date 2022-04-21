---
layout: post
title: Creating a Kubernetes Cluster with Raspberry Pi Devices  - Part 1: The Shopping List
categories: [Raspberry Pi]
---

# Creating a Kubernetes Cluster with Raspberry Pi Devices  - Part 1: The Shopping List

Lately I've been playing around with Kubernetes (mainly on Azure) so I thought why not build a Kubernetes cluster out of Raspberry Pi devices (apparently all the cool kids are doing it). Admittedly, I do have another reason which is to extend Azure, via Azure Arc, to my Raspberry Pi Kubernetes Cluster, but that will be a different blog series.

Before I could build the cluster, I needed to spend some cash as I didn't have a Raspberry Pi or any accessories. After doing some research I thought I'd start small and if need be add to the list down the track.

## The Shopping List (initial)

1 x Raspberry Pi 4 Model B. I went with the 8GB model in the end, you can never have enough RAM in my experience. I thought I start with one device before forking out cash on another to make this a true cluster.

1 x Raspberry Pi Keyboard and Hub. Full disclosure here, I don't have a USB keyboard or mouse and I'm pretty sure my Bluetooth Apple Magic Keyboard and Logitech MX Master 2s would not work out of the box. Plus, the Raspberry Red and White keyboard does look pretty cool.

1 x SanDisk Extreme 32GB MicroSD Card. Just in case you weren't aware, the Raspberry Pi doesn't come with any persisent storage, so where do you install the operating system you ask. The Raspberry Pi does come with a MicroSD card slot. You can use a MicroSD card to provide the persisent storage for the operating system. There is  guidance on the Raspberry Pi site for selecting a MicroSD Card if you're interested. 

1 x UGREEN Micro HDMI Adapter Micro HDMI to HDMI Female Cable 4K Compatible for Raspberry Pi 4. The Raspberry Pi 4 has 2 x Micro HDMI Ports which is pretty cool. I just don't have a Micro HDMI Adapter, hence the purchase.

1 x GeeekPi Raspberry Pi Cluster Case Raspberry Pi Case with Cooling Fan and Raspberry Pi Heatsink for Raspberry Pi 3 Model B+ Raspberry Pi 3/2 Model B. As I build out my cluster of Raspberry Pi devices, this case looked like a great way to keep things nice and tidy.

1 x Satechi 108W PRO USB-C PD DESKTOP CHARGER. I didn't want to purchase individual power supplies for my devices and thought I could consolidate them onto this desktop hub. Hopefully it has enough juice.

Once you have your equipment we can move onto Setting up the Raspberry Pi.
