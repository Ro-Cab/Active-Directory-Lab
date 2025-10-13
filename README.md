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

<div align="center">

## 🧩 Step 1 - Install VirtualBox and Extension Pack  
Started by downloading and installing Oracle VirtualBox on my host computer. Once installed, added the Extension Pack to enable USB, RDP, and advanced network support.  

<img width="975" height="413" alt="image" src="https://github.com/user-attachments/assets/62ca7fd5-5f17-4325-a539-3efd142dbc0a" />

*Oracle VM and Extension Pack download page*

---

## 💾 Step 2 - Download Windows ISOs  
Obtained the Windows Server 2019 ISO and Windows 10 ISO from Microsoft’s official evaluation or download site. (Note: For Windows 10, I needed to download the installation media and then choose Windows 10 -> save as ISO)  
Stored them together in an easy-to-find folder (Desktop) so I could attach them during VM creation.  

<img width="975" height="443" alt="image" src="https://github.com/user-attachments/assets/c9ad837e-3fad-4c6c-acd1-5722b534da42" />

*Download page for the Windows Server 2019 & Windows Media Creation Tool*  

---

## 🖥️ Step 3 - Create the Domain Controller VM  
Launched VirtualBox, created a new VM named DC, and used the Windows Server 2019 iso as the image. Made sure to set the OS edition to Desktop Experience or else I would not have a GUI and can only navigate using CLI.  

Set the type to “Windows (64-bit)” and assign at least 2 GB of RAM. Left the rest of the settings as is and clicked Finish.  
I then right-clicked on the VM. Went to Network -> Adapter 1 & Adapter 2  

**- Adapter 1:** NAT (for Internet access)  
**- Adapter 2:** Internal Network (for communication with other VMs)  
This configuration allowed my virtual domain to connect internally while still reaching the Internet.  

<img width="975" height="299" alt="image" src="https://github.com/user-attachments/assets/0d67dabf-a174-4887-87fe-a251bd69f85e" />

*Setup Options to use*  

<br> 
<img width="975" height="566" alt="image" src="https://github.com/user-attachments/assets/c3ef85b4-bf55-4ce3-8288-be6b992699ae" />

*Enabling Adapter 2 and Setting to Internal Network*  

---

## ⚡ Step 4 - Install Windows Server 2019 & Guest Additions  
Double-clicked the VM to enter Windows Server 2019 setup.  
Chose **“Standard (Desktop Experience)”** for a GUI interface.  
Completed the installation and set the Administrator password to Password123! (for lab purposes).  

Logged in once setup finished.  

<img width="666" height="510" alt="image" src="https://github.com/user-attachments/assets/af7257ae-3acc-4feb-95b8-7b8e7aa67121" />

*Windows Server 2019 Setup Wizard*  

<br> 

<img width="520" height="654" alt="image" src="https://github.com/user-attachments/assets/62e32653-2d43-4ec3-9f8e-e774b0365d23" />

*Click Input -> Keyboard -> Ctrl + Alt + Delete to access the login screen*  

---

## 🧱 Step 5 - Install Guest Additions  
After the OS booted, inserted Guest Additions from the VirtualBox “Devices” menu.  
Ran the AMD64 installer to improve mouse control, video scaling, and shared clipboard functionality.  
Rebooted when finished for smoother operation.  

<img width="765" height="437" alt="image" src="https://github.com/user-attachments/assets/5a1d2a1e-234e-4993-aa6d-a013a0fb67d9" />

*Installing guest additions*   

---

## 🌐 Step 6 - Configure IP Addressing and Rename Server  
Within the VM, opened Network Connections and identified both adapters.  
1. Identified the adapter with the 10.0.2.15 IP address. We can tell that this one is connected to the internet.  
2. We know that the adapter that has the 169.254.122.127 is the Internal NIC because that is an APIPA address. This means that this adapter was searching for a DHCP server to assign an address; however, it was not configured yet, so it was assigned an APIPA address. Renamed to INTERNAL so I’d know it will be used for the internal network.  

