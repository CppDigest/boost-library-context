# #758 - Remove json::generic_category [Closed]

> Username: grisumbras  
> Created at: 2022-09-26 10:54:59 UTC  
> Updated at: 2022-10-19 20:10:15 UTC  
> Closed at: 2022-10-19 20:10:15 UTC  
> Url: https://github.com/boostorg/json/issues/758  

Its reason for existence seems to have been standalone mode, which is no more.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-09-26 14:47:41 UTC  
> Url: https://github.com/boostorg/json/issues/758#issuecomment-1258154134  

If you remove it don't forget to remove it from the index.  
  
But I'm not 100% certain it should be removed. In fact you might want to make it work like Boost.URL, where all the aliases are in a nested namespace called `error_types`. This way the user can import the symbols into their own namespace and use them without qualification. If you take away `generic_category` then switching to Boost.JSON's aliases becomes more difficult if the user already has code that uses the generic category.  
  
See: https://github.com/boostorg/url/blob/81cb06ec5e616d609ac01ef1b6a5d67eaa8448e2/include/boost/url/error_types.hpp#L22

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-10-19 14:32:08 UTC  
> Url: https://github.com/boostorg/json/issues/758#issuecomment-1284114532  

I don't believe users need to do that, though. And we now have a special function to create `result` that stores an `error_code` with `generic_category` (Peter asked for it).
