# **Part 1: Basic Setup**

------

## 🔹 Hardware

In terms of hardware, this will all be dependent on how many different VMs you would like to have and what kinds of things you would like to perform. For reference, this project will be run on:

* Ryzen 7 7700x (8 cores, 16 threads) 
* 32GB of RAM
* As much storage as I have available!

Purchasing older hardware second-hand could be a good option to get things up and running. In general, CPU compute (particularly core/thread count), RAM, and storage will be the most important factors as to how your network will perform and how many VMs you can run simultaneously. 

------

## 🔹 Virtualization

Here is a list of things that will be used:

1. <u>VMWare Workstation</u>: To setup all of the virtual machines, I will be using VMware Workstation. There are other options like VirtualBox and Windows Hyper-V, but for now I will stick with VMware.  

2. <u>Virtual Machines</u>: As of writing this first section, the different VMs that will be used will be a mixture of Windows & Linux. Eventually I'd like to have different services running on a variety of different machines in the network and have an external Kali VM that will be used to create traffic and generate alerts. For this first section we can just focus on Windows hosts such as Win10/11, Windows Server, pfSense for our firewall, and a couple of Linux hosts such as Ubuntu and Fedora.  

------

## 🔹 Topology

Lastly for this section we will be following this topology:

![topology](C:\Users\Alex\Downloads\topology.png)

**Corporate Network** (<u>VLAN 1</u>) - This represents the LAN for a corporate network and only has access to the simulated internet. User workstations and company servers run here.

**Corporate Security** (<u>VLAN 1</u>0) - This represents a security team working at the organization to retrieve logs. Notice that the security network has access to the corporate network but not the other way around. We will allow this network to have access to the actual internet for various things.

**Hacker Network** (<u>VLAN 20</u>) - This represents bad actors from the internet trying to breach and gain access to the corporate network.

**Management Network** (<u>VLAN 30</u>) - This will be used to configure critical security infrastructure.

Shoutout to @facyber for the inspiration on the topology! (https://facyber.me/posts/blue-team-lab-guide-part-1/)