# #33 - tests take too long to complete on variant=debug [Open]

> Username: Kojoley  
> Created at: 2019-03-30 17:15:54 UTC  
> Updated at: 2023-01-29 00:39:28 UTC  
> Url: https://github.com/boostorg/sort/issues/33  

Typed `b2 -j2 variant=debug threading=multi link=shared address-model=64 toolset=msvc-14.1 warnings=off libs/sort/test//test_parallel_stable_sort` and it took about 5-7 minutes to complete (with `variant=release` it is done in seconds).  
  
It does build with optimizations though:  
```  
     Usage requirements for test_parallel_stable_sort:  <include>.  
     Build properties:  <abi>ms <address-model>64 <architecture>x86 <asynch-exceptions>off <binary-format>pe <context-impl>fcontext <debug-store>object <debug-symbols>on <deduced-address-model>32 <deduced-architecture>x86 <define>BOOST_ALL_NO_LIB=1 <doxygen.doxproc.index>no <doxygen.processor>xsltproc <embed-manifest>on <exception-handling>on <extern-c-nothrow>off <format>html <hardcode-dll-paths>true <host-os>windows <implicit-dependency>object(notfile-target)@9043 <include>. <include>.. <inlining>off <install-dependencies>off <link>shared <local-visibility>hidden <location-prefix>test_parallel_stable_sort.test <midl-robust>yes <midl-stubless-proxy>yes <optimization>speed <os>NT <pch>on <preserve-test-targets>on <profiling>off <python-debugging>off <python.interpreter>C:\Users\SERPENT\AppData\Local\Programs\Python\Python37\python <python>3.7 <relevant>build:<relevant>address-model <relevant>build:<relevant>architecture <relevant>build:<relevant>cxxstd <relevant>build:<relevant>cxxstd-dialect <relevant>build:<relevant>target-os <relevant>build:<relevant>toolset <relevant>build:<relevant>toolset-msvc:vendor <relevant>build:<relevant>toolset-msvc:version <relevant>define:<relevant>toolset <relevant>link:<relevant>toolset <relevant>python.interpreter:<relevant>address-model <relevant>python.interpreter:<relevant>python <relevant>python.interpreter:<relevant>target-os <relevant>threading:<relevant>runtime-link <relevant>threading:<relevant>toolset <relevant>toolset <rtti>on <runtime-debugging>on <runtime-link>shared <stdlib>native <strip>off <suppress-import-lib>false <symlink-location>project-relative <tag>@Jamfile<C:\Working\Repositories\boost>%Jamfile<C:\Working\Repositories\boost>.tag <target-os>windows <testing.execute>on <threadapi>win32 <threading>multi <toolset-msvc:version>14.1 <toolset>msvc <user-interface>console <variant>debug <vectorize>off <visibility>hidden <warnings-as-errors>off <warnings>off <windows-api>desktop <xsl:param>boost.defaults=Boost  
     Usage requirements from test_parallel_stable_sort:  <include>. <relevant>address-model <relevant>architecture <relevant>asynch-exceptions <relevant>context-impl <relevant>cxxstd <relevant>cxxstd-dialect <relevant>debug-store <relevant>debug-symbols <relevant>deduced-address-model <relevant>deduced-architecture <relevant>embed-manifest <relevant>exception-handling <relevant>extern-c-nothrow <relevant>htm <relevant>inlining <relevant>instruction-set <relevant>known-warnings <relevant>link <relevant>numa <relevant>optimization <relevant>pch <relevant>pch-on:version <relevant>preserve-test-targets <relevant>rtti <relevant>runtime-debugging <relevant>runtime-link <relevant>segmented-stacks <relevant>target-os <relevant>testing.execute <relevant>threading <relevant>toolset <relevant>toolset-msvc:vendor <relevant>toolset-msvc:version <relevant>valgrind <relevant>variant <relevant>warnings <relevant>warnings-as-errors <relevant>wavetool <relevant>windows-api  
common.mkdir bin.v2\libs\sort  
  
        if not exist "bin.v2\libs\sort\\" mkdir "bin.v2\libs\sort"  
      
common.mkdir bin.v2\libs\sort\test  
  
        if not exist "bin.v2\libs\sort\test\\" mkdir "bin.v2\libs\sort\test"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed"  
      
common.mkdir bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi  
  
        if not exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\\" mkdir "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi"  
      
  
file bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.obj.rsp  
"libs\sort\test\test_parallel_stable_sort.cpp" -Fo"bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.obj"    -TP /O2 /Z7 /Ob0 /W0 /GR /MDd /Zc:forScope /Zc:wchar_t /wd4996 /favor:blend /wd4675 /EHs -c   
-DBOOST_ALL_NO_LIB=1   
"-I."   
"-I.."  
compile-c-c++ bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.obj  
  
    call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 cl /Zm800 -nologo @"bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.obj.rsp"   
  
test_parallel_stable_sort.cpp  
  
file bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe.rsp  
  
"bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.obj"      
msvc.link bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe  
  
        call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 link /NOLOGO /INCREMENTAL:NO /DEBUG /MACHINE:X64 /MANIFEST /subsystem:console /out:"bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe"   @"bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe.rsp"  
        if %ERRORLEVEL% NEQ 0 EXIT %ERRORLEVEL%  
      
msvc.manifest bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe  
  
        if exist "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe.manifest" (  
            call "bin.v2\standalone\msvc\msvc-14.1\address-model-64\architecture-x86\msvc-setup.bat"  >nul  
 mt -nologo -manifest "bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe.manifest" "-outputresource:bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.exe;1"  
        )  
      
testing.capture-output bin.v2\libs\sort\test\test_parallel_stable_sort.test\msvc-14.1\debug\address-model-64\optimization-speed\threading-multi\test_parallel_stable_sort.run  
```

---

## Comment 1

> Username: spreadsort  
> Created at: 2021-01-24 12:17:47 UTC  
> Url: https://github.com/boostorg/sort/issues/33#issuecomment-766338763  

Is there a target speed?  Some issues will only occur in larger sorts.
