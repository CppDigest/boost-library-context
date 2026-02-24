# #1185 [intersection] remove closing point correctly [Merged]

> Username: barendgehrels  
> Created at: 2023-08-16 17:37:23 UTC  
> Updated at: 2023-12-04 10:43:20 UTC  
> Merged at: 2023-09-13 18:08:21 UTC  
> Closed at: 2023-09-13 18:08:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1185  

* the function (in namespace detail) is split and two parts are renamed on purpose, because functionality is changed  
* tests are added  
* intersection.cpp split into new file intersection_integer.cpp  
* testing point count now if specified  
  
Fixes: #1184

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-08-16 17:39:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1185#pullrequestreview-1581126769  

📁 test/algorithms/set_operations/intersection/test_intersection.hpp

```diff
 199 |+         // Check inserter behaviour with stratey
 200 |+         using strategy_type
 201 |+             = typename bg::strategies::relate::services::default_strategy<G1, G2>::type;
```

> Username: barendgehrels  
> Created_at: 2023-08-16 17:39:08 UTC  
> Updated_at: 2023-08-16 17:39:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1185#discussion_r1296238057  

This was a left-over related to the change to umbrella strategies.  
Because the define `BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE`, this code was effectively not tested for some years... but the new unit test does not use that define.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-08-16 17:40:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1185#pullrequestreview-1581128774  

📁 test/algorithms/set_operations/intersection/intersection.cpp

```diff
 884 |- 
 885 |-     test_ticket_10868<long long>("MULTIPOLYGON(((33520458 6878575,33480192 14931538,31446819 18947953,30772384 19615678,30101303 19612322,30114725 16928001,33520458 6878575)))");
 886 |- #endif
```

> Username: barendgehrels  
> Created_at: 2023-08-16 17:40:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1185#discussion_r1296239332  

Moved to `intersection_integer.cpp` - but it was excluded anyway, and still fails, for reasons not investigated.  
Apart from that, verifying WKT expectations is cumbersome and normally not done in these tests.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2023-08-16 17:48:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1185#pullrequestreview-1581140155  

📁 test/algorithms/set_operations/intersection/intersection_integer.cpp

```diff
  44 |+ 
  45 |+     ut_settings settings;
  46 |+     settings.test_point_count = true;
```

> Username: barendgehrels  
> Created_at: 2023-08-16 17:48:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1185#discussion_r1296246805  

This is the way, in these tests, to verify if the closing point is removed.  
Some test code (for example `test_point_count`) in this PR relates to that.


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2023-09-13 09:33:43 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1185#pullrequestreview-1624118032  

Thanks, OK for merging!

---
