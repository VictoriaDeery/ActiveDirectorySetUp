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

-Set up:
 - A. Make a Resource Group in the Subscription inside the Tenant Organization, Azure
 - B. Create a Virtual Network and Subnet
 - C. Create 2 VMs (Windows Server [Domain Controller] & Windows 10 [Client])
-  Install active directory on the domain controller & Create domain admin user
 -   D. Create a Domain on the Windows Server Server (Domain Controller)
- Join Client to domain, allowing all users that exist in the domain to log into client-1
 - E.Join the Windows 10 computer to the Domain (Providing the windows 10 computer with access to all user accounts on the Domain)
 - F. Set up Remote Desktop for non-administrative users on client-1, allowing other user accounts to log into client-1 using RDP.
 - G. Confirm by logging into the Windows 10 computer using a domain user, which we will create)
 - H. Use Powershell to automatically create users/clients in bulk
- Coming Up!: In the next repository, we will reset users passwords, create network shares, and assign permissions to them based on certain users and groups for permission understanding in AD.

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
   4. Log into the domain controller using its public IP address and remote desktop to disable the Windows firewall, just for connectivity testing. You should re-enable the firewall before using the the VM otherwise. To do this, in azure go to VMs, and get dc-1's public IP address. Use remote desktop to connect to dc-1, the server manager will load if you are in the domain manager dc-1 VM and made the right kind of VM. Inside your VM if you right click the start menu and select system, it should say "Windows Server 2022." Now, right click the start menu and select"run" and type "wf.msc" for Windows Firewall. Click "Properties" on the right and for "firewall state" select "off" go through the tabs above to disbale firewall for the domain, private, and public profile, then select "apply" and "ok" and then close the window. 
   <p>
    5. Now set client-1's DNS settings on the NIC to dc-1's Private IP address. In Azure, select the dc-1 VM and copy its Private IP address (Likely 10.0.0.4). Then select the Client-1 VM ->Networking -> Network Settings -> Select "Network interface/ IP Configuration as shown in the picture above. Then to the left under the selected "IP Configurations" is "DNS Servers" select that. And instead of the default "inherit from virtual network" the Vnet DNS server, we will select "custom" and paste the dc-1's private IP address here. Now whenever the client computer looks up anything, it will look to dc-1 for it, allowing us to join the domain. select "save" above. From the Azure portal, restart the "client-1" VM to update the changes we've made, by selecting the box next to its name under VM's and selecting "restart" with the slightly curved arrow above.
  </p>
  6. Log into client-1 and ping dc-1's private IP address. copy dc-1's private IP address from Azure. In the client-1 VM go to start and type and go to powershell and type ping followed by dc-1's private IP address so for example "ping 10.0.0.4" if you get the error "request timed out" the VMs are likely not in the same virtual network or dc-1's firewall is still blocking ping, so review the relevant steps. If it worked you will see messages saying "Reply from 10.0.0.4" with other information. In client-1 open PowerShell again and run "ipconfig /all" and the output for the DNS setting should show dc-1's private IP address. Meanwhile dc-1 is using the vnet DNS server.
 </p>

- D. Create a Domain on the Windows Server Server (Domain Controller)
- ![image](https://github.com/user-attachments/assets/40932bee-5138-4c0d-a427-f9b613eb7bc1)

  1. To install active directory on the domain controller, log into the VMs and on the dc-1 VM, install active directory domain services by clicking "start" then "server manager" canceling out any pop up window. Then go to "add roles and features" click "next" twice. There should only be one server selection available so click "next." For server roles, there should show checkboxes, select "active directory domain services" and select "add features". Now you're back, so click "next" twice. Select "restart if required" "yes" and "install." Once installed, you may close the window.

 -   D. Create a Domain on the Windows Server Server (Domain Controller)
 -   <p>
  
 </p>
 1. Now we will  promote dc-1 as a domain controller by configuring the active directory that has been installed. This is called setting up a new forest which we will call mydomain.com. In the dc-1 VM click the flag in the top right of ther server manager. select "promote this server to a domain controller" so the AD can become a domain controller. Then select "add a new forest" and type "mydomain.com" or whatever you chose. and select "next" and then create a DSRM password. select "next"'s but make sure "create DNS delegation" is unchecked and click "next"'s, note sometimes the NetBIOS page takes a few moments to load. select "next" until you can select "install" and do so, so that the new forest can be installed and dc-1 can be turned into a domain controller. It will automatically restart. Use RDP to log back into dc-1 using it public IP address. But because the dc-1 VM is now a domain controller, we need to specify the context to which we want to log into it as. Since the domain holds all the users, we need to specify which user we want to log in as. When people log into dc-1 VM now, since the user accounts exist in a domain (now you must specify you are using this domain and this user, so for example, to specify the domain, do so in a backslash followed bywhichever user in that domain you want to log into, which right now is my username: "mydomain.com\labuser" The domain is a context that holds many users, however another context is local on the device itself. That is why you may need to specify the context that you want to login as lab user on mydomain.com. Since dc-1 is now a domain controller on the domain, you must specify that you want to log on as a domain user, and you must specify which domain, otherwise you may be logged in locally.
 <p>
  2. Let's create a Domain Admin User within the domain (so we do not need to use the default username, which in this case is labuser)

  May impact all users
- Join Client to domain, allowing all users that exist in the domain to log into client-1
 - E.Join the Windows 10 computer to the Domain (Providing the windows 10 computer with access to all user accounts on the Domain)
 - F. Set up Remote Desktop for non-administrative users on client-1, allowing other user accounts to log into client-1 using RDP.
 - G. Confirm by logging into the Windows 10 computer using a domain user, which we will create)
 - H. Use Powershell to automatically create users/clients in bulk
- Coming Up!: In the next repository, we will reset users passwords, create network shares, and assign permissions to them based on certain users and groups for permission understanding in AD.
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
