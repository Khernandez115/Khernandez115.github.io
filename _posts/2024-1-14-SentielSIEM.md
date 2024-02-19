---
title: "Azure Sentinel SIEM Project"
date: 2024-1-14 00:00:00 +0800
categories: [Blogs]
tags: [SIEM, Azure, Threat Research, Logs]
---
To gain some familiarity using the Azure Cloud and some of the tools that it offers, I found a Sentinel SIEM tutorial by Josh Madakor. The tutorial goes through setting a Virtual Machine(VM) with minimal to no security. This VM is used as a honeypot to attract malicious users. The goal of the tutorial is to get a working SIEM that monitors login attempts with a map visual using the ipgeolocation api. Jordan provides a powershell script that takes the log event 4625 and formats the logs to fit the needs of the SIEM. 

Process of project:

Set up VM with little security and no firewall.

Enable Log Analytics to use log information in SIEM

Configure SIEM to produce map


# Results
The results surpassed my expection of the activity the VM would experience. Below shows roughly 24 hours of running the VM and SIEM, which resulted in over 20,000 failed login attempts from various parts of the world. A majority of the usernames being used for attempted access were variations of 'administrator'. This may mean that it would be beneficial to move away from the 'administrator' and use a different username with admin privileges.

<img src = "/assets/images/SIEM-MAP.png" alt="MAP">

# What's Next?
I plan to do some further research on the data that was collected to see the frequency in usernames and passwords. I'd also be interested in seeing the difference in credentials between regions or if there are an correlations between regions that can point at the use of a proxy.


