# #623 - Jammyfile should set warnings [Closed]

> Username: vinniefalco  
> Created at: 2022-11-08 01:56:00 UTC  
> Updated at: 2022-11-09 23:08:40 UTC  
> Closed at: 2022-11-09 23:08:40 UTC  
> Url: https://github.com/boostorg/url/issues/623  

Our Jamfile sets no warnings, so we get /W1 by default (msvc). We should crank this up. And by we I mean Alan.

---

## Comment 1

> Username: alandefreitas  
> Created at: 2022-11-08 02:00:48 UTC  
> Url: https://github.com/boostorg/url/issues/623#issuecomment-1306507624  

We don't set any `<warnings>` in our Jamfiles, so the default under MSVC is `/W1`. Not going to get much that way.  
  
In `test/Jamfile`, add  
  
```  
  default-build  
    <warnings>extra  
```  
  
and maybe `<warnings-as-errors>` on according to taste:  
  
https://github.com/boostorg/variant2/blob/4d1fd43d72b6738c24e2d5f9e7438839c4bfd134/test/Jamfile#L12-L24
