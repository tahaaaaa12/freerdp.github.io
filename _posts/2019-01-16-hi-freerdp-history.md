---
layout: posts
title: Hi! - The history of the FreeRDP project
date: 2019-01-16
author: bm
---

# Back story

When Microsoft initially added RDP support to Windows, the only RDP client available
was mstsc, which was only available for Windows.
The fact that there was no client for other operating systems forced system administrators
to fall back on non-native alternatives (like VNC) for Windows desktop remoting or to have an
installation of Windows with mstsc.
Due to this lack of support for other operating systems, around 1999 Matthew
Chapman started to implement a client for the, at that time closed, RDP 4.0 protocol by
the means of reverse engineering. This project was called rdesktop and was published under the GPL
version 2. Around 2004 Erik Forsberg developed, within the scope of his thesis ["Reverse-Engineering
and Implementation of the RDP 5 Protocol"][rdp5-thesis], an RDP proxy to reverse engineer the RDP5
protocol. Over the years rdesktop evolved to the standard remote desktop client for all Unix and
Linux based operating systems and also a lot of graphical frontends became available (e.g. grdesktop
or tsclient).


# Open specifications

In February 2008, when Microsoft announced to open up the specifications for the RDP protocol, the
first [discussions][spec-discussion] about if and how the opened specifications should be used for
rdesktop where held, polarizing the developer community. When the specifications for RDP 6.0 and 7.0
became available, the debate about using the specifications or not flared up again. The main
reasons against using them were possible legal and licensing issues (see the [mailing-list-archive]
for the full discussion). In 2009 the rdesktop code base was around 30,000 lines, mostly
based on work of reverse engineering. Some developers wanted to add newer protocol features but it
was not possible to implement them as simple patches and therefore they suggested major changes in
rdesktop’s architecture in order to create a modern and state-of-the-art RDP client. The rdesktop
team in place would not allow it and did not encourage changes like this. Triggered by a conflict
about modifications and improvements in the keyboard handling system, and due to the lack of
cooperation, patch acceptance and the rdesktop team's strict attitude towards the use of the RDP
specifications to implement the RDP protocol, the developers around Marc-André Moreau
and Jay Sorg created a fork of rdesktop called FreeRDP. This happened in May 2009 and was the
birth of the FreeRDP project.


# Relicensing

Some of the project goals the FreeRDP developers set for themselves were to improve and
modularize the source base forked from rdesktop and to start (re-)implementing the protocol parts
according to the open Microsoft specifications. Since the fork a lot of progress was made and until November 2010
ten releases were published. But as with rdesktop, FreeRDP could not easily be used by companies or
in proprietary products because it used the [GPL v2][gplv2] as license which requires to publish
the source code for any derived products. To even out the way to become the standard free RDP
library and aiming towards the possibility to also be used by commercial companies, as base for
their RDP products, the FreeRDP developers decided to [change their license][license-change] to the
[Apache license version 2.0][apl]. During the relicensing process it turned out that a clean
license change was not possible because some of the copyright holders were unwilling to agree to that change.
Since the relicensing was not possible, the developers took a rather drastic approach and decided to
throw away the current code base and reimplement FreeRDP from scratch by using the published
specifications from Microsoft. This was [announced][announcement-mail] in July 2011. In January
2012, only about six months after the rewrite had started, Marc-André Moreau officially
[announced][freerdp-announced] FreeRDP 1.0.0.

# The recent past

During the rewrite Marc-André Moreau and the other developers worked literally almost 24/7 so version 1.0.0 was already
really mature in terms of functionality and features. It supported hardware optimized versions of RemoteFX decoders, NSCodec, an initial version of
remote apps, multimedia redirection (tsmf), an experimental version of an RDP server, all important channels (at least basic implementations),
FIPS-compliant RDP security and, as one of the most important features, network level authentication (NLA - NTLMv2).

Due to this feature set, flexibility and it's availability on different platforms, FreeRDP gained more and more traction from users, thin client vendors
and application developers. - It was used for RDP implementations with special codecs, Windows RDP applications, protocol parsers like
for RDP proxies and in a lot of other open source and commercial products.

With the release of Ubuntu 12.04 FreeRDP finally got available to users without the need to compile it from source.

One event in the recent past that caused a little "surge" happened between version 1.0.0 and 1.1.0 - the introduction of the new command line syntax.
It's a hate/love relationship. People either hate or love it. The syntax is based (and for basic parameters also compatible) on the syntax of mstsc and 
naturally it differs from the "standard" syntax people are used to from Linux/Unix programs.
On the other hand, for the developers the syntax itself is very powerful and flexible and helped to solve some of the use cases.
There was (and still is) a compatibility mode for the "old style" syntax but as FreeRDP grew and developed, not all features and functions
are available in this mode.

Around December 2016 Marc-André passed the maintainership and lead development on to the other developers.

# The present

Today there are four core developers and multiple more or less frequent committers. Version 1.1+ is found in almost every distribution
and the team is working hard towards a release of version 2.0.

Started as a fork, today FreeRDP is available for multiple operating systems including Linux,
Windows, iOS, OS X and Android and provides a client side as well as server side implementation of
RDP. Part of FreeRDP is winpr, the Windows portable runtime that aims to provide parts of the Windows APIs for
non-Windows operating systems. One could argue, but in fact FreeRDP is nowadays the standard
client implementation where no native Microsoft client is available.

# The future

That's a topic for a different day. Stay tuned ;).


[rdp5-thesis]: http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.331.339&rep=rep1&type=pdf
[spec-discussion]: http://sourceforge.net/p/rdesktop/mailman/message/18647319/
[mailing-list-archive]: http://sourceforge.net/p/rdesktop/mailman/message/21445723/
[gplv2]: http://www.gnu.org/licenses/gpl-2.0.html
[license-change]: https://github.com/FreeRDP/FreeRDP/wiki/License-Change
[apl]: http://www.apache.org/licenses/LICENSE-2.0
[announcement-mail]: http://sourceforge.net/p/freerdp/mailman/message/27782543/
[freerdp-announced]: https://www.freerdp.com/2012/01/16/freerdp-1-0-released
