---
tags: 
date: 13-02-2024
---

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
#### Assigning Static IP ####
- **Running PowerShell as Administrator**
	- Usually when dealing with servers we'd want a static IP addressing, especially if we're running DNS service on it, thus we'll be assigning static IP to our server here:
	- Open PowerShell as administrator
		- ![[Pasted image 20240213213009.png]]
		- Or open run and type in "powershell", and hit Ctrl + Alt + Enter:
			- ![[Pasted image 20240213213131.png]]
	- Now once we've opened PowerShell, we are looking for our network adapters interface index, we can retrieve that by executing the command:
- **Set IPv4 Address**
		```
		Get-NetAdapter
		```
		- ![[Pasted image 20240213220038.png]]
	- To set a new IPv4 address we can use d/t commands, we'll use the New-NetIPAddress command:
		```
			New-NetIPAddress –IPAddress <ip_address>-PrefixLength <subnet_mask_in_bit_format> -DefaultGateway <default_gateway>  -InterfaceIndex <interface_index_retrieved_earlier>
		```
		- We can also chain the two commands in to one buy using:
			```
			New-NetIPAddress –IPAddress <ip_address> -DefaultGateway <default_gateway> -PrefixLength <subnet_mask_in_bit_format> -InterfaceIndex (Get-NetAdapter).InterfaceIndex
			```
	- We can check if we succeeded in changing the IP, by using the "ipconfig" command;
			```
			ifconfig
			```
- **Set DNS Servers**
	- We can use the command Set-DNSClientServerAddress command to set DNS for the server, now we can have multiple DNS servers configured on our server, but one of them ideally the primary needs to be the IP address of the DNS server, in this case the active directory server:
	```
	Set-DNSClientServerAddress –InterfaceIndex (Get-NetAdapter).InterfaceIndex –ServerAddresses <Your_AD_IP>
	```
	- ![[Pasted image 20240213223026.png]]
	- We can check if DNS and IP is set accordingly by using the command, "ipconfig"

#### Installing ADDS Role and Features ####
- Run PowerShell as admin like before
- **Adding ADDS Services**
		```
		Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
		```
		- Once we run the command above, we'll begin to see the progress of the installation:
			- ![[Pasted image 20240213223747.png]]
			- This will be the result once the installation is complete:
				- ![[Pasted image 20240213224140.png]]
		- 
- **Promote and Install ADDS Forest**
	- Now when adding these services, we'll be providing number of parameters, some standard set of options, others specific to our env't like the name of our Forest/Domain:
# {{References}}
- https://www.wikihow.com/Use-VMware-Workstation
- https://www.youtube.com/watch?v=nvdnQX9UkMY
- https://www.virtualbox.org/manual/ch01.html