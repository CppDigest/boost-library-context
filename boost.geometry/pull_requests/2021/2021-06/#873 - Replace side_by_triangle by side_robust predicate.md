# #873 Replace side_by_triangle by side_robust predicate [Merged]

> Username: vissarion  
> Created at: 2021-06-30 10:45:25 UTC  
> Updated at: 2022-11-16 14:12:13 UTC  
> Merged at: 2021-09-21 11:26:26 UTC  
> Closed at: 2021-09-21 11:26:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/873  



---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 10:04:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697060892  

📁 include/boost/geometry/strategies/cartesian/side_non_robust.hpp

```diff
  10 |- #ifndef BOOST_GEOMETRY_STRATEGY_CARTESIAN_SIDE_NON_ROBUST_HPP
  11 |- #define BOOST_GEOMETRY_STRATEGY_CARTESIAN_SIDE_NON_ROBUST_HPP
  10 |+ #ifndef BOOST_GEOMETRY_STRATEGIES_CARTESIAN_SIDE_NON_ROBUST_HPP
```

> Username: awulkiew  
> Created_at: 2021-07-01 10:04:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662158490  

Some time ago we agreed to move legacy strategies to `strategy/` directory. This is why you or Tinco created these strategies here.  
  
This strategy should stay here and instead e.g. `side_by_triangle` should be moved.  
  
We could do this for all strategies at once or move systematically the strategies that we modify. In case you prefered to do this now the original file should containg deprecation warning. See e.g. area strategies in both directories.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 10:20:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697075088  

📁 include/boost/geometry/strategies/cartesian/side_robust.hpp

```diff
 103 |+         vec2d pa;
 104 |+         pa.x = get<0>(p1);
 105 |+         pa.y = get<1>(p1);
```

> Username: awulkiew  
> Created_at: 2021-07-01 10:20:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662168449  

Right this was failing to compile before. I was working on a PR fixing that. There are several possibilities how this could be fixed:  
1. assigning values after creation like you did  
2. defining a constructor/constructors in `vec2d` and replacing '{}' with '()'  
3. using `numeric_cast<PromotedType>(...)`  
In my PR I decided to use `numeric_cast` because this also silences conversion warnings with msvc but this may not be desireable. Because maybe there should be warnings. On the other hand these warnings are only generated with msvc (or maybe it depends on compiler options?). Furthermore these warnings are generated in other strategies too so maybe this is a feature. ;)  
  
So anyway, I'm ok with this solution but would like to know your opinion why do you think it's correct?

> Username: vissarion  
> Created_at: 2021-07-01 14:12:57 UTC  
> Updated_at: 2021-07-01 14:12:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662327003  

I selected (1) since I found it simpler than (2). Also compared to (3) it will not silence conversion warning that could be useful since we do not test this piece of code with many types (maybe we should) apart from `double` and `long long int` AFAIR. Also (3) is dependent on other `boost` libraries which of course is not a problem but why not an independent solution?


---

## Comment 3

> Username: awulkiew  
> Created_at: 2021-07-01 10:32:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-872123729  

Do you think this should be released in 1.77 or wait for 1.78?  
This PR is not big but it may potentially affect many algorithms: all relops, all setops, buffer, convex_hull. Should this be considered as a major change?  
  
On the other hand it fixes a bug preventing convex_hull strategy to compile and improves turns calculation so this could be treated as a bugfix.  
  
The safest option would be to create a separate PR fixing the bug in `side_robust` and wait for 1.78 with the rest. But I don't have a strong opinion about this. What do you think?

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 10:43:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697093613  

📁 include/boost/geometry/util/precise_math.hpp

```diff
 375 |+     auto const sy1 = p1.y;
 376 |+     auto const sx2 = p2.x;
 377 |+     auto const sy2 = p2.y;
```

> Username: awulkiew  
> Created_at: 2021-07-01 10:43:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662181310  

