---
layout: post
title: "Kubernetes Cluster with Raspberry Pi Devices - Part 3: Installing Kubernetes onto the Master Node"
categories: [Raspberry Pi, K3s]
---

Now that I have my Raspberry Pi up and running, it's time to install Kubernetes. I've decided to go with [K3s](https://k3s.io/) as the Kubernetes distribution for my cluster. This is because K3s is lightweight and optimised for ARM, which makes it a great choice for my Raspberry Pi. 

This is the first time I've installed K3s and I've just been following along with the [K3s quick-start guide](https://rancher.com/docs/k3s/latest/en/quick-start/).

To install K3s, SSH to the Raspberry Pi device that will be the master node and execute the below command.

```
curl -sfL https://get.k3s.io | sh -
```

![](/docs/assets/images/raspberry-pi/k8s-setup/Install-k3s.jpg)

Now that K3s has been installed, execute the below command to get information on the nodes in the cluster.

```
sudo kubectl get nodes -o wide
```

![](/docs/assets/images/raspberry-pi/k8s-setup/MasterNodeStatus.jpg)

Execute the below command to list all the pods running on the cluster.

```
sudo kubectl get pods --all-namespaces
```

![](/docs/assets/images/raspberry-pi/k8s-setup/PodRunning.jpg)

As you can see, we now have Kubernetes up and running. Next we will look to join a worker node to our cluster.


# Troubleshooting Section

**_kubectl: The connection to the server 127.0.0.1:6443 was refused - did you specify the right host or port?_**

If you look at the syslog file (/var/logs/syslog) you will see the following error message:

_level=error msg="Failed to find memory cgroup, you may need to add \"cgroup_memory=1 cgroup_enable=memory\" to your linux cmdline (/boot/cmdline.txt on a Raspberry Pi)"_

![](/docs/assets/images/raspberry-pi/k8s-setup/kubectl_syslog_error.jpg)

To resolve this issue, follow the below steps.

```
sudo vi /boot/firmware/cmdline.txt 
```

Appended the following additions and save the modifications.

_cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1_

![](/docs/assets/images/raspberry-pi/k8s-setup/boot_cmdline_updated.jpg)


```
sudo reboot
```

