<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create Microsoft Azure Virtual Machines
- Connect the virtual machines
- Install and Setup Active Directory
- Join Client to Domain Controller
- Setup Remote Desktop for Non-Admins

<h2>Deployment and Configuration Steps</h2>

<h3> 1. Creating Microsoft Azure Virtual Machines</h3>
<p>
<a href="https://imgur.com/dBVwKID"><img src="https://i.imgur.com/dBVwKID.png" title="source: imgur.com" /></a>
</p>
<p>
In Microsoft Azure, provided you have an active subscription, search "Virtual Machines" and click on the corresponding link. Once there, we need to create our Domain Controller (DC-1). Create a new resource group and take note of the name. We can just use DC-1 for the name of our Domain Controller. As for the region, take note of which region and zone you choose. For security type, I personally have has issues doing this when set to anything other than "Standard" so I recommend that's where we set it. For the image, we'll be using Windows Server 2022.
</p>
<p>
<a href="https://imgur.com/PhGP3r6"><img src="https://i.imgur.com/PhGP3r6.png" title="source: imgur.com" /></a>
</p>
<p>
Under the Networking section, we also want to take note of the name of the Virtual Network being created. Click Review + Create. After a small validation period, check to make sure everything is set up correctly and confirm the creation of DC-1.
</p>
<br />

<p>
<a href="https://imgur.com/dBVwKID"><img src="https://i.imgur.com/dBVwKID.png" title="source: imgur.com" /></a>
</p>
<p>
<a href="https://imgur.com/LMuUXgW"><img src="https://i.imgur.com/LMuUXgW.png" title="source: imgur.com" /></a>
</p>
<p>
Next, preferably after the first Virtual Machine has finished deploying to avoid complications, we create a second VM. This VM will be known as Client-1. Make sure to put Client-1 in the same resource group, region, and availability zone. Set security type to standard, and for Client-1 we will be using the Windows 10 (22H2) image. Be sure to check the authorization box at the bottom before you proceed to the networking tab.
</p>
<br />

<p>
<a href="https://imgur.com/L6SKTUc"><img src="https://i.imgur.com/L6SKTUc.png" title="source: imgur.com" /></a>
</p>
<p>
In the Networking section, be sure to put Client-1 on the same Virtual Network as DC-1. Next, Review + Create, just like before.
</p>
<br />

<h3> 2. Connect the Virtual Machines</h3>
<p>
Because connectivity is very important for this to work and we want to simplify the process by which users can connect to our system we want to make sure that DC-1's IP Address isn't changing all the time. We do this by changing it's IP from Dynamic (using whatever it feels like at the time) to Static (using 1 and only 1 IP Address).
</p>
<p>
<a href="https://imgur.com/EEgzPkT"><img src="https://i.imgur.com/EEgzPkT.png" title="source: imgur.com" /></a>
</p>
<p>
We can do this cloud side by navigatting to the DC-1 VM and clicking the Networking option in the left panel...
</p>
<p>
<a href="https://imgur.com/1qpCpo5"><img src="https://i.imgur.com/1qpCpo5.png" title="source: imgur.com" /></a>
</p>
<p>
then clicking on it's network interface...
</p>
<p>
<a href="https://imgur.com/LWPFdmF"><img src="https://i.imgur.com/LWPFdmF.png" title="source: imgur.com" /></a>
</p>
<p>
Then on the Network Interface screen, click the IP Configurations option in the left panel...
</p>
<p>
<a href="https://imgur.com/qV0i9sj"><img src="https://i.imgur.com/qV0i9sj.png" title="source: imgur.com" /></a>
</p>
<p>
then clicking on its current IP Configuration (likely ipconfig1 if this VM is fresh)...
</p>
<p>
<a href="https://imgur.com/yl68fyd"><img src="https://i.imgur.com/yl68fyd.png" title="source: imgur.com" /></a>
</p>
<p>
Then, on the right hand side, switching its allocation from Dynamic to Static. Be sure to click Save at the bottom.
</p>
<br />

