# #160 Avoiding -ldl linking when using -DBOOST_STACKTRACE_USE_ADDR2LINE [Closed]

> Username: sanketRmeshram  
> Created at: 2024-03-16 14:55:22 UTC  
> Updated at: 2024-07-07 15:31:08 UTC  
> Closed at: 2024-07-07 15:31:08 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/160  

While using `addr2line` binary to get the symbolic function information from address. We Fork and execute command in the child process. But currently, it requires unnecessarily linking Dynamic Loader (-ldl).  
  
Changed the code to avoid that linking.

---
