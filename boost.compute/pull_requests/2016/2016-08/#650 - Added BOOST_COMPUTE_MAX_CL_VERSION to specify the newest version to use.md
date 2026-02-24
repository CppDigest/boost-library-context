# #650 Added BOOST_COMPUTE_MAX_CL_VERSION to specify the newest version to use [Closed]

> Username: amirshavit  
> Created at: 2016-08-23 13:56:42 UTC  
> Updated at: 2017-03-29 20:25:30 UTC  
> Closed at: 2017-03-29 20:25:30 UTC  
> Url: https://github.com/boostorg/compute/pull/650  

See #647

---

## Comment 1

> Username: kylelutz  
> Created_at: 2016-09-14 03:18:12 UTC  
> Url: https://github.com/boostorg/compute/pull/650#issuecomment-246894632  

Thanks for the patch! This does seem like a good approach, though with a big change like this we'll need to do some testing. Will try to get this merged in the next Boost release.

---

## Comment 2

> Username: coveralls  
> Created_at: 2017-03-25 13:50:04 UTC  
> Url: https://github.com/boostorg/compute/pull/650#issuecomment-289212837  

[![Coverage Status](https://coveralls.io/builds/10770764/badge)](https://coveralls.io/builds/10770764)  
  
Coverage increased (+0.07%) to 83.407% when pulling **d22be2815290e08d2e8e1b7253adc5873f49db05 on amirshavit:dev_max_cl_ver** into **82f588f24ed2640bca4bf3f31dfcb06703d9bc13 on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2017-03-25 17:38:02 UTC  
> Updated_at: 2017-03-26 18:11:35 UTC  
> Url: https://github.com/boostorg/compute/pull/650#issuecomment-289227108  

I'll test it on 1.2, 2.0 and 2.1 implementations, check if it covers all `#ifdef`s.

---

## Comment 4

> Username: jszuppe  
> Created_at: 2017-03-26 18:38:34 UTC  
> Updated_at: 2017-03-29 19:42:18 UTC  
> Url: https://github.com/boostorg/compute/pull/650#issuecomment-289305024  

You did not apply changes (`CL_VERSION_X_Y` to `BOOST_COMPUTE_CL_VERSION_X_Y`) in tests.   
  
Also, I think that in each  
```cpp  
#if defined(CL_VERSION_X_Y)  
#  if !defined(BOOST_COMPUTE_MAX_CL_VERSION) || BOOST_COMPUTE_MAX_CL_VERSION >= X0Y  
#    define BOOST_COMPUTE_CL_VERSION_X_Y  
#  elif !defined(CL_USE_DEPRECATED_OPENCL_Z_W_APIS)  
#    define CL_USE_DEPRECATED_OPENCL_Z_W_APIS  
#  endif  
#endif  
```  
we should define macro `CL_USE_DEPRECATED_OPENCL_Z_W_APIS` if `BOOST_COMPUTE_CL_VERSION_X_Y` can not be defined (where `Z_W` is previous OpenCL version). This covers situations when we have 2.1 headers but want to use OpenCL 2.0 etc.

---
