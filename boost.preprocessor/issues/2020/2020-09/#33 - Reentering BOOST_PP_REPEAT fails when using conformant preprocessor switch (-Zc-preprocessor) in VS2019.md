# #33 - Reentering BOOST_PP_REPEAT fails when using conformant preprocessor switch (/Zc:preprocessor) in VS2019 [Closed]

> Username: paszklar  
> Created at: 2020-09-04 13:40:51 UTC  
> Updated at: 2020-12-14 16:15:48 UTC  
> Closed at: 2020-12-14 16:15:48 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/33  

The following use of BOOST_PP_REPEAT:  
  
    #include <boost/preprocessor.hpp>  
    #define MACRO_1(z, n, x) BOOST_PP_CAT(x, n)  
    #define MACRO_2(z, n, x) BOOST_PP_REPEAT(n, MACRO_1, x)  
    BOOST_PP_REPEAT(4, MACRO_2, x)  
  
incorrectly expands to:  
  
    BOOST_PP_REPEAT_1(0, MACRO_1, x) BOOST_PP_REPEAT_1(1, MACRO_1, x) BOOST_PP_REPEAT_1(2, MACRO_1, x) BOOST_PP_REPEAT_1(3, MACRO_1, x)   
  
instead of:  
  
     x0 x0 x1 x0 x1 x2  
  
when `/Zc:preprocessor` switch is used. I'm using Visual Studio version 16.7.2 (cl version  19.27.29111) and boost version 1.74.0

---

## Comment 1

> Username: eldiener  
> Created at: 2020-09-08 18:12:49 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/33#issuecomment-689049352  

It is a bug in VC++ and the new preprocessor. I will report this to Microsoft. A workaround is to change the code to:  
  
```  
#include <boost/preprocessor.hpp>  
#define MACRO_1(z, n, x) BOOST_PP_CAT(x, n)  
#define MACRO_2(z, n, x) BOOST_PP_REPEAT_ ## z(n, MACRO_1, x)  
BOOST_PP_REPEAT(4, MACRO_2, x)  
```

---

## Comment 2

> Username: eldiener  
> Created at: 2020-09-09 17:10:42 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/33#issuecomment-689700233  

The code works correctly in the latest version of the Visual Studio Preview version 16.8.0 Preview 2.1. This is version 19.28.29213 of the VC++ compiler. Which means that Microsoft has fixed this issue, but not in VS2019 yet. I do not know at what version it was fixed since I find it impossible to go back to previous preview versions, but Preview versions eventually make their way into the regular VS2019 version. Therefore there is no need to report this to Microsoft.

---

## Comment 3

> Username: eldiener  
> Created at: 2020-12-14 16:15:47 UTC  
> Url: https://github.com/boostorg/preprocessor/issues/33#issuecomment-744546718  

The code now works correctly in Visual Studio 2019 with the latest update, using the new preprocessor, so I will close this issue.
