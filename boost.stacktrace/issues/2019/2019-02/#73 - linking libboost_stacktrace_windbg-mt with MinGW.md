# #73 - linking libboost_stacktrace_windbg-mt with MinGW [Closed]

> Username: yuwenyong  
> Created at: 2019-02-07 14:31:18 UTC  
> Updated at: 2019-02-09 10:19:57 UTC  
> Closed at: 2019-02-09 10:19:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/73  

I compiled my program and lib with MinGW64 in debug mode. I don't know why only part of the stacktrace can be displayed.   The following is an example about the stacktrace of my program.  
  
26# 0x00000000004243DE in httpservermt_test  
27# 0x0000000000425802 in httpservermt_test  
28# 0x00000000004255E6 in httpservermt_test  
29# 0x00000000004266F3 in httpservermt_test  
30# 0x00000000004266B3 in httpservermt_test  
31# 0x000000000042664C in httpservermt_test  
32# _atomic_flag_test_and_set_explicit in libstdc___6  
33# _pthread_create_wrapper in libwinpthread_1  
34# _beginthreadex in msvcrt  
35# _endthreadex in msvcrt  
36# _BaseThreadInitThunk in KERNEL32  
37# _RtlUserThreadStart in ntdll

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-02-08 11:39:34 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/73#issuecomment-461776358  

Does linking with libboost_stacktrace_bcktrace fixes the issue? http://boostorg.github.io/stacktrace/stacktrace/configuration_and_build.html

---

## Comment 2

> Username: yuwenyong  
> Created at: 2019-02-09 10:19:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/73#issuecomment-462032103  

Yes, using backtrace fixed this issue
