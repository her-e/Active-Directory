## Active Directory with Virtual Machines - Implementing Domain Controller and Group Policy.

## Description
In this lab, I am going to walk through how to create an Active Directory home lab Environment using Oracle VirtualBox. Configuring and running this lab will help develop our understanding how Active Directory and windows networking works. In this lab, we will be creating our domain controller on a virtual machine that has two network adapters. This will connect to our outside internet and the other network adapter will connect to our VirtualBox private network. This will allow our client's PC to connect to the internet through the domain controller. We will be configuring our NAT and routing, DHCP, DNS, IP Address on the domain controller, and then a Client PC on another virtual machine. This will allow us to apply Group Policy Objects and provision, maintain, and deprovision users in Active Directory.

<br>

## Important:

We’re intentionally setting up a vulnerable ecosystem, some of these practices are simply unrealistic in a working real world solution.
The whole intention is getting ourselves a practice ground to get our hands dirty and gain some knowledge with AD’s numerous vulnerabilities and defensive methodologies.
  
<br>

## What is Active Directory?

Active Directory (AD) is a database and set of services that connect users with the network resources they need to get their work done.

The database (or directory) contains critical information about your environment, including what users and computers there are and who is allowed to do what. For example, the database might list 100 user accounts with details like each person’s job title, phone number and password. It will also record their permissions.

These services control much of the activity that goes on an IT environment.

<br>

## **How does Active Directory work?**

The main Active Directory service is Active Directory Domain Services (AD DS), which is part of the Windows Server operating system. The servers that run AD DS are called domain controllers (DC’s). Organizations normally have multiple DC's, and each one has a copy of the directory for the entire domain. Changes made to the directory on one domain controller — such as password update or the deletion of a user account — are replicated to the other DC’s so they all stay up to date. Desktops, laptops and other devices running Windows (rather than Windows Server) can be part of an Active Directory environment but they do not run AD DS. AD DS relies on several established protocols and standards, including LDAP (Lightweight Directory Access Protocol), Kerberos and DNS (Domain Name System).

It’s important to understand that Active Directory is only for on-premises Microsoft environments. AD and Azure AD are separate but can work together to some degree if your organization has both on-premises and cloud IT environments (a hybrid deployment).

</p>

[Source](https://www.quest.com/solutions/active-directory/what-is-active-directory.aspx#:~:text=Active%20Directory%20(AD)%20is%20a,who%27s%20allowed%20to%20do%20what.)

<br>

## Set up and Configure Domain Controller:
Note: Enabled 2 network adapter before starting up (NAT and Internal Network) on Windows Server 2019. <br/>
- Assign static IP Address (172.16.0.1), Subnet Mask (255.255.255.0), and Preferred DNS (127.0.0.1) to Internal Network:  <br/>
<p align="center">
<img src="https://imgur.com/ADSxZOl.png" height="80%" width="80%" alt="Active Directory"/>

- Add Features and Roles: Active Directory Domain Services, then promote to Domain Controller. 
<p align="center"> 
<img src="https://imgur.com/xl1leju.png" height="80%" width="80%" alt="Active Directory"/>
<br />
<br />

## Configure Networks (Remote Access and DHCP Server):
- Add Roles and Feature "Remote Access", then add feature "Routing" and "DirectAccess and VPN (RAS)", then Install. Configure and Enable Routing and Remote Access, Select "NAT", then Select Internet, Finish Configuration: <br>
<p align="center">
<img src="https://imgur.com/dpwGi5s.png" height="80%" width="80%" alt="Active Directory"/>

- Install DHCP Server in Add Roles and Feature, then Configure (Create New Scope in IPv4):<br/>
Scope Name: 172.16.0.100-200<br>
Configure Start (172.16.0.100) and End (172.16.0.200) IP Address, and Subnet Mask (255.255.255.0):<br/>
<p align="center">
<img src="https://imgur.com/RhyBLK9.png" height="80%" width="80%" alt="Active Directory"/>

- Set Lease Duration (3 days for this Lab):<br/>
- Add IP Address to Router (Default Gateway), then authorize:<br/>
<p align="center">
<img src="https://imgur.com/G7zu3DE.png" height="80%" width="80%" alt="Active Directory"/>
<br />
<br />


## Create new Windows 10 Virtual Machine and join Domain:
- Set up Windows 10 (New VM), Verify IP Address for 172.16.0.100 on Command Prompt and domain join workstation.
<p align="center">
<img src="https://imgur.com/D5NEYXr.png" height="80%" width="80%" alt="Active Directory"/>

- Results: Verify Address Lease to Client1 on Domain Controller.
 <p align="center">
<img src="https://imgur.com/STMkoaG.png" height="80%" width="80%" alt="Active Directory"/>
<br />


## Part 2: Apply and Configuring Group Policy Objects for Software Deployment (Google Chrome, Slack, Zoom and Microsoft Teams)
- Create Security Group "Software Deployment" under Users in AD, then add PC workstations as members (in this case, its "Client1"):
<p align="center">
<img src="https://imgur.com/H6uJWBd.png" height="80%" width="80%" alt="Active Directory"/>

- Make new file called "Software" with MSI installers files and share folders with "Authenticated Users". Give permission for "Full" control. Then add Security Group "Software Deployment" to security tab:
<p align="center">
<img src="https://imgur.com/wsAFBpN.png" height="80%" width="80%" alt="Active Directory"/>

- Create Group Policy Object in Domain, edit, go to Computer Configuration -> Policies -> Software Settings -> Software Installation. Then right click new packages on Software Installation, then find MSI installer files, open, then add them in:
<p align="center">
<img src="https://imgur.com/LIWFuVR.png" height="80%" width="80%" alt="Active Directory"/>

- Remove "Authenticated Users" in "Security Filtering", then add Security Group, "Software Deployment":
<p align="center">
<img src="https://imgur.com/orGCgEh.png" height="80%" width="80%" alt="Active Directory"/>

- Run "gpupdate /force" in Command Prompt, then login to workstation "Client1" with a created user to verify software:
<p align="center">
<img src="https://imgur.com/Ijct63B.png" height="80%" width="80%" alt="Active Directory"/>

- Continue placing Group Policies as you see fit.

- For Example: Enforce Windows Defender Firewall, Event Log, Local Polocies, Account Policy, Windows Settings, Network, Mapped Drives, and etc.
<br/>

<p align="center">
Example: Desktop Background Change.
<img src="https://imgur.com/KWQ6awT.png" height="80%" width="80%" alt="Active Directory"/>
<br />
<br />
 
 
<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
