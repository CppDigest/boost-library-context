# #425 Add ARM64 as a valid architecture for MSVC [Merged]

> Username: marswe  
> Created at: 2019-04-09 21:08:00 UTC  
> Updated at: 2021-10-02 21:19:06 UTC  
> Merged at: 2019-04-15 14:09:32 UTC  
> Closed at: 2019-04-15 14:09:32 UTC  
> Url: https://github.com/boostorg/build/pull/425  

MSVC can build ARM64 binaries that run natively on Windows 10 on ARM devices.  For Boost to build as ARM64, msvc.jam needs small updates to know about the existence of ARM64 as an architecture and how to build it.  
  
These changes have been used to build an ARM64 version of the boost regex library by passing "address-model=64 architecture=arm" to bjam.

---
