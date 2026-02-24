# #235 - The ucontext headers always use code for macOS. [Closed]

> Username: ilyakurdyukov  
> Created at: 2023-08-18 10:30:31 UTC  
> Updated at: 2023-10-01 14:42:55 UTC  
> Closed at: 2023-10-01 14:42:55 UTC  
> Url: https://github.com/boostorg/context/issues/235  

This:  
https://github.com/boostorg/context/blob/6fa6d5c50d120e69b2d8a1c0d2256ee933e94b3b/include/boost/context/fiber_ucontext.hpp#L70  
  
The same in `continuation_ucontext.hpp`, three such lines in both sources.  
  
Conflicts with ["boost/predef/macos.h"](https://github.com/boostorg/predef/blob/e508ed842c153b5dd4858e2cdafd58d2ede418d4/include/boost/predef/os/macos.h#L41):  
```c++  
#define BOOST_OS_MACOS BOOST_VERSION_NUMBER_NOT_AVAILABLE  
```  
  
All `#ifdef BOOST_OS_MACOS` needs to be changed to `#if BOOST_OS_MACOS`, otherwise the code for macOS is used for any OS!

---

## Comment 1

> Username: ilyakurdyukov  
> Created at: 2023-08-18 11:21:37 UTC  
> Url: https://github.com/boostorg/context/issues/235#issuecomment-1683769825  

@eduardvoronkin, your commit https://github.com/boostorg/context/commit/cc106be672f54b71842aa6322fe45bf60ed1d3cc introduced this issue.

---

## Comment 2

> Username: olk  
> Created at: 2023-10-01 14:42:55 UTC  
> Url: https://github.com/boostorg/context/issues/235#issuecomment-1742102964  

should be fixed by #236
