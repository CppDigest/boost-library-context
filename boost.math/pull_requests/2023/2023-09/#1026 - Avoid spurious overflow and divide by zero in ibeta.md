# #1026 Avoid spurious overflow and divide by zero in ibeta. [Merged]

> Username: jzmaddock  
> Created at: 2023-09-01 14:49:17 UTC  
> Updated at: 2023-09-02 14:57:04 UTC  
> Merged at: 2023-09-02 08:14:54 UTC  
> Closed at: 2023-09-02 08:14:54 UTC  
> Url: https://github.com/boostorg/math/pull/1026  

Add tests extracted from SciPy bug report.  
Fixes https://github.com/boostorg/math/issues/1006.   
See also https://github.com/boostorg/math/pull/1009.

---

## Comment 1

> Username: mdhaber  
> Created_at: 2023-09-01 18:54:42 UTC  
> Updated_at: 2023-09-01 18:58:35 UTC  
> Url: https://github.com/boostorg/math/pull/1026#issuecomment-1703195035  

Thanks @jzmaddock! This seems to help on my M1 mac.   
```python3  
import scipy.stats  
scipy.stats.beta(a=1e-323, b=5).ppf(.2)  
```  
Before this patch, I get an error; after this patch, I get `1.8189894035458565e-12`. I'm not sure if the relative accuracy is great, since taking the `cdf` at that argument returns `1.0` instead of `0.2`, but I'm guessing it will fix the crash I experience on other machines.

---
