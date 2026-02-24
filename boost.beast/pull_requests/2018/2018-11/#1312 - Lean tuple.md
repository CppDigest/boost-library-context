# #1312 Lean tuple [Closed]

> Username: djarek  
> Created at: 2018-11-21 01:36:35 UTC  
> Updated at: 2019-09-10 21:02:21 UTC  
> Closed at: 2018-11-23 04:54:34 UTC  
> Url: https://github.com/boostorg/beast/pull/1312  

Intended to replace `std::tuple` for simple cases where a variadic list of data members is needed.

---

## Review 1 [Changes requested]

> Username: glenfe  
> Created_at: 2018-11-21 02:06:48 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/beast/pull/1312#pullrequestreview-177051798  

📁 include/boost/beast/core/detail/lean_tuple.hpp

```diff
  14 |+ #include <boost/mp11/integer_sequence.hpp>
  15 |+ 
  16 |+ #if __cplusplus > 201703L
```

> Username: glenfe  
> Created_at: 2018-11-21 02:06:38 UTC  
> Updated_at: 2018-11-21 20:01:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#discussion_r235232589  

This is wrong. You cannot assume this for *any* C++ language feature or C++ library feature. This is why we have Boost.Config. Because C++ implementations that have  `__cpluplus`  greater than 201703L (e.g. in C++2a standards mode) still do not have this feature implemented.


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2018-11-21 15:33:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1312#pullrequestreview-177298504  

📁 test/beast/core/detail/lean_tuple.cpp

```diff
   1 |+ //
   2 |+ // Copyright (c) 2016-2017 Vinnie Falco (vinnie dot falco at gmail dot com)
```

> Username: vinniefalco  
> Created_at: 2018-11-21 15:33:30 UTC  
> Updated_at: 2018-11-21 20:01:04 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#discussion_r235434220  

Your name?


---

## Comment 3

> Username: codecov[bot]  
> Created_at: 2018-11-21 20:19:55 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#issuecomment-440797184  

# [Codecov](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=h1) Report  
> Merging [#1312](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/beast/commit/d4c5dc2747e62c6c1a2c3e319f88940309f0c644?src=pr&el=desc) will **increase** coverage by `<.01%`.  
> The diff coverage is `96.42%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/beast/pull/1312/graphs/tree.svg?width=650&token=Gq6MFA9JRF&height=150&src=pr)](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop    #1312      +/-   ##  
===========================================  
+ Coverage    94.45%   94.46%   +<.01%       
===========================================  
  Files          123      124       +1       
  Lines        11442    11456      +14       
===========================================  
+ Hits         10808    10822      +14       
  Misses         634      634  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/beast/core/buffers\_cat.hpp](https://codecov.io/gh/boostorg/beast/pull/1312/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2J1ZmZlcnNfY2F0LmhwcA==) | `100% <ø> (ø)` | :arrow_up: |  
| [include/boost/beast/core/detail/lean\_tuple.hpp](https://codecov.io/gh/boostorg/beast/pull/1312/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9sZWFuX3R1cGxlLmhwcA==) | `100% <100%> (ø)` | |  
| [include/boost/beast/core/detail/bind\_handler.hpp](https://codecov.io/gh/boostorg/beast/pull/1312/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2RldGFpbC9iaW5kX2hhbmRsZXIuaHBw) | `100% <100%> (ø)` | :arrow_up: |  
| [include/boost/beast/core/impl/buffers\_cat.ipp](https://codecov.io/gh/boostorg/beast/pull/1312/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9iZWFzdC9jb3JlL2ltcGwvYnVmZmVyc19jYXQuaXBw) | `93.84% <90.9%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=footer). Last update [d4c5dc2...3963b70](https://codecov.io/gh/boostorg/beast/pull/1312?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 4 [Commented]

> Username: glenfe  
> Created_at: 2018-11-21 21:18:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/beast/pull/1312#pullrequestreview-177432519  

📁 include/boost/beast/core/detail/lean_tuple.hpp

```diff
   9 |+ 
  10 |+ 
  11 |+ #ifndef BOOST_BEAST_DETAIL_LEAN_TUPLE_HPP
```

> Username: glenfe  
> Created_at: 2018-11-21 21:18:00 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#discussion_r235541377  

Shouldn't this be `BOOST_BEAST_CORE_DETAIL_LEAN_TUPLE_HPP`?

> Username: vinniefalco  
> Created_at: 2018-11-21 22:09:28 UTC  
> Updated_at: 2018-11-21 22:09:29 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#discussion_r235552218  

I pretend like the "core" directory doesn't actually exist. Because it shouldn't exist. The actual include file should be `<beast/detail/lean_tuple.hpp>`. Unfortunately if I were to do that then the Visual Studio project would have a really long, annoying list of header files that I could not collapse and still see other virtual folders.


---

## Comment 5

> Username: vinniefalco  
> Created_at: 2018-11-23 04:54:14 UTC  
> Url: https://github.com/boostorg/beast/pull/1312#issuecomment-441156293  

Merged in 191

---
