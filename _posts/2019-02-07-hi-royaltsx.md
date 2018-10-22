---
layout: posts
title: Hi! Royal TSX - a Remote Management Solution
date: 2019-02-07
author: felixdeimel
---


![royaltsx-logo](/assets/royaltsx/royaltsx_logo_840x181.png){:class="img-responsive"}

# Royal TSX and FreeRDP - A love story

Hi, I’m Felix, lead developer of Royal TSX for macOS! Royal TSX is a very popular Mac tool for systems engineers and IT professionals who need remote access to systems with different protocols. It allows you to manage your systems using Remote Desktop Protocol (RDP), SSH, VNC, S/FTP, web pages, and many more. In addition, Royal TSX also offers great features around credential management, task automation, team sharing, SSH tunneling and more but the focus of this blog post is, of course, all about the features based on FreeRDP.


![royaltsx-screenshot](/assets/royaltsx/royaltsx_screenshot.png){:height="95%" width="95%"}

# History of Royal TSX

Royal TS for Windows has been around for what feels like ages. First introduced in 2003, it quickly became a staple among Windows administrators because it drastically simplified the way RDP connections could be managed. Gone were the days of Microsoft Remote Desktop shortcuts filling IT professional’s desktops. With the surge of the Mac in the IT-professional space around 2010, we felt it was time to fill the gap and go cross-platform. So in 2012, Royal TSX 1.0 was released and quickly became the go-to remote management tool on the platform. Fast-forward to 2018 and tens of thousands of IT professionals around the world are now using our solutions on the Mac. Quite a few of our customers are even ranked in the Fortune 500 list which makes us very proud of what we’ve achieved over the last couple years and FreeRDP plays a big part in that success-story!


# Why FreeRDP?

When Royal TSX development was started in 2010, there weren’t a lot of options to integrate the RDP protocol into non-Windows applications. Basically, we could’ve either chosen to write our own protocol implementation or go with rdesktop.

Since the RDP protocol is such a beast, writing our own implementation wasn’t ever an option. So the only viable option left was to go with rdesktop. Fortunately, a Mac-native open source application called CoRD was available which we based our very first version of the Royal TSX RDP plugin on. While this initially worked and a lot of the pre-V1 public beta versions shipped with only the CoRD-based RDP plugin, it quickly became apparent that the future for rdesktop and CoRD wasn’t bright. Development basically stalled for both projects, while the RDP protocol continued to evolve at rapid pace. Fortunately, FreeRDP was already born to fill the gap in the open source RDP implementation space and as soon as the first reference implementation for the Mac was available in FreeRDP’s Github repository, we immediately began to work on our own Mac-specific implementation of FreeRDP. A couple months later we released the first version of our FreeRDP-based RDP plugin for Royal TSX and it became clear that FreeRDP was the way forward for our RDP-specific features in Royal TSX. In 2014, when Royal TSX 2.0 was released, the FreeRDP-based RDP plugin finally became the only supported option and rdesktop/CoRD hasn’t been missed by our users ever since.

# Development and Features
Because we started using FreeRDP in our products when it was still early days, we knew from the get-go that we wanted to separate our main application as much as possible from the RDP implementation. This was also a requirement because all connection types in Royal TSX are distributed as separate, downloadable plugins. The benefit of this approach is that users can only install the plugins they actually need which helps keep the application’s footprint low and allows us to update plugins separately from the main application.

Back then, we found Google Chrome’s approach to process separation exciting and decided we wanted something similar for our FreeRDP implementation. And that’s exactly what we built! Each RDP connection you open in Royal TSX launches an external helper process which essentially handles most of the core processing, protocol implementation and pre-rendering of the remote desktop. While painting the actual pixels on your screen, keyboard and mouse input still happen in the main application, almost everything else is handled by the helper processes. This approach has the benefit that it doesn’t crash the whole application if something goes wrong in the protocol implementation. In the worst case, your RDP connection’s tab closes but you can still continue work in any other connection tabs you might’ve open at the time.

Of course, there are also drawbacks to this approach. For starters, it’s not an easy task to build and maintain an IPC protocol that needs to be rock-solid while at the same time offering performance to handle millions of pixels moving across the screen. With the inception of Retina/HiDPI displays this became even more challenging. While we’re quite happy with our implementation and think that we achieved to strike a good balance between safety and performance, there’s of course always room for improvement. Being able to watch YouTube videos over RDP on a Mac and seeing the the full potential of the new iMac Pro’s glorious 5k display unleashed in a remote Windows session is pretty cool though! ;)


Another area of focus of our FreeRDP implementation is input handling. Since Mac keyboard layouts differ from their Windows counterparts, we had to come up with solutions that makes the transition between the Mac and Windows worlds as smooth as possible. Having to constantly think about which keyboard shortcuts work inside a remote Windows session vs. locally on your Mac is a total productivity killer. That’s why we came up with a little feature called “Remap macOS keyboard shortcuts to their Windows equivalent”, which, when enabled allows you to use keyboard shortcuts like command+c inside Windows sessions. We then translate this to ctrl+c before sending the command to the remote machine through RDP. In a recent update we also introduced a feature that allows you to completely customize the modifier key mappings. Of course, we also support other features that Mac users take for granted like smooth mouse/trackpad scrolling and macOS-native full screen mode.


# The Problems

As with every piece of software and especially when implementing a beast of a protocol with such scarce documentation as RDP, you’re bound to run into some problems at one point or another. While our experience with FreeRDP has been mostly positive there are of course some items on my wishlist. Here are some things I’d like to see improved in future updates:

* Improved/Updated/More detailed documentation
* Better/Unified/More detailed error handling
* Simplified API
* Improved support for Smart Card redirection (already in progress)
* Improved support for audio redirection on the Mac
* Better support for “exotic” Remote Desktop Gateway configurations
* Support for transferring files through clipboard redirection on the Mac


# Future and Thanks

We will of course continue to improve our FreeRDP implementation and have already started to work on a new major Royal TSX version which will also contain a few new RDP specific features. Royal TSX 4.0 will include support for the RDP8 and RDP10 graphics pipeline as well as H.264/AVC 4:4:4 codec support. A lot of general/stability improvements and bugfixes will also make it to this new release because of the great work of FreeRDP’s contributors over the last couple years.


Speaking of FreeRDP’s contributors: We’d like to take the opportunity to thank every single one of them for their outstanding work on this project! A lot of the success we’ve seen with Royal TSX would not have been possible without your outstanding contributions to one of the greatest open source projects I’ve ever come across. Implementing a beast of a protocol like RDP on multiple platforms as open source software is an incredible achievement and we’re very grateful to be able to build our products on top of such great software. Thank you!

If you’d like to learn more about Royal TSX or our other products, please head over to our [website](https://www.royalapplications.com/)
