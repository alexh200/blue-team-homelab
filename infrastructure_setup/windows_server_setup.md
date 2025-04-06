# **Windows Server Setup**

## 🔹 Installation

For our first server lets get Windows Server installed. In my homelab, I have installed Server 2022 but anything recent (like 2019) should work just fine. I gave my VM 4 cpus and 4gb of ram for now, but this can always be changed later. Don't forget to put the it on the vmnet10 network. Accept the defaults for the installation and set a password for your newly created Administrator account.

### 🔹 Configuration

Lets first set an static IP address as we don't want the IP to be changing on our Domain Controller. To do so, go to *Windows Icon -> Settings -> Network & Internet -> Change Adapter Options -> Right click your Adapter -> Properties -> IPv4 Properties*:

![ip_settings](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/domaincontroller_ip_settings.PNG?raw=true)

Next up we want to set our hostname, then reboot for it to set (*Server Manager -> Local Server -> Computer Name*. Click *Change* and then *OK*): 

![hostname](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/hostname.PNG?raw=true)

To get our roles and features installed go to *Manage -> Add Roles and Features ->* Accept the first 3 defaults -> *Server Roles*:

![AD_roles_and_features](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/roles_and_features.PNG?raw=true)
**Make sure you select Remote Access (I forgot this in the initial screenshot)**

Now just accept the rest of the defaults and hit install once you get to the last page. Once the roles are installed we will see a few notifications that need to be taken care of in the top right of the Server Manager. First we want to promote the Windows Server to a Domain Controller by clicking *Promote this server to a domain controller*:

![promote_domain_controller](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/promote_domain_controller.PNG?raw=true)

This will open up a wizard for us to create a new forest. Select *Add a new forest* and put in the domain name that you would like to use and click next. Leave the settings default and insert your Administrator password. Leave everything else default and click install. The server will then reboot. Upon logging in we should now see that our domain has been successfully created:

![login screen](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/login_screen.PNG?raw=true)

### 🔹 DHCP Setup

Now lets get our DHCP server setup and running. Go to *Server Manager -> Tools -> DHCP* to open up the DHCP dialog. Right click *IPv4* and click *New Scope*. Enter your new scope name and hit next:

![new_scope](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/new_scope.PNG?raw=true)

If we refer back to our networking table we can see that our Corporate LAN network is a 10.0.10.0 network so that is what IP addresses we will be handing out. Remember that our Domain Controller IP is 10.0.10.0 so we do not want to give that IP out to a host. In the real world we would create a DHCP reservation so the server would know to give out the .50 IP only to the server, but we simply won't have  many hosts so we will just forget that for now and start at 51.

![dhcp_scope_range](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/dhcp_scope_range.PNG?raw=true)

Once you make it to the *Router (Default Gateway)* page, make sure to set your server's IP as the default gateway and click add: 

 ![default_gateway](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/default_gateway.PNG?raw=true)

We will leave Domain Name and DNS Servers default as it has automatically added our servers IP address for DNS resolution:

 ![domain_name_dns_server](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/domain_name_dns_server.PNG?raw=true)

We can add our IP to the WINS server dialog but this is primarily an older technology which was a predecessor to DNS. Once you make it to the end, make sure *Yes, I want to activate this scope now* is checked and click next & finished. We should now see our newly created Scope and address pool:

 ![dhcp_updated](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/dhcp_updated.PNG?raw=true)

### 🔹 DNS Setup

Lets now setup our DNS server. From *Server Manager* open *Tools -> DNS*. Expand your Server so that you are able to see *Reverse Lookup Zones* and Right Click -> *New zone*. Keep hitting next until you get to the page where we setup our network ID. Insert your network ID (Mine is 10.0.10) and from here we can just accept all the defaults and click finish.

![reverse_lookup](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/reverse_lookup.PNG?raw=true)

Your Reverse Lookup Zone should now be listed in the right hand panel. Under our Forward Lookup Zone lets add an A record (IPv4 Entry) for our firewall so that it can be resolved by other hosts. Right click your domain and select *New Host (A or AAAA)*:

![firewall_dns_entry](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/firewall_dns_entry.PNG?raw=true)

Now if we run a nslookup on our firewall, we are returned the correct IP address:

![nslookup](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/nslookup.PNG?raw=true)

### 🔹 Routing Setup

Lastly, since we want our logs to be sent to the SIEM, we need to configure routing. From Server Manager select *Tools -> Routing and Remote Access*. Then right click on your domain controller and select *Configure* and *Enable Routing and Remote Access*. Select *Custom configuration* then *LAN routing* and *Finish*. Then start the service. From here we are going to select Static Routes and create a new static route. 

![new_static_route](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/new_static_route.PNG?raw=true)

Checking our networking table from previous sections shows that our Corporate Security network is 10.0.20.0. I will assign the SIEM on that network to 10.0.20.50, so that is what I will put into the Destination field in the new route. 

![configured_static_route](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/configured_static_route.PNG?raw=true)

### 🔹 Active Directory Setup

Lastly for this section lets create some users and groups in Active Directory. These users and groups are completely arbitrary so feel free to create anything and everything. To do this in Server Manager go to *Tools -> Active Directory Users and Computers*. Expand your domain and right click it, select new *Organizational Unit* and name it *Groups*:

![new_organizational_unit](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/new_ou.PNG?raw=true)

Here we can just create a few random groups. I chose Developers, HR, and Accounting. To do this, just repeat the steps above but make sure it's a new group (instead of OU) inside of the newly created *Groups* OU. Now we can create a few new users using *username.lastname* as their login, and random passwords. For a couple of their passwords, I'm going to use a password from *rockyou.txt* to imitate bad password policy. Make sure these users are created in the *Users* OU and their passwords never expire.

![aduc_users](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/aduc_users.PNG?raw=true)
![aduc_groups](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/aduc_groups.PNG?raw=true)

Be sure to add one of your users to the administrator group. To do this right click on one of your users and select *Properties* and go to the *Member Of* tab. From here click *Add*, type *Administrator* in the object names field,  select *Check Names*, and *OK*. We will want one of these users to be able to manage host workstations which will require us creating a local admin policy in Group Policy.

From Server Manager select *Tools -> Group Policy Management* and expand your forest, domains, and domain itself. Right Click on your domain and select *Create a GPO in this domain, and Link it here...* Name it whatever you would like.

![group_policy](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/group_policy.PNG?raw=true)

Right click your newly created GPO and select *Edit*. From here we will transverse to *Computer Configuration > Policies > Windows Settings > Security Settings > Restricted Groups* and create a new group inside named something like Local Admins and add a user or two to it. 

![local_admins](https://github.com/alexh200/blue-team-homelab/blob/main/infrastructure_setup/images/windows_server_setup/local_admins.PNG?raw=true)

In the next section we will create a new Windows host machine and log into our domain with one of the newly created users.



[**Next Section (Endpoint Deployment)**](https://github.com/alexh200/blue-team-homelab/tree/main/infrastructure_setup/endpoint_deployment.md)