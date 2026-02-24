# #17 - BOOST_PP_VARIADICS_MSVC is not set to 1 when building with CUDA+MSVC [Closed]

> Username: cedrou  
> Created at: 2018-09-06 16:31:37 UTC  
> Updated at: 2018-09-11 15:13:58 UTC  
> Closed at: 2018-09-11 15:13:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/17  

When compiling a CUDA file on Windows with the Microsoft Visual C++ toolchain, it fails with errors similar to those in issue #15.  
  
It appears that, in the file `include\boost\preprocessor\config\config.hpp`, the macro `BOOST_PP_VARIADICS_MSVC` is set to 0 whereas `BOOST_PP_VARIADICS` is set to 1.  
  
In this case, both `__CUDACC__` and `_MSC_VER` are defined:  
- if `BOOST_PP_VARIADICS` is not predefined, the test case for `_MSC_VER` in line 81 that would set `BOOST_PP_VARIADICS_MSVC` to 1 is not evaluated because the test for `__CUDACC__` is evaluated before.  
- if `BOOST_PP_VARIADICS` is predefined to 1, the test case for `_MSC_VER` in line 97 is false because `__CUDACC__` is defined.  
  
The only workaround to this is to fully disable the variadics (predefine `BOOST_PP_VARIADICS` to 0)

---

## Comment 1

> Username: eldiener  
> Created at: 2018-09-10 21:40:34 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/17#issuecomment-420070636  

I have decide to just back out the change which was made to specify that CUDA files support variadic macros. It is possible that I should not turn off variadic macro support for CUDA files in config.h but rely on whatever other predefined compiler macros exist when compiling CUDA files, but until I can get definitive answers regarding what nvcc preprocessing entails on various platforms when compiling CUDA files, and what compiler macros are actually defined when this happens I think it is safer to just return to the previous situation. Thanks for everybody bringing up this issue, even if it is a real headache for myself as the current Boost PP maintainer.
