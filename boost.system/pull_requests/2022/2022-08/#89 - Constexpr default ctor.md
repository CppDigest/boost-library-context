# #89 Constexpr default ctor [Merged]

> Username: vinniefalco  
> Created at: 2022-08-21 01:12:45 UTC  
> Updated at: 2022-08-21 22:00:51 UTC  
> Merged at: 2022-08-21 21:51:00 UTC  
> Closed at: 2022-08-21 21:51:00 UTC  
> Url: https://github.com/boostorg/system/pull/89  

This makes `error_code` default constructor `constexpr` in C++11 instead of just C++14

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2022-08-21 05:55:33 UTC  
> Url: https://github.com/boostorg/system/pull/89#issuecomment-1221474419  

I pushed a fix for defective gcc 4.7.x

---

## Comment 2

> Username: pdimov  
> Created_at: 2022-08-21 09:50:50 UTC  
> Url: https://github.com/boostorg/system/pull/89#issuecomment-1221511310  

1. This PR contains unrelated changes and hence can't be merged as-is.  
2. What is the code that fails without this PR and passes with it?

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2022-08-21 13:53:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/89#pullrequestreview-1079676733  

📁 include/boost/system/detail/error_code.hpp

```diff
 116 | 
 117 |-     BOOST_SYSTEM_CONSTEXPR error_code() BOOST_NOEXCEPT:
 117 |+ #if ! defined(BOOST_GCC) || BOOST_GCC >= 40800
```

> Username: pdimov  
> Created_at: 2022-08-21 13:53:02 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950849957  

BOOST_WORKAROUND would probably be better here.

> Username: vinniefalco  
> Created_at: 2022-08-21 13:55:40 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950850312  

Yep, Emil suggested that too but I erred on the side of not adding an additional include. I'm happy to use that macro though.

> Username: pdimov  
> Created_at: 2022-08-21 13:57:53 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950850701  

Please do.

> Username: vinniefalco  
> Created_at: 2022-08-21 14:00:05 UTC  
> Updated_at: 2022-08-21 14:00:06 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950850935  

oh dang, system/detail/config.hpp already includes workaround.hpp.

> Username: pdimov  
> Created_at: 2022-08-21 14:01:19 UTC  
> Updated_at: 2022-08-21 14:01:20 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950851113  

Doesn't matter, you should include it here too because it's used.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2022-08-21 13:57:31 UTC  
> Url: https://github.com/boostorg/system/pull/89#issuecomment-1221551549  

1. fixed  
2. I have `error_code` as a member of a struct, contained in objects which are constexpr variables with static storage duration:  
  
https://github.com/vinniefalco/http_proto/blob/cbdbb6e959258eebb8970a9b4f1950709d1998b8/include/boost/http_proto/metadata.hpp#L179  
  
(I switched it to the enum as a temporary workaround)  
  
The three "default" values (what you get with a default-constructed object) are constexpr here (`header` contains a `metadata` data member)  
https://github.com/vinniefalco/http_proto/blob/cbdbb6e959258eebb8970a9b4f1950709d1998b8/include/boost/http_proto/detail/impl/header.ipp#L73

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-08-21 14:00:02 UTC  
> Updated_at: 2022-08-21 14:00:17 UTC  
> Url: https://github.com/boostorg/system/pull/89#issuecomment-1221551937  

OK, but what is the simplest self-contained code example that both demonstrates the failure and is representative of your actual code?

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2022-08-21 14:09:11 UTC  
> Updated_at: 2022-08-21 14:10:03 UTC  
> Url: https://github.com/boostorg/system/pull/89#issuecomment-1221553537  

> OK, but what is the simplest self-contained code example that both demonstrates the failure and is representative of your actual code?  
  
Given this code:  
  
```  
#include <boost/system/error_code.hpp>  
  
struct M  
{  
    constexpr M() = default;  
  
    boost::system::error_code ec;  
};  
  
struct H  
{  
    constexpr H() = default;  
  
    M m;  
};  
  
H const& f()  
{  
    static constexpr H h;  
    return h;  
}  
```  
  
The current tip of develop fails to compile it at all under C++11.  
  
Changing `error_code::error_code()` in System from `BOOST_SYSTEM_CONSTEXPR` to `BOOST_CONSTEXPR` will compile under C++11 except for GCC 4.7.x (compiler bug I suppose, I struggled to find a workaround but failed)  
  
Applying this pull request will it compile under C++11, except that it will not be constexpr for GCC 4.7.x. My library does not support anything below 4.8 anyway, so this should be no worse than before my change.

---

## Review 7 [Commented]

> Username: pdimov  
> Created_at: 2022-08-21 15:09:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/system/pull/89#pullrequestreview-1079686080  

📁 include/boost/system/detail/error_code.hpp

```diff
 116 | 
 117 |-     BOOST_SYSTEM_CONSTEXPR error_code() BOOST_NOEXCEPT:
 117 |+ #if ! BOOST_WORKAROUND(BOOST_GCC, >= 40800)
```

> Username: pdimov  
> Created_at: 2022-08-21 15:09:48 UTC  
> Updated_at: 2022-08-21 15:09:49 UTC  
> Url: https://github.com/boostorg/system/pull/89#discussion_r950860599  

Should be <.


---
