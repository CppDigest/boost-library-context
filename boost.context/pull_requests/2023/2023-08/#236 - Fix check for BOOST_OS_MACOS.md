# #236 Fix check for BOOST_OS_MACOS [Merged]

> Username: iv-m  
> Created at: 2023-08-18 12:03:26 UTC  
> Updated at: 2023-10-01 14:29:35 UTC  
> Merged at: 2023-10-01 14:29:30 UTC  
> Closed at: 2023-10-01 14:29:30 UTC  
> Url: https://github.com/boostorg/context/pull/236  

BOOST_OS_MACOS is always defined; when we're not compiling for Mac OS, it's just defined as zero. So, the correct check is `#if BOOST_OS_MACOS`, because `#ifdef BOOST_OS_MACOS` is true on any operating system.  
  
Fixes: cc106be672f54b71842aa6322fe45bf60ed1d3cc  
Reported-by: Ilya Kurdyukov <ilyakurdyukov@altlinux.org>

---

## Comment 1

> Username: olk  
> Created_at: 2023-10-01 14:29:35 UTC  
> Url: https://github.com/boostorg/context/pull/236#issuecomment-1742099323  

ty

---
