# [Active Directory Basics](https://tryhackme.com/room/winadbasics)

## Task 1 - 3

In the first few tasks, there where no using the Machine. No hacking was needed for this part, all the solutions could be found by reading the text. So there is no point in explaining how i solved the tasks. Insted, here are my notes of the most important parts of the course beginning.

A **Domain** is a grouping of **objects** and the administration of those components in a single repository called a **Active Directory (AD)**. 
The server that runs the Active Directory services is known as a **Domain Controller (DC)**. 
The **Active Directory Domain Service (AD DS)** catalogues the **objects**. 
Theese are some of the most common objects:
- **Users**: often represent people, but can also represent services. Can be authenticated by the domain and be given different levels of privilege. They are known as **security principals**, meaning that they can act on the resources on the network.
- **Machines**: represents computers. They are considerd security principals**. They are assigned accounts, with username equal the computers name pluss dollar sign "$". The password is automatically rotated, and generally consists of 120 characters.
- **Security groups**: are groups of objects that can be assigned certin privileges, that is automatically inherited by all the the objects in the group. There are several defult groups in a domain. Here are some of the most important ones:

| Security Group |	Description |
|-|-|
Domain Admins|	Users of this group have administrative privileges over the entire domain. By default, they can administer any computer on the domain, including the DCs.
Server Operators|	Users in this group can administer Domain Controllers. They cannot change any administrative group memberships.
Backup Operators|	Users in this group are allowed to access any file, ignoring their permissions. They are used to perform backups of data on computers.
Account Operators|	Users in this group can create or modify other accounts in the domain.
Domain Users|	Includes all existing user accounts in the domain.
Domain Computers|	Includes all existing computers in the domain.
Domain Controllers|	Includes all existing DCs on the domain.

You can obtain the complete list of default security groups from the [Microsoft documentation](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups).

The **"Active Directory Users and Computers"** program can be used on the DC to configure users groups and machines.
In the program, objects are organized in **Organizational Units (OU)**.
A user can only be a part of one OU, at a time.
In the OUs, you can see the users they contain and perform tasks like creating, deleting, modifying them or reseting theis passwords.
Windows automatically creates these default OUs:
- Builtin: Contains default groups available to any Windows host.
- Computers: Any machine joining the network will be put here by default. You can move them if needed.
- Domain Controllers: Default OU that contains the DCs in your network.
- Users: Default users and groups that apply to a domain-wide context.
- Managed Service Accounts: Holds accounts used by services in your Windows domain.

Security Groups and OUs, sound similar. So to clarify, these are the differences:
- **Securitu groups**: are used to grant *permission over resources*, and a user can be a part of *multile* groups at a time.
- **OU**: are used for *applying poicies* and *configurations*, and users can only be a part of *one* OU at a time.

## Task 4

### Delete a OU

To delete the extra OU, on the taskbar, click "view", then "Advanced features". Then right-click the OU, click "Properties". Then select the "Object" tab and disable "Protect object from accidental deletion". Exit the menu, right-click the OU, click "Delete" and follow the wizard.

### Delegate control

To delegate control over a OU, righhtclick it and click "Delegate Control". Click "Add...", then type the name in the textfield, click "Check Names" and then click "Ok". Then select the tasks you want to delegate. Here, you select "Reset user password and force password change at next logon". Lastly, click "Next" and "Finish"

### Reset a pasword

First, connect to the nettwork with the "Remote Desktop Connection" application. Connect to the "thm.local" network. Then log in to the user with the given credentials. Open the terminal and run:

    Set-ADAccountPassword sophie -Reset -NewPassword (Read-Host -AsSecureString -Prompt 'New Password') -Verbose
You will then be promted to type a new password. Remember this password.\
You can also let the user reset their own password by forcing a password change at logon. Run this in the terminal:

    Set-ADUser -ChangePasswordAtLogon $true -Identity sophie -Verbose

You can now log in to Sophies user, the same way you logged in to Phillips account, with the password you just set. You can then find the flag in a file on the desktop.

