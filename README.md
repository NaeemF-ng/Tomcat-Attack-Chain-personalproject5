## Tomcat-Attack-Chain-personalproject5

## Overview
• In this project I demonstrated what an attacker might do after gaining external access, including initial enumeration, credential harvesting, SSH key extraction, privilege escalation, persistence, lateral movement preparation. 

## Objective
• Perform a full attack chain simulation by identifying vulnerable services, gaining initial access through a vulnerable service,  gathering critical information, and establishing total control over the target system through post-exploitation, persistence techniques, and credential harvesting

## Architecture
I completed this simulation in my homelab using both my kali and ubuntu vitual machines. They were on a NAT network called "LAB" which is isolated from the internet making it a great environment for this project.
![](images/)

## Tools Used
• nmap 
• msfvenom
• hydra
• netcat 

## Recon/ Scanning methodology
nmap -A -sC (Metasploitable-ip)
![nmap scan](images/namp-scan.png)
![nmap scan](images/namp-scan2.png)
![nmap scan](images/namp-scan3.png)
![nmap scan](images/namp-scan4.png)
![nmap scan](images/namp-scan5.png)
![nmap scan](images/namp-scan6.png)


## Attack Methodogy
• Looking at the results from my nmap scan I could see that the target was metasploitable which is notorious for being extremely vulnerable leaving many services exposed creating a large. Although there were many options in regarding the attack surface, I chose to attempt to gain initial access through the eposed service tomcat on port 8080. Tomcat's web is known for using default credentials for it's login panel.

• I ran a hydra brute force attack against the Tomcat login page located at the /manager/html directory where you are prompted to enter to the correct credentials needed in order to access the backend web application manager page to test if the target was using default credentials. 
![Tomcat brute force](images/tomcat-bf.png)
![Tomcat login prompt](images/tomcat-entry.png)


## Findings
• Tomcat was configured with default credentials which as a reslut granted me access to the web application manager page. On this page I see that it was allowed war extension files to be uploaded and executed, I immidiately thought of creating and uploading a war reverse shell with msfvenom. 
![Tomcat Manager Page](images/tomcat-manager-page.png)
![War reverse shell](images/war-reverse-shell.png)
![War shell uploaded](images/war-uploaded.png)
![War rev shell webpage](images/reverse-webpage.png)
![War deployed](images/war-deployed.png)


• Before I lauched the shell within the panel, I setup a netcat listener in order to catch the shell so I could execute commands from my attacker machine (kali). I then caught the shell and began trying to find information about the target as well as the stabilizing the shell, the reason being for more functionality and durability. There are many ways to stabilize your shell, as you can see i tried to using python3 and it didn't work but when I used the regular version of python it worked. 
![Netcat listener](images/nc-listener.png)

• After stabilizng my shell I began to look through the target to see what I had access to, pivot to other users and access their files as well, I didn't have much permissions. This led me to attempt to escalate my privileges to the root use, I searched for suid binaries as those can possibly have vulnerabilities within them that I could take advantage of. 
![Looking through the target](images/priv-esc1.png)
![](images/priv-esc2.png)
![](images/priv-esc3.png)
![](images/priv-esc4.png)
![](images/priv-esc5.png)

• I discovered that there was a vulnerable nmap suid binary that is usually never there. I went on GTFObins and saw that the nmap binary could be used to escalate privileges. It would affect all of the older versions nmap so I strongly felt that it would work considering mostly everything on this machine was outdated and exposed. 
![Vulnerable suid binary](images/priv-esc6.png)
![GTFObins-screenshot](images/gtfo-bin-ss.png)

• The nmap suid binary allowed me to escalate privileges resulting in me becoming the root user
![Root user](images/priv-esc7.png)

• Once I became root I went back to the user msfadmin's directory to see to confirm that i'd be able to view valuable information such as ssh keys. I was able to view that information so I extracted msfadmin's ssh key to my attacker machine to  
![msfadmin access](images/home.png)
![msfadmin access](images/home2.png)

• I was able to view that information so I extracted msfadmin's ssh key to my attacker machine as I could use it later to log back in.
![msfadmin id_rsa transfer](images/idrsa-transfer.png)
![msfadmin id_rsa transfer](images/idrsa-transfer2.png)
It didn't work here because I was supposed to configure ssh a certain way on the target machine and I didn't know I had to so this would be more of an architectural error on my end but here would be next steps to log in using the ssh key:
![Logging in with the ssh key](images/idrsa-next-steps.png)

• To establish persistence within the taregt I added a reverse shell to the .bashrc file so that when the user logs in i'd get a shell
![Persistence](images/persistence.png)

• I transferred everything in the /etc/shadow file to attacker machine just to have all of the information about the target regarding personell and anything else valuable. 
![/etc/shadow](images/etc-shadow.png)

• I took the hashes for the user and msfadmin accounts from the target and cracked them as another means to gain access
[user hash crack](images/hashcrack1.png)
[msfadmin hash crack](images/hashcrack2.png)


## Recommendations
• Remove fefault credentials
• Restrict access to the Tomcat interface to trusted IP's only
• Disable war file uploads 
• Regularly check for msiconfigured SUID binaries
• Check for outbound connections as they can be C2 activity or reverse shells


## MITRE ATT&CK Mapping
• Initial Access (T1078) – Used valid/default Tomcat credentials
• Execution (T1059) – Command execution through the uploaded WAR reverse shell
• Persistence (T1547) – From the reverse shell added to the .bashrc
• Privilege Escalation (T1548) – Abuse of the vulnerable SUID nmap binary
• Credential Access (T1003) – Extraction of /etc/shadow and SSH keys
• Lateral Movement / Alternate Access (T1021) – The cracked passwords the use of ssh keys for re entry
• Collection (T1005) – Gathering sensitive user and system data

## Conclusion
This engagment demonstrated a complete attack chain and how attackers can quickly escalate their attacks from such a small msiconfiguration such as weakly protected management interface after gaining an initial foothold. The combination of outdated system software, weak credentials, and SUID binaries allowed me to carry out a full attack chain. 

## Lessons Learned
• Small flaws in a systems security can have drastic consequences
• Familiarity with msfvenom reverse shells, uploading them, and executing them from a manager dashboard
• Catching a shell with netcat, stabilizing the shell and the importanace of doing so
• Finding SUID binaries
• SUID priv escalation with GTFObins and finding SUID binaries


