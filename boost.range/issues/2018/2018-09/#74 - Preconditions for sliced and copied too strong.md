# #74 - Preconditions for sliced and copied too strong [Open]

> Username: tobias-loew  
> Created at: 2018-09-13 06:04:37 UTC  
> Updated at: 2018-09-13 06:05:04 UTC  
> Url: https://github.com/boostorg/range/issues/74  

Hi,  
  
in the documentation the preconditions of sliced and copied should state:  
  
```  
0 <= n && n <= m && m <= distance(rng)  
                       ^  
```  
  
currently it says  
```  
0 <= n && n <= m && m < distance(rng)  
```