<p>
Next we need to connect Client-1 to DC-1 to make sure they can communicate with each other.
</p>
<p>
<a href="https://imgur.com/kTxVQvU"><img src="https://i.imgur.com/kTxVQvU.png" title="source: imgur.com" /></a>
</p>
<p>
In remote desktop, we want to log in to Client-1 by typing its *PUBLIC* IP Address into the text box.
</p>
<p>
<a href="https://imgur.com/yEuqQBK"><img src="https://i.imgur.com/yEuqQBK.png" title="source: imgur.com" /></a>
</p>
<p>
Once done, this credentials window should appear. In it, we want to type in the username and password used when we create the Client-1 VM on the Azure platform.
</p>
<p>
Once logged in, open Command Prompt from the start menu. Be sure to open it with Administrator priveleges.
</p>
<p>
<a href="https://imgur.com/bvl7bwC"><img src="https://i.imgur.com/bvl7bwC.png" title="source: imgur.com" /></a>
</p>
<p>
Once inside, we want to type "ping -t [DC-1's *PRIVATE* IP Address]" and press enter. To find DC-1's private IP Address, navigate to the DC-1 VM on Azure and it should be displayed in the bottom right quadrant of the screen.
</p>
<p>
Leaving that ping command running, we now need to open another instance of Remote Desktop and, this time, type DC-1's *PUBLIC* IP Address in the text box, logging in with the credentials used when creating DC-1's VM on the Azure platform.
</p>
<p>
<a href="https://imgur.com/bz7PHuj"><img src="https://i.imgur.com/bz7PHuj.png" title="source: imgur.com" /></a>
</p>
<p>
Logged into DC-1, open Windows Firewall with Advanced Security from the Start Menu. Again, you can do this by typing it into the search box.

In this windows, select Inbound Rules in the left panel. Next, you want to find 2 things.
Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In), one for Private and one for domain. In this picture, I used the sort tabs to separate the rules by Protocol. You'll be looking for ICMPv4.
Once you locate them, enable them, either by clicking on them and selecting Enable Rule on the right side panel or by right clicking them and selecting Enable Rule.
</p>
<p>
<a href="https://imgur.com/t0B3k03"><img src="https://i.imgur.com/t0B3k03.png" title="source: imgur.com" /></a>
</p>
<p>
Next, going back to Client-1, if all went as planned, Command Prompt should have switched from showing "Request Timed Out" to an actual reply from DC-1 for the pings. This is our evidence thats DC-1 and Client-1 are communicating. Press Ctrl+C to stop the pings.
</p>
<br />

<h3>3. Install and Setup Active Directory</h3>
<p>
Now that the VMs are talking it's time to install Active Directory.
</p>
<p>
<a href="https://imgur.com/iNf5IRv"><img src="https://i.imgur.com/iNf5IRv.png" title="source: imgur.com" /></a>
</p>
<p>
On the DC-1 VM, access the Server Manager. Usually, this pops up on the screen when logging in but you should be able to open it from the Start Menu. Once there, click the "Add roles and features" option.
</p>
<p>
<a href="https://imgur.com/2mVNWzT"><img src="https://i.imgur.com/2mVNWzT.png" title="source: imgur.com" /></a>
</p>
<p>
When you get to the part of choosing server roles, check the box next to Active Directory Domain Services, then click the Add Features button in the bottom right.
</p>
<p>
<a href="https://imgur.com/XVPyLaj"><img src="https://i.imgur.com/XVPyLaj.png" title="source: imgur.com" /></a>
</p>
<p>
Once you come to the confirmation screen, read over the information box to verify it is what you're looking to do and, if all is well, click the Install button.
</p>
<p>
<a href="https://imgur.com/Wc1lU4n"><img src="https://i.imgur.com/Wc1lU4n.png" title="source: imgur.com" /></a>
</p>
<p>
When that is finished installing, you'll notice on the Server Manager Dashboard there is a yellow triangle trying to get your attention in the top left next to the flag. Click the flag and select the option that says "Promote this server to a domain controller".
</p>
<p>
<a href="https://imgur.com/sd7jFKn"><img src="https://i.imgur.com/sd7jFKn.png" title="source: imgur.com" /></a>
</p>
<p>
This should open the Deployment Configuration window. Here, select the "Add a new forest" option and choose a name for your domain. Then, click the Next button. Be sure to remember the password you use on the next screen in case you need it for future use.
</p>
<p>
  <a href="https://imgur.com/Wlskgij"><img src="https://i.imgur.com/Wlskgij.png" title="source: imgur.com" /></a>
