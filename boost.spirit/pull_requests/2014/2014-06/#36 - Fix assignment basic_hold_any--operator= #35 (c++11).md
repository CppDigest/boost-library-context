# #36 Fix assignment basic_hold_any::operator= #35 (c++11) [Merged]

> Username: sehe  
> Created at: 2014-06-06 00:14:17 UTC  
> Updated at: 2014-06-12 13:17:55 UTC  
> Merged at: 2014-06-06 00:19:55 UTC  
> Closed at: 2014-06-06 00:19:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/36  

The template version would not be used because the compiler-generated  
one was a closer match. The implicit assignment, however, broke the  
ownership semantics.  
  
See also Pull Request #35 (wrong branch, not C++11 safe)

---
