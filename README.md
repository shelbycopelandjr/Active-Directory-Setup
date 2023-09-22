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

<h3></h3>
<p>
<image please>
</p>
<p>
explain
</p>
<br />

<h3></h3>
<p>
<image please>
</p>
<p>
explain
</p>
<br />

<h3></h3>
<p>
<image please>
</p>
<p>
explain
</p>
<br />

<h3></h3>
<p>
<image please>
</p>
<p>
explain
</p>
<br />

<h3></h3>
<p>
<image please>
</p>
<p>
explain
</p>
<br />
