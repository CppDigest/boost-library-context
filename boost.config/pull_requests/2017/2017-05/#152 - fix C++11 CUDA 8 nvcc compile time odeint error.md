# #152 fix C++11 CUDA 8 nvcc compile time odeint error [Closed]

> Username: psychocoderHPC  
> Created at: 2017-05-30 12:18:33 UTC  
> Updated at: 2017-08-11 18:39:00 UTC  
> Closed at: 2017-06-15 12:06:28 UTC  
> Url: https://github.com/boostorg/config/pull/152  

fix https://svn.boost.org/trac/boost/ticket/13049  
  
disable usage of noexcept with `BOOST_NO_CXX11_NOEXCEPT` for nvcc from CUDA 8

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-06-12 10:55:38 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-307756939  

Is this only device code that is effected, or does it hit the host meta-compiler as well?

---

## Comment 2

> Username: psychocoderHPC  
> Created_at: 2017-06-14 13:36:08 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-308433013  

This bug effects host code ([see example](https://svn.boost.org/trac10/ticket/13049#Example)) which is compiled with nvcc out of a `*.cu` file.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2017-06-15 12:06:28 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-308711225  

Reproduced and fixed in https://github.com/boostorg/config/commit/9b8de65f1dae960ecfde689a99c83f39ba60e3f9

---

## Comment 4

> Username: akors  
> Created_at: 2017-08-09 11:34:05 UTC  
> Updated_at: 2017-08-09 11:34:39 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321229735  

Hi! Unfortunately, your fix broke compilation with CUDA 9. The reason for that is that you use the symbol `__CUDACC_VER__` for version checking, but the symbol has been deprecated in CUDA 9.  
  
Line 64 in `crt/common_functions.h` of the CUDA toolkit now reads:  
  
    #define __CUDACC_VER__ "__CUDACC_VER__ is no longer supported.  Use __CUDACC_VER_MAJOR__, __CUDACC_VER_MINOR__, and __CUDACC_VER_BUILD__ instead."  
  
The fix seems simple enough, instead of checking for the whole version, you could check separately for `__CUDACC_VER_MAJOR__` and `__CUDACC_VER_MINOR__` .

---

## Comment 5

> Username: psychocoderHPC  
> Created_at: 2017-08-09 13:01:07 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321248225  

@akors You are right, but the deprecation breaks all checks in `include/boost/config/compiler/nvcc.hpp`.   
Therefore the full file needs an update.

---

## Comment 6

> Username: akors  
> Created_at: 2017-08-09 13:03:40 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321248869  

Ok, that's a shame. Here's the Trac ticket URL: https://svn.boost.org/trac10/ticket/13152

---

## Comment 7

> Username: psychocoderHPC  
> Created_at: 2017-08-09 13:05:03 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321249203  

thx for opening the ticket: btw NVIDIA breaks all old boost versions with this change.

---

## Comment 8

> Username: akors  
> Created_at: 2017-08-09 13:15:42 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321251736  

> ouch, it might be worth opening a CUDA bug report as long as CUDA 9 is still in RC!  
  
Good idea! I just did that too, the internal link is https://developer.nvidia.com/nvidia_bug/1971621 , in case anyone has access to that.

---

## Comment 9

> Username: psychocoderHPC  
> Created_at: 2017-08-09 13:18:46 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321252565  

I tested the beta version of CUDA 9.0067 with a little program:  
  
```C++  
#include <iostream>  
#include <cuda_runtime.h>  
  
int main()  
{  
        std::cout<<__CUDACC_VER__<<std::endl;  
}  
```  
compiel and run  
```  
nvcc main.cu  
./a.out  
90067 #this is the output  
```  
  
This means that the define is available in the beta, we need to check the latest rc.

---

## Comment 10

> Username: ax3l  
> Created_at: 2017-08-09 13:33:00 UTC  
> Updated_at: 2017-08-09 13:47:34 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321256257  

The problem appears first with the second CUDA 9 release candidate, CUDA 9.0.103.

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2017-08-11 18:39:00 UTC  
> Url: https://github.com/boostorg/config/pull/152#issuecomment-321888638  

Can you guys please verify that https://github.com/boostorg/config/pull/175 fixes this?  
  
Many thanks, John Maddock.

---
