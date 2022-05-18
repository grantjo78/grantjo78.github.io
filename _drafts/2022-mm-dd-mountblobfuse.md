---
layout: post
title: "Title"
categories: [Category]
---

## Contents

## What is Blobfuse

Blobfuse is a virtual file system driver for Azure Blob storage. Blobfuse allows you to access your existing block blob data in your storage account through the Linux file system. 

## The Demo Environment

## Prerequisites


[Blobfuse](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-how-to-mount-container-linux)


## Configure the Microsoft package repository



```
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

## Install blobfuse


```
sudo apt-get install blobfuse
```

sudo apt-get install blobfuse


## Use an SSD as a temporary path
```
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```



jogranttapeark001.blob.core.windows.net:/jogranttapeark001/backup01  /nfsdata    nfs defaults,sec=sys,vers=3,nolock,proto=tcp,nofail    0 0