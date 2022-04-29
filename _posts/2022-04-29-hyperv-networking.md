---
layout: post
title: "Nested Virtualization on Azure - Part 3: Networking - NAT"
categories: [Hyper-V]
---

Since my last post in this series, I've deployed a virtual machine (VM1) onto the Hyper-V host.

VM1's has been allocated to the virtual switch that was created during the enabling of the Hyper-V role (Microsoft Hyper-V Network Adapter - Virtual Switch).

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

 *"Microsoft Hyper-V Network Adapter - Virtual Switch"* configuration indicates that it has been configured as an [external virtual switch](https://docs.microsoft.com/en-us/windows-server/virtualization/hyper-v/plan/plan-hyper-v-networking-in-windows-server#switch-and-network-adapter-choices). This configuration does not allow for my guest virtual machine to egress traffic outside of the Hyper-V host. 

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-vSwitch-Original.jpg)

Looking at the guest virtual machine's IP configuration, you can see that it did not receive an IP address via DHCP and there is no default gateway.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Networking-Guest-IP.jpg)

If I try to access a website on the internet (e.g. www.microsoft.com) it does not succeed.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-NoInternetAccess.jpg)

In order to allow the guest virtual machine to route traffic externally we need to create a **NAT Virtual Network**. 


## How to create a NAT Virtual Network

From the Hyper-V host, open an Administrator PowerShell session and run the below command.

```
Get-VMSwitch
```

The output will show the virtual switches that have been created.

```
Example:

Get-VMSwitch

Name                                               SwitchType NetAdapterInterfaceDescription   
----                                               ---------- ------------------------------   
Microsoft Hyper-V Network Adapter - Virtual Switch External   Microsoft Hyper-V Network Adapter                              

```
To create the NAT Virtual Switch, follow the below commands:

### Step 1: Create a new Internal Virtual Switch

Create a new **Internal** virtual switch by running the below command. 

```
New-VMSwitch -SwitchName <SwitchName> -SwitchType Internal
```

- **SwitchName**: Name to give the virtual switch.

```
Example:

New-VMSwitch -SwitchName "Azure" -SwitchType Internal

Name  SwitchType NetAdapterInterfaceDescription
----  ---------- ------------------------------
Azure Internal                                 

```

### Step 2: Identity Virtual Switches Interface Index

Identify the newly created virtual switches interface index number.

```
Get-NetAdapter
```

```
Example:

Get-NetAdapter

Name                      InterfaceDescription                    ifIndex Status       MacAddress             LinkSpeed
----                      --------------------                    ------- ------       ----------             ---------
vEthernet (Microsoft H... Hyper-V Virtual Ethernet Adapter              9 Up           00-22-48-94-F6-8A       100 Gbps
vEthernet (Azure)         Hyper-V Virtual Ethernet Adapter #2          14 Up           00-15-5D-02-05-06        10 Gbps
Ethernet                  Microsoft Hyper-V Network Adapter             6 Up           00-22-48-94-F6-8A       100 Gbps
Ethernet 4                Mellanox ConnectX-5 Virtual Adapter #3       22                                         0 bps
```

The **ifIndex** number that we would need in this example is **14**.


### Step 3: Configure NAT Gateway IP Address

Assign an IP address to the NAT Gateway by running the below command. 

```
New-NetIPAddress -IPAddress <NAT Gateway IP> -PrefixLength <NAT Subnet Prefix Length> -InterfaceIndex <ifIndex>

```

- IPAddress: Specifies the IPv4 or IPv6 address to use as the NAT gateway IP (e.g. 192.168.0.1)
- PrefixLength: Defines the NAT local subnet size (subnet mask) (e.g. 255.255.255.0)
- InterfaceIndex: Interface index of the virtual switch (e.g. 14)

```
Example:

New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 14


IPAddress         : 192.168.0.1
InterfaceIndex    : 14
InterfaceAlias    : vEthernet (Azure)
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 192.168.0.1
InterfaceIndex    : 14
InterfaceAlias    : vEthernet (Azure)
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 24
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Invalid
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : PersistentStore


```

### Step 4: Configure NAT Network

Configure the NAT network by running the below command.

```
New-NetNat -Name <NATOutsideName> -InternalIPInterfaceAddressPrefix <NAT subnet prefix>
```

- Name: Describes the name of the NAT network (e.g. AzureNAT)
- InternalIPInterfaceAddressPrefix: NAT Subnet Prefix Length (e.g. 192.168.0.0/24)

```
Example:

New-NetNat -Name AzureNAT -InternalIPInterfaceAddressPrefix 192.168.0.0/24

Name                             : AzureNAT
ExternalIPInterfaceAddressPrefix : 
InternalIPInterfaceAddressPrefix : 192.168.0.0/24
IcmpQueryTimeout                 : 30
TcpEstablishedConnectionTimeout  : 1800
TcpTransientConnectionTimeout    : 120
TcpFilteringBehavior             : AddressDependentFiltering
UdpFilteringBehavior             : AddressDependentFiltering
UdpIdleSessionTimeout            : 120
UdpInboundRefresh                : False
Store                            : Local
Active                           : True

```

## Guest VM Changes

Now that we have a NAT gateway bound to a virtual switch, we need to configure the guest virtual machine to use it.

### Step 5: Virtual Switch

If you go to the virtual machines Hyper-V settings and click on **Network Adapters**, you can see that the guest is attached to the original virtual switch.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Original.jpg)

Click on the pull down and select the new virtual switch (e.g. Azure)

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-vSwitch-Azure.jpg)

### Step 6: Guest Machine IP Address

The NAT network does not dynamically assign IP addresses to guest virtual machines, this will require the IP address, subnet, default gateway and dns addresses to be manually assigned. 

*["Since WinNAT by itself does not allocate and assign IP addresses to an endpoint (e.g. VM), you will need to do this manually from within the VM itself - i.e. set IP address within range of NAT internal prefix, set default gateway IP address, set DNS server information."](https://docs.microsoft.com/en-us/virtualization/hyper-v-on-windows/user-guide/setup-nat-network#connect-a-virtual-machine)*

Configure the guest networking interface with the following:
- Static IP address: This should be within the address range of the NAT network specified above (e.g. 192.168.0.10)- Subnet mask: This should match the subnet mask specified above (e.g. 255.255.255.0)
- Default gateway: This should match the NAT Gateway IP address (e.g. 192.168.0.1)
- DNS Sever: IP address of a DNS server that is able to perform name resolution (e.g. 8.8.8.8) 

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-IP.jpg)

Once the above configurations have been made, the guest should be able to access the internet.

![](/docs/assets/images/2022-04-29-hyperv-networking/Hyperv-Guest-InternetAccess.jpg)
