# #49 - How to build boost_stacktrace_backtrace [Closed]

> Username: yuwenyong  
> Created at: 2018-04-14 06:21:26 UTC  
> Updated at: 2018-05-25 01:38:36 UTC  
> Closed at: 2018-05-25 01:38:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/49  

libbacktrace exists on my system.  
  
After I had built boost with default options, I found boost_stacktrace_backtrace was not built, but libboost_stacktrace_addr2line was generated.

---

## Comment 1

> Username: ArekPiekarz  
> Created at: 2018-04-14 15:46:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/49#issuecomment-381338280  

Have you seen this? https://www.boost.org/doc/libs/1_66_0/doc/html/stacktrace/configuration_and_build.html  
  
Also when posting an issue it's helpful to include steps to reproduce the problem and information about your environment.

---

## Comment 2

> Username: yuwenyong  
> Created at: 2018-04-14 16:05:10 UTC  
> Updated at: 2018-04-21 06:26:33 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/49#issuecomment-381339600  

yes,I have read this.   
I build boost on my macOS.I do as the following steps:  
    1 ./bootstrap.sh  
    2 ./b2  
    3 ./b2 install  
Later,I built boost on linux and the lib was successful generated.  
  
So my question is why libbacktrace can't be auto detected on macOS?

---

## Comment 3

> Username: apolukhin  
> Created at: 2018-05-24 17:46:30 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/49#issuecomment-391801558  

@yuwenyong please help me to diagnose the problem.  
Do the following steps from the boost root:  
* ./bootstrap.sh  
* cd libs/stacktrace/build  
* ../../../b2 -a libbacktrace  
  
The last command should produce some error message. Please paste it to this issue.

---

## Comment 4

> Username: yuwenyong  
> Created at: 2018-05-25 01:38:29 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/49#issuecomment-391915440  

@apolukhin  Sorry, forgot to close this issue. After doing some investigation, I find libbacktrace only supported by g++. I compile it using clang on macos, so it is not generated. However even without libbacktrace, stacktrace_basic can generate the call stack as pretty as stacktrace_backtrace.
