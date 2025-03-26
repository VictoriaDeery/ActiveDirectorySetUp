<h1>Active Directory Lab</h1>

 ### [YouTube Demonstration-available upon request]
 
<h2>Description</h2>
This lab consists of a walkthrough for creating an Active Directory using Azure. Configuring and running this lab develops an understanding of how Active Directory and Windows networking work. Active Directory is software built and maintained by Microsoft that allows for central management of thousands of user accounts, devices, and respective permissions and access to resources. A familiar use case example is an active directory "Domain" at a university that all the computers are joined to, which  allows you to log into any computer on campus with your same user and password on the domain. So if a computer is joined to the domain, it is possible to log into that computer by using a user account that is stored in Active Directory.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Azure</b>
- <b>Active Directory</b>
- <b>remote desktop</b>
- <b>Active Directory Users and Computers</b> 

<h2>Environments Used </h2>

- <b>Windows 10</b> (21H2)

<h2>Outline:</h2>

- Set up:
  - A. Make a Resource Group in the Subscription inside the Tenant Organization, Azure
  - B. Create a Virtual Network and Subnet
  - C. Create 2 VMs (Windows Server [Domain Controller] & Windows 10 [Client])
-  Install active directory on the domain controller & Create domain admin user
   - D. Create a Domain on the Windows Server Server (Domain Controller)
- Join Client to domain, allowing all users that exist in the domain to log into client-1
  - E.Join the Windows 10 computer to the Domain (Providing the Windows 10 computer with access to all user accounts on the Domain)
  - F. Set up Remote Desktop for non-administrative users on client-1, allowing other user accounts to log into client-1 using RDP.
  - G. Use Powershell to automatically create users/clients in bulk
  - H. Confirm by logging into the Windows 10 computer using a domain user, which we will create)

- Coming Up!: In the next repository, we will reset users' passwords, create network shares, and assign permissions to them based on certain users and groups for permission understanding in AD.

<h2>Program walk-through:</h2>

<p align="center">

<img src="https://github.com/user-attachments/assets/30f43066-78a7-448f-9a22-eef5090994a6" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
Overview: The default DNS settings on the client, its NIC, will point to a DNS server managed by Microsoft. So, for the client to join the domain, we will tell the client to use our domain controller as the DNS server. To do this, we will set the Client's DNS IP address in its virtual NIC to the IP address of the domain controller. (Often, the domain controller doubles as a DNS server, as it will in this lab.) 
 <p>
 <br />
- A & B Make a Resource Group and virtual network
<p>
 Create a new virtual network and resource group. I am calling the resource group "Active-Directory-Lab" and putting it in Canada. For step by step directions to make a Resource group, network, VM, and remote desktop, review my repository [network and computing]. I'll manually create the virtual network instead of letting the VM make it. I'll name this virtual network "Active-Directory-Vnet."
 <p>
  
- C. Create 2 VMs (Windows Server [Domain Controller] & Windows 10 [Client]); set up their NICs
<img src="https://github.com/user-attachments/assets/a6c8676e-cf0e-4850-8449-9fe80460d9bb" height="80%" width="80%" alt="Disk Sanitization Steps"/>


