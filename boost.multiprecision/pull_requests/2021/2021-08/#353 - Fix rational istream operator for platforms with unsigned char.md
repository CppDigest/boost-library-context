# #353 Fix rational istream operator for platforms with unsigned char. [Merged]

> Username: awulkiew  
> Created at: 2021-08-04 15:35:53 UTC  
> Updated at: 2021-08-11 18:12:42 UTC  
> Merged at: 2021-08-11 18:12:42 UTC  
> Closed at: 2021-08-11 18:12:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/353  

```  
../boost/multiprecision/number.hpp:2293:16: warning: result of comparison of constant -1 with expression of type 'char' is always true [-Wtautological-constant-out-of-range-compare]  
../boost/multiprecision/number.hpp:2309:19: warning: result of comparison of constant -1 with expression of type 'char' is always true [-Wtautological-constant-out-of-range-compare]  
```  
If you think that this should be fixed in a different way feel free to close this PR.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-08-11 18:12:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/353#issuecomment-897042919  

Looks good to me, thanks for that!

---
