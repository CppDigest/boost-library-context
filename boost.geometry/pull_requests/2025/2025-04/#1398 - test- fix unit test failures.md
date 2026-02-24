# #1398 test: fix unit test failures [Merged]

> Username: barendgehrels  
> Created at: 2025-04-26 08:31:37 UTC  
> Updated at: 2025-07-16 15:53:52 UTC  
> Merged at: 2025-04-29 14:50:14 UTC  
> Closed at: 2025-04-29 14:50:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398  

Fixes 2 unit tests which were broken in my previous two PR's (each one broke one)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 08:32:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1398#pullrequestreview-2795826192  

📁 test/algorithms/overlay/overlay.cpp

```diff
 247 |     {
 248 |         overlay::apply(g1, g2, std::back_inserter(result), strategy, visitor);
 249 |+     }
```

> Username: barendgehrels  
> Created_at: 2025-04-26 08:32:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2061224139  

This was somehow removed.  
I noticed it earlier but I thought it was locally.  
The CI apparently does not test all tests for a PR.

> Username: barendgehrels  
> Created_at: 2025-04-26 08:47:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2061227155  

Got it, on a PR only `minimal.cpp` is tested. After merging all is tested.  
  
Could we, for one platform, add some tests more? The question is then of course: which tests...

> Username: vissarion  
> Created_at: 2025-04-29 10:47:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2066039404  

I appears that this happens quite often (I have similar experiences). The old school way is of course to run all tests locally before pushing. If we prefer a CI approach you should enable all tests I think by running a script similar to `cmake` on PRs. As it is now it is 1.5 hrs which seems a lot. We could try to have a "light-weight" version of it (test only one type, e.g. `double`, avoid compiler's optimization etc).  
  
Note also that the `cmake` script is running by default in your fork; e.g. for this PR https://github.com/barendgehrels/geometry/actions/runs/14679494761/job/41200347512

> Username: barendgehrels  
> Created_at: 2025-04-29 14:49:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2066745020  

Yes, 1.5 hrs is a lot.  
Even a light-weight test would not have caught this one (at least not the overlay-one), probably.  
  
I run all tests locally on every new PR. But because this was a merge of an older one, didn't do it this time.  
So I should ;-)   
  
Thanks! I'll merge now.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 08:32:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1398#pullrequestreview-2795826243  

📁 test/algorithms/overlay/overlay.cpp

```diff
 381 |     TEST_UNION(ggl_list_20140212_sybren, 0.002471626, 2, 0);
 382 |     TEST_UNION_WITH(issue_1100_multi, 1, 0, 1.46181, 1, 0); // fixed by ux/uu blocking
 382 |-     TEST_UNION(issue_1363_multi, 99.99, 2, 0);
```

> Username: barendgehrels  
> Created_at: 2025-04-26 08:32:50 UTC  
> Updated_at: 2025-04-26 08:43:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2061224175  

This one has never succeeded


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 08:33:49 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1398#pullrequestreview-2795826509  

📁 test/algorithms/overlay/overlay.cpp

```diff
 371 | 
 372 |     TEST_UNION(case_134_multi, 66.0, 1, 2);
 371 |-     return;
```

> Username: barendgehrels  
> Created_at: 2025-04-26 08:33:49 UTC  
> Updated_at: 2025-04-26 08:33:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2061224287  

Note, most of these tests are repeated in other unit-tests and for my own investigations.  
Earlier they were not there - and maybe we could remove them.  
For now, I just fixed the test and removed the `return;` which is not intended to be commited.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-04-26 08:34:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1398#pullrequestreview-2795826696  

📁 test/core/assert.cpp

```diff
  13 | 
  14 | 
  15 |+ #define BOOST_GEOMETRY_ENABLE_ASSERT_HANDLER
```

> Username: barendgehrels  
> Created_at: 2025-04-26 08:34:43 UTC  
> Updated_at: 2025-04-26 08:34:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/1398#discussion_r2061224430  

This was caused by the PR for the define, which changed some code in `geometry_test_common.hpp`  
Therefore the assert handler should be enabled earlier.


---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2025-04-29 10:41:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1398#pullrequestreview-2802919316  

Thanks!

---
