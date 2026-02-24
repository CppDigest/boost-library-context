# #230 Use correct 64bit full multiplication for MinGW on ARM64 [Merged]

> Username: vejbomar  
> Created at: 2024-10-17 16:04:25 UTC  
> Updated at: 2024-10-18 07:39:52 UTC  
> Merged at: 2024-10-17 17:29:59 UTC  
> Closed at: 2024-10-17 17:29:59 UTC  
> Url: https://github.com/boostorg/charconv/pull/230  

Update `fast_float` code to use correct 64bit full multiplication for MinGW on ARM64. This fixes the following error when building with MinGW for ARM64 from https://github.com/Windows-on-ARM-Experiments/mingw-woarm64-build:  
  
```  
In file included from ./boost/charconv/detail/fast_float/fast_float.hpp:11,  
                 from libs/charconv/build/../src/from_chars.cpp:16:  
./boost/charconv/detail/fast_float/float_common.hpp: In function ‘boost::charconv::detail::fast_float::value128 boost::charconv::detail::fast_float::full_multiplication(uint64_t, uint64_t)’:  
./boost/charconv/detail/fast_float/float_common.hpp:254:16: error: ‘_umul128’ was not declared in this scope; did you mean ‘umul128’?  
  254 |   answer.low = _umul128(a, b, &high); // _umul128 not available on ARM64  
      |                ^~~~~~~~  
      |                umul128  
```  
  
Corresponding change in `fast_float` repository: https://github.com/fastfloat/fast_float/pull/269  
  
Please let me know if there is some other way how the code from `fast_float` is updated here. If this is fine, I'll create a similar PR in https://github.com/boostorg/json

---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-10-17 16:08:01 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/charconv/pull/230#pullrequestreview-2375691510  

LGTM. Thanks. @grisumbras is there any need to update in JSON?  I thought you were moving to charconv this cycle with dropping GCC 4.X series.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2024-10-17 17:06:02 UTC  
> Url: https://github.com/boostorg/charconv/pull/230#issuecomment-2420065566  

I'll be switching to Charconv after the next release (that is, after 1.87).

---

## Comment 3

> Username: mborland  
> Created_at: 2024-10-17 17:29:50 UTC  
> Url: https://github.com/boostorg/charconv/pull/230#issuecomment-2420109196  

> I'll be switching to Charconv after the next release (that is, after 1.87).  
  
Ok. @vejbomar if you could transpose this into the same place in Boost.JSON for the meantime that would be great. I'm going to merge it and then fix the CI. Need to update the Drone images to LTS.

---

## Comment 4

> Username: vejbomar  
> Created_at: 2024-10-18 07:39:51 UTC  
> Url: https://github.com/boostorg/charconv/pull/230#issuecomment-2421668766  

Thank you for the brisk merging! I've created PR for Boost.JSON as suggested: https://github.com/boostorg/json/pull/1057

---
