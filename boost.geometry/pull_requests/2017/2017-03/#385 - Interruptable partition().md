# #385 Interruptable partition() [Merged]

> Username: awulkiew  
> Created at: 2017-03-09 16:29:06 UTC  
> Updated at: 2017-09-06 12:29:38 UTC  
> Merged at: 2017-03-10 22:46:21 UTC  
> Closed at: 2017-03-10 22:46:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/385  

This PR makes partition() algorithm interruptable. The partition is interrupted when `visitor.apply(...)` returns `false`.  
  
`self_ip_exception` is no longer needed, so removed. It's related to https://github.com/boostorg/geometry/pull/384  
  
A side effect is performance increase. Below I show times gathered using this benchmark:  
https://github.com/awulkiew/benchmark-geometry/blob/master/benchmarks/algorithms.cpp  
data generated like this:  
https://github.com/awulkiew/benchmark-geometry/blob/master/benchmarks/data.hpp  
below:  
 - gcc is mingw-w64 4.9.1 -O2  
 - msvc is msvc-14 /O2  
 - develop - times for develop branch, interrupt - times for this branch  
 - relate-disj is relate() with disjoint mask  
 - zero - there is no overlap between geometries  
 - min - there is minimal overlap btween geometries (touching)  
 - max - each segment of one geometry overlaps two segments of other geometry  
 - zigzag - each segment of one geometry overlaps all segments of other geometry  
 - time of one function call in seconds  
 - zero/min/max geometries contain 100001 points, zigzag linestrings contain 400 points  
  
| test name           | gcc develop | gcc interrupt | msvc develop | msvc interrupt |  
| ------------------- | ----------- | ------------- | ------------ | -------------- |  
relate-disj-poly-zero | 0.00780 | 0.00780 | 0.00839 | 0.00876 |  
relate-disj-poly-min  | **0.00557** | 0.00452 | **0.00616** | 0.00457 |  
relate-disj-poly-max  | **0.02275** | 0.00450 | **0.02758** | 0.00442 |  
relate-disj-ls-zero   | 0.00446 | 0.00448 | 0.00455 | 0.00439 |  
relate-disj-ls-min    | **0.00560** | 0.00450 | **0.00594** | 0.00436 |  
relate-disj-ls-max    | **0.02248** | 0.00455 | **0.02775** | 0.00440 |  
relate-disj-ls-zigzag | **0.11100** | 0.00009 | **0.13010** | 0.00008 |  
intersects-poly-zero  | 0.00775 | 0.00784 | 0.00854 | 0.00812 |  
intersects-poly-min   | **0.00553** | 0.00450 | **0.00603** | 0.00440 |  
intersects-poly-max   | **0.02095** | 0.00442 | **0.02766** | 0.00450 |  
intersects-ls-zero    | 0.00441 | 0.00447 | 0.00479 | 0.00441 |  
intersects-ls-min     | **0.00547** | 0.00447 | **0.00635** | 0.00442 |  
intersects-ls-max     | **0.02106** | 0.00447 | **0.02828** | 0.00447 |  
intersects-ls-zigzag  | **0.09510** | 0.00006 | **0.11216** | 0.00008 |  
touches-poly-zero     | 0.00779 | 0.00784 | 0.00837 | 0.00832 |  
touches-poly-min      | 0.00545 | 0.00547 | 0.00604 | 0.00621 |  
touches-poly-max      | 0.44652 | 0.44052 | 0.45616 | 0.45228 |  
touches-poly          | 0.00620 | 0.00610 | 0.00852 | 0.00863 |  
intersects-poly       | 0.00619 | 0.00613 | 0.00869 | 0.00855 |  
is_valid-poly         | 0.02374 | 0.02306 | 0.03384 | 0.03265 |

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2017-03-10 18:53:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#issuecomment-285753413  

Looking very good - also the performance increases!

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2017-03-10 18:59:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/385#pullrequestreview-26351122  

📁 include/boost/geometry/algorithms/detail/partition.hpp

```diff
 432 |+                              ExpandPolicy2 const& expand_policy2,
 433 |+                              OverlapsPolicy2 const& overlaps_policy2,
 434 |+                              VisitBoxPolicy& box_policy)
```

> Username: barendgehrels  
> Created_at: 2017-03-10 18:59:28 UTC  
> Updated_at: 2017-03-10 19:00:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#discussion_r105464930  

Next time, if possible, please don't mix layout changes with other changes. It makes the review more work.  
  
About the layout change itself, this is how it is more and more done. But it is not how we started. We started by just intending 3 or 4 tabs (units 4 spaces). So independent on method name.  
  
A drawback of this new convention is that, if the method name changes, or the return type (in length), all the lines have to change.  
  
Another, larger, drawback is with long method names, it is all very indented, has to be split more (so into more lines), and in the end is less readable.  
  
Especially because of the last reason, I don't know if I agree. However, sometimes I see myself taking it over also because QtCreator nearly forces it... that is maybe how this appears here too.

> Username: awulkiew  
> Created_at: 2017-03-10 22:41:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#discussion_r105504344  

Yes, it looks better but requires more work unless the environment is able to format automatically somehow. In this case I've done it manually while traversing the file. I agree that I shouldn't have done it as a part of this PR, sorry about that.

> Username: barendgehrels  
> Created_at: 2017-03-12 16:31:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#discussion_r105561849  

OK - no problem


---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-03-12 04:12:46 UTC  
> Updated_at: 2017-03-12 17:52:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#issuecomment-285920992  

I noticed that on linux ubuntu/mint x64 gcc 4.9.2 the performance decreased slightly for "zero" cases:  
https://awulkiew.github.io/benchmark-geometry/  
I was able to compensate this with a simple optimization: https://github.com/boostorg/geometry/commit/51f85b6fe24c2a71de7ddc4ecc7aac8435c95ce8.  
EDIT: It's not clear if it's really the case because after running a few cycles of benchmarks for recent commits sometimes the performance is better and sometimes isn't.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2017-03-12 14:57:05 UTC  
> Updated_at: 2017-03-12 17:50:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/385#issuecomment-285949884  

So the issue remains (in some cases?) and it looks like it depends on the compiler. Though I'm not sure how reliable are the performance tests on CircleCI (though I was able to reproduce it locally on Linux Mint). I don't know why it doesn't manifest itself with other compilers I tested locally (esspecially mingw-w64 gcc).  
  
In the worst case scenario, i.e. if it was true that the performance is slightly worse in `disjoint()`/`intersects()` and `relate()` when there is no overlap. Would it be a good tradeof for immediate interruption? It would depend how probable was it that geometries was overlapping. My guess is that if we consider arbitrary geometries scatered around the coordinate system then the probability that they overlap is low, so a user might notice performance decrease. But if we consider that in practice a user would test bounding boxes first, e.g. by using the rtree, then the probability that the geometries indeed overlap increases so the performance might increase.  
  
It seems to not manifest itself in `get_turns` tests when all turns has to be calculated, `no_interrupt_policy` is used which causes the visitor to always return `true`. Surprisingly it seems to affect `relation()` (for linestrings the performance increases and for polygons decreases). I wonder how about `buffer()`?  
  
Anyway, these are only guesses and I'm not sure how reliable is benchmarking at CircleCI. Overall I think that the change is good enough.

---
