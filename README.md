# configure-ad

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Computer)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create 2 Virtual machines in Azure:
  - Domain Controller (DC-01) (Windows Server 2022)
  - Client-1 (Windows 10)
- Ensure Connectivity between the Client-1 and Domain Controller (DC-01)
  - Enable ICMPv4 (ECHO) in on the local windows Firewall (DC-01)
- Install Active Directory Domain Services from (DC-01)
- Create an Admin and Normal User Account in Active Directory
- Join Client-1 to your Domain
- Setup Remote Desktop for non-admin users on Client-1
- Create additional users and attempt to log into Client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

1)Create a Domain Controller (DC-01)

 - In the Search Box at the top header, type and select "Virtual machines".
 - Click "Create", then select "Azure virtual machine".

<img src="https://i.imgur.com/Y4hI9Fw.jpg.png" height="50%" width="80%" alt="Disk Sanitization Steps"/>

 - Name your Virtual Machine anyway you want (for this example i am using DC-01)
   - Resource Group is automatically given a name when naming the Virtual Machine, but you can change it if you wish for this example i am using (AD_lab)
 - Change the Region that best suites your location (for this example use (US) West US 3).
 - Change the Image to a Windows Server, as this will become our DC-01 for this example use (Windows Server 2022 Datacenter)
 - Make sure the Size is adequate enough to run this server for this example use (Standard_E2s_v3 - 2 vcpus, 16 GiB memory).
 - Create a username and password of your choice for this example use (labuser).
 - Skip everything else and click "Review + create".
   - IF there is a Licensing Checkbox at the end, make sure that is CHECKED!
 - If Validation passed, click "Create".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

2)Create a Client VM (Client-1)

 - Follow the same steps as before for creating a virtual machine.
   - However, the Resource Group should be assigned as the same for the Domain Controller VM (for this example use AD_lab).
   - Use a different virtual machine name (for this example use Client-1).
   - Change the Administrator Account credentials to differentiate the two VMs (for this example use chris).
 - Change the Image to a Windows OS for this example use (Windows 10 Pro, version 22H2 - x64 Gen2).
 - Once done, click "Next" until you reach "Networking" (OR you can simply click the Networking tab at the top).

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Make sure that the "Virtual network" is set to the Vnet that the Domain Controller VM automatically created for this example use (DC-01-vnet).
 - Select the dropdown box for Subnet and confirm the default selection.
   - Sometimes you will have to manually set the Subnet, otherwise it will not let you proceed.
 - Skip everything else and click "Review + create".
 - If Validation passed, click "Create".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

3)Assign Domain Controller's Private IP address to "STATIC".

 - Later in this demo, we'll need users to login using a domain name instead of their standard username, so we'll have to make sure the Domain Controller's NIC Private IP address doesn't get changed in the future:
 - From Azure Portal, go to DC-01 VM Overview page.
 - Click on "Networking", then click on the "Network Interface" for this example it is (dc-).

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Click on "IP configurations" on the left.
 - You can see that the Private IP address is "Dynamic".
   - Click on "ipconfig1".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Change the Assignment at the bottom to "Static".
 - Click "Save".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

4)Ensure Connectivity between the Client and Domain Controller

 - Login to both the DC-1 and Client-1 VMs with Remote Desktop
  - In Azure Portal, go to any VM Overview page (for this example start with Client-1 VM).
  - COPY its public IP address (located on the right side).

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Press the Windows Key/Button, type and select "Remote Desktop Connection".
 - Input the virtual machine's Public IP Address and click Connect.
 - Enter the username and password, then click OK.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - A prompt will appear about the identity cannot be verified; just press "YES".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Minimize the Virtual Machine(Client-1)and login to the other VM (DC-01).

<img src="https://i.imgur.com/vqZGI0l.jpg.png" height="40%" width="80%" alt="Disk Sanitization Steps"/>

 - Return to Azure Portal,go to vm overview page and get (DC-01) public ip address.
 - Press the Windows Key/Button, type and select "Remote Desktop Connection".
   - for a 2nd (RDC)
 - Input the virtual machine's Public IP Address and click Connect.
 - Enter the username and password, then click OK.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Once login you should see the Service Manager page.

<img src="https://i.imgur.com/8gJ02Zg.png.png" height="40%" width="80%" alt="Disk Sanitization Steps"/>

 - On the DC-1 VM, press the Windows Key/Button, then type and select "Windows Defender Firewall with Advanced Security".

