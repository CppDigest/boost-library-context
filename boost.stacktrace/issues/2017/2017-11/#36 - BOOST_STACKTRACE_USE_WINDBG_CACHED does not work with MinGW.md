# #36 - BOOST_STACKTRACE_USE_WINDBG_CACHED does not work with MinGW [Closed]

> Username: githubuser0xFFFF  
> Created at: 2017-11-03 07:47:47 UTC  
> Updated at: 2017-11-24 20:26:36 UTC  
> Closed at: 2017-11-24 20:26:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36  

We use the MinGW toolchain that ships with the Qt Libraries. This is the output of gcc -v  
  
```  
Thread model: posix  
gcc version 5.3.0 (i686-posix-dwarf-rev0, Built by MinGW-W64 project)  
```  
Using **BOOST_STACKTRACE_USE_WINDBG_CACHED** results in the following compiler error:  
  
```  
../boost/stacktrace/detail/frame_msvc.ipp(167): fatal error C1189: #error: Your compiler does not support C++11 thread_local storage. It's impossible to build with BOOST_STACKTRACE_USE_WINDBG_CACHED.  
```  
  
These are the compiller settings:  
  
```  
 -O0 -g3 -Wall -c -fmessage-length=0 -std=c++14  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-11-04 20:09:37 UTC  
> Updated at: 2017-11-04 20:09:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36#issuecomment-341926257  

Yes, that's a known behaviour. What change are you requesting?

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-11-05 17:24:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36#issuecomment-341989658  

Tried to reflect that behaviour in docs in 8ab572d. Is it OK now? You can see the changes [here](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html#stacktrace.configuration_and_build.mingw_specific_notes)

---

## Comment 3

> Username: githubuser0xFFFF  
> Created at: 2017-11-05 18:21:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36#issuecomment-341993662  

Sorry, if I misunderstand something, but the [documentation](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html#stacktrace.configuration_and_build.mingw_specific_notes) says:  
  
> MinGW-w64 users may use the BOOST_STACKTRACE_USE_WINDBG and BOOST_STACKTRACE_USE_WINDBG_CACHED for getting the best results.  
  
But I use a MinGW-w64 compiler and **BOOST_STACKTRACE_USE_WINDBG_CACHED** does not even compile. Have I overlooked something or do I have the wrong compiler?

---

## Comment 4

> Username: apolukhin  
> Created at: 2017-11-12 19:40:35 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36#issuecomment-343761548  

OK, I've reproduced the issue locally. The issue is related to this comment https://sourceforge.net/p/mingw-w64/bugs/527/#6b1a  
  
In other words, `thread_local` depends on MinGW build. Just dropped the MinGW* mentions in BOOST_STACKTRACE_USE_WINDBG_CACHED description.  
  
See updated [docs here](http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html)

---

## Comment 5

> Username: apolukhin  
> Created at: 2017-11-24 20:26:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/36#issuecomment-346892399  

Merged the docs to master
