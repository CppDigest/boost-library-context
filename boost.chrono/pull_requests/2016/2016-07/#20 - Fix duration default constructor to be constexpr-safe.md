# #20 Fix duration default constructor to be constexpr-safe. [Merged]

> Username: jzmaddock  
> Created at: 2016-07-03 17:49:16 UTC  
> Updated at: 2016-07-03 18:39:43 UTC  
> Merged at: 2016-07-03 18:39:43 UTC  
> Closed at: 2016-07-03 18:39:43 UTC  
> Url: https://github.com/boostorg/chrono/pull/20  

Also add tests for the issue.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-07-03 17:53:21 UTC  
> Url: https://github.com/boostorg/chrono/pull/20#issuecomment-230166065  

This issue was exposed by compiling with MSVC-14up3 with constexpr support enabled: for whatever reason VC++ was flagging up the fact that duration's default constructor was not actually constexpr in contexts that GCC/Clang did not.  Of course GCC/Clang will complain as well if you actually test that constructor directly.  
  
Fixing this is a showstopper for enabling constexpr support in Boost.Config for VC14up3, as otherwise we get all kinds of regressions in chrono.

---
