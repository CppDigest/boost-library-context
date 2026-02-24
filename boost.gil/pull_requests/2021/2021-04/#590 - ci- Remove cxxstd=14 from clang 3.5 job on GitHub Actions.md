# #590 ci: Remove cxxstd=14 from clang 3.5 job on GitHub Actions [Merged]

> Username: mloskot  
> Created at: 2021-04-02 16:45:47 UTC  
> Updated at: 2021-04-02 18:35:24 UTC  
> Merged at: 2021-04-02 18:35:18 UTC  
> Closed at: 2021-04-02 18:35:18 UTC  
> Url: https://github.com/boostorg/gil/pull/590  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Despite https://clang.llvm.org/cxx_status.html saying:  
  
>  Clang 3.4 and later implement all of the ISO C++ 2014 standard.  
  
The clang 3.5 suffers from the bug in C++14 mode causing failure of  
Boost.Filesystem build with:  
  
```  
error: debug information for auto is not yet supported  
error: debug information for auto is not yet supported  
```  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Attempt to fix #562 (comment) (/cc @meshtag)  
- https://github.com/boostorg/gil/pull/589  
- https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=800483  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
