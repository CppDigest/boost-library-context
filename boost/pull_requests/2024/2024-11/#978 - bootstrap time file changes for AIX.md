# #978 bootstrap time file changes for AIX [Closed]

> Username: tjcw  
> Created at: 2024-11-05 14:11:14 UTC  
> Updated at: 2024-11-08 15:24:27 UTC  
> Closed at: 2024-11-08 15:24:27 UTC  
> Url: https://github.com/boostorg/boost/pull/978  

This PR adds file renaming to bootstrap.sh and bootstrap.cmd so that the initial BOOST tarball can be unpacked on all platforms including AIX, and then the 'wave' test involving the directory with the UTF8 name can run on all the non-AIX platforms.

---

## Comment 1

> Username: tjcw  
> Created_at: 2024-11-05 14:23:28 UTC  
> Url: https://github.com/boostorg/boost/pull/978#issuecomment-2457312707  

https://github.com/boostorg/wave/pull/233 is the associated PR

---
