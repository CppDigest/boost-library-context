# #112 - cannot find -lDbgeng [Open]

> Username: dvirtz  
> Created at: 2021-09-01 12:31:47 UTC  
> Updated at: 2024-09-12 07:55:16 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/112  

I'm building on Linux with mingw-w64 and the compiler fails to link to Dbgeng, presumably because the library name is in lower case  
  
```  
> find /opt/mingw-w64-build/ -name '*bgeng*'  
/opt/mingw-w64-build/x86_64-w64-mingw32/x86_64-w64-mingw32/include/dbgeng.h  
/opt/mingw-w64-build/x86_64-w64-mingw32/x86_64-w64-mingw32/lib/libdbgeng.a  
/opt/mingw-w64-build/x86_64-w64-mingw32/x86_64-w64-mingw32/lib32/libdbgeng.a  
```  
  
from `config.log`:  
```  
/opt/mingw-w64-build/x86_64-w64-mingw32/lib/gcc/x86_64-w64-mingw32/10.2.0/../../../../x86_64-w64-mingw32/bin/ld: cannot find -lDbgeng  
collect2: error: ld returned 1 exit status  
...skipped <p/root/.conan/data/boost/1.76.0/_/_/build/24c72ffed451b48e203e1f76e0b9681825a52595/boost/bin.v2/libs/stacktrace/build/gcc-10.2./rls/trgt-os-wndws/thrd-mlt/vsblt-hdn>WinDbg.output for lack of <p/root/.conan/data/boost/1.76.0/_/_/build/24c72ffed451b48e203e1f76e0b9681825a52595/boost/bin.v2/libs/stacktrace/build/gcc-10.2./rls/trgt-os-wndws/thrd-mlt/vsblt-hdn>WinDbg_exe.exe...  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 07:49:11 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/112#issuecomment-2345514553  

I have no access to that platform. Please try to lowercase the Dbgeng, check if that works for you. If that helps - send a PR with a fix and we'll see in the CI if the fix breaks some other platforms
