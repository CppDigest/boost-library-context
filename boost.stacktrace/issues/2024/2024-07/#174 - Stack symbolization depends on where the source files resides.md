# #174 - Stack symbolization depends on where the source files resides [Closed]

> Username: subbota-a  
> Created at: 2024-07-28 11:34:34 UTC  
> Updated at: 2024-09-12 07:56:23 UTC  
> Closed at: 2024-09-12 07:56:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/174  

For some reasons I started to use `stacktrace` library injecting the source files to my project. And it turned out that the result depends on how to build the project.  
```  
/project  
---/stacktrace  
---   backtrace.cpp  
---   from_exception.cpp  
---   CMakeLists.txt which add_library(stacktrace  backtrace.cpp from_exception.cpp)  
  test.cpp  
  CMakeLists.txt  
```  
And I build two versions of executable:  
1. add_executable(ver1 test.cpp stacktrace/backtrace.cpp stacktrace/from_exception.cpp)  
2. add_executable(ver2 test.cpp) + target_link_libraries(ver2 stacktrace)  
  
The ver1 shows stack as   
```  
 0# foo() in /opt/test  
 1# main in /opt/test  
 2# 0x00007FE22EAA8D90 in /lib/x86_64-linux-gnu/libc.so.6  
 3# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 4# _start in /opt/test  
```  
The ver2 show stack as   
```  
 0# 0x000055FEA0E2D528 in /opt/test  
 1# 0x000055FEA0E2CA13 in /opt/test  
 2# 0x00007F11801E7D90 in /lib/x86_64-linux-gnu/libc.so.6  
 3# __libc_start_main in /lib/x86_64-linux-gnu/libc.so.6  
 4# 0x000055FEA0E2C905 in /opt/test  
```  
Can you help me to understand what's going on?  
  
Code for reproduction is [here](https://github.com/subbota-a/stacktrace)  
I use clang-17 and libc++

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 07:46:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/174#issuecomment-2345509646  

What are the build flags?  
  
What version of stacktrace library are you using in 2. and with which implementation do you link (backtrace, noop, add2line...?)

---

## Comment 2

> Username: subbota-a  
> Created at: 2024-09-12 07:56:22 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/174#issuecomment-2345528874  

Everyone involved in the issue died a long time ago, so I'm closing the question.
