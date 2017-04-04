---
layout: post
title:  "The Mirai Botnet"
date:   2017-02-12
author: Mark Bestavros, Brian Siao Tick Chong, Tyrone Hou, and Freddie Vargus
---

**Abstract &amp; Timeline**

On September 20th, 2016, the security blog “KrebsOnSecurity” written by Brian Krebs was taken down for several hours by what was believed to be a Distributed Denial of Service attack, or DDoS. Akamai, a Content Delivery Network and Cloud Computing Services company that monitors Brian Krebs’ site, reported that this was in fact a DDoS. The attack was attributed to a new, undocumented type of malware.

On October 21st, 2016, a second massive DDoS attack by the same malware was performed on Dyn, a major Domain Name Service (DNS) provider. This attack was the largest DDoS recorded up to this point, and managed to take down a large part of the internet on the East Coast. Over the course of the next several months, multiple attacks on a smaller scale would be performed on various victims, including Rutgers University.

On September 30th, 2016, ten days after the first attack on Krebs, the source code for the malware was released by its anonymous author, who holds the username “Anna-senpai” on Hackforums. While it is known that Anna-senpai conducted the original attack on Kreb’s blog [3], the instigators who initiated the other attacks are unknown.

The attacks were produced by a malware called Mirai, which converts Internet of Things (IoT) devices running Linux into bots that are remotely controlled as part of a botnet; a network of devices infected with malicious software and controlled as a group without the owner's knowledge. This malware was supposedly based on older IoT malware called BASHLITE [5].

In this report we detail the media response following the high profile attack on Dyn, then explore the technical architecture and details of one attack vector specifically used against Dyn’s servers. We conclude by discussing potential prevention tactics and mechanisms.

**The Superficial/Sensational News Presented in the Press**

The Mirai botnet attacks were covered across all sorts of media sites, from security blogs to company blogs to main sources of news such as CNBC. The mainstream media focused on the sites of Dyn seemingly brought offline in the second DDoS attack. Dyn’s Points of Presence (POPs) on the East Coast were taken down for two hours as well as globally for about an hour on October 21st [8], leaving users unable to connect to Dyn’s large clients such as PayPal, Twitter, Reddit, GitHub, Amazon, Netflix, and Spotify. This resulted in millions of dollars lost across all of the companies, as well as millions of users being unable to use these services. Brian Krebs’ blog, as well as the blogs of companies such as Akamai, Symantec, Incapsula, and Dyn, went further into detail about the attack itself and how much more effective it was than previous DDoS attacks. 

As the victim of the first attack, Brian Krebs had the most in-depth analysis of any of the sites, and has a large exposé covering everything from the first attack, to the author of the malware, to the source code, to the various other attacks that occurred following the initial attack on his own blog.

**Motivations of the Author**

The motivations of the attacker are unknown, although the motivation of the author is known. Anna-senpai, the author, wrote in a post on hackforums saying that many hackers wanted a botnet better than the previously successful botnet called qbot, and so Anna-senpai ended up making a better botnet as a result [1]. As far as incentives, there is not much information on what the author could have gotten out of the botnet other than money, and the credit of making the botnet itself. It is also believed that Brian Krebs, who is known for exposing hackers, exposed one of Anna-senpai’s friends, a co-owner of the DDoS-for-hire service called vDOS, and so that was the reason for attacking Krebs’ site with the botnet [2]. The attack on Dyn, was likely a slight on the large companies affected by the outage, to either gain attention or cause them to lose millions of dollars during their downtime.

**Architecture of the Mirai Botnet**

The Mirai malware has three important components that make the attack effective: the Command &amp; Control server (CNC), the infection mechanism, which the author calls “real-time load” [1], and attack vectors.

The setup used by the author of Mirai consists of two virtual private servers (VPS), one CNC server, and three loader servers. One VPS is used as a database. The second VPS server serves as a report server. Bots that return successful scan results send the IPs of vulnerable devices to the report server, which then distributes the connections to the IoT devices to the three other servers, which are called loaders. The CNC is used as the main point of communication between the attacker and the bots. 

**Infection of Vulnerable Devices**

In terms of how the attack is carried out, there are three steps that are involved. The first step is the successful discovery of vulnerable IoT devices. In order to discover these vulnerable devices, IoT devices are scanned over Wide Area Network (WAN). Upon discovery, the telnet and ssh ports of each device are brute forced with a simple dictionary attack using sixty-three common username and password combinations [6]. This is how the second step of the attack, the infection mechanism, begins.

After successfully discovering a compromisable device, the bots report to a report server that tells a trio of loader servers to send a binary of the malware to the bot to run. This loading is performed in real time as results come into the report server, hence the name “real-time-load”. Once a device is infected with the malware, it will shut off all telnet and ssh ports, and delete processes ending in the .anime extension, which are running processes of “Anime” malware. This “territorial” nature is a unique aspect of the Mirai attack; it removes other malware processes and does not allow other devices to connect while the attack is being carried out. In addition to seeking out and removing competing malware processes, Mirai also takes steps to ensure that its own process cannot be deleted by outside sources. The process name is replaced with a randomly generated alphanumeric string, and the source code is deleted from the bot once the process starts. Once hidden, the bot establishes a connection to the CNC by sending its name and retaining the connection via null packets sent back and forth acting as a “heartbeat” [9]. 

