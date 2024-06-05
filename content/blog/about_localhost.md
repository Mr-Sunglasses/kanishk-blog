---
external: false
draft: false
title: "Why you should not use `localhost` 🤯"
description: "My Observations regarding localhost and localhost vs 127.0.0.1"
date: 2024-06-06
---


Today, while working on an [issue](https://github.com/sparckles/Robyn/issues/557) for an open-source project, I discovered the difference between `localhost` and `127.0.0.1` and why you should avoid using `localhost` (specifically the word `localhost`, not the concept).



While solving the issue, I started one of my apps on `localhost` on port `8080` and another app on `127.0.0.1` on the same port `8080`. I expected errors, assuming that `localhost` would resolve to `127.0.0.1`, but it worked without issues. Initially, I couldn't believe it and tried again, and it still worked. This was a new learning experience for me, so I dug deeper to understand the differences and why `localhost` caused a problem in this case.

## The Problem 🐛

Let's first understand what the name `localhost` means,

_We are using `Python` and `socket` to demonstrate it._

```python
import socket

print(socket.getaddrinfo('localhost',0))
```

**In the above code we are using the socket module to find the information about the address `localhost`.**

#### Output

```bash
[(<AddressFamily.AF_INET6: 30>, <SocketKind.SOCK_DGRAM: 2>, 17, '', ('::1', 0, 0, 0)), (<AddressFamily.AF_INET6: 30>, <SocketKin│
│d.SOCK_STREAM: 1>, 6, '', ('::1', 0, 0, 0)), (<AddressFamily.AF_INET: 2>, <SocketKind.SOCK_DGRAM: 2>, 17, '', ('127.0.0.1', 0)),│
│ (<AddressFamily.AF_INET: 2>, <SocketKind.SOCK_STREAM: 1>, 6, '', ('127.0.0.1', 0))]
```

The code returns two different responses: an `IPv6` response at `*::1` (for `localhost`) and an `IPv4` response at `127.0.0.1*`. This leads to a few problems.

### The first Problem
The fisrt problem with `localhost` is that it can refer to two different **IP** families it can be in `ipv4` or can be in `ipv6`, most of the time the program will handle it by trying `ipv6` address and fails and then try `ipv4` address or the other way around but some programs does not handle it correctly, which is what i have to face, So for me one app is running on the `ipv4` at `127.0.0.1` address and one is running on the `ipv6` at `localhost` address, So it caused all sort of different problems.

### The second Problem
While trying the `ipv6` address first and then failing, the program will necessarly waste some time, also the program will also waste time in looking up `localhost` in `/etc/hosts` which can cause some latency.

# Recommendation

Change all instances of `localhost` to `127.0.0.1` to avoid the `IPv6` problem and achieve some performance improvements. _This can be significant when dealing with a very large codebase._

I've written this blog post to share my experience and help you avoid similar issues.
