# #54 Fix move construction of value from object [Closed]

> Username: sdkrystian  
> Created at: 2020-03-08 02:01:31 UTC  
> Updated at: 2020-06-08 19:24:51 UTC  
> Closed at: 2020-03-08 22:23:23 UTC  
> Url: https://github.com/boostorg/json/pull/54  



---

## Review 1 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 02:11:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/54#pullrequestreview-370799771  

📁 test/value.cpp

```diff
 502 |+             {
 503 |+                 // verify moved from
 504 |+                 object obj{{"a", 1}, {"a", 1}};
```

> Username: vinniefalco  
> Created_at: 2020-03-08 02:11:21 UTC  
> Updated_at: 2020-03-08 03:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/54#discussion_r389327995  

I would not use duplicate keys


---

## Review 2 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 02:11:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/54#pullrequestreview-370799780  

📁 test/value.cpp

```diff
 503 |+                 // verify moved from
 504 |+                 object obj{{"a", 1}, {"a", 1}};
 505 |+                 BOOST_TEST(obj.capacity());
```

> Username: vinniefalco  
> Created_at: 2020-03-08 02:11:40 UTC  
> Updated_at: 2020-03-08 03:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/54#discussion_r389328019  

I would not depend on conversion to bool. In other words, spell this out: `obj.capacity() > 0`


---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 02:12:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/54#pullrequestreview-370799794  

📁 test/value.cpp

```diff
 505 |+                 BOOST_TEST(obj.capacity());
 506 |+                 value jv(std::move(obj), {});
 507 |+                 BOOST_TEST(!obj.capacity());
```

> Username: vinniefalco  
> Created_at: 2020-03-08 02:12:08 UTC  
> Updated_at: 2020-03-08 03:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/54#discussion_r389328039  

I would spell this out: `obj.capacity() == 0`

> Username: sdkrystian  
> Created_at: 2020-03-08 02:16:52 UTC  
> Updated_at: 2020-03-08 03:21:09 UTC  
> Url: https://github.com/boostorg/json/pull/54#discussion_r389328261  

Copy, will apply.


---

## Comment 4

> Username: codecov[bot]  
> Created_at: 2020-03-08 02:33:38 UTC  
> Updated_at: 2020-03-08 05:02:01 UTC  
> Url: https://github.com/boostorg/json/pull/54#issuecomment-596159306  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=h1) Report  
> Merging [#54](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/37d0a09242bf0d116c0cd69415034ff93fd9cc02&el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/54/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #54   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4854     4855    +1       
========================================  
+ Hits          4810     4811    +1       
  Misses          44       44             
```  
  
  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=footer). Last update [37d0a09...f75ecaf](https://codecov.io/gh/vinniefalco/json/pull/54?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
