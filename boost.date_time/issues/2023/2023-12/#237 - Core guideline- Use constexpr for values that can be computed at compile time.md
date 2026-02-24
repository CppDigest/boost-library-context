# #237 - Core guideline: Use constexpr for values that can be computed at compile time [Open]

> Username: trueqbit  
> Created at: 2023-12-10 20:35:53 UTC  
> Updated at: 2023-12-10 20:35:53 UTC  
> Url: https://github.com/boostorg/date_time/issues/237  

Commit 4e1b7cde45ed introduced `constexpr` functions.  
  
A code analysis with Visual C++ 2022 and the Microsoft Recommended Native Recommended Rules spits out the warning "con.5" from the category ["Constants and immutability"](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Rconst-constexpr) in `str_from_delimited_time_duration()` and `counted_time_rep<>::frac_sec_per_day()`:  
  
```  
warning C26498: The function 'function' is constexpr, mark variable 'variable' constexpr if compile-time evaluation is desired (con.5).  
```  
  
Correcting this warning has no effect on the `constexpr`essivity, but it would be nice if these warnings disappeared.
