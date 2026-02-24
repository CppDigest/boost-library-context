# #40 fix surrogate conversion error [Closed]

> Username: madmongo1  
> Created at: 2020-02-11 14:55:42 UTC  
> Updated at: 2020-02-14 01:46:42 UTC  
> Closed at: 2020-02-14 01:46:42 UTC  
> Url: https://github.com/boostorg/json/pull/40  

fixes #38

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2020-02-11 15:51:46 UTC  
> Updated_at: 2020-02-12 08:34:57 UTC  
> Url: https://github.com/boostorg/json/pull/40#issuecomment-584703212  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=h1) Report  
> Merging [#40](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/75528587ff45c9aa0b7d7e0ca379a4f5b36d49fe?src=pr&el=desc) will **not change** coverage.  
> The diff coverage is `100%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/40/graphs/tree.svg?width=650&token=HNiMmIjyKi&height=150&src=pr)](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff           @@  
##           develop     #40   +/-   ##  
=======================================  
  Coverage     98.2%   98.2%             
=======================================  
  Files           57      57             
  Lines         4856    4856             
=======================================  
  Hits          4769    4769             
  Misses          87      87  
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/basic\_parser.ipp](https://codecov.io/gh/vinniefalco/json/pull/40/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvYmFzaWNfcGFyc2VyLmlwcA==) | `97.96% <100%> (ø)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=footer). Last update [7552858...62bdfca](https://codecov.io/gh/vinniefalco/json/pull/40?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-11 21:51:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/40#pullrequestreview-357022963  

📁 test/basic_parser.cpp

```diff
 359 |         bad ("\"\\\\\n\"");
 360 |+ 
 361 |+         good(R"json("Hello\u0000World")json"); // Issue 38
```

> Username: vinniefalco  
> Created_at: 2020-02-11 21:51:05 UTC  
> Updated_at: 2020-02-12 07:57:16 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r377921120  

If you're going to mention the issue, please make it a hyperlink before the statement.  
  
However, since this library has not yet been released, it is not important to even mention the issue. Your call.

> Username: madmongo1  
> Created_at: 2020-02-12 07:57:56 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r378087671  

replaced with comment to indicate the nature of the test rather than the origin


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-11 21:53:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/40#pullrequestreview-357024135  

📁 test/parser.cpp

```diff
 431 |+                 BOOST_ASSERT(v2.is_string());
 432 |+                 auto s2 = v2.as_string();
 433 |+                 BOOST_TEST(s2 == expected);
```

> Username: vinniefalco  
> Created_at: 2020-02-11 21:53:02 UTC  
> Updated_at: 2020-02-12 07:57:16 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r377922043  

This is a pretty verbose way of simply saying  
```  
BOOST_TEST( json::parse(s).as_array()[0].as_string() == expected );  
```

> Username: vinniefalco  
> Created_at: 2020-02-11 21:54:49 UTC  
> Updated_at: 2020-02-12 07:57:16 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r377922916  

Or even  
```  
BOOST_TEST(json::parse(string_view( "[\"Hello\u0000World\"]" ))  
    .as_array().at(0).as_string() == string_view("Hello\x00World", 11) );  
```

> Username: madmongo1  
> Created_at: 2020-02-12 07:58:29 UTC  
> Updated_at: 2020-02-12 07:58:30 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r378087875  

made less verbose.  
Renamed the test case to indicate function rather than motivation


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-02-14 01:44:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/40#pullrequestreview-358668287  

📁 test/basic_parser.cpp

```diff
 359 |         bad ("\"\\\\\n\"");
 360 |+ 
 361 |+         // embedded NULL character
```

> Username: vinniefalco  
> Created_at: 2020-02-14 01:44:54 UTC  
> Updated_at: 2020-02-14 01:44:55 UTC  
> Url: https://github.com/boostorg/json/pull/40#discussion_r379213827  

Redundant


---
