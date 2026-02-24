# #157 Add include for add_const [Merged]

> Username: cho-m  
> Created at: 2025-03-31 03:10:22 UTC  
> Updated at: 2026-01-22 22:14:05 UTC  
> Merged at: 2025-04-25 00:42:52 UTC  
> Closed at: 2025-04-25 00:42:52 UTC  
> Url: https://github.com/boostorg/range/pull/157  

While testing 1.88.0.beta1 in Homebrew (https://github.com/Homebrew/homebrew-core/pull/217231), I saw a build error for `ncmpcpp`:  
```  
  In file included from ./curses/menu.h:25:  
  In file included from /opt/homebrew/include/boost/range/detail/any_iterator.hpp:22:  
  /opt/homebrew/include/boost/range/detail/any_iterator_interface.hpp:30:26: error: no template named 'add_const'; did you mean 'std::add_const'?  
     30 |                 typename add_const<  
        |                          ^  
```  
  
From glance, it looks like Boost `add_const` is wanted for  
https://github.com/boostorg/range/blob/a18dbf5d3384444e23e3c65915c1f69981d026bd/include/boost/range/detail/any_iterator_interface.hpp#L30

---

## Comment 1

> Username: computerquip-work  
> Created_at: 2025-04-24 20:09:30 UTC  
> Updated_at: 2025-04-24 20:11:59 UTC  
> Url: https://github.com/boostorg/range/pull/157#issuecomment-2828742795  

Ran into this as well, seems to prevent compilation entirely with no way to work around (without hacking in an add_const.hpp include just before). Change LGTM, I have to use similar locally.

---

## Comment 2

> Username: jwakely  
> Created_at: 2026-01-12 12:15:40 UTC  
> Url: https://github.com/boostorg/range/pull/157#issuecomment-3738276171  

This patch was not merged to the release branch, so boost-1.90.0 still has this bug.

---

## Comment 3

> Username: adentinger  
> Created_at: 2026-01-22 22:11:31 UTC  
> Updated_at: 2026-01-22 22:14:05 UTC  
> Url: https://github.com/boostorg/range/pull/157#issuecomment-3787005091  

@jwakely I have the same problem, so I created an issue for it: #163

---