<img src="https://i.imgur.com/fbd49FX.png.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Click "Inbound Rules" (on the left sidebar).
 - Find the two names "Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)" (easier to sort by Protocol).
 - Select them both, then click "Enable Rule" on the right sidebar (or right-click, select).
   - Make sure that you enable ICMPv4, and NOT ICMPv6!

<img src="https://i.imgur.com/J1PuP0e.jpg.png" height="50%" width="100%" alt="Disk Sanitization Steps"/>

 - Once enabled, return to the DC-01 VM Overview page in Azure.
 - COPY the Private IP Address.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - With that copied, go into the Client-1 VM.
 - Press the Windows key (or Start Button), the type and select CMD or "Command Prompt" (you can run as Admin if desired).

<img src="https://i.imgur.com/rdxkUFs.jpg.png" height="10%" width="50%" alt="Disk Sanitization Steps"/>

 - Inside the Command Prompt, type "ping -t {DC-01 Private IP Address}" (for this example use IP address 10.0.0.4)
   - This will infinitely sent data packets for response to the DC-01 VM.
 - This confirms if the Client-1 VM can see the DC-1 VM successfully, otherwise you'll recieve a "Request Timed Out" message.
 - You can either press "Ctrl+C" to stop the ping process, OR you can simply close the Command Prompt.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

5)Install Active Directory Domain Services within DC-01 VM

 - Login to DC-1 VM and open "Server Manager" (if not open already).
 - Click on "Add Roles and Features" (2nd) on the front page.

<img src="https://i.imgur.com/h9ODygs.jpg.png" height="40%" width="50%" alt="Disk Sanitization Steps"/>

 - Keep clicking "Next" until you reach Server Roles tab.
 - Checkmark "Active Directory Domain Services", and prompt will appear:
 - Click "Add Features".
 - Keep clicking "Next" until the Confirmation tab.
 - Click "Install" then close once completed.

<img src="https://i.imgur.com/Ds5Da2H.png.png" height="40%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/eGcl2w1.png.png" height="40%" width="50%" alt="Disk Sanitization Steps"/>

 - Back on the Server Manager, click on the flag icon with a caution symbol on it (located at top-right header).
 - Click "Promote this server to a domain controller"
   
<img src="https://i.imgur.com/c6KbBfH.jpg.png" height="60%" width="60%" alt="Disk Sanitization Steps"/>

 - In the Deployment Configuration tab, select "Add a new forest".
 - Type any domain name you wish to use for this example use ("mydomain.com")
 - Click "Next".

<img src="https://i.imgur.com/jAkzCLH.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>

 - Create a password of your choice.
 - Keep clicking "Next" until the "Install" option is enabled, then click "Install".
   - Installing will result in restarting the DC-01 VM.
  
<img src="https://i.imgur.com/u6mMwAd.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/XMfQZ7Y.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DjdAibt.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>

 - Once completed, log back into the DC-01 VM
   - However, you should not be able use the same username as before, now it will require the domain name.
 - Select "More Choices", then click "Use a different account".
 - Change the username to add the domain name at the beginning of the original username for this example use ("mydomain.com\labuser").

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

6)Create an Admin Account in Active Directory

 - On the DC-01 VM, in Server Manager, click on "Tools" on the top-right header.
 - Click "Active Directory Users and Computers"

<img src="https://i.imgur.com/1wo2D3b.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>

 - For this demo, we are going to create 2 new folders within mydomain.com also known as ("Organizational Unit")
   - Right-click "mydomain.com" on the left sidebar.
   - Hover "New", then click "Organizational Unit".
  
<img src="https://i.imgur.com/wev5s3u.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>

 - Name one _EMPLOYEES and the other _ADMINS.

<img src="https://i.imgur.com/fgvAiQD.jpg.png" height="70%" width="60%" alt="Disk Sanitization Steps"/>

 - Next, we'll add a new Admin user account inside the _ADMINS folder.
   - Right-click on _ADMINS(or any empty space within the folder).
   - Hover "New", then click "User".

<img src="https://i.imgur.com/QPqgrXL.jpg.png" height="50%" width="60%" alt="Disk Sanitization Steps"/>

 - Create a first and last name, as well as a login name for this Admin user, then click "Next" for this example use (Jane Doe / jane_admin)
 - Create a password of your choice for that account.
 - Uncheck "User must change password at next login".
 - Checkmark "Password never expires".
 - Click "Next" until the account is created.

