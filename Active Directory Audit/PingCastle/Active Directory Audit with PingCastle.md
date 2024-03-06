---
tags: 
date: 01-03-2024
---

# Active Directory Audit with PingCastle (Part 1)
- Whenever we consider Information Security, our first priorities are our crown jewels, this are our most critical assets that require the highest level of protection. 
- Typical examples of this are:
	- Databases (Patient Records, PII DBs ...)
	- IAM (DC (Microsoft AD DS), Okta ...)
> [!Note]
> Although this are typical examples, your crown jewels are the things you identify as your crown jewels, it all depends on your organization.

- So now that we've established Active Directory as being one of the core essential part of our infrastructure, how do we secure it? 
> - What's that Bob? "Sometimes you just gotta sing along and hope for the best", No! "This is the worst idea I've ever heard...and I've heard some doozies.". 
> - Let's just solve this problem like adults... by yelling at each other and auditing until we're secure, maybe not scream.

- PingCastle is an Active Directory tool which helps audit our AD DS environment, that is free for personal use and depending on additional professional needs, there are various editions of commercial packages. We'll be focusing on using the free version, as we're working on personal environment.
## So why are we auditing an Active Directory? (I know you're not an Accountant Bob, Chill!) ##
- So the reason for security auditing is mainly to identify security flaws in our environment, an audit highlights what misconfigurations, vulnerabilities and weaknesses we have in our environment.
- Once we know what our weaknesses are, we can then tackle those weaknesses one by one, (yes Bob first step in solving a problem is knowing there's a problem.)
- Now before we proceed let's check PingCastles recommended methodology, which can be find here: https://pingcastle.com/methodology/
### Methodology ###
- **Understanding the Stakeholders**: understanding different stakeholders and their needs is an important part of auditing systems for security.
	- Management
		- AD security level benchmark and standards in the industry, and how the current setup fairs compared to them. This helps them identify if more investment is needed or less.
		- Avoiding critical risks, it's all about prioritizing and critical risks are always of concern for management.
		- Cost, management is always concerned with cost of performing any action, and budgets for securing AD needs to be defined as budget is shared with other areas of interest.
	- Technical Ops
		- Always concerned with securing and making sure systems are always available for users.
		- Visibility, key aspect of security and huge concern of technical security teams is having visibility and ease of identifying security flaws is major area of interest.
		- Concerned with making sure vulnerabilities are fixed but without causing problems, to do this testing changes aka sandboxing is a key aspect.
- In PingCastle we follow Maturity Based Improvement.
- **Conduct Assessment/Audit**: understand our strength and weakness, identify misconfigurations and inherent security flaws that exist.
- **Analyze the results**: identify vulnerabilities, gaps in controls, critical flaws and areas for improvement.
- **Prepare a Plan**: auditing and securing AD is not a one of task, it's a continuous maturity based improvement. As such we need to define our goal, timeline, resources needed for securing and milestones  & schedules.
	- Define goal (Maturity level: basic, intermediate, advanced), multiple stakeholders are involved here.
	- Define timeline for the process, milestones, and schedules for each task.
	- Define resources need for the process
	- Prioritize: based on criticality.
- **Implement Plan**: fix vulnerabilities, security flaws and improve Active Directory security posture according to plan.
- **Measure Progress**: assess progress of implementing plan and readjust if needed.
- **Continuous Improvement**: information security is never a one off project or task, it's a continuous ongoing process. Regularly auditing and reassessments are important for improving maturity level or maintaining the existing level. 
- Alright let's dive in:
> [!Note]
> If you haven't checked out the guide on how to build AD Lab for learning cyber security, checkout my [lazy-ad](https://github.com/krooth/lazy-AD) project on github.

---

## Download and Setup PingCastle ##
- Go to PingCastle and grab the latest and greatest download link:
- Now although this is a pingcastle audit blog, in reality we'll be auditing AD using different set of tools, so for organizing our auditing it's better to contain the auditing in the same directory. 
 ```PowerShell
 mkdir AD_Audit
 cd AD_audit
 Invoke-WebRequest -Uri https://github.com/vletoux/pingcastle/releases/download/3.2.0.1/PingCastle_3.2.0.1.zip -Outfile PingCastle.zip
 Expand-Archive .\PingCastle.zip PingCastle
 cd .\PingCastle\
 ```
 Alriiiiight! we've successfully installed/downloaded PingCastle...
## Everything's better with cheese (I mean Audit, Darn It Bob) ##
- So let's run PingCastle, now there are different check options in it:
### General health check ###
- This is the default check, which essentially quickly collects the most important information of AD to establish an overview on it.
	```PowerShell
	.\PingCastle --server <AD Server IP> --healthcheck --level Full --no-enum-limit
	```
	
	- server: specifies the target server (if we're running PingCastle we can use `localhost` or `127.0.0.1`)
	- healthcheck: type of check we're performing
	- level: specify the amount of data found in the xml file (Full, Normal, Light)
	- no-enum-limit: remove the max 100 users limitation in html report

> [!Note]
> PingCastle has many more options, like sending html to emails, specifying exceptions and so much more, we can check that with the `--help` option.
![](../../imgs/Pasted%20image%2020240302124021.png)

- Alriiiight! Let's run the health check:
![](../../imgs/Pasted%20image%2020240305224838.png)
- Well it's official we've our first report, however in an Enterprise environment a quick health check.
### Scanners ###
- Like we spoke earlier, we have several type of scanners we can run to gather better information about our domain environment.
	- aclcheck: checks authorization related to users or groups
	- antivirus: checks for computers without known antivirus installed.
	- computerversion: checks version of OS's, which help determine if we're using obsolete OS's
	- foreignusers: use trusts to enumerate users located in domain denied such as bastion or domains too far away.
	- laps_bitlocker: check if LAPS and/or Bitlocker is enabled,
	- localadmin: Enumerate the local administrators of a computer.
	- nullsession: check if null sessions are enabled.
	- remote: chek if an rdp solution is installed on computers.
	- share: scan for local share and result shows if share has been accessed by anyone.
	- smb: determines smb version available and if smb signing is applied.
	- spooler: cheks if the spooler service is remotely active.
	- startup: retrieves the last date of startup.
	- zerologon: check for zerologon vulnerability.

```PowerShell
.\PingCastle.exe --scanner antivirus
.\PingCastle.exe --scanner computerversion
.\PingCastle.exe --scanner laps_bitlocker
.\PingCastle.exe --scanner localadmin
.\PingCastle.exe --scanner nullsession
.\PingCastle.exe --scanner remote
.\PingCastle.exe --scanner share
.\PingCastle.exe --scanner smb
.\PingCastle.exe --scanner spooler
.\PingCastle.exe --scanner startup
.\PingCastle.exe --scanner zerologon
```

> [!Reminder]
> When running this scanners we're prompted if we want to scan all computers or the domain we'd like to scan, it's important to scan all computers in the domain and specify or use the default domain listed there, unless we're auditing specific computers and/or other domains.

![](../../imgs/Pasted%20image%2020240306122211.png)
- No finally we've scanned and checked our AD, the only thing left for us is to check the reports generated and address the issues.
- For convenience purposes, I personally like to collect and keep the generated reports in one directory and also organize the audit, an easy way to identify the generated reports is that, all the reports have the name of our domain. 
```PowerShell
$today = Get-Date -Format "dd_MM_yyy"
mkdir audit_report_$today
Move-Item *<domain name>* <to this directory we created earlier>
```

![](../../imgs/Pasted%20image%2020240306124003.png)

- Now let's cd to our new report directory and check the html output
```PowerShell
	cd audit_report_$today
```
![](../../imgs/Pasted%20image%2020240306124403.png)
- As we can see above we have our reports here, now this report can be use differently, we can use the xml output to ingest to other tools.
- So let's finally check our html report, (Got be honest Bob, super excited. This is going to be a burger flip)
![](../../imgs/Pasted%20image%2020240306125355.png)
- I'll take that back, Well this is just great!
- We'll be going over the report in Part 2, and see what each warnings and scores are, how we can solve them or why we won't solve them, because business needs differ from organization to organization, and we might not be able to apply every recommended solutions.
- Okay I've got to go, [Bob's voice] `Linda, Honey...Linda`
# {{References}}
- [Methodology](https://pingcastle.com/methodology/)
- [Documentation](https://pingcastle.com/methodology/)