--- 
title: Hacking Windows with EternalBlue
date: 2025-11-14
categories: [pentest]
tags: [eternalblue, windows, metasploit] #tags should always be lowercase
---

**Objective**: Hack into a Windows machine, leveraging common misconfiguration issues

### Recon

First I ran nmap with some specific flags:

`nmap -sV -sC --script vuln -oN blue.nmap 10.201.48.102`

- The `-sV` scans for service names and version numbers
- The `-sC` runs Nmap's default scripts for info gathering, basic vulnerability checks and to discover services
- The `--script vuln` flag and option tells nmap to run all scripts in the "vuln" category. It checks for known issues like EternalBlue, SQL injection, weak configurations, misconfigurations and old CVEs. It checks more aggressively than `-sC`
- The `-oN` flag outputs the results to a file "blue.nmap"

The results were the following:

![nmap1](/assets/img/EternalBlue/nmapresult1.png)

![nmap2](/assets/img/EternalBlue/nmapresult2.png)

**Challenge:**

>*How many ports are open with a port number under 1000?*

Answer: 3

**Challenge:**

>*What is this machine vulnerable to? (Answer in the form of: ms??-???)

The nmap scan showed two different vulnerabilities. `MS12-020` and `MS17-010`
Answer: MS17-010

**Understanding**

The `MS12-020` vulnerability is a vulnerability in the Remote Desktop Protocol. There were two CVEs identified by `nmap`: CVE-2012-0152 and CVE-2012-0002. The first is a RDP vulnerability that could allow remote attackers to cause a denial of service. The second is a RDP vulnerability that could allow remote attackers to execute arbitrary code on the targeted system

The `MS17-010` vulnerability is a vulnerability in Microsoft SMB servers. The CVE identified by `nmap` was CVE-2017-0143, which allows remote code execution through a vulnerability in Microsoft's SMBv1 servers.

### Gaining Access

Now I know the machine is vulnerable to `MS17-010`, I can check Metasploit for exploitation codes that I can use on it.

`msfconsole`

`search ms17-010`

I got a number of results:

![metasploitsearch](/assets/img/EternalBlue/metasploitsearchms17010.png)

**Challenge:**

>*Find the exploitation code we will run against the machine. What is the full path of the code?*

Answer: exploit/windows/smb/ms17_010_eternalblue

**Challenge:**

>*Show options and set the one required value. What is the name of this value?*

`show options`

`set rhosts 10.201.48.102`

`exploit` - to run the exploit

Answer: RHOSTS

### Escalation

We are now connected to the machine and in a meterpreter session. I used `CTRL + Z` to background the session.

Some of the questions in this section ask how we upgrade the shell to a meterpreter shell in Metasploit, which I wasn't exactly sure how to do, because I was thrown in a meterpreter shell by default. 

However, there is a post access module we can use to do this. 

**Challenge:**

>*What is the name of the post module we will use to convert a shell to a meterpreter shell in Metasploit?*

Answer: post/multi/manage/shell_to_meterpreter

You can background your session with:

`use post/multi/manage/shell_to_meterpreter`. 

Set the one option required to set, which is the SESSION option. And then `run` the module. It will upgrade you to a meterpreter shell.

**Challenge:**

>*Verify that we have escalated to `NT AUTHORITY\SYSTEM`

There are two ways to do this. You can use Meterpreter's `getuid` command if in a Meterpreter shell. 

Or you can type Meterpreter's command `shell` to get into a Windows shell, and run the command `whoami` to verify you are `NT AUTHORITY\SYSTEM`. 

To get back to the meterpreter session from the Windows shell, you can background the Windows shell with `CTRL + Z`

If you are `NT AUTHORITY\SYSTEM`, you are God.

What I need to do next is move my session to a more stable process that also matches the architecture of my system. This is important because I am going to be dumping password hashes from this system, by migrating with the `spoolsv.exe` process. If I don't match the architecture of that process, I can't talk to it.

I'm choosing `spoolsv.exe` because it always runs with `NT AUTHORITY\SYSTEM`.

`ps` to show running processes

`migrate 1352` to migrate to the selected process.

In my case, I was already running on this PID. Confirmed with the `getpid` command.

![pid](/assets/img/EternalBlue/proccessespid.png)

### Cracking

**Challenge:**

>*Within our elevated shell, run the command `hashdump`. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?*

Answer: Jon

![img-description](/assets/img/EternalBlue/hashdumpresults.png)

**Challenge:**

>*Copy this password hash to a file and research how to crack it. What is the cracked password?*

I opened a new terminal tab.

`touch jonhash.txt`

`echo Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d::: > jonhash.txt`

`cat jonhash.txt` to confirm the command worked

Now we can use John the Ripper to attempt to crack the hash!

`john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt jonhash.txt`

![johncracked](/assets/img/EternalBlue/johncrackresult.png)

Answer: alqfna22

### Capture the Flags!

Time to capture some flags!

I first ran `pwd` to see where I was in the system. And I just moved around the filesystem from there. 

I found the first flag in `C:\flag1.txt`.

**Challenges:**

>*Flag1? This flag can be found at the system root*

Answer: flag{access_the_machine}

>*Flag2? This flag can be found at the location where passwords are stored within Windows*

Windows passwords are stored at `C:\Windows\System32\Config\SAM`. In this case, I found it one directory up, at `C:\Windows\System32\Config\flag2.txt`

Answer: flag{sam_database_elevated_access}

>*Flag3? This flag can be found in an excellent location to loot.*

I found this flag in the user Jon's Documents directory. `C:\Users\Jon\Documents\flag3.txt`

Answer: flag{admin_documents_can_be_valuable}

That's it! Very fun room!




