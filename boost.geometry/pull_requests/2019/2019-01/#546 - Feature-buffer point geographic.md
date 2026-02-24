# #546 Feature/buffer point geographic [Merged]

> Username: barendgehrels  
> Created at: 2019-01-16 11:31:19 UTC  
> Updated at: 2019-03-14 20:42:03 UTC  
> Merged at: 2019-02-23 13:03:24 UTC  
> Closed at: 2019-02-23 13:03:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/546  

As discussed, this PR implements a strategy for buffers around points in the Geographic coordinate system.  
  
Besides the strategy, it needed some adaptions in the buffer code.  
  
Included are a unit test and documentation.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2019-01-30 15:31:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-458987295  

Is it OK to merge this?

---

## Comment 2

> Username: awulkiew  
> Created_at: 2019-01-30 15:38:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-458989934  

Sorry that it takes me so long to review it. I'll do this this week.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-01-30 16:15:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-459005072  

No problem

---

## Comment 4

> Username: vissarion  
> Created_at: 2019-01-30 18:37:19 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-459058043  

I am also delayed in this. I'll try to do it the next days (while travelling) otherwise beginning of next week (if this is not an issue).

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-01-31 17:37:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-459435212  

Hi @vissarion , thanks - no problem, no hurry necessary.

---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:19:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200499652  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 770 |-                     pc.offsetted_count++;
 778 |+                         pc.robust_ring.insert(boost::begin(pc.robust_ring) + index_in_vector, rit->point);
 779 |+                         pc.offsetted_count++;
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:19:11 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254210627  

tab? ;)


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:19:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200499810  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 778 |- #endif
 786 |+             BOOST_GEOMETRY_ASSERT(assert_indices_in_robust_rings());
 787 |+         }
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:19:33 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254210747  

tab


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:22:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200501290  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 225 |+         return analyse_continue;
 226 |+     }
 227 |+ };
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:22:53 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254211901  

Tabs. Do you have an idea why does github display the `;` differently here?

> Username: barendgehrels  
> Created_at: 2019-02-09 16:14:45 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255303657  

; differently might be caused by me, splitting a free function to a member function of a class, and that might also confuse you for the 'tabs'. Because as far as I can see they are all right.


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:26:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200502951  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 314 |+         typedef typename geometry::coordinate_type<point_type>::type coordinate_type;
 315 |+ 
 316 |+         typedef strategy::within::winding<point_type> strategy_type;
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:26:43 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254213269  

Actually now there are separate winding strategies for each coordinate system. This one is there for backward compatibility and internally calls one of the CS specific ones based on PointType, see: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/agnostic/point_in_poly_winding.hpp#L63. So my guess is that you should get this strategy from the one passed into the buffer.

> Username: barendgehrels  
> Created_at: 2019-02-09 17:16:25 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255305715  

Done, can you verify if this is what you intended?


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:29:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200503940  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 755 |+             // TODO: this point_in_geometry is a performance-bottleneck here and
 756 |+             // will be replaced completely by extending analyse_piece functionality
 757 |+             return detail::within::point_in_geometry(turn.robust_point, piece.robust_ring);
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:29:04 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254214047  

You should pass CS-specific winding strategy extracted from buffer strategy into this algorithm.

> Username: barendgehrels  
> Created_at: 2019-02-09 16:10:19 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255303505  

Thank you

> Username: barendgehrels  
> Created_at: 2019-02-09 17:16:49 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255305725  

Also here: done, can you verify if this is what you intended?


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:29:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200504126  

📁 include/boost/geometry/algorithms/detail/buffer/turn_in_piece_visitor.hpp

```diff
 768 |+     static inline int apply(Turn const& turn, Piece const& piece)
 769 |+     {
 770 |+         return detail::within::point_in_geometry(turn.robust_point, piece.robust_ring);
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:29:30 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254214186  

Needs explicit winding strategy.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:40:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200508781  

📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
  48 |+     typename CalculationType = void
  49 |+ >
  50 |+ class point_circle_geographic
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:40:53 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254217758  

The naming is slightly inconsistent with already existing geographic strategies. We have:  
- `strategy::distance::geographic`  
- `strategy::side::geographic`  
- `strategy::intersection::geographic_segments`  
- `strategy::distance::geographic_cross_track`  
  
So my guess is that this should be called `geographic_point_circle`.  
  
Side note: I noticed that the naming of strategies is inconsistent with the directories structure. Typically we want to have namespaces aligned with directories. Should there be CS-specific namespaces in `strategy` namespace resembling CS-specific directories in *strategies* directory? For example:   
- `strategy::geographic::distance::point_point`  
- `strategy::geographic::distance::point_segment`  
- `strategy::geographic::intersection::segments`  
- `strategy::geographic::side::point_segment`  
- `strategy::cartesian::distance::point_point` <=> `strategy::cartesian::distance::pythagoras`  
Just a side question for the future.

> Username: vissarion  
> Created_at: 2019-02-06 14:13:13 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254285307  

I agree with the name `geographic_point_circle` and also with the side note. Just to add that we probably need to change also names like pythagoras and andoyer to something to remind that it is Euclidean distance and first order approximation of geographic/ellipsoidal distance.

> Username: barendgehrels  
> Created_at: 2019-02-09 16:09:37 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255303485  

We have:  
  
- strategy::buffer::point_circle  
- strategy::buffer::point_square  
And therefore I selected the fitting:  
- strategy::buffer::point_circle_geographic  
  
But yes, it can also be  
- strategy::buffer::geographic_point_circle  
  
If you prefer that (and apparently you do) then I will change it.  
  
We can actually not rename existing strategies (pythagoras) because they might be used by users.

> Username: awulkiew  
> Created_at: 2019-02-10 00:23:33 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255318965  

Right, but we can add another strategy with a different name, deprecate the old one by putting the `#warning` in the strategy header and remove the include from main header `geometry.hpp`.  
  