For the Internal Network adapter, manually assigned:  
**IP Address:** 172.16.0.1  
**Subnet Mask:** 255.255.255.0  
No need to assign a Default Gateway since the server will act as it.  
**DNS: 127.0.0.1** (Assigned loopback since we installed AD DS, and the server uses itself)  
Rename the computer to DC and restart.  

<img width="875" height="647" alt="image" src="https://github.com/user-attachments/assets/4d7e0232-0dfb-4fce-a28a-c43351692a32" />

*We can tell which adapter is which based on the preconfigured IP*  

<br> 

<img width="556" height="700" alt="image" src="https://github.com/user-attachments/assets/058b4c59-eafe-4e39-87b8-7de2ba7e7813" />

*Manually setting INTERNAL adapter ip address*  

<br> 

<img width="975" height="706" alt="image" src="https://github.com/user-attachments/assets/8e1ca72b-b2c3-458b-881b-545fe7ba1da9" />

*Renaming the PC*  

---

## 🏗️ Step 7 - Install Active Directory Domain Services  
Opened Server Manager > Add Roles and Features and installed Active Directory Domain Services (AD DS).  
After installation, promoted the server to a Domain Controller by adding a new forest named mydomain.com.  
Set the Directory Services Restore Mode password (used Password123!).  

<img width="773" height="450" alt="image" src="https://github.com/user-attachments/assets/af2439fb-8365-4a73-ad56-faf4dbe7aabe" />

*Installing AD Domain Services*  

<br> 

<img width="975" height="686" alt="image" src="https://github.com/user-attachments/assets/4444d7d8-c7eb-491d-a44c-1996e4bc24f8" />

*Promoting to Domain Controller by Clicking the yellow flag and setting the domain name*  

---

## 👤 Step 8 - Create a Domain Admin Account  
Logged back in as newly created Admin user from prev step.  
Opened Active Directory Users and Computers.  
Created a new Organizational Unit called “MyAdmins.”  
Inside it, created a user (a-rcabral) and set its password to Password123!.  
Added this user to the Domain Admins group.  
Logged out of the Administrator account and log back in using your new domain admin credentials.  

<img width="975" height="615" alt="image" src="https://github.com/user-attachments/assets/a5162a49-5c0d-4d9d-a3bd-2abc752e57dc" />

*Newly created user and joined Domain Admins*  

<br>

<img width="975" height="722" alt="image" src="https://github.com/user-attachments/assets/c6c4b4ee-7dca-4e10-b03b-ccc2aa98ef5d" />

*Signed in using the newly created user*  

---

## 🔁 Step 9 - Configure NAT and Routing  
Installed the Remote Access role and enable Routing and Remote Access.  
Selected Network Address Translation (NAT) and chose the external (NAT) adapter as the public interface.  
This step allows internal network clients to access the Internet through the Domain Controller.  

<img width="975" height="500" alt="image" src="https://github.com/user-attachments/assets/92e8e13a-a67a-4127-b61a-eeb1f428b68e" />

*Installing the Remote Access role*  

<br>

<img width="975" height="591" alt="image" src="https://github.com/user-attachments/assets/ba443c91-ca7d-44e0-8c67-69abca310e30" />

*Configuring NAT on the INTERNET adapter*  


---

## 📡 Step 10 - Set Up DHCP  
Installed the DHCP Server role via Server Manager.  
Created a new scope using:  
**Start IP:** 172.16.0.100  
**End IP:** 172.16.0.200  
**Subnet Mask:** 255.255.255.0  
**Set the Router Option to 172.16.0.1** (Domain Controller IP), we use this because we configured RAS / NAT on the DC so its job is to forward traffic from client to the internet.  
Finally, authorized and activated the scope.  

<img width="975" height="576" alt="image" src="https://github.com/user-attachments/assets/bd38cbe5-103e-4629-ab58-76010340324a" />

*DHCP configurations and end result*  

---

## 🌎 Step 11 - Enable Internet Access on the Server  
In Server Manager, opened Local Server Properties and disable “Internet Explorer Enhanced Security Configuration.”  
This allowed browsing the internet without continuous pop-ups prompting for confirmation.  

<img width="975" height="439" alt="image" src="https://github.com/user-attachments/assets/ac7c4ce9-605b-4e16-b141-dc9807534c98" />

*Disabling IE Enhanced Security Configuration*  

---

## 🧠 Step 12 - Create Users with PowerShell  
Downloaded the provided PowerShell script and extracted it to the Desktop.  
Edited the names.txt file by adding my own name at the top.  
Opened PowerShell ISE as Administrator and ran:  

Set-ExecutionPolicy Unrestricted


I then opened the script, pointed to the folder location, and executed it.
The script creates 1,000 user accounts automatically inside a new “_Users” OU, each with username format firstinitiallastname and password Password1.

<img width="975" height="447" alt="image" src="https://github.com/user-attachments/assets/e72fce77-a904-41fd-9daa-77470f6b48e7" />

*PowerShell ISE showing script execution*

<br>

<img width="975" height="437" alt="image" src="https://github.com/user-attachments/assets/cabe6f7f-b666-4993-a294-97a3bce208f9" />

*New OU and Users have been created*

---

## 🧮 Step 13 - Create the Windows 10 Client VM
Created a new VirtualBox VM named Client1, set OS type to Windows 10 (64-bit), and assign 2–4 GB RAM.
Attached the Windows 10 ISO, and configure its single network adapter to Internal Network so it connects to the lab’s private network.

<img width="975" height="570" alt="image" src="https://github.com/user-attachments/assets/8d8c408f-1cd0-4840-b851-594b175f735f" />

*Using Internal Network to get DHCP address from DC*

---

## 💽 Step 14 - Install Windows 10 Pro
Booted the VM and installed Windows 10 Pro (not Home, since Home editions can’t join a domain).
Create a local user called “User” and skip adding a Microsoft account.
Once installed, verified the client received a DHCP address in the 172.16.0.x range by running ipconfig.

<img width="975" height="639" alt="image" src="https://github.com/user-attachments/assets/06a46a9c-d849-48bd-822a-4917683661fc" />

*Command Prompt showing ipconfig output, DFG, DNS, DHCP from DC*

<br>

<img width="975" height="510" alt="image" src="https://github.com/user-attachments/assets/53d46635-de04-4cbc-8854-03a8ebbc386f" />


*Successful pings indicate proper config*

---

## 🧾 Step 15 - Rename and Join the Domain
Opened System Properties > Computer Name > Change.
Renamed the machine to Client1 and join the domain mydomain.com.
Provided domain credentials when prompted (admin or regular domain user).
Restarted to complete the domain join.

<img width="975" height="734" alt="image" src="https://github.com/user-attachments/assets/8d8337d2-02e5-4a20-9ef8-7151c2024cd0" />

*Domain join confirmation*

---

## 🔐 Step 16 - Log In with a Domain Account
After restart, chose Other User at login.
Entered one of the new domain usernames (for example rcabral) with the password Password1.
Successfully signed in and loaded a profile connected to the domain.

This verified that AD, DHCP, DNS, and NAT are working correctly.

<img width="975" height="662" alt="image" src="https://github.com/user-attachments/assets/45c3959d-e5ce-48c4-834f-931a13440d44" />

*.\ shows PC name*

<br>

<img width="975" height="474" alt="image" src="https://github.com/user-attachments/assets/556f09d3-9a2d-4bab-a9b7-40f6e7fb050f" />


*Successfully able to login with created user*

<br>

<img width="975" height="476" alt="image" src="https://github.com/user-attachments/assets/fc5de5ab-77a9-40ab-a635-ce9a95dd343a" />


*Computer joined to domain and IP leased, able to login with any user in _USERS OU*

</div>
