# #24 Various g++/clang config_test fixes [Merged]

> Username: pdimov  
> Created at: 2014-06-08 22:03:36 UTC  
> Updated at: 2014-06-10 18:24:34 UTC  
> Merged at: 2014-06-10 18:17:19 UTC  
> Closed at: 2014-06-10 18:17:19 UTC  
> Url: https://github.com/boostorg/config/pull/24  

This updates the test files to not use `throw/try/catch` when exceptions are off, to not use `typeid` when RTTI is off, and to not cast a lambda to `void` as this triggers a g++ bug.

---
