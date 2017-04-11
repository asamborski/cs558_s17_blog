---
layout: post
title: Cloudbleed
date:   2017-04-08
author: Shahrez Jan, Scarleth Estevez, Austin Small, Mike Winters
---

## Abstract
Cloudflare is a company that provides a CDN, DNS, and security services for customer's websites. Cloudflare acts as a reverse proxy, sitting between customers' sites and their visitors in order to add security features and improve performance. Early in 2017, a bug in Cloudflare's services was found to be leaking sensitive memory from Cloudflare's servers into some HTML responses. This bug, similar to its namesake- the 2014 Heartbleed bug in TLS/SSL, could leak whatever data was present in memory at the time: internal headers, cookies, HTML POST bodies, and even passwords. This was exacerbated by search engines caching the leaked data- making it more public and more easily accessible. Cloudflare responded to the bug quickly and deployed a kill switch for the source of the bug (its HTML parser) within 8 hours of it being reported. Cloudflare also insists that there is no evidence suggesting the bug was exploited in the wild. However, the potential severity of the bug is difficult to overstate. Cloudflare's reverse proxy created a single point of failure- effectively making secure encryption schemes like SSL/TLS, authentication, and other modern security efforts pointless. In this post we will be discussing Cloudbleed's coverage in the media, the problem in Cloudflare's HTML parser that lead to memory being leaked, and what the security community can learn from this experience going forward.

## Discovery, Incentives, and Involved Parties

## Media Coverage
Cloudflare was able to stop the bug before any major leaks occurred. However, the media blew the dangers of the leaks out of proportion. A journalist from spamfighter even stated that “For, Cloudbleed' name of one fresh security bug possibly hacked user-data from websites that utilize the security facility 'Cloudflare”, claiming that the bug was a hack but it was actually a leak.  Thomas Fox-Brewster from Forbes stated that ““A large company like Uber might receive somewhere between 10 billion and 50 billion requests a month and so could have leaked somewhere between 1,000 and 6,000 times,” which over exaggerate the leaks because the reported number of request can not be determined just by how many request is made by Cloudflare. The title of this article “How bad was Cloudbleed?1.2 Million Leaks Bad”, as again an over exaggeration of the useable sensitive data that was leaked. 

## The Bug

## Prevention and Takeaways

## Legal and Ethical Issues

## References
