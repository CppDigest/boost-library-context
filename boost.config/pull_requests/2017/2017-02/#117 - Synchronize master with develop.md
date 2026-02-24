# #117 Synchronize master with develop [Closed]

> Username: Lastique  
> Created at: 2017-02-17 13:11:32 UTC  
> Updated at: 2017-02-20 16:22:56 UTC  
> Closed at: 2017-02-20 09:07:20 UTC  
> Url: https://github.com/boostorg/config/pull/117  

Please, merge develop to master. We need at least https://github.com/boostorg/config/commit/6e93ac5d7267914503fea4611035e4773daa5c1f to merge our changes in Boost.Core to master.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-02-20 09:07:20 UTC  
> Url: https://github.com/boostorg/config/pull/117#issuecomment-281023888  

Merged manually, last few commits to develop omitted for now as they cause unresolved issues with clang.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-02-20 16:08:48 UTC  
> Url: https://github.com/boostorg/config/pull/117#issuecomment-281118496  

Thanks. Though unfortunately, `BOOST_NO_CXX11_SFINAE_EXPR` (6e93ac5) is not merged yet, and this is the change I was looking forward to be in master. I'll have to wait until the problems are resolved.

---

## Comment 3

> Username: glenfe  
> Created_at: 2017-02-20 16:22:56 UTC  
> Url: https://github.com/boostorg/config/pull/117#issuecomment-281122129  

It's not defined in Clang in C++03/98 mode; I'll submit a pull request for Clang.hpp shortly.

---
