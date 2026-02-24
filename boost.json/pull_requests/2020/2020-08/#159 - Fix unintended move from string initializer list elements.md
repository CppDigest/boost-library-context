# #159 Fix unintended move from string initializer list elements [Closed]

> Username: sdkrystian  
> Created at: 2020-08-16 14:52:10 UTC  
> Updated at: 2020-08-16 17:07:50 UTC  
> Closed at: 2020-08-16 17:07:50 UTC  
> Url: https://github.com/boostorg/json/pull/159  

Fixes #158

---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-08-16 15:02:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/159#pullrequestreview-468071545  

📁 test/value_ref.cpp

```diff
 327 |+             string a = "abcdefghijklmnopqrstuvwxyz";
 328 |+             BOOST_TEST(!a.empty());
 329 |+             array b{a, string()};
```

> Username: vinniefalco  
> Created_at: 2020-08-16 15:02:32 UTC  
> Updated_at: 2020-08-16 15:02:33 UTC  
> Url: https://github.com/boostorg/json/pull/159#discussion_r471123171  

I hate this form of initialization. I prefer  
```  
array b = { a, string() };  
```  
or  
```  
array b( { a, string() } );  
```

> Username: sdkrystian  
> Created_at: 2020-08-16 15:05:10 UTC  
> Updated_at: 2020-08-16 15:05:11 UTC  
> Url: https://github.com/boostorg/json/pull/159#discussion_r471123428  

Meh, it's the style we use in all the other tests. If you want, I can change them all

> Username: vinniefalco  
> Created_at: 2020-08-16 15:08:06 UTC  
> Url: https://github.com/boostorg/json/pull/159#discussion_r471123732  

No I'm just grumbling. The user reported a const string though, should we test that too?

> Username: vinniefalco  
> Created_at: 2020-08-16 15:41:41 UTC  
> Url: https://github.com/boostorg/json/pull/159#discussion_r471127177  

I added a test for `string const`


---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2020-08-16 16:15:10 UTC  
> Updated_at: 2020-08-16 16:50:03 UTC  
> Url: https://github.com/boostorg/json/pull/159#issuecomment-674545944  

# [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=h1) Report  
> Merging [#159](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=desc) into [develop](https://codecov.io/gh/CPPAlliance/json/commit/d7959196c74f8b3574b012aca7b99ea187dab665&el=desc) will **not change** coverage.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/CPPAlliance/json/pull/159/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #159   +/-   ##  
========================================  
  Coverage    98.42%   98.42%             
========================================  
  Files           62       62             
  Lines         5790     5790             
========================================  
  Hits          5699     5699             
  Misses          91       91             
```  
  
  
| [Impacted Files](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/CPPAlliance/json/pull/159/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <ø> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=footer). Last update [d795919...4e763fb](https://codecov.io/gh/CPPAlliance/json/pull/159?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
