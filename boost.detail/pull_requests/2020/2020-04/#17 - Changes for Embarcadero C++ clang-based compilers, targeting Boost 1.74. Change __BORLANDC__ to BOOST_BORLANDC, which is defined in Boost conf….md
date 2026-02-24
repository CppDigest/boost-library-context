# #17 Changes for Embarcadero C++ clang-based compilers, targeting Boost 1.74. Change __BORLANDC__ to BOOST_BORLANDC, which is defined in Boost conf… [Merged]

> Username: eldiener  
> Created at: 2020-04-08 17:11:49 UTC  
> Updated at: 2020-05-06 17:29:54 UTC  
> Merged at: 2020-05-06 17:29:54 UTC  
> Closed at: 2020-05-06 17:29:54 UTC  
> Url: https://github.com/boostorg/detail/pull/17  

…ig for the Embarcadero non-clang-based compilers.

---

## Comment 1

> Username: eldiener  
> Created_at: 2020-05-06 13:23:32 UTC  
> Url: https://github.com/boostorg/detail/pull/17#issuecomment-624646525  

Can this please be merged ?

---

## Review 2 [Commented]

> Username: Lastique  
> Created_at: 2020-05-06 15:00:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/detail/pull/17#pullrequestreview-406702385  

📁 include/boost/detail/catch_exceptions.hpp

```diff
  28 | 
  29 |- # if defined(__BORLANDC__) && (__BORLANDC__ <= 0x0551)
  29 |+ # if defined(BOOST_BORLANDC) && (BOOST_BORLANDC <= 0x0551)
```

> Username: Lastique  
> Created_at: 2020-05-06 15:00:37 UTC  
> Updated_at: 2020-05-06 17:16:36 UTC  
> Url: https://github.com/boostorg/detail/pull/17#discussion_r420861822  

`#include <boost/config.hpp>` is missing. Same below.

> Username: eldiener  
> Created_at: 2020-05-06 15:19:36 UTC  
> Updated_at: 2020-05-06 17:16:36 UTC  
> Url: https://github.com/boostorg/detail/pull/17#discussion_r420876520  

You are correct and I have updated the file in the PR. However the header file is using BOOST_NO_EXCEPTIONS, which definitely does require config.hpp, so should not boost/config.hpp be included ?  
  
I also do not know what you mean by "Same below".

> Username: Lastique  
> Created_at: 2020-05-06 15:22:20 UTC  
> Updated_at: 2020-05-06 17:16:36 UTC  
> Url: https://github.com/boostorg/detail/pull/17#discussion_r420878565  

No, my point is that you should add `#include <boost/config.hpp>`.  
  
> I also do not know what you mean by "Same below".  
  
I mean the other file you changed in the PR. It has the same issue of missing `boost/config.hpp`.

> Username: eldiener  
> Created_at: 2020-05-06 17:17:05 UTC  
> Url: https://github.com/boostorg/detail/pull/17#discussion_r420957874  

Done !


---
