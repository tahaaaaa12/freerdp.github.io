---
layout: posts
title: FreeRDP 1.0.1 released
date: 2012-02-09
author: marc
---

FreeRDP 1.0.1 is a maintenance release to address a certain number of issues found in 1.0.0. This release also brings corrective measures to certificate validation which were required for inclusion in Ubuntu.

* Certificate Validation
  * Improved validation logic and robustness
  * Added validation of certificate name against hostname
* Token-based Server Redirection
  * Fixed redirection logic
  * HAProxy load-balancer support
* xfreerdp-server
  * better event handling
  * capture performance improvements
* wfreerdp
  * Fix RemoteFX support
  * Fix mingw64 compilation
* libfreerdp-core
  * Fix severe TCP sending bug
  * Added server-side Standard RDP security
