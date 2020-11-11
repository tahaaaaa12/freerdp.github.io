---
layout: posts
title: Hi! Myrtille - A native HTML4 / HTML5 Remote Desktop Protocol and SSH client
date: 2020-11-09
author: cc
---
![myrtille-logo](/assets/myrtille/myrtille_logo.png){:class="img-responsive"}

# Myrtille - from a PoC to an open source solution with FreeRDP at core
Hello, I am cedrozor the author of Myrtille, a project that started in 2007 as a challenge for fun with former work colleagues, on our spare time. The goal was to provide a native web access, for a simplified user experience, to remote servers and applications. We wanted legacy desktop applications to be as easy to use as a website, accessible from a single URL, making them literally "web apps".

It was originally the idea of UltraSam, the author of UltraVNC (another well known open source project), who was before that my project manager in a teleconferencing company based in France. But instead of the VNC protocol, we focused more on RDP because the rest of the team (including me) was more into .NET/C# development and we wanted something new in the Windows environment. That said, Myrtille relies on an abstraction layer and could easily integrate VNC or any other protocol (as it was done with SSH).

We started with RDesktop, but moved quickly to FreeRDP when it was released! :)

![myrtille-windows](/assets/myrtille/myrtille_windows.png){:height="95%" width="95%"}

# Screenscraping with FreeRDP
I have tried to keep the Myrtille footprint into FreeRDP as minimal as possible. Myrtille communicates with FreeRDP through named pipes (IPC), in order to maintain a FIFO data transfer. User inputs (keyboard, mouse, touchscreen, etc.) are captured by the browser (using javascript) and forwarded through the RDP session, while display updates (regions of the screen that have changed) are compressed into PNG, JPEG or WEBP images and sent to the browser (using websocket, server-sent events (HTML5) or even long-polling (HTML4)).

Because FreeRDP is a big project, I wanted an easy way to track the Myrtille code within it (whenever I couldn't have it into separate files). Thus, all the Myrtille code is surrounded by *"#pragma region Myrtille"* and *"#pragma endregion"* tags. This is very handy when I need to resynchronize the Myrtille fork of FreeRDP with the FreeRDP repository! Because, of course, I want Myrtille to benefit from the latest features, optimizations and bug fixes from FreeRDP! :) in this process, I may also find and fix bugs in FreeRDP, then inform the FreeRDP team.

In an earlier version (0.9.x), The Windows FreeRDP client (wfreerdp) was written in C++. It's possible to mix C and C++ code in a single project, so I was able to keep most of the code I wrote years ago and benefit from some C++ improvements over C (OOP, GDI+ image processing, etc.).

I also wanted to have a clean separation of concern between Myrtille and FreeRDP, so each RDP connection spawns a FreeRDP process. This makes it easier to track all active connections and if necessary disconnect one by killing its process.

![myrtille-linux](/assets/myrtille/myrtille_linux.png){:height="95%" width="95%"}

# Challenges and development roadmap
From the start we decided that we didn't want any plugin, extension or whatsoever in the browser. It would be a pure HTML/JS/CSS implementation. We also wanted to avoid javascript libraries whenever possible (and reasonable), to avoid useless (and countless) dependencies and keep the code low profiled. For simple tasks (and Myrtille doesn't require/do anything fancy), I think it's fine (and even good) to simply use vanilla javascript. Javascript is a powerful language, constantly evolving, and I have fun learning and harnessing it. I'm also a minimalist person who loves to get its hands dirty in the engine to see how things work on a lower level! :p

It was even more difficult back in time (2007), because websockets and canvas (HTML5) weren't available. We had to rely on XmlHttp requests, long polling and divs (HTML 4), with of course different implementations in different browsers (no typescript back then). IE was still widely used at this time, so we wanted our PoC to run on the first version that supported XmlHttp, namely IE6 (to add even more difficulty!).

That said, that was before SignalR was available to abstract and simplify network communication (automatic and transparent switching of protocols in case one of them is not available or fails). If Myrtille were to start today, I would of course make use of it (in addition to a few other libraries). Another thing I would do would be to use .NET core and the latest new stuff, in order to take Myrtille forward in the future and also have it on multiple platforms (using xfreerdp on Linux, for example). This is planned in fact, and I could use some help for it (any contribution is welcome!). Another option would be to have a commercial version of Myrtille, with a paid license to support these developments (because it's a lot of work and I now have to earn a living, as an independent developer).

Among the upcoming features, file transfer is the next improvement goal. Microphone support is also planned. Maybe also smart cards after that. A separation of the Myrtille gateway and services is also planned, for an easier configuration of the gateway into a DMZ, and further enhance the security (the installer will allow to select the module to be installed; currently this must be done manually). Still about security, I would like to offer another 2FA out of the box (probably Google Authenticator).

Regarding the user interface, I think Myrtille also needs a little visual rework. The login page, the toolbar will be redesigned with better flat styles, colors and icons.

About deployment and integration (and devops by extension), I would like also to bring more cloud support to Myrtille; Azure obviously, because of its integration within the Microsoft/Windows ecosystem, but not only (Amazon, Google, etc.). Myrtille already have a Docker image, but there are some limitations regarding print and audio. I will also look into that.

![myrtille-hyperv](/assets/myrtille/myrtille_hyperv.png){:height="95%" width="95%"}

# Performance
Responsiveness is the key to a good user experience. I tried hard to chase for every millisecond I could save in the roundtrip workflow. That's also why I wanted a lightweight javascript code from the start. The Myrtille gateway is also just that, a link between the browser and FreeRDP, maintaining the correlation between the http and rdp sessions, receiving/forwarding data from/to both sides.

Whenever possible, I try to parallelize the processing. The user inputs and display updates, for example, are asynchronous. You can have a display change resulting from a user action, or not. What is important however, is to maintain the order in which they occur. I decided to use named pipes between the gateway and FreeRDP, because they are FIFO queues and maintain such an order. XmlHttp requests and websocket messages are also delivered in order, and so are long polling DOM injections and server-sent events (SSE).

A path for improvement would be to use hardware accelerated graphics and take advantage of the H.264/AVC encoding supported by FreeRDP. Myrtille actually relies on images (PNG/JPEG/WEBP), generated by GDI+ (win32 API, software), but this could be replaced by a video stream (MP4/OGG/WEBM, etc.), generated by FreeRDP (against hardware, or software when using a VM), and pushed from the gateway to the browser into an HTML5 <video> tag or handled by a modern web API (MediaSource, WebRTC, etc.).

# Thank you
Many thanks to the FreeRDP team for this wonderful project! It is not an easy task when working with such a complex protocol and with so many changes over the years.

If you want to know more about Myrtille and its offered services (support, training, etc.), watch demo/tutorial videos or get in touch, you can consult our [website](https://www.myrtille.io).