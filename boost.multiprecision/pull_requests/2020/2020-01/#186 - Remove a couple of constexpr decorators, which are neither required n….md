# #186 Remove a couple of constexpr decorators, which are neither required n… [Merged]

> Username: jzmaddock  
> Created at: 2020-01-24 18:52:42 UTC  
> Updated at: 2021-01-30 16:09:58 UTC  
> Merged at: 2020-01-26 09:15:32 UTC  
> Closed at: 2020-01-26 09:15:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/186  

…or permitted

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2020-01-25 11:41:57 UTC  
> Updated_at: 2020-01-25 11:42:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/186#issuecomment-578399316  

> Some checks were not successful  
  
I feel as though the one single job that did not pass Travis CI build was a bad mojo event.  
Line 211 of the failed build log says  
  
```  
GnuTLS recv error (-54): Error in the pull function.  
```  
  
Does it make sense or is it possible to restart the CI build?

---