</p>
<p>
After a while of the VM doing it's background work and you clicking the next button you'll come to this screen. The VM should, after a while, tell you that all prerequisites have been met. Click the Install button. Once it installs and finishes setting up the server as a domain controller, it will automatically restart. This will also kick you out of the Remote Desktop connection. This is normal. You cannot connect to a computer that isn't "on", so we wait a little while and reestablish our Remote Desktop connection. There is a chance the PUBLIC IP of the DC-1 VM could change, so that's something to look out for.
</p>
<p>
<a href="https://imgur.com/PMf99eq"><img src="https://i.imgur.com/PMf99eq.png" title="source: imgur.com" /></a>
</p>
<p>
Also, the VM is now officially a domain controller, so instead of logging into DC-1 like we may have down previously, we now need to log into the domain itself using your username @ whatever your domain name is. (ex. LetmaPPolgo@mydomain.com)
</p>
<p>
<a href="https://imgur.com/86ohFTb"><img src="https://i.imgur.com/86ohFTb.png" title="source: imgur.com" /></a>
</p>
<p>
To do this, you can log in as you normally would but when you come to credentials window, click the "More choices" option and choose "Use a different account".
</p>
<p>
<a href="https://imgur.com/xEhXQik"><img src="https://i.imgur.com/xEhXQik.png" title="source: imgur.com" /></a>
</p>
<p>
Then you will enter your username @ domain name combo (ex. LetmaPPolgo@mydomain.com) followed by the same password you've already been using. This should get you back into the domain controller.
</p>
<br />

<h3>4. Configuring Active Directory For Use</h3>
<p>
<a href="https://imgur.com/ToDCbG8"><img src="https://i.imgur.com/ToDCbG8.png" title="source: imgur.com" /></a>
</p>
<p>
Once you're back in the system and at the Server Manager Dashboard, you now need to make some users and attach some computers to the domain. In the Tools menu in the top left of the window select the Active Directory Users and Computers option.
</p>
<p>
  <a href="https://imgur.com/hNHN9Ho"><img src="https://i.imgur.com/hNHN9Ho.png" title="source: imgur.com" /></a>
</p>
<p>
For this project, we created 2 organizational units (folders essentially) within our domain. To do this, click on your domain's name in the left panel. From there you can click the folder icon in the hotbar furthest to the right or you can right-click the domain, go to create new, and choose organizational unit from the drop down. We ended up making one for EMPLOYEES and one for ADMINS.
</p>
<p>
<a href="https://imgur.com/0IJVAzY"><img src="https://i.imgur.com/0IJVAzY.png" title="source: imgur.com" /></a>
</p>
<p>
Now, we want to create an admin account. This is so that there is an account on the system with the necessary priveleges to operate throughout the network. In the ADMINS organizational unit, create a new user either by clicking the icon indicated or by right clicing the ADMINS OU, going to Create New, and choosing user from the drop down. In this window, fill in all the necessary information and make a note of of the logon name and password. We'll need that later. 
</p>
<p>
<a href="https://imgur.com/ocZgmBm"><img src="https://i.imgur.com/ocZgmBm.png" title="source: imgur.com" /></a>
</p>
<p>
Next, right click on the new user and select Properties.
</p>
<p>
<a href="https://imgur.com/NCm8nC4"><img src="https://i.imgur.com/NCm8nC4.png" title="source: imgur.com" /></a>
</p>
<p>
Click the "Member Of" tab.
</p>
<p>
<a href="https://imgur.com/GAIFOmA"><img src="https://i.imgur.com/GAIFOmA.png" title="source: imgur.com" /></a>
</p>
<p>
Click the Add button.
</p>
<p>
<a href="https://imgur.com/Z6S1WEo"><img src="https://i.imgur.com/Z6S1WEo.png" title="source: imgur.com" /></a>
</p>
<p>
Type "Domain Admins" in the text field. You can click the Check Names button to make sure it is a valid entry. The click OK. Now we have our Admin User set up for the next phase.
</p>
<br />

