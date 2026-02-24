# #417 - CMake 3.12 requirement [Open]

> Username: pdimov  
> Created at: 2025-10-10 09:25:54 UTC  
> Updated at: 2025-12-02 14:01:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/417  

CMake 3.12 is required here  
  
https://github.com/boostorg/histogram/blob/3afe76f91f93af237764242d56fa6ebaad7c81e6/CMakeLists.txt#L6  
  
but I'm not sure why; from a quick look at the CML files nothing seems to need it.  
  
Ubuntu 18.04 has CMake 3.10, and we're adding CI jobs to test with earlier CMake versions, so this came up.

---

## Comment 1

> Username: HDembinski  
> Created at: 2025-12-02 13:16:36 UTC  
> Url: https://github.com/boostorg/histogram/issues/417#issuecomment-3602006561  

I am not sure either, I probably copy/pasted that from some source. We can try to lower it to 3.10 and test it.

---

## Comment 2

> Username: henryiii  
> Created at: 2025-12-02 14:01:08 UTC  
> Url: https://github.com/boostorg/histogram/issues/417#issuecomment-3602222368  

https://github.com/boostorg/histogram/commit/cda7447c9708f915a3a22bda7119293a77538d0a  
  
Needed for FindPackage Python. But I’m assuming that is only for tests or something that is not always required?  
  
3.10 is really old, btw. ;)
