# #426 - [regression] Boost 1.70.0: Visual Studio 15 2017 amd64 b2 error [Closed]

> Username: ruslo  
> Created at: 2019-04-12 12:05:52 UTC  
> Updated at: 2019-04-12 19:44:13 UTC  
> Closed at: 2019-04-12 19:44:13 UTC  
> Url: https://github.com/boostorg/build/issues/426  

Download and unpack:  
- https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.zip  
  
Start "x64 Native Tools Command Prompt for VS 2017":  
  
```  
**********************************************************************  
** Visual Studio 2017 Developer Command Prompt v15.9.11  
** Copyright (c) 2017 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x64'  
  
>  
```  
  
`boost.user.jam` content:  
```  
using msvc  
  :   
  : "C:\...\msvc\2017\VC\Tools\MSVC\14.16.27023\bin\Hostx86\x64\cl.exe"  
  : <archiver>""  
 <ranlib>""  
  <compileflags>/std:c++17  
  <compileflags>/DWIN32  
  <compileflags>/D_WINDOWS  
  <compileflags>/W3  
  <compileflags>/GR  
  <compileflags>/EHsc  
  <compileflags>/DBOOST_ALL_NO_LIB=1  
  <compileflags>/DNOMINMAX  
  <compileflags>/DHAVE_ROUND  
;  
```  
  
Run build:  
```  
> cd boost_1_70_0  
[boost_1_70_0]> .\bootstrap.bat  
  
[boost_1_70_0]> .\b2 -a link=static threading=multi variant=debug,release --layout=tagged toolset=msvc --user-config=C:/.../boost_1_70_0/boost.user.jam --with-system linkflags=/machine:x64 address-model=64 --ignore-site-config  
```  
  
Error:  
```  
C:\...\boost_1_70_0\boost.user.jam:6: Unescaped special character in argument <compileflags>/std:c++17  
C:\...\boost_1_70_0\boost.user.jam:12: Unescaped special character in argument <compileflags>-std:c++17  
  
warning: Did not find command for MSVC toolset. If you have Visual Studio 2017 installed you will need to specify the full path to the command, set VS150COMNTOOLS for your installation, or build from the 'Visual Studio Command Prompt for VS 2017'.  
  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
  
Building the Boost C++ Libraries.  
  
  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - contract                 : not building  
    - coroutine                : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - headers                  : not building  
    - iostreams                : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - mpi                      : not building  
    - program_options          : not building  
    - python                   : not building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - stacktrace               : not building  
    - system                   : building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - type_erasure             : not building  
    - wave                     : not building  
  
...found 124 targets...  
...updating 19 targets...  
compile-c-c++ bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\error_code.obj  
msvc.archive bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib  
The filename, directory name, or volume label syntax is incorrect.  
  
        if exist "bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib" DEL "bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib"  
           /out:"bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib" @"bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib.rsp"  
  
...failed msvc.archive bin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi\libboost_system-mt-gd-x64.lib...  
...skipped <pC:\...\boost_1_70_0\stage\lib>libboost_system-mt-gd-x64.lib for lack of <pbin.v2\libs\system\build\msvc-6.0\debug\link-static\threading-multi>libboost_system-mt-gd-x64.lib...  
compile-c-c++ bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\error_code.obj  
msvc.archive bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib  
The filename, directory name, or volume label syntax is incorrect.  
  
        if exist "bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib" DEL "bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib"  
           /out:"bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib" @"bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib.rsp"  
  
...failed msvc.archive bin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi\libboost_system-mt-x64.lib...  
...skipped <pC:\...\boost_1_70_0\stage\lib>libboost_system-mt-x64.lib for lack of <pbin.v2\libs\system\build\msvc-6.0\release\link-static\threading-multi>libboost_system-mt-x64.lib...  
...failed updating 2 targets...  
...skipped 2 targets...  
...updated 15 targets...  
```  
  
> warning: Did not find command for MSVC toolset. If you have Visual Studio 2017 installed you will need to specify the full path to the command, set VS150COMNTOOLS for your installation, or build from the 'Visual Studio Command Prompt for VS 2017'.  
  
I do use VS command prompt and have `VS150COMNTOOLS` environment set.  
  
