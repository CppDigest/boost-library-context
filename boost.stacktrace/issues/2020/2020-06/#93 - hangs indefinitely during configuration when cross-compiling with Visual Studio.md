# #93 - hangs indefinitely during configuration when cross-compiling with Visual Studio [Closed]

> Username: GyrosGeier  
> Created at: 2020-06-18 12:02:08 UTC  
> Updated at: 2024-07-16 05:53:46 UTC  
> Closed at: 2024-07-16 05:53:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/93  

During a build attempt on amd64 for arm64 with Visual Studio:  
  
22:17:11     - addr2line builds         : no  
12:29:14     - WinDbg builds            : no  
12:30:10     - WinDbgCached builds      : no  
  
The two WinDbg tests tried to start arm64 binaries, which causes a requester to pop up if a desktop is available to the session the build is running in, or an infinite hang if it isn't.

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-12-19 11:07:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/93#issuecomment-748458947  

Please, provide more info:  
* What was the command line for b2?  
* What version of Visual Studio do you use?  
* What version of Boost and is it reproducible on 1.75 release?

---

## Comment 2

> Username: apolukhin  
> Created at: 2024-07-16 05:53:46 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/93#issuecomment-2230080540  

Can not reproduce the issue. Please, provide a PR with fix if the issue still exists
