# #76 Null from override call [Closed]

> Username: mathstuf  
> Created at: 2016-07-15 16:38:55 UTC  
> Updated at: 2016-07-22 14:40:11 UTC  
> Closed at: 2016-07-22 14:40:11 UTC  
> Url: https://github.com/boostorg/python/pull/76  

---  
  
Ok, so here's a test, but it doesn't actually test the code I changed. The problem occurs when the `PyFooOverride` returns `NULL`. I can't seem to get it to actually do so (I think there's too much crap in between which does other things). The original problem was, I think, something gone terribly wrong inside the Python interpreter, but the code path _is_ possible (never would have found this if not).

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2016-07-15 16:46:12 UTC  
> Url: https://github.com/boostorg/python/pull/76#issuecomment-233004627  

Thanks. However, we do need a test case (which fails without and passes with you patch), as I have no other way to check that this is addressing a real problem, on all tested platforms.

---

## Comment 2

> Username: mathstuf  
> Created_at: 2016-07-22 14:40:11 UTC  
> Url: https://github.com/boostorg/python/pull/76#issuecomment-234562027  

I can't reproduce the problem anymore; I guess it has been fixed in a different way since then? Closing.

---
