# #141 Feature/segment iterator [Merged]

> Username: mkaravel  
> Created at: 2014-09-24 13:46:00 UTC  
> Updated at: 2014-10-31 13:56:25 UTC  
> Merged at: 2014-09-30 10:45:57 UTC  
> Closed at: 2014-09-30 10:45:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/141  

This PR proposes a const iterator for iterating over the segments of a geometry.  
The supported geometries are: linestring, ring, polygon, multi-linestring and multi-polygon  
  
Design-wise, the segment iterator keeps track of a point iterator for the supported geometry.  
Once `operator*` is called on the segment iterator, then a segment object is created on the fly and returned to the user (using the current point iterator and its successor). To be precise the segment returned is a `boost::geometry::referring_segment` defined over a const point type (the point type of the geometry).  
  
The proposed segment iterator is currently bidirectional.  
  
**Motivation:** in the distance algorithm code, I need to create an R-tree based on the segments of a geometry. To do this I need access to the segments of a geometry, but without creating/copying the points.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2014-09-24 14:07:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56674511  

Hi Menelaos,  
Sounds great.  
Because we did a similar (later aborted) action in the past, I would like to see a performance report (before merging):  
- speed iterating over points/segments "manually"  
- speed using this segment_iterator  
  
So just iterating, not doing time-consumptive things with them.  
  
Is that possible?

---

## Comment 2

> Username: mkaravel  
> Created_at: 2014-09-24 15:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56690010  

I think it is.  
  
You might remember that I implemented in the past a class called `get_segments`, which outputs the segments of a geometry to an output iterator. Getting the segments is done in a _manual_ way, which is what you want to compare against.  
See: https://github.com/mkaravel/geometry/blob/feature/distance-segments-in-rtree/include/boost/geometry/algorithms/detail/distance/get_segments.hpp  
  
I can implement the same functionality as `get_segments` using the segment iterator and compare the two.  
  
I think this will give us a fair measure of the efficiency of the segment iterator, as in a common usage scenario what you would do is to use the segment iterator to recover the segments of a geometry, more or less like what `get_segments` does.  
  
What do you think?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2014-09-24 15:44:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56690520  

Yes, I think that will do, it should be just a small test, iterating over a lot of points, to check if we don't degrade performance if using the iterator

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-09-26 08:47:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56935254  

Regarding the three commits above: I wanted the `segment_iterator` to return a value that is assignable, so that the `segment_iterator` can be used in an algorithm such as `std::copy`.  
  
The way I handled this is my introducing a new model for the segment concept, which I call `pointing_segment`. It stores pointers to points instead of references to them. This class is now used as the value type of the the `segment_iterator`.  
  
In addition to adding this class, I have also modified the unit test for the segment iterator to also test it with `std::copy`: the unit test now copies (using `std::copy`) the segments of a geometry in an `std::vector` using the segment iterator.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-09-26 10:03:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56942611  

**_Performance tests**_  
  
I compared outputting the segments of a geometry to n `std::vector` with:  
- the `segment_iterator` and `std::copy`  
- _manually_, by means of the `get_segments` class that maybe found here: https://github.com/mkaravel/geometry/blob/performance/performance/iterators/segment_iterator/get_segments.hpp  
  
The code used for doing the performance tests/timings can be found here: https://github.com/mkaravel/geometry/tree/performance/performance/iterators/segment_iterator  
  
I considered three types of data sets:  
- linestrings with random points in the square `[0,1]x[0,1]`  
- multilinestrings consisting of linestrings where each one is a single segment; again the points are taken randomly in the square `[0,1]x[0,1]`  
- polygons of the same type that I used for doing performance tests for `is_valid` in PR #124; these polygons are squares with a series of pairwise disjoint holes in their interior, all of which are also squares.  
  
The performance test program was compiled with `clang++ -O3 -DNDEBUG -std=c++11 -stdlib=libc++`.  
  
Boost.Timer has been used for timings.  
  
The value type for both the segment iterator and the output vector was the `pointing_segment` that I introduce in this PR. I also tried `bg::model::segment` for the manual approach, but the times where twice as high wrt to using the pointing segment, due to the cost of actually copying points. So in order to have a fair comparison, I used the `pointing_segment` class for both the manual and iterator-based approach.  
  
Here are the times. Each time value is in seconds and corresponds to 10 runs of the copy procedure.  
  
