# #104 Detect compatible types via overload resolution [Open]

> Username: eido79  
> Created at: 2022-11-11 20:39:58 UTC  
> Updated at: 2025-09-07 15:41:48 UTC  
> Url: https://github.com/boostorg/variant/pull/104  

Use an overload set to detect if a type can be used in a converting constructor or assignment, in a similar way as std::variant.  
  
Fix the behavior of is_constructible, is_assignable and is_convertible traits with boost variant reported in #102.  
Also fix the "regression" mentioned in #100.

---
