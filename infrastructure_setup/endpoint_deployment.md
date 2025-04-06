# **Endpoint Deployment**

### 🔹 Installation

Now that our domain is setup, lets create a couple of different user workstations to generate logs for further use. I am going to install Windows 11 as that is easiest to get an iso file. These VMs don't need a ton of resources, just enough to get booted and logged in. Don't forget to put the VMs on the same network as your Domain Controller & make sure its running. To join a domain go to *Control Panel -> System and Security -> System -> Domain or workgroup -> Change -> Member of: type in your domain*. I also chose to rename the machine to Workstation#. If you don't have an IP address, you will not be able to join the domain properly. 

![change_domain](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/endpoint_deployment/change_domain.PNG?raw=true)

Now is the time to check to make sure your DHCP and AD servers are configured properly. Make sure you can log into the domain with the accounts you created previously. 

### 🔹 Sysmon Setup

System Monitor (Sysmon) is a system service that logs system activity to the Windows event log in much more detail than default windows logging. It gives us a lot of information regarding things like process creations, network connections, and file system changes. This will allow us to send detailed logs to our SIEM and analyze for malicious activity. The configuration of this application can get extremely detailed and focused depending on an organizations needs. For now I will be using SwiftOnSecurity's Sysmon config (*https://github.com/SwiftOnSecurity/sysmon-config*) as it will give me a good starting point that wont overrun me in logs. Lets get to installing this on our server and workstations. Visit the Microsoft Article on Sysmon (https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon) and download the Sysmon zip folder as well as the .xml config from SwiftOnSecurity's Github repo and put the config in the extracted folder. I'm going to move the folder to the root directory of our workstations and server. You may want to either allow internet traffic temporarily into your Corporate LAN to complete this or create a shared folder through your hypervisor and copy the folder onto the VMs. To install this, open PowerShell as Admin, navigate to the folder, and run the following:

![sysmon_install](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/endpoint_deployment/sysmon_install.PNG?raw=true)

### 🔹 Generating Logs

Let's log into a different user on each workstation and start to generate some logs for our future SIEM setup. Here is a list of things we can do to simulate user activity:

* Open different applications
* Login/logout
* Create files & folders (use different extensions ex. .txt .docx .ps1)
* Setup a simple task in Task Scheduler every few minutes
* Create UAC events