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

- <b>Windows 10</b> - Client machine
- <b>Windows Server 20219</b> - Domain Controller and AD services

<h2>:clapper: Program walk-through:</h2>

<p align="center">
Launch the utility: <br/>
<img width="1062" height="432" alt="image" src="https://github.com/user-attachments/assets/74356e95-47b2-48a4-8900-f4bff4dbd59b" />
<br />
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