The third step of the attack is the actual distribution of packets to the victim’s servers. The botnet primarily relies on flood attacks that overload target systems with connection requests in order to completely consume machine resources, making the system unresponsive to legitimate user traffic. The botnet has ten different attack vectors for flooding: UDP, Valve Source Engine (VSE) query, DNS, SYN, ACK, ACK Stomp, GRE IP, GRE Ethernet, Plain UDP, and HTTP. All of these vectors, with the exception of VSE, are different types of network protocols that servers utilize in order to connect to each other, and to connect to users within an application or service. VSE is a specific UDP amplification flood attack against gaming servers which use TSource Engine Query [10].

**Attack Mechanisms**

For the Mirai botnet in particular, GRE IP and GRE Ethernet flooding are unique in that these vectors are recent, and had never been used in a botnet of Mirai’s scale before. GRE, or Generic Routing Encapsulation, is a protocol that allows creation of point-to-point connections similar to VPN. GRE allows communication between two computers running potentially different network protocols. The GRE layer of a packet is composed of an extra 24 bytes that contains a new source and destination IP, as well as a GRE header. What is special about this protocol is that although you can spoof the IP address of the inner packet, you cannot spoof the IP address of the encapsulating GRE packet. This property of GRE revealed that tens of millions of IPs were used in the DYN attack [4]. De-encapsulation of the GRE packets cause resource exhaustion, as there are millions of packets sent from each bot, and there are thousands if not millions of bots.

**Preventing the Attacks**

We can consider attack mitigation from the perspectives of IoT device users, manufacturers, and website owners. Considering the nature of the botnet and how quickly it spreads, understanding how to prevent additional devices from being infected could be helpful. As discussed before, the infection mechanism relies on brute-forcing default passwords over WAN, which assumes weak password security and unrestricted access to the network. Clamping down on those by strengthening IoT password security and removing WAN access on IoT devices would make botnet expansion significantly more difficult [6]. However, there are practical considerations that make these solutions infeasible in many cases: Firstly, changing passwords from the default on IoT devices may not even be possible in the first place. Even if IoT devices do allow the default password to be changed, getting the general public to care about the passwords on their IoT devices would be extremely difficult. Requiring a password change upon device activation is another potential solution from the manufacturer, but this would increase user frustration and companies may be hesitant to implement it. Additionally, disabling WAN access may cripple the device’s functionality if it needs any kind of client-server communication and may not even be end-user accessible, just like with passwords.

There are some other methods that rely more on website administrators and Internet providers. Following the Mirai-powered attack on KrebsOnSecurity’s blog, Google’s Project Shield program (which aims to protect academics and journalists from hacking by malicious actors, including governments) began working with the blog to mitigate attacks, eventually developing techniques that allowed the small site to sustain itself even when it was being attacked by a Mirai botnet. These techniques were outlined in a talk given by Damian Menscher at the Enigma security conference in February 2017 [7]. Most of the techniques stem from being to effectively categorize incoming requests as either legitimate or fraudulent (i.e. part of a botnet). For example, if a request comes in from an IP that has visited the site several times before, we can assume that IP is probably from a real user. From there, the site can rate-limit botnet traffic and reduce the impact on the web server while also serving legitimate requests from a cached version of the site, effectively giving the impression of a normally-functioning site to real users. He also mentioned simply having greater network capacity than is really needed, which provides redundancy in case of a DDoS — although this may not be feasible for smaller sites.

**Conclusion**

The attacks that stemmed from the Mirai malware were some of the largest DDoS attacks ever seen, of which the attack on Dyn was declared the largest DDoS in terms of payload and people affected, although it did not have the largest number of bots under its control, compared to other botnets. Aside from its size, the attack introduced many new concepts to the world of Denial of Service attacks, including the new threat of GRE flood attacks, territorial behavior of malware, and increasing attack volume produced from a relatively small number of bots. The attack was also heavily mentioned in many mainstream media sites which is abnormal for these sorts of attacks. The main reason for mainstream media coverage seems to be that, because many large consumer-facing services, such as Amazon, Netflix, and Spotify, were affected by the attack, millions of users were left wondering why they could not access their favorite services and applications, which the media happened to pick up on. Further research is being conducted by many security-focused companies, sites, and people to try and mitigate or prevent attacks like this from occurring again in the future.

**Acknowledgements**

This work was done without any outside collaboration.

**Citations**

