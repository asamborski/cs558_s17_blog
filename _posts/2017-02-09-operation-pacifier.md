---
layout: post
title:  "Operation Pacifier"
date:   2017-02-09
author: Ben Getchell, Eric Mooney, Luke Osborne, and Dor Rubin 
---

**Abstract**

In 2015, the FBI took down a website on the dark web called "Playpen"
that was serving child pornography. They carried out this attack by
seizing the physical server, which they were able to locate due to a
misconfiguration of the server as a Tor hidden Service. They then hosted
Playpen themselves and used an exploit in the Tor browser to install
malware on the computers of visitors to the site. This investigation has
led to charges being brought against over one hundred alleged users, but
also raises concerns about the power of the FBI to break the anonymity
that Tor is supposed to provide. This article analyzes both the
technological and ethical issues surrounding this hack.

**The superficial/sensational issues presented in the press.**

In December 2014, the FBI received a tip from an undisclosed foreign
intelligence agency that a Tor Hidden Service website called "Playpen"
was hosting child pornography. The tip also stated that the server
hosting Playpen had been misconfigured and it's real IP address was
publicly visible from outside the Tor network.[1]

In early 2015, the FBI began its formal investigation and tracked down
the physical server hosting the site using the site's IP address. They
obtained a warrant and seized that server from a hosting service in
North Carolina and returned it to FBI offices in Virginia. Then the FBI
received another warrant for use in the Eastern District of Virginia to
use some malware, which they call NIT (for "Network Investigative
Technique"), they had developed to track individuals using the site.
From February 20 through March 4, 2015, the FBI hosted the child
pornography website from its offices in Newington, VA.[2]

During this time, the FBI NIT copied identifying information including
the real IP addresses and MAC information from over 1300 computers[3]
visiting the site from all over the world and sent it back to the FBI
headquarters. As a result of the investigation, at least 137 cases have
been brought against alleged users in the United States.[4]

The Tor Browser is actually based on a version of Mozilla's Firefox
Browser. In May 2015, Mozilla requested details of the vulnerability
from the FBI so that it could protect its Firefox users from being
exploited by this non-public vulnerability.[5] And to date, the FBI has
refused to disclose the details of the exploit used.

News of this investigation has raised many ethical and legal issues. Was
it legal for the FBI to use its warrant to infect computers outside the
Eastern District of Virginia? Should the evidence be admissible in the
court cases brought against alleged users? Was it ethical for the FBI to
host child pornography from its offices as part of the sting
operation?[6] Should users visiting or hosting illegal content on the
internet be protected under the 4th amendment? Is it ethical for the FBI
to withhold knowledge of a zero-day vulnerability within the Tor/Firefox
browsers?[7] We hope this article provides the reader with enough
background to formulate an opinion on these questions.

**Tor Background**

Tor, originally named "The Onion Routing", is an an open source software
and anonymity network. Originally developed by the US Naval Research
Laboratory, Tor was used as a way to protect intelligence communication.
Today, it is owned and maintained by the non-profit organization, The
Tor Project Inc.

The two major components of Tor are the software and the network. Though
tightly intertwined, the distinction is made to highlight that one
cannot exist without the other. Tor software leverages a technique known
as Onion Routing. Onion Routing encapsulates digital messages multiple
layers of encryption that are decrypted or "peeled back" at each hop
throughout the Tor network.

![image](https://upload.wikimedia.org/wikipedia/commons/e/e1/Onion_diagram.svg)

The diagram[8] above provides an example message transmitted from Alice
(source) to Bob (destination) through the network. The different color
cylinders represent unique layers of encryption that can only be
decrypted by specific nodes. After the targeted recipient removes a
layer, the next layer reveals instructions for which node to send it
next. The first node Alice comes in contact with is the Guard (Router
A). This node knows that it came from Alice and needs to go to Router B,
but does not know the final destination of the packet. Once it arrives
at the Middle Relay (Router B), the node does not know the origin of the
message or the destination, but only that it came from Router A and
needs to be passed along to Router C. The last stop within the Tor
Network is the Exit node (Router C). It transmits the original message
to the final destination.[9]

Encryption utilizes a stream cipher, specifically a 128-bit AES in
counter mode with an initialization vector of all 0s. For a public-key
cipher, Tor uses RSA with 1024-bit keys and a fixed exponent of 65537.
It also uses OAEP-MGF1 padding with SHA-1 as its digest function.[10]

**Tor Hidden Services Background**

Tor Hidden Services act on the Tor network as websites do on the open
internet, with the difference being that for a Tor Hidden Service, both
the server's location (IP address) and the client's are anonymized
through the Tor network[11]. Tor Hidden Services might also be used to
host instant messaging services, allowing for real-time anonymized
communication. The [Tor Hidden Service
Protocol](https://www.torproject.org/docs/hidden-services), which we
will summarize below, lays out the details of how this anonymization is
preserved. It is important to note, however, that without proper
configuration of the server, one's location can be found, which is
exactly what happened in the Playpen case.

To keep both parties anonymous, both the server and the client
communicate through intermediate nodes, each connected to either side
via a multi-step Tor relay, as described above, so that their location
cannot be discovered from either end. To access a Tor Hidden Service,
one must first learn it's "onion" address (for example "xyz.onion"),
which is similar to a URL. Onion addresses are only accessible for users
of the Tor network.

Configuring a Tor Hidden Service is similar to setting up a web server
on the open internet, with a few extra steps involved. First, one sets
up a server in some way (say, using Python or Apache), and then hosts
the server only to his or her local network. Then, to make it a Tor
Hidden Service, the host (having previously installed Tor), edits a
configuration file called "torrc", providing information about the path
to the server files and which IPs and ports the server uses.[12] Then,
upon starting Tor, a private key is generated along with a hostname,
which is the onion address mentioned above. The Tor network is then
linked to the server, and though it is not accessible to the open
internet, anyone using Tor who is provided with the onion address can
now anonymously access this website.

Tor uses a distributed hash table to keep track of its database of
Hidden Services[13]. This means that it's possible for a user to check
whether an onion address is valid or not by hashing it and checking it
against the hash table. If someone has access to the hash table,
however, it is not possible to determine the onion addresses contained
in the database from the hashes alone.

The key step that the host of Playpen got wrong was in setting up the
web server. Instead of only hosting the site to his local network, he
instead had his computer and router setup to broadcast the site to the
open internet, as a public IP address that anyone could visit if the IP
address was guessed or stumbled upon.[14] According to the Electronic
Frontier Foundation, a foreign law enforcement agency then tipped off
the FBI about this misconfiguration, and the FBI was then able to find
the location of the server. If the server had been set up correctly, the
FBI would not have been able to seize it and carry out the next step of
its attack--injecting malware to the users of Tor using an exploit in
the Tor browser.

**Best Practices for Using Tor/Tor Hidden Services**

Tor is a powerful tool for protecting one's privacy online, but it
depends on proper cybersecurity hygiene on the user's end as well.
[Tor's
website](https://www.torproject.org/download/download.html.en#Warning)
has a list of best practices for getting the most out of Tor, which
include disabling browser plugins, using HTTPS versions of websites, not
torrenting, and not opening documents while connected to the internet
(especially DOC and PDF files). These suggestions, while sound, are not
the limit of what the user can do to protect themself. In addition to
basic security practices like keeping one's computer up-to-date as
software updates are released, one can also do things like disable Flash
and JavaScript, use custom Linux distributions like Tails ([which Edward
Snowden uses](https://www.wired.com/2014/04/tails/)), or set up a
firewall to limit internet connections to anything other than Tor[15].

**Motivation of FBI**

The incentives in this case are pretty straightforward--the FBI wanted
to take down a child pornography distribution ring. While their decision
to host the content of Playpen on their own servers for some time might
raise some ethical concerns, they could easily argue that if they did
not maintain the facade of actually being the server, they wouldn't have
been able to find out the location of so many users. In this attack, the
harm to the "victims" is that their identity was revealed, allowing them
to be punished for their crimes.

Beneath the surface of the criminal investigation however there are some
ethical issues that offer food for thought. By refusing to release the
details of what exploit they found in the Tor Browser, one might argue
that the FBI is leaving users of Mozilla Firefox exposed to a malicious
attacker using the same exploit on innocent internet users.

**Ethical Issues with Tor**

In the popular media, Tor is often portrayed as the "dark web", which
can lead to negative connotations about its origin and intended usage.
According to a prior version of the Tor Project Overview page, "Tor was
originally designed, implemented, and deployed as a third-generation
onion routing project of the U.S. Naval Research Laboratory. It was
originally developed with the U.S. Navy in mind, for the primary purpose
of protecting government communications."[16]

However, when Tor launched publicly almost a decade later the project
leader Roger Dingledine claimed that Tor was developed "with civil
liberties in mind".[17] Today, the dedicated section of Tor's purpose to
protect government communications has been replaced with the focus of
"volunteer-operated servers that allows people to improve their privacy
and security on the Internet."[18] Journalists, political dissidents,
whistleblowers and stalker victims may use Tor to help protect their
online privacy through the shield of anonymity for noble and just
reasons.

For example, during the ongoing Syrian Civil War, blogger Tariq Biasi
was arrested and sentenced to three years in prison for "dwindling the
national feeling" when he allegedly posted a critical comment about the
regime. As censorship in Syria takes hold, other bloggers such as Anas
Qtiesh can write and share their ideas safely behind the anonymity of a
Tor Browser.[19]

Tor usage, however, is not always so righteous. The Tor community also
consists of many users leveraging the anonymity of the service to engage
in illicit behavior. According to a study by Daniel Moore, a PhD
candidate in the Department of War Studies at King's College London,
almost 57% of active Tor Hidden Services linked to sites categorized as
illicit. Of the 1,547 categorized illicit sites, 122 were labeled as
illegitimate pornography (see image).[20]

![image](http://www.tandfonline.com/na101/home/literatum/publisher/tandf/journals/content/tsur20/2016/tsur20.v058.i01/00396338.2016.1142085/20160201/images/medium/tsur_a_1142085_f0002_b.gif)

Exit nodes are especially exposed members of the Tor network. Whether a
user ("Alice") accesses a site that is legal or illegal, the receiver
("Bob") sees the incoming IP request coming from the Exit node of Tor
relay, not Alice's IP address. If Alice used Tor to browse cat videos,
this isn't an issue for the users acting as the Exit node. However, Exit
nodes can face legal action due to illicit behavior stemming from their
IP address. Luckily, as an Exit node, there is some ability to whitelist
safe browsing services and blacklist nefarious ones, with Tor routing
users accessing nefarious sites through exit nodes willing to assume
such risk.[21] Though, if an Exit allows for more precarious services
such as email or Hidden Services, it is best to prepare legally by
taking measures such as notifying their ISP, setting up a separate IP
for their own web traffic, or even creating a Limited Liability
Corporation to run the Exit node.[22]

**Rule 41 Legal Considerations**

In order for the FBI to use the server they seized to go after users of
the site, they applied for and were granted a second warrant under Rule
41. The Federal Rules of Criminal Procedure govern how federal criminal
prosecutions are carried out in US district courts and the general trial
courts of the US government. In particular, Rule 41 determines the
process through which warrants can be issued and how they may be used by
federal law enforcement to conduct searches and seizures. At the time of
the Playpen case, Rule 41 only allowed searches and seizures via a
warrant from a magistrate judge to be executed within the district that
the warrant was issued.[23]

One can see how eliminating this feature of Rule 41 might jeopardize
protections guaranteed by the 4th Amendment - if a judge in Nevada could
authorize the search of a computer in Arkansas, federal law enforcement
could effectively seek out judges that might be more lenient with
respect to a particular investigation. By weakening this rule, the legal
obstacles normally encountered in the process of obtaining a warrant in
cases involving the internet would be much more easily overcome.

**In the context of the case**

After identifying the location of the Playpen server, obtaining a
warrant to seize it, and doing so, the FBI continued to host content on
the site for two weeks.[24] The ethics of a federal organization hosting
and distributing child pornography is an entire issue unto itself,[25]
but what is important with respect to Rule 41 and the 4th Amendment is
that the vast majority of users who accessed child pornography stored on
the server did so in districts outside of the one in which the FBI
obtained the warrant.

It's important to note that the FBI didn't simply obtain the IP
addresses of users when they accessed the server. As discussed above,
the function of a properly configured Tor Hidden Service is to prevent
both user and host from obtaining identifying information about one
another. However, due to a vulnerability in the Tor browser itself, the
NIT deployed by the FBI was able to obtain identifying information (MAC
address, IP address, OS, etc.) from each user's machine, which was then
forwarded to a server at the FBI. This effectively amounted to a search
and seizure of personal property outside of the district authorized by
the warrant, which (at the time) could not be authorized by a single
magistrate. In many of the cases resulting from the FBI's investigation,
evidence has been thrown out due the fact that the actions taken by law
enforcement were determined to be in violation of Rule 41.[26]

Lacking key evidence, many cases have stalled and could potentially be
dismissed. Lawmakers understood that the FBI couldn't have possibly
known where the users were located prior to deploying the NIT, making it
impossible to obtain a warrant for each district in which a user
accessing Playpen was located. Perhaps the FBI realized this might
happen, but also had no other way to identify the users, and so went
ahead with the investigation, knowing that at worst the cases would be
dismissed. Recognizing the importance for legal procedure to keep pace
with advances in technology, the Department of Justice sought to
introduce changes to Rule 41 in order to make it possible to apprehend
criminals in these types of cases.

**Post-Playpen**

In December of 2016, Rule 41 was amended to include the following
provision:

"a magistrate judge ... has authority to issue a warrant to use remote
access to search electronic storage media and to seize or copy
electronically stored information located within or outside that
district if [...] the district where the media or information is located
has been concealed through technological means"[27]

The expression "concealed through technological means" is left with no
further definition, and is yet to be interpreted in a court, which
raises serious questions as to how it will be applied in the future.
What is certain, however, is that anyone using Tor or a VPN on the
internet is certainly now within the scope of the amendment. The EFF has
even speculated that machines infected with malware such that they act
as part of a botnet could be subjected to it[28] (since DDoS attacks,
when implemented via a botnet, typically involve IP spoofing). This
seems like a bit of a stretch, since IP spoofing is simply creating IP
packets with false IP addresses, but it's alarming that such obvious
ambiguity could be written into a law which gives federal law
enforcement so much unchecked power. One could even see a judge
interpreting "technological means" to be an IP address itself--after
all, there is not a direct mapping from IP address to district hardcoded
anywhere.

With around 500 magistrate judges across the country,[29] law
enforcement can now essentially pick any one they like if they want to
remotely search a US citizen's computer in any part of the country. The
requirement is simply that some 'technological means' are preventing
them from determining the location of a device, and choosing which
magistrate will be the least skeptical of the search.

Furthermore, since the FBI has since classified the source code for the
NIT they used in the Playpen hack, it is unlikely that they will have to
disclose it in future cases[30]. For a government organization to have
no legal oversight in how it constructs and executes malware is
extremely dangerous, since it leaves the public entirely in the dark
with respect to its overall effect. What, for example, is currently
stopping the FBI from developing malware that can infect other
computers, perhaps with completely unintended consequences?[31]

Giving federal law enforcement such sparse restrictions on which devices
they can search, while simultaneously requiring little oversight in the
means they use to get access to the device itself, allows them to take
advantage of Zero-Day exploits without disclosing them to the general
public.[32] This of course puts all internet users at risk in exchange
for greater power in the hands of law enforcement, since those same
exploits could be known to individuals with malicious intent.

**Discussion**

It goes without saying that we have no sympathy for child pornographers,
and it's deeply disturbing that many of them could be let go on the
grounds of a legal technicality. Time will tell how the amendment to
Rule 41 will be interpreted in courts, but one has good reason to be
worried whenever a single case, especially one as sensational and awful
as child pornography, gives rise to sweeping changes in legislation that
potentially allow law enforcement to have indiscriminate power over such
a wide class of citizens. Deploying malware in the absence of rigorous
legal oversight leaves open wide avenues for abuse, and any expansion of
law enforcement like this one ought to be coupled with equal
restrictions in the form of legal oversight.

**Citations**

1.  ["The Playpen Cases: Mass Hacking by U.S. Law Enforcement",
    Electronic Frontier Foundation
    (EFF)](https://www.eff.org/cases/playpen-cases-mass-hacking-us-law-enforcement)
2.  ["The FBI's 'Unprecedented' Hacking Campaign Targeted Over a
    Thousand Computers", motherboard.vice.com, Accessed
    01/23/2017](https://motherboard.vice.com/read/the-fbis-unprecedented-hacking-campaign-targeted-over-a-thousand-computers)
3.  [FBI ran website sharing thousands of child porn images", USA Today,
    Jan 21, 2016, Accessed
    01/23/2017,](http://www.usatoday.com/story/news/2016/01/21/fbi-ran-website-sharing-thousands-child-porn-images/79108346/)
4.  ["The Playpen Cases: Frequently Asked Questions", EFF, Aug 25, 2016,
    Accessed
    01/23/2017](https://www.eff.org/pages/playpen-cases-frequently-asked-questions)
5.  ["The FBI Is Classifying Its Tor Browser Exploit Because 'National
    Security'", motherboard.vice.com, June 24, 2016, Accessed
    01/23/2017](https://motherboard.vice.com/read/the-fbi-is-classifying-its-tor-browser-exploit)
6.  [The Ethics of a Child Pornography Sting", New York Times, Jan 27,
    2016, Accessed
    01/23/2017](http://www.nytimes.com/roomfordebate/2016/01/27/the-ethics-of-a-child-pornography-sting)
7.  ["FBI Refuses to Divulge How It Tracked Pedophiles on Tor",
    gizmodo.com, Mar 30, 2016, Accessed 01/23/2017
    "](http://gizmodo.com/fbi-refuses-to-divulge-how-it-tracked-paedophiles-on-to-1767933079)
8.  ["SVG Diagram of the "Onion Routing" Principle", wikipedia.org,
    Accessed
    01/24/2017](https://commons.wikimedia.org/wiki/File:Onion_diagram.svg)
9.  ["How Tor Works", WeFightCensorship.org, 08/05/2013, Accessed
    01/23/2016](http://www.wefightcensorship.org/tr/node/28html.html)
10. ["Tor Protocol Specification", Torproject.org, Accessed
    01/24/2017](https://gitweb.torproject.org/torspec.git/tree/tor-spec.txt)
11. ["Tor Hidden Service Protocol", Torproject.org, Accessed
    01/23/2017](https://www.torproject.org/docs/hidden-services.html.en)
12. ["Configuring Hidden Services for Tor", Torproject.org, accessed
    01/23/2017](https://www.torproject.org/docs/tor-hidden-service.html.en)
13. ["Tor Hidden Service Protocol", Torproject.org, Accessed
    01/23/2017](https://www.torproject.org/docs/hidden-services.html.en)
14. ["Playpen: The Story of the FBI's Unprecedented and Illegal Hacking
    Operation", EFF, Sept 15, 2016, Accessed
    01/23/2017](https://www.eff.org/deeplinks/2016/09/playpen-story-fbis-unprecedented-and-illegal-hacking-operation)
15. ["Best practices for Tor use, in light of released NSA slides"
    stackexchange.com, Oct 7, 2013, Accessed
    01/24/2017](http://security.stackexchange.com/questions/43369/best-practices-for-tor-use-in-light-of-released-nsa-slides)
16. ["Tor Project Overview Archive", Jun 26, 2014, Accessed
    01/24/2017](http://web.archive.org/web/20140626020652/https:/www.torproject.org/)
17. ["Roger Dingledine about the TOR-Project", NetzpolitikTV Interview
    May 22, 2012, Accessed
    01/24/2017](https://youtu.be/itMZ0Qq-rGk?t=1m35s)
18. ["Tor Project Overview", Torproject.org, Accessed
    01/24/2017](https://www.torproject.org/about/overview.html.en)
19. ["Dissent Made Safer", MIT Technology Review, Apr 21, 2009, Accessed
    01/24/2017](https://www.technologyreview.com/s/413091/dissent-made-safer/)
20. ["Cryptopolitik and the Darknet", Daniel Moore and Thomas Rid, Feb
    01, 2016, Accessed
    01/24/2017](http://dx.doi.org/10.1080/00396338.2016.1142085)
21. ["Tor Project FAQ", Torproject.org, Accessed
    01/24/2017](https://www.torproject.org/docs/faq.html.en#ExitPolicies)
22. ["Tips for Running an Exit Node", Torproject.org, Jun 30, 2010,
    Updated Feb 25, 2015, Accessed
    01/24/2017](https://blog.torproject.org/blog/tips-running-exit-node)
23. ["Playpen: The Story of the FBI's Unprecedented and Illegal Hacking
    Operation", EFF, Sept 15, 2016, Accessed
    01/23/2017](https://www.eff.org/deeplinks/2016/09/playpen-story-fbis-unprecedented-and-illegal-hacking-operation)
24. ["FBI ran website sharing thousands of child porn images", USA
    Today, Jan 21, 2016, Accessed
    01/23/2017,](http://www.usatoday.com/story/news/2016/01/21/fbi-ran-website-sharing-thousands-child-porn-images/79108346/)
25. ["FBI Apparently Made Darkweb Child Porn Site Faster During Its
    Hosting Of Seized Server", techdirt.com, Aug 23, 2016, Accessed
    01/24/2017](https://www.techdirt.com/articles/20160823/09364735314/fbi-apparently-made-darkweb-child-porn-site-faster-during-hosting-seized-server.shtml)
26. ["Judge: child porn evidence obtained via FBI's Tor hack must be
    suppressed", Arstechnica.com, Sept 21, 2016, Accessed
    01/24/2017](https://arstechnica.com/tech-policy/2016/09/judge-child-porn-evidence-obtained-via-fbis-tor-hack-must-be-suppressed/)
27. ["Rule 41. Search and Seizure", Federal Rules of Criminal Procedure,
    Legal Information Institute - Cornell University Law School,
    Accessed
    01/23/2017](https://www.law.cornell.edu/rules/frcrmp/rule_41)
28. ["The Playpen Story: Rule 41 and Global Hacking Warrants", EFF, Sept
    26, 2016, Accessed
    01/23/2017](https://www.eff.org/deeplinks/2016/08/illegal-playpen-story-rule-41-and-global-hacking-warrants)
29. ["FMJA History", Federal Magistrate Judge
    Association](http://www.fmja.org/about-us.html)
30. ["The FBI Is Classifying Its Tor Browser Exploit Because 'National
    Security'", motherboard.vice.com, June 24, 2016, Accessed
    01/24/2017](https://motherboard.vice.com/en_us/article/the-fbi-is-classifying-its-tor-browser-exploit)
31. ["Rule 41 would make it easier for the government to carry out
    hacks", Arstechnica.com, Apr 29, 2016, Accessed
    01/24/2017](https://arstechnica.com/tech-policy/2016/04/rule-41-would-make-it-easier-for-the-government-to-carry-out-hacks/)
32. ["ACLU Comments on Proposed Amendment to Rule 41", Apr 4, 2014,
    Accessed
    01/24/2017](https://www.aclu.org/other/aclu-comments-proposed-amendment-rule-41?redirect=aclu-comments-proposed-amendment-rule-41)

**Other References**

-   ["After global FBI hacking operation, admin of dark web's largest
    child porn site sentenced to 20 years", www.cyberscoop.com, Jan 12,
    2017, Accessed
    01/23/2017,](https://www.cyberscoop.com/fbi-playpen-sentence-government-hacking/)
-   ["Attacking Tor: how the NSA targets users' online anonymity",
    theguardian.com, Oct 4, 2013, Accessed
    01/23/2017,](http://cyber-peace.org/wp-content/uploads/2013/06/Attacking-Tor_-how-the-NSA-targets-users-online-anonymity-_-World-news-_-theguardian.pdf)
-   ["Admin's Mistake Led To The Shutdown Of Playpen", darkwebnews.com,
    May 31, 2016, Accessed
    01/23/2017](https://darkwebnews.com/dark-web/playpen-shutdown/)
-   ["Former Tor developer created malware for the FBI to hack Tor
    users", www.dailydot.com, Apr 27, 2016, Accessed
    01/23/2017](http://www.dailydot.com/layer8/government-contractor-tor-malware/)
-   ["FBI's New Hacking Powers Take Effect This Week", Fortune, Nov 30,
    2016, Accessed 01/24/2017](http://fortune.com/2016/11/30/rule-41/)
-   ["Judge: FBI's Playpen Hack is 'Unquestionably' A Search Under
    Fourth
    Amendment'](https://www.deepdotweb.com/2016/09/25/judge-fbi-playpen-hack-unquestionably-search-fourth-ammendment/)
-   ["IP Spoofing", Cisco, Accessed
    01/24/2017](http://www.cisco.com/c/en/us/about/press/internet-protocol-journal/back-issues/table-contents-38/104-ip-spoofing.html)
-   ["Feds May Let Child Porn Suspect Go to Keep Concealing Their Source
    Code", Arstechnica.com, Jan 9, 2017, Accessed
    01/24/2017](https://arstechnica.com/tech-policy/2017/01/feds-may-let-playpen-child-porn-suspect-go-to-keep-concealing-their-source-code/)
-   ["FBI Hacked Into 8k Computers in 120 Countries Using Single
    Disputed Warrant - Report", www.rt.com, Nov 23, 2016, Accessed
    01/24/2017](https://www.rt.com/usa/367890-fbi-playpen-hacking-warrant/)
-   [Want Tor to really work?, Torproject.org, Accessed
    01/24/2017](https://www.torproject.org/download/download.html.en#Warning)
-   ["Out in the Open: Inside the Operating System Edward Snowden Used
    to Evade the NSA", Wired.com, Apr 14, 2014, Accessed
    01/24/2017](https://www.wired.com/2014/04/tails/)

**Acknowledgements** 

 This work was done without any outside collaboration.