## Task 5

### Create a OU

To create a OU, rightclick the folder you want to create the OU in, click "New" and select "Organizational Unit". Then type the name of the new OU.

## Task 6

### Change Group Policies

Group Policyes are mangaed through **Group Policy Objects (GPO)**, which are accesed throug the **Group Policy Management** tool. \
GPO policies can be viewed by selecting the GPO, and viewing the "Settings" tab. \
The GPO policies can be changed by right-clicking the GPO and clicking "Edit...". We are going to change the minimum password length policy. Navigate this path: Computer Configurations -> Policies -> Windows Setting -> Security Settings -> Account Policies -> Password Policy. Then change the minimum password length policy to 10. \
Once you have made the change, it might take up to 2 hours for the system to updater ans the change to take effect automaticaly. You can force the update by running this in the terminal:

    gpupdate /force

### Make new Group Policies

Right click the "Group Policy Objects" folder  and click "New". Type the name of the new policy. \
Then change the policy, like shown in the previous task, but with the navigation provided in this task. To apply the policy to a group, you simply have to drag-and-drop the policy to the correct OU.

## Task 7 and 8

Here, there is also no use of the THM Machine. These section are about Authentication Methods and Trees, Forests and Trusts, so here are my notes from the sections.

Two protocols can be used for network authentication in windows domains:

- Kerberos: Used by any recent version of Windows. This is the default protocol in any recent domain.
- NetNTLM: Legacy authentication protocol kept for compatibility purposes.

### Kerberos Authentication

This is a short description of the kerberos authentication process:
1. User sends username and timestamp encrypted with a key derived from their password (User Hash), to **Key Distribution Center (KDC)** on the DC.
2. The KDC returns a **Ticket Granting Ticket (TGT)** encrypted with a *krbtgt key* and a Session key encrypted with the *User Hash*.
![Kerberos Authentication, step 1 and 2](../../Images/THM-AD%20Basics/d36f5a024c20fb480cdae8cd09ddc09f.png)
The user is now authenticated, but stil has to ask for access to each specific services they want to access.
3. The user sends the username and a timestamp, both encrypted with she *Session key*, the *TGT key* still encrypted with the *krbtgt key* and a **Service Principal Name (SPN)** with the name the service the user wants to access, to the KDC.
4. The KDC returns a **Ticket Granting Service (TGS)** encrypted with a *Service Owner Hash* and a *Svc Session Key* encrypted with the *Session key*.
![Kerberos Authentication, step 3 and 4](../../Images/THM-AD%20Basics/84504666e78373c613d3e05d176282dc.png)
The user now has the ticket to the wanted servise. The user now only has to give that ticket to the service to establish a conection. That is done like this.
5. The user sends the username and a timestamp, both encrypted with she *Svc Session key* and the *TGS key* still encrypted with the *Service Owner Hash*
![Kerberos Authentication, step 5](../../Images/THM-AD%20Basics/8fbf08d03459c1b792f3b6efa4d7f285.png)

### NetNTLM Authentication


1. The client sends an authentication request to the server they want to access.
2. The server generates a random number and sends it as a challenge to the client.
3. The client combines their NTLM password hash with the challenge (and other known data) to generate a response to the challenge and sends it back to the server for verification.
4. The server forwards the challenge and the response to the Domain Controller for verification.
5. The domain controller uses the challenge to recalculate the response and compares it to the original response sent by the client. If they both match, the client is authenticated; otherwise, access is denied. The authentication result is sent back to the server.
6. The server forwards the authentication result to the client.
![NetNTLM Authentication, all steps](../../Images/THM-AD%20Basics/2eab5cacbd0d3e9dc9afb86169b711ec.png)

### Trees, Forests and Trust

**Trees** allow for sub-domains under one domain.
**Enterprise Admins** group have access to the entire domain, including its sub-domains.
**Forests** allow for connections between different domains.
**Trust** allows for sharing of resources to users in another domain.