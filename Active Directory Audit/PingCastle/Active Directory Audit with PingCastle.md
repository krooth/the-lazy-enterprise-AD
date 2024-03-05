---
tags: 
date: 01-03-2024
---

# Active Directory Audit with PingCastle
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
- 
# {{References}}
