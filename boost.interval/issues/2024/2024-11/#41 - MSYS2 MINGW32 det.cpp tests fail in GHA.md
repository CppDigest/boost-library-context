# #41 - MSYS2 MINGW32 det.cpp tests fail in GHA [Open]

> Username: jeking3  
> Created at: 2024-11-30 04:26:12 UTC  
> Updated at: 2024-11-30 04:26:12 UTC  
> Url: https://github.com/boostorg/interval/issues/41  

```  
testing.capture-output bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.run  
====== BEGIN OUTPUT ======  
libs/numeric/interval/test/det.cpp(96): test 'test<float>()' failed in function 'int main()'  
libs/numeric/interval/test/det.cpp(97): test 'test<double>()' failed in function 'int main()'  
libs/numeric/interval/test/det.cpp(98): test 'test<long double>()' failed in function 'int main()'  
3 errors detected.  
   
EXIT STATUS: 3   
====== END OUTPUT ======  
  
    set PATH=D:\a\_temp\msys64\mingw32\bin;D:\a\_temp\msys64\mingw32\lib;D:\a\_temp\msys64\mingw32\lib32;D:\a\_temp\msys64\mingw32\lib64;%PATH%  
  
     "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.exe"   > "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.output" "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.run" >NUL  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output bin.v2\libs\numeric\interval\test\det.test\gcc-14\release\x86_32\cxxstd-03-iso\link-static\threadapi-win32\threading-multi\visibility-hidden\det.run...  
```
