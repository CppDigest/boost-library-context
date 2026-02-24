# #35 Feature/disjoint [Merged]

> Username: mkaravel  
> Created at: 2014-05-21 14:13:53 UTC  
> Updated at: 2014-05-23 22:09:07 UTC  
> Merged at: 2014-05-23 22:09:07 UTC  
> Closed at: 2014-05-23 22:09:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/35  

Major changes:  
1) clean-up code;  
2) move multi code to algorithms directory  
3) updated segment-box algorithm that does not use numeric_limits  
4) move bg::detail::equals::point_point implementation to proper place  
5) add support for a few more geometry combinations (multi-point is not supported yet)  
6) more compact dispatching by means of tag_cast  
7) eliminate usage of segment_view

---

## Comment 1

> Username: mkaravel  
> Created_at: 2014-05-21 14:29:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43761402  

The file algorithms/detail/disjoint.hpp is still there but should go away.  
I have kept it in case somebody wants to compare the old and updated implementation for segment-box.

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-05-23 00:29:07 UTC  
> Updated_at: 2014-05-23 00:29:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43959860  

Thanks for cleaning this up!  
  
I'm curious, have you tested the performance of disjoint(Seg,Box)? Is it faster or slower?  
  
There is duplicated code in disjoint_segment_box_impl<> because it's specialized for I==0. Would it be possible to  
- Move some initialization out of disjoint_segment_box_impl<>,  
- Specialize for I == Dimension with basically empty apply() ?  
  
Or would it require to use numeric_limits which you want to avoid?  
Or at least would it be possible to enclose the common parts in one place and use it in disjoint_segment_box_impl<>?

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-05-23 10:35:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43993585  

@awulkiew No I have not run any performance tests. I do not expect it to be different from the old implementation. The algorithm is the same and the number of operations is the same (give or take a couple more or less).  
  
There is a specialization for I == Dimension. See line 218. It is not really "empty". It always returns false.  
  
I want to keep the specialization for Dimension == 0, as it is there to avoid the need to initialize t_min and t_max. This is indeed related with the usage for std::numeric_limits, which gives wrong results for non-fundamental number types. std::numeric_limits is not supposed to be specialized for non-fundamental number types, although there is a generic implementation, which basically gives no useful info for non-fundemantal number types.  
See: http://www.cplusplus.com/reference/limits/numeric_limits/  
  
About the wrong results. Suppose that your geometries have ttmath_big as number type (but any other non-fundamental number type would have the same behavior). Then take a box and an axis-parallel segment disjoint from the box. The old implementation will erroneously reply that the two geometries intesect. Concrete example: BOX(0 0,1 1) and SEGMENT(0 10,10 10).  
  
Finally, yes, I can enclose the common part in one place. Will do so and push.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-05-23 11:13:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996170  

@awulkiew Common code has been factored out. See above commit.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2014-05-23 11:14:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996268  

Because by default numeric_limits<> for non-built-in types returns max and min equal to 0, ok.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-05-23 11:16:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996404  

@mkaravel Thanks, I'll wait for more comments and merge today.

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-05-23 11:17:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996486  

@awulkiew Yes exactly: min and max for non-fundamental number types defaults to 0.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-05-23 11:18:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996505  

@awulkiew Sure, let's wait for more comments.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-05-23 11:22:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43996917  

@awulkiew One more thing: before doing the merge, I need to delete algorithms/detail/disjoint.hpp  
Of course this can be done via another pull request, but I would prefer to do it as part of this pull request. So please let me know when you are ready for the pull, so that I can remove the file.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-05-23 11:31:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-43998297  

@mkaravel I plan to merge this PR after midnight CET.

---

## Comment 11

> Username: mkaravel  
> Created_at: 2014-05-23 20:48:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/35#issuecomment-44058701  

@awulkiew Branch is ready for merge

---
