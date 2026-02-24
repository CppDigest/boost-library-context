# #1368 fix: pass umbrella strategies in rtree spatial predicates [Merged]

> Username: awulkiew  
> Created at: 2025-01-16 20:45:45 UTC  
> Updated at: 2025-03-18 13:20:18 UTC  
> Merged at: 2025-01-20 21:58:19 UTC  
> Closed at: 2025-01-20 21:58:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:41:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2558083447  

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
  30 |+                                && bg::util::is_box<typename Rtree::value_type>::value
  31 |+                             || bg::util::is_box<QueryValue>::value
  32 |+                                && bg::util::is_point<typename Rtree::value_type>::value;
```

> Username: barendgehrels  
> Created_at: 2025-01-17 06:41:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1919633795  

Everywhere I work it is required to put brackets, there were `&&` and `||` are combined. Also compilers normally warn for it. Can we do it?  
Though the layout is beautiful here, so it's not unclear to me.  
And it's test code.  
  
Nit, bp or bb (but the order is: bb or bp)

> Username: awulkiew  
> Created_at: 2025-01-17 22:46:15 UTC  
> Updated_at: 2025-01-17 22:46:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920842057  

Sure, done.


---

## Review 2 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:42:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2558084549  

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
  10 |+ #include <vector>
  11 |+ 
  12 |+ #define BOOST_GEOMETRY_INDEX_DETAIL_EXPERIMENTAL_PREDICATES
```

> Username: barendgehrels  
> Created_at: 2025-01-17 06:42:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1919634485  

Is it still experimental? When can we promote it to "general"?

> Username: awulkiew  
> Created_at: 2025-01-17 21:51:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920804684  

Here it's only for `touches` and only to test if it compiles. I can't remember exactly but I think it's experimental because it makes little sense to calculate whether or not bounding boxes touch. It could be removed I guess.  
  
There is also experimental path predicate, aka distance along linestring/segment, aka ray query which could be useful but it probably requires more thinking.

> Username: barendgehrels  
> Created_at: 2025-01-18 20:25:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1921145571  

OK - I understand - but you'll probably have the best understanding of this code.  
  
If it makes no sense - we should remove it?

> Username: awulkiew  
> Created_at: 2025-01-19 13:24:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1921553382  

Now that I think about it. The R-tree also works for segments so maybe `touches(segment, segment)` could be useful? But this should be done in a different PR anyway.


---

## Review 3 [Approved]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:43:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2558085608  

👍

---

## Review 4 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:43:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2558086102  

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
 144 |+     test_params<Value, bgi::linear<4>>();
 145 |+     test_params<Value, bgi::quadratic<4>>();
 146 |+     test_params<Value, bgi::rstar<4>>();
```

> Username: barendgehrels  
> Created_at: 2025-01-17 06:43:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1919635719  

Did we document somewhere what is the adviced option? `rstar` ?

> Username: awulkiew  
> Created_at: 2025-01-17 21:59:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920810858  

It depends on the data and algorithm using the rtree. It's described here: https://www.boost.org/doc/libs/1_87_0/libs/geometry/doc/html/geometry/spatial_indexes/introduction.html#geometry.spatial_indexes.introduction.r_tree  
  
With that said I'd probably use quadratic balancing algorithm by default and packing if possible.


---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2025-01-17 06:44:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2558086761  

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
 132 |+ {
 133 |+     test_strategies<Value, Params, bg::strategies::index::cartesian<>>();
 134 |+     test_strategies<Value, Params, bg::strategies::cartesian<>>();
```

> Username: barendgehrels  
> Created_at: 2025-01-17 06:44:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1919636089  

What is the difference actually? Is it another umbrella?

> Username: awulkiew  
> Created_at: 2025-01-17 22:01:03 UTC  
> Updated_at: 2025-01-17 22:01:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920811824  

Yes, just like various algorithms has their own strategies index has it as well. And in addition to that it should work with the highest level one which can be passed anywhere.


---

## Review 6 [Approved]

> Username: vissarion  
> Created_at: 2025-01-17 14:07:36 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1368#pullrequestreview-2559059791  

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
  72 |+ {
  73 |+     // These predicates use algorithms that are not implemented for
  74 |+     // some geometry combinations
```

> Username: vissarion  
> Created_at: 2025-01-17 13:47:34 UTC  
> Updated_at: 2025-01-17 14:07:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920217623  

Is it useful/simple to list here those combinations? For all CSs?

> Username: awulkiew  
> Created_at: 2025-01-17 22:42:47 UTC  
> Updated_at: 2025-01-17 22:42:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920840161  

Yes, if we want to implement missing combinations. But I'm not sure if this is the correct place for this.  
  
The combinations that are not implemented are related mostly to `within`/`covered_by` when:  
- higher topo-dim geometry is checked to be inside lower topo-dim geometry (e.g. `box` in `point`)  
- segment is any of the arguments (it seems that the compiling combination is `point` in `segment`)  
  
`overlaps` is defined only for combinations of the same topo-dim and is not implemented for segments  
  
`touches` is not implemented for segments, point/box, surprisingly it compiles for point/point which should always return false  
  
So technically more combinations could be checked than are enabled now but I think it's enough for a test of rtree umbrella strategies.  
  
Btw, in index/detail/predicates.hpp there is a table describing which algorithms are called for values stored in leafs and bounds stored in internal nodes:  
```  
// value_tag        bounds_tag  
// ---------------------------  
// contains(I,G)    covers(I,G)  
// covered_by(I,G)  intersects(I,G)  
// covers(I,G)      covers(I,G)  
// disjoint(I,G)    !covered_by(I,G)  
// intersects(I,G)  intersects(I,G)  
// overlaps(I,G)    intersects(I,G)  
// touches(I,G)     intersects(I,G)  
// within(I,G)      intersects(I,G)  
```

> Username: vissarion  
> Created_at: 2025-01-20 13:02:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1922372283  

Thanks for the details!

---

📁 index/test/rtree/rtree_with_strategies.cpp

```diff
  90 |+     std::enable_if_t<is_pp_or_bb_v<QueryValue, Rtree>, int> = 0
  91 |+ >
  92 |+ void test_overlaps_pp_bb(Rtree const& rtree)
```

> Username: vissarion  
> Created_at: 2025-01-17 14:06:39 UTC  
> Updated_at: 2025-01-17 14:07:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920244754  

maybe rename to `test_overlaps_touches_pp_bb` ?

> Username: awulkiew  
> Created_at: 2025-01-17 22:43:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/1368#discussion_r1920840515  

Right, I forgot to rename it to `test_queries_pp_bb`.


---
