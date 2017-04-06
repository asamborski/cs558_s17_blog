---
layout: post
title: Stagefright
author: Sarah Larbi, Victoria Thompson, Konstantino Sparakis, and Megan Horan
---


# StageFright Attack
By Sarah Larbi, Victoria Thompson, Konstantino Sparakis, and Megan Horan

## Abstract
Stagefright was a hack initially discovered and reported to Google by a security researcher, Joshua Drake, in April 2015. News of the attack surfaced in the mainstream media later that summer, describing it as “the worst android hack ever,” [3] affecting nearly 950 million devices.  Headlines ominously depicted the attack, implying that no Android was safe. Stagefright was a vulnerability found within the Android media server library.  It existed in several versions of Android’s operating system and allowed an attacker to execute remote code on a user’s device, potentially without detection. In our report, we detail what the Stagefright vulnerability was, as well as demonstrate how Joshua Drake was able to exploit it. 

## Superficial Issues
In April of 2015 Joshua Drake, the current VP of Platform Research and Exploitation and a security researcher at Zimperium, discovered a set of vulnerabilities in all versions of Android’s operating system greater than 2.2 (“Froyo”). He reported the bug to Google as well as incorporated a related bug fix into its internal source code repositories two days after the report. [1] The bug was announced publicly in July 2015 and the full disclosure took place on August 5th at the Black Hat USA conference where Josh Drake explains the hack in detail. When the bug was announced publicly in July, the hack was reported in the mainstream media as a vulnerability potentially affecting up to 950 million devices[2]. Reportedly the scariest part of the hack was that, contrasting to a spear-phishing attack,  all vulnerable devices could potentially be attacked via a simple text message that required zero interaction on user side. Following the initial discovery, further vulnerabilities were discovered and patches have been continuously released for them.

## Technical Overview
Underlying technical issues surrounded the issues in the C++ media processing library described above, StageFright. Libstagefright is located in the media server in the Android architecture. Drake initially wanted to look into the media server because it has a lot of privileges:  significantly more than afforded to other android apps. Some of those privileges included access to camera devices, MMS, email, and bluetooth. Drake knew that if you could attack the media server you could escalate your privileges  and have all the necessary means to spy on someone’s phone.

Drake started his search by running several tests on the media server to see where there were errors/crashes. His tests resulted in discovering 5 different memory corruptions. These became CVE-2015-1538 and CVE-2015-1539, which centered around problems pertaining to buffer and overflow issues. These types of issues have been proven exploitable in the past and provide the main source of exploitation in this attack, and will be elaborated on in the next section.                                          

In finding these issues, Drake started digging deeper into the media server framework and libstagefright library. He began by finding all the ways  he could send media files to himself. Through this methodology, which he called vector enumeration, he discovered eleven different attack vectors. These vectors included Mobile Network MMS, Client Side, Browser, Downloads, Email, and more [7]. 

The attack vector he chose to exploit in this specific attack was the MMS vector. This attack proved to be so powerful because if  media files received via text message had a header, Android would automatically download the attachment. Further, the media library executes continuously from the moment you receive the file, to when you view the notification, to when you tilt your screen on the text message page. This means that upon receipt of a MMS containing an audio/video file, libstagefright would automatically begin processing the malicious code before the user even opens the message. Further, the media library executes continuously from the moment you receive the file, to when you view the notification, to when you tilt your screen on the text message page.


## The MMS Attack
Now that the malicious MMS received, the attack began to attack the media server and eventually escalate privileges. 

