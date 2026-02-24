# #15 - Broken on CUDA? [Closed]

> Username: jzmaddock  
> Created at: 2018-06-23 18:33:08 UTC  
> Updated at: 2019-09-27 05:50:44 UTC  
> Closed at: 2018-09-11 15:14:20 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15  

When compiling with nvcc for the device, Boost.Preprocessor seems to have been broken somewhere between 1.66 and 1.67.  Post 1.67 when compiling a .cu file containing simply:  
  
```  
#include <boost/mpl/or.hpp>  
```  
  
I see:  
  
```  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "T2" is undefined  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "BOOST_PP_IIF_BOOST_PP_BOOL_BOOST_PP_TUPLE_ELEM_O_1" is undefined  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a ")"  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "BOOST_PP_WHILE_2" is undefined  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: function returning function is not allowed  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "BOOST_PP_SUB_P" is undefined  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "BOOST_PP_SUB_O" is undefined  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a type specifier  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: expected a "," or ">"  
  
../../../../..\boost/mpl/aux_/preprocessed/plain/or.hpp(58): error: identifier "T2" is undefined  
```  
  
and many more such errors.  Reverting libs/preprocessor alone to 1.66 solves the issue.  Any ideas?  Let me know if you want me to test anything locally.

---

## Comment 1

> Username: eldiener  
> Created at: 2018-06-23 22:39:06 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-399715093  

Would you please try to define the macro BOOST_PP_VARIADICS to 0 either on the command line or at the beginning of your source file(s), and see if this fixes the problem you are seeing. There was a report that cuda always supported variadic macros, so it was turned on by default in Boost PP, where previously it had been turned off by default. By defining BOOST_PP_VARIADICS to a value of 0 you are overriding the default setting for your compiler in Boost PP.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2018-06-30 17:14:54 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-401554287  

Testing with CUDA-9, but I believe 8 is the same:  
  
nvcc handles variadics just fine in .cpp files, the error occurs when compiling either host or device code in .cu files.  
  
Using:  
  
```  
#ifdef __CUDACC__  
#define BOOST_PP_VARIADICS 0  
#endif  
```  
  
Fixes things, and has no effect on .cpp files compiled with nvcc.  
  
Thanks!  John.

---

## Comment 3

> Username: eldiener  
> Created at: 2018-06-30 23:29:02 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-401572640  

Why would .cpp files be different from .cu files ? Don't they both eventually include Boost PP .hpp files, whether directly or through some other Boost library's .hpp files ?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-07-01 07:43:10 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-401590269  

>Why would .cpp files be different from .cu files ? Don't they both eventually include Boost PP .hpp files, whether directly or through some other Boost library's .hpp files ?  
  
Sure, but we're effectively using two different compilers - .cpp files get minimal processing before being passed on to the host compiler, where as .cu files get split into host and device code with device code handled by nvcc and host by msvc (in my case).  This requires much more complete preprocessing by nvcc before the actual compiler sees the code.  BTW I can't tell you whether this is specific to CUDA on windows with msvc, but I doubt it given that it seems to be an nvcc front end issue.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2018-07-01 07:45:22 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-401590382  

BTW, `__NVCC__` is always defined by the compiler, and `__CUDACC__` only when preprocessing .cu files (which may contain device code).

---

## Comment 6

> Username: cedrou  
> Created at: 2018-07-06 10:09:54 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-402991208  

I had the same problem on Windows with MS vc++ compiler. I found out that there is a special workaround flag in [config.h](https://github.com/boostorg/preprocessor/blob/00b2f4462445d7dd17bd11a8c8fdc26b2e41d653/include/boost/preprocessor/config/config.hpp) (`BOOST_PP_VARIADICS_MSVC`). But this flag is never enabled when using nvcc with vc++.  
  
The simplest fix I found was to move the test for `__CUDACC__` in [config.h:76](https://github.com/boostorg/preprocessor/blob/00b2f4462445d7dd17bd11a8c8fdc26b2e41d653/include/boost/preprocessor/config/config.hpp#L76) below the test for `_MSC_VER` in [config.h:85](https://github.com/boostorg/preprocessor/blob/00b2f4462445d7dd17bd11a8c8fdc26b2e41d653/include/boost/preprocessor/config/config.hpp#L85)

---

## Comment 7

> Username: eldiener  
> Created at: 2018-07-18 01:28:08 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-405779375  

I installed CUDA to see what is going on, but there is no Boost build doc on how to setup the nvcc toolset or even what it is called. Can anybody specify a Boost build toolset setup I can try ?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2018-07-18 11:16:54 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-405896796  

I don't think there is one - certainly not for .cu files.  Suggest you try from the VS IDE assuming you installed CUDA integration.  Or just create a blank .cu file that contains:  
  
```  
#include <boost/mpl/or.hpp>  
```  
  
and try compiling with nvcc.  
  
The relevant macros are:  
  
* `__NVCC__` always for nvcc  
* `__CUDACC__` when compiling .cu files.  
* `__CUDA_ARCH__` when the .cu file is targeting the device.

---

## Comment 9

> Username: eldiener  
> Created at: 2018-07-18 11:35:45 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-405900903  

Thanks, John. It is a little hard to run bjam tests when there is no support for the compiler in Boost build. I will have to manually extract whatever tests I want to run and then figure out an nvcc command line for running them.

---

## Comment 10

> Username: jzmaddock  
> Created at: 2018-07-18 12:07:58 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-405908633  

>It is a little hard to run bjam tests when there is no support for the compiler in Boost build. I will have to manually extract whatever tests I want to run and then figure out an nvcc command line for running them.  
  
Right, but it's worse than that, because your existing tests which are all in .cpp will pass just fine as nvcc basically just passes everything on the cl in that case.  It's only compiling .cu files which causes the issue.  
  
If you must have bjam support then there is tentative "sort of works" support in https://github.com/boostorg/math/blob/cuda/test/cuda/cu.jam.  See also https://github.com/boostorg/math/blob/cuda/test/cuda/Jamfile.v2 for use.  This works but dependency scanning doesn't work...  
  
Note however, that there is no CUDA support on any of the CI testers, so any kind of automated testing is a bit of a lost cause at present :(

---

## Comment 11

> Username: eldiener  
> Created at: 2018-09-06 21:43:31 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-419251152  

I am still baffled, when reading the CUDA and nvcc documemntation, of which compiler is preprocessing the source code when either a .cu file is being compiled or a .c/.cpp file is being compiled. But in either case why should __CUDACC__ ever be considered by the config.h since either nvcc or some host compiler is executing the preprocessor ?

---

## Comment 12

> Username: eldiener  
> Created at: 2018-09-10 21:40:14 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/15#issuecomment-420070553  

I have decide to just back out the change which was made to specify that CUDA files support variadic macros. It is possible that I should not turn off variadic macro support for CUDA files in config.h but rely on whatever other predefined compiler macros exist when compiling CUDA files, but until I can get definitive answers regarding what nvcc preprocessing entails on various platforms when compiling CUDA files, and what compiler macros are actually defined when this happens I think it is safer to just return to the previous situation. Thanks for everybody bringing up this issue, even if it is a real headache for myself as the current Boost PP maintainer.
