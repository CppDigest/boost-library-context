# #55 Add moving of strings from value_ref, fix value constructor [Closed]

> Username: sdkrystian  
> Created at: 2020-03-08 03:10:46 UTC  
> Updated at: 2020-06-10 15:20:30 UTC  
> Closed at: 2020-03-09 00:43:57 UTC  
> Url: https://github.com/boostorg/json/pull/55  



---

## Comment 1

> Username: sdkrystian  
> Created_at: 2020-03-08 03:18:32 UTC  
> Url: https://github.com/boostorg/json/pull/55#issuecomment-596161776  

Tests are failing as these changes are applied separately from #54, will pass once it is applied.

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2020-03-08 03:20:47 UTC  
> Url: https://github.com/boostorg/json/pull/55#issuecomment-596161894  

If one commit requires the other then they should be part of the same pull request. One commit per bugfix doesn't mean you have to put each one into its own pull request.

---

## Review 3 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 03:23:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/55#pullrequestreview-370802031  

📁 include/boost/json/impl/value_ref.ipp

```diff
  78 |+     if (what_ == what::strfunc)
  79 |+         return *static_cast<const string*>(f_.p);
  80 |+     else
```

> Username: vinniefalco  
> Created_at: 2020-03-08 03:23:25 UTC  
> Updated_at: 2020-03-08 22:26:59 UTC  
> Url: https://github.com/boostorg/json/pull/55#discussion_r389331353  

I would lose the `else`, it reads better


---

## Review 4 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 05:01:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/55#pullrequestreview-370804805  

📁 test/value_ref.cpp

```diff
 337 |+             // when the table pointer is null
 338 |+             array b{std::move(a), object()};
 339 |+             BOOST_TEST(a.capacity() == 0);
```

> Username: vinniefalco  
> Created_at: 2020-03-08 05:01:32 UTC  
> Updated_at: 2020-03-08 22:26:59 UTC  
> Url: https://github.com/boostorg/json/pull/55#discussion_r389335739  

I would remove these comments because they don't provide any additional information

> Username: sdkrystian  
> Created_at: 2020-03-08 22:27:29 UTC  
> Url: https://github.com/boostorg/json/pull/55#discussion_r389411931  

Done


---

## Review 5 [Commented]

> Username: vinniefalco  
> Created_at: 2020-03-08 16:28:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/json/pull/55#pullrequestreview-370842249  

📁 include/boost/json/impl/value_ref.ipp

```diff
  50 |+         e.what_ != what::strfunc)
  51 |+             return false;
  52 |     return true;
```

> Username: vinniefalco  
> Created_at: 2020-03-08 16:28:01 UTC  
> Updated_at: 2020-03-08 22:26:59 UTC  
> Url: https://github.com/boostorg/json/pull/55#discussion_r389384570  

Yep this looks much better without the else, as it is absolutely clear that the function ends on line 52


---

## Comment 6

> Username: codecov[bot]  
> Created_at: 2020-03-08 22:27:02 UTC  
> Updated_at: 2020-03-09 00:14:25 UTC  
> Url: https://github.com/boostorg/json/pull/55#issuecomment-596260113  

# [Codecov](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=h1) Report  
> Merging [#55](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=desc) into [develop](https://codecov.io/gh/vinniefalco/json/commit/37d0a09242bf0d116c0cd69415034ff93fd9cc02&el=desc) will **increase** coverage by `0.00%`.  
> The diff coverage is `100.00%`.  
  
[![Impacted file tree graph](https://codecov.io/gh/vinniefalco/json/pull/55/graphs/tree.svg?width=650&height=150&src=pr&token=HNiMmIjyKi)](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=tree)  
  
```diff  
@@           Coverage Diff            @@  
##           develop      #55   +/-   ##  
========================================  
  Coverage    99.09%   99.09%             
========================================  
  Files           57       57             
  Lines         4854     4861    +7       
========================================  
+ Hits          4810     4817    +7       
  Misses          44       44             
```  
  
  
| [Impacted Files](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [include/boost/json/impl/value\_ref.ipp](https://codecov.io/gh/vinniefalco/json/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL2ltcGwvdmFsdWVfcmVmLmlwcA==) | `97.26% <100.00%> (+0.24%)` | :arrow_up: |  
| [include/boost/json/value.hpp](https://codecov.io/gh/vinniefalco/json/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlLmhwcA==) | `100.00% <100.00%> (ø)` | |  
| [include/boost/json/value\_ref.hpp](https://codecov.io/gh/vinniefalco/json/pull/55/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9qc29uL3ZhbHVlX3JlZi5ocHA=) | `100.00% <100.00%> (ø)` | |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=footer). Last update [37d0a09...fdc86db](https://codecov.io/gh/vinniefalco/json/pull/55?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---

## Comment 7

> Username: vinniefalco  
> Created_at: 2020-03-09 00:43:57 UTC  
> Url: https://github.com/boostorg/json/pull/55#issuecomment-596273783  

Merged, thanks!

---
