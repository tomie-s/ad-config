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

<ul>
  <li>Setup Virtual Machines in Azure</li>
  <li>Enable Connection between Client VM and Domain Controller VM</li>
  <li>Install Active Directory on VM and Setup Domain for Domain Controller VM</li>
  <li>Create Admin user account in Active Directory</li>
  <li>Connect Client VM to the new Domain</li>
  <li>Setup Remote Desktop for Non-admin users on Client VM</li>
  <li>Create some additional users and attempt to log into the Client VM</li>
</ul>

<h2>Deployment and Configuration Steps</h2>

<b>Setup Virtual Machines in Azure</b>
<ol>
  <li>On Azure, first, create a Domain Controller VM (Windows Server 2022)
    <ul>
      <li>Update its NIC Private IP address from dynamic to static</li>
    </ul>
  </li>
  <li>Create the second VM to set up is a Client VM (Windows 10)
    <ul>
      <li><b>NOTE: Use the Resource Group and Vnet created for the Domain Controller VM when creating the Client VM.</b></li>
    </ul>
  </li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/a40edc72-c123-4186-ac63-b8e60ace2c91" height="65%" width="65%" alt="Domain Controller VM created"/>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/b047b38e-60bc-4a0f-97a7-2ae3bf0d8034" height="65%" width="65%" alt="Client VM created"/>
</p>
<br />

<b>Enable Connection between Client VM and Domain Controller VM</b>
<ol>
  <li>Log into Client-1 with Remote Desktop and ping DC-1’s private IP address to test the connection between the two VMs
    <ul>
      <li>You should see 'Request Timed Out' response</li>
    </ul>
  </li>
  <li>Log into the Domain Controller VM and enable ICMPv4 to come in on the local Windows Firewall</li>
  <li>Go back to Client-1 to confirm that the ping is now successful</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/f5ef6674-9739-456a-b81d-e12a1da4e9a7" height="65%" width="65%" alt="ICMPv4 request enabled on local Windows Firewall"/><br />
<p>Before and After enabling ICMPv4 requests on Windows Firewall</p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/dc951736-1d14-4c1d-9b8f-7514d08cf0be" height="45%" width="45%" alt="Ping response before enabling ICMPv4 requests"/>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/c114921d-c00a-4353-abc7-bd5435ccd56e" height="45%" width="45%" alt="Ping response after enabling ICMPv4 requests"/>
</p>
<br />

<b>Install Active Directory on VM and Setup Domain for Domain Controller VM</b>
<ol>
  <li>Log into the Domain Controller VM (if needed), launch 'Server Manager', and select 'Add Roles and Features' to install <b>Active Directory Domain Services</b></li>
  <li>Continue the AD deployment and set up a new forest with a domain name - xxxxxx.com</li>
  <li>Once domain setup is complete, restart the Domain Controller VM and log in as xxxxxx.com\username (the same username used to log in before)</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/d7238335-54d5-44ee-b30d-3f23327f2f2b" height="45%" width="45%" alt="Active Directory Domain Services installation"/>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/d7587aff-7876-45b6-96ee-b0c31d0ebef1" height="45%" width="45%" alt="Setting up new domain on AD DS"/>
</p>
<br />

<b>Create Admin user account in Active Directory</b>
<ol>
  <li>In the Domain Controller VM, launch AD Users and Computers and create an Organization Unit (OU) named EMPLOYEES and another OU named ADMINS in the xxxxx.com folder</li>
  <li>In the ADMINS folder create a new admin user and add them to the “Domain Admins” Security Group</li>
  <li>Log out of the VM and log back in as this admin user</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/9bbd0b65-a4ed-4722-87f0-ab4f2d4af061" height="45%" width="45%" alt="New OUs created"/>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/8b619bba-4047-4e42-a23c-edb5442f26fb" height="45%" width="45%" alt="New admin account created"/>
</p>
<br />

<b>Connect Client VM to the new Domain</b>
<ol>
  <li>On Azure Portal, set the Client VM (Client-1) DNS settings to the Domain Controller VM’s (DC-1) Private IP address
    <ul>
      <li>In the Azure Portal, restart Client-1</li>
    </ul>
  </li>
  <li>Log back into Client-1 VM, go to settings and select 'Rename this PC (advanced)' to connect the VM with the new domain
    <ul>
      <li>Once completed, a 'Welcome to the xxxxx.com domain' message should appear and a restart will be required</li>
    </ul>
  </li>
  <li>Login to the Domain Controller VM and verify that Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/e7ad5a66-b75a-4b42-ba18-9e8a32e81000" height="65%" width="65%" alt="Client VM DNS set to DC VM Private IP"/>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/fbc0d19e-29bc-4a28-bbdb-92e66f88a6f6" height="65%" width="65%" alt="Connect Client VM to new Domain"/>
</p>
<br />

<b>Setup Remote Desktop for Non-admin users on Client VM</b>
<ol>
  <li>Log into Client-1 VM as xxxxxxx.com\admin_username and open system settings</li>
  <li>Select 'Remote Desktop' and allow “domain users” access to remote desktop</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/1823be9c-5c1f-4064-a82b-1527696f40ab" height="70%" width="70%" alt="Remote Desktop setup for non-admin users"/>
</p>
<br />

<b>Create some additional users and attempt to log into the Client VM</b>
<ol>
  <li>Login to DC-1 VM as jane_admin and create a couple of new standard users for the EMPLOYEES folder
    <ul>
      <li>See the 'Create Admin user account in Active Directory' step for reference</li>
    </ul>
  </li>
  <li>Attempt to log into Client-1 VM with one of the new standard accounts to confirm admin and non-admin users can access the domain</li>
</ol>
<p>
<img src="https://github.com/tomie-s/ad-config/assets/59409588/b7b321cb-b624-49b4-a88a-c9077538abad" height="65%" width="65%" alt="new non-admin users created"/>
</p>
<br />
