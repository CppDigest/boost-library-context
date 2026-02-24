# #1322 - BOOST_MATH_SYCL_ENABLED is inescapable [Closed]

> Username: will-saunders-ukaea  
> Created at: 2025-09-25 16:37:23 UTC  
> Updated at: 2025-09-26 13:11:34 UTC  
> Closed at: 2025-09-26 13:11:34 UTC  
> Url: https://github.com/boostorg/math/issues/1322  

The macros at [1] assume that if `SYCL_LANGUAGE_VERSION` is defined then the user wants to use boost math with SYCL. However this macro is defined by the SYCL compiler even when it is compiling host only code.  
  
1) The user may only want to use boost::math on the host and never the device. For example consider test code which uses boost::math as an alternative implementation to test against.  
2) The user may use a intermediate library on the host which transitively includes boost::math.  
  
These macros also assume that `SYCL_EXTERNAL` is defined [2]. However `SYCL_EXTERNAL` is optionally defined by the SYCL implementation if it is supported [3].  
  
These macros define `BOOST_MATH_NO_EXCEPTIONS` which in turn triggers this bug [4]? This might be fixed as of 1.89.0?  
  
Please can boost::math SYCL support be opt-in or at least opt-out?  
  
Many thanks,  
Will  
  
[1] https://github.com/boostorg/math/blob/5e088ffe2ed0e237b9069e3a7352865283d8f196/include/boost/math/tools/config.hpp#L705  
[2] https://github.com/boostorg/math/blob/5e088ffe2ed0e237b9069e3a7352865283d8f196/include/boost/math/tools/config.hpp#L707  
[3] https://registry.khronos.org/SYCL/specs/sycl-2020/html/sycl-2020.html#_preprocessor_directives_and_macros  
[4] https://github.com/boostorg/math/issues/1285

---

## Comment 1

> Username: mborland  
> Created at: 2025-09-26 06:10:15 UTC  
> Url: https://github.com/boostorg/math/issues/1322#issuecomment-3336908626  

Since SYCL itself has opt-in device support I made our device support opt-in as well in the linked PR

---

## Comment 2

> Username: will-saunders-ukaea  
> Created at: 2025-09-26 09:51:45 UTC  
> Url: https://github.com/boostorg/math/issues/1322#issuecomment-3337773946  

Thanks - the linked PR seems to solve the issue.
