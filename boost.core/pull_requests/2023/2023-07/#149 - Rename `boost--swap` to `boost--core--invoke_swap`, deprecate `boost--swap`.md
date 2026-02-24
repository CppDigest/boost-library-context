# #149 Rename `boost::swap` to `boost::core::invoke_swap`, deprecate `boost::swap` [Merged]

> Username: Lastique  
> Created at: 2023-07-11 09:49:21 UTC  
> Updated at: 2023-08-25 11:50:11 UTC  
> Merged at: 2023-08-25 11:50:06 UTC  
> Closed at: 2023-08-25 11:50:06 UTC  
> Url: https://github.com/boostorg/core/pull/149  

The rename allows to avoid forming an infinite recursion in compile time (because of `noexcept` specification that needs to resolve the unqualified call to `swap`) or run time (in case if the `boost::swap` function is the only one suitable for the passed arguments).  
  
To avoid the compile-time recursion, removed `noexcept` specification from `boost::swap`. The specification is still present in `boost::core::invoke_swap`.  
  
Deprecated `boost::swap` and associated headers. `boost::core::invoke_swap` is defined in a new `boost/core/invoke_swap.hpp` header.  
  
Updated docs and tests. Removed tests that check inclusion of deprecated headers.  
  
Fixes https://github.com/boostorg/core/issues/148.

---

## Comment 1

> Username: pdimov  
> Created_at: 2023-07-11 21:46:45 UTC  
> Url: https://github.com/boostorg/core/pull/149#issuecomment-1631553327  

This looks good to me. @glenfe?

---

## Comment 2

> Username: Lastique  
> Created_at: 2023-07-11 21:56:04 UTC  
> Url: https://github.com/boostorg/core/pull/149#issuecomment-1631561816  

Also, do we want this to be part of 1.83? I did not create any PRs updating other libraries to switch over to `invoke_swap`.

---

## Comment 3

> Username: pdimov  
> Created_at: 2023-07-11 22:05:50 UTC  
> Url: https://github.com/boostorg/core/pull/149#issuecomment-1631570902  

It's probably too late for 1.83.

---

## Comment 4

> Username: Lastique  
> Created_at: 2023-07-12 09:13:59 UTC  
> Url: https://github.com/boostorg/core/pull/149#issuecomment-1632144531  

> It's probably too late for 1.83.  
  
Yeah, I thought so too. I'll merge this after the release then. 1.83 will be released as currently is.

---

## Comment 5

> Username: glenfe  
> Created_at: 2023-07-12 19:20:41 UTC  
> Updated_at: 2023-07-12 19:24:46 UTC  
> Url: https://github.com/boostorg/core/pull/149#issuecomment-1633083930  

Looks good to me.  
  
We might want to delay 1.83 for other reasons (Context submodule being disconnected from master HEAD has resulted in other issues) so I want to think about getting this in.   
  
The 1.83 beta will ship with a broken (on ARM64) Context, but it should be fixed before the 1.83 release.

---