1. Create a domain controller named "dc-1" as a new VM in Azure, selecting the same resource group that was just made and the same region. For image, select "Windows server 2022". Select a size of 2 vCPUs. Create a username and password. Agree to licensing if needed. Select Next: Disks and Next: Networking. Then make sure the virtual network is the one just created, Active-Directory-Vnet. Select "review and create" and "create." 
<p>
 2. Now for the other VM, create it similarly, selecting the same resource group and region but name it "client-1" and for the image select "Windows 10 Pro", still 2vcpus, same username and password for the sake of this lab, and follow all subsequent steps for the previous vm creation.
 <p>
  3. Set the domain Controller's NIC Private IP address to be static. This is to make sure it doesn't get reassigned, as dc-1 is going to be both a server and acting as a DNS server. DC-1's private IP address needs to be static because Client-1 will use DC-1 as the DNS server. We will manually configure Client-1's DNS settings to use DC-1's private IP address. To do this, select the dc-1 VM. Then select "networking" on the left and "network settings" below that. Select dc-1's virtual Network Interface card, shown in the picture above. Now, under "name," select "ipconfig1," and a window will appear on the right side of the screen. On this window you can see "Private IP address settings" and "allocation" underneath that, select "static" and "save" so that dc-1's private IP address will not change, regardless of how many times the virtual machine is restarted, so the client using this server will still have access.
  <p>
   4. Log into the domain controller using its public IP address and remote desktop to disable the Windows firewall, just for connectivity testing. You should re-enable the firewall before using the VM otherwise. To do this, in Azure, go to VMs and get dc-1's public IP address. Use remote desktop to connect to dc-1, The server manager will load if you are in the domain manager dc-1 VM and made the right kind of VM. Inside your VM, if you right-click the start menu and select system, it should say "Windows Server 2022." Now, right-click the start menu and select"run," and type "wf.msc" for Windows Firewall. Click "Properties" on the right and for "firewall state" select "off" go through the tabs above to disbale firewall for the domain, private, and public profile, then select "apply" and "ok" and then close the window. 
   <p>
    5. Now set client-1's DNS settings on the NIC to dc-1's Private IP address. In Azure, select the dc-1 VM and copy its Private IP address (Likely 10.0.0.4). Then select the Client-1 VM ->Networking -> Network Settings -> Select "Network interface/ IP Configuration as shown in the picture above. Then to the left under the selected "IP Configurations" is "DNS Servers" select that. And instead of the default "inherit from virtual network" the Vnet DNS server, we will select "custom" and paste the dc-1's private IP address here. Now whenever the client computer looks up anything, it will look to dc-1 for it, allowing us to join the domain. select "save" above. From the Azure portal, restart the "client-1" VM to update the changes we've made, by selecting the box next to its name under VM's and selecting "restart" with the slightly curved arrow above.
  </p>
  6. Log into client-1 and ping dc-1's private IP address. Copy dc-1's private IP address from Azure. In the client-1 VM go to start and type and go to powershell and type ping followed by dc-1's private IP address so for example "ping 10.0.0.4" if you get the error "request timed out" the VMs are likely not in the same virtual network or dc-1's firewall is still blocking ping, so review the relevant steps. If it worked you will see messages saying "Reply from 10.0.0.4" with other information. In client-1, open PowerShell again and run "ipconfig /al,l" and the output for the DNS setting should show dc-1's private IP address. Meanwhile dc-1 is using the vnet DNS server.
 </p>

