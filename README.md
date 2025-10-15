# :jigsaw: Active-Directory-Lab

<h2>:memo: Description</h2>

This project demonstrates the setup of a complete Active Directory lab environment using Oracle VirtualBox. The lab simulates a real-world network, showcasing domain creation, user management, and network services configuration.
<br /><br />
The lab begins by installing Oracle VirtualBox and downloading Windows Server 2019 and Windows 10 ISOs to create two virtual machines. The Server 2019 VM acts as the Domain Controller, configured with two network adapters, one for internet access and one for the internal network. After assigning IP addresses, Active Directory Domain Services (AD DS) is installed, a new domain is created, and routing and DHCP are configured to allow client connectivity and automatic IP assignment. A PowerShell script is then used to bulk-create 1,000 users in Active Directory. Finally, a Windows 10 client VM is created, joined to the domain, and tested using one of the newly created domain accounts.
<br />

<h2>:small_blue_diamond: Overview</h2>
Illustrated below, this virtual lab uses Windows Server 2019 as the domain controller (DC) with IP 172.16.0.1, acting as both the DHCP and DNS server for internal clients (Windows 10) on the 172.16.0.0/24 subnet. The DC provides Active Directory Domain Services and DHCP (range 172.16.0.100-200), while its other network interface connects to the internet via home router DHCP, enabling NAT and internet access for internal clients
<img width="1813" height="1079" alt="image" src="https://github.com/user-attachments/assets/346829c6-6946-4ac8-aa56-92f59b033071" />

<h2>:toolbox: Languages and Utilities Used </h2>

- <b>PowerShell</b>        - User account automation
- <b>Active Directory </b> - Directory and identity management
- <b>Oracle VirtualBox</b> - Virtualization software
- <b>DNS / DHCP / NAT</b>  - Core network services
    
<h2>:globe_with_meridians: Environments Used </h2>

- <b>Windows 10</b>           - Client machine
- <b>Windows Server 20219</b> - Domain Controller and AD services

<p align="center"><em>Thanks to <a href="https://www.youtube.com/c/JoshMadakor">Josh Madakor</a> for providing script/walkthrough.</em></p>


<h2>:clapper: Program walk-through:</h2>

<div align="center">

## 🧩 Step 1 - Install VirtualBox and Extension Pack  
Started by downloading and installing Oracle VirtualBox on my host computer. Once installed, I added the Extension Pack to enable USB, RDP, and advanced network support.  

<img width="2018" height="851" alt="image" src="https://github.com/user-attachments/assets/aa8aadb0-399b-43ed-969c-329c24e1c048" />

*Oracle VM and Extension Pack download page*

---

## 💾 Step 2 - Download Windows ISOs  
Obtained the Windows Server 2019 ISO and Windows 10 ISO from Microsoft’s official download site. (Note: For Windows 10, I needed to download the installation media and then choose Windows 10 -> save as ISO)  
Stored them together in an easy-to-find folder (Desktop) so I could attach them during VM creation.  

<img width="2018" height="911" alt="image" src="https://github.com/user-attachments/assets/b5bc3230-d045-4b41-94fa-53259d2827f5" />

*Download page for the Windows Server 2019 & Windows Media Creation Tool*  

---

## 🖥️ Step 3 - Create the Domain Controller VM  
Launched VirtualBox, created a new VM named DC, and used the Windows Server 2019 iso as the image. Made sure to set the OS edition to Desktop Experience or else I would not have a GUI and can only navigate using CLI.  

Set the type to “Windows (64-bit)” and assign 4 GB of RAM. Left the rest of the settings as is and clicked Finish.  
I then right-clicked on the VM. Went to Network -> Adapter 1 & Adapter 2  

**- Adapter 1:** NAT (for Internet access)  
**- Adapter 2:** Internal Network (for communication with other VMs)  
This configuration allowed my virtual domain to connect internally while still reaching the Internet.  

<img width="2069" height="620" alt="image" src="https://github.com/user-attachments/assets/26ac7bad-370f-49a1-98a2-68c9ddb0110b" />

*Setup options to use*  

<br> 
<img width="1943" height="1062" alt="image" src="https://github.com/user-attachments/assets/44ffaeca-8d75-4af0-adfd-34915f823149" />

*Enabling Adapter 2 and setting to Internal Network*  

---

## ⚡ Step 4 - Install Windows Server 2019 & Guest Additions  
Double-clicked the VM to enter Windows Server 2019 setup.  
Chose **“Standard (Desktop Experience)”** for a GUI interface.  
Completed the installation and set the Administrator password to Password123! (for lab purposes).  

Logged in once setup finished.  

<img width="1324" height="1014" alt="image" src="https://github.com/user-attachments/assets/c4c0b60d-e186-4be1-9157-de0291e85361" />

