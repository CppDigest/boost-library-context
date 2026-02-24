# #309 Fixed OpenCL 2.0 Header compatibility [Closed]

> Username: mwyborski  
> Created at: 2014-11-20 10:17:21 UTC  
> Updated at: 2014-11-21 03:48:47 UTC  
> Closed at: 2014-11-21 03:48:47 UTC  
> Url: https://github.com/boostorg/compute/pull/309  

When compiling against OpenCL 2.0 Headers i had to modify image2d.hpp and image3d.hpp, because they renamed the "buffer" attribute to "mem_object" still being of type cl_mem.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-11-20 10:38:49 UTC  
> Url: https://github.com/boostorg/compute/pull/309#issuecomment-63789804  

[![Coverage Status](https://coveralls.io/builds/1501850/badge)](https://coveralls.io/builds/1501850)  
  
Coverage remained the same when pulling **db7c302dc9c4dae4388ca512448c6e1921f9f582 on robotrovsky:master** into **3183effcb0e09766e5a1f120cd688af9447952db on kylelutz:master**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-11-21 03:48:47 UTC  
> Url: https://github.com/boostorg/compute/pull/309#issuecomment-63920935  

Merged in PR #310.

---
