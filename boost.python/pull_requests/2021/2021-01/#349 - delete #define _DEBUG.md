# #349 delete #define _DEBUG [Open]

> Username: shelltdf  
> Created at: 2021-01-26 14:00:02 UTC  
> Updated at: 2022-11-13 12:23:04 UTC  
> Url: https://github.com/boostorg/python/pull/349  

this is bug

---

## Comment 1

> Username: Osyotr  
> Created_at: 2022-11-13 12:23:04 UTC  
> Url: https://github.com/boostorg/python/pull/349#issuecomment-1312718075  

This is not a bug. `_DEBUG` was temporarily undefined here. https://github.com/boostorg/python/blob/a6dbc37254689edd9efbd8ca4e4771baaf801447/include/boost/python/detail/wrap_python.hpp#L45

---
