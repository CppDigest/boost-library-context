# #235 - Ugly GCC -Wattributes warnings that cannot be suppressed using /included/ variant [Closed]

> Username: pabristow  
> Created at: 2019-11-13 10:19:41 UTC  
> Updated at: 2020-01-07 06:43:46 UTC  
> Closed at: 2020-01-07 06:43:26 UTC  
> Url: https://github.com/boostorg/test/issues/235  

I am seeing dozens of warnings (that cannot be suppressed using -W-no-attributes) like this:  
  
```  
Build: Release_gcc810 in hello_boost_test_included (compiler: GNU GCC 8.1)  
  
c++.exe -Wall -fexceptions -O2 -Wno-attributes -flto=thin -I..\..\..\boost -c I:\boost\libs\hello_boost\example\hello_boost_test_included.cpp -o obj\Release\boost\libs\hello_boost\example\hello_boost_test_included.o  
g++.exe  -o bin\Release\hello_boost_test_included.exe obj\Release\boost\libs\hello_boost\example\hello_boost_test_included.o  -s    
I:/boost/boost/test/impl/framework.ipp: In function 'setup_loggers':  
I:/boost/boost/test/impl/framework.ipp:1145:1: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
 }  
 ^  
lto1.exe: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
lto1.exe: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
...  
  
I:/boost/boost/test/impl/unit_test_parameters.ipp: In function 'init':  
I:/boost/boost/test/impl/unit_test_parameters.ipp:745:1: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
 }  
 ^  
I:/boost/boost/test/impl/results_reporter.ipp: In function 'set_stream':  
I:/boost/boost/test/impl/results_reporter.ipp:113:1: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
 }  
 ^  
I:/boost/boost/test/impl/framework.ipp: In function 'test_unit_aborted':  
I:/boost/boost/test/impl/framework.ipp:1757:1: warning: visibility attribute not supported in this configuration; ignored [-Wattributes]  
 }  
..  
  
```  
This seem to be specific to using mingw (and Codeblocks 17.12)   
  
[https://stackoverflow.com/questions/26025708/warning-visibility-attribute-ignored-symbol-visibility-c-gcc](https://stackoverflow.com/questions/26025708/warning-visibility-attribute-ignored-symbol-visibility-c-gcc)  
  
sheds a little light on what might be causing these messages.  
  
Full log of this simple test of Boost.Test available on request.  
  
This is harmless but annoying.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-12-12 20:57:20 UTC  
> Url: https://github.com/boostorg/test/issues/235#issuecomment-565183471  

Hi,  
  
Sorry about those warnings (at some point I need to fix the Appveyor to get those as well). Which Boost Test variant (header/shared/static) are you using in this configuration?

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-12-14 10:59:05 UTC  
> Url: https://github.com/boostorg/test/issues/235#issuecomment-565706858  

Sorry I missed this information from the title of the issue.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-12-14 11:18:38 UTC  
> Url: https://github.com/boostorg/test/issues/235#issuecomment-565708065  

We can try hard to silence the warning, but I remember from a past project that this does not work well, and it seems that this would hide the real issue.  
  
In the included variant, the macro `BOOST_TEST_DECL` evaluates to `BOOST_SYMBOL_VISIBLE` which evaluates to `__attribute__((__visibility__("default")))`, unconditionally. This does not cause any issue in the platforms I am testing on, but apparently MINGW is more picky.  
  
My understanding is that in the included variant, `BOOST_TEST_DECL` can just evaluate to nothing and this should be also valid in all platforms.   
  
The branch `topic/GH-235-gcc-attribute-warnings` is a tentative fix, it would be nice if you can give a try.

---

## Comment 4

> Username: pabristow  
> Created at: 2019-12-16 12:10:59 UTC  
> Url: https://github.com/boostorg/test/issues/235#issuecomment-566035023  

I:\boost\libs\test>git status  
HEAD detached at origin/topic/GH-235-gcc-attribute-warnings  
nothing to commit, working tree clean  
and recompiled my very simple use of Boost.test included with NO errors.  
  
Program: I:\boost\libs\hello_boost\example\hello_boost_test_included.cpp  
BuildInfo:  
  Platform Win32, 64-bit.  
  Compiler GNU C++ version 8.1.0  
  STL GNU libstdc++ version 20180502  
  Boost version 1.71.0  
  
Also builds OK with GCC 9.2.0  
  
BuildInfo:  
  Platform Win32, 64-bit.  
  Compiler GNU C++ version 9.2.0  
  STL GNU libstdc++ version 20190812  
  Boost version 1.71.0  
  
(__MINGW64__is defined with value 1.)  
  
So your fix seems to do the trick for Codeblock 17.2 using  GCC and mingw64 :-)  
  
Many thanks.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2020-01-07 06:43:26 UTC  
> Url: https://github.com/boostorg/test/issues/235#issuecomment-571458377  

Merged to master
