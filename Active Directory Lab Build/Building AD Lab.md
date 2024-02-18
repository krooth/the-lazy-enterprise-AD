
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
#### Assigning Static IP ####
- **Running PowerShell as Administrator**
	- Usually when dealing with servers we'd want a static IP addressing, especially if we're running DNS service on it, thus we'll be assigning static IP to our server here:
	- Open PowerShell as administrator
		- ![[../imgs/Pasted image 20240213213009.png]]
		- Or open run and type in "powershell", and hit Ctrl + Alt + Enter:
			- ![[../imgs/Pasted image 20240213213131.png]]
	- Now once we've opened PowerShell, we are looking for our network adapters interface index, we can retrieve that by executing the command:
- **Set IPv4 Address**
	```PowerShell
		Get-NetAdapter
	```
	 ![[../imgs/Pasted image 20240213220038.png]]
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
	 ![[../imgs/Pasted image 20240213223026.png]]
	- We can check if DNS and IP is set accordingly by using the command, `ipconfig`.

#### Installing ADDS Role and Features ####
- Run PowerShell as admin like before
> [!REMINDER]
> Replace the names and the specific paths, according to your setup.
- **Adding ADDS Services**
	- We first need to add the Active Directory Domain service:
	```PowerShell
	Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
	```
	
	- Once we run the command above, we'll begin to see the progress of the installation:
			 ![[../imgs/Pasted image 20240213223747.png]]
			- This will be the result once the installation is complete:
			 ![[../imgs/Pasted image 20240213224140.png]]
- **Promote and Install ADDS Forest**
	- Now when adding these services, we'll be providing number of parameters, some standard set of options, others specific to our env't like the name of our Forest/Domain:
	
| **Parameters** | **Description** | **Value** |
| ---- | ---- | ---- |
| -DomainName | This is the name of our domain. | kroothy.io |
| -InstallDNS | Active Directory is heavily reliant and integrated with DNS thus we do need to install DNS almost always, unless in d/t specialized configurations, which we're not using here. | $true |
| -CreateDNSDelegation | Used to automatically create delegation of control (DoC) relationships between the newly installed domain controller (DC) and other existing DC, in our we won't have any other DCs so we don't need this. | $false |
| -DomainMode | Specifies the functional level of the first domain in the creation of  a new forest. We are using the latest Windows Server 2016 mode, which has a value of '7' or 'WinThreshold'. | 7 |
| -ForestMode | Similarly this option specified the forest functional level. | 7 |
| -DatabasePath | Specifies the fully qualified, non-Universal Naming Convention (UNC) path to directory of the local server, that is to contain the domain database. | C:\Windows\NTDS |
| -SysvolPath | Specifies the path to where the Sysvol file is written. | C:\Windows\SYSVOL |
| -LogPath | Specifies the path log files for this operation (Install-ADDSForest) file is written. | C:\Windows\NTDS |
| -DomainNetbiosName | Specifies the NetBIOS name for the root domain in the new forest. | kroothy |
- Installing ADDS Forest:
	```PowerShell
	Install-ADDSForest -DomainName <Your_Full_AD_Forest_Name> -InstallDns:$true  -CreateDnsDelegation:$false -DomainMode "7" -ForestMode "7" -DatabasePath <Your_DB_Directory_Path> -SysvolPath <Your_SYSvol_Directory_Path> -LogPath <Your_Log_Directory_Path> -DomainNetbiosName <Your_AD_Forest_NetBIOS_Name>
	```
	![[../imgs/Pasted image 20240218134918.png]]
# {{References}}
- [VMware Windows Server Virtualization](https://www.wikihow.com/Use-VMware-Workstation)
- [VirtualBox For Beginners](https://www.youtube.com/watch?v=nvdnQX9UkMY)
- [VirtualBox Documentation](https://www.virtualbox.org/manual/ch01.html)
- [Microsoft Guide for installing AD DS via PS](https://learn.microsoft.com/en-us/powershell/module/addsdeployment/install-addsforest?view=windowsserver2019-ps)
- 