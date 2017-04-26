---
layout: post
title: Sony Pictures Hack
date: 2017-04-18
author: Anushree Srinivasan, Aswin Vasudevan
---


# Abstract:
This work aims on analysing the Sony Pictures Hack performed by the guardians of peace against the Sony corporations. The motivation of the attack was that the Sony tried to make a movie “The Interview” showing the dictator of North Korea in bad light. The report details the security aspects of the attack and suggest preventive methods along with the Media and legal viewpoints.

# Timeline:
The image below shows the timeline of the attack and its consequences.

![alt text](https://github.com/ansi7794/cs558_s17_blog/blob/master/img/SonyHack1.png "Timline of Sony Pictures Hack")

# SMB Protocol:
The Sony Corporation provided VAIO laptops which are windows based machines. The system thus follows a Windows Protocol – Server Message Block Protocol. It is an application layer protocol with client-server implementation. It uses file access packets between the server and client. The protocol authenticates the users by challenge response authentication.  It allows connection through port 445.

# Challenge Response Authorization:
![alt text](https://github.com/ansi7794/cs558_s17_blog/blob/master/img/SonyHack2.png "Challenge Response Authentication")

# Null Session Attack:
The windows system allows users to access the shared path folder without a 
username and password as it is inter process communication. This became an advantage to GOP as they can retrieve the basic complexities in configuration to brute force attack. The attacker pretends to be no user via null session and can try infinite combinations. At once it achieves the result, the attacker can act as the normal user and get into the system.   

# Sensitive data leaked:
## Data leaked by GOP:
*	47,000 unique SSN’s
*	Movies - Annie, Mr. Turner, Still Alice and To Write Love on Her Arms
* A copy of the script for the James Bond Film Spectre
* E-mail conversations between Pascal and Scott Rudin
## Data leaked from Wikileaks:
* Over 30,000 documents that were obtained via the hack in April 2015

# Probable Causes & Solutions:
As neither the FBI nor Sony Pictures, has released the actual cause of the of the hack, we have listed some probable ones with possible solutions below:
*	Passwords were within the file system with the files which were protected by them. These files were also named passwords providing easy access to hacker. This could have been avoided by storing passwords in encrypted format and not in plaintext.
*	The passwords were rather easy. For this employees should be asked to set tougher passwords with special character, numericals as well as letters of both cases.
* Windows SMB is susceptible to attacks, while it is speculated that if MAC was used then the attacks could have been avoided.

# Media Views:
The Media reports suggested that the Sony had accepted the conditions of the GOP and had decided not to release the movie. The then president of the united states issued a strong statement claiming that a crime organization cannot dictate terms to our actions. After this statement, Sony denied the reports of it not screening the movie and decided to release in selected areas.   

# Legal Views:
The following are a short summary of the White Papers : 

## FBI press release:
Sony was hacked by GOP. The technical analysis of the data deletion malware used were similar to the one in known attacks by North Korea. Significant overlap was noticed between the infrastructure used in this attack and other malicious cyber activity the U.S. government has previously linked directly to North Korea. FBI discovered that several Internet protocol (IP) addresses associated with known North Korean infrastructure communicated with IP addresses that were hardcoded into the data deletion malware used in this attack.

## US-CERT Alert (TA14-353A):
This document explains how SMB Worm Tool functions. It states that, “Cyber threat actors are using an SMB worm to conduct cyber exploitation activities.  This tool contains five components a listening implant, lightweight backdoor, proxy tool, destructive hard drive tool, and destructive target cleaning tool.” The worm is said to be able to cause the affected organization loss of intellectual property and disruption of critical systems, due to its highly destructive nature.

# References:
*	https://www.washingtonpost.com/news/the-switch/wp/2014/12/18/the-sony-pictures-hack-explained/?utm_term=.6c4b84fa4b6e
*	http://www.huffingtonpost.com/2015/01/02/obama-north-korea-sanctions_n_6407712.html
*	http://techgenix.com/anatomy-nul-attack/
*	http://deadline.com/2006/04/page-six-scandal-qa-120/
*	https://en.wikipedia.org/wiki/Sony_Pictures_hack
*	https://www.fbi.gov/news/pressrel/press-releases/update-on-sony-investigation
* https://www.us-cert.gov/ncas/alerts/TA14-353A
*	https://www.engadget.com/2014/12/10/sony-pictures-hack-the-whole-story/
*	https://www.usatoday.com/story/news/nation-now/2014/12/18/sony-hack-timeline-interview-north-korea/20601645/
*	http://mashable.com/2014/12/02/sony-hack-passwords/#7QaLgmlA95qz
*	https://www.riskbasedsecurity.com/2016/02/sony-a-year-after-the-hack/
*	http://www.hcsw.org/reading/chalresp.txt
