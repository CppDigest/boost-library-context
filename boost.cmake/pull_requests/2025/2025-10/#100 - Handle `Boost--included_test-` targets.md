# #100 Handle `Boost::included_test*` targets [Merged]

> Username: Flamefire  
> Created at: 2025-10-27 12:43:43 UTC  
> Updated at: 2025-10-28 07:37:14 UTC  
> Merged at: 2025-10-28 00:17:56 UTC  
> Closed at: 2025-10-28 00:17:56 UTC  
> Url: https://github.com/boostorg/cmake/pull/100  

Fixes #99  
  
Also makes the quoting of library names in messages consistent.  
  
Prior this was a bit confusing:  
  
```  
-- Adding Boost dependency utility  
```  
  
And inconsistent:  
```  
-- Adding Boost dependency function_types  
-- Enabling installation for 'function_types'  
```

---
