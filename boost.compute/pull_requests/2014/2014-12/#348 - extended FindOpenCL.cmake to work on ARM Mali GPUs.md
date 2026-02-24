# #348 extended FindOpenCL.cmake to work on ARM Mali GPUs [Merged]

> Username: sschaetz  
> Created at: 2014-12-16 09:35:58 UTC  
> Updated at: 2014-12-18 00:40:49 UTC  
> Merged at: 2014-12-16 16:02:46 UTC  
> Closed at: 2014-12-16 16:02:46 UTC  
> Url: https://github.com/boostorg/compute/pull/348  

I tested Boost.Compute on an ARM Mali GPU (an ODROID-XU3 board). For Boost.Compute to compile on this plattform, libmali must be linked. I propose this extention to FindOpenCL.cmake do just that.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-16 09:53:15 UTC  
> Url: https://github.com/boostorg/compute/pull/348#issuecomment-67135278  

[![Coverage Status](https://coveralls.io/builds/1617837/badge)](https://coveralls.io/builds/1617837)  
  
Coverage remained the same when pulling **d5fc3170f8185e8e58b82c4b8229b3bef489048f on sschaetz:develop** into **7a6eaef22207fa5a58d6e4fc5b9f6669d12b0d02 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-16 16:04:47 UTC  
> Url: https://github.com/boostorg/compute/pull/348#issuecomment-67182660  

Very cool! Thanks!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-12-18 00:40:49 UTC  
> Url: https://github.com/boostorg/compute/pull/348#issuecomment-67424081  

@sschaetz CMake-3.1 was just released today and now includes its own [`FindOpenCL.cmake`](http://www.cmake.org/cmake/help/v3.1/module/FindOpenCL.html#module:FindOpenCL) module. You may want to try to push this change there as well.

---