- D. Create a Domain on the Windows Server Server (Domain Controller)
<img src="https://github.com/user-attachments/assets/40932bee-5138-4c0d-a427-f9b613eb7bc1" height="80%" width="80%" alt="Disk Sanitization Steps"/>

  1. To install active directory on the domain controller, log into the VMs and on the dc-1 VM, install active directory domain services by clicking "start" then "server manager" canceling out any pop up window. Then go to "add roles and features" and click "next" twice. There should only be one server selection available, so click "next." For server roles, there should be checkboxes. Select "active directory domain services" and select "add features". Now you're back, so click "next" twice. Select "restart if required," "yes," and "install." Once installed, you may close the window.

 </p>
 2. Now we will  promote dc-1 as a domain controller by configuring the active directory that has been installed. This is called setting up a new forest, which we will call mydomain.com. In the dc-1 VM click the flag in the top right of the server manager. Select "promote this server to a domain controller" so the AD can become a domain controller. Then select "Add a new forest" and type "mydomain.com" or whatever you chose. Select "next" and then create a DSRM password. Select "next"'s but make sure "create DNS delegation" is unchecked and click "next"'s, note sometimes the NetBIOS page takes a few moments to load. Select "next" until you can select "install" and do so, so that the new forest can be installed and dc-1 can be turned into a domain controller. It will automatically restart. Use RDP to log back into dc-1 using its public IP address. But because the dc-1 VM is now a domain controller, we need to specify the context to which we want to log into it as. Since the domain holds all the users, we need to specify which user we want to log in as. When people log into dc-1 VM now, since the user accounts exist in a domain (now you must specify you are using this domain and this user, so for example, to specify the domain, do so in a backslash followed bywhichever user in that domain you want to log into, which right now is my username: "mydomain.com\labuser" The domain is a context that holds many users, however another context is local on the device itself. That is why you may need to specify the context that you want to log in as labuser on mydomain.com. Since dc-1 is now a domain controller on the domain, you must specify that you want to log on as a domain user, and you must specify which domain; otherwise, you may be logged in locally.
 <p>
<img src="https://github.com/user-attachments/assets/4bd59605-b292-4a5a-bf2e-3f2bccf6fb2d" height="80%" width="80%" alt="organizational unit creation"/>

  3. Let's create organizational units and a Domain Admin User within the domain (so we do not need to use the default username, which in this case is labuser)

 Open Active Directory Users and Computers and then create an organizational unit called "_EMPLOYEES." An organizational unit is similar to a folder for organizing and more. So we'll create an organizational unit for employees and one for admins and actually create a domain admin inside the admins folder. Domain Admins may impact thousands of users!

 3a. To do this, open the dc-1 computer, select start -> select "Windows administrative tools" with a folder to the left -> in its drop-down down select active directory and computers (not one of the other active directories). Select "mydomain.com" to expand the dropdown. The "users" will show many users, including your own. right-click "mydomain.com" -> new ->organizational unit-> name : _EMPLOYEES. and repeat the process, making another organizational unit and calling it _ADMINS. (Later, when we run the  script to create employees, it will look for this folder. Putting an underscore before the name puts our folders first if you right-click mydomain.com and refresh since its alphabetical, and this may be useful if there are many organizational units.
 <p>
<img src="https://github.com/user-attachments/assets/0b6624c6-32fe-4fc5-858e-00ffd0a8e937" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/user-attachments/assets/d4d74c12-56a1-40e5-888b-a7e5a97a3752" height="80%" width="80%" alt="Disk Sanitization Steps"/>

<img src="https://github.com/user-attachments/assets/27576f05-e8a7-4923-978f-3225d9e8f399" height="80%" width="80%" alt="Disk Sanitization Steps"/>
  3b. Let's create a new employee, Jane Doe, with the username of jane_admin and a password. right click the "_ADMINS" organizational unit we just made -> New -> User. I will deselect "user must change password at next login" for ease and create a password, and for the dake of the lab, you may select "password never expires." Although we named the user jane_admin and put her in the admin folder, we still need to give her admin permissions by adding her account to the built-in "Domain Admins" security group. To do this, right click jane's account -> properties -> member of -> add -> under "enter the object names to select," write "domain admins" and select "check names" to find it (if it does not exist, you will get the error "name not found"). Select "okay's" and "apply." Now Jane Doe is an actual admin who can create and manage users and more. Now, let's test it! Log out of dc-1 and login as Jane using mydomain.com\jane_admin as the username
  <p>
 
- Join Client to domain, allowing all users that exist in the domain to log into client-1
</p>
 - E.Join the Windows 10 computer to the Domain (Providing the Windows 10 computer with access to all user accounts on the Domain)
 <p>
  
<img src="https://github.com/user-attachments/assets/610afab8-3304-4b46-a149-702120f11bc0" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
 
</p>
  1. Make sure you have completed the earlier step of setting client-1's DNS settings to dc-1's Private IP address from the Azure portal and restarting client-1 and if you've followed along, this is already done. Log into client-1 using the original local admin (labuser)and join it to the domain by right-clicking the start menu ->system -> a window will pop up, to the right select "rename this PC advanced." Under the computer name tab, click "change" and under "member of" select "domain" and type your domain. Notice: Because we set the DNS settings on client-1 to use dc-1's private IP address, it can locate the domain controller (DC) for the mydomain.com domain, prompting a username and password instead of receiving the error "AD DC for this domain could not be contacted." Again, because mydomain.com is a DNS and its settings have been configured properly, it can locate the appropriate domain controller. Now log in, specifying the context via using mydomain.com\jane_admin as the username because she is a domain admin so she should have permissions to join the domain. Begin closing windows and restarting. When it restarts it has become a member of the domain! But let's make sure. Go to the domain and verify that client-1 is in there.
  <p>
<img src="https://github.com/user-attachments/assets/16a07c12-741c-4886-bd7a-cb3145bb72fb" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/user-attachments/assets/81595327-884d-4bdc-8f95-2f22d54c5092" height="80%" width="80%" alt="Disk Sanitization Steps"/>

  </p>
  2. Log into dc-1 as jane_admin.in the start search, search "active directory users and computers." expand "mydomain.com and select "computer" and you should see "client-1" double click it and you can view information about it like its a member of what group, and running what system. Make another operational unit called "_CLIENTS. "Go back to "computers," and then drag client-1 into _CLIENTS, saying yes to the pop-up warning. Right-click mydomain.com to refresh.
  <p>
 - F. Set up Remote Desktop for non-administrative users on client-1, allowing other user accounts to log into client-1 using RDP.
   <p>
    
    <p>
    <img src="https://github.com/user-attachments/assets/5d95f5b6-d8d7-4e0e-9d50-ab7994534564 height="80%" width="80%" alt="Disk Sanitization Steps"/>
<p>
 
</p>
    log into client-1 as jane_admin using mydomain.com\jane_admin as the username. Then, right-click the start menu -> system -> a window will pop up, select "remote desktop" on the left. Allow domain users to access the remote desktop by selecting "select users that can remotely access this PC" under "user accounts," then select "add". The computer can access the context of mydomain.com (for example, you can check again for "domain admins" now on this computer, and when you check, it will find it because you are connected to the domain. However, domain admins already have access to log in, so instead type "domain users," indicating all users in the domain will be allowed to log in to this computer. select "ok" and "Add." Normally, this step would be done with Group Policy, allowing you to change many systems at once. For more on Group Policy Objects (GPO) look to my future repository on active directory, a continuation of this lab.
    
  - G. Use Powershell to automatically create users/clients in bulk
   <p>
   
   <p>
<img src="https://github.com/user-attachments/assets/080e5f8f-d88e-4e82-b127-888a0264f875" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://github.com/user-attachments/assets/5f00bcc9-d2dd-4976-a57f-c7ebf4a719c5" height="80%" width="80%" alt="Disk Sanitization Steps"/>



  </p>
  1. Log into dc-1 as domain admin and open PowerShell Ise as admin by right-clicking it. copy the code from the file at the top of this repository titled "Generate-Names-Create-Users" then back in Powershell create a new file by clicking the first icon below "file", paste it in there and then type ctrl+s to save it to the desktop calling it "create-users" hit the green arrow to run the script to generate users.

  
 - H. Confirm by logging into the Windows 10 computer using a normal domain user, which we will create)
   <p>
<img src="https://github.com/user-attachments/assets/4dccc062-34a1-4659-9f95-25dd70af1436" height="80%" width="80%" alt="Disk Sanitization Steps"/>
 </p>
  1. Every user being created will have the specified "Password1" password unless you change that in the script. If you click into the OU "_EMPLOYEES" you will now see many users. Select a random user For example, I choose topuh.goto. (If you double-click a user and select "member of," you'll see that by default, they are a member of the domain users, which is the group we allowed remote access for in a previous step. Close client-1's connection with Jane Doe. Log in to client-1 with the username mydomain.com\topuh.goto, and if you open the command prompt, you will see there is a local profile on this computer for topuh.goto. And if you select file explorer -> this PC -> Windows (C:) drive -> users, you will see all users that logged in. You may sign out of client-1
   <p>
    
   </p>
- Coming Up!: In the next repository, we will reset users' passwords, create network shares, and assign permissions to them based on certain users and groups for permission understanding in AD.
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
Wait for the process to complete (may take some time):  <br/>
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
