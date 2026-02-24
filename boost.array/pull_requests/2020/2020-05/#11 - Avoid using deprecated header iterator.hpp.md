# #11 Avoid using deprecated header iterator.hpp [Merged]

> Username: Lastique  
> Created at: 2020-05-10 16:40:05 UTC  
> Updated at: 2020-05-10 19:51:35 UTC  
> Merged at: 2020-05-10 19:40:35 UTC  
> Closed at: 2020-05-10 19:40:35 UTC  
> Url: https://github.com/boostorg/array/pull/11  

`<boost/detail/iterator.hpp>` is marked deprecated and is going to be removed in a future release. Replace it with `<iterator>` to avoid deprecation warnings.

---
