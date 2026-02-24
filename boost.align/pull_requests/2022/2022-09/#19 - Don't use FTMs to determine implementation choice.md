# #19 Don't use FTMs to determine implementation choice. [Merged]

> Username: ericonr  
> Created at: 2022-09-07 21:07:18 UTC  
> Updated at: 2022-11-11 21:31:20 UTC  
> Merged at: 2022-11-11 21:29:45 UTC  
> Closed at: 2022-11-11 21:29:45 UTC  
> Url: https://github.com/boostorg/align/pull/19  

Feature test macros should be defined before including system headers, and that's their only role. Using them later on to choose which implementation to use leads to issues with differing usage of FTMs across code bases.  
  
qBittorrent, via libtorrent-rasterbar, via Boost ASIO, uses the align module, and was crashing on musl libc due to mismatched implementations: the alloc impl from aligned_alloc.h and the free impl from aligned_alloc_posix.h.  
  
A more resilient way of checking for a POSIX system is with the _POSIX_VERSION macro, defined by <unistd.h>, which is included in this file. Ideally, however, this choice could somehow come from the Boost <config.h> header.

---

## Comment 1

> Username: thesamesam  
> Created_at: 2022-11-11 05:03:02 UTC  
> Url: https://github.com/boostorg/align/pull/19#issuecomment-1311233884  

@glenfe could you take a look? various Linux distros are having to patch this in

---

## Comment 2

> Username: glenfe  
> Created_at: 2022-11-11 21:30:10 UTC  
> Url: https://github.com/boostorg/align/pull/19#issuecomment-1312205947  

Sorry for the delay. The change looks OK to me - merged.

---

## Comment 3

> Username: thesamesam  
> Created_at: 2022-11-11 21:31:20 UTC  
> Url: https://github.com/boostorg/align/pull/19#issuecomment-1312206672  

Many thanks!

---
