
# Building AD Lab
### Requirements ###
- A Windows Server VM preferably Windows Server 2022 or 2019 or 2016.
	- This lab is built with Windows Server 2022, with iso image downloaded from Microsoft Evaluation Center, https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022.
- A virtualization software/platform
	- VMware Workstation Pro or Player
	- VirtualBox
	- This lab is built using VMware's Workstation Pro version 17.
	- You can also use other virtualization methods.
- Note: this guide doesn't go through the process of virtualizing Windows Server, there are links to good guides of doing so in the References section.
- In summary you need a virtualized Windows Server 2016/19/22, ideally the latest for creating this lab.
- Now once we have the windows server ready, we can build our AD Lab for use of security assessments, hardening and hacking simulations.

### AD Lab Build Process ###
- Now there are ways to got about things in windows or any operating system, using the GUI or the command line, in our case we'll be using PowerShell, using GUI is just as effective, but learning the PowerShell way will help us get familiarize with PowerShell as that's one important component of windows with regards to administration and security as well.

> [!IMPORTANT]
> Replace the IPv4 prompts with your server IPv4 address, likewise for the ADDS Forest installation, replace the names according to your own lab naming preference.

---

#### Assigning Static IP ####
- **Running PowerShell as Administrator**
	- Usually when dealing with servers we'd want a static IP addressing, especially if we're running DNS service on it, thus we'll be assigning static IP to our server here:
	- Open PowerShell as administrator
		- ![](../imgs/c57d2f866388d63381d357ab05d121c5_MD5.jpeg)
		- Or open run and type in "powershell", and hit Ctrl + Alt + Enter:
		- ![](../imgs/db3e4c9fbc904b0a50c350e97e20b9fb_MD5.jpeg)
	- Now once we've opened PowerShell, we are looking for our network adapters interface index, we can retrieve that by executing the command:
- **Set IPv4 Address**
	```PowerShell
		Get-NetAdapter
	```
	 ![](../imgs/bb72b610a2804892a1e27cf4f12dafed_MD5.jpeg)
	- To set a new IPv4 address we can use d/t commands, we'll use the New-NetIPAddress command:
		```PowerShell
			New-NetIPAddress –IPAddress <ip_address>-PrefixLength <subnet_mask_in_bit_format> -DefaultGateway <default_gateway>  -InterfaceIndex <interface_index_retrieved_earlier>
		```
		- We can also chain the two commands in to one buy using:
			```PowerShell
			New-NetIPAddress –IPAddress <ip_address> -DefaultGateway <default_gateway> -PrefixLength <subnet_mask_in_bit_format> -InterfaceIndex (Get-NetAdapter).InterfaceIndex
			```
	- We can check if we succeeded in changing the IP, by using the "ipconfig" command;
		```PowerShell
		ifconfig
		```
- **Set DNS Servers**
	- We can use the command Set-DNSClientServerAddress command to set DNS for the server, now we can have multiple DNS servers configured on our server, but one of them ideally the primary needs to be the IP address of the DNS server, in this case the active directory server:
	```PowerShell
	Set-DNSClientServerAddress –InterfaceIndex (Get-NetAdapter).InterfaceIndex –ServerAddresses <Your_AD_IP>
	```
![](../imgs/2b9e6e5aee00f450ab2360ad210825be_MD5.jpeg)
	- We can check if DNS and IP is set accordingly by using the command, `ipconfig`.

---
#### Renaming the Server HostName####
- Run PowerShell as admin like before
> [!CAUTION]
> Replace the name of the server according to you setup

- **Set Hostname**
	```PowerShell
	Rename-Computer -ComputerName <Your_Old_ServerName> -NewName <Your_New_ServerName> -DomainCredential <DomainName\Username> -Password <Password> -Force -Restart
	```
> [!NOTE]
> - DomainName can be your ComputerName\Username, Eg: Win2019\Administrator.
> - This task like all the others can be done via GUI.
> - The Server/Computer needs to restart after hostname change.

---

#### Installing ADDS Role and Features ####
- Run PowerShell as admin like before
> [!CAUTION]
> Replace the names and the specific paths, according to your setup.
- **Adding ADDS Services**
	- We first need to add the Active Directory Domain service:
	```PowerShell
	Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
	```
	
	- Once we run the command above, we'll begin to see the progress of the installation:
			 ![](../imgs/4b3e9cb4e070aca26c57e1107ac26d06_MD5.jpeg)
			- This will be the result once the installation is complete:
			 ![](../imgs/230003853bcb2c940c57a3f7a77df850_MD5.jpeg)
