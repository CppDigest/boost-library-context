# #891 [test] remove the non used KRAMER define and some other changes in testing is_valid [Merged]

> Username: barendgehrels  
> Created at: 2021-07-21 17:01:02 UTC  
> Updated at: 2021-07-28 11:09:34 UTC  
> Merged at: 2021-07-28 11:09:27 UTC  
> Closed at: 2021-07-28 11:09:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/891  

The objective of this PR is removing the define `BOOST_GEOMETRY_USE_KRAMER` because it's always used.  
Last year I tried to change that by using the generic form instead. That's possible but didn't give much improvements and some regressions, and needed more and more conditions and tuning.  
  
So it's redundant now, and I applied some other changes in tests, for example in difference where the symmetric difference was actually never tested without rescaling (I forgot that earlier).

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-07-21 17:02:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/891#pullrequestreview-711942857  

📁 test/algorithms/set_operations/difference/difference_multi.cpp

```diff
 515 |     // The difference algorithm can generate (additional) slivers
 528 |-     BoostGeometryWriteExpectedFailures(22, 12, 16, 7);
 516 |+     BoostGeometryWriteExpectedFailures(28, 15, 19, 10);
```

> Username: barendgehrels  
> Created_at: 2021-07-21 17:02:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/891#discussion_r674172698  

Some more tests are done in all cases. Therefore there are more failures.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-07-22 07:17:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/891#pullrequestreview-712427474  

Thanks!

---
