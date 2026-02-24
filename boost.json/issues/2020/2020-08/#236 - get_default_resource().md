# #236 - get_default_resource() [Closed]

> Username: vinniefalco  
> Created at: 2020-08-30 23:46:39 UTC  
> Updated at: 2020-09-01 14:55:23 UTC  
> Closed at: 2020-09-01 14:55:22 UTC  
> Url: https://github.com/boostorg/json/issues/236  

Should we add this  
```  
inline  
storage_ptr  
get_default_resource() noexcept  
{  
  return storage_ptr();  
}  
```  
  
It would make call sites more clear and it would have a synergy with pmr.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-09-01 14:55:22 UTC  
> Url: https://github.com/boostorg/json/issues/236#issuecomment-684915138  

@pdimov says no
