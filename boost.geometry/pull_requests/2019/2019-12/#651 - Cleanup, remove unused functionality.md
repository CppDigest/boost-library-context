# #651 [buffer] Cleanup, remove unused functionality [Merged]

> Username: barendgehrels  
> Created at: 2019-12-11 13:59:45 UTC  
> Updated at: 2020-04-29 17:20:06 UTC  
> Merged at: 2020-01-18 13:15:22 UTC  
> Closed at: 2020-01-18 13:15:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/651  

This PR:  
* (buffer) removes unused functionality (occupation map, get_left_turns, several fields in buffer pieces, and more)  
* removes some unused includes  
* moves function for creating ring_identifier from segment_identifier, and use that more generally  
* (buffer) creates monotonic sections in another way  
* (buffer) creates original rings in another way (reducing its number of points)  
  
*About monotonic sections*:  
The monotonic section-bounding box was previously enlarged with 1 (also for non-rescaled versions).   
  
For rescaling that is fine, but without rescaling it is way too big. However, changing that (I now call sectionalize function for doing that), I got one regression (without rescaling). I debugged that and analyzed that (this is also a reason for the refactoring of monotonic section creation), but in the end it appears that if it is made larger (e.g. 2 i/o 1), it also fails. So the root cause is something else, still to be fixed (without rescaling), a turn is missed somehow.  
  
Anyway, it is better to not specifically for this only one testcase use an exceptionallly and wrong increment of the bounding box.  
  
For the rest there are no regressions.  
  
*After this*:  
This PR prepares also the next PR, which will be larger than this one: I'm revising turn-in-buffered-piece detection, making it more robust and also less dependent on cartesian system (this will deprecate the large turn_in_piece visitor and side_by_intersection, which are both targeted to cartesian). And (the first reason to do it) it should be more robust.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-12-11 15:41:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/651#pullrequestreview-330634831  

Thanks! Looks promising, I am ok with merging.

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 861 |+             // Assign the ring to the original_ring collection
 862 |+             // For rescaling, it is recalculated. Without rescaling, it
 863 |+             // is just assignming (note that this Ring type is the
```

> Username: vissarion  
> Created_at: 2019-12-11 15:40:56 UTC  
> Updated_at: 2019-12-18 09:13:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#discussion_r356671103  

typo

> Username: barendgehrels  
> Created_at: 2019-12-12 20:21:00 UTC  
> Updated_at: 2019-12-18 09:13:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#discussion_r357352163  

thnks, will fix

> Username: barendgehrels  
> Created_at: 2019-12-18 09:15:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#discussion_r359230260  

Fixed.  
  
@awulkiew OK with merging?


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2020-01-17 10:15:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#issuecomment-575563366  

I would like to merge this because I've a follow up nearly ready. @awulkiew OK?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-01-17 13:06:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#issuecomment-575618379  

@barendgehrels ok sorry, I'll review it today.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2020-01-17 16:18:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#issuecomment-575693090  

Btw, recently I was working on a patch for integer overflow in overlay (basically replacing `long_long_type` with greater Multiprecision integer) [1] and I noticed that there are some algorithms called without strategy in buffer (collection and/or turn_in_piece_visitor). `comparable_distance` for sure and AFAIR some other ones (envelope/expand or some relop). Something to keep in mind.  
  
This also revealed some interesting issue with the library. I.e. that we assume that user-defined type is floating-point type, which may not be the case. But that's a different issue.  
  
[1] This is a patch which I maintain until the rescaling is dropped. See: https://github.com/boostorg/geometry/compare/boost-1.72.0...awulkiew:fix/mysql

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2020-01-18 13:16:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/651#issuecomment-575897633  

@awulkiew : thanks - I'm revising turn_in_piece visitor now, that definitely has several calls without strategy. So I will keep this in mind.

---
