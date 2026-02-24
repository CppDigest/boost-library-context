# #113 - Does not show line number when link with boost_stacktrace_addr2line. [Closed]

> Username: Kidsunbo  
> Created at: 2021-09-02 05:18:09 UTC  
> Updated at: 2024-09-12 08:21:52 UTC  
> Closed at: 2024-09-12 08:21:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/113  

### Environment  
Package Name/Version: **boost/1.76.0**  
Operating System+version: **Linux Ubuntu 20.04.3 LTS** (in docker)  
Compiler+version: **GCC 9**  
Docker image: **ubuntu/latest**  
  
### Description  
I find it does not show the line number if I link with `boost_stacktrace_addr2line`, but if I link with `boost_stacktrace_backtrace`, the line number shows up. The stacktrace is always shown but the line number is only shown if I link with `boost_stacktrace_backtrace` rather than `boost_stacktrace_addr2line` which is different with what the document says.  
  
The other linked libraries are always the same which are `backtrace` and `dl`, and the `addr2line` is at the path `/usr/bin/addr2line`.   
  
I have tried to install boost with `conan` and `apt` respectively, and the result is the same.   
Did I misunderstand the document? And is it expected?

---

## Comment 1

> Username: apolukhin  
> Created at: 2024-09-12 08:21:52 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/113#issuecomment-2345582636  

Was probably fixed in https://github.com/boostorg/stacktrace/commit/fbcd543b5180b8ee307fef6fbfc34aa372d330cf