<img src="https://i.imgur.com/wMRlKWC.jpg.png" height="40%" width="80%" alt="Disk Sanitization Steps"/>

 - The user account is only inside a folder named _ADMINS, but that doesn't mean it has the privileges as one, so:
   - Right-click on the new account, then click "Properties".

<img src="https://i.imgur.com/X50OUJt.jpg.png" height="40%" width="50%" alt="Disk Sanitization Steps"/>

 - Click on the "Member Of" tab, then click the "Add" button.
 - Type in the word "domain", then click "Check Names", allowing to view all already built-in groups.
 - Select "Domain Admins", then "OK".
 - Click "Apply", then "OK" again.

<img src="https://i.imgur.com/aHRziYK.jpg.png" height="70%" width="70%" alt="Disk Sanitization Steps"/>

 - Once completed, logoff of DC-01 VM and login to the newly created admin account with the domain name (mydomain.com\jane_admin).
   - We use jane_admin account from now on instead of "labuser".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

7)Join Client-1 vm to the domain (mydomain.com)

 - From Azure Portal, go to Client-1 VM Overview page.
 - Click on "Networking", then click on the "Network Interface for this example use (client-).

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Go to "DNS servers" on the left sidebar.
 - Select "Custom" option under DNS servers.
 - Input the DC-1 Private IP Address (for this example use 10.0.0.4).
 - Click "Save".
 - Restart Client-1 VM.
   - You can also press the Restart button in the Client-1 VM Overview page.
   - Login again to Client-1 VM.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - On Client-1 VM, Right-click the Windows Button and select "System".

<img src="https://i.imgur.com/C6RAJoo.jpg.png" height="30%" width="40%" alt="Disk Sanitization Steps"/>

 - Click on "Rename this PC (advanced)"

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Within the System Properties window, click "Change".
   - This will allow us to use the domain name connected to the Domain Controller.
 - Under Member Of, select "Domain" option, then type your domain name (mydomain.com).
 - Then click "OK".
   - A login prompt will appear.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Enter the Admin user's logon credentials (mydomain.com\jane_admin).
 - Click "OK".
 - If done correctly, you should see a welcoming window appear to joining the domain.
 - Click "OK" again and you will be required to restart.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Use Remote Desktop (RDP) to login to the Client-1 VM.
 - Click "Use a different account", for now we will login using the jane_admin account.
   - The admin account is already logged onto the DC-01 VM, but this time we are logging in through the Client-1 VM.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

8)Setup Remote Desktop for non-administrative users on Client-1

 - Right-click the Windows Button and select "System", then click "Remote Desktop".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - At the bottom, click "Select users that can remotely access this PC".

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Next, click "Add".
 - Type in "Domain_Users" into the Object names box.
 - Once assigned, click "OK" and "OK" again.
 - This now makes Client-1 as a normal, non-administrative user.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

9)Create additional users and attempt to log into Client-1 with one of them:

 - Login to DC-01 as your admin account, if not already use ("jane_admin").
 - Press the Windows Key/Button and type "PowerShell".
   - Right-click on PowerShell_ISE and select Run as administrator.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - At the top menu, click on "New Script"
 - Using a premade script, copy and paste the code into the box.
   - https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1
 - When ready, at the top menu, click the "Run" button (green play button icon).
   - This script will create 1000's of  accounts using the password "Password1" (variables set at beginning of code). These accounts will be placed in its set path: _EMPLOYEES, listed near the end of the code.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Open "Active Directory Users and Computers" (from the Server Manager or Windows search)
 - Reveal mydomain.com, then reveal _EMPLOYEES folder.
   - You should see all of the randomly created user accounts in this folder.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Next, we're going to attempt to login one of those random users to Client-1 VM.
 - Copy any randomly created user(for this example i used   ).
 - Logoff of any account currently on Client-1 VM, and attempt to login with the random user.
   - Remember to start with the domain name before the username.

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

 - Whether it's failing at logging into accounts, resetting a password, or protecting against dangerous actors, there will always be a need for assistance to access one's account.
 - Open "Active Directory Users and Computers"
 - Reveal mydomain.com, then reveal _EMPLOYEES folder.
 - Double-click the user's account (this example uses ) to access the properties.
   - With this you can unlock accounts, reset passwords, and more!

<img src="https://i.imgur.com/DJmEXEB.png" height="50%" width="50%" alt="Disk Sanitization Steps"/>

!!!Complete!!!




