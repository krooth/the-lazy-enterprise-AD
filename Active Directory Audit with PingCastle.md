---
tags: 
date: 01-03-2024
---

# Active Directory Audit with PingCastle
- PingCastle is an Active DIrectory tool, that is free for personal use and depending on additional professional needs, there are various editions of commercial packages.
- We'll be focusing on using the free version, as we're working on personal environment.
## So why are we auditing an Active Directory? (I know you're not an Accountant Bob!) ##
- So the reason for security auditing is mainly to identify security flaws in our environment, an audit highlights what misconfigurations, vulnerabilities and weaknesses we have in our environment.
- Once we know what our weaknesses are, we can then tackle those weaknesses one by one, (yes Bob first step in solving a problem is knowing there's a problem.)
- Alright let's dive in:
> [!Note]
> If you haven't checked out the guide on how to build AD Lab for learning cyber security, checkout my [lazy-ad](https://github.com/krooth/lazy-AD) project on github.

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
## Everything's better with cheese (I mean Audit, Darn It Bob)
- So let's run pingcastle, now when we run pingcastle we have a general health check p
# {{References}}
