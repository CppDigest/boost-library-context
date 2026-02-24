# #1146 [experimental] update test-demo with version with lambdas [Open]

> Username: barendgehrels  
> Created at: 2023-05-08 20:37:06 UTC  
> Updated at: 2023-10-04 14:35:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146  

Hi, this is an experimental version of partition using lambdas.  
  
For demonstration and evaluation purposes.  
  
I'm curious to your findings.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2023-05-08 20:38:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1417480148  

📁 test/robustness/within/partition_within.cpp

```diff
 280 |+             },
 281 |+             box_visitor
 282 |+             );
```

> Username: barendgehrels  
> Created_at: 2023-05-08 20:38:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1187875630  

This shows how the function could be used.  
5 lambdas (2 expand, 2 overlaps, 1 main) plus an optional lambda (called per box),  
instead of the earlier policies with `apply`

> Username: barendgehrels  
> Created_at: 2023-05-08 20:42:32 UTC  
> Updated_at: 2023-05-08 20:42:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1187880087  

The program itself creates SVG's like this:  
  
![image](https://user-images.githubusercontent.com/334849/236930381-1cdcceb8-979a-4f59-9f3f-150bd9a3189c.png)

> Username: barendgehrels  
> Created_at: 2023-05-08 20:58:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1187892952  

See also https://barendgehrels.blogspot.com/2023/05/partition-with-boostgeometry.html

> Username: awulkiew  
> Created_at: 2023-05-09 08:02:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188277916  

A lot better! I prefer function objects over objects with `apply()` function.  
  
One nitpick. This is not quadtree, it's kdtree, a node in quadtree is divided into 4 children, here a division is done into 2, once per dimension. Furthermore partition is k-dimensional, quadtree is 2-dimensional.  
  
Side note, I was thinking about redesigning the API for a long time and use features from the rtree. Note e.g. that in your example you shouldn't be forced to pass function objects defining how to expand a box or perform intersection. These operations are always done the same way. We probably also never use box visitors, besides debugging or visualization so box visitor could be hidden as well. The only additional information in your example that's needed is the way of getting a box from item (taking point from point should work by default). In rtree this is done by IndexableGetter. There are also additional things like the type of operation (in this case intersects) and strategies (for defining CS) but they could be optional as well. I'm not saying to do this, only sharing what I was thinking about.

> Username: barendgehrels  
> Created_at: 2023-05-09 18:27:39 UTC  
> Updated_at: 2023-05-09 18:27:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188989035  

Thanks!  
  
I think it's not exactly a kdtree either. You are right, we divide into halves and not into 4. But always at the middle, in kdtree that it is not necessarily the case. Anyway, I can rename it of course. But it cannot be called `partition`... (unless we replace it).  
Plus we have those additional boxes for cases where geometries exceed the halves. But sure, they are not part of the quadtree either.  
  
I see your point. Yes, taking the box from an object is usually enough. Or point. But it could be done in a different way as well. For example: a box with a margin (for (max) distance calculations). It's flexible now. But that flexibility is probably used rarely, if at all. We use partition only internally, I think. It was introduced before our index so at that moment it was not a choice. So yes, it can be a bit simpler. The box visitor is indeed only used for debugging (at least at this moment).  
  
Anyway, additionally, if we go this way, we don't need those visitor-adaptor(s), we can deprecate the old implementation and let the new one immediately use function objects.

> Username: awulkiew  
> Created_at: 2023-05-09 23:21:25 UTC  
> Updated_at: 2023-05-11 07:49:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1189213924  

Splitting planes can be placed at various locations in a kd-tree. Classic approach is to place them at the median object. Another strategy is to cut empty space out. There is also version using surface area heuristic. There are probably more. It depends on a use case. Splitting in half is probably a poor choice in general good only for objects distributed uniformly but such tree is still kd-tree.  
  
Btw, in kd-tree splitting doesn't have to be done one dimension after another. The space can be split e.g. in X axis cutting out empty space and then again in X axis at the middle object, etc. But no matter what strategy is used this is still kd-tree.  
  
Regarding quadtrees. There are versions of quadtrees that have splitting planes at different locations than geometric middle of a node. AFAIR there are also quadtrees with child nodes partially overlapping each other which makes them similar to rtrees.  
  
Obviously partition is neither quadtree nor kd-tree because it's not a data structure.

> Username: barendgehrels  
> Created_at: 2023-05-10 09:10:39 UTC  
> Updated_at: 2023-05-10 09:10:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1189595403  

You're completely right, it's not a data structure. Thanks for the backgrounds. I will just keep the `partition` name.  
As a work-title it's now temporarily `lambda_partition`.  
I will not merge this PR. It is a discussion piece. If we agree and things are clear, I'll change the original (copy, and deprecate the old one)

> Username: barendgehrels  
> Created_at: 2023-05-10 09:14:33 UTC  
> Updated_at: 2023-05-10 09:14:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1189603599  

It's used internally around 12 times.  
Unfortunately it's not in namespace `detail` so officially it is part of the interface.


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2023-05-09 08:07:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1418106030  

📁 test/robustness/within/quadtree_partition.hpp

```diff
  95 |+ bool quadtree_partition(ForwardRange1 const& forward_range1,
  96 |+                         ForwardRange2 const& forward_range2,
  97 |+                         const std::function
```

> Username: awulkiew  
> Created_at: 2023-05-09 08:07:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188283204  

I'd avoid using `std::function` because it's heavy. Why did you decide to use it over a function object?

> Username: barendgehrels  
> Created_at: 2023-05-09 18:18:46 UTC  
> Updated_at: 2023-05-09 18:18:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188976281  

The compiler error messages are clearer.  
I was not aware that it is heavier.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2023-05-09 08:13:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1418115189  

📁 test/robustness/within/quadtree_partition.hpp

```diff
  35 |+         return m_f(i1, i2);
  36 |+     }
  37 |+ };
```

> Username: awulkiew  
> Created_at: 2023-05-09 08:13:12 UTC  
> Updated_at: 2023-05-09 10:13:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188289040  

All of the adaptors could probably be replaced with one simply forwarding parameters to function object:  
```  
template <typename F>  
struct adapt_partition_visitor  
{  
    F m_f;  
  
    explicit adapt_partition_visitor(F&& f)  
        : m_f(std::move(f))  
    {}  
  
    template <typename ...Ts>  
    decltype(auto) apply(Ts&& ...is) const  
    {  
        return m_f(std::forward<Ts>(is)...);  
    }  
};  
```

> Username: barendgehrels  
> Created_at: 2023-05-09 18:33:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1188994742  

Indeed, works perfectly. Thanks!  
:heavy_check_mark:


---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2023-05-12 12:48:35 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1424431927  

Thanks, this is cool! I am OK with merging now.  
I will have a deeper look next week.   
Regarding the name I also agree it is neither quad nor kd-tree (it is not a tree at all of course and it actually combines the default splitting strategy "cut in middle" of quad trees with the kd-tree per-dimension partitioning). So I think it is better to just call it "partitioning".

📁 test/robustness/within/Jamfile

```diff
   1 |+ # Boost.Geometry (aka GGL, Generic Geometry Library)
   2 |+ # Robustness Test - convex_hull
```

> Username: vissarion  
> Created_at: 2023-05-12 12:39:06 UTC  
> Updated_at: 2023-05-12 12:48:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1192311512  

this is not convex hull, typo?

> Username: barendgehrels  
> Created_at: 2023-05-12 17:33:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1192637524  

Indeed, I copied another file and forgot to adapt the title.  
:heavy_check_mark: fixed

> Username: barendgehrels  
> Created_at: 2023-05-12 17:34:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1192637919  

> Thanks, this is cool! I am OK with merging now. I will have a deeper look next week. Regarding the name I also agree it is neither quad nor kd-tree (it is not a tree at all of course and it actually combines the default splitting strategy "cut in middle" of quad trees with the kd-tree per-dimension partitioning). So I think it is better to just call it "partitioning".  
  
Renamed now to `partitioning` (currently in namespace `experimental`)


📁 test/robustness/within/lambda_partition.hpp

```diff
  91 |+         >::apply(forward_range1, forward_range2, av,
  92 |+                  aev1, aov1,
  93 |+                  aev2, aov2, 16, apbv);
```

> Username: vissarion  
> Created_at: 2023-05-12 12:43:11 UTC  
> Updated_at: 2023-05-12 12:48:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1192316665  

maybe add a comment that 16 is the end of the iteration (the max number of points in a partition box)

> Username: barendgehrels  
> Created_at: 2023-05-12 17:09:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1192617232  

I changed it to an options structure, as I planned earlier.  
Is something like this fine?  
:heavy_check_mark:


---

## Comment 5

> Username: barendgehrels  
> Created_at: 2023-09-29 12:51:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#issuecomment-1740849136  

I updated this PR:  
* adding a lambda version over one range as well (like the original had)  
* applying the lambda version in `buffer`, which nicely removes many helper structures.  
  
It's still in namespace `experimental` (so don't merge yet), I can't put it in namespace `geometry` because it conflicts with the structure `partition`. Any suggestions?

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2023-09-29 12:53:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1650719420  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 369 |+                     return turn.is_turn_traversable && ! turn.within_original;
 370 |+                 }
 371 |+             };
