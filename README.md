<h1>Active Directory Lab</h1>

 ### [YouTube Demonstration](https://youtu.be/7eJexJVCqJo)

<h2>Description</h2>
This lab consists of a walkthrough for creating an Active Directory using Azure. Configuring and running this lab develops an understanding of how Active Directory and Windows networking work. ActiveDirectorymis asoftware built and maintained by Microsoft that allows for central management of thousands of user accounts, devices, and respective permissions and access to resources. A familiar use case example is an active directory "Domain" at a university that all the computers are joined to, which  allows you to log into any computer on campus with your same user and password on the domain. So if a computer is joined to the domain, it is possible to log into that computer by using a user account that is stored in Active Directory.
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
- E.Join the Windows 10 computer to the Domain (Providing the windows 10 computer with access to all user accounts on the Domain)
- F. Confirm by logging into the Windows 10 computer using a domain user, which we will create)
- G. Use Powershell to automatically create users/clients in bulk
- H. In the next repository, we will create network shares and assign permissions to them based on certain users and groups for permission understanding in AD.

<h2>Program walk-through:</h2>

<p align="center">

<img src="https://github.com/user-attachments/assets/30f43066-78a7-448f-9a22-eef5090994a6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Overview: The default DNS settings on the client, its NIC, will point to a DNS server managed by Microsoft. So for the client to join the domain, we will tell the client to use our domain controller, as the DNS server. To do this, we will set the Client's DNS IP address in its virtual NIC, to the IP address of the domain controller. (often the domain controller doubles as a DNS server, as it will in this lab.) 
 <p>
 <br />
  - A & B Make a Resource Group and virtual network
<p>
 Create a new virtual network and resource group. I am calling the resource group "Active-Directory-Lab" and putting it in Canada. For step by step directions to make a Resource group, network, VM, and remote desktop, review my repository [network and computing]. I'll manually create the virtual network instead of letting the VM make it. I'll name this virtual network "Active-Directory-Vnet."
 <p>
  
- C. Create 2 VMs (Windows Server [Domain Controller] & Windows 10 [Client]);set up their NICs
<img width="478" alt="image" src="https://github.com/user-attachments/assets/a6c8676e-cf0e-4850-8449-9fe80460d9bb" />


1. Create domain controller named "dc-1" as a new VM in Azure, selecting the same resource group that was just made, and the same region. For image select "Windows server 2022". Select a size of 2 vcpus. create a username and password. Agree to licensing if needed. select Next: Disks and Next: Networking. Then make sure the virtual network is the one just created, Active-Directory-Vnet. select "review and create" and "create." 
<p>
 2. Now for the other VM, create it similarly, selecting the same resource group and region but name it "client-1" and for the image select "Windows 10 Pro", still 2vcpus, same username and password for the sake of this lab, and follow all subsequent steps for the previous vm creation.
 <p>
  3.Set the domain Controller's NIC Private IP address to be static. This is to make sure it doesn't get reassigned as dc-1 is going to be both a server and acting as a dNS server. dc-1's private IP address needs to be static because client-1 will use dc-1 as the DNS server. We will manually configure client-1's DNS settings to use dc-1's private IP address. To do this, select the dc-1 VM. Then select "networking" on the left and "network settings" below that. Select dc-1's virtual Network Interface card, shown in the picture above. Now under "name" select "ipconfig1" and a window will appear from the right side of the screen. On this window you can see "Private IP address settings" and "allocation" underneath that, select "static" and "save" so that dc-1's private IP address will not change, regardless of how many times the virtual machine is restarted, so the client using this server will still have access.
  <p>
   4. Log into the domain controller using its public IP address and remote desktop, and disable the Windows firewall, just for connectivity testing. You should re-enable the firewall before using the the VM otherwise. To do this, in azure go to VMs, and get dc-1's public IP address.
  </p>
 </p>

- D. Create a Domain on the Windows Server Server (Domain Controller)
  

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
