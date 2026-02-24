# #597 Using SSE4.2 intrinsics in basic_parser if available [Closed]

> Username: octopus-prime  
> Created at: 2017-07-04 22:16:16 UTC  
> Updated at: 2017-07-05 05:17:35 UTC  
> Closed at: 2017-07-05 04:23:39 UTC  
> Url: https://github.com/boostorg/beast/pull/597  

issue #585

---

## Comment 1

> Username: octopus-prime  
> Created_at: 2017-07-04 22:36:56 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312962946  

Don't miss `-msse4.2` for gcc and clang. Otherwise the compiler will not generate SSE4.2 code ;-)

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 22:41:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/597#pullrequestreview-47931126  

📁 include/beast/http/detail/basic_parser.hpp

```diff
 221 | 
 218 |-     #if ! BEAST_NO_INTRINSICS
 222 |+     #if (!BEAST_NO_INTRINSICS)
```

> Username: vinniefalco  
> Created_at: 2017-07-04 22:41:39 UTC  
> Updated_at: 2017-07-04 23:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/597#discussion_r125534430  

Why the parenthesis?

> Username: octopus-prime  
> Created_at: 2017-07-04 22:44:56 UTC  
> Updated_at: 2017-07-04 23:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/597#discussion_r125534556  

Don't know. May be because of `if (...)` :D


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2017-07-04 22:42:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/597#pullrequestreview-47931143  

📁 include/beast/http/detail/basic_parser.hpp

```diff
  22 |+ #if (!BEAST_NO_INTRINSICS)
  23 |+ #include <nmmintrin.h>
  24 |+ #endif
```

> Username: vinniefalco  
> Created_at: 2017-07-04 22:42:00 UTC  
> Updated_at: 2017-07-04 23:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/597#discussion_r125534446  

Is `<nmintrin.h>` available on msvc, gcc, clang?

> Username: octopus-prime  
> Created_at: 2017-07-04 22:44:08 UTC  
> Updated_at: 2017-07-04 23:23:00 UTC  
> Url: https://github.com/boostorg/beast/pull/597#discussion_r125534523  

Yes, I think so.


---

## Comment 4

> Username: vinniefalco  
> Created_at: 2017-07-04 22:42:12 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312963328  

This is exciting!!

---

## Comment 5

> Username: octopus-prime  
> Created_at: 2017-07-04 22:50:14 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312963887  

WTH! Builds failed because `fatal error: 'boost/predef/hardware/simd.h'` ?!???

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2017-07-04 22:51:06 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312963960  

Welcome to the last year of my life.  
  
Check the version of Boost used on travis:  
https://github.com/vinniefalco/Beast/blob/develop/.travis.yml#L14

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2017-07-04 23:23:18 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312966124  

I'm pulling the optimizations out of **v72** and putting them into **v73** so I can get the other branch merged

---

## Comment 8

> Username: vinniefalco  
> Created_at: 2017-07-05 00:12:28 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312969206  

>fatal error: 'boost/predef/hardware/simd.h' ?!???  
  
Boost.Predef is really for end-users, not for writers of Boost libraries. Beast is supposed to use Boost.Config, in fact its mandatory as part of Boost.

---

## Comment 9

> Username: vinniefalco  
> Created_at: 2017-07-05 05:17:35 UTC  
> Url: https://github.com/boostorg/beast/pull/597#issuecomment-312998475  

Hmm... GitHub closed the branch because the v72 branch was deleted...

---