The second issue is that libstagefright was suffering from overflow issues. This is what allowed an attacker to execute malicious code in a media file. There existed many exploitable overflows across various attack vectors, but specifically for MPEG4 files, Drake found this snippet of code that contained an integer overflow vulnerable function “parseChunk(offset, depth + 1)” [7]. Issues arising from this overflow are elaborated on in the next section.
![alt text](https://github.com/ksparakis/Stagefright-Explained/raw/master/4.png "Title")
								
### How Does the Buffer Overflow Work?				        

Buffer overflows are an entire family of attacks. Although Android is primarily written in Java, certain parts of it, such as the media library, are written in C++ for speed and other reasons. The improper use of memory pointers is what allows for these stack overflow vulnerabilities which was the basis for the Stagefright attack. 

The integer overflows, pointed out in detail earlier with references to CVE’s, are a sort of heap overflow attack. 
![alt text](https://github.com/ksparakis/Stagefright-Explained/raw/master/1.png "Title")
For the sake of this specific we will concentrate on the Heap and Stack.
In the diagram below on the left, we see that in the Stack we have a pointer, that points to the memory location of our variable text, which is located in the heap. Let's say text = “AAAAAABBBBBCCCCCDDDDD”. The  memory location for text  is at *Var text=“0x29ff1c”.Var command, points to “0x29ff18” in this example.
        [11.]
	![alt text](https://github.com/ksparakis/Stagefright-Explained/raw/master/3.png "Title")
	![alt text](https://github.com/ksparakis/Stagefright-Explained/raw/master/2.png "Title")
This attack works when the actual size of text, exceeds the allocated size of text. Then it keeps writing over whatever is next. So what the attacker does is send in a variable text = “AAAAAABBBBBCCCCCDDDDD” + Malicious(){does something bad;}. There is still one issue here: the malicious function needs to override perfectly where the function command() is in the memory.. But to an attacker's advantage there exists a CPU instruction called a “nop”. The CPU will skip over this until it reaches it’s next valid instruction. So an attacker adds this in between the text and their malicious code, in something called a Nop Slide: text = “AAAAAABBBBBCCCCCDDDDD” + “nop/nop/nop/nop….” + “Malicious(){does something bad;}”. So as long as one of the nops is positioned properly, when the process thinks it is calling command(), if it hits a nop it will keep executing nop which is nothing until it reaches the instructions of Malicious(), executing the malicious code. Enter the intro into Drake’s Stagefright attack.

Given that most modern heaps have meta data and other architectures would complicate such an attack, this explanation is generally oversimplified, yet buffer overflow is the premise of the Stagefright attack. The media file containing the malicious, corrupt code executes this type flavor of attack.

This attack is so singular in its methodology because upon receiving an MMS messages, an attacker could execute the attacks explained above on libstagefright and gain remote code execution privileges. Giving an attacker the ability to conduct a REMOTE, SILENT, and PRIVILEGED code execution. 

## Preventing the Attack: Provider Level
### Prevention attempt: (ASLR)
Now we will see how ASLR, an attempt to fix issues in the attack, was not enough. If an attacker knows the memory layout of a device,. It allows them to find  locations in memory and execute their buffer overflow with accuracy. This allows them to potentially capitalize on the vulnerability of the attack outlined in the last section. Yet in response to this, there is os level implementation that randomizes the location of memory addresses. That means that every time a process is created it has new randomized memory address location, preventing an attacker from easily capitalizing on knowledge of a device's’ memory architecture.

Initially, Android version Gingerbread did not have ASLR. Then there was a ASLR was created in the next version, Ice Cream Sandwich, which was also exploitable. Google Claims that at version 4.1 “Kitkat” they have implemented a fully working ASLR, but as sources point out [5], this ASLR is weak and does not impede attackers sufficiently. Android ASLR only provides 2^8 entropy or randomization, which means an attacker has a 1/256 probability that they will guess the memory location of their malicious payload properly.

Further research has also shown that there are ways to bypass ASLR in Android due to leaking of information regarding memory allocation of processes. This allows for side channel attacks allowing you to bypass ASLR [10]. ASLR can still be somewhat considered a good protection system for other computers, but more and more research are pointing to the fact that this is not enough and more of a temporary solution [6].

### Preventing the Attack: User Level 
* Avoid downloading media files (mp3, mp4, mpeg...) from sketchy websites or torrents. [9.]
* Avoid installing android apps from third party app stores, as the vulnerabilities can be exploited from a malicious app. [9.] 
* Disable auto-retrieve mms functions
* Block texts from unknown numbers
* Download Stagefright Detector apps found in google play.  (one from Zimperium and the other from Lookout Mobile Security)
* Update to Android Nougat (Version 7.0/7.1)
	* Although patches were released to fix the specifically pointed out vulnerabilities initially found in Stagefright, researchers have been continuously finding other related attacks within the media libraries that can have the same effect. 
	* Google has announced that in Android Nougat [8.], they have completely redesigned the media library to avoid these types of attacks altogether. So although updating your phone or lower versions with patches maybe be somewhat effective, updating to version 7.0 or high of android is the only real solution to avoid the entire family of attacks that may still be lurking in the old media libraries such as stagefright.
	
## Discussion of incentives/motivation for the attack. 
Fortunately, there is no evidence of this attack ever being implemented in the wild. Joshua Drake attributes his motivation for researching this particular vulnerability to a variety of reasonings. At Black Hat USA 2015, he describes his primary motivation for the project to be his interest in “improving overall mobile security”[4]. 

The stagefright library was specifically interesting for several reasons. Prior to the discovery of the Stagefright bug set, there had been multiple public mentions of instability/crashes [3]. Many users were reporting battery death, phone reboots, and a slew of other errors. Intel fuzz tested the media library on these Android devices and discovered nearly 12 terabytes of crashes within the server[3]. All of this pointed back to ‘corrupt media files’ which led to Joshua Drake looking further into the media server library to find what was going wrong. The mode of attack that Drake used in his research was through the MPEG4 vector, which was most frightening due to it’s use of undetectable remote code execution on the user’s device. Even further, he found that there were more than 11 attack vectors discovered overall, not all necessarily undetectable, but exploitable nonetheless. 





## Resources
1. https://en.wikipedia.org/wiki/Stagefright_(bug)
2. https://www.forbes.com/sites/thomasbrewster/2015/07/27/android-text-attacks/#674525573a50
3. https://www.lifewire.com/protect-yourself-from-the-worst-android-hack-ever-2487129
4. https://video.search.yahoo.com/yhs/search;_ylt=A0LEVr0Q7sZYy20AQ.snnIlQ?p=joshua+drake+black+hat&fr=yhs-mozilla-002&fr2=piv-web&hspart=mozilla&hsimp=yhs-002#id=&vid=&action=close
5. https://arstechnica.com/security/2015/09/googles-own-researchers-challenge-key-android-security-talking-point/
6. http://www.tomshardware.com/news/aslr-apocalypse-anc-attack-cpus,33665.html
7. https://www.blackhat.com/docs/us-15/materials/us-15-Drake-Stagefright-Scary-Code-In-The-Heart-Of-Android.pdf
8. http://www.theverge.com/2016/9/6/12816386/android-nougat-stagefright-security-update-mediaserver
9. http://www.techradar.com/news/phone-and-communications/mobile-phones/how-to-keep-your-android-device-safe-from-stagefright-2-0-1305897
10. https://www.blackhat.com/docs/us-15/materials/us-15-Gong-Fuzzing-Android-System-Services-By-Binder-Call-To-Escalate-Privilege-wp.pdf
11. https://www.youtube.com/watch?v=rtkRYxbt-r8
12. https://latestcomputing.blogspot.com/2014/08/memory-stack-vs-heap.html


