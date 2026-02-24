# #545 Define BOOST_MP_NO_ET_IMPLICIT_CONVERSIONS for Clang 14 w/C++17 on Linux [Closed]

> Username: mborland  
> Created at: 2023-03-20 22:38:29 UTC  
> Updated at: 2023-12-22 13:09:10 UTC  
> Closed at: 2023-12-22 13:09:10 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/545  

Workaround for #544

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-03-21 09:02:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/545#issuecomment-1477479498  

If this is required all the way up to clang-14, then the whole concept of this fix is probably not viable after all :(  
  
But.... didn't CI with clang-14 pass without this define?  What's the test case - I wonder if the issue is dependent on the std lib is_constructible internals which would be horrible/worrying?

---

## Comment 2

> Username: mborland  
> Created_at: 2023-03-21 15:07:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/545#issuecomment-1478008503  

It is only clang-14 that failed here: https://github.com/boostorg/math/actions/runs/4474375135/jobs/7862781129#step:17:315. Based on the error it looks like Clang is using the version of libstdc++ that comes with g++12, but g++12 compiles it fine.

---
