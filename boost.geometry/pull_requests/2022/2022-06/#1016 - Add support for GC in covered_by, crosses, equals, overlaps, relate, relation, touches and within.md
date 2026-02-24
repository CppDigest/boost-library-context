# #1016 Add support for GC in covered_by, crosses, equals, overlaps, relate, relation, touches and within [Merged]

> Username: awulkiew  
> Created at: 2022-06-14 16:38:43 UTC  
> Updated at: 2022-06-29 20:43:33 UTC  
> Merged at: 2022-06-29 20:43:33 UTC  
> Closed at: 2022-06-29 20:43:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016  

All of these algorithms use `relate`.  
  
The `relate` algorithm merges parts of geometry collections with set operations and calculates partial results.  
  
Some of the GC utilities are moved, their names changed to use the same pattern, i.e. `detail::gc_xxx`. There are also some fixes and small refactoring.  
  
I've also found a case of `relate` L/L which result is wrong but it's unrelated to this PR.  
  
This PR is built on top of https://github.com/boostorg/geometry/pull/1011 so I think we should merge the other one before reviewing this one.  
  
----  
  
For future reference below is a set of combinations of Areal and Linear parts of GCs that I used while developing the algorithm. The combinations of these parts are tricky to handle because they may touch and therefore can have common intersection points. Unlike PointLike part which is filtered-out by merging.  
![rect184](https://user-images.githubusercontent.com/1226951/173628824-835b76ac-a60c-4690-b68a-d7f71aa250c3.png)  
For X/Y, X is part of GC1 (red), Y is part of GC2 (green).  
A is Areal, L is Linear, 0 is empty geometry.  
Parts here are after merging so Linear part is always outside of the Areal part of the same GC.  
Li is the intersection of Linear part of a GC and the Areal part of the other GC, so part of Linear that is inside other Areal.  
Ld is the difference of Linear part of a GC and Areal part of the other GC, so part of Linear that is outside of other Areal.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-06-24 10:59:45 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1016#pullrequestreview-1018310207  

I skipped the setops and review the rest of the code that looks OK. Thanks Adam!

📁 test/algorithms/covered_by/test_covered_by.hpp

```diff
  78 | 
  79 |-     typedef typename bg::strategy::covered_by::services::default_strategy
  79 |+     typedef typename bg::strategies::relate::services::default_strategy
```

> Username: vissarion  
> Created_at: 2022-06-24 10:47:44 UTC  
> Updated_at: 2022-06-24 10:59:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r905948226  

since you changed it maybe use `using` ?

> Username: awulkiew  
> Created_at: 2022-06-24 23:21:42 UTC  
> Updated_at: 2022-06-24 23:21:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r906514508  

Done


📁 test/algorithms/relate/test_relate.hpp

```diff
 122 | 
 123 |-         typedef typename bg::strategy::relate::services::default_strategy
 123 |+         typedef typename bg::strategies::relate::services::default_strategy
```

> Username: vissarion  
> Created_at: 2022-06-24 10:48:35 UTC  
> Updated_at: 2022-06-24 10:59:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r905948776  

as in overlaps, do it with `using`?

> Username: awulkiew  
> Created_at: 2022-06-24 23:21:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r906514519  

Done


---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2022-06-24 11:11:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1016#pullrequestreview-1018332001  

📁 include/boost/geometry/algorithms/detail/gc_group_elements.hpp

```diff
 156 |+             }
 157 |+         }
 158 |+         if (! group.empty())
```

> Username: vissarion  
> Created_at: 2022-06-24 11:11:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r905963468  

or  
```  
if (! group.empty() && ! intersecting_fun(group) ) return;  
```

> Username: awulkiew  
> Created_at: 2022-06-24 23:21:32 UTC  
> Updated_at: 2022-06-24 23:21:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r906514482  

Yes, this would be shorter but IDK really that it'd be better. The original makes it more clear that this is a call of a function passed by the user and not another condition check. It is also more similar to the second call of the other function below. @barendgehrels what's your opinion?


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2022-06-29 10:23:04 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1016#pullrequestreview-1023084253  

Looks like great work!  
I reviewed some of it and it looks good to me, and things are consistent and looks the same way in different algorithms.  
Thanks!

📁 include/boost/geometry/core/topological_dimension.hpp

```diff
  80 | 
  81 |+ template <>
  82 |+ struct top_dim<geometry_collection_tag> : std::integral_constant<int, -1> {};
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:08:06 UTC  
> Updated_at: 2022-06-29 10:23:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r909449098  

Okay for me.


📁 include/boost/geometry/algorithms/crosses.hpp

```diff
  70 |+     {
  71 |+         int dimension1 = detail::gc_topological_dimension(geometry1);
  72 |+         int dimension2 = detail::gc_topological_dimension(geometry2);
```

> Username: barendgehrels  
> Created_at: 2022-06-29 10:21:44 UTC  
> Updated_at: 2022-06-29 10:23:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r909460705  

Minor: can it be const, or even static maybe?

> Username: awulkiew  
> Created_at: 2022-06-29 20:31:27 UTC  
> Updated_at: 2022-06-29 20:31:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1016#discussion_r910387662  

`const` yes, `static` no because the dimension depends on what is actually stored in a GC.


---
