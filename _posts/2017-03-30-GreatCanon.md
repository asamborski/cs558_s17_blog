---
layout: post
title: Github Attack by Great Canon
date: 2017-03-30
author: Heqian Liu, Qi Wang, Jiachen Yao, Heming Chen
---

# Abstract
On March 26, 2015, Github was attacked by large scale DDoS attack. A report released by GreatFrie.org and several other reports suggested that the Great Canon of China orchestrated this attack by injecting malicious javascript into Baidu connections. Our report will mainly focus on demonstrating how China Net connecting to the rest of the Internet through Great Firewall and Great Canon. Moreover, we will analyze how Great Canon plays the role of man-in-the-middle and perform this Github attack. Last we want to speak for ourselves as members inside of the Great Firewall.

# Introduction
On March 16th, 2015, GreatFire.org’s servers were under DDoS attack. Ten days later, GreatFire Github page and cn-nytimes Github page was under the similar attack, which causes Github.com down for 120 hours. This attack is the largest DDoS in site’s history, and is aiming to remove or delete these two web pages.

# Overview
![alt text](https://raw.githubusercontent.com/asamborski/cs558_s17_blog/master/img/GC_Overview.png "Overview")

# Great Firewall
Great Firewall is an on-path system, unlike other in-path firewall. It eavesdrops on traffic between China and the rest of the world, and terminate requests for banned content, by injecting a series forged TCP Reset packets that tell both the requester and the destination to stop communicating. GFW keeps track of connections and reassembles the packets to determine if it should block traffic. GFW is a barrier, not an attacking tool. As an on-path system, the GFW can monitor traffic and inject additional packets, but cannot stop in-flight packets from reaching its destination.
![alt text](https://raw.githubusercontent.com/asamborski/cs558_s17_blog/master/img/GF.png "Great Firewall Traffic Flow")

As the diagram shows above, if Alice inside of GFW intends to talk to Bob in global Internet, when TCP packets reach GFW, GFW will forge TCP reset packets and inject into Alice’s TCP request. In such way, Alice and Bob could not communicate.

# Great Canon

![alt text](https://raw.githubusercontent.com/asamborski/cs558_s17_blog/master/img/GC.png "Great Cannon")

The GC operates as a separate, in-path system
The institution observed Baidu responding as normal to the retransmitted request. And by this they conclude that the GC dropped the request before it reached Baidu, a capability not present in the GFW.
The institute also checked whether the GFW and GC might share the same injector device,14 but found no evidence that they do.  In particular, from a given TCP source port, they sent one request designed to trigger GC injection, followed by a request designed to trigger GFW injection.  They repeated the experiment from a number of source ports.  While packets injected by both the GFW and GC exhibited a similar (peculiar) TTL side-channel indicative of shared code between the two systems, the institute found no apparent correlation between the GFW and GC TTL values themselves. So, there must exist a strong MITM different from the GFW, and by this we infer the existence of GC.
Localization the GC
The institute sent the “Falun” queries from our test system to the Baidu server with TTL values increasing from 1 on up.  They observed that the GC’s TCP Reset injection only occurred when we sent packets with TTL values >= 18, suggesting that the GC acts on traffic flowing between the 17th and 18th hop, same as GFW along the path from our test system to the Baidu server. And by this they infer that GC exist in the same location as GFW, in the backbone of Chinese web providers.
Probalistically working 
The cache capacity test also provides evidence that the GC’s probabilistic choice occurs on the decision to act on a particular flow, and not as some sort of pre-filter for reducing analysis load.  When we succeeded in completely filling the flow cache, subsequently injected packets occurred for different source ports than in the initial test.  If the GC only intercepted a subset of flows to the target IP address, we would expect subsequent injections to appear for the same flows, since most schemes to probabilistically select flows for interception (such as hashing the connection 4-tuple) would select the same set of flows the second time around.

# Influence
Because Github was taken down, some pages that some companies use Github to hold were also taken down.
Github set the two attacked pages censored to Chinese IP.

# Life inside Great Firewall
We think there are two advantages of Great Firewall. The first is to protect the development of  Chinese internet industry. In early twenty Century, when those large internet companies like Google and Facebook had became essential part of people’s daily life, Chinese internet industry was still backward. If there was no Great Wall, people in China would use Google and Facebook instead of the product of Chinese internet industry. Then Chinese internet industry would develop slowly. The second advantage is to main the social stability in China. The population of China is huge and a large part of Chinese people are not well educated. They don’t have the ability to judge the information they get from the internet. Therefore, for those not well educated people, if they see some anti-communist information from internet, they may become an unstabilizing factor of the society. But for those well educated people, the Great Firewall actually doesn’t affect their life. They know how to use VPN to get over the wall so the Great Firewall doesn’t matter.


# Acknowledgement:
Thanks to Professor Goldberg and Ann Ming Samborski.
This work was done without any outside collaboration. 

# Reference:
https://citizenlab.org/wp-content/uploads/2009/10/ChinasGreatCannon.pdf 
http://www.cs.tufts.edu/comp/116/archive/fall2016/ctang.pdf 
https://www.usenix.org/system/files/conference/foci15/foci15-paper-marczak.pdf 
https://github.com/blog/1981-large-scale-ddos-attack-on-github-com
