# #23 Pass function name as wide-char string on WinCE [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 16:40:17 UTC  
> Updated at: 2018-11-25 15:14:29 UTC  
> Closed at: 2018-11-24 18:36:39 UTC  
> Url: https://github.com/boostorg/filesystem/pull/23  

On Windows CE GetProcAddress takes its parameter as a wide-char string. Unfortunately no such equivalent (e.g. GetProcAddressW) exists on regular Windows. Hence the wrapper macro for its parameter.

---

## Comment 1

> Username: muggenhor  
> Created_at: 2015-12-21 13:22:46 UTC  
> Url: https://github.com/boostorg/filesystem/pull/23#issuecomment-166304648  

Updated the patch to take advantage of boostorg/winapi@27b1ac8139197a7feaf16097074e2a1c3e7628f1

---
