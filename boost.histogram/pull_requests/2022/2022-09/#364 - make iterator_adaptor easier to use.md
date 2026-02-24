# #364 make iterator_adaptor easier to use [Merged]

> Username: HDembinski  
> Created at: 2022-09-17 11:23:49 UTC  
> Updated at: 2022-09-17 16:35:34 UTC  
> Merged at: 2022-09-17 16:35:33 UTC  
> Closed at: 2022-09-17 16:35:33 UTC  
> Url: https://github.com/boostorg/histogram/pull/364  

This makes it simpler to implement a skipping iterator with detail::iterator_adaptor, that has a stride larger than one but otherwise is just an ordinary pointer.

---
