# #207 Remove dependency on Boost.StaticAssert [Merged]

> Username: mloskot  
> Created at: 2019-01-10 22:49:58 UTC  
> Updated at: 2019-01-11 09:33:14 UTC  
> Merged at: 2019-01-11 09:33:10 UTC  
> Closed at: 2019-01-11 09:33:10 UTC  
> Url: https://github.com/boostorg/gil/pull/207  

Replaced `BOOST_STATIC_ASSERT` with C++11 binary `static_assert(expr, msg)`, with empty message.  
  
In future, this should make it possible to automatically refactor into C++17 unary `static_assert(expr)`  
using `clang-tidy` and its `modernize-unary-static-assert` check.  
  
### References  
  
Closes #106  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---
