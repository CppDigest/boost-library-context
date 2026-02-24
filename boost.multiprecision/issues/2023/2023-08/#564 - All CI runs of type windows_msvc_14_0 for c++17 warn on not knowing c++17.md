# #564 - All CI runs of type windows_msvc_14_0 for c++17 warn on not knowing c++17 [Closed]

> Username: ckormanyos  
> Created at: 2023-08-21 12:34:57 UTC  
> Updated at: 2023-08-22 05:48:05 UTC  
> Closed at: 2023-08-22 05:48:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/564  

ALL CI runs of type windows_msvc_14_0 for std C++17 warn that the toolset does not _know_ of C++17.  
  
Each file reports somethnig like:  
  
```  
cl : Command line warning D9002 : ignoring unknown option `/std:c++17`  
```  
  
We should either give the compiler a standard that it _knows_ or use _latest_ or even remove the runs entirely?  
  
Thoughts @jzmaddock and @mborland

---

## Comment 1

> Username: jzmaddock  
> Created at: 2023-08-21 15:11:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/564#issuecomment-1686518210  

I think it should be /std:latest for C++17 on that compiler?
