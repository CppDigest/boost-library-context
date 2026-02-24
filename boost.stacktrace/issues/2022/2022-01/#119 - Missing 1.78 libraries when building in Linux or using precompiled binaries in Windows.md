# #119 - Missing 1.78 libraries when building in Linux or using precompiled binaries in Windows [Closed]

> Username: alvaropalmaaste  
> Created at: 2022-01-21 15:20:07 UTC  
> Updated at: 2022-09-12 13:29:37 UTC  
> Closed at: 2022-09-12 13:29:37 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/119  

I recently downloaded Boost 1.78, but when building it for Linux using:  
```  
./bootstrap.sh --with-python=/usr/bin/python3 --with-libraries=all  
./b2 --layout=versioned cxxflags="-fPIC" toolset=gcc variant=release link=shared,static threading=multi runtime-link=shared install  
```  
I noticed the following libraries are built, BUT not installed under `/usr/local/lib`:  
```  
libboost_fiber  
libboost_stacktrace_addr2line  
libboost_stacktrace_basic  
libboost_stacktrace_noop  
```  
In the other hand, for Windows I downloaded the binaries from the "[official](https://sourceforge.net/projects/boost/files/boost-binaries/1.78.0/)" place, but when installed, the following libraries are not there either (for Visual Studio 2019, which is MSVC 14.2):  
```  
libboost_stacktrace_noop-vc142-mt-gd-x64-1_78.lib  
libboost_stacktrace_noop-vc142-mt-s-x64-1_78.lib  
libboost_stacktrace_noop-vc142-mt-sgd-x64-1_78.lib  
libboost_stacktrace_noop-vc142-mt-x64-1_78.lib  
libboost_stacktrace_windbg-vc142-mt-gd-x64-1_78.lib  
libboost_stacktrace_windbg-vc142-mt-s-x64-1_78.lib  
libboost_stacktrace_windbg-vc142-mt-sgd-x64-1_78.lib  
libboost_stacktrace_windbg-vc142-mt-x64-1_78.lib  
libboost_stacktrace_windbg_cached-vc142-mt-gd-x64-1_78.lib  
libboost_stacktrace_windbg_cached-vc142-mt-s-x64-1_78.lib  
libboost_stacktrace_windbg_cached-vc142-mt-sgd-x64-1_78.lib  
libboost_stacktrace_windbg_cached-vc142-mt-x64-1_78.lib  
```  
and in consequences, I can't upgrade to 1.78, since my existing CMake files requires the Stacktrace libraries to be there.

---

## Comment 1

> Username: mclow  
> Created at: 2022-01-21 18:44:48 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/119#issuecomment-1018768997  

We'll start with stack trace.

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-09-12 13:28:11 UTC  
> Updated at: 2022-09-12 13:28:47 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/119#issuecomment-1243744131  

This was fixed in https://github.com/bfgroup/b2/issues/104  
Checked with `boost/libs/stacktrace/build$ ../../../b2 stage`, the files install fine.  
  
Anyway, I'll cleanup the stacktrace Jamfile a little bit
