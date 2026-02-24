# #1 Fix for Python 3 versus Python 2 where the PyVarObject is different. [Merged]

> Username: eldiener  
> Created at: 2018-10-18 18:43:27 UTC  
> Updated at: 2018-10-25 15:30:49 UTC  
> Merged at: 2018-10-25 15:30:49 UTC  
> Closed at: 2018-10-25 15:30:49 UTC  
> Url: https://github.com/boostorg/parameter_python/pull/1  



---

## Review 1 [Commented]

> Username: pdimov  
> Created_at: 2018-10-18 19:10:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/parameter_python/pull/1#pullrequestreview-166251943  

📁 include/boost/parameter/python.hpp

```diff
  65 |       };
  66 |+       
  67 |+ #if PY_MAJOR_VERSION <= 2
```

> Username: pdimov  
> Created_at: 2018-10-18 19:10:50 UTC  
> Updated_at: 2018-10-19 20:00:06 UTC  
> Url: https://github.com/boostorg/parameter_python/pull/1#discussion_r226430782  

I think that the `Py_TYPE(o)` macro is supposed to encapsulate the differences between 2 and 3, so if you use it, there would be no need to check the version.


---