*Windows Server 2019 Setup Wizard*  

<br> 

<img width="826" height="1000" alt="image" src="https://github.com/user-attachments/assets/4f5b4ac9-e4e7-4f21-a7cc-017fe02f7410" />

*Click Input -> Keyboard -> Ctrl + Alt + Delete to access the login screen*  

---

## 🧱 Step 5 - Install Guest Additions  
After the OS booted, inserted Guest Additions from the VirtualBox “Devices” menu.  
Ran the AMD64 installer to improve mouse control, video scaling, and shared clipboard functionality.  
Rebooted when finished for smoother operation.  

<img width="1221" height="690" alt="image" src="https://github.com/user-attachments/assets/d50d8119-19c1-4d64-b525-3b091ea78717" />

*Installing guest additions*   

---

## 🌐 Step 6 - Configure IP Addressing and Rename Server  
Within the VM, opened Network Connections and identified both adapters.  
1. The adapter with the IP address 10.0.2.15 was identified as the external NIC, as it is connected to the internet. 
2. The adapter with the IP address 169.254.122.127 is the Internal NIC, as this is an APIPA address. This indicates the adapter was attempting to obtain an IP from a DHCP server but was not yet configured, so it automatically received an APIPA address. I renamed it to INTERNAL to clearly indicate that it will be used for the internal network.  

For the Internal Network adapter, manually assigned:  
**IP Address:** 172.16.0.1  
**Subnet Mask:** 255.255.255.0  
No need to assign a Default Gateway since the server will act as it.  
**DNS: 127.0.0.1** (Assigned loopback since we installed AD DS, and the server uses itself)  
Renamed the computer to **DC** and restarted.  

<img width="1398" height="1021" alt="image" src="https://github.com/user-attachments/assets/e99fcaa0-6e95-4b79-802e-bcf280aa420b" />

*We can tell which adapter is which based on the IP*  

<br> 

<img width="878" height="1054" alt="image" src="https://github.com/user-attachments/assets/4e3bf244-bc93-40a9-9ae5-dfdcb2c57a61" />

*Manually setting INTERNAL adapter ip address*  

<br> 

<img width="1551" height="1107" alt="image" src="https://github.com/user-attachments/assets/8b8fd527-545d-42d6-872b-98d61a00d011" />

*Renaming the PC*  

---

## 🏗️ Step 7 - Install Active Directory Domain Services  
Opened Server Manager > Add Roles and Features and installed Active Directory Domain Services (AD DS).  
After installation, promoted the server to a Domain Controller by adding a new forest named mydomain.com.  
Set the Directory Services Restore Mode password (used Password123!).  

<img width="1536" height="875" alt="image" src="https://github.com/user-attachments/assets/9bb4ec87-63fe-450c-80c5-151be8bee943" />

*Installing AD Domain Services*  

<br> 

<img width="1946" height="1108" alt="image" src="https://github.com/user-attachments/assets/5fa9c2af-ff0a-466d-b603-825727b9ef18" />

*Promoting to Domain Controller by clicking the yellow flag and setting the domain name*  

---

## 👤 Step 8 - Create a Domain Admin Account  
Logged back in as newly created Admin user from prev step.  
Opened Active Directory Users and Computers.  
Created a new Organizational Unit called “MyAdmins.”  
Inside it, created a user (a-rcabral) and set its password to Password123!.  
Added this user to the Domain Admins group.  
Logged out of the Administrator account and logged back in using the newly created domain admin credentials.  

<img width="1867" height="1068" alt="image" src="https://github.com/user-attachments/assets/498eebf1-2e40-47e3-ab1c-e3738f31277f" />

*Newly created user and joined Domain Admins*  

<br>

<img width="1712" height="1136" alt="image" src="https://github.com/user-attachments/assets/be359163-3c87-4809-9dfe-28416838741e" />

*Signed in using the newly created user*  

---

## 🔁 Step 9 - Configure NAT and Routing  
Installed the Remote Access role and enable Routing and Remote Access.  
Selected Network Address Translation (NAT) and chose the external (NAT) adapter as the public interface.  
This step allows internal network clients to access the Internet through the Domain Controller.  

<img width="1709" height="865" alt="image" src="https://github.com/user-attachments/assets/61d3b63c-1649-4b64-b0cd-8f7693489b08" />

*Installing the Remote Access role*  

<br>

<img width="1718" height="1031" alt="image" src="https://github.com/user-attachments/assets/ad308938-1e92-4474-ae6e-0625c1a8f86a" />

*Configuring NAT on the INTERNET adapter*  


---

