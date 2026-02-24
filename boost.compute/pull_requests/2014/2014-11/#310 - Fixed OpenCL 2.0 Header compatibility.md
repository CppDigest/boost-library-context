# #310 Fixed OpenCL 2.0 Header compatibility [Merged]

> Username: mwyborski  
> Created at: 2014-11-20 17:45:41 UTC  
> Updated at: 2016-10-09 19:54:30 UTC  
> Merged at: 2014-11-20 18:14:05 UTC  
> Closed at: 2014-11-20 18:14:05 UTC  
> Url: https://github.com/boostorg/compute/pull/310  

When compiling against OpenCL 2.0 Headers i had to modify image2d.hpp and image3d.hpp, because they renamed the "buffer" attribute to "mem_object" still being of type cl_mem.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-11-20 18:00:10 UTC  
> Url: https://github.com/boostorg/compute/pull/310#issuecomment-63851011  

[![Coverage Status](https://coveralls.io/builds/1504007/badge)](https://coveralls.io/builds/1504007)  
  
Coverage remained the same when pulling **bd845fea465cffa1e715f15dd808b003b932eaa9 on robotrovsky:develop** into **a5fab450d981981142b2f4906e17c238a8705001 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-20 18:14:10 UTC  
> Url: https://github.com/boostorg/compute/pull/310#issuecomment-63853176  

Merged! Thanks!

---

## Comment 3

> Username: mwyborski  
> Created_at: 2014-11-20 19:49:34 UTC  
> Url: https://github.com/boostorg/compute/pull/310#issuecomment-63867940  

You are welcome!

---
