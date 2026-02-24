# #249 clear python exception after expected attribute lookup failure [Merged]

> Username: orivej  
> Created at: 2019-02-13 18:33:47 UTC  
> Updated at: 2019-02-14 04:38:04 UTC  
> Merged at: 2019-02-14 04:38:03 UTC  
> Closed at: 2019-02-14 04:38:04 UTC  
> Url: https://github.com/boostorg/python/pull/249  

Python 3.7.0 asserts that attribute lookup functions are called without outstanding exceptions:  
https://github.com/python/cpython/blob/v3.7.0a2/Objects/typeobject.c#L3037  
Motivation: https://bugs.python.org/issue34068#msg321262  
Therefore the error set by the first PyObject_GetItem should be cleared before calling PyObject_GetAttrString.

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-02-13 18:39:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/python/pull/249#pullrequestreview-203379740  

📁 src/object/function.cpp

```diff
 488 |             new_func->m_name = name;
 489 | 
 490 |+         assert(!PyErr_Occurred());
```

> Username: stefanseefeld  
> Created_at: 2019-02-13 18:39:32 UTC  
> Url: https://github.com/boostorg/python/pull/249#discussion_r256532762  

I don't quite understand: this code here is conditional on `existing` being non-null. So the only way to be here is with the previous `PyObject_GetItem()` call not having failed. So why the `PyErr_Clear()` call between that and here ?

> Username: orivej  
> Created_at: 2019-02-13 19:35:10 UTC  
> Url: https://github.com/boostorg/python/pull/249#discussion_r256555293  

Why is it conditional on `existing`? It is outside `if (existing)`.

> Username: stefanseefeld  
> Created_at: 2019-02-13 19:37:33 UTC  
> Url: https://github.com/boostorg/python/pull/249#discussion_r256556260  

oups, sorry, I misread (the diff view collapsed a few important lines here :-) ). OK, makes sense, then.


---
