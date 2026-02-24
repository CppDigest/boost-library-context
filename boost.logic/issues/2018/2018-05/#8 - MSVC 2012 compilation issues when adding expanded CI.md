# #8 - MSVC 2012 compilation issues when adding expanded CI [Closed]

> Username: jeking3  
> Created at: 2018-05-21 01:49:15 UTC  
> Updated at: 2022-04-27 17:17:04 UTC  
> Closed at: 2022-04-27 17:17:04 UTC  
> Url: https://github.com/boostorg/logic/issues/8  

Job: https://ci.appveyor.com/project/jeking3/logic/build/1.0.1-develop/job/rt8dey0t8pawppdy#L598  
  
Error:  
```  
libs\logic\test\tribool_io_test.cpp(50) : error C2383: '_Ty' : default-arguments are not allowed on this symbol  
    call "bin.v2\standalone\msvc\msvc-11.0\msvc-setup.bat" x86 >nul  
 cl /Zm800 -nologo @"bin.v2\libs\logic\test\tribool_io_test.test\msvc-11.0\release\cxxstd-03-iso\threading-multi\tribool_io_test.obj.rsp"   
...failed compile-c-c++ bin.v2\libs\logic\test\tribool_io_test.test\msvc-11.0\release\cxxstd-03-iso\threading-multi\tribool_io_test.obj...  
```  
  
This is the only failure across all the new CI jobs for Boost.Logic.

---

## Comment 1

> Username: jeking3  
> Created at: 2018-07-04 17:27:08 UTC  
> Url: https://github.com/boostorg/logic/issues/8#issuecomment-402530991  

When fixing this please remove the MSVC 2012 appveyor.yml "MAYFAIL" and "REASON" settings, so the build can fail if there are any other problems in the future.

---

## Comment 2

> Username: jeking3  
> Created at: 2022-04-27 17:17:04 UTC  
> Url: https://github.com/boostorg/logic/issues/8#issuecomment-1111265473  

MSVC 2012 is EOL so I am dropping this.
