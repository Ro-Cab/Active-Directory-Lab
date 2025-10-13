# :jigsaw: Active-Directory-Lab

<h2>:memo: Description</h2>

This project demonstrates the setup of a complete Active Directory lab environment using Oracle VirtualBox. The lab simulates a real-world network, showcasing domain creation, user management, and network services configuration.
<br /><br />
The lab begins by installing Oracle VirtualBox and downloading Windows Server 2019 and Windows 10 ISOs to create two virtual machines. The Server 2019 VM acts as the Domain Controller, configured with two network adapters, one for internet access and one for the internal network. After assigning IP addresses, Active Directory Domain Services (AD DS) is installed, a new domain is created, and routing and DHCP are configured to allow client connectivity and automatic IP assignment. A PowerShell script is then used to bulk-create 1,000 users in Active Directory. Finally, a Windows 10 client VM is created, joined to the domain, and tested using one of the newly created domain accounts.
<br />

<h2>:small_blue_diamond: Overview</h2>
Illustrated below, this virtual lab uses Windows Server 2019 as the domain controller (DC) with IP 172.16.0.1, acting as both the DHCP and DNS server for internal clients (Windows 10) on the 172.16.0.x subnet. The DC provides Active Directory Domain Services and DHCP (range 172.16.0.100-200), while its other network interface connects to the internet via home router DHCP, enabling NAT and internet access for internal clients
<img width="1813" height="1079" alt="image" src="https://github.com/user-attachments/assets/346829c6-6946-4ac8-aa56-92f59b033071" />

<h2>:toolbox: Languages and Utilities Used </h2>

- <b>PowerShell</b>        - User account automation
- <b>Active Directory </b> - Directory and identity management
- <b>Oracle VirtualBox</b> - Virtualization software
- <b>DNS / DHCP / NAT</b>  - Core network services
    
<h2>:globe_with_meridians: Environments Used </h2>

- <b>Windows 10</b>           - Client machine
- <b>Windows Server 20219</b> - Domain Controller and AD services

<h2>:clapper: Program walk-through:</h2>

<p align="center">
🧩 Step 1 – Install VirtualBox and Extension Pack
Stared by downloading and installing Oracle VirtualBox on my host computer. Once installed, added the Extension Pack to enable USB, RDP, and advanced network support.
 
Showing the Oracle VM an Extension Pack download
💾 Step 2 – Download Windows ISOs
Obtained the Windows Server 2019 ISO and Windows 10 ISO from Microsoft’s official evaluation or download site. (Note: For Windows 10, I needed to download the installation media and then choose Windows 10 -> save as ISO) 
Stored them together in an easy-to-find folder (Desktop) so I could attach them during VM creation.
 Download page for the Windows Server 2019 & Windows Media Creation Tool

🖥️ Step 3 – Create the Domain Controller VM
Launched VirtualBox, created a new VM named DC, and used the Windows Server 2019 iso as the image. Made sure to set the OS edition to Desktop Experience or else I would not have a GUI and can only navigate using CLI.

Set the type to “Windows (64-bit)” and assign at least 2 GB of RAM. Left the rest of the settings as is and clicked Finish. 
I then right-clicked on the VM. Went to Network -> Adapter 1 & Adapter 2 
•  Adapter 1: NAT (for Internet access)
•  Adapter 2: Internal Network (for communication with other VMs)
This configuration allowed my virtual domain to connect internally while still reaching the Internet.

 
Setup Options to use
 Enabling Adapter 2 and Setting to Internal Network 

⚡ Step 4 – Install Windows Server 2019 & Guest Additions
Double-clicked the VM to enter Windows Server 2019 setup.
Chose “Standard (Desktop Experience)” for a GUI interface.
Completed the installation and set the Administrator password to Password123! (for lab purposes).
Logged once setup finished. 

 
Windows Server 2019 Setup Wizard
 
Click Input -> Keyboard -> Ctrl + Alt + Delete to access the login screen

🧱 Step 5 – Install Guest Additions
After the OS booted, inserted Guest Additions from the VirtualBox “Devices” menu.
Ran the AMD64 installer to improve mouse control, video scaling, and shared clipboard functionality.
Rebooted when finished for smoother operation.
 
