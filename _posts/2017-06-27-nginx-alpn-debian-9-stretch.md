---
layout: post
title: Getting ALPN negotiation on Debian 9 (stretch) to activate HTTP2 with nginx
categories:
- blog
---

TLDR: With Debian 9 (stretch), ALPN negociation is finally **working out of the box** thanks to a recent version of OpenSSL used.


```
OpenSSL> version
OpenSSL 1.1.0f  25 May 2017

# Nginx was installed using official repositories
nginx -v
nginx version: nginx/1.13.1
```

All guides on internet refers to Debian 8 (Jessie) and none of them talks about Debian 9 (stretch), so I guess that worth writing a post to let you know that everything is working nicely out of the box.

[![Proof http2 ALPN nginx](/assets/images/http2_alpn_debian_stretch.png)](https://tools.keycdn.com/http2-test)

No more manual compilation, or using backports version of openSSL ! **Happy HTTP2**.
