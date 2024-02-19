# AD Enterprise Population

### Requirements ###
- An already configured server with AD DS (Active Directory Domain Services) is needed to run this tools, if you haven't done so you [Building AD Lab](https://github.com/krooth/lazy-AD/blob/main/Active%20Directory%20Lab%20Build/Building%20AD%20Lab.md) section.
- We need to have a user with Domain Admin and Schema Admin, while it is indeed possible to use the Administrator account, we don't want to do that for reasons we'll talk about in our Active Directory Best Practices blog. So then, yes you guessed it we're creating an admin account and adding it to Domain Admins and Schema Admins groups.
- 
### AD Enterprise Population Process ###
- For this we'll be mainly using the tool Badblood by @davidprowe, and some other PowerShell scripts as well.

#### Badblood ####
- Badblood is a tools is a security tool for Active Directory, it used to populate an AD, so professionals can simulate/learn/demo security related concepts regarding AD.
- Each Badblood script execution generates different results, with different users, groups, computers and permissions.
- Now, let's get to the fun part [[AD Enterprise Population#Steps]]
##### Steps #####
- 

# {{References}}
- https://www.secframe.com/badblood/