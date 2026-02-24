# #117 Use constexpr when compiling with clang [Merged]

> Username: CaseyCarter  
> Created at: 2018-03-07 01:42:56 UTC  
> Updated at: 2018-03-09 19:07:39 UTC  
> Merged at: 2018-03-09 19:07:39 UTC  
> Closed at: 2018-03-09 19:07:39 UTC  
> Url: https://github.com/boostorg/math/pull/117  

Clang compilers do not define `__clang`, they define `__clang__`. (https://godbolt.org/g/R7gX4h)

---

## Comment 1

> Username: CaseyCarter  
> Created_at: 2018-03-08 04:38:59 UTC  
> Url: https://github.com/boostorg/math/pull/117#issuecomment-371376566  

Travis failure is unrelated to the change; it's a timeout.

---