<h3>5. Joining Computers to the Network</h3>
<p>
Now we need to put computers on the network. This is why we needed the Client-1 VM for this project.
</p>
<p>
<a href="https://imgur.com/skzZs9o"><img src="https://i.imgur.com/skzZs9o.png" title="source: imgur.com" /></a>
</p>
<p>
Back in Microsoft Azure, we now need to navigate to the Client-1 VM and select Networking from the left panel.
</p>
<p>
<a href="https://imgur.com/D1Nh24p"><img src="https://i.imgur.com/D1Nh24p.png" title="source: imgur.com" /></a>
</p>
<p>
Click the Network Interface link.
</p>
<p>
<a href="https://imgur.com/gE6vCPB"><img src="https://i.imgur.com/gE6vCPB.png" title="source: imgur.com" /></a>
</p>
<p>
Select the DNS Servers setting from the left panel.
</p>
<p>
<a href="https://imgur.com/9GIj3SF"><img src="https://i.imgur.com/9GIj3SF.png" title="source: imgur.com" /></a>
</p>
<p>
Here in the DNS Server settings, click the Custom option and for the DNS Server type in the PRIVATE IP address from *DC-1*. Remember to click the Save button when finished.
</p>
<p>
<a href="https://imgur.com/Uz88tKw"><img src="https://i.imgur.com/Uz88tKw.png" title="source: imgur.com" /></a>
</p>
<p>
Then, back in the Client-1 VM's overview page, restart the VM. If you are still connected to it using Remote Desktop, the connection will be cut. Just wait for Client-1 to restart and log back in as normal.
</p>
<p>
<a href="https://imgur.com/3UPma3k"><img src="https://i.imgur.com/3UPma3k.png" title="source: imgur.com" /></a>
</p>
<p>
Once logged back into Client-1, open up Control Panel from the Start menu. From there, select System and Security, then select the System setting.
</p>
<p>
<a href="https://imgur.com/U4287qf"><img src="https://i.imgur.com/U4287qf.png" title="source: imgur.com" /></a>
</p>
<p>
Next, click on the option that says *Rename this PC (advanced)* from the right hand panel.
</p>
<p>
<a href="https://imgur.com/T9iTD6p"><img src="https://i.imgur.com/T9iTD6p.png" title="source: imgur.com" /></a>
</p>
<p>
Then, click the Change button.
</p>
<p>
<a href="https://imgur.com/HuoU5d8"><img src="https://i.imgur.com/HuoU5d8.png" title="source: imgur.com" /></a>
</p>
<p>
Change the "Member of" option to Domain and fill in the text box with the name of your domain. Click OK.
</p>
<p>
<a href="https://imgur.com/z1F32hy"><img src="https://i.imgur.com/z1F32hy.png" title="source: imgur.com" /></a>
</p>
<p>
When this window pops up, put in the user name and password of the *ADMIN YOU CREATED ON DC-1*. 
</p>
<p>
<a href="https://imgur.com/jMamAN5"><img src="https://i.imgur.com/jMamAN5.png" title="source: imgur.com" /></a>
</p>
<p>
If all goes well, this little confimation should appear on the screen. It may show up *behind* other windows, so don't be surpised if you have to minimize some things to see it.
</p>
<br />

<h3>6. Setting Up Remote Desktop For Non-Admins</h3>
<p>
Now we need anyone in the organization to be able to use this computer to connect the network.
</p>
<p>
<a href="https://imgur.com/JeaNPnt"><img src="https://i.imgur.com/JeaNPnt.png" title="source: imgur.com" /></a>
</p>
<p>
Now that Client-1 is connected to the domain, log in to Client-1 as *the ADMIN OF DC-1*. From there, go to the Control panel via the Start Menu, click on System and Security, and click the System setting.
</p>
<p>
<a href="https://imgur.com/jW8JLhc"><img src="https://i.imgur.com/jW8JLhc.png" title="source: imgur.com" /></a>
</p>
<p>
In this window, click the Remote Desktop option on either side.
</p>
<p>
<a href="https://imgur.com/3eh0TyJ"><img src="https://i.imgur.com/3eh0TyJ.png" title="source: imgur.com" /></a>
</p>
<p>
Click the option that says "Select users that can remotely access this PC".
</p>
<p>
<a href="https://imgur.com/yhJDSgf"><img src="https://i.imgur.com/yhJDSgf.png" title="source: imgur.com" /></a>
</p>
<p>
This should look familiar. Click the Add button.
</p>
<p>
<a href="https://imgur.com/zcaiR39"><img src="https://i.imgur.com/zcaiR39.png" title="source: imgur.com" /></a>
</p>
<p>
In the text field, type "Domain Users". Use the Check Names button on the right to validate if necessary.

And that's that. Now any user added into DC-1 should be able to access Client-1 remotely.
</p>
<br />

<h3>DONE</h3>
<p>
AWESOME. This should prove to be a solid basis to build from. I hope this little project was helpful.
</p>
<br />
