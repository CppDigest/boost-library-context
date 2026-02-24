# #21 Pass function name as wide-char string on WinCE [Closed]

> Username: muggenhor  
> Created at: 2015-12-16 16:44:31 UTC  
> Updated at: 2015-12-21 12:53:48 UTC  
> Closed at: 2015-12-20 18:14:18 UTC  
> Url: https://github.com/boostorg/log/pull/21  

On Windows CE GetProcAddress takes its parameter as a wide-char string. Unfortunately no such equivalent (e.g. GetProcAddressW) exists on regular Windows. Hence the wrapper macro for its parameter.  
  
A similar problem exists with GetModuleHandleA: it doesn't exist on Windows CE. Luckily a GetModuleHandleW _does_ exist on both the regular Windows and CE.

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-12-20 18:14:18 UTC  
> Url: https://github.com/boostorg/log/pull/21#issuecomment-166142074  

Applied a different fix in develop. You'll also need an updated WinAPI submodule to use the fix.

---

## Comment 2

> Username: muggenhor  
> Created_at: 2015-12-21 12:53:44 UTC  
> Url: https://github.com/boostorg/log/pull/21#issuecomment-166297641  

Thanks, with the changes from boostorg/log@388a3e21260093bf9b5a1bcc2cda1782f45692b3 and boostorg/winapi@27b1ac8139197a7feaf16097074e2a1c3e7628f1 cherry-picked (and reduced to only the `GetProcAddress` changes) this works!

---
