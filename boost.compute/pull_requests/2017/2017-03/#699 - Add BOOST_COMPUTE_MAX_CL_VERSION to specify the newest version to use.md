# #699 Add BOOST_COMPUTE_MAX_CL_VERSION to specify the newest version to use  [Merged]

> Username: jszuppe  
> Created at: 2017-03-29 20:25:16 UTC  
> Updated at: 2017-04-24 16:07:01 UTC  
> Merged at: 2017-04-24 16:07:01 UTC  
> Closed at: 2017-04-24 16:07:01 UTC  
> Url: https://github.com/boostorg/compute/pull/699  

This resolves https://github.com/boostorg/compute/issues/647. I was not able to push to https://github.com/amirshavit/compute/tree/dev_max_cl_ver, so that's why this is in new merge request. See https://github.com/boostorg/compute/pull/650 and my comments in it.

---

## Comment 1

> Username: coveralls  
> Created_at: 2017-03-29 23:26:44 UTC  
> Url: https://github.com/boostorg/compute/pull/699#issuecomment-290256749  

[![Coverage Status](https://coveralls.io/builds/10838442/badge)](https://coveralls.io/builds/10838442)  
  
Coverage remained the same at 83.426% when pulling **17be2b8c694c3b3fd1ac1cdf2651401adccf7920 on haahh:dev_max_cl_ver** into **6f9e6cf650f4fedb5670cfea2b3f70a584ccd8d2 on boostorg:develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-04-23 21:15:22 UTC  
> Url: https://github.com/boostorg/compute/pull/699#issuecomment-296489201  

[![Coverage Status](https://coveralls.io/builds/11203687/badge)](https://coveralls.io/builds/11203687)  
  
Coverage remained the same at 83.699% when pulling **568e0fe5bb200fdba5ae760b145801104fe7cc52 on haahh:dev_max_cl_ver** into **60a588955f945726e9a90ab0413f942afb354243 on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-04-24 16:06:32 UTC  
> Url: https://github.com/boostorg/compute/pull/699#issuecomment-296721680  

In https://github.com/boostorg/compute/pull/699/commits/568e0fe5bb200fdba5ae760b145801104fe7cc52 I used `BOOST_COMPUTE_MAX_CL_VERSION` macro and OpenCL 2.1 headers in Travis script, and everything works correctly.

---
