# #106 - operator==( error_code, error_code ) is suboptimal [Open]

> Username: pdimov  
> Created at: 2023-02-28 17:05:58 UTC  
> Updated at: 2023-02-28 19:11:18 UTC  
> Url: https://github.com/boostorg/system/issues/106  

There's room for improvement both when the RHS is unknown (https://godbolt.org/z/sK3G6j3e6) and when it's known (https://godbolt.org/z/6M9qd4dqa). (GCC does best here; Clang is even worse.)  
  
Fixing this would probably require switching from `interop_category` to `unknown_category` (and retaining the original code value) as mentioned in #95. Also, the  
```  
        bool s1 = lhs.lc_flags_ == 1;  
        bool s2 = rhs.lc_flags_ == 1;  
```  
logic should probably be changed to an explicit `if` tree.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-02-28 19:11:18 UTC  
> Url: https://github.com/boostorg/system/issues/106#issuecomment-1448718755  

https://godbolt.org/z/1ob4zqvE7
