# Basic Setup

## 🔹 Hardware

In terms of hardware, this will all be dependent on how many different VMs you would like to have and what kinds of things you would like to perform. For reference, this project will be run on:

* Ryzen 7 7700x (8 cores, 16 threads) 
* 32GB of RAM
* As much storage as I have available!

Purchasing older hardware second-hand could be a good option to get things up and running. In general CPU compute (particularly core/thread count), RAM, and storage will be the most important factors as to how your network will perform and how many VMs you can run simultaneously. 

## 🔹 Virtualization

Here is a list of things that will be used:

1. <u>VMWare Workstation</u>: To setup all of the virtual machines, I will be using VMware Workstation. There are other options like VirtualBox and Windows Hyper-V, but for now I will stick with VMware.  

2. <u>Virtual Machines</u>: As of writing this first section, the different VMs that will be used will be a mixture of Windows & Linux. Eventually I'd like to have different services running on a variety of different machines in the network and have an external Kali VM that will be used to create traffic and generate alerts. For this first section we can just focus on Windows hosts such as Win10/11, Windows Server, pfSense for our firewall, and a couple of Linux hosts such as Ubuntu and Fedora.  

## 🔹 Topology

Lastly for this section we will be following this topology:

![topology map](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/topology.png?raw=true)

**Management Network** (<u>VLAN 1</u>) - This will be used to configure critical security infrastructure.

**Corporate Network** (<u>VLAN 1</u>0) - This represents the LAN for a corporate network and only has access to the simulated internet. User workstations and company servers run here.

**Corporate Security** (<u>VLAN 20</u>) - This represents a security team working at the organization to retrieve logs. Notice that the security network has access to the corporate network but not the other way around. We will allow this network to have access to the actual internet for various things.

**Hacker Network** (<u>VLAN 30</u>) - This represents bad actors from the internet trying to breach and gain access to the corporate network.

