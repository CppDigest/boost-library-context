# #1404 Fix/buffer use cluster for within piece [Merged]

> Username: barendgehrels  
> Created at: 2025-05-04 10:53:39 UTC  
> Updated at: 2025-07-16 15:58:07 UTC  
> Merged at: 2025-05-13 20:17:29 UTC  
> Closed at: 2025-05-13 20:17:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404  

This is a follow-up of #1369   
I wrote it in February, after first PR - and just rebased it and retested it.  
It is much smaller.  
  
What can happen that one point is considered as within another piece (that's good) but another clustered piece is not considered as within, or it is considered as within the first piece. This is all caused by floating point behaviour.  
  
It happens with both side strategies (robust and `by_triangle`)  
  
This fix is simple, it considers clusters and the pieces involved, and does not even need to perform a within-check in those cases.  
  
Behaviour, before (on current `develop`, this is the same as reported in #1369 but there it was on that branch):  
```  
geometries: 603809 errors: 506 type: d time: 67.478  
geometries: 610519 errors: 417 type: d time: 63.547 (robust side)  
```  
  
Behaviour, after the fix  
```  
geometries: 608834 errors: 430 type: d time: 68.572  
geometries: 615232 errors: 333 type: d time: 64.119 (robust side)  
```  
  
So roughly 25% improvement. A few of these are added to the unit test suite.  
  
In terms of pictures, it's a big difference often - it cannot make the output if some turns are classified as "within". The orange/red dotted line is the resulting buffer.  
  
Before (the turn labelled as `7` is classified wrongly)  
<img width="630" alt="Screenshot 2025-05-04 at 12 52 15" src="https://github.com/user-attachments/assets/0df4e4d6-05df-4252-874c-fdba8bfa9b35" />  
  
After:  
<img width="620" alt="Screenshot 2025-05-04 at 12 52 45" src="https://github.com/user-attachments/assets/c629ef77-6a1d-4d77-a9d0-549bab1b8ddc" />

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-04 10:54:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2813430128  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 186 |+ 
 187 |+         if (skip_by_same_cluster(turn, piece))
 188 |+         {
```

> Username: barendgehrels  
> Created_at: 2025-05-04 10:54:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2072584574  

This is the fix


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-04 10:54:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2813430189  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 183 |         {
 143 |-             return false;
 184 |+             return true;
```

> Username: barendgehrels  
> Created_at: 2025-05-04 10:54:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2072584671  

This does not make any difference - but it should have been `true`

> Username: vissarion  
> Created_at: 2025-05-12 12:17:15 UTC  
> Updated_at: 2025-05-12 12:24:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2084545915  

Does this mean that this case was not covered by a unit test? Could we add one?

> Username: barendgehrels  
> Created_at: 2025-05-13 16:46:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087249152  

Good point. Apparently not. Those empty sides are rare.   
  
I'm not sure how to test it.  
  
What happens with `false` is that partition bails out and does not verify further cases.  
```  
            if (! visitor.apply(**it1, **it2))  
            {  
                return false; // Bail out if visitor returns false  
            }  
```  
  
It might be that this change fixes some issues for linestrings with flat-ends, in rare cases.  
  
Currently I'm not available to dive deeper into it - but I can make a follow-up ticket.

> Username: barendgehrels  
> Created_at: 2025-05-13 19:45:20 UTC  
> Updated_at: 2025-05-13 19:45:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087515982  

Added #1408 for this situation


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-04 10:59:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2813431326  

📁 test/algorithms/buffer/buffer_multi_polygon.cpp

```diff
 671 |+     TEST_BUFFER(rt_w38, join_miter, end_flat, 68.2279, 1.0);
 672 |+     TEST_BUFFER(rt_w39, join_miter, end_flat, 46.2279, 1.0);
 673 |+     TEST_BUFFER(rt_w40, join_miter, end_flat, 49.0490, 1.0);
```

> Username: barendgehrels  
> Created_at: 2025-05-04 10:59:23 UTC  
> Updated_at: 2025-05-04 10:59:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2072585820  

These new 5 all fail in `develop`


---

## Comment 4

> Username: barendgehrels  
> Created_at: 2025-05-04 14:37:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#issuecomment-2849254990  

What will be the follow-up...  
  
The statistics indicate that the `side_robust` is another big improvement and we can turn it on, at least for the `buffer` algorithm. That will increase another 25%.  
  
I just tested the buffer-unit tests and there are only two regressions, `w12` and `w20`. This apart from a lot of improvements which are not yet in the test-suite.  
  
During rewriting graph traversal, some cases were analyzed and postponed because increased the scope too much.  
  
These are unfinished - but marked as `BOOST_GEOMETRY_CONCEPT_FIX_ARRIVAL` and `BOOST_GEOMETRY_CONCEPT_FIX_START_TURNS`. They are slightly related.  
  
The `BOOST_GEOMETRY_CONCEPT_FIX_ARRIVAL` currently fixes both `w12` and `w20` so this looks good. Therefore we could already use that side strategy (temporary excluding the failing cases) and fix the arrival cases later. Or the other way round, first fix the arrival cases (which will be harder - I already started in February but did not succeed yet) and then enable the strategy.  
  
I think it's more pragmatic to enable first the robust side strategy - for buffer.

---

## Review 5 [Commented]

> Username: tinko92  
> Created_at: 2025-05-04 14:37:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2813476719  

The code looks good to me and catching something through the algorithm logic before predicate calls seems good.  
  
When running the full test suite on my ARM64 machine (`Linux magicbook 5.15.167.4-microsoft-standard-WSL2 #1 SMP Tue Nov 5 00:19:58 UTC 2024 aarch64 GNU/Linux`), I get 10 new test case failures in algorithms_buffer_linestring_geo that I do not get on develop:  
  
```  
[...]/test_buffer_geo.hpp(83): [...] aimes_67_rr has too many points too close 1 0.25  
[...]/test_buffer_geo.hpp(107): [...] aimes_67_rr the area is too small, expected at least 1.0038978240812866 15735.3694290676200406610455466882215  
[...]/test_buffer_geo.hpp(83): [...] aimes_67_rf has too many points too close 1 0.25  
[...]/test_buffer_geo.hpp(107): [...] aimes_67_rf the area is too small, expected at least 1.0038978240812866 13870.0487909986804213579532542697212  
[...]/test_buffer_geo.hpp(107): [...] aimes_67_mf the area is too small, expected at least 1.0038978240812866 13870.0487909986804213579532542697212  
[...]/test_buffer_geo.hpp(83): [...] aimes_163_rr has too many points too close 0 nan  
[...]/test_buffer_geo.hpp(107): [...] aimes_163_rr the area is too small, expected at least 0 6078.31722878943595932505319884877138  
[...]/test_buffer_geo.hpp(107): [...] aimes_181_rr the area is too small, expected at least 198.67884304160947 13456.4538307680239549585864667961117  
[...]/test_buffer_geo.hpp(83): [...] aimes_163_rr has too many points too close 0 nan  
[...]/test_buffer_geo.hpp(107): [...] aimes_163_rr the area is too small, expected at least 0 6078.31722878943595932505319884877138  
  
*** 10 failures are detected in the test module "Test Program"  
```  
  
If they don't occur for you maybe this is related to long double being float128 on my machine (the numbers in this output look like long doubles) and can be ignored and addressed later (since that also causes other errors on develop already on my machine).  
  
Edit: Changing side strategy does not change these 10 additional test failures for me.

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
  98 |+         for (auto const& index : it->second.turn_indices)
  99 |+         {
 100 |+             if (index == turn.turn_index)
```

> Username: tinko92  
> Created_at: 2025-05-04 14:04:28 UTC  
> Updated_at: 2025-05-04 14:37:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2072627033  

This produces a comparison of integers of different signedness (long int and size_t) warning in algorithms_buffer_linestring_aimes.test and other tests.

> Username: barendgehrels  
> Created_at: 2025-05-13 19:26:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087489933  

✅


---

## Comment 6

> Username: barendgehrels  
> Created_at: 2025-05-04 14:44:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#issuecomment-2849257900  

> When running the full test suite on my ARM64 machine, I get 10 new test case failures  
  
Thanks for testing! Indeed I don't get this. I've arm64 but not float128.   
`Darwin Kernel Version 23.6.0: Thu Mar  6 21:58:56 PST 2025; root:xnu-10063.141.1.704.6~1/RELEASE_ARM64_T6020 arm64`  
  
> if they don't occur for you maybe this is related to long double being float128 on my machine and can be ignored and addressed later  
  
Good you mention this. I tested the buffer algorithms but not yet the geo-ones. I will recheck.  
  
The aimes tests are fragile (some of them) - indeed we might ignore them.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2025-05-04 15:03:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#issuecomment-2849266250  

> Good you mention this. I tested the buffer algorithms but not yet the geo-ones. I will recheck.  
  
All fine. The only check failing is `boost_geometry_index_rtree_exceptions_rst` - which also fails for me on `develop`. It is not related - I would be happy if it could be fixed or skipped

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2025-05-04 15:49:08 UTC  
> Updated_at: 2025-05-04 15:49:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#issuecomment-2849286330  

> If they don't occur for you maybe this is related to long double being float128 on my machine  
  
The `long double` is not tested in the default test suite configuration...  
  
```  
#if ! defined(BOOST_GEOMETRY_TEST_ONLY_ONE_TYPE)  
    test_linestring<bg::strategy::andoyer, true, bg::model::point<long double, 2, bg::cs::geographic<bg::degree> > >();  
#endif  
```

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2025-05-12 12:24:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2833007151  

Thanks, I have only a few comments.

📁 test/algorithms/buffer/buffer_multi_polygon.cpp

```diff
 642 |-     TEST_BUFFER(rt_w22, join_miter, end_flat, 86.0416, 1.0);
 643 |- #endif    
 637 |+     TEST_BUFFER(rt_w22, join_miter, end_flat, 86.1274, 1.0);
```

> Username: vissarion  
> Created_at: 2025-05-12 12:20:09 UTC  
> Updated_at: 2025-05-12 12:24:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2084550927  

There is a change in area here. Did the expected result changed?

> Username: barendgehrels  
> Created_at: 2025-05-13 19:37:23 UTC  
> Updated_at: 2025-05-13 19:38:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087504196  

👍  Yes, the earlier area was wrong.  
  
This is the new result:  
  
![image](https://github.com/user-attachments/assets/33342bf7-3c84-41ac-b961-c9c52d58cc23)  
  
  
While earlier (however, not in `develop` but probably in one of my branches) it did not include one triangle, so it went via `58/69`:  
  
![image](https://github.com/user-attachments/assets/87309708-34b3-40b4-9885-58ca916a3870)


📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
  85 |+     inline bool skip_by_same_cluster(Turn const& turn, Piece const& piece) const
  86 |+     {
  87 |+         if (turn.cluster_id <= 0)
```

> Username: vissarion  
> Created_at: 2025-05-12 12:24:12 UTC  
> Updated_at: 2025-05-12 12:24:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2084560524  

what does a negative cluster_id means and why we are not searching for it? It is the case that m_clusters does not contain negative values so this condition speeds up the search?

> Username: barendgehrels  
> Created_at: 2025-05-13 19:39:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087506946  

👍 I removed it, the condition was indeed redundant and wrong. Thanks.


---

## Comment 10

> Username: barendgehrels  
> Created_at: 2025-05-13 19:59:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#issuecomment-2877798894  

> When running the full test suite on my ARM64 machine (`Linux magicbook 5.15.167.4-microsoft-standard-WSL2 #1 SMP Tue Nov 5 00:19:58 UTC 2024 aarch64 GNU/Linux`), I get 10 new test case failures in algorithms_buffer_linestring_geo that I do not get on develop:  
>   
  
They didn't run earlier with cmake. They didn't fail for me, but I added them to the failing cases set. It was probably for `double` as well.  
  
Thanks for the report.

---

## Review 11 [Commented]

> Username: barendgehrels  
> Created_at: 2025-05-13 20:01:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1404#pullrequestreview-2837978271  

📁 test/algorithms/buffer/buffer_linestring_geo.cpp

```diff
 121 |+     std::set<int> const skip_cases_round_round{17, 22, 38, 67, 75, 109, 163, 181, 143, 196};
 122 |+     std::set<int> const skip_cases_round_flat{17, 22, 38, 67, 75, 103, 109, 196};
 123 |+     std::set<int> const skip_cases_miter_flat{17, 18, 22, 38, 67, 75, 103, 109, 196};
```

> Username: barendgehrels  
> Created_at: 2025-05-13 20:01:28 UTC  
> Updated_at: 2025-05-13 20:01:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1404#discussion_r2087540174  

A few cases were added - not (necessarily) by this PR but by the fact that earlier I tested in release mode, and now in debug mode. I also added `67` and `163` reported by @tinko92


---
