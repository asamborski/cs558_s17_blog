---
layout: post
title: Cloudbleed
date:   2017-04-08
author: Shahrez Jan, Scarleth Estevez, Austin Small, Mike Winters
---

## Abstract
Cloudflare is a company that provides a CDN, DNS, and security services for customer's websites. It acts as a reverse proxy, sitting between its customers' sites and their visitors in order to add security features and improve performance. Early in 2017, a bug in Cloudflare's server-side code was found to be leaking sensitive memory from Cloudflare's servers into some HTML responses. This bug, similar to its namesake, the 2014 Heartbleed bug in TLS/SSL, could leak whatever data was present in memory at the time: internal headers, cookies, HTML POST bodies, and even passwords. This was exacerbated by search engines caching the leaked data- making it more public and more easily accessible. Cloudflare responded to the bug quickly and deployed a kill switch for the source of the bug (its HTML parser) within 8 hours of it being reported. Cloudflare also insists that there is no evidence suggesting the bug was exploited in the wild. However, the potential severity of the bug is difficult to overstate. Cloudflare's reverse proxy created a single point of failure- effectively making secure encryption schemes like SSL/TLS, authentication, and other modern security efforts pointless. In this post we will be discussing Cloudbleed's coverage in the media, the problem in Cloudflare's HTML parser that lead to memory being leaked, and what the security community can learn from this experience going forward.

## Discovery, Incentives, and Involved Parties

## Media Coverage

## The Bug
Cloudflare parses HTML on the fly in order to provide useful features like email obfuscation (to prevent spam) and `http` to `https` rewrites (to prefer encrypted connections for linked URLs). The memory leak bug was located inside Cloudflare's HTML parser and essentially resulted in a classic buffer-overrun which would be triggered in specific circumstances and leak possibly sensitive data.

### Triggering the Bug
The bug is triggered when an HTML page being parsed is malformed in a particular way.
```html
<!DOCTYPE html>
<html>
<head>
    <script type=
```
 When an HTML page requested by a site visitor (and subsequently parsed by Cloudflare) contains unbalanced and unclosed script tag and attribute like the example above, the bug is triggered. On parsing this, the buffer containing the HTML is overrun and data from private server memory is included in the response sent to the requester.

### The Culprit Code
Cloudflare's HTML parser is written in Ragel, a somewhat obscure language that expresses logic by defining various state machines and ultimately generates C source code. Let's dive into the Ragel source code that allowed the buffer overrun (luckily Cloudflare released snippets of it).

This is the Ragel code responsible for parsing a script attribute like the one above.
```
script_consume_attr := ((unquoted_attr_char)* :>> (space|'/'|'>'))
>{ ddctx("script consume_attr"); }
@{ fhold; fgoto script_tag_parse; }
$lerr{ dd("script consume_attr failed");
       fgoto script_consume_attr; };
```
Here, the input HTML is matched against the regular expression `((unquoted_attr_char)* :>> (space|'/'|'>'))` which represents an unspecified number of `unquoted_attr_char` concatenated with a space, a forward slash, or a closing bracket. If the input matches the expression, the code inside the `@{}` block is executed. If it does not match, the code in the `$lerr{}` block is executed.

Given an unbalanced script tag like the HTML example above, the `$lerr{}` block would be executed since the regular expression matches well-formed tags. This is where we run into trouble. In this code block we `fgoto script_consume_attr` which will execute this same state machine to try to parse the next attribute. The problem with this is that with HTML malformed like the one above, there *is* no next attribute. Instead, the parser finds raw server memory and that gets injected into the response HTML instead.

The bug becomes much clearer when we look at some of the C code that this Ragel generates:
```c
/* generated code */
if ( ++p == pe )
    goto _test_eof;
```
Here, `p` is a pointer to the current character in the buffer and `pe` is a pointer to the character after the buffer. This is a classic buffer overrun problem in C. The `==` should really be `>=` because if `p` gets incremented past the value of `pe` the `_test_eof` function will not be called and instead whatever is in memory after the buffer will be referenced by `p`. This makes it clearer how private server memory was leaked into Cloudflare HTML responses.

## Prevention and Takeaways

## Legal and Ethical Issues

## References
