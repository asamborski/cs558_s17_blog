---
layout: post
title: Cloudbleed
date:   2017-04-08
author: Shahrez Jan, Scarleth Estevez, Austin Small, Mike Winters
---

# Cloudbleed

## Abstract
Cloudflare is a company that provides a CDN, DNS, and security services for customer's websites. Cloudflare acts as a reverse proxy, sitting between customers' sites and their visitors in order to add security features and improve performance. Early in 2017, a bug in Cloudflare's services was found to be leaking sensitive memory from Cloudflare's servers into some HTML responses. This bug, similar to its namesake- the 2014 Heartbleed bug in TLS/SSL, could leak whatever data was present in memory at the time: internal headers, cookies, HTML POST bodies, and even passwords. This was exacerbated by search engines caching the leaked data- making it more public and more easily accessible. Cloudflare responded to the bug quickly and deployed a kill switch for the source of the bug (its HTML parser) within 8 hours of it being reported. Cloudflare also insists that there is no evidence suggesting the bug was exploited in the wild. However, the potential severity of the bug is difficult to overstate. Cloudflare's reverse proxy created a single point of failure- effectively making secure encryption schemes like SSL/TLS, authentication, and other modern security efforts pointless. In this post we will be discussing Cloudbleed's coverage in the media, the problem in Cloudflare's HTML parser that lead to memory being leaked, and what the security community can learn from this experience going forward.

## Discovery, Incentives, and Involved Parties

## Media Coverage

## The Bug

## Prevention and Takeaways

## Legal and Ethical Issues

## References