Shoutout to <u>@facyber</u> for the inspiration on the topology! (https://facyber.me/posts/blue-team-lab-guide-part-1/)

To create these VLANs in our virtual network, we will be creating networks within VMware workstation (or whatever hypervisor you are using). To do this in VMware:

1. Edit -> Virtual Network Editor -> Change Settings (might have to provide admin credentials).

2. Here we will add 4 networks and name them accordingly (I had to rename some once they were added):

   * vmnet1 (**Subnet Address**: <u>10.0.1.0</u>) - Management (*VLAN 1*)
   * vmnet10 (**Subnet Address**: <u>10.0.10.0</u>) - Corporate Network (*VLAN 10*)
   * vmnet20 (**Subnet Address**: <u>10.0.20.0</u>) - Corporate Security (*VLAN 20*)
   * vmnet30 (**Subnet Address**: <u>10.0.30.0</u>) - Hacker Network (*VLAN 30*)


   Make sure that all of these are set to host-only as we want to create our own virtual private network. We also want to uncheck use local DHCP service since we will have a DHCP server on our firewall and a windows server machine that will handle ip addressing. This is what our current configuration should look like:

   ![virtual network editor](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/network_editor.png?raw=true)
   
---

# **Firewall Setup**


## 🔹 pfSense

pfSense will be our firewall of choice as it can easily be configured through a web interface, has plenty of features, and most importantly is free. To download the pfSense Community Edition image, visit this link: https://atxfiles.netgate.com/mirror/downloads/ and download <u>*pfSense-CE-2.7.2-RELEASE-amd64.iso.gz*</u> (This download link refers you directly to their mirror so that you don't have to make a Netgate account). Make sure to extract the archive using a tool like 7-zip if you are on Windows.

1. We will now create our first VM of this project:

* CPU: 1 Core
* RAM: 1 GB
* Storage: 20 GB

* NICs:
  * VMnet0 (*Bridged*)
    * VMnet0 is our bridged connection and will be used as our outbound traffic interface (physical LAN interface). This allows traffic from inside our virtual LAN to be routed out to the internet.
  * vmnet1
  * vmnet10
  * vmnet20
  * vmnet30

2. Once all of the hardware settings are setup, go ahead and power the VM on and accept the defaults for the installation. Once rebooted, this is what we see with two interfaces (LAN and WAN):

   ![pfsense installation](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/pfsense_install.PNG?raw=true)

   

   Lets now configure our WAN interface with the correct address. Depending on the environment you are in, pfSense might automatically be assigned an address on the WAN interface by a DHCP server. If not, you'll want to figure out what subnet that your ISP's modem or router hands out IPs on. In the picture above, I was automatically assigned an IP and will stick with that as I can access the internet (don't forget to check).

3. We should now be able to access the pfSense admin panel by simply typing in the address of the firewall on our host machine. We are greeted with a sign in page and the default credentials are username: **admin**, password: **pfsense**. After logging in this is what you should see:

   ![pfsense installation](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/pfsense_welcome.PNG?raw=true)

## 🔹 Configuration

1. Lets first configure all of those interfaces that we created an added to the firewall. In the top menu bar go to *Interfaces -> Assignments*. We should then be under the Interface Assignments. From here click the green *Add* button until all of the interfaces have been added and click *Save*.

2. Now lets go through and configure all of these interfaces by clicking *Interfaces* and editing each interface to its proper settings. Here are some global settings:

   * **Enabled**: Yes
   * **Description**: NETWORK_NAME_VLAN
   * **IPv4 Configuration Type**: Static IPv4
   * **IPv6 Configuration Type**: None
   * **IPv4 Address**: IP_ADDRESS/24
   * **Block private networks and loopback address**: Unchecked (only check this for WAN interface)
   * **Block bogon networks**: Unchecked (only check this for WAN interface)

   As a quick note, if you change the Management Interface IP to something other than the Virtual Network Adapters subnet, you will no longer have connection to the web configurator with the default firewall rules.
   
   | Interface                             | IP Address               |
   | ------------------------------------- | ------------------------ |
   | WAN                                   | 192.168.1.x/24 (Or DHCP) |
   | MANAGEMENT                            | 10.0.1.50/24             |
   | CORPORATE_LAN_VLAN10                  | 10.0.10.254/24           |
   | CORPORATE_SECURITY_VLAN20             | 10.0.20.254/24           |
   | CORPORATE_WAN_VLAN30 (Hacker Network) | 10.0.30.254/24           |

![pfsense interfaces](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/pfsense_interfaces.PNG?raw=true)

![pfsense interfaces](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/pfsense_interfaces_2.PNG?raw=true)

3. We should also go through the general setup (*System -> General Setup*) to configure our hostname and a couple of other things:
   * **Hostname**: Change to whatever you like (I used firewall).
   * **Domain**: Change to whatever you like (I used aholm.homelab).
   * **DNS Servers**: 1.1.1.1 and 8.8.8.8 (Cloudflare and Google)
   * **Timezone**: Select your region

4. Time to get some firewall rules up and running so that our networks are isolated from one another (*Firewall -> Rules*):

   * **WAN Interface**:

     ![pfsense wan_interface](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/wan_interface.PNG?raw=true)

     We are allowing all traffic for now but may edit this later

   * **MANAGEMENT Interface**:

     ![pfsense management_interface](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/management_interface.PNG?raw=true)

     We currently only have one host in the management VLAN so we can keep the rules default. Ideally only one specific IP or host would be allowed to configure something like a firewall. I have disabled the IPv6 rule.

   * **CORPORATE WAN**:
   
     ![pfsense management_interface](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/corporate_wan_firewall.PNG?raw=true)
   
     For the corporate WAN, we are allowing any traffic in and allowing traffic to be sent to any Corporate Network address. We will have a deny all rule at the bottom to reject any other traffic. By default there is a default deny all rule that is invisible, but we will add that rule just to be safe.
   
   * **CORPORATE SECURITY**:
   
     ![pfsense management_interface](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/corporate_security_firewall.PNG?raw=true)
   
     The Corporate Security should only have access to the Internet and the Corporate LAN.
   
   * **CORPORATE LAN**:
   
     ![pfsense management_interface](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/vlan_firewall_setup/corporate_lan_firewall.PNG?raw=true)
   
     For the Corporate LAN, we want traffic to and from the fake internet to simulate attacks.




[**Next Section (Windows Server Setup)**](https://github.com/alexh200/blue-team-homelab/tree/main/infrastructure_setup/windows_server_setup.md)