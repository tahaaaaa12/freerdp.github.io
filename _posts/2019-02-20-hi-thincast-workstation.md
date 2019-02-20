---
layout: posts
title: Hi! - The Thincast Workstation
date: 2019-02-20
author: mf
---
![tcw-icon](/assets/tcw/tcw_icon.png){:height="15%" width="15%"}

# Thincast Workstation - Virtual Desktop Streaming built around FreeRDP
In April 2018, Thincast released the first public Beta of Thincast Workstation, a new workstation hypervisor based on Oracle's VirtualBox.
We use FreeRDP's server implementation to replace VirtualBox's VRDP extension which is only available with the Oracle VM VirtualBox Extension Pack.
Together with our own powerful 3D graphics virtualization inside the guest we provide a smooth and rich user experience, no matter from where you are accessing your virtual machines.

But now let's take a closer look at where and how FreeRDP comes into play in our product.

# FreeRDP meets virtualization
Our RDP server resides in a separate, platform independent library that we call "virtual server". One of the tasks of this library is to serve as a bridge between VirtualBox's
remote desktop extension (VRDE) API and FreeRDP. It provides a full featured RDP server, including clipboard and sound redirection, user management as well as RemoteFX and H.264
streaming. The great thing here is that thanks to FreeRDP, all the protocol related work has already been done for us. We don't have to worry about connection management, packet
parsing or data marshalling. Our library just hooks the various callbacks that the FreeRDP API provides (i.e. handling mouse/keyboard input) and passes them into VirtualBox's VRDE.

On the other hand our virtual server library receives notifications from VirtualBox i.e. when the display buffer is updated. Using FreeRDP we can easily forward all these
updates to one, or multiple RDP clients connected to our server. Because RDP is a standardized protocol, the users are able to use any RDP compatible client to connect to their
virtual machines. On top of that, FreeRDP gives us access to the latest desktop streaming extensions of the RDP protocol like RemoteFX and H.264 remoting.

Thanks to WinPR it was an easy task for us to implement the virtual server library platform independent. Threading, synchronization or file access is all done using the Win32
API. Platform specific code parts were reduced to just a handful of lines i.e. for user management. This allows us to have a single code base that we can reuse on multiple
platforms by just linking to WinPR on non-Windows systems.

# Streaming the Virtual Desktop
With H.264 desktop streaming, introduced in RDP 10, we now also have new possibilities to transfer the desktop content to the user. We have developed a special load-balancing
algorithm that adjusts image quality during streaming, based on current network characteristics. FreeRDP's network characteristics detection (which is part of the RDP core
specification) provides us with all the necessary information about the connection's latency and bandwidth. Depending on these parameters we dynamically increase or decrease the
quality level of the H.264 encoder. An implementation of the software H.264 encoder backend, using OpenH264, is also part of the FreeRDP library.

# Performance matters
As explained earlier, Thincast Workstation provides virtual graphics hardware to the guest that exposes full Direct3D11 support. This means all graphical operations in the guest
OS are handled and accelerated by the host GPU. As we want to achieve the best performance possible, it would be a pity to do the H.264 encoding of our virtual desktop in
software. Luckily, thanks to FreeRDP's modular architecture, we were able to plug-in our own hardware encoders and completely offload this work from the CPU. Because every vendor
has its own multimedia framework we implemented encoders for all major GPU producers including NVidia, AMD and Intel.

![tcw-screenshot](/assets/tcw/tcw_screenshot.png){:height="95%" width="95%"}
Screenshot: Remote session to a virtual machine running UL's 3DMark benchmark.

# Thincast Viewer - 3D Remoting with FreeRDP
Thincast Workstation comes with its own FreeRDP based client called Thincast Viewer. The Viewer is part of the Thincast Client package and offers some advantages over other RDP
clients like Microsoft's Windows client mstsc. The first advantage it offers is an improved performance mode for connections to local virtual machines. In this mode graphical
updates are not streamed over RDP but directly fetched from the GPU. Thincast Viewer also provides various control options like starting, pausing or shutting down the virtual
machine. These features are implemented using a custom virtual channel that we added to our FreeRDP library. Remote connections to a virtual machine using H.264 desktop streaming
will be decoded on the client side using hardware support. All current GPUs offer hardware H.264 decoding support and especially mobile users will greatly benefit from the
significantly lower CPU utilization. Again we simply replaced FreeRDP's default software decoder with our own hardware implementation, without modifying anything in the library
itself. And last but not least, Thincast Viewer is available on Windows, Linux and macOS.

# Future Development
What is coming to Thincast Workstation in the future? We are thinking about adding some kind of gateway service that serves as a central access point to all virtual machines.
Currently each VM listens on its own port for incoming connections which makes management and administration a bit troublesome.

We also keep an eye out for further extensions or changes to the RDP protocol. For instance newer video codecs, i.e. H.265 or AOMedia Video 1 (a.k.a AV1), might become interesting for
desktop streaming in the future.

Apart from these RDP-related improvements we also want to improve the remaining parts of Thincast Workstation. Our current Beta for instance already contains virtual graphics
hardware that offers support for Direct3D 12. The implementation is already close to completion but still has some known issues. We have also added Vulkan to our to-do list but
work on this topic is just in its beginnings.

If you want to know more about our products, give them a try or you can just keep in touch by signing up for our newsletter on our [website](https://thincast.com)!

