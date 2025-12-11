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
![](images/)


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
![War deployed](images/war-deployed.png)

• Before I lauched the shell within the panel, I setup a netcat listener in order to catch the shell so I could execute commands from my attacker machine (kali).
![Netcat listener](images/nc-listener.png)
![](images/.png)
## Recommendations
## MITRE ATT&CK Mapping
## Conclusion
## Lessons Learned
![](images/.png)