This was done to improve readability correct?  
If that's the case and `p1`, `p2` and `p3` have some specific meaning then maybe it's these variables that should be renamed?

> Username: awulkiew  
> Created_at: 2021-07-01 10:51:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662185653  

I don't understand why aren't they used below too. E.g. below there is `t1[0] = p1.x - p3.x;` which would translate to `t1[0] = sx1 - x;` or `t1[0] = -dpx;`. Does these make sense? If they doesn't it means that the names are incorrect.

> Username: vissarion  
> Created_at: 2021-07-02 13:38:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r663020300  

I followed the naming of `side_by_triangle` but that complicated things. This can be simplified. Actually the `eps_policy` can be created/updated using `t1,...,t4`. See https://github.com/boostorg/geometry/pull/873/commits/e3eb4bae2cbac767ca1ff106dcbce504fcd0771c


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 10:54:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697102495  

📁 include/boost/geometry/strategies/cartesian/side_robust.hpp

```diff
  37 |+ 
  38 |+     template <typename Policy, typename T1, typename T2>
  39 |+     static auto apply(T1 a, T2 b, Policy policy)
```

> Username: awulkiew  
> Created_at: 2021-07-01 10:54:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662187368  

Ok this is confusing. A policy is taking a policy and below...


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 10:56:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697104142  

📁 include/boost/geometry/strategies/cartesian/side_robust.hpp

```diff
  79 |+         {}
  80 |+         Policy policy;
  81 |+     };
```

> Username: awulkiew  
> Created_at: 2021-07-01 10:56:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662188424  

...another policy storing a Policy is defined.

> Username: vissarion  
> Created_at: 2021-07-02 15:05:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r663079291  

This is based on similar policy in `side_by_triangle` but here I wanted to add two more "equal" policies. I changed the code a bit https://github.com/boostorg/geometry/pull/873/commits/c1f8ab10de002891c34561ff3e756f996171df04


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 11:00:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697107111  

📁 include/boost/geometry/util/precise_math.hpp

```diff
 358 |-                            vec2d<RealNumber> const& p3)
 368 |+                            vec2d<RealNumber> const& p3,
 369 |+                            EpsPolicy& eps_policy)
```

> Username: awulkiew  
> Created_at: 2021-07-01 11:00:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662190482  

Do I understand correctly that `eps_policy` is output argument. And it's a policy defined internally in `side_robust`? Shouldn't this policy be defined somewhere in this file instead?

> Username: vissarion  
> Created_at: 2021-09-07 11:03:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r703409620  

It is created in `side_robust` and passed to `orient2d` that is why is defined there.


---

## Comment 8

> Username: vissarion  
> Created_at: 2021-07-01 14:14:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-872284480  

> Do you think this should be released in 1.77 or wait for 1.78?  
> This PR is not big but it may potentially affect many algorithms: all relops, all setops, buffer, convex_hull. Should this be considered as a major change?  
>   
> On the other hand it fixes a bug preventing convex_hull strategy to compile and improves turns calculation so this could be treated as a bugfix.  
>   
> The safest option would be to create a separate PR fixing the bug in `side_robust` and wait for 1.78 with the rest. But I don't have a strong opinion about this. What do you think?  
  
I agree with the last. New PR for the "narrowing type bug" and this PR for 1.78.

---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2021-07-01 20:37:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/873#pullrequestreview-697635988  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
  68 |     <
  69 |-         T, Geometry, strategy::side::side_by_triangle<CT>, CSTag
  69 |+         T, Geometry, strategy::side::side_robust<CT>, CSTag
