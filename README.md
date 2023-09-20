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

<h3>Creating Microsoft Azure Virtual Machines</h3>
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
In the Networking section, be sure to put Client-1 on the same Virtual Network as DC-1.
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
