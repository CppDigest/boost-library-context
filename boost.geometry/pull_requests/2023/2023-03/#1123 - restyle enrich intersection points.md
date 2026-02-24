# #1123 restyle enrich intersection points [Merged]

> Username: barendgehrels  
> Created at: 2023-03-15 13:43:37 UTC  
> Updated at: 2023-03-22 10:37:24 UTC  
> Merged at: 2023-03-22 10:29:59 UTC  
> Closed at: 2023-03-22 10:29:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-15 13:44:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1341534999  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 469 |                     geometry1, geometry2,
 525 |-                     robust_policy, strategy.side()); // TODO: pass strategy
 470 |+                     robust_policy, strategy);
```

> Username: barendgehrels  
> Created_at: 2023-03-15 13:44:08 UTC  
> Updated_at: 2023-03-15 13:44:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1137096116  

:heavy_check_mark: done


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-15 13:44:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1341536715  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 304 |-          it != boost::end(turns);
 305 |-          ++it, ++index)
 276 |+     for_each_with_index(turns, [&](std::size_t index, auto const& turn)
```

> Username: barendgehrels  
> Created_at: 2023-03-15 13:44:55 UTC  
> Updated_at: 2023-03-15 13:44:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1137097555  

This makes the code a lot shorter and more readable.  
Better view it side-by-side.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2023-03-17 13:51:11 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1345982198  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
  95 |-             boost::end(operations),
  95 |+     std::sort(std::begin(operations),
  96 |+             std::end(operations),
```

> Username: vissarion  
> Created_at: 2023-03-17 13:45:52 UTC  
> Updated_at: 2023-03-17 13:51:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1140258776  

could you please also fix indentation?

---

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 113 |-     typedef typename turn_type::turn_operation_type op_type;
 114 |-     typedef typename boost::range_iterator<Operations>::type iterator_type;
 113 |+     if (operations.empty())
```

> Username: vissarion  
> Created_at: 2023-03-17 13:46:21 UTC  
> Updated_at: 2023-03-17 13:51:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1140259361  

could this be a one-liner?

> Username: barendgehrels  
> Created_at: 2023-03-22 09:36:24 UTC  
> Updated_at: 2023-03-22 09:36:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1144486047  

I prefer to keep it like this.


---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-22 09:48:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1352121780  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 362 |-         turn_type& turn = *it;
 305 |+         using ctype = typename geometry::coordinate_type<decltype(turn.point)>::type;
 306 |+         auto distance_measure = [](auto const& a, auto const& b) -> ctype
```

> Username: barendgehrels  
> Created_at: 2023-03-22 09:48:04 UTC  
> Updated_at: 2023-03-22 09:48:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1144504830  

This is not handled correctly by gcc up to version 7  
For that reason I will undo this change to lambda


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-22 10:15:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1352194237  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 221 |-     std::size_t const x = operations.size() - 1;
 206 |     bool next_phase = false;
 207 |+     std::size_t previous_index = operations.size() - 1;
```

> Username: barendgehrels  
> Created_at: 2023-03-22 10:15:51 UTC  
> Updated_at: 2023-03-22 10:15:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1144548653  

Earlier it was slightly different, leading to a reproducable gcc-5 compilation error.  
```  
./boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp:206:54: internal compiler error: in tsubst_copy, at cp/pt.c:13040  
     std::size_t const last_index = operations.size() - 1;  
```  
  
This now works for all compilers AFAICS


---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2023-03-22 10:29:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1123#pullrequestreview-1352223903  

📁 test/algorithms/set_operations/union/union_other_types.cpp

```diff
  76 | template <typename Point>
  77 |- void test_areal(std::set<exclude> const& exclude = {}, bool debug = false)
  77 |+ void test_areal(std::set<exclude> const& exclude_set = {}, bool debug = false)
```

> Username: barendgehrels  
> Created_at: 2023-03-22 10:29:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#discussion_r1144569721  

(this was neither accepted by gcc-5, but it was weird anyway that the name of the type was the same as the name of the parameter, my bad)


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2023-03-22 10:29:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1123#issuecomment-1479296858  

Now all is green, merging.

---