- **Promote and Install ADDS Forest**
	- Now when adding these services, we'll be providing number of parameters, some standard set of options, others specific to our env't like the name of our Forest/Domain:
	
| **Parameters**       | **Description**                                                                                                                                                                                            | **Value**         |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| -DomainName          | This is the name of our domain.                                                                                                                                                                            | kroothy.io        |
| -InstallDNS          | Active Directory is heavily reliant and integrated with DNS thus we do need to install DNS almost always, unless in d/t specialized configurations, which we're not using here.                            | $true             |
| -CreateDNSDelegation | Used to automatically create delegation of control (DoC) relationships between the newly installed domain controller (DC) and other existing DC, in our we won't have any other DCs so we don't need this. | $false            |
| -DomainMode          | Specifies the functional level of the first domain in the creation of  a new forest. We are using the latest Windows Server 2016 mode, which has a value of '7' or 'WinThreshold'.                         | 7                 |
| -ForestMode          | Similarly this option specified the forest functional level.                                                                                                                                               | 7                 |
| -DatabasePath        | Specifies the fully qualified, non-Universal Naming Convention (UNC) path to directory of the local server, that is to contain the domain database.                                                        | C:\Windows\NTDS   |
| -SysvolPath          | Specifies the path to where the Sysvol file is written.                                                                                                                                                    | C:\Windows\SYSVOL |
| -LogPath             | Specifies the path log files for this operation (Install-ADDSForest) file is written.                                                                                                                      | C:\Windows\NTDS   |
| -DomainNetbiosName   | Specifies the NetBIOS name for the root domain in the new forest.                                                                                                                                          | kroothy           |
- Installing ADDS Forest:
	```PowerShell
	Install-ADDSForest -DomainName <Your_Full_AD_Forest_Name> -InstallDns:$true  -CreateDnsDelegation:$false -DomainMode "7" -ForestMode "7" -DatabasePath <Your_DB_Directory_Path> -SysvolPath <Your_SYSvol_Directory_Path> -LogPath <Your_Log_Directory_Path> -DomainNetbiosName <Your_AD_Forest_NetBIOS_Name>
	```
	![](../imgs/f1d4c9677821a8173916384f4a3c1b6d_MD5.jpeg)
	- Right after this we'll be prompted ==SafeModeAdministratorPassword==, we should enter a long password with good complexity if we're deploying this in a production environment.
		 ![](../imgs/be35697f172a5035b88bddf9930f66f7_MD5.jpeg)
	- We should the finally confirm the operation, we can choose 'A' to say yes to all prompts.
> [!Note] 
> It will take some time to complete the installation. i
> After completion it will output that our forest is installed and restart the server.

  - After this we're all set we just need to check if ADDS Forest installed and configured, we could do that with a couple of PowerShell commands:
	```PowerShell
	Get-ADForest 
	Get-ADDomain
	Get-ADDomainController
	Get-ADReplicationSite
	Get-Service adws,kdc,netlogon,dns
	Get-smbshare SYSVOL
	```
- Here is how it looks when running the commands above:

	![](../imgs/ceac5d84fcc644b5c294c0b475d1c97f_MD5.jpeg)

	![](../imgs/58d5e65a30db626551acd195e1090c87_MD5.jpeg)

	![](../imgs/f9afbf6bb24a1574e9c254202c631c23_MD5.jpeg)

	![](../imgs/d1540cb182ecd29b31c108a286608f9c_MD5.jpeg)

	![](../imgs/fcf0241ed324598c6bbc3021d2638512_MD5.jpeg)

	![](../imgs/d1540cb182ecd29b31c108a286608f9c_MD5.jpeg)
**Complete**
- We have stood up a Domain Controller, fully functional with no records inside it, we'll be populating it with other tools online.
- Next up is [AD Enterprise Population](https://github.com/krooth/lazy-AD/blob/main/Active%20Directory%20Lab%20Build/AD%20Enterprise%20Population.md)
# {{References}}
- [VMware Windows Server Virtualization](https://www.wikihow.com/Use-VMware-Workstation)
- [VirtualBox For Beginners](https://www.youtube.com/watch?v=nvdnQX9UkMY)
- [VirtualBox Documentation](https://www.virtualbox.org/manual/ch01.html)
- [Microsoft Guide for installing AD DS via PS](https://learn.microsoft.com/en-us/powershell/module/addsdeployment/install-addsforest?view=windowsserver2019-ps)