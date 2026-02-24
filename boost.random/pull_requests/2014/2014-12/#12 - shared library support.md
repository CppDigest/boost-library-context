# #12 shared library support [Closed]

> Username: arvidn  
> Created at: 2014-12-01 21:35:27 UTC  
> Updated at: 2015-04-01 19:40:04 UTC  
> Closed at: 2015-04-01 19:40:04 UTC  
> Url: https://github.com/boostorg/random/pull/12  

Currently the compiled symbols in boost.random are only exported on msvc. If boost.random is built on gcc or clang with -fvisibility=hidden (or any other compiler that may not use __declspec(dllexport) for exporting funtions), the functions are not exported and the resulting library cannot be used.  
  
This patch simplifies the boost.random code by leveraging boost.config for determining how to export and import symbols, depending on compiler.

---
