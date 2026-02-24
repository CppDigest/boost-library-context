# #10 Merge port to TypeIndex and fix for placement new warnings to master [Closed]

> Username: Lastique  
> Created at: 2016-07-16 19:53:56 UTC  
> Updated at: 2016-07-16 20:41:33 UTC  
> Closed at: 2016-07-16 20:41:33 UTC  
> Url: https://github.com/boostorg/function/pull/10  

The PR contains porting to Boost.TypeIndex for RTTI-related operations and also a fix for gcc 6 warnings about insufficient space for placement new. The tests have cycled, the warnings are cleared.

---

## Comment 1

> Username: Belcourt  
> Created_at: 2016-07-16 20:35:23 UTC  
> Url: https://github.com/boostorg/function/pull/10#issuecomment-233149783  

Cherry-picked these commits to master, I think you can close this PR.  
  
commit bf91c9bb3c4f26e53a675c49a764902ba9f1ab52  
Author: Andrey Semashev andrey.semashev@gmail.com  
Date:   Sat Apr 2 17:31:22 2016 +0300  
  
```  
Changed implementation to avoid calculating the size of the raw data buffer manually. Trim trailing spaces.  
```  
  
commit 6e98e46e7be28d1db01112655e6f65d8ffdc6686  
Author: Andrey Semashev andrey.semashev@gmail.com  
Date:   Sat Apr 2 14:59:57 2016 +0300  
  
```  
Fix gcc 6 warnings about invoking placement new on a buffer of insufficient size.  
```  
  
commit 6a24b1e59c8f9826ecee48aab2e6641892b76504  
Author: Antony Polukhin antoshkka@gmail.com  
Date:   Sat Apr 25 17:45:13 2015 +0300  
  
```  
Use Boost.TypeIndex to work with type_info to avoid bunch of workarounds and non-optimal operators. Added RTTI-off tests  
```

---

## Comment 2

> Username: Lastique  
> Created_at: 2016-07-16 20:41:33 UTC  
> Url: https://github.com/boostorg/function/pull/10#issuecomment-233150014  

Thanks.

---
