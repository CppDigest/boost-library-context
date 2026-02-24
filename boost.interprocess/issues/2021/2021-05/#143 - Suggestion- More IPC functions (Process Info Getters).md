# #143 - Suggestion: More IPC functions (Process Info Getters) [Closed]

> Username: ghost  
> Created at: 2021-05-19 03:51:18 UTC  
> Updated at: 2026-01-07 09:29:23 UTC  
> Closed at: 2026-01-07 09:29:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/143  

Wanted to let you guys know if you like my code in this file:  
  
https://github.com/time-killer-games/xProcess/blob/main/crossprocess.cpp  
  
...you are free to use it in this library and relicense under boost (only granting this permission to boost authors, no, I don't want some foreign entity to steal my code and killing its true freedoms and replacing them with the anti-proprietary GPL license).  
  
I don't have any CI tests but I intend to write some for Windows via AppVeyor at a minimum. Currently it can only be built with MSYS2/MinGW on Windows however.  Not sure what to do as a replacement xxd in Visual Studio, but you need that to create a hex dump and append a binary. I'm happy to provide build scripts and a main() functon to test it out on your end, but first I'd like to know whether this function set is even going to be in the realm of possiblity to be considered in boost. I already have a test app, see my xproc repository, which is what I will use for manual testing as well as CI tests with AppVeyor and possibly Travis.  
  
Notice the process enumeration function has some platforms where it checks for it to manually add the pid of zero to the vector, this is because on some platforms I noticed the OS-level process enumeration API's include the process swapper (pid=zero) others do not, I guess perhaps they found it redundant but I made sure all platforms include that pid for consistency.  
  
I plan to target OpenBSD, NetBSD, DragonflyBSD, Solaris, OpenSolaris, and OpenIndiana in the future, however, I'm pretty sure those last 3 OS's use the same exact API's so that'ss probably more like 4 OS's on the to-do list instead of 6, will double check when I get around to it, and will not be considering mobile, embedded systems outside of Linux/*BSD's or console due to costs and time. On Windows I am using private API's (or in this case, private structs) and I'm not sure whether that's illegal.   
  
I've heard mixed answers, I only assume it's legal because there are a lot of software out there MS could easily sue if that were the case, (such as WINE). On macOS, my process enumeration function is potentially bugged, in that it might not just get process id's but also thread id's (more specifically--process id's that are not equal to the main thread's thread id). This is not intended behavior and therefore inconsistent with my other platform code, however I haven't tested whether this is truly the case on not, I just know I see multiple / a decent amount of processes running with the same exact executable name and path, however that could just be more than one instance of those applications running, I'd have to debug and verify that manually.  
  
There's surprisingly not many other bugs I can find after a very long time (like, a crap ton of hours total over the span of a decent amount of months) however some of those bugs I found just the other day, so I can't say there won't be more to arise soon. Although those bugs I knew about for a while, and only fixed them recently, not discovered them recently. I'm fairly confident this code is rock solid stable at this point, and reasonbly so enough to be included in the boost library collection.   
  
At any rate, here I'd like feedback on my code and how it could be improved, and what could be done to make it acceptable for adding to an official boost library such as this one. Also before it gets added to boost, if you are reading this, and want to contribute while it's pending boost team approval please send me a pull request and I'll be happy to check it out asap. I intend to make a pull request on this repository as soon as it's approved as a "possibility" and I have a better idea given to me of where my API's should be put in this repository's sources and which functions are of any interest and what function names to use, if renaming is advised.  
  
Feel free to ask questions regarding my implementions if you have any.  
  
Thanks!  
Samuel

---

## Comment 1

> Username: ghost  
> Created at: 2021-05-26 01:31:51 UTC  
> Updated at: 2021-05-26 03:44:13 UTC  
> Url: https://github.com/boostorg/interprocess/issues/143#issuecomment-848389944  

Hey, the test application for my xProcess API (xproc cli) had some serious bugs still it turned out that needed to be fixed before this was anywhere near presentable. The vast majority of that should be fixed now I'm hoping. Here's the repository if you'd like to build or test it yourself, currently supports Windows, macOS, Linux, and FreeBSD:  
  
https://github.com/time-killer-games/xproc

---

## Comment 2

> Username: ghost  
> Created at: 2021-06-02 20:39:33 UTC  
> Updated at: 2021-06-02 20:44:09 UTC  
> Url: https://github.com/boostorg/interprocess/issues/143#issuecomment-853367533  

I added new features that are windowing-related but still process info-related if that makes sense. Since not all operating systems will have a windowing system installed necessarily, these features are disabled by default. When enabled, on systems that support it, the X Window Server is used. On macOS both Cocoa and XQuartz are options to use for Windowing.  
  
For example, kill a process based on it's window id, enumerate all window id's from topmost to bottom ordered by chronologically created owner process id's, enumerate window id's belonging to a process id, check if a window id exists based on whether the process id associated with it exists, and getting the process id from a window id.  
  
Wayland isn't supported yet, but once it is on platforms where applicable, there will be an X11 fallback if WAYLAND_DISPLAY isn't defined in the environment block of the current executable. People may disable this environment variable manually if they wish to use X11/Weston Windowing on a Wayland session. I've never programmed in Wayland before so this will take time.   
  
Build with the `-DXPROCESS_GUIWINDOW_IMPL` definition flag for windowing features to be enabled, along with linking to necessary dependencies or frameworks. X11 platforms need `pkg-config x11 --cflags --libs` and MacOS needs `-framework CoreFoundation  -framework CoreGraphics` when not targeting X11 for windowing via XQuartz. You don't need all of Cocoa.

---

## Comment 3

> Username: ghost  
> Created at: 2021-06-27 09:02:09 UTC  
> Updated at: 2021-06-27 09:04:54 UTC  
> Url: https://github.com/boostorg/interprocess/issues/143#issuecomment-869127834  

I've added demo executables so you may test this hands on and see my work and how reliable it is before I make my pull request, which I will be ready to do sometime within the next couple days or so. I still have yet to add optional Wayland window support.  
  
Demo Executable System Requirements:  
- Windows 7/8/8.1/10 or later, x86/x64  
- MacOS Catalina 10.15 or later amd64  
- Ubuntu Focal Fossa 20.04 LTS amd64  
- Ubuntu Focal Fossa 20.04 LTS armv7  
- FreeBSD 12.2, 13.0-RELEASE amd64  
  
Ubuntu dependencies:   
- required: `sudo apt-get install libprocps-dev`  
- optional: `sudo apt-get install libx11-dev`  
  
FreeBSD dependencies:  
- optional: `sudo pkg install libX11 `  
  
They can be downloaded from the xproc releases page:  
- https://github.com/time-killer-games/xproc/releases/tag/v1.0.0.0  
  
It might be a good idea if this waited until Windows 11 comes out the end of this year in case that breaks anything.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2026-01-07 09:29:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/143#issuecomment-3718007805  

Closing old stale bugs, sorry for the inconvenience.
