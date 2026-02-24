# #417 Replace `throw()` with `BOOST_NOEXCEPT_OR_NOTHROW` [Merged]

> Username: Romain-Geissler-1A  
> Created at: 2018-11-11 22:22:05 UTC  
> Updated at: 2018-11-12 11:38:46 UTC  
> Merged at: 2018-11-12 11:38:46 UTC  
> Closed at: 2018-11-12 11:38:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/417  



---

## Review 1 [Commented]

> Username: Kojoley  
> Created_at: 2018-11-11 23:04:17 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/417#pullrequestreview-173719309  

Makes sense. Thanks!

📁 example/support/utree/error_handler.hpp

```diff
  76 | 
  77 |-     virtual ~expected_component() throw() {}
  77 |+     virtual ~expected_component() noexcept {}
```

> Username: Kojoley  
> Created_at: 2018-11-11 22:55:52 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232508580  

Please, use `BOOST_NOEXCEPT_OR_NOTHROW` here too.

> Username: Romain-Geissler-1A  
> Created_at: 2018-11-11 23:06:28 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509109  

I thought that things in "example" would use standard C++ rather than boost specific macros (as most people are using C++11 nowadays). But as you wish.

> Username: Romain-Geissler-1A  
> Created_at: 2018-11-11 23:08:43 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509204  

Done.

> Username: Kojoley  
> Created_at: 2018-11-11 23:11:34 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509325  

The examples are part of CI (I did not add them to builds just because we already hitting time limits, but it is on my TODO list).

---

📁 example/support/utree/error_handler.hpp

```diff
  78 | 
  79 |-     virtual char const* what() const throw()
  79 |+     virtual char const* what() const noexcept
```

> Username: Kojoley  
> Created_at: 2018-11-11 22:56:12 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232508597  

Same as above.

> Username: Romain-Geissler-1A  
> Created_at: 2018-11-11 23:08:37 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509198  

Done.


📁 include/boost/spirit/home/x3/directive/expect.hpp

```diff
  28 |         {}
  29 |-         ~expectation_failure() throw() {}
  29 |+         ~expectation_failure() BOOST_NOEXCEPT_OR_NOTHROW {}
```

> Username: Kojoley  
> Created_at: 2018-11-11 22:59:40 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232508748  

Just remove `throw()` (X3 is C++14+ library and since C++11 destructors are `noexcept` by default)

> Username: Romain-Geissler-1A  
> Created_at: 2018-11-11 23:08:26 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509185  

Done.


---

## Review 2 [Commented]

> Username: Kojoley  
> Created_at: 2018-11-11 23:13:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/417#pullrequestreview-173720217  

📁 example/support/utree/error_handler.hpp

```diff
  76 | 
  77 |-     virtual ~expected_component() throw() {}
  77 |+     virtual ~expected_component() BOOST_NOEXCEPT_OR_NOTHROW {}
```

> Username: Kojoley  
> Created_at: 2018-11-11 23:13:11 UTC  
> Updated_at: 2018-11-11 23:14:18 UTC  
> Url: https://github.com/boostorg/spirit/pull/417#discussion_r232509401  

I do not see `#include <boost/config.hpp>`, can you please add it?


---
