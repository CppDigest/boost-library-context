# #559 - negative NaN [Open]

> Username: gpeterhoff  
> Created at: 2023-06-03 19:21:19 UTC  
> Updated at: 2023-08-21 11:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/559  

In several cases it does not work:  
- unary operator-(nan)  
- operator<<(neagative nan)  
See https://godbolt.org/z/hh8EsarKd   
At godbolt there seems to be a problem with mpfr right now, so I had to comment out this case.  
  
thx  
Gero

---

## Comment 1

> Username: ckormanyos  
> Created at: 2023-08-21 11:18:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/559#issuecomment-1686138103  

> unary operator-(nan)  
  
Sorry I missed this issue at first Gero (@gpeterhoff).  
  
Are you saying that the handling of the sign bit for NaNs is the first point of the issue?
