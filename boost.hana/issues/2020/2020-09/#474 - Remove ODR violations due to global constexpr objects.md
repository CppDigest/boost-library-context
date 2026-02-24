# #474 - Remove ODR violations due to global constexpr objects [Closed]

> Username: ldionne  
> Created at: 2020-09-03 17:00:19 UTC  
> Updated at: 2021-05-26 15:04:21 UTC  
> Closed at: 2021-05-26 15:04:21 UTC  
> Url: https://github.com/boostorg/hana/issues/474  

As reported in #76, we have benign ODR violations due to global `constexpr` function objects. The variable templates are OK because they have linkonce_odr semantics by default in the language, but not the non-templates. We can use C++17 `inline` variables to solve that.

---

## Comment 1

> Username: ldionne  
> Created at: 2020-09-03 17:01:32 UTC  
> Url: https://github.com/boostorg/hana/issues/474#issuecomment-686626595  

I found candidates to be made `inline` variables with:  
  
```  
grep -r include -e 'constexpr' | grep '{}' | grep -v 'INLINE_VARIABLE'  
```