🌐 Step 6 – Configure IP Addressing and Rename Server
Within the VM, opened Network Connections and identified both adapters.
1. Identified the adapter with the 10.0.2.15 IP address. We can tell that this one is connected to the internet.
2. We know that the adapter that has the 169.254.122.127 is the Internal NIC because that is an APIPA address. This means that this adapter was searching for a DHCP server to assign an address; however, it was not configured yet, so it was assigned an APIPA address. Renamed to INTERNAL so I’d know it will be used for the internal network.
For the Internal Network adapter, manually assigned:
•	IP Address: 172.16.0.1
•	Subnet Mask: 255.255.255.0
•	No need to assign a Default Gateway since the server will act as it.
•	DNS: 127.0.0.1  (Assigned loopback since we installed AD DS, and the server uses itself)
Rename the computer to DC and restart.
(Screenshot: Network Adapter Properties window)
 
We can tell which adapter is which based on the preconfigured IP
 
Renaming the INTERNAL adapter
 
Renaming the PC________________________________________
🏗️ Step 7 – Install Active Directory Domain Services
Opened Server Manager > Add Roles and Features and installed Active Directory Domain Services (AD DS).
After installation, promoted the server to a Domain Controller by adding a new forest named mydomain.com.
Set the Directory Services Restore Mode password (used
 Password123!).

 
Installing AD Domain Services
 
Promoting to Domain Controller by Clicking the yellow flag and setting the domain name

 	
 	
👤Step 8 – Create a Domain Admin Account
Logged back in as newly created Admin user from prev step.
Opened Active Directory Users and Computers.
Created a new Organizational Unit called “MyAdmins.”
Inside it, created a user (for example, a-rcabral) and set its password to Password123!.
Added this user to the Domain Admins group.
Logged out of the Administrator account and log back in using your new domain admin credentials.
 
Newly created user and joined Domain Admins
 
Signed in using the newly created user________________________________________

🔁 Step 9 – Configure NAT and Routing
Installed the Remote Access role and enable Routing and Remote Access
Selected Network Address Translation (NAT) and chose the external (NAT) adapter as the public interface.
This step allows internal network clients to access the Internet through the Domain Controller.
 
Installing the Remote Access role 
 
Configuring NAT on the INTERNET adapter
________________________________________
📡 Step 10 – Set Up DHCP
Installed the DHCP Server role via Server Manager.
Created a new scope using:
•	Start IP: 172.16.0.100
•	End IP: 172.16.0.200
•	Subnet Mask: 255.255.255.0
Set the Router Option to 172.16.0.1 (Domain Controller IP), we use this because we configured RAS / NAT on the DC so its job is to forward traffic from client to the internet
Authorized and activated the scope.
 
DHCP configurations and end result
________________________________________
🌎 Step 11 – Enable Internet Access on the Server
In Server Manager, opened Local Server Properties and disable “Internet Explorer Enhanced Security Configuration.”
This allowed browsing the internet without continuous pop-ups prompting for confirmation
 
Disabling IE Enhanced Security Configuration
________________________________________
🧠 Step 12 – Create Users with PowerShell
Downloaded the provided PowerShell script and extracted it to the Desktop.
Edited the names.txt file by adding my own name at the top.
Opened PowerShell ISE as Administrator and ran:
Set-ExecutionPolicy Unrestricted
Then opened the script, pointed to the folder location, and executed it.
The script creates 1,000 user accounts automatically inside a new “_Users” OU, each with username format firstinitiallastname and password Password1.
 
PowerShell ISE showing script execution)
 
New OU and Users have been created
________________________________________
🧮 Step 13 – Create the Windows 10 Client VM
Created a new VirtualBox VM named Client1, set OS type to Windows 10 (64-bit), and assign 2–4 GB RAM.
Attached the Windows 10 ISO, and configure its single network adapter to Internal Network so it connects to the lab’s private network.
 
Using Internal Network to get DHCP address from DC
________________________________________
💽 Step 14 – Install Windows 10 Pro
Booted the VM and installed Windows 10 Pro (not Home, since Home editions can’t join a domain).
Create a local user called “User” and skip adding a Microsoft account.
Once installed, verified the client received a DHCP address in the 172.16.0.x range by running ipconfig.
 
Command Prompt showing ipconfig output, DFG, DNS, DHCP from DC
 
Successful pings indicate proper config
________________________________________
🧾 Step 15 – Rename and Join the Domain
Opened System Properties > Computer Name > Change.
Renamed the machine to Client1 and join the domain mydomain.com.
Provided domain credentials when prompted (admin or regular domain user).
Restarted to complete the domain join.
 
Domain join confirmation
________________________________________
🔐 Step 16 – Log In with a Domain Account
After restart, chose Other User at login.
Entered one of the new domain usernames (for example rcabral) with the password Password1.
Successfully signed in and loaded a profile connected to the domain.

This verified that AD, DHCP, DNS, and NAT are working correctly.
 
.\ shows PC name
 
Successfully able to login with created user
 
Computer joined to domain and IP leased, able to login with any user in _USERS OU

</p>
