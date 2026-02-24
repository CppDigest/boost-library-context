# #123 - string_view::hash_value not noexcept [Closed]

> Username: vinniefalco  
> Created at: 2022-08-30 17:33:41 UTC  
> Updated at: 2022-09-18 10:03:07 UTC  
> Closed at: 2022-09-18 10:03:07 UTC  
> Url: https://github.com/boostorg/core/issues/123  

But it could be

---

## Comment 1

> Username: pdimov  
> Created at: 2022-08-30 21:54:00 UTC  
> Url: https://github.com/boostorg/core/issues/123#issuecomment-1232208278  

It's implemented in terms of `boost::hash_range`, and that's not `noexcept` because it can call other `hash<>` instantiations which aren't necessarily `noexcept`.
