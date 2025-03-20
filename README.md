<h1>Active Directory Lab</h1>

 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
This lab consists of a walkthrough for creating an Active Directory using Azure. Configuring and running this lab develops an understanding of how Active Directory and Windows networking work. ActiveDirectorymis asoftware built and maintained by Microsoft that allows for central management of thousands of user accounts, devices, and respective permissions ans access to resources. A familiar use case example is an active directory "Domain" at a university that all the computers are joined to, which  allows you to log into any computer on campus with your same user and password on the domain. So if a computer is joined to a domain, it is possible to log into that computer, by using a user account that is stored in Active Directory.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Diskpart</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Outline:</h2>

- A. Make a Resource Group in the Subscription inside the Tenant Organization, Azure
- B. Create a Virtual Network and Subnet
- C. Create 2 VMs (Windows Server [Domain Controller] & Windows 10 [Client])
- D. Create a Domain on the Windows Server Server (Domain Controller)
- E.Join the Windows 10 computer to the Domain (Providing it with access to all user accounts on the Domain)
- F. Confirm by logging into the Windows 10 computer using a domain user, which we will create)
- G. Use Powershell to automatically create users in bulk
- H. In the next repository, we will create network shares and assign permissions to them based on certain users and groups. Permission understanding in AD

<h2>Program walk-through:</h2>

<p align="center">

<img src="https://github.com/user-attachments/assets/30f43066-78a7-448f-9a22-eef5090994a6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Overview: The default DNS settings on the client, its NIC, will point to a DNS server managed by Microsoft. So for the client to join the domain, we will tell the client to use our domain controller, as the DNS server. To do this, we will set the Client's DNS IP address in its virtual NIC, to the IP address of the domain controller. Createva new virtual network utilizing this resource group, name the vnet name for example "Active-Directory-Vnet" in Ca
 <p>
 <br />
  - A. Make a Resource Group
  img
  For step by step directions to make a Resource group, see my repository [network and computing] perhaps. I'll name this resource group "Active-Directory" in canada
$Launch the utility: <br/>
 <br />
Select the disk:  <br/>
<img src="https://i.imgur.com/tcTyMUE.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Enter the number of passes: <br/>
<img src="https://i.imgur.com/nCIbXbg.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Confirm your selection:  <br/>
<img src="https://i.imgur.com/cdFHBiU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Wait for process to complete (may take some time):  <br/>
<img src="https://i.imgur.com/JL945Ga.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Sanitization complete:  <br/>
<img src="https://i.imgur.com/K71yaM2.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Observe the wiped disk:  <br/>
<img src="https://i.imgur.com/AeZkvFQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
