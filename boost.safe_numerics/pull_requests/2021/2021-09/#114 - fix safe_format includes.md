# #114 fix safe_format includes [Merged]

> Username: hgkjshegfskef  
> Created at: 2021-09-09 17:03:13 UTC  
> Updated at: 2022-06-07 01:23:00 UTC  
> Merged at: 2022-06-07 01:22:59 UTC  
> Closed at: 2022-06-07 01:22:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/pull/114  

std::numeric_limits requires `<limits>`  
  
Without the include safe_format.hpp will not compile, as can be seen here: https://godbolt.org/z/5K73PGd7h

---