```

> Username: awulkiew  
> Created_at: 2021-07-01 20:37:48 UTC  
> Updated_at: 2021-07-03 10:14:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r662577507  

All cartesian strategies should probably be suppoted in `collected_vector`.

> Username: vissarion  
> Created_at: 2021-09-07 11:04:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#discussion_r703410179  

I created a specialization for `side_by_triangle` for backwards compatibility.


---

## Comment 10

> Username: barendgehrels  
> Created_at: 2021-07-03 10:12:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-873382442  

> I agree with the last. New PR for the "narrowing type bug" and this PR for 1.78.  
  
I agree too, this should not yet be part of 1.77, it is a major change.

---

## Comment 11

> Username: barendgehrels  
> Created_at: 2021-07-08 16:27:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-876578248  

Is the performance changed?

---

## Comment 12

> Username: vissarion  
> Created_at: 2021-07-13 13:11:26 UTC  
> Updated_at: 2021-07-13 13:12:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-879075165  

There are two sets of benchmarks:  
  
1. `star_comp` and `recursive_polygons` from `test/robustness/overlay/areal_areal/`  
  
`./star_comp --count 10 --point_count 100`   
- robust, gcc9, time: 3.952  
- side_by_triangle, gcc9, time: 4.209  
- robust, clang10, time: 3.269  
- side_by_triangle, clang10, time: 3.169  
  
`./recursive_polygons  --count 10000 `  
- robust, gcc9, time: 6.636  
- side_by_triangle, gcc9, time: 6.805  
- robust, clang10, time: 6.736  
- side_by_triangle, clang10, time: 6.524  
  
`./star_comp_robust_release_gcc9 --count 10 --point_count 200`   
robust gcc9 time: 161.425  
side_by_triangle gcc9 time: 168.443  
  
Here, the performance of side predicate is dominated by other computations (e.g. turns computations).  
  
2. synthetic data-sets generated with https://gist.github.com/vissarion/8180c7e7b3f9bf952e785edadde1844c (based on a benchmark by @tinko92)   
  
|                     | Continuous random: | | Random exponent: | | Perturbed grid: | | Purposefully degenerate values: | |  
| ------------------- | ------------------ | ---------------- | --------------- | ------------------------------- | -------- | ------ | -------- | --------- |  
|                     | time(ms)           | sum              | time(ms)        | sum                             | time(ms) | sum    | time(ms) | sum       |  
| **g++**               |                    |                  |                 |                                 |          |        |          |           |  
| Non\_robust 1:      | 65                 | \-1968           | 65              | \-1312                          | 65       | \-4366 | 36       | 3333334   |  
| Non\_robust 2:      | 34                 | \-1968           | 35              | \-1312                          | 34       | \-4366 | 36       | 3333334   |  
| Side\_by\_triangle: | 190                | \-1968           | 272             | \-1952                          | 196      | \-4192 | 127      | 3333334   |  
| Robust\_eps:        | 53                 | \-1968           | 75              | \-1806                          | 55       | \-4222 | 690      | 0         |  
| Adaptive\_0\_eps:   | 51                 | \-1968           | 59              | \-1731                          | 49       | \-4168 | 46       | 10000000  |  
| Adaptive\_1\_eps:   | 59                 | \-1968           | 71              | \-1708                          | 56       | \-4243 | 94       | 0         |  
| Adaptive\_2\_eps:   | 61                 | \-1968           | 75              | \-1806                          | 59       | \-4222 | 142      | \-3333333 |  
| Robust\_fp:         | 64                 | \-1968           | 75              | \-1806                          | 60       | \-4222 | 686      | 0         |  
| Adaptive\_0\_fp:    | 39                 | \-1968           | 37              | \-1912                          | 38       | \-4139 | 36       | 10000000  |  
| Adaptive\_1\_fp:    | 51                 | \-1968           | 54              | \-1900                          | 48       | \-4234 | 88       | 0         |  
| Adaptive\_2\_fp:    | 53                 | \-1968           | 57              | \-2034                          | 50       | \-4224 | 135      | \-3333333 |  
|                     |                    |                  |                 |                                 |          |        |          |           |  
| **clang++**            |                    |                  |                 |                                 |          |        |          |           |  
| Non\_robust 1:      | 33                 | 1482             | 34              | 1465                            | 35       | \-378  | 36       | \-3333333 |  
| Non\_robust 2:      | 34                 | 1482             | 33              | 1465                            | 35       | \-378  | 35       | \-3333333 |  
| Side\_by\_triangle: | 193                | 1482             | 232             | 509                             | 199      | \-343  | 138      | \-3333333 |  
| Robust\_eps:        | 210                | 1482             | 236             | 1143                            | 212      | \-276  | 983      | 0         |  
| Adaptive\_0\_eps:   | 41                 | 1482             | 41              | 1271                            | 41       | \-262  | 43       | 0         |  
| Adaptive\_1\_eps:   | 212                | 1482             | 228             | 1248                            | 212      | \-278  | 260      | 0         |  
| Adaptive\_2\_eps:   | 227                | 1482             | 235             | 1143                            | 226      | \-276  | 297      | \-3333333 |  
| Robust\_fp:         | 211                | 1482             | 238             | 1143                            | 211      | \-276  | 973      | 0         |  
| Adaptive\_0\_fp:    | 35                 | 1482             | 36              | 2195                            | 35       | \-257  | 35       | 0         |  
| Adaptive\_1\_fp:    | 197                | 1482             | 203             | 2150                            | 197      | \-273  | 272      | 0         |  
| Adaptive\_2\_fp:    | 209                | 1482             | 216             | 1530                            | 210      | \-296  | 292      | \-3333333 |  
  
where:  
- _eps means using [this policy](https://github.com/boostorg/geometry/pull/873/files#diff-fc187ebce47d2e8ed80562f60ef0adc96392bd142970571dd6f86caa60855a9aR35)  
- _fp means using [this policy](https://github.com/boostorg/geometry/pull/873/files#diff-fc187ebce47d2e8ed80562f60ef0adc96392bd142970571dd6f86caa60855a9aR45)  
- non\_robust 1: defined [here](https://github.com/boostorg/geometry/pull/873/files#diff-bf0d7320d352ee9f2d071fbc3fe281975d3dd1994d0199913175624ae1600d71R69)  
- non\_robust 2: defined [here](https://github.com/boostorg/geometry/pull/873/files#diff-bf0d7320d352ee9f2d071fbc3fe281975d3dd1994d0199913175624ae1600d71R77)  
- Adaptive 0 or 1 or 2: we get them by setting the `Robusteness` parameter [here](https://github.com/boostorg/geometry/pull/873/files#diff-ffdcbda2344342f3dc25c5cc8b7ca7b3cf5665fe8d56a387b041f936feb80210R363);   
- robust: `Robusteness` is set to the default value `3`    
- sum is the total sum of all determinants computed, an indication on the correctness of the results  
  
Notes:  
- robust predicate is (up to 4x) faster from side_by_triangle when compiling with g++ and equally performant with clang expect from the highly degenerate cases where it is slower  (up to 7x)  
- assuming that robust predicate returns the correct answer always (see also https://github.com/boostorg/geometry/issues/699) the non robust and side_by_triangle predicates return all results correct only in the case of "continuous random" points.     
- in the generic cases (3 first columns) the robust predicate is very close to the performance of the (fastest) floating point non-robust predicate and always correct  
-  Adaptive 0 or 1 or 2 could be useful only in very special use cases  
- different compilers can affect performance significantly e.g.1 gcc can create a 2x slower non robust predicate (non robust 1), e.g.2 clang  generates a 4x slower robust predicate  
- we get similar results for different versions of a given compiler such as  9 and 10 for clang and 8,9,10 for g++

---

## Comment 13

> Username: barendgehrels  
> Created_at: 2021-07-13 17:48:51 UTC  
> Updated_at: 2021-07-13 17:50:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-879281974  

> There are two sets of benchmarks:  
>   
  
Thank you very much! I had seen it, or an earlier version, long ago but had forgotten it. This looks very good to me.

---

## Comment 14

> Username: vissarion  
> Created_at: 2021-09-07 11:08:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/873#issuecomment-914212345  

@awulkiew are you OK with merging this ?

---
