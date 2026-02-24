# #496 - BOOST_SYMBOL_VISIBLE? [Closed]

> Username: vinniefalco  
> Created at: 2021-02-02 17:18:46 UTC  
> Updated at: 2021-06-23 19:50:27 UTC  
> Closed at: 2021-06-23 19:50:27 UTC  
> Url: https://github.com/boostorg/json/issues/496  

The boost developer's mailing list thread with subject "[boost] Wrong class export/visibility setting for shared libs in many Boost libs" might be actionable and should be investigated.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-02-02 17:20:14 UTC  
> Url: https://github.com/boostorg/json/issues/496#issuecomment-771810362  

See: https://github.com/boostorg/json/blob/f0ea69fefda1c8b02075814e32d86a74e216cc59/include/boost/json/detail/shared_resource.hpp#L25

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-06-23 13:47:35 UTC  
> Url: https://github.com/boostorg/json/issues/496#issuecomment-866851160  

This comment describes what uses are affected:  https://github.com/boostorg/config/issues/362#issuecomment-771855999  
I've looked through the project and I don't think we have any of those.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-23 18:39:34 UTC  
> Url: https://github.com/boostorg/json/issues/496#issuecomment-867071700  

we can close it I guess?
