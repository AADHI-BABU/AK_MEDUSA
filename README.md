# TryHackMe Blue – Room Walkthrough

## 1. Introduction

This repository documents my learning experience while completing the **Blue** room on TryHackMe.

The room focuses on identifying and understanding the MS17-010 vulnerability, commonly known as **EternalBlue**, affecting Microsoft's SMBv1 implementation.

Through this lab, I learned about vulnerability scanning, Metasploit, Meterpreter, Windows directory structures, and basic post-exploitation concepts in an authorized training environment.

> **Disclaimer:** All activities documented here were performed in the authorized TryHackMe lab environment. Never test these techniques against systems without explicit permission.

## 2. Lab Environment

| Component | Details |
|---|---|
| Platform | TryHackMe |
| Room | Blue |
| Target OS | Windows Server 2012 R2 |
| Target Architecture | x64 |
| Vulnerability | MS17-010 (EternalBlue) |
| Main Tool | Metasploit Framework |
| Session | Meterpreter |
| Protocol | SMB |
| Port | 445 |

**Tools used:**
- Kali Linux
- Metasploit Framework
- Nmap
- Meterpreter

## 3. Understanding the Vulnerability

### What is MS17-010?

MS17-010 is a security vulnerability affecting Microsoft's Server Message Block (SMBv1) protocol.

SMB is a network protocol used by Windows systems for file and printer sharing.

EternalBlue is an exploit associated with this vulnerability.

### Why is it important?

- It affects vulnerable SMBv1 implementations.
- It can allow remote code execution on vulnerable systems.
- It demonstrates the importance of security updates and disabling outdated protocols.

## 4. Vulnerability Identification

The target was identified as a Windows Server 2012 R2 system.

The SMB vulnerability check indicated that the target was likely vulnerable to MS17-010.

**Important observation:**

A vulnerability scanner reporting a positive result does not automatically mean that exploitation will succeed. Configuration, network connectivity, and target stability can affect the result.

## 5. Exploitation – Metasploit

The Metasploit Framework was used in the authorized lab to study the MS17-010 vulnerability.

The module used was:

`exploit/windows/smb/ms17_010_eternalblue`

The relevant configuration included:

- RHOSTS – Target machine address.
- LHOST – Attacker machine's lab network address.
- LPORT – Listening port for the reverse connection.

### Understanding the result

The initial attempt returned:

`Exploit completed, but no session was created.`

This demonstrated that detecting a vulnerability and establishing a successful session are two different things.

After checking the lab's target information and network configuration, the session was established successfully.

## 6. Meterpreter Session

Meterpreter provided an interactive session with the Windows lab machine.

The following commands helped me understand the session:

| Command | Purpose |
|---|---|
| `sysinfo` | Displays target system information |
| `pwd` | Displays the current directory |
| `ls` | Lists files and directories |
| `cd` | Changes the current directory |
| `getuid` | Displays the current user identity |
| `show_mount` | Displays available drives |
| `cat` | Displays the contents of a text file |

### Understanding SYSTEM privileges

The session showed:

`NT AUTHORITY\SYSTEM`

This indicates that the session was running with Windows SYSTEM-level privileges.

SYSTEM is a highly privileged Windows security identity.

## 7. Finding the Flags

The room contains three flags placed in different Windows locations.

### Flag 1 – System Root

**Hint:**

"This flag can be found at the system root."

The system drive's root directory is commonly:

`C:\`

I used Meterpreter's directory navigation to access the system root and locate the flag.

**Flag 1:**

`flag{access_the_machine}`

### Flag 2 – SAM Database

**Hint:**

"I wish I wrote down where I kept my password. Luckily it's still stored here on Windows."

This clue refers to the Windows Security Account Manager (SAM).

The SAM database stores information related to local Windows user accounts.

**Location:**

`C:\Windows\System32\config\`

Important Windows registry files found in this directory include:

| File | Purpose |
|---|---|
| SAM | Local user account database |
| SYSTEM | Windows system configuration |
| SECURITY | Security-related configuration |

The flag was located in this directory.

**Flag 2:**

`flag{sam_database_elevated_access}`

### Flag 3 – Administrator's Files

**Hint:**

"This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved."

This clue points towards Windows user profiles and their personal directories.

The Administrator profile is commonly located at:

`C:\Users\Administrator\`

Important profile directories include:

- Desktop
- Documents
- Downloads
- Pictures

During my investigation, I checked the Administrator profile's Desktop and Documents directories. The flag has not yet been confirmed.

**Flag 3:** Pending – to be updated after completing the task.

## 8. Important Concepts Learned

### SMB

Server Message Block is a network protocol used for sharing files, printers, and other resources.

### EternalBlue

EternalBlue is an exploit associated with the MS17-010 SMB vulnerability.

### Meterpreter

Meterpreter is an advanced Metasploit payload that provides an interactive session for authorized security testing.

### SAM Database

The Security Account Manager stores local Windows account information.

### SYSTEM Privileges

`NT AUTHORITY\SYSTEM` is a highly privileged Windows security identity.

### Windows User Profiles

Windows stores individual users' files and settings in separate profile directories, usually under `C:\Users\`.

## 9. Lessons Learned

By completing this room, I gained practical exposure to:

1. Understanding the MS17-010 vulnerability.
2. Learning the role of SMB in Windows networking.
3. Understanding vulnerability detection versus successful exploitation.
4. Learning the basic structure of Metasploit.
5. Understanding Meterpreter sessions.
6. Identifying Windows system directories.
7. Understanding the purpose of the SAM database.
8. Learning about Windows user profiles and privileges.

## 10. Conclusion

The TryHackMe Blue room provided practical exposure to Windows security and the MS17-010 vulnerability.

It helped me understand how outdated services can introduce security risks and why regular patching, secure configurations, and network security monitoring are important.

This walkthrough represents my learning progress in an authorized cybersecurity lab.

---

**Platform:** TryHackMe  
**Room:** Blue  
**Focus:** Windows Security | SMB | MS17-010 | Metasploit | Meterpreter

**Note:** Flag 3 and the final completion details will be updated after the remaining task is completed.# AK_MEDUSA
