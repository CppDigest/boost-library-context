# #12 Drop using directive, and fully qualify std names. [Merged]

> Username: geoffromer  
> Created at: 2015-11-30 20:34:17 UTC  
> Updated at: 2015-11-30 21:41:36 UTC  
> Merged at: 2015-11-30 21:41:36 UTC  
> Closed at: 2015-11-30 21:41:36 UTC  
> Url: https://github.com/boostorg/rational/pull/12  

Even with `using namespace std`, it's not safe to use names from std  
without qualification in a header file, because the names may conflict  
with names in the global namespace, leading to ambiguities.

---

## Comment 1

> Username: mclow  
> Created_at: 2015-11-30 20:57:32 UTC  
> Url: https://github.com/boostorg/rational/pull/12#issuecomment-160759501  

This LGTM.

---