1. “Who is Anna-Senpai, the Mirai Worm Author?”, KrebsOnSecurity, Jan 17, 2017. [https://krebsonsecurity.com/2017/01/who-is-anna-senpai-the-mirai-worm-author/](https://krebsonsecurity.com/2017/01/who-is-anna-senpai-the-mirai-worm-author/)

2. “Linux/Mirai, how an old ELF malcode is recycled” blog.malwaremustdie.org, Aug 31, 2016. [http://blog.malwaremustdie.org/2016/08/mmd-0056-2016-linuxmirai-just.html](http://blog.malwaremustdie.org/2016/08/mmd-0056-2016-linuxmirai-just.html)

3. “​The Dyn report: What we know so far about the world's biggest DDoS attack”, ZDNet, Oct 25, 2016. [http://www.zdnet.com/article/the-dyn-report-what-we-know-so-far-about-the-worlds-biggest-ddos-attack/](http://www.zdnet.com/article/the-dyn-report-what-we-know-so-far-about-the-worlds-biggest-ddos-attack/) 

4. Mirai Source Code. [https://github.com/jgamblin/Mirai-Source-Code](https://github.com/jgamblin/Mirai-Source-Code)

5. “KrebsOnSecurity Hit With Record DDoS”, KrebsOnSecurity, Sept 16, 2016. [https://krebsonsecurity.com/2016/09/krebsonsecurity-hit-with-record-ddos/](https://krebsonsecurity.com/2016/09/krebsonsecurity-hit-with-record-ddos/)

6. “Breaking Down Mirai: An IoT DDoS Botnet Analysis”, Incapsula, Oct 26, 2016. [https://www.incapsula.com/blog/malware-analysis-mirai-ddos-botnet.html](https://www.incapsula.com/blog/malware-analysis-mirai-ddos-botnet.html) 

7. “Mirai: A Botnet of Things”, Ixia Blog, Oct 21, 2016. [https://www.ixiacom.com/company/blog/mirai-botnet-things](https://www.ixiacom.com/company/blog/mirai-botnet-things) 

8. “Mirai IoT DDoS Botnet Source Code Reveals Specific Targeting Of Valve Source Engine Games On Steam”, Oct 3, 2016. [http://hothardware.com/news/mirai-iot-ddos-botnet-source-code-targets-valve-source-engine](http://hothardware.com/news/mirai-iot-ddos-botnet-source-code-targets-valve-source-engine) 

9. “Dyn Statement on 10/21/2016 DDoS Attack”, Dyn Blog, Oct 22, 2016. [http://dyn.com/blog/dyn-statement-on-10212016-ddos-attack/](http://dyn.com/blog/dyn-statement-on-10212016-ddos-attack/)

10. “How Google fought back against a crippling IoT-powered botnet and won”, Ars Technica, Feb 2, 2017. [https://arstechnica.com/security/2017/02/how-google-fought-back-against-a-crippling-iot-powered-botnet-and-won/](https://arstechnica.com/security/2017/02/how-google-fought-back-against-a-crippling-iot-powered-botnet-and-won/)



**Other References**

“Linux.Gafgyt Profile” [https://www.symantec.com/security_response/writeup.jsp?docid=2014-100222-5658-99](https://www.symantec.com/security_response/writeup.jsp?docid=2014-100222-5658-99)

“Mirai: what you need to know about the botnet behind recent major DDoS attacks” [https://www.symantec.com/connect/blogs/mirai-what-you-need-know-about-botnet-behind-recent-major-ddos-attacks](https://www.symantec.com/connect/blogs/mirai-what-you-need-know-about-botnet-behind-recent-major-ddos-attacks)

“620+ Gbps Attack - Post Mortem” [https://blogs.akamai.com/2016/10/620-gbps-attack-post-mortem.html](https://blogs.akamai.com/2016/10/620-gbps-attack-post-mortem.html)

“How to not break the Internet” [https://blog.kaspersky.com/attack-on-dyn-explained/13325/](https://blog.kaspersky.com/attack-on-dyn-explained/13325/)

“Double-dip Internet-of-Things botnet attack felt across the Internet” [http://arstechnica.com/security/2016/10/double-dip-internet-of-things-botnet-attack-felt-across-the-internet/](http://arstechnica.com/security/2016/10/double-dip-internet-of-things-botnet-attack-felt-across-the-internet/)

“Massive cyber attack 'sophisticated, highly distributed', involving millions of IP addresses” [http://www.cnbc.com/2016/10/22/ddos-attack-sophisticated-highly-distributed-involved-millions-of-ip-addresses-dyn.html](http://www.cnbc.com/2016/10/22/ddos-attack-sophisticated-highly-distributed-involved-millions-of-ip-addresses-dyn.html)

“DDoS attack that disrupted internet was largest of its kind in history, experts say” [https://www.theguardian.com/technology/2016/oct/26/ddos-attack-dyn-mirai-botnet](https://www.theguardian.com/technology/2016/oct/26/ddos-attack-dyn-mirai-botnet)

“Chinese firm acknowledges inadvertent role in cyberattack” [http://money.cnn.com/2016/10/23/technology/ddos-cyber-attack-chinese-firm/](http://money.cnn.com/2016/10/23/technology/ddos-cyber-attack-chinese-firm/)

“Qbot Botnet Sniffs 800,000 Banking Transactions from More than 500,000 Systems” [http://news.softpedia.com/news/Qbot-Botnet-Sniffs-800-000-Banking-Transactions-From-More-Than-500-000-Systems-461374.shtml](http://news.softpedia.com/news/Qbot-Botnet-Sniffs-800-000-Banking-Transactions-From-More-Than-500-000-Systems-461374.shtml)