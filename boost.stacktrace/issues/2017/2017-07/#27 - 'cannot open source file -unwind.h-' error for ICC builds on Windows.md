# #27 - 'cannot open source file "unwind.h"' error for ICC builds on Windows [Closed]

> Username: sav-ix  
> Created at: 2017-07-17 16:12:10 UTC  
> Updated at: 2017-07-17 18:55:28 UTC  
> Closed at: 2017-07-17 17:40:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/27  

Hello everyone,  
  
For Boost.Stacktrace builds using ICC on Windows got error:  
```  
.\b2 --build-type=complete --with-stacktrace toolset=intel-17.0-vc14 variant=release link=shared threading=multi runtime-link=shared address-model=64 -j1 -d2 -q  
  
<snip>  
  
"libs\stacktrace\build\..\src\windbg.cpp" -Fo"bin.v2\libs\stacktrace\build\intel-vc14-win-17.0\release\threading-multi\windbg.obj"    -TP /O2 /Ob2 /W4 /GR /MD /Zc:forScope /Zc:wchar_t /favor:blend /nologo /Qwn5 /Qwd985 -Qoption,c,--arg_dep_lookup /Qvc14 -Qpchi- /wd4675 /EHs -c   
-DBOOST_ALL_NO_LIB=1   
-DBOOST_STACKTRACE_DYN_LINK=1   
-DNDEBUG   
"-I."  
compile-c-c++ bin.v2\libs\stacktrace\build\intel-vc14-win-17.0\release\threading-multi\windbg.obj  
  
    call "C:\Users\test\AppData\Local\Temp\b2_intel-win_17.0_iclvars_intel64 vs2015.cmd" > nul   
    icl @"bin.v2\libs\stacktrace\build\intel-vc14-win-17.0\release\threading-multi\windbg.obj.rsp"   
  
windbg.cpp  
.\boost/stacktrace/detail/collect_unwind.ipp(17): catastrophic error: cannot open source file "unwind.h"  
  #include <unwind.h>  
                     ^  
  
compilation aborted for libs\stacktrace\build\..\src\windbg.cpp (code 4)  
...failed compile-c-c++ bin.v2\libs\stacktrace\build\intel-vc14-win-17.0\release\threading-multi\windbg.obj...  
...failed updating 1 target...  
...updated 9 targets...  
```  
  
Reproduced for builds using ICC with `<Debug|Release><Shared|Static>` configurations.  
Not reproduced for builds using mingw-w64 or MSVC.  
  
Just in case, ICC trying imitate MSVC on Windows and GCC on *Linux platforms. Its installation on Windows contain file `unwind.h`, which has references to SEP Development Kit, but seems it's not intended for Windows builds.  
  
Environment:  
- Windows 10 x64,  
- IPSXE 2017 Update 4,  
- MSVC 2015 Update 3,  
- Windows SDK 10.0.14393.33,  
- [Boost-1.65.0-beta1_rc2](https://dl.bintray.com/boostorg/beta/1.65.0.beta.1/source/boost_1_65_0_beta1_rc2.tar.gz).  
  
  
Alexander

---

## Comment 1

> Username: sav-ix  
> Created at: 2017-07-17 18:29:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/27#issuecomment-315840942  

Hello, Antony,  
Just tested your patch for Boost.Stacktrace builds using ICC with `<Debug|Release><Shared|Static>` configurations and error not reproduced.  
Thank you for fix.

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-07-17 18:55:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/27#issuecomment-315848093  

Thanks for reporting the issue!
