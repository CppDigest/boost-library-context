# #388 Split out dynamic-only tests [Merged]

> Username: ecatmur  
> Created at: 2023-06-29 13:29:10 UTC  
> Updated at: 2024-04-24 14:08:42 UTC  
> Merged at: 2024-04-24 14:08:42 UTC  
> Closed at: 2024-04-24 14:08:42 UTC  
> Url: https://github.com/boostorg/histogram/pull/388  

Compiling tests under Visual Studio 2019 14.29.30133 (19.29.30145.0) fails with a not very helpful error:  
```  
libs\histogram\test\algorithm_project_test.cpp(186,1): fatal error C1903: unable to recover from previous error(s); stopping compilation  
```  
This can be fixed by moving the dynamic-only tests into a separate function, so that they are only compiled and run once.

---

## Comment 1

> Username: HDembinski  
> Created_at: 2024-04-24 13:58:31 UTC  
> Url: https://github.com/boostorg/histogram/pull/388#issuecomment-2075019185  

@ecatmur Thank you for this nice patch and sorry for the long delay in responding. I added a comment to the code why this was changed. How did you find out this fix?

---
