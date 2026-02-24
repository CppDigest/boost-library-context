# #1149 [test] change boost random to std random in robustness tests [Merged]

> Username: barendgehrels  
> Created at: 2023-05-21 11:42:54 UTC  
> Updated at: 2023-06-16 14:02:50 UTC  
> Merged at: 2023-06-10 11:56:27 UTC  
> Closed at: 2023-06-10 11:56:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-05-21 11:45:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1149#pullrequestreview-1435523132  

📁 test/robustness/overlay/linear_areal/recursive_polygons_linear_areal.cpp

```diff
 222 |+         bg::detail::segment_as_subrange<segment_t> subrange2(seg2);
 223 |+         bg::segment_intersection_points<point_t> is 
 224 |+             = strategy.apply(subrange1, subrange2, policy_t());
```

> Username: barendgehrels  
> Created_at: 2023-05-21 11:45:19 UTC  
> Updated_at: 2023-05-21 11:45:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1199750743  

Additionally, it repairs this broken test.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2023-05-21 11:45:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1149#pullrequestreview-1435523185  

📁 test/robustness/overlay/buffer/multi_point_growth.cpp

```diff
  75 | 
  76 |-     std::vector<GeometryOut> buffered;
  76 |+     bg::model::multi_polygon<GeometryOut> buffered;
```

> Username: barendgehrels  
> Created_at: 2023-05-21 11:45:46 UTC  
> Updated_at: 2023-05-21 11:45:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1199750793  

This repairs this test.


---

## Review 3 [Commented]

> Username: barendgehrels  
> Created_at: 2023-05-21 11:46:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1149#pullrequestreview-1435523291  

📁 test/robustness/convex_hull/random_multi_points.cpp

```diff
  18 |- #include <boost/random/uniform_real.hpp>
  19 |- #include <boost/random/variate_generator.hpp>
  20 |- #include <boost/timer.hpp>
```

> Username: barendgehrels  
> Created_at: 2023-05-21 11:46:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1199750929  

It also changes `boost::timer` to `std::chrono` in the touched files.


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2023-05-22 13:42:13 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1149#pullrequestreview-1436664663  

Thanks! LGTM.

📁 test/robustness/convex_hull/random_multi_points.cpp

```diff
 174 |             ("help", "Help message")
 175 |             ("seed", po::value<int>(&seed), "Initialization seed for random generator")
 181 |-             ("count", po::value<int>(&count)->default_value(1), "Number of tests")
```

> Username: vissarion  
> Created_at: 2023-05-22 13:41:33 UTC  
> Updated_at: 2023-05-22 13:42:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1200536282  

Is this not needed anymore? Seems unrelated to boost random replacement.

> Username: barendgehrels  
> Created_at: 2023-05-22 19:55:28 UTC  
> Updated_at: 2023-05-22 19:55:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1200982723  

hmm, thanks! I have to check this indeed. Looks like an unwanted omission.

> Username: barendgehrels  
> Created_at: 2023-06-10 11:55:01 UTC  
> Updated_at: 2023-06-10 11:55:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/1149#discussion_r1225314958  

It's fine. Indeed not really unrelated to Boost.Random - but sidely because I changed some of these in touched code.  
The `->default_value` is not necessary if the variable is already initialized like that. And in this case (and many more), it is.


---

## Review 5 [Approved]

> Username: awulkiew  
> Created_at: 2023-05-25 08:40:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1149#pullrequestreview-1443380276  

Thanks!

---
