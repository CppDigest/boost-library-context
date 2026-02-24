# #259 - [clang-win, mingw] Support of automatic tss cleanup for native threading API not available [Open]

> Username: jeking3  
> Created at: 2018-11-25 15:49:10 UTC  
> Updated at: 2018-11-26 06:36:49 UTC  
> Url: https://github.com/boostorg/thread/issues/259  

From a new CI job targeting `toolset=clang-win`:  
(warning: large data link: https://ci.appveyor.com/project/jeking3/thread/builds/20524620/job/svesllxvmphivmuq?fullLog=true#80760)  
```  
testing.capture-output bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.run  
====== BEGIN OUTPUT ======  
Running 7 test cases...  
tss_instances = 0; tss_total = 5  
tss_instances = 5; tss_total = 5  
libs/thread/test/test_tss.cpp(208): error: in "test_tss": Support of automatic tss cleanup for native threading API not available  
000001CF8AB26D70  
*** 1 failure is detected in the test module "Boost.Threads: tss test suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
      
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.exe"   > "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.output" "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output bin.v2\libs\thread\test\test_tss_lib.test\clng-win-7.0.0\rls\adrs-mdl-64\cxstd-11-iso\thrdp-wn32\thrd-mlt\test_tss_lib.run...  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-25 15:57:11 UTC  
> Url: https://github.com/boostorg/thread/issues/259#issuecomment-441450823  

Also on mingw:  
https://ci.appveyor.com/project/jeking3/thread/builds/20524620/job/man4slwfc0q5jh9o#L5836  
```  
gcc.link bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.exe  
testing.capture-output bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.run  
====== BEGIN OUTPUT ======  
Running 7 test cases...  
tss_instances = 0; tss_total = 5  
tss_instances = 5; tss_total = 5  
libs/thread/test/test_tss.cpp(208): error: in "test_tss": Support of automatic tss cleanup for native threading API not available  
0x1876f50  
*** 1 failure is detected in the test module "Boost.Threads: tss test suite"  
   
EXIT STATUS: 201   
====== END OUTPUT ======  
    set PATH=C:\msys64\mingw64\bin;C:\msys64\mingw64\lib;C:\msys64\mingw64\lib32;C:\msys64\mingw64\lib64;%PATH%  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.exe"   > "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.output" 2>&1   
    set status=%ERRORLEVEL%  
    echo. >> "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.output"  
    echo EXIT STATUS: %status% >> "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.output"  
    if %status% EQU 0 (  
        copy "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.output" "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
...failed testing.capture-output bin.v2\libs\thread\test\test_tss_lib.test\gcc-mngw6\dbg\cxstd-17-iso\thrdp-wn32\thrd-mlt\vsblt-hdn\test_tss_lib.run...  
```

---

## Comment 2

> Username: viboes  
> Created at: 2018-11-26 06:35:03 UTC  
> Url: https://github.com/boostorg/thread/issues/259#issuecomment-441533160  

Here it is the warnings accompanying the check :(  
```  
    // The following is not really an error. TSS cleanup support still is available for boost threads.  
    // Also this usually will be triggered only when bound to the static version of thread lib.  
    // 2006-10-02 Roland Schwarz  
    //BOOST_CHECK_EQUAL(tss_instances, 0);  
```

---

## Comment 3

> Username: viboes  
> Created at: 2018-11-26 06:36:49 UTC  
> Url: https://github.com/boostorg/thread/issues/259#issuecomment-441533412  

What about contacting him?
