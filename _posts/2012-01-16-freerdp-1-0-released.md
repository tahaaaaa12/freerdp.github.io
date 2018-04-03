---
layout: posts
title: FreeRDP 1.0 released
date: 2012-01-16
author: marc
---

Yes, the stable release of FreeRDP 1.0 is finally here!

I have been waiting so long for this day, and I know a lot of people were eagerly waiting for it as well. Our previous stable release, 0.8.2, is more than a year old and lacks an incredible amount of features which can now be found in 1.0. In the meantime, we planned a 0.9 release but then had to cancel it. Instead, we focused on proceeding with a rewrite which took about 6 months of work. If last year was hell at certain points, I am more than ever proud of the strong community that we are, and have faith that we will continue to deliver the best open source RDP implementation around. FreeRDP 1.0 is only the beginning, there are many challenges ahead of us, such as RDP8 or exploring FreeRDP as a server. It is also important to note that FreeRDP 1.0 is our first release officially under the Apache license.

FreeRDP 1.0 can be downloaded [here](https://github.com/FreeRDP/FreeRDP/downloads).

## New Features

* RemoteFX
  * Both encoder and decoder
  * SSE2 and NEON optimization
* NSCodec
* RemoteApp
  * Working, minor glitches
* Multimedia Redirection
  * ffmpeg support
* Network Level Authentication (NLA)
  * NTLMv2
* Certificate validation
* FIPS-compliant RDP security
* new build system (cmake)
* added official logo and icon

## New Architecture

* libfreerdp-core
  * core protocol
  * highly portable
  * both client and server
* libfreerdp-cache
  * caching operations
* libfreerdp-codec
  * bitmap decompression
  * codec encoding/decoding
* libfreerdp-kbd
  * keyboard mapping
* libfreerdp-channels
  * virtual channel management
  * client and server side support
* libfreerdp-gdi
  * extensively unit tested
  * portable software GDI implementation
* libfreerdp-rail
  * RemoteApp library
* libfreerdp-utils
  * shared utility library

## FreeRDP Clients

* client/X11 (xfreerdp)
  * official client
  * RemoteApp support
  * X11 GDI implementation
* client/DirectFB (dfreerdp)
  * DirectFB support
  * software-based GDI (libfreerdp-gdi)
* client/Windows (wfreerdp)
  * Native Win32 support

## FreeRDP Servers (experimental)
* server/X11 (xfreerdp-server)
  * RemoteFX-only
  * no authentication
  * highly experimental
  * keyboard and mouse input supported 

## Virtual Channels:
* cliprdr (Clipboard Redirection)
* rail (RemoteApp)
* drdynvc (Dynamic Virtual Channels)
* audin (Audio Input Redirection)
  * alsa support
  * pulse support
* tsmf (Multimedia Redirection)
  * alsa support
  * pulse support
  * ffmpeg support
* rdpdr (Device Redirection)
  * disk (Disk Redirection)
  * parallel (Parallel Port Redirection)
  * serial (Serial Port Redirection)
  * printer (Printer Redirection)
  * CUPS support
  * smartcard (Smartcard Redirection)
* rdpsnd (Sound Redirection)
  * alsa support
  * pulse support

