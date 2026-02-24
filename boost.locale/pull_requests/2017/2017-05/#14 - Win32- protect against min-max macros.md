# #14 Win32: protect against min/max macros [Closed]

> Username: MarcelRaad  
> Created at: 2017-05-28 12:00:08 UTC  
> Updated at: 2022-05-29 10:57:51 UTC  
> Closed at: 2022-05-29 09:39:02 UTC  
> Url: https://github.com/boostorg/locale/pull/14  

Defining `NOMINMAX` doesn't help if `windows.h` gets included before `api.hpp`.  
Use `BOOST_PREVENT_MACRO_SUBSTITUTION` from config instead.  
This fixes compilation with `BOOST_USE_WINDOWS_H` defined,  
which is required for compiling with clang on Windows.

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-05-28 13:13:38 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-304513416  

Note, api.h is internal header that isn't compiler by user. (It is under src section)  
  
So no need this fix - private API.

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2017-05-28 18:04:57 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-304530387  

@artyom-beilis Thanks for your response. Sorry, I forgot to mention that the problem is not being able to compile the library itself, not user code.  
  
```  
In file included from libs\locale\src\win32\numeric.cpp:24:  
libs\locale\src\win32/api.hpp:137:62: error: too few arguments provided to function-like macro invocation  
res.grouping+=std::numeric_limits<char>::min();  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\shared\minwindef.h:197:9: note: macro 'min' defined here  
#define min(a,b)            (((a) < (b)) ? (a) : (b))  
```  
```  
In file included from libs\locale\src\win32\numeric.cpp:24:  
libs\locale\src\win32/api.hpp:140:62: error: too few arguments provided to function-like macro invocation  
res.grouping+=std::numeric_limits<char>::max();  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\shared\minwindef.h:193:9: note: macro 'max' defined here  
#define max(a,b)            (((a) > (b)) ? (a) : (b))  
```  
  
The only alternative that I see would be to move the `NOMINMAX` definition to the jamfile when targeting Windows.

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2017-05-29 08:24:37 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-304603306  

I see...  
  
What platform/compiler version?  
  
It is also possible to define NOMINMAX in the first place windows.h or any other Windows header is included.

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2017-05-29 19:19:37 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-304718183  

This is clang on Windows with Windows SDK 10.0.15063:  
```  
clang version 5.0.0 (trunk)  
Target: i686-pc-windows-msvc  
Thread model: posix  
```

---

## Comment 5

> Username: artyom-beilis  
> Created_at: 2017-07-13 13:51:47 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-315083906  

Does adding   
  
    #ifndef NOMINMAX  
    #define NOMINMAX  
    #endif  
  
To numeric.cpp or the top of the api file helps?  
  
I really don't want to use MACRO_SUBST since it is dirty hack

---

## Comment 6

> Username: MarcelRaad  
> Created_at: 2017-07-13 20:48:55 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-315197848  

Moving it to the very top of api.hpp doesn't help.  
Adding it to the top of numeric.cpp before any includes fixes the compile errors.

---

## Comment 7

> Username: egorpugin  
> Created_at: 2017-08-21 17:01:30 UTC  
> Updated_at: 2017-08-21 17:03:18 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-323799155  

Also hit by this with clang 5.0 + VS2017.3.

---

## Comment 8

> Username: Kojoley  
> Created_at: 2017-12-26 12:54:49 UTC  
> Url: https://github.com/boostorg/locale/pull/14#issuecomment-353965724  

How about placing `<toolset>msvc:<define>NOMINMAX` in `build/Jamfile.v2`?

---
