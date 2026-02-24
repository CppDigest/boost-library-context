# #391 - float128 doesn't work on ARM. [Closed]

> Username: NAThompson  
> Created at: 2021-11-17 20:03:57 UTC  
> Updated at: 2021-12-18 17:00:55 UTC  
> Closed at: 2021-12-18 17:00:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/391  

float128 uses x86 asm, and hence is not available on ARM. The error message is currently inscrutable.  
  
Is there a config flag that could provide a nice error message? Maybe  
  
```cpp  
#ifndef BOOST_ARCH_X86_64  
#error "libquadmath only works on x86"  
#endif  
```  
  
but that would put a dependency on boost.predef. [This](https://stackoverflow.com/a/69856463/904050) might also work.
