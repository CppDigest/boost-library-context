# #32 Fix order of destruction of state-saver. [Closed]

> Username: jzmaddock  
> Created at: 2015-10-22 08:29:17 UTC  
> Updated at: 2017-05-23 22:43:28 UTC  
> Closed at: 2017-05-23 22:43:28 UTC  
> Url: https://github.com/boostorg/serialization/pull/32  

Without this patch member codecvt_null_facet is destroyed before the locale is restored to the stream buffer resulting in a program crash.

---