| Linestring size (# of points) | manual approach | segment iterator approach |  
| :-: | --: | --: |  
| 10^2 | 0.000035 | 0.000027 |  
| 10^3 | 0.000140 | 0.000122 |  
| 10^4 | 0.001369 | 0.001596 |  
| 10^5 | 0.013583 | 0.013496 |  
| 10^6 | 0.122989 | 0.121532 |  
| 10^7 | 1.515166 | 1.514365 |  
| 10^8 | 13.303213 | 13.284014 |  
  
| Multilinestring size (# of segments) | manual approach | segment iterator approach |  
| :-: | --: | --: |  
| 10^2 | 0.000038 | 0.000028 |  
| 10^3 | 0.000194 | 0.000108 |  
| 10^4 | 0.001245 | 0.001208 |  
| 10^5 | 0.013415 | 0.014151 |  
| 10^6 | 0.126289 | 0.129300 |  
| 10^7 | 1.545215 | 1.537651 |  
| 10^8 | 16.769838 | 16.862996 |  
  
| Polygon size (# of points) | Polygon size (# of segments) | manual approach | segment iterator approach |  
| :-: | :-: | --: | --: |  
| 2.5*10^2 | 2*10^2 | 0.000029 | 0.000017 |  
| 2.5*10^3 | 2*10^3 | 0.000288 | 0.000241 |  
| 2.5*10^4 | 2*10^4 | 0.002500 | 0.002684 |  
| 2.5*10^5 | 2*10^5 | 0.033020 | 0.035189 |  
| 2.5*10^6 | 2*10^6 | 0.228691 | 0.213961 |  
| 2.5*10^7 | 2*10^7 | 3.227438 | 3.278189 |

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-09-26 10:51:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56946833  

@mkaravel: What's the purpose of assignable segments? I'm not sure if this should be allowed. I can imagine that many things can go wrong with them. AFAIU the goal is to represent a Geometry as a Range of (more or less not connected) Segments. If they're assignable then you can change Points of the original Geometry, so when you modify one segment, you can also modify some other one also pointing to the same Point, stored in the same Range.  
  
Regarding the performance testing. Are the results similar for GCC and/or C++98?

---

## Comment 7

> Username: mkaravel  
> Created_at: 2014-09-26 11:35:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56950300  

@awulkiew Regarding the assignable segments: I agree with you that it is not obvious that we should have them (in fact I believe we should not). But this is not really what is happening, and I apologize for not making this clear enough.  
  
I want to have a model of the segment concept, let's call it `custom_segment`, such that I can do things like:  
  
```  
custom_segment s(p1, p2);  
custom_segment other = s;  
```  
  
This is possible with `bg::model::segment<Point>` but it copies points. It is not possible with `bg::model::referring_segment<Point>`, because it stores point references internally.  
  
To be more precise, I want my custom segment to have read-only access to the points, but still be assignable in the sense above. `bg::model::segment<Point const>` does this, but again copies points. `bg::model::referring_segment<Point const>` does not allow these assignments as it stores const references to points internally.  
  
All I want to do is to be able to assign segments, but not necessarily modify them. So what I did is to implement the class `template <typename Point> bg::model::pointing_segment` that stores internally two `Point*`. When used from within the `segment_iterator` class it is instantiated as `point_segment<SomePoint const>`, so internally it stores const pointers. the segment itself can be assigned but the contents of the points cannot. So it is basically a read-only assignable segment.  
  
I will get back to the performance testing with other compilers later.

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-09-26 12:21:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-56954162  

@awulkiew Regarding performance testing for different compilers/modes: I tried clang++-3.2/C++98 and g++-4.8/C++11 (the ones reported are clang++-3.2/C++11). The results are consistent. The actual timings vary, but the manual vs. `segment_iterator` relative times are more or less the same.  
  
I have included these timings in .txt files in the directory: https://github.com/mkaravel/geometry/tree/performance/performance/iterators/segment_iterator so that you can take a look at them if you want.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2014-09-26 19:51:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57011730  

@mkaravel Ah yes, sorry I didn't check the code before. I had very little free time to respond. So the pointing_segment<> has pointer-semantics, shallow-copy is performed. Plus, it's possible to default-construct it so to store elements of this type in an array, though this is probably less needed.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2014-09-26 19:54:32 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18110189  

Have you considered setting pointers to NULL in default ctor? And putting some asserts in get/set access functions, to detect uninitialized segments?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2014-09-26 19:56:36 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18110312  

Explicitly defined copy ctor and assignement aren't needed in this case, right?

---

## Comment 12

> Username: awulkiew  
> Created_at: 2014-09-26 20:06:24 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18110798  

Should this struct be defined in namespace bg? AFAIU this is a helper class used internally. Same as point_iterator_value_type.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2014-09-26 20:40:39 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18112480  

I'm wondering, what's the purpose of this class template? Only to handle closed/open rings or do you plan to reuse it also somewhere else than in `segment_iterator<>` implementation? Why all mechanics wasn't implemented/dispatched in one place?  
  
Or rather why isn't this class called `segment_iterator<>` since AFAIS range_segment_iterator is the type defined by `segment_iterator_type<>` and returned by `segments_begin/end()` functions. Then why is the `segment_iterator<>` needed? To always refer to non-mutable Geometries? If the answer is yes I'm not sure if this is a good approach. It's inconsistent with the `point_iterator<>` isn't it? It should probably be possible to refer to mutable Geometries. If not, then maybe it should be called `const_segment_iterator<>`?

---

## Comment 14

> Username: mkaravel  
> Created_at: 2014-09-27 09:51:09 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18122853  

Good point. Will do.

---

## Comment 15

> Username: mkaravel  
> Created_at: 2014-09-27 09:51:46 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18122854  

You are right. they are essentially the same with what the compiler would produce.  
I will remove them.

---

## Comment 16

> Username: mkaravel  
> Created_at: 2014-09-27 09:52:59 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18122858  

I had the same concern. Are we allowed to use a detail namespace much like what we do in algorithms? My preference/proposal would be to put this code inside a `detail::segment_iterator` namespace. Is that okay?

---

## Comment 17

> Username: barendgehrels  
> Created_at: 2014-09-27 09:56:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57048051  

Hi Menelaos,  
Thanks for the performance tests and reports! Looks very good.  
Adam, thanks for your comments.  
My time is currently and next week quite limited, so please go ahead, if the review comments are processed I'm OK with merging.  
Barend

---

## Comment 18

> Username: mkaravel  
> Created_at: 2014-09-27 10:01:20 UTC  
> Updated_at: 2014-09-30 06:54:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#discussion_r18122870  

Quite a few questions here.  
  
The classes `range_iterator_begin` and `range_iterator_end` are used to handle open/closed rings. I can certainly move the code somewhere else. Also related to my comment about regarding the `detail::segment_iterator` namespace: they could go there.  
  
Regarding the class `range_segment_iterator`: is used for both rings and linestrings. It should not be in `boost::geometry` namespace but rather in some detail namespace (see other comments above).  
  
Regarding the `segment_iterator` and `segment_iterator_type` classes: I need to think a bit more whether they can be merged. I also prefer a name that reflects that the iterator is const. `const_segment_iterator` is one, and I am also thinking about `segment_const_iterator`. Which one would you prefer? I think I prefer the `segment_const_iterator` (it puts the emphasis on the `const_iterator` part rather than the `const_segment` part).  
  
NOTE: this comment has been edited again, after the initial edit.

---

## Comment 19

> Username: mkaravel  
> Created_at: 2014-09-27 10:06:11 UTC  
> Updated_at: 2014-09-27 10:16:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57048254  

@barendgehrels Hi Barend. Thanks for the positive feedback.  
  
Most of Adam's comments are easy to deal with.  
  
There are a couple of comments I would like your opinion on:  
- Are we allowed to use a detail namespace for iterators? Would `boost::geometry::detail::segment_iterator` be okay?  
- Since the iterator is meant to return read-only segments, should the name be changed? Which one among `const_segment_iterator` and `segment_const_iterator` would you prefer?

---

## Comment 20

> Username: barendgehrels  
> Created_at: 2014-09-27 12:35:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57051371  

Yes, I think the way of algorithms (detail/<algorithm_name>) we can use for iterators too. So your proposal looks fine.  
About the name - my opinion: I'm OK with keeping it segment_iterator. If ever we create a non-const version it should be transparent, having the same name, and probably / if possible reflected in the type, the way boost::range_iterator also works. OTOH std library also uses const in the name, as member type, so in that case I would prefer const first (const_segment_iterator). Also because the segment is const and not the iterator.  
But I think I prefer omitting const

---

## Comment 21

> Username: awulkiew  
> Created_at: 2014-09-27 20:31:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57064801  

I also think that `const_segment_iterator` is a better name. But I'm curious, is there other reason why `segment_iterator<>` shouldn't work with both non-mutable and mutable Geometries than the unintuitive usage we were talking about before? That while modifying one segment, some other one could be modified? Besides that modifying the Geometry this way might invalidate it, but I'd assume that if someone done something like this he'd know what he's doing.  
  
AFAIU if it worked also with mutable Geometries it would be in-line with `point_iterator<>` (and also other iterators or algorithms/tools?). AFAIR it's also possible to mess with the Geometry's data using this iterator. Or maybe should `point_iterator<>` be const too, to be compatible with the `segment_iterator<>`? Assuming I'm not missing something and it isn't already.  
  
I can't see a use case why someone would want to modify the Geometry through point or segment iterator but this doesn't mean that it couldn't be useful. What do you think?

---

## Comment 22

> Username: mkaravel  
> Created_at: 2014-09-29 12:28:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57153158  

@barendgehrels Okay, I am fine with keeping the name `segment_iterator`. In fact, I suggest that in order to make crystal clear that the iterator is a const one, I suggest that the function prototypes are:  
  
```  
segment_iterator<Geometry const> segments_begin(Geometry const& geometry);  
segment_iterator<Geometry const> segments_end(Geometry const& geometry);  
```  
  
as opposed to the current prototypes  
  
```  
segment_iterator<Geometry> segments_begin(Geometry const& geometry);  
segment_iterator<Geometry> segments_end(Geometry const& geometry);  
```  
  
which is somehow misleading and also different from those of the point iterator.  
  
@awulkiew I am okay with the idea to have a mutable segment iterator. But here with mutable segment iterator we would mean a segment whose endpoints are mutable, and thus modifying the endpoints of the segment would modify the geometry itself, as well as other segments that are values of segment iterators, much like Adam's has pointed out. Just wanted to make the distinction clear. I agree with Adam's point that if a user utilizes a mutable segment iterator, then he/she probably knows what he/she is doing and in that respect allowing mutable segment iterators is fine.  
  
In fact the code I have written is perfectly compatible with having mutable segment iterators, except for one technical issue: for open geometries (rings, polygons, etc.), I am using the closing iterator to transform the open geometry's iterator range to that of its closed counterpart. The closing iterator currently offers only const access. To provide non-const access via the segment iterator I would need to modify the code of the closing iterator as well. Please let me know if this is okay.

---

## Comment 23

> Username: awulkiew  
> Created_at: 2014-09-29 14:04:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57165187  

@mkaravel: It's good that you mention closed geometries. Is it possible to modify the last, closing Point using `segment_iterator<>` and/or `point_iterator<>`?  
  
If the answer is NO.  
The user would be forced to manually modify the last Points of all closed Rings, which is ok I guess, just inconvenient.  
`bg::correct()` wouldn't do the trick because it would just append a duplicate of the first Point at the end leaving the non-modified closing Point as it was.  
We could move `for_each_range()` from details but this algorithm might be unintuitive for some of the users, e.g. it could as well iterate over single geometries contained in multi-geometry. It works differently for open and closed Polygons. And still the user would be forced to fix the Geometry manually.  
  
If the answer is YES.  
For closed Geometries the first Segment would refer to Points 0 and 1, and the Points N-1 and N. But for open Geometries the last Segment would refer to Points N-1 and 0. So during iteration over closed and open Rings at some point different data would be accessed. So it wouldn't be transparent.  
Similar (but more intuitive) for `point_iterator<>` which would iterate over one additional, duplicated Point.  
  
So maybe it would be better to keep those iterators const, until we really needed non-const ones.  
  
Related, AFAIS `for_each_segment()` is very naiive, it only iterates over the segments directly stored in Range. I.e. for open Polygons it doesn't return the last Segment between the "terminal" Points. I feel that it shouldn't work this way but I don't recall any complaints. Probably noone is using it. But, is this a bug?

---

## Comment 24

> Username: mkaravel  
> Created_at: 2014-09-30 07:13:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57275586  

@awulkiew As it is implemented right now `point_iterator<>` returns/provides access to the points stored.  
  
So for open geometries you access the starting/closing point only once. If your iterator is mutable and you modify the starting point, the closing point will obviously be implicitly modified (as it is not explicitly stored).  
  
For closed geometries you access the starting and closing points separately. If the point iterator it mutable you can modify the starting point of a closed geometry without touching the closing point and vice versa. In that respect you can invalidate the geometry. Honestly, although this an inconventient behavior, it is _consistent_ with what we do with closed geometries anyways: one may define a geometry as closed, but enter different starting and closing points. So I would prefer not to change this behavior.  
  
The `segment_iterator<>` is still non-mutable, so in that respect I cannot modify the geometry.  
  
I agree that for now I would like to keep the segment iterator as non-mutable. If the needs arises we can think about making it mutable, and address any issues that come up (including what you pointed out).  
  
I also consider the behavior of `for_each_segment` buggy, or at least inconsistent with other BG functionalities. For example `num_segments` returns the true number of segments of a geometry (includes the last segment for open geometries). Also the segment iterator includes the last segment. So my personal point of view is that the implementation of `for_each_segment` must change.  
  
BTW, I am currently using `for_each_segment` within `bg::distance` and had assumed that it returns all segments of a geometry (including the implicitly defined one for open geometries). I guess the distance code is buggy because of the `for_each_segment` behavior you just mentioned and I need to fix it. I need to go back to it and make sure everything works correctly.

---

## Comment 25

> Username: mkaravel  
> Created_at: 2014-09-30 07:18:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57275996  

In the last 7 commits, I introduced the `boost::geometry::detail::segment_iterator` namespace.  
All functionality that is not supposed to be visible to the end user and was in the BG namespace is now moved to this new namespace.  
  
The segment iterator is still non-mutable. I have modified the free function prototypes to return `segment_iterator<Geometry const>` in order to make more explicit that the iterator is a const one.  
  
There is one last comment by Adam that I had not replied to: the `segment_iterator_type` class. I have moved it to the `detail::segment_iterator` namespace, so it is not visible to the use right now.  
  
AFAIC, the PR is ready for merging.

---

## Comment 26

> Username: mkaravel  
> Created_at: 2014-09-30 07:20:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57276205  

Next step: restructure the `point_iterator` code. I need to move parts of the point iterator code inside the `detail::point_iterator` namespace. This way both iterators will have the same design/code structure. I will wait for this PR to be merged first, as changes in the point iterator code will slightly affect the segment iterator code.

---

## Comment 27

> Username: awulkiew  
> Created_at: 2014-09-30 10:45:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/141#issuecomment-57295874  

Thanks! I'm merging then.  
  
This probably isn't a good place for discussing this issue but it's related so I guess it's ok.  
  
Taking into account that during the iteration using `point_iterator<>` the last, duplicated Point can be accessed for closed Rings then if someone wanted to modify the Geometry this way he'd be forced to keep track of the number of traversed Points in order to somehow know that the currently accessed Point is the closing one and do something else with it. It would be helpful if we had some method to somehow retrieve this info out from the `point_iterator<>`. And/or some method to access the first Point of a currently traversed range, however in this case the user could just use 2 iterators and also keep the begin of the currently traversed range. So AFAIU now, if someone wanted to modify the Geometry he'd be forced to either traverse the ranges manually or count Points, basically do everything by hand, even with `point_iterator<>`. In this case it would probably be less convenient to use the iterator since the counters should be enough to access Ranges. Therefore we could consider makeing the `point_iterator<>` const as well to be consistent with (probably all?) other iterators.  
  
Another thing, if the dupicated endpoint of a Ring is also returned, the view of the Geometry is somehow obscured by the data. E.g. the rtree containing all Points of a geometry stores those duplicated Points. It probably isn't a problem but maybe stopping at the last Point (not iterating through the last duplicated one) could be convenient, though I'm not sure about it. On the other hand internally we always use closed geometries so maybe `point_iterator<>` should return the first Point for open Rings? This would be consistent, also with `segment_iterator<>` I guess. What do you think?  
On the other hand, in the case of `segment_iterator<>` the last segment could also always refer to Points N-1 and 0, even for closed Rings. However I know that accessing this duplicated Point at the end of a Range means better cacheing.  
  
It's not clear to me what would be better. BTW, Is there a piece of code where `point_iterator<>` must be used with mutable Geometry?

---
