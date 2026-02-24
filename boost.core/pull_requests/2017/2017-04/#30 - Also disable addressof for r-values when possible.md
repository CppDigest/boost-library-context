# #30 Also disable addressof for r-values when possible [Merged]

> Username: glenfe  
> Created at: 2017-04-23 06:20:31 UTC  
> Updated at: 2017-04-23 13:16:12 UTC  
> Merged at: 2017-04-23 13:16:12 UTC  
> Closed at: 2017-04-23 13:16:12 UTC  
> Url: https://github.com/boostorg/core/pull/30  

Now in C++17: `template<class T> const T* addressof(const T&&) = delete;`

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-04-23 10:28:54 UTC  
> Updated_at: 2017-04-23 10:32:29 UTC  
> Url: https://github.com/boostorg/core/pull/30#issuecomment-296433652  

Doesn't this disable `addressof` for all non-const types? Shouldn't there be `disable_if_c< is_reference< T >::value >::type`?

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-04-23 11:34:55 UTC  
> Url: https://github.com/boostorg/core/pull/30#issuecomment-296437064  

`T` can never be a reference here.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-04-23 11:48:03 UTC  
> Url: https://github.com/boostorg/core/pull/30#issuecomment-296437730  

Ah, I forgot there is already an overload for lvalue reference. Sorry for the noise.

---
