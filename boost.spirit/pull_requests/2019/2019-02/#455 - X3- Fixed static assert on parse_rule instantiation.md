# #455 X3: Fixed static assert on parse_rule instantiation [Merged]

> Username: Kojoley  
> Created at: 2019-02-07 18:23:18 UTC  
> Updated at: 2019-02-12 15:31:45 UTC  
> Merged at: 2019-02-08 11:51:36 UTC  
> Closed at: 2019-02-08 11:51:36 UTC  
> Url: https://github.com/boostorg/spirit/pull/455  

During overload resolution disallowed signature can be instantiated leading to firing the static assertation and overload resolution failure. I could just remove it, but do not want to leave unbounded by value overloads; replacing to not deducing Attribute with specifying a default parameter will not work because we want to allow `BOOST_SPIRIT_DECLARE` without prior `BOOST_SPIRIT_DEFINE` - this requires having default parameters in both declaration and definition and the standard seems to forbid it (GCC - fine, MSVC - warning, Clang - error).  
  
Reverts #437, fixes #453.

---
