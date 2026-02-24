# #230 Faster linearize [Merged]

> Username: HDembinski  
> Created at: 2019-10-03 21:00:14 UTC  
> Updated at: 2019-10-09 22:12:21 UTC  
> Merged at: 2019-10-06 21:03:46 UTC  
> Closed at: 2019-10-06 21:03:46 UTC  
> Url: https://github.com/boostorg/histogram/pull/230  



---

## Comment 1

> Username: HDembinski  
> Created_at: 2019-10-04 13:39:30 UTC  
> Url: https://github.com/boostorg/histogram/pull/230#issuecomment-538400934  

Summary so far:  
* introduce offset for faster linearization of non-growing axes  
* added traits::is_inclusive and constexpr bool inclusive() methods for builtin axes  
* bug fix of fill method when weight array is used with non-inclusive axis  
* bug fix of fill method when growing axes are used  
* bug fix of axis::options::test(...)  
* coverage tested with gcc-8, updated CONTRIBUTING.md with coverage info  
  
Todo:  
* document inclusive() method in axis concept

---
