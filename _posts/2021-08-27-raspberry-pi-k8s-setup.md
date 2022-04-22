---
layout: post
title: "Creating a Kubernetes Cluster with Raspberry Pi Devices - Part 3: Installing Kubernetes onto the Master Node"
categories: [Raspberry Pi]
---

Now that I have my Raspberry Pi up and running, it's time to install Kubernetes. I've decided to go with K3s as the Kubernetes distribution for my cluster. The reason behind this is because K3s is lightweight and optimised for ARM, which makes it a great choice for my Raspberry Pi. 

This is the first time I've installed K3s and I've just been following along with the K3s quick-start guide.

To install K3s, SSH to the Raspberry Pi device that will be the master node in the cluster and execute the below command.

```
curl -sfL https://get.k3s.io | sh -
```

![](/docs/assets/images/2021-08-26-raspberry-pi-k8s-setup/Install-k3s.jpg)

Now that K3s has been installed, lets install kubectl onto the master node so that we can see what's happening. To install kubectl enter the below commands.

```
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl
```

Once kubectl has been installed, execute the below command to get information on the nodes in the cluster.

```
sudo kubectl get nodes -o wide
```

![](/docs/assets/images/2021-08-26-raspberry-pi-k8s-setup/MasterNodeStatus.jpg)

Execute the below command to list all the pods running on the cluster.

```
sudo kubectl get pods --all-namespaces
```

![](/docs/assets/images/2021-08-26-raspberry-pi-k8s-setup/PodRunning.jpg)

As you can see, we now have Kubernetes up and running. Next we will look to join a worker node to our cluster.
