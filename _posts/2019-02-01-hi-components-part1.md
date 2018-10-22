---
layout: posts
title: Hi! FreeRDP components part 1 - WinPR
date: 2019-02-01
author: bm
---

Hi! there. In this post we'll talk about a special component of FreeRDP: WinPR. Continue reading if you want to learn more about WinPR and its purpose.

# Back in the days

Before we dive into the details, let's review a little bit of history.

In the early days of FreeRDP 1.0, each "functional" group was packed into it's own library and a
build would create 40+ libraries. While the initial idea behind this was good (being able to pick and link only
against the functionality needed), it didn't work in the long run for multiple reasons:
* There were so many libraries
* It was a nightmare for packaging - if you look at Debian or Ubuntu for FreeRDP 1.1, you will see more than 40 packages
 (including development and debugging packages) that are required to run xfreerdp - around 20 of them were WinPR related
* Application load times (especially on small systems)
* Some of the created libraries only exported a few symbols (i.e. one of them just 4 functions)
* Cross dependencies between libraries
* For third party developers who used FreeRDP it was hard to integrate since finding the libraries wasn't easy

Due to this library mess, most people using FreeRDP ended up doing a "MONOLITHIC" build, where only a few libraries,
containing all functionality, were created. During development this behavior was adapted and was made the default (in fact
doing non monolithic builds was dropped completely - with the exception of channels).
Nowadays a build of FreeRDP creates only up to five binaries and eight libraries, depending on the build configuration and the operating system.

# WinPR - Windows Portable Runtime
(See source code directory: [winpr](https://github.com/FreeRDP/FreeRDP/tree/master/winpr) from which the libwinpr2 and libwinpr-tools2 libraries are created)

Quoting from the initial [announcement][winpr-announcement] in 2012:

> WinPR is the result of a long and thoughtful process that came up after years of FreeRDP development. RDP, as you know, is a protocol designed by Microsoft and is primarily “Windows to Windows”. In many instances, protocol messages rely heavily on Windows-specific APIs which have no direct equivalent on other operating systems. For instance, core graphical messages are based on GDI, network level authentication makes use of the Microsoft SSPI authentication modules (CredSSP, NTLM), and terminal server gateway makes use of both SSPI and MSRPC. That’s not even mentioning all the effort that went into mapping keyboard systems and time zones. 

The goal of WinPR is to provide API compatibility for applications that target non-Windows environments.
Let's have a look what that means in practice.
The following was carried out on a fresh installed Ubuntu 18.10 but it should work on any distribution
where freerdp2 is available.
Preparations:
```
apt install build-essential pkg-config freerdp2-dev
```
Copy the example code from the Microsoft documentation of [\_aligned\_malloc][alignedmalloc],
paste it to a file called aligned.c and add the following lines after the stdio.h include

```
#ifndef WIN32
#include <winpr/crt.h>
#define printf_s printf
#endif
```
and compile:
```
gcc `pkg-config --cflags winpr2` aligned.c -o aligned `pkg-config --libs winpr2`
```

That's it. Admitted this was quite a trivial example and for complex programs porting will
definitely be more work than just popping in some headers - but you get the idea ;).

Under the winpr subdirectories in the source you'll find many "Test" directories containing perfect reference and example code for using WinPR's functionality.

The Windows API implementation in WinPR is far from being complete but the parts that are
used by FreeRDP itself (like threads, memory management, file functions, critical sections, ...),
are well tested and quite stable.
In addition to the Windows API, WinPR received multiple enhancements and features over time and it evolved to
FreeRDP's de facto cross platform toolkit.
Some of these enhancements are winpr-crypt (common interface for cryptographic functions),
winpr-tools (see below) and a generic clipboard implementation.
The nice thing: WinPR's functionality is available on many platforms (iOS, macOS, Linux, Android, ..).

But WinPR isn't only sunshine and roses. It suffers from the same issues most cross platform toolkits have.
There is almost always a trade-off between portability and functionality/performance. Keeping in mind that WinPR
is largely modeled after the Windows API, which is quite different from the "POSIX" API, it is hard to
make it behave alike on different platforms. In some parts of WinPR the developers had to go through
great efforts to get it running on every system. Some parts still do have "documented misbehavior"
because it's not easily possible to have the complete identical behavior (CreateProcess would be such an example).
There are still some parts in FreeRDP's code where platform specific functions are used directly because there
was no other (performant) solution.

Two utilities that come with WinPR are winpr-hash and winpr-makecert.
winpr-hash is a small helper utility that can generate NTLM v1 and v2 hashes from username, password and domain.
Here is an example how to create a hash and output it in "SAM" format.

```
winpr-hash -u rabbit -p totallysecret -v 2 -d freerdp.local -f sam
```

winpr-makecert is the other utility that can be used to create self-signed certificates.
It was designed to provide the same functionality and command line as the [MakeCert][makecert] utility on Windows.
Today winpr-makecert is mostly just a wrapper around libwinpr-tools2 that contains all the necessary functions for
certificate generation and handling. This functionality can be used for RDP servers when certificates are required.

To simply generate a new self-signed certificate for "textbox.freerdp.local" that expires in 2 years, you can run:

```
mkdir /tmp/selfie
winpr-makecert -format crt -path /tmp/selfie -n "testbox.freerdp.local" -y 2
```

The generated key and crt file (in x509 format) can then be found in /tmp/selfie.

All in all it can be said that WinPR grew into a nice and powerful cross platform library. For non GUI applications
that should run on different platforms it's definitely worth giving it a try - even if the application isn't related
to RDP ;).

[winpr-announcement]: http://www.freerdp.com/2012/05/24/winpr-windows-portable-runtime
[alignedmalloc]: https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/aligned-malloc?view=vs-2017
