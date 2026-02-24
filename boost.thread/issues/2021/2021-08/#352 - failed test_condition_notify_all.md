# #352 - failed test_condition_notify_all [Closed]

> Username: makelinux  
> Created at: 2021-08-01 18:17:06 UTC  
> Updated at: 2021-08-02 18:59:10 UTC  
> Closed at: 2021-08-02 18:57:49 UTC  
> Url: https://github.com/boostorg/thread/issues/352  

00:06:29] testing.capture-output bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.run  
[00:06:29] ====== BEGIN OUTPUT ======  
[00:06:29] Running 1 test case...  
[00:06:29] libs/thread/test/test_condition_notify_all.cpp(197): error: in "test_condition_notify_all": check multiple_wake_count==3 has failed  
[00:06:29] C:/projects/boost-root/libs/thread/test/./util.inl(176): fatal error: in "test_condition_notify_all": Timed test didn't complete in time, possible deadlock.  
[00:06:29]  
[00:06:29] *** 2 failures are detected in the test module "Boost.Threads: condition_variable notify_all test suite"  
[00:06:29]  
[00:06:29] EXIT STATUS: 201  
[00:06:29] ====== END OUTPUT ======  
[00:06:29]  
[00:06:29]  
[00:06:29]     set status=0  
[00:06:29]     if %status% NEQ 0 (  
[00:06:29]         echo Skipping test execution due to testing.execute=off  
[00:06:29]         exit 0  
[00:06:29]     )  
[00:06:29]      "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.exe"   > "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.output" 2>&1  
[00:06:29]     set status=%ERRORLEVEL%  
[00:06:29]     echo. >> "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.output"  
[00:06:29]     echo EXIT STATUS: %status% >> "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.output"  
[00:06:29]     if %status% EQU 0 (  
[00:06:29]         copy "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.output" "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.run"  
[00:06:29]     )  
[00:06:29]     set verbose=0  
[00:06:29]     if %status% NEQ 0 (  
[00:06:29]         set verbose=1  
[00:06:29]     )  
[00:06:29]     if %verbose% EQU 1 (  
[00:06:29]         echo ====== BEGIN OUTPUT ======  
[00:06:29]         type "bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.output"  
[00:06:29]         echo ====== END OUTPUT ======  
[00:06:29]     )  
[00:06:29]     exit %status%  
[00:06:29]  
[00:06:29] ...failed testing.capture-output bin.v2\libs\thread\test\test_condition_notify_all_lib.test\msvc-14.2\rls\thrdp-wn32\thrd-mlt\test_condition_notify_all_lib.run...  
  
https://ci.appveyor.com/project/viboes/thread/builds/40209760/job/g6u09v7iuvxk05v7

---

## Comment 1

> Username: makelinux  
> Created at: 2021-08-02 18:59:10 UTC  
> Url: https://github.com/boostorg/thread/issues/352#issuecomment-891257248  

fixed with   
https://github.com/boostorg/thread/pull/349/commits/97522b4d0f0ffe6eac886d4c7191695e8afaf50c