Same configuration works fine with Boost 1.70.0 beta1 release:  
- https://dl.bintray.com/boostorg/beta/1.70.0.beta1/source/boost_1_70_0_b1.zip  
  
Boost 1.69.0 worked fine too.

---

## Comment 1

> Username: swatanabe  
> Created at: 2019-04-12 12:58:25 UTC  
> Url: https://github.com/boostorg/build/issues/426#issuecomment-482564740  

AMDG  
  
On 4/12/19 6:05 AM, Ruslan Baratov wrote:  
> <snip>  
> using msvc  
>   :   
>   : "C:\...\msvc\2017\VC\Tools\MSVC\14.16.27023\bin\Hostx86\x64\cl.exe"  
  
'\' is an escape character.  Use forward slashes or \\.  
  
>   : <archiver>""  
>  <ranlib>""  
  
These do nothing.  
  
>   <compileflags>/std:c++17  
  
Quote it.  (':' is used to separate arguments)  
  
>   <compileflags>/DWIN32  
>   <compileflags>/D_WINDOWS  
>   <compileflags>/W3  
>   <compileflags>/GR  
>   <compileflags>/EHsc  
>   <compileflags>/DBOOST_ALL_NO_LIB=1  
>   <compileflags>/DNOMINMAX  
>   <compileflags>/DHAVE_ROUND  
> ;  
> ```  
>   
> Run build:  
> ```  
>> cd boost_1_70_0  
> [boost_1_70_0]> .\bootstrap.bat  
>   
> [boost_1_70_0]> .\b2 -a link=static threading=multi variant=debug,release --layout=tagged toolset=msvc --user-config=C:/.../boost_1_70_0/boost.user.jam --with-system linkflags=/machine:x64 address-model=64 --ignore-site-config  
> ```  
>   
> Error:  
> ```  
> C:\...\boost_1_70_0\boost.user.jam:6: Unescaped special character in argument <compileflags>/std:c++17  
> C:\...\boost_1_70_0\boost.user.jam:12: Unescaped special character in argument <compileflags>-std:c++17  
>   
> warning: Did not find command for MSVC toolset. If you have Visual Studio 2017 installed you will need to specify the full path to the command, set VS150COMNTOOLS for your installation, or build from the 'Visual Studio Command Prompt for VS 2017'.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: ruslo  
> Created at: 2019-04-12 13:39:01 UTC  
> Url: https://github.com/boostorg/build/issues/426#issuecomment-482577981  

> `'\'` is an escape character. Use forward slashes or `\\`.  
  
Forward slashes are not working.  
  
Looks better with `\\` in my local test, will run more tests now.  
  
> These do nothing.  
  
It's part of the automatic configuration.  
  
> Quote it.  (':' is used to separate arguments)  
  
Will try that too.  
  
Side note: it was working for years like this why it has changed somewhere between 1.70.0 beta 1 and 1.70.0?

---

## Comment 3

> Username: swatanabe  
> Created at: 2019-04-12 13:52:04 UTC  
> Url: https://github.com/boostorg/build/issues/426#issuecomment-482582505  

AMDG  
  
On 4/12/19 7:39 AM, Ruslan Baratov wrote:  
>> `'\'` is an escape character. Use forward slashes or `\\`.  
>   
> Forward slashes are not working.  
>   
> Looks better with `\\` in my local test, will run more tests now.  
>   
  
Okay.  I see what the problem is.  I would  
actually recommend:  
  
using msvc : 14.1 : :  
  # options  
;  
  
>> Quote it.  (':' is used to separate arguments)  
>   
> Will try that too.  
>   
  
This is only a warning for now, but it will become  
a hard error soonish.  
  
> Side note: it was working for years like this why it has changed somewhere between 1.70.0 beta 1 and 1.70.0?  
>   
  
It only sort of worked.  The path with single backslashes  
was always broken.  It was just ignored entirely, before  
I cleaned things up a bit when adding support for VS2019.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: ruslo  
> Created at: 2019-04-12 19:44:13 UTC  
> Url: https://github.com/boostorg/build/issues/426#issuecomment-482699411  

> will run more tests now  
  
It works. Thanks for the help.