```

> Username: barendgehrels  
> Created_at: 2023-09-29 12:53:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1341328280  

Doing this, I noticed that we sometimes use the `include_turn_policy` but sometimes have it in the get_box/overlaps functors. For that reason, if this functionality is always there, we could also choose to get rid of these options.  
(the one left is the default partition size but we can specify it as an optional parameter).  
What do you think?

> Username: barendgehrels  
> Created_at: 2023-10-04 13:34:15 UTC  
> Updated_at: 2023-10-04 13:34:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1345808419  

I omitted it, it was really just about expanding the bounding box.


---

## Review 7 [Commented]

> Username: barendgehrels  
> Created_at: 2023-09-29 12:54:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1146#pullrequestreview-1650721401  

📁 include/boost/geometry/algorithms/detail/partition_lambda.hpp

```diff
 106 |+ <
 107 |+     typename BoxType,
 108 |+     typename ForwardRange,
```

> Username: barendgehrels  
> Created_at: 2023-09-29 12:54:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#discussion_r1341329452  

This version is added, it uses only one `ForwardRange` which is partitioned with itself


---

## Comment 8

> Username: barendgehrels  
> Created_at: 2023-10-04 13:35:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#issuecomment-1746894196  

> I updated this PR:  
>   
> * adding a lambda version over one range as well (like the original had)  
> * applying the lambda version in `buffer`, which nicely removes many helper structures.  
>   
> It's still in namespace `experimental` (so don't merge yet), I can't put it in namespace `geometry` because it conflicts with the structure `partition`. Any suggestions?  
  
I'm now using `detail::lambda_partition`.   
Still to do:  
* use it in `disjoint`  
* use it in `is_valid`  
* use it in `relate`  
So it's getting there.  
  
Then we can deprecate `partition` itself because it is not used inside the library.  
  
In a next Boost version, we can delete it and rename `partition_lambda` to just `partition`.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2023-10-04 14:02:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#issuecomment-1746944853  

gcc 5 now reports:  
```  
/home/gehrels/git_public/boost/boost/geometry/algorithms/detail/overlay/get_turns.hpp:524:32: error: request for member ‘bounding_box’ in ‘sec’, which is of non-class type ‘const int’  
                geometry::expand(box, sec.bounding_box, strategy);  
```  
This is inconvenient and wrong, it is there and correct. And it is only for the second lambda...  
  
Any idea how to circumvent this?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2023-10-04 14:35:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1146#issuecomment-1747008331  

> which is of non-class type ‘const int’  
  
It confuses the two overloads of `partition_lambda`... If I use different names, it works. But that is less convenient....  
To be continued.

---
