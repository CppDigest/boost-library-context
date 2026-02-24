# #314 [get_turns][strategies] Optimize get_turns(). [Merged]

> Username: awulkiew  
> Created at: 2015-07-03 19:47:50 UTC  
> Updated at: 2015-07-11 01:25:49 UTC  
> Merged at: 2015-07-09 11:21:47 UTC  
> Closed at: 2015-07-09 11:21:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/314  

The optimization is based on the fact that in the most cases the segments  
handled in the `TurnInfoPolicy` are disjoint. For disjoint segments first  
the points are rescaled, then the test for disjoint is run, next the  
`TurnInfoPolicy` just returns and the next pair of segments is handled.  
Therefore to optimze the `get_turns()` this commit changes two things:  
1. the `cart_intersect` strategy may return just after the calculation of sides  
   for the first segment (2 sides calculation instead of 4),  
2. the points are rescaled only one time in the intersection_helper,  
   already rescaled points are passed into the intersection strategy  
  
I tested it using the MultiPoint `buffer()`. For only some fraction of the tests (all basic tests and 2 first tests from the `test_many_points_per_circle`, because I was initially testing it like this to not wait too much) the stats of the segments relations are:  
  
```  
disjoint segments: 15260208  
crossing segments: 1857  
collinear segments: 670  
```  
  
so you can see how important is it to have an optimized version of the disjoint segments handling, performing as small number of operations as possible for them. Though have in mind that those test cases are very specific and typically the number of disjoint segments should be smaller in relation to the other ones.  
  
I tested the performance with MSVC10 release O2, NDEBUG. The "release" MultiPoint buffer test (so with `test_many_points_per_circle` but without `BOOST_GEOMETRY_BUFFER_INCLUDE_SLOW_TESTS`) took 88 sec. After applying the optimization 1 the time decreased to 80 sec., after applying the optimization 2 it decreased further to 55 sec. It seems that the rescaling adds the major factor for disjoint segments.  
  
I see one more optimization possibility. Currently all 6 points are always rescaled before the segments disjoint test. But for this test only 4 points are needed. So we could even further optimize it by postponing the rescaling of `pk` and `qk`.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-07-08 15:07:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/314#issuecomment-119617320  

Thanks!  
I'm OK with merging

---