## 📡 Step 10 - Set Up DHCP  
Installed the DHCP Server role via Server Manager.  
Created a new scope using:  
**Start IP:** 172.16.0.100  
**End IP:** 172.16.0.200  
**Subnet Mask:** 255.255.255.0  
**Set the Router Option to 172.16.0.1** (Domain Controller Internal NIC), we use this because we configured RAS / NAT on the DC so its job is to forward traffic from client to the internet.  
Finally, authorized and activated the scope.  

<img width="1716" height="989" alt="image" src="https://github.com/user-attachments/assets/ff6436aa-6b38-418d-a641-507fa9ea65e8" />

*DHCP configurations and end result*  

---

## 🌎 Step 11 - Enable Internet Access on the Server  
In Server Manager, opened Local Server Properties and disabled “Internet Explorer Enhanced Security Configuration.”  
This allowed browsing the internet without continuous pop-ups prompting for confirmation to continue.  

<img width="1699" height="766" alt="image" src="https://github.com/user-attachments/assets/53e18495-b72e-4347-a72c-a45dadddb488" />

*Disabling IE Enhanced Security Configuration*  

---

## 🧠 Step 12 - Create Users with PowerShell  
Downloaded a provided PowerShell script and file with a list of names (names.txt) and extracted it to the Desktop.  
Edited the names.txt file by adding my own name at the top. (so an account with my name is created) <br>
Opened PowerShell ISE as Administrator and ran the follwing command to allow all scripts to run:  

*Set-ExecutionPolicy Unrestricted*

I then opened the downloaded script, pointed to the folder location, and executed it. 
<br>

*The script creates 1,000 user accounts automatically inside a new “_Users” OU, each with username format firstinitiallastname and password Password1.*

<img width="2491" height="1093" alt="image" src="https://github.com/user-attachments/assets/629b5aa3-291b-48cb-92b1-ef88d740e52d" />

*PowerShell ISE script execution*

<br>

<img width="2478" height="1094" alt="image" src="https://github.com/user-attachments/assets/27952739-84e1-4685-bd65-9faaf089f678" />

*New OU and Users have been created*

---

## 🧮 Step 13 - Create the Windows 10 Client VM
Created a new VirtualBox VM named Client1, set OS type to Windows 10 (64-bit), and assigned 4 GB RAM.
Attached the Windows 10 ISO, and configured its single network adapter to Internal Network so it recieves DHCP address from Domain Controller.

<img width="2171" height="1111" alt="image" src="https://github.com/user-attachments/assets/77127e21-0dc4-46f5-a2aa-bb6e02f13932" />

*Using Internal Network to get DHCP address from DC*

---

## 💽 Step 14 - Install Windows 10 Pro
Booted the VM and installed Windows 10 Pro (not Home, since Home editions can’t join a domain).
Created a local user called “User” and skipped adding a Microsoft account.
Once installed, verified the client received a DHCP address in the 172.16.0.100-200 range by running ipconfig.

<img width="1938" height="1117" alt="image" src="https://github.com/user-attachments/assets/a699ba32-4034-4c2a-b64c-145a317b5d06" />

*172.16.0.100 ip address recieved from DC*

<br>

<img width="1794" height="925" alt="image" src="https://github.com/user-attachments/assets/1edbee7b-b47f-4b28-821f-d12ed43328aa" />


*Successful pings indicate proper config*

---

## 🧾 Step 15 - Rename and Join the Domain
Opened System Properties > Computer Name > Change.
Renamed the machine to Client1 and joined the domain mydomain.com (setup on DC). <br>
Provided domain credentials when prompted (admin or regular domain user).
Restarted to complete the domain join.

<img width="1393" height="1037" alt="image" src="https://github.com/user-attachments/assets/fca58e8c-7a2a-4151-ad22-d41b843d8d48" />

*Domain join confirmation*

---

## 🔐 Step 16 - Log In with a Domain Account
After restart, chose Other User at login.
Entered one of the new domain usernames (for example rcabral) with the password Password1.
Successfully signed in and loaded a profile connected to the domain and was able to connect to the internet.

This verified that AD, DHCP, DNS, and NAT were working correctly.

<img width="1555" height="1052" alt="image" src="https://github.com/user-attachments/assets/35c27c7d-6425-4108-94cc-510066d54396" />

*.\ shows PC name*

<br>

<img width="1769" height="862" alt="image" src="https://github.com/user-attachments/assets/f782e5ba-7e3e-4d3b-b1df-814e2367d094" />


*Successfully able to login with created user*

<br>

<img width="2406" height="1098" alt="image" src="https://github.com/user-attachments/assets/3d2ddbd0-55d6-443c-870d-e075e4f056e4" />


*Computer joined to domain and IP leased, able to login with any user in _USERS OU*

</div>
