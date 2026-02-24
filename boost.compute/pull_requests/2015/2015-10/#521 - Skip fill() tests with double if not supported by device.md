# #521 Skip fill() tests with double if not supported by device [Merged]

> Username: kylelutz  
> Created at: 2015-10-02 05:50:42 UTC  
> Updated at: 2015-10-10 14:59:01 UTC  
> Merged at: 2015-10-10 14:58:59 UTC  
> Closed at: 2015-10-10 14:58:59 UTC  
> Url: https://github.com/boostorg/compute/pull/521  

This skips the tests for fill() and fill_n() with double values  
if the device does not support the cl_khr_fp64 extension.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-10-02 05:51:45 UTC  
> Url: https://github.com/boostorg/compute/pull/521#issuecomment-144931523  

@haahh This fixes some issues with `test_fill` on my laptop (the GPU doesn't support `double` in kernels). Are there other places we should do the same check?

---

## Comment 2

> Username: jszuppe  
> Created_at: 2015-10-02 06:35:13 UTC  
> Url: https://github.com/boostorg/compute/pull/521#issuecomment-144936013  

I think `double` is used only in `test_fill` and your changes fixes it. I'm not sure, but maybe in some tests we should check if the devices supports atomic operations.

---