The above is also true for `strategy::buffer::point_circle` to be consistent it could be renamed to `strategy::buffer::cartesian_point_circle`. But I'd like to propose something different.  
  
Correct me if I'm wrong. In the future other geographic and spherical strategies could be implemented. Furthermore as it is now the interface of `buffer()` would allow passing various-CS strategies at the same time, e.g. one for cartesian, one geographic, yet another spherical, etc. If I'm correct and it's the case I propose to redesign the interface by:  
- allowing to pass 1 CS-specific buffer strategy as the last, additional parameter of `buffer()`  
- treating current strategies as policies of this strategy  
This way the parameters would be consistent by design.

> Username: barendgehrels  
> Created_at: 2019-02-10 10:01:16 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255330779  

Yes, indeed, you are right. It currently would allow that (though we could protect that).  
So I can do that later - for now I propose to rename it as you both agreed (didn't rename it yet - will do tonight). That is the last point of this review and then I think it can be merged.


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2019-02-06 10:45:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200510890  

📁 test/algorithms/buffer/test_buffer_geo.hpp

```diff
  49 |+         bg::strategy::thomas, 5,
  50 |+         bg::srs::spheroid<long double>, long double
  51 |+     > area_strategy;
```

> Username: awulkiew  
> Created_at: 2019-02-06 10:45:58 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254219460  

Just for cross-reference. Issues related to this:  
https://github.com/boostorg/geometry/issues/541  
https://github.com/boostorg/geometry/issues/542


---

## Comment 14

> Username: awulkiew  
> Created_at: 2019-02-06 10:47:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#issuecomment-460979232  

Thanks. I'm ok with merging after addressing some minor issues.

---

## Review 15 [Commented]

> Username: vissarion  
> Created_at: 2019-02-06 14:33:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-200586296  

I am ok with merging. I have a few minor comments. Also it seems that the algorithm does not work correctly when the point is a pole. Could you please verify?

📁 test/algorithms/buffer/buffer_point_geo.cpp

```diff
   8 |+ // http://www.boost.org/LICENSE_1_0.txt)
   9 |+ 
  10 |+ #include <test_buffer_geo.hpp>
```

> Username: vissarion  
> Created_at: 2019-02-06 13:56:26 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254279032  

the use of "" instead of <> is more appropriate here, right?

> Username: barendgehrels  
> Created_at: 2019-02-09 17:32:09 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255306297  

Yes, we're not so consequent in that indeed.  
Fixed all for buffer tests


📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
  44 |+ template
  45 |+ <
  46 |+     typename FormulaPolicy = strategy::andoyer,
```

> Username: vissarion  
> Created_at: 2019-02-06 14:15:31 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r254286183  

why not `thomas` same as the one used in tests to parametrize `area`?

> Username: barendgehrels  
> Created_at: 2019-02-09 17:20:38 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255305860  

All strategies do it like this (e.g. area, azimuth, densify, etc)  
It should not necessarily be aligned with test code, should it?  
Of course - we could change all with tomas if you advise that and it is fast enough.  
But for now I would keep it consistent with the other strategies

> Username: vissarion  
> Created_at: 2019-02-11 07:41:25 UTC  
> Updated_at: 2019-02-23 13:01:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r255399084  

ok maybe it is not needed to be aligned with the tests, this could be changed together with area default strategy in a separate PR


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2019-03-14 20:42:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/546#pullrequestreview-214752744  

📁 include/boost/geometry/strategies/geographic/buffer_point_circle.hpp

```diff
  93 |+         // TODO: after calculation of some angles is corrected,
  94 |+         // we can start at 0.0
  95 |+         calculation_type angle = 0.001;
```

> Username: awulkiew  
> Created_at: 2019-03-14 20:42:02 UTC  
> Updated_at: 2019-03-14 20:42:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/546#discussion_r265757860  

I wanted to fix it. How can I verify that the angles are correct or incorrect? I didn't find any test case in the test file which was commented out due to this. Do you have in mind this issue: https://github.com/boostorg/geometry/issues/542 or is this a different issue?  
  
I'm asking because I wanted to fix it by making the azimuth passed into the direct formula to be in valid range (-pi, pi], like this in the loop below:  
  
    if (angle > pi)  
        angle -= two_pi;  
  
and also to start from 0. I verified that this changes the coordinates but causes more cases to fail. And I don't know if this is because the coordinates are incorrect or that it's inaccurate area's fault.  
  
My understanding is that this `0.001` magic number only hides the problem in the specific several cases in test file. Is that correct? Because in general case if the intermediate results from some formulas are incorrect it doesn't matter where is the starting point since problematic angles can be achieved in various ways (i.e. by also tweaking the point count or changing the starting point).


---
