# #563 Enhance/more remove rescale preparations [Merged]

> Username: barendgehrels  
> Created at: 2019-03-03 14:27:39 UTC  
> Updated at: 2019-03-06 08:47:40 UTC  
> Merged at: 2019-03-06 08:47:35 UTC  
> Closed at: 2019-03-06 08:47:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/563  

- Almost all is test-only; in code it only extracts buffer_box to separate headerfile  
- Changes BOOST_GEOMETRY_NO_ROBUSTNESS to BOOST_GEOMETRY_USE_RESCALING (with negated meaning). This precedes future versions where rescaling is NOT the default (or, in the end, removed)  
- Checks conditionally code w.r.t. runs without rescaling (many is already fixed in earlier commits)  
- Simplified conditional testcode  
- Adds three testcases from reported issues  
  
Note, this is not yet the last PR, work on intersection strategy is still to come.

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-03-04 01:51:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/563#pullrequestreview-209949367  

📁 include/boost/geometry/algorithms/detail/buffer/buffer_box.hpp

```diff
   1 |+ // Boost.Geometry (aka GGL, Generic Geometry Library)
   2 |+ 
   3 |+ // Copyright (c) 2018 Barend Gehrels, Amsterdam, the Netherlands.
```

> Username: awulkiew  
> Created_at: 2019-03-04 01:51:50 UTC  
> Updated_at: 2019-03-05 19:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/563#discussion_r261903358  

2018-2019 :)


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2019-03-04 02:15:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/563#pullrequestreview-209952224  

📁 test/algorithms/set_operations/union/union_multi.cpp

```diff
 406 |- #endif
 407 |-             0, 31, 0.2187385,
 394 |+             BG_IF_RESCALED(4, 3), 0, 31, 0.2187385,
```

> Username: awulkiew  
> Created_at: 2019-03-04 02:15:18 UTC  
> Updated_at: 2019-03-05 19:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/563#discussion_r261905669  

So one of these numbers is not correct, right?

> Username: barendgehrels  
> Created_at: 2019-03-04 19:39:00 UTC  
> Updated_at: 2019-03-05 19:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/563#discussion_r262211842  

I will merge Wednesday and check this. It might be both right (rescaling might split a polygon which would otherwise be unsplit). The reverse case we also had (should actually be split but is merged in rescaling).  
Thank you.

> Username: awulkiew  
> Created_at: 2019-03-04 21:19:01 UTC  
> Updated_at: 2019-03-05 19:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/563#discussion_r262246844  

No, I mean in general, in all of these cases where the result depends on rescaling. I'm just curious if you consider both results correct or not. Previously some of the tests were enabled only if the `FAILING_TESTS` flag was enabled, but now this macro is used. So it's not clear which expected result is the correct one.

> Username: barendgehrels  
> Created_at: 2019-03-05 19:35:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/563#discussion_r262657437  

But also in general, as far as I'm aware and can think of now, all results are correct (unless marked as _IGNORE when invalid). Otherwise they're excluded from the suite. Or a comment explicitly notes the reason of failure.  
I did not remove FAILING_TESTS flags but renamed some of them, and (when denoting invalidity or skipping a testcase) that is handled in the test-header itself now.  
  
Anyway, there are not so much of these new IF_RESCALED flags, I just checked most of them and added some comments. In general I consider the non-rescaled versions (often more splitted) as more correct. We've to check in the next phase what the final result will be.


---
