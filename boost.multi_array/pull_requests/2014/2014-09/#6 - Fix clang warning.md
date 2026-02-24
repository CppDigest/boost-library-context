# #6 Fix clang warning [Merged]

> Username: MarcelRaad  
> Created at: 2014-09-26 08:54:36 UTC  
> Updated at: 2015-11-01 09:02:41 UTC  
> Merged at: 2014-10-05 16:10:35 UTC  
> Closed at: 2014-10-05 16:10:35 UTC  
> Url: https://github.com/boostorg/multi_array/pull/6  

clang complains about the friend declaration with a warning:  
  
warning : unqualified friend declaration referring to type outside of the nearest enclosing namespace is a Microsoft extension; add a nested name specifier [-Wmicrosoft]  
    friend class iterator_core_access;  
                       ^  
                       ::boost::  
  
Explicitly qualifying the class name with its namespace fixes the warning.

---

## Comment 1

> Username: rxg  
> Created_at: 2014-10-05 16:11:13 UTC  
> Url: https://github.com/boostorg/multi_array/pull/6#issuecomment-57942462  

Thanks!

---

## Comment 2

> Username: rxg  
> Created_at: 2015-11-01 09:02:41 UTC  
> Url: https://github.com/boostorg/multi_array/pull/6#issuecomment-152808143  

I've done this by hand.  Sorry for the delay, I was trying to find a way to commit this through github and finally gave up.

---
