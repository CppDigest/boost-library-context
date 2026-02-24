# #561 Add new algorithm detail::calculate_point_order() with strategies (and optimize remove_spikes()). [Merged]

> Username: awulkiew  
> Created at: 2019-03-01 21:23:49 UTC  
> Updated at: 2019-08-14 14:32:09 UTC  
> Merged at: 2019-08-14 14:32:08 UTC  
> Closed at: 2019-08-14 14:32:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/561  

This solves the issue caused by inaccurate area calculation for small polygons with andoyer formula.  
  
For geographic CS instead of area the sum of external angles is calculated.  
  
I tried something new with this algorithm. Instead of being dispatched by geometry tag I dispatched it by strategy's `version_tag`.  
  
There are 2 versions of this algorithm:  
- area-based which simply calculates the area and checks its value  
- azimuth-based which filters out point duplicates and spikes (it's modified version of `remove_spikes`)  
  
The version of an algorithm is defined by the strategy as a member type `version_tag`. The reason for this is that both algorithms are very different and the alternative would be to implement everything in strategies.  
  
This algorithm works for Rings only (actually Ranges of points). It returns one of three values of type `bg::order_selector`.  
  
As mentioned before geographic, azimuth-based version in modified `remove_spikes`. Instead of calling side strategy (2 azimuth calculated per point) it performs 1 azimuth/reverse-azimuth calculation per segment if needed and stores the result for future use. This means that 2x less azimuths are calculated compared to `remove_spike`. Spike detection is a side effect of the "normal" calculation of external angle for each segment. At a point reverse azimuth of previous segment is subtracted from azimuth of the next segment. Also because azimuths are the only thing that is calculated it's faster than `area` which does other things.  
  
Benchmark results (of area, point_order and remove_spikes as a bonus, plus time taken to calculate azimuth for each segment of a ring):  
  
Circle containing 2000001 points.  
  
| formula | `area` | `remove_spikes` | `calculate_point_order` | azimuth for each segment |  
| ----- | ----- | ----- | ----- | ----- |  
| andoyer | 1.752 | 1.993 | **1.014** | 0.766 |  
| thomas | 2.05 | 2.54 | **1.289** | 1.004 |  
| vincenty | 2.723 | 2.858 | **1.646** | 1.184 |  
  
Invalid polygon being one spike `(0 0, 45 45, 0 0, 45 45, ...)` containing 2000001 points.  
  
| formula | `area` | `remove_spikes` | `calculate_point_order` | azimuth for each segment |  
| ----- | ----- | ----- | ----- | ----- |  
| andoyer | 1.572 | 1.192 | **0.948** | 0.725 |  
| thomas | 1.871 | 1.439 | **1.205** | 0.956 |  
| vincenty | 3.025 | 2.105 | **1.998** | 1.578 |  
  
Note that `area` is faster than `remove_spikes` in the first case and slower in the second. My guess is that it has something to do with the fact that `remove_spikes` uses a vector of cleared points which is used to store points not being spikes nor duplicates and that in the second case all of the points are spike points so they are being cleared as they go and this temporary vector has at most 4 elements.  
  
Note that in the second case the times of `remove_spikes` and `calculate_point_order` are similar. ~My guess is that it's because here the number of azimuths calculation is not 2x smaller as I stated before because if spike is detected in `calculate_point_order` the azimuths has to be recalculated for both neighboring segments which means that more azimuth calculations is performed here (Looks like 2x as many but I haven't checked)~.  
  
Btw, I think `remove_spikes` could be slightly optimized:  
- `cleared` container could contain only iterators to the original points  
- the finalizing removal of spikes at the beginning and end of the container could be removed by range of iterators which means that `deque` could be replaced by `vector`.  
  
I've done this in `calculate_point_order` and it seemed to be faster than the original. This is also probably the case why in the second benchmark `calculate_point_order` is still faster than `remove_spikes`.  
  
