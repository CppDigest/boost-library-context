# #59 - `subview` doesn't seem to be documented [Closed]

> Username: pdimov  
> Created at: 2024-05-07 17:45:54 UTC  
> Updated at: 2025-12-15 13:28:51 UTC  
> Closed at: 2025-12-15 13:28:51 UTC  
> Url: https://github.com/boostorg/static_string/issues/59  

I don't see it in https://www.boost.org/doc/libs/1_85_0/libs/static_string/doc/html/static_string/ref/boost__static_strings__basic_static_string.html.

---

## Comment 1

> Username: sdkrystian  
> Created at: 2024-05-07 17:56:15 UTC  
> Url: https://github.com/boostorg/static_string/issues/59#issuecomment-2099000126  

Seems like `BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW` isn't being correctly set when running doxygen (the declaration of `subview` is guarded by `#ifdef BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW`)? I think adding `BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW \\` [here](https://github.com/boostorg/static_string/blob/develop/doc/Jamfile#L31) would fix it.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2024-05-08 00:59:36 UTC  
> Url: https://github.com/boostorg/static_string/issues/59#issuecomment-2099539993  

One of the reasons I don't like "configuration macros" which effectively create two versions of the library.

---

## Comment 3

> Username: gennaroprota  
> Created at: 2025-10-24 10:11:35 UTC  
> Url: https://github.com/boostorg/static_string/issues/59#issuecomment-3442330636  

I see `subview()` now: <https://www.boost.org/doc/libs/1_89_0/libs/static_string/doc/html/static_string/ref/boost__static_strings__basic_static_string.html>. I'm not sure why, though, since it doesn't look like `BOOST_STATIC_STRING_HAS_ANY_STRING_VIEW` is being defined.

---

## Comment 4

> Username: gennaroprota  
> Created at: 2025-12-15 13:28:51 UTC  
> Url: https://github.com/boostorg/static_string/issues/59#issuecomment-3655658264  

As said, the documentation of `subview()` does appear now, so I'm going to close this issue.
