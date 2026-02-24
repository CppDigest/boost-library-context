# #119 Fix mingw compile error [Merged]

> Username: Lastique  
> Created at: 2020-06-07 12:31:14 UTC  
> Updated at: 2020-06-23 21:04:50 UTC  
> Merged at: 2020-06-11 20:11:34 UTC  
> Closed at: 2020-06-11 20:11:34 UTC  
> Url: https://github.com/boostorg/interprocess/pull/119  

MinGW has nonstandard definition of `PSECURITY_DESCRIPTOR`, which requires a cast when calling WinAPI functions `InitializeSecurityDescriptor` and `SetSecurityDescriptorDacl`. This is done by Boost.WinAPI wrapper functions.  
  
Fixes https://github.com/boostorg/interprocess/issues/118.  
  
Also, fixed the `initialized` flag not being set correctly after `interprocess_all_access_security` initialization completes.

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-11 16:18:33 UTC  
> Url: https://github.com/boostorg/interprocess/pull/119#issuecomment-642784579  

@igaztanaga A kind reminder. This is currently causing massive Boost.Log CI failures on Windows.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2020-06-11 20:11:54 UTC  
> Url: https://github.com/boostorg/interprocess/pull/119#issuecomment-642903463  

Many thanks for the patch and the reminder ;-)

---

## Comment 3

> Username: Lastique  
> Created_at: 2020-06-23 19:22:09 UTC  
> Url: https://github.com/boostorg/interprocess/pull/119#issuecomment-648368622  

Please, remember to merge to master.

---

## Comment 4

> Username: igaztanaga  
> Created_at: 2020-06-23 21:04:50 UTC  
> Url: https://github.com/boostorg/interprocess/pull/119#issuecomment-648422721  

Done!

---