But to be sure what's happening exactly more investigation would be needed.  
  
EDIT:  
  
I checked the numbers of azimuth calculations and the above remark about 2x as many calculations is not true. These are the numbers of inverse formula calls:  
  
| benchmark # | `area` | `remove_spikes` | `calculate_point_order` |  
| ----- | ----- | ----- | ----- |  
| 1 | 2000000 | 4000002 | 2000001 |  
| 2 | 2000000 | 4000002 | 2000000 |  
  
So the small difference above is not due to the number of azimuths called but something else. Above I also added benchmark for pure azimuths calculation as a base-line. So I don't know right now why does `remove_spike` times fluctuate so much. It's very similar algorithm to `calculate_point_order`. Maybe I'll try to do the same modifications there (mainly replace deque with vector) and see what happens.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2019-03-01 21:24:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-468816848  

Inspired by issue found while working on reading shapefiles: https://github.com/boostorg/geometry/pull/554

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-03-04 16:54:24 UTC  
> Updated_at: 2019-03-04 21:24:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-469328146  

I modified the `remove_spikes` algorithm in similar way, i.e. replaced deque with vector and modification of container with iterators tracking front and back. This makes the algorithm ~20% faster in my cases:  
  
Benchmark 1 (as above):  
  
| formula | `area` | `remove_spikes` develop | `remove_spikes` this PR | `calculate_point_order` | azimuth for each segment |  
| ----- | ----- | ----- | ----- | ----- | ----- |  
| andoyer | 1.752 | 1.993 | **1.636** | **1.014** | 0.766 |  
| thomas | 2.05 | 2.54 | **2.099** | **1.289** | 1.004 |  
| vincenty | 2.723 | 2.858 | **2.464** | **1.646** | 1.184 |  
  
Benchmark 2 (as above):  
  
| formula | `area` | `remove_spikes` develop | `remove_spikes` this PR | `calculate_point_order` | azimuth for each segment |  
| ----- | ----- | ----- | ----- | ----- | ----- |  
| andoyer | 1.572 | 1.192 | **0.85** | **0.948** | 0.725 |  
| thomas | 1.871 | 1.439 | **1.075** | **1.205** | 0.956 |  
| vincenty | 3.025 | 2.105 | **1.691** | **1.998** | 1.578 |  
  
Note that in the first benchmark `remove_spike` is slower than `calculate_point_order` but in the second one it's faster. It's probably due to the fact that the latter calculates, stores and checks states of azimuths in case they had to be recalculated and in benchmark 2 they have to be recalculated in all of the cases since the whole geometry is a spike. So this the worst, special case for this algorithm. In "normal" case which I consider to be most frequent one the algorithm is faster.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2019-04-08 11:54:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/561#pullrequestreview-223793660  

📁 test/algorithms/detail/Jamfile.v2

```diff
  19 |-     [ run partition.cpp : : : : algorithms_partition ]
  18 |+     [ run as_range.cpp              : : : : algorithms_as_range ]
  19 |+ 	[ run calculate_point_order.cpp : : : : algorithms_calculate_point_order ]
```

> Username: vissarion  
> Created_at: 2019-04-08 11:50:24 UTC  
> Updated_at: 2019-04-13 02:48:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#discussion_r273006580  

tab


---

## Comment 4

> Username: vissarion  
> Created_at: 2019-04-08 12:37:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-480813825  

Thanks for the extra algorithm. Why `version_tag` is needed? AFAIU, cartesian and spherical use area computations for orientation while geographic the new algorithm based on azimuth computation. Moreover, is it possible that spherical cs could also be benefited by this new algorithm?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2019-04-09 00:06:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-481051810  

> Why version_tag is needed?  
  
Because otherwise it would be implemented as either of these:  
1. 3 CS-dependant strategies containing the whole algorithms.  
2. 2 agnostic strategies implementing the CS-agnostic part of the algorithm (one using azimuth and the other area) and 3 CS-dependant strategies. Agnostic strategies would expect certain CS-dependant strategies.  
  
