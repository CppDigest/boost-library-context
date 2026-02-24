# #640 - nvc++ errors with message "float128_type is undefined" [Closed]

> Username: davetaflin  
> Created at: 2023-09-14 22:44:22 UTC  
> Updated at: 2024-11-06 13:08:45 UTC  
> Closed at: 2024-11-06 13:08:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640  

nVidia's HPC toolkit (https://developer.nvidia.com/hpc-sdk-downloads) supplies compiler nvc++, which defines macro __PGI. Using it to compiling a C++ file that #includes <boost/geometry/index/rtree.hpp> issues the following error message (this was with Boost 1.83.0, but the error began occurring with 1.79.0):  
  
.../boost_1_83_0/boost/multiprecision/detail/number_base.hpp", line 205: error: identifier "float128_type" is undefined  
  struct bits_of<float128_type>  
  
Tested operating system: Ubuntu 20.04 LTS  
Compiler: nvc++ 23.7-0 64-bit target on x86-64 Linux -tp haswell

---

## Comment 1

> Username: DJDavies2  
> Created at: 2024-10-31 11:06:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2449914691  

Is there a solution for this?  I am seeing this issue although the versions are slightly different: boost 1.86, nvidia 22.7.

---

## Comment 2

> Username: mborland  
> Created at: 2024-10-31 14:22:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2450002852  

Do you have a minimal reproducer? If multiprecision is being run in the CUDA Kernel there's no hope of it working because this library does not support being run in device code. The lines in question are extracted below:  
  
```  
#if defined(_GLIBCXX_USE_FLOAT128) && defined(BOOST_GCC) && !defined(__STRICT_ANSI__)  
#define BOOST_MP_BITS_OF_FLOAT128_DEFINED  
template <>  
struct bits_of<float128_type>  
{  
   static constexpr unsigned value = 113;  
};  
#endif  
```  
  
If you're using CMake with GCC as the host compiler all of those conditions are being met.

---

## Comment 3

> Username: DJDavies2  
> Created at: 2024-10-31 16:50:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2450359172  

The code doesn't get as far as doing anything, the compilation fails just with the #include, e.g.:  
  
`  
#include <boost/multiprecision/float128.hpp>  
  
int main()  
{  
}  
`

---

## Comment 4

> Username: mborland  
> Created at: 2024-11-04 17:14:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2455270184  

This gets things compiling locally: https://github.com/boostorg/multiprecision/pull/641. Boost.Config says that the NVC++/PGI compiler should not be providing any kind of Float128 support. The NVC++ documentation only mentions `_Float128` in a bug fix so I assume this is still correct.

---

## Comment 5

> Username: DJDavies2  
> Created at: 2024-11-06 12:29:33 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2459624805  

Thanks, that fix seemed to work for me.

---

## Comment 6

> Username: mborland  
> Created at: 2024-11-06 13:08:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/640#issuecomment-2459711154  

Thanks for the confirmation.
