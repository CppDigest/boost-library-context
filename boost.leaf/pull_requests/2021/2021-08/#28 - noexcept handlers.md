# #28 noexcept handlers [Closed]

> Username: ProfDiesel  
> Created at: 2021-08-05 20:49:37 UTC  
> Updated at: 2021-11-05 16:04:37 UTC  
> Closed at: 2021-08-24 17:59:03 UTC  
> Url: https://github.com/boostorg/leaf/pull/28  

Support ``noexcept`` handlers

---

## Comment 1

> Username: zajo  
> Created_at: 2021-08-06 01:20:54 UTC  
> Updated_at: 2021-08-06 01:21:25 UTC  
> Url: https://github.com/boostorg/leaf/pull/28#issuecomment-893932719  

Thanks. Probably not a bad idea but tests are failing, it needs further ifdefing, probably should only be enabled on C++17 and newer.  
  
(Note that e.g. `try_handle_all`/`try_handle_some` are `noexcept` only under `BOOST_LEAF_NO_EXCEPTIONS` (`-fno-exceptions`).

---
