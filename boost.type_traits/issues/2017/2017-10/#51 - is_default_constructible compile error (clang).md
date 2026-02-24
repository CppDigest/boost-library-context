# #51 - is_default_constructible compile error (clang) [Closed]

> Username: vinniefalco  
> Created at: 2017-10-29 18:51:07 UTC  
> Updated at: 2018-02-01 18:18:35 UTC  
> Closed at: 2018-02-01 18:18:35 UTC  
> Url: https://github.com/boostorg/type_traits/issues/51  

`is_default_constructible` fails to compile on a pair that has one element which is not DefaultConstructible. This happens on clang, haven't seen it on gcc.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-02-01 18:18:34 UTC  
> Url: https://github.com/boostorg/type_traits/issues/51#issuecomment-362355213  

This should now be fixed in develop.
