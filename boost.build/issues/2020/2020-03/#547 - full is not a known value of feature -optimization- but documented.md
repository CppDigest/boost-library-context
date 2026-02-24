# #547 - full is not a known value of feature <optimization> but documented [Open]

> Username: mloskot  
> Created at: 2020-03-10 23:09:34 UTC  
> Updated at: 2021-05-29 18:22:26 UTC  
> Url: https://github.com/boostorg/build/issues/547  

The current, master and develop, docs say  
  
```  
b2 app optimization=full define=USE_ASM  
  
The <optimization> feature is propagated, so both app and foo will be compiled with full optimization.   
```  
  
while the reference says:  
  
```  
optimization  
  
    Allowed values: off, speed, space.  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:56 UTC  
> Url: https://github.com/boostorg/build/issues/547#issuecomment-850877291  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
