# #146 - Build failure: Windows 10, MinGW-w64, g++ 11.2, -std=c++20/c++23 [Closed]

> Username: francoisk  
> Created at: 2022-02-24 13:12:27 UTC  
> Updated at: 2022-03-07 11:10:22 UTC  
> Closed at: 2022-03-07 11:10:22 UTC  
> Url: https://github.com/boostorg/nowide/issues/146  

I'm getting a warning and compiler error from the following command line (-I paths omitted and path prefixes removed for legibility):  
  
```  
g++ -DBOOST_NOWIDE_HAS_INIT_PRIORITY -DBOOST_NOWIDE_DYN_LINK  
-DBOOST_FILESYSTEM_NO_LIB -DBOOST_FILESYSTEM_DYN_LINK  
-Wall -std=c++23 -finput-charset=UTF-8 -o stat.o -c -x c++ src\stat.cpp  
```  
  
```  
In file included from include/c++/11.2.0/cstddef:49,  
                 from include/boost/config/compiler/gcc.hpp:165,  
                 from include/boost/config.hpp:39,  
                 from include/boost/nowide/config.hpp:14,  
                 from src\stat.cpp:15:  
include/c++/11.2.0/x86_64-w64-mingw32/bits/c++config.h:573:2: warning: #warning "__STRICT_ANSI__ seems to have been undefined; this is not supported" [-Wcpp]  
  573 | #warning "__STRICT_ANSI__ seems to have been undefined; this is not supported"  
      |  ^~~~~~~  
```  
  
```  
In file included from include/c++/11.2.0/bits/max_size_type.h:37,  
                 from include/c++/11.2.0/bits/ranges_base.h:38,  
                 from include/c++/11.2.0/string_view:48,  
                 from include/c++/11.2.0/bits/basic_string.h:48,  
                 from include/c++/11.2.0/string:55,  
                 from include/c++/11.2.0/bits/locale_classes.h:40,  
                 from include/c++/11.2.0/bits/ios_base.h:41,  
                 from include/c++/11.2.0/streambuf:41,  
                 from include/c++/11.2.0/bits/streambuf_iterator.h:35,  
                 from include/c++/11.2.0/iterator:66,  
                 from include/boost/nowide/utf/convert.hpp:15,  
                 from include/boost/nowide/convert.hpp:13,  
                 from include/boost/nowide/stackstring.hpp:11,  
                 from src\stat.cpp:19:  
include/c++/11.2.0/numbers:139:9: error: unable to find numeric literal operator 'operator""Q'  
  139 |       = 2.718281828459045235360287471352662498Q;  
      |         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
include/c++/11.2.0/numbers:139:9: note: use '-fext-numeric-literals' to enable more built-in suffixes  
```  
  
With `std=c++17` I get only the warning.

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-02-26 15:24:53 UTC  
> Url: https://github.com/boostorg/nowide/issues/146#issuecomment-1052198968  

Hello and thanks for reporting this issue. I'm afraid the currently unreleased C++23 standard is currently unsupported, so there might be no solution.  
  
Currently there is CI testing MinGW-w64 8.1.0 on Windows, which passes. So using another MinGW version might workaround the issue.  
  
However I do see where the issue(s) come from, so I might be able to support this. However I'll need to reproduce this first, at least locally.  
Can you provide more information on your setup? I.e. especially the name "MinGW-w64" is due to historic reasons ambiguous. So what/how exactly did you install your MinGW? My guess would be you are using the MSYS2 distribution from https://www.mingw-w64.org/downloads/, is this correct? If you could provide a step-by-step guide to rebuild your setup it would help a lot, if you can still recall.  
  
How exactly do/did you build Boost? Do you actually use the manual compiler invocation or do you use `b2` (as it is meant to be used)?

---

## Comment 2

> Username: Flamefire  
> Created at: 2022-03-02 16:55:22 UTC  
> Url: https://github.com/boostorg/nowide/issues/146#issuecomment-1057152274  

@francoisk Can you tell which MinGW you are using? Is it MSYS2 or something else? If it is, I may have an (easy) fix available

---

## Comment 3

> Username: francoisk  
> Created at: 2022-03-03 08:34:39 UTC  
> Url: https://github.com/boostorg/nowide/issues/146#issuecomment-1057801927  

Hi, sorry for the delayed response.  
  
This report is based on the [build2](https://build2.org/) port of Boost (https://github.com/build2-packaging/boost), so we're using neither b2 nor cmake.  
  
Our MinGW setup is based on the `mingw-w64-x86_64-gcc-11.2.0-1` package from MSYS2. If you want to completely reproduce our environment you can download our [build2-baseutils](https://stage.build2.org/0/0.15.0-a.0/build2-baseutils-0.15.0-a.0-x86_64-windows.zip) and [build2-mingw](https://stage.build2.org/0/0.15.0-a.0/build2-mingw-0.15.0-a.0-x86_64-windows.tar.xz) archives and perhaps [the installation script](https://stage.build2.org/0/0.15.0-a.0/build2-install-mingw-0.15.0-a.0-stage.bat).
