# #91 - Build on cygwin (32-bit) fails [Closed]

> Username: jeking3  
> Created at: 2018-11-13 20:37:58 UTC  
> Updated at: 2020-11-02 13:57:06 UTC  
> Closed at: 2020-11-02 13:57:06 UTC  
> Url: https://github.com/boostorg/container/issues/91  

Setup:  
```  
    - FLAVOR: cygwin (64-bit)  
      APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2017  
      ADDPATH: C:\cygwin64\bin;  
      B2_ADDRESS_MODEL: address-model=64  
      CXXSTD: 11,17  
      # https://github.com/boostorg/test/issues/144  
      DEFINES: define=_POSIX_C_SOURCE=200112L define=__USE_ISOC99  
      THREADING: threadapi=pthread  
      TOOLSET: gcc  
```  
  
Command:  
```  
b2 libs/container toolset=gcc cxxstd=03,11  define=_POSIX_C_SOURCE=200112L threadapi=pthread address-model=32   variant=release,debug -j3  
```  
  
Output:  
  
https://ci.appveyor.com/project/jeking3/container/build/job/umhtyg2l308r44jj?fullLog=true#L2001  
```  
gcc.link bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.exe  
testing.capture-output bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.run  
====== BEGIN OUTPUT ======  
   
EXIT STATUS: 1   
====== END OUTPUT ======  
    set Path=C:\projects\boost-root\bin.v2\libs\container\build\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden;C:\cygwin\bin;C:\cygwin\lib;C:\cygwin\lib32;C:\cygwin\lib64;%Path%  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.exe"   > "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.output" "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output bin.v2\libs\container\test\string_test.test\gcc-6.4.0\release\cxxstd-11-iso\target-os-cygwin\visibility-hidden\string_test.run...  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-11-02 13:57:06 UTC  
> Url: https://github.com/boostorg/container/issues/91#issuecomment-720487506  

I've tested with cygwin 32 bit, a recent cygwin (gcc 9.3.0):  
  
~/b2.exe toolset=gcc cxxstd=11  define=_POSIX_C_SOURCE=200112L variant=release address-model=32 string_test  
  
and the bug does not reproduce, so I'm closing this.