So I decided to try something new. Since the use of a specific CS-specific strategy has sense only in one algorithm or the other this information is used to dispatch the algorithm. I also think this is the correct approach in general since the Strategy in general can change the way how the geometries are processed it should be used together with geometries in dispatching of the algorithm.  
  
Envelope strategies could benefit from it since currently they contain member functions returning either a range of points or range of segments. So with this design this could be moved from strategies to algorithms where it should be.  
  
Furthermore, with this design we could remove the CS-agnostic strategies which AFAIU are algorithms, not strategies. Though in this case it's probably not wise due to backward compatibility reasons.  
  
> Moreover, is it possible that spherical cs could also be benefited by this new algorithm?  
  
Yes. I don't remember the exact reason why I didn't implement it. Probably something along the lines: if it isn't broken, don't fix it. Of course we could consider it.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-04-13 02:51:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-482770258  

> Moreover, is it possible that spherical cs could also be benefited by this new algorithm?  
  
It looks like the strategy based on area is faster: 0.282s and 0.272s vs 0.907s and 0.677s for benchmark 1 and 2 respectively.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2019-06-28 12:06:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-506710342  

@barendgehrels What do you think about it?  
  
This algorithm could be used to express point-order calculation clearly in algorithms like buffer or in overlay instead of directly calling area for this purpose.

---

## Comment 8

> Username: awulkiew  
> Created_at: 2019-07-10 13:34:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-510063017  

Today is the last call for 1.71. If we do not merge it then it will be postponed to 1.72.

---

## Comment 9

> Username: barendgehrels  
> Created_at: 2019-07-10 14:53:17 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#issuecomment-510095811  

Thank you, I'm OK with merging, I've a few remarks but those are minor comments.  
Sorry for the delay, I had looked at it earlier but did not finish the review.  
Great that the inaccuracy for geographic CS is solved, that will help future developments such as buffer.

---

## Review 10 [Commented]

> Username: barendgehrels  
> Created_at: 2019-07-10 14:53:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/561#pullrequestreview-260148801  

📁 include/boost/geometry/strategies/spherical/point_order.hpp

```diff
 108 |+ //        }
 109 |+ //    }
 110 |+ //};
```

> Username: barendgehrels  
> Created_at: 2019-07-10 14:50:28 UTC  
> Updated_at: 2019-07-10 14:53:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#discussion_r302108152  

Can you remove the commented code, or explain why it is commented?


📁 test/algorithms/detail/calculate_point_order.cpp

```diff
  31 |+         return "counterclockwise";
  32 |+     else
  33 |+         return "order_undetermined";
```

> Username: barendgehrels  
> Created_at: 2019-07-10 14:51:21 UTC  
> Updated_at: 2019-07-10 14:53:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#discussion_r302108664  

Curly braces are missing  
(alternatively you can use ternary operations)

---

📁 test/algorithms/detail/calculate_point_order.cpp

```diff
 113 |+    test_one<P>("POLYGON((5 5, 6 5, 6 6, 6 4, 6 5, 5 5, 5 4, 4 4, 4 6, 5 6, 5 5))", bg::clockwise);
 114 |+    test_one<P>("POLYGON((5 5, 6 5, 6 6, 6 4, 6 5, 5 5, 5 6, 4 6, 4 4, 5 4, 5 5))", bg::counterclockwise);
 115 |+    test_one<P>("POLYGON((5 5, 6 5, 6 5, 6 6, 6 5, 6 4, 6 5, 6 5, 5 5, 5 4, 4 4, 4 6, 5 6, 5 5))", bg::clockwise);
```

> Username: barendgehrels  
> Created_at: 2019-07-10 14:51:52 UTC  
> Updated_at: 2019-07-10 14:53:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/561#discussion_r302108956  

Indentation is inconsistent


---
