# #88 Do not include boost/noncopyable.hpp to avoid a dependency on Core [Merged]

> Username: pdimov  
> Created at: 2018-08-24 23:18:19 UTC  
> Updated at: 2019-11-02 11:38:50 UTC  
> Merged at: 2018-08-27 17:09:13 UTC  
> Closed at: 2018-08-27 17:09:13 UTC  
> Url: https://github.com/boostorg/type_traits/pull/88  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2018-08-25 07:42:49 UTC  
> Url: https://github.com/boostorg/type_traits/pull/88#issuecomment-415949854  

Will merge, but could you please add a comment explaining that base_token is duplicated from core?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-08-25 12:37:14 UTC  
> Url: https://github.com/boostorg/type_traits/pull/88#issuecomment-415966466  

Good idea. Done.

---

## Comment 3

> Username: glenfe  
> Created_at: 2018-08-25 14:18:33 UTC  
> Url: https://github.com/boostorg/type_traits/pull/88#issuecomment-415972381  

Looks good. I'll submit a PR to TypeTraits later today for `enable_if_` and `enable_if_t`, to sever that dependency on Core.

---
