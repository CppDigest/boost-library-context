# #36 - sprintf deprecated [Closed]

> Username: sdarwin  
> Created at: 2025-08-21 14:50:44 UTC  
> Updated at: 2025-08-22 16:30:23 UTC  
> Closed at: 2025-08-21 21:45:19 UTC  
> Url: https://github.com/boostorg/integer/issues/36  

"Use snprintf if available as some compilers (clang 14.0) issue deprecation warnings for sprintf"  
  
  
```  
libs/integer/test/common_factor_test.cpp:    sprintf(buf,"%I64d", i);  
libs/integer/test/common_factor_test.cpp:    sprintf(buf,"%I64u", i);  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2025-08-21 21:41:49 UTC  
> Url: https://github.com/boostorg/integer/issues/36#issuecomment-3212163618  

That code is for MSVC 6.0 only. How did recent clang see that?

---

## Comment 2

> Username: sdarwin  
> Created at: 2025-08-22 16:30:23 UTC  
> Url: https://github.com/boostorg/integer/issues/36#issuecomment-3214953296  

> How did recent clang see that?  
  
It didn't.    
Another boost library hit the `sprintf` issue.      
Then I searched the whole superproject for `sprintf`.  
For a few of them, I didn't post an issue. Within `/tests/zlib/` or something.   Not sure if those must be fixed.
