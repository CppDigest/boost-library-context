# #279 Repository: subrule: Fixed partial ordering problem [Merged]

> Username: Kojoley  
> Created at: 2017-11-15 18:34:28 UTC  
> Updated at: 2017-11-16 10:29:41 UTC  
> Merged at: 2017-11-15 18:34:36 UTC  
> Closed at: 2017-11-15 18:34:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/279  

The problem is that `f(T&)` and `f(T&&)` overloads had the same priority  
http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_defects.html#1164  
  
Affected compilers are:  
 - MSVC 9/10/11  
 - GCC 4.7.2, 4.8.4 with `-std=c++1x`

---

## Review 1 [Commented]

> Username: vtnerd  
> Created_at: 2017-11-16 00:50:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/279#pullrequestreview-76980699  

📁 include/boost/spirit/repository/home/karma/nonterminal/subrule.hpp

```diff
 469 |-                 def_type(compile<spirit::karma::domain>(expr), sr.name_)));
 470 |-         }
 461 |+ #define SUBRULE_MODULUS_ASSIGN_OPERATOR(lhs_ref, rhs_ref)                     \
```

> Username: vtnerd  
> Created_at: 2017-11-16 00:50:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/279#discussion_r151295664  

Should this have a `BOOST_` prefix? It should be extremely rare to find this macro already declared elsewhere, but ...

> Username: Kojoley  
> Created_at: 2017-11-16 10:29:41 UTC  
> Url: https://github.com/boostorg/spirit/pull/279#discussion_r151374809  

It had `BOOST_SPIRIT_` but was shortened. Someone even could have and this one, so the actual solution is to add push/pop. I do not want to touch this more, because I have in mind an alternative fix and this overload madness should be removed.


---
