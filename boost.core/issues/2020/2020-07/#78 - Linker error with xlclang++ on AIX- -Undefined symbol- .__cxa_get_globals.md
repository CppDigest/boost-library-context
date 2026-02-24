# #78 - Linker error with xlclang++ on AIX: "Undefined symbol: .__cxa_get_globals" [Closed]

> Username: mbs-c  
> Created at: 2020-07-16 13:12:09 UTC  
> Updated at: 2020-07-16 19:28:01 UTC  
> Closed at: 2020-07-16 17:55:40 UTC  
> Url: https://github.com/boostorg/core/issues/78  

I compiled Boost.Serialization as a static library with xlclang++ on AIX. When trying to link the program, I get the following error:  
  
> ld: 0711-317 ERROR: Undefined symbol: .__cxa_get_globals  
  
It seems like clang on AIX provides `cxxabi.h`, but doesn't define this symbol, so it needs a special case in `uncaught_exceptions.hpp`.

---

## Comment 1

> Username: Lastique  
> Created at: 2020-07-16 13:17:42 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659405067  

You may have to link against `libc++abi`. Normally, the compiler should do that implicitly, but I don't know what xlclang++ is and how it is different from vanilla clang.

---

## Comment 2

> Username: mbs-c  
> Created at: 2020-07-16 13:59:19 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659431133  

Thanks for the quick reply. `libc++abi` isn't even present on my system - I'll try to find out whether that's expected or not. To be perfectly honest, I also don't know much about this version of clang.  
  
For now, I'm adding `!defined(__ibmxl__)` to the conditions in line 49 of `uncaught_exceptions.hpp`.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-07-16 15:38:45 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659494543  

`libc++abi` may need to be installed with a separate package. That said, if you have `cxxabi.h` then you should already have the C++ ABI library installed, and it's not `libc++abi`, apparently. Looks like I should disable `__cxa_get_globals` for your compiler after all.  
  
Just to be sure, could you attach your `cxxabi.h`?

---

## Comment 4

> Username: mbs-c  
> Created at: 2020-07-16 17:55:43 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659573834  

It's identical to this version:  
  
https://github.com/llvm-mirror/libcxxabi/blob/2ecb4ee2222ad37b0d4da0812fa09edd3834a5d3/include/cxxabi.h  
  
> libc++abi may need to be installed with a separate package.  
  
Unfortunately, I cannot say with 100% certainty how the system I'm looking at was set up. I'm assuming we just installed the compiler package provided by IBM [here](https://www.ibm.com/support/pages/ibm-xl-cc-aix-161#DNLD), but I might be wrong.

---

## Comment 5

> Username: Lastique  
> Created at: 2020-07-16 18:02:35 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659577856  

Could you test that this code compiles, links and runs on your system:  
  
```  
#include <cxxabi.h>  
#include <iostream>  
  
int main()  
{  
    std::cout << abi::__cxa_uncaught_exceptions() << std::endl;  
}  
```  
  
I've committed a fix that disables `__cxa_get_globals`, but that means that the function will be emulated with `std::uncaught_exception`, which does not indicate the number of pending exceptions. I'd like to provide this functionality, if possible.

---

## Comment 6

> Username: mbs-c  
> Created at: 2020-07-16 18:12:03 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659582930  

Nope, also produces a linker error. Strange.  
  
I'll try to find out more about how this system was set up and whether we modified the installation in any way or whether that's really how the compiler is packaged.

---

## Comment 7

> Username: Lastique  
> Created at: 2020-07-16 19:28:01 UTC  
> Url: https://github.com/boostorg/core/issues/78#issuecomment-659621756  

Ok, so it seems then there's really no way to get the number of pending exceptions. In any case, thank you for the report and for testing.
