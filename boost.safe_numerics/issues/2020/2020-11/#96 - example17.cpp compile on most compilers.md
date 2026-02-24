# #96 - example17.cpp compile on most compilers [Closed]

> Username: GregKon  
> Created at: 2020-11-29 20:25:15 UTC  
> Updated at: 2020-12-16 18:04:28 UTC  
> Closed at: 2020-12-16 18:04:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/96  

Hi  
  
example 17 does compile on most compilers where sizeof(int) == sizeof(long), e.g.: gcc 9.2 32bits, msvc in 32 bit mode and most of cross compilers for embedded (where 64bits arch is not common).  
  
The simplest fix will be use "long long".  
```  
using safe_t = safe<long long, native, loose_trap_policy>;  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2020-12-16 18:04:27 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/96#issuecomment-746743406  

I did find that the example was never used in the documentation so I could have just eliminated it.  But I still think it's interesting.  Perhaps it should be migrated to tests or perhaps this is an indicator that documentation needs some more information.  In any case I alter the example so that it illustrates what it's supposed to.
