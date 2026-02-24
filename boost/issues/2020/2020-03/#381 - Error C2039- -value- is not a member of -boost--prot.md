# #381 - Error C2039: "value" is not a member of "boost::prot" [Closed]

> Username: UnknownObject000  
> Created at: 2020-03-26 00:24:45 UTC  
> Updated at: 2020-03-26 01:02:07 UTC  
> Closed at: 2020-03-26 01:01:05 UTC  
> Url: https://github.com/boostorg/boost/issues/381  

I installed boost and cpp-netlib using vcpkg,but when I'm using it by ```#include “boost/network/uri.hpp"``` ,I got error C2039.The error is reported at "F:\vcpkg\installed\x64-windows\include\boost\proto\generate.hpp",in line239 and line 248.  
IDE: Viaual Studio Professional 2019  
System: Windows 10 x64

---

## Comment 1

> Username: UnknownObject000  
> Created at: 2020-03-26 00:32:22 UTC  
> Url: https://github.com/boostorg/boost/issues/381#issuecomment-604160474  

I made a spell error.. It's ```boost::proto``` , not ```boost::prot```.

---

## Comment 2

> Username: mclow  
> Created at: 2020-03-26 01:02:07 UTC  
> Url: https://github.com/boostorg/boost/issues/381#issuecomment-604168366  

If changing `prot` to `proto` didn't fix your problem, feel free to reopen the issue.
