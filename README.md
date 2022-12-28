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



<h2>Deployment and Configuration Steps</h2>

<h3>Step 1: Setup Resources in Azure</h3>

- Create two virtual machines
  - If you need help creating your virtual machines, please see my tutorial [here](https://github.com/miquelmanaois/virtualmachine)
    - 1st virtual machine will be the Domain Controller
       - Name: DC-1
       - Image: Windows Server 2022
       - Take note of the Virtual Network (Vnet) that gets created at this time
       - Set DC-1's virtual Network Interface Card (NIC) Private IP address to be static
           - Go to DC-1's network settings -> select networking -> select the link next to network interface -> IP Configurations -> ipconfig1 -> Assignment from dynamic to static (this ensures DC-1's IP address will not change)
    - 2nd virtual machine will be the Client
       - Name: Client-1
       - Image: Windows 10 Pro
       - Use the same resource group and Vnet as DC-1

<p align="center">
<img src="https://i.imgur.com/Cxy8NM7.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/f1eRIx4.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

<h3>Step 2: Ensure Connectivity between client and Domain Controller</h3>

- Login to Client-1 using Microsoft Remote Desktop
  - Search for command line and open it
  - ping DC-1's private IP Address
    - ping - t 822332229189838
      - Due to the firewall, the request is timing out. To fix this, we need to enable ICMPv4 on DC-1's local Windows firewall. 
- Login to DC-1 using Microsoft Remote Desktop
    - Start -> Windows Administrative Tools -> Windows Defender Firewall with Advanced Security -> Inbound rules.
    - Sort the list by protocols 
      - Find ICMPv4 and enable these two inbound rules.
- Log back into Client-1 and the Command line will start to ping DC-1 successfully.
    
<p align="center">
<img src="https://i.imgur.com/Cxy8NM7.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/f1eRIx4.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>


<h3>Step 3:  Install Active Directory</h3>

- Log back ito DC-1 
    - Open Server Manager
    - Select "Add roles and features." Follow the prompts.
    - At Server Roles, check "Active Directory Domain Services." Then select next Add Features. Select next and finish installing.
    - At the top right of the Server Manager Dashboard, click on the flag. 
        - Select Promote this server to a domain controller.
            - Select Add a new forest
                - Root domain name: mydomain.com
            - Select next
                - Create password
            - Select next, follow the prompts and finish up by selecting install. 
                - DC-1 will automatically restart.
-Log back into DC-1 as user: mydomain.com\labuser               



<p align="center">
<img src="https://i.imgur.com/Ed9wc2j.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/7ryNBQg.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>


<h3>Step 4: Create an Admin and Normal User Account in AD v1.15.8</h3>
     
- Download osTicket (download from within lab files: [link](https://drive.google.com/drive/u/0/folders/1APMfNyfNzcxZC6EzdaNfdZsUwxWYChf6))
- Right click on the file and select extract all
- Copy the “upload” folder INTO c:\inetpub\wwwroot
- Rename “upload” to “osTicket”


<p align="center">
<img src="https://i.imgur.com/QhE5p74.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/I5yvFc4.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>
 
     

<h3>Step 5: Join Client-1 to your domain (mydomain.com)
</h3>

- Search for Internet Information Services (IIS) and select open
- On the left, select Sites -> Default Website -> osTicket
- On the right, click “Browse *:80”
- Before continuing, head back to and open IIS.


<p align="center">
<img src="https://i.imgur.com/nOv1FP1.png" height="80%" width="80%" alt="Azure Free Account"/>

<h3>Step 6:  Setup Remote Desktop for non-adminitrative users on Client-1
</h3>

- Go back to IIS, Sites -> Default Web Site -> osTicket
- Double-click PHP Manager
- Click “Enable or disable an extension” at the bottom under “PHP Extensions”
- Right click and enable the following
    - php_imap.dll
    - php_intl.dll
    - Php_opcache.dll

 
     
 <p align="center">
<img src="https://i.imgur.com/14pPOdv.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/okabWbT.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

<h3>Step 7:   Create a bunch of additional users and attempt to log into client-1 with on of the users
</h3>

- Intl Extension should now have a green check mark next to it


<p align="center">
<img src="https://i.imgur.com/okabWbT.png" height="60%" width="60%" alt="Azure Free Account"/>



<h3>Step 8: Rename</h3>
 
- Open Windows Explorer and select C:-> inetpub-> wwwroot-> osTicket-> Include and rename.
	- From: C:\inetpub\wwwroot\osTicket\include\ost-sampleconfig.php
	- To: C:\inetpub\wwwroot\osTicket\include\ost-config.php


<p align="center">
<img src="https://i.imgur.com/rEBpL8Y.png" height="80%" width="80%" alt="Azure Free Account"/>

<h3>Step 9: Assign Permissions: ost-config.php</h3>

- Right click ost-config.php, 
- Open Properties -> Security -> Advanced -> Permissions 
- Select Disable inheritance -> Remove all inherited permissions from this object 
- Afterwards, Select add ->New Permissions -> Everyone -> All

<p align="center">
<img src="https://i.imgur.com/rEBpL8Y.png" height="80%" width="80%" alt="Azure Free Account"/>
  
<h3>Step 10: Continue Setting up osTicket in the browser</h3>

- Go back to browser and click continue
  - Name: Helpdesk
  - Email: whichever email you want
  
<p align="center">
<img src="https://i.imgur.com/rEBpL8Y.png" height="80%" width="80%" alt="Azure Free Account"/>

<h3>Step 11: Download and Install HeidiSQL</h3>

- Open HeidiSQL and create new session
   - User: root
   - Password : Password1
- Select Open
- On the left side, right click “Unamed” -> “Create New” -> “Database
- Name it “osTicket”

 <p align="center">
<img src="https://i.imgur.com/14pPOdv.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/okabWbT.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

<h3>Step 12:  Go back to the browser and continue setting up osTicket by filling out the fields.</h3>

- First Name: your first name
- Last Name: your last name
- Email Address: whichever email you want (needs to be different from the Default Email)
- Username: user_admin 
- Password: Password1 
- MySQL Database: osTicket (the one you just created in HeidiSQL)
- MySQL Username: root
- MySQL Password: Password1
- Finally, click Install Now

 <p align="center">
<img src="https://i.imgur.com/14pPOdv.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/okabWbT.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>

🎉Congratulations! You have sucessfully installed osTicket!🎉

<h3>Step 13: Cleanup.</h3>

- Go to C: -> inetpub->wwwroot->osTicket->setup
    - Delete the contents in the setup folder
    - Afterwards, delete the setup folder
- Go to C:-->Inetpub-->wwwroot-->osTicket-->include
    - Right click on ost-config.php 
    - Select securities -> Advanced -> edit to change permissions
	- Allow everyone to only have read and execute
	
 <p align="center">
<img src="https://i.imgur.com/14pPOdv.png" height="70%" width="70%" alt="Azure Free Account"/> <img src="https://i.imgur.com/okabWbT.png" height="70%" width="70%" alt="Azure Free Services"/>
</p>	


