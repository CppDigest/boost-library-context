# #997 Add support for GeometryCollection in difference() [Merged]

> Username: awulkiew  
> Created at: 2022-05-07 14:24:04 UTC  
> Updated at: 2022-05-24 14:45:29 UTC  
> Merged at: 2022-05-24 14:45:29 UTC  
> Closed at: 2022-05-24 14:45:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/997  

Add also:  
- `make_rtree()` utility making an rtree of GC elements  
- `geometry_collection_view<>` allowing to represent any geometry as GC  
- tests  
  
This PR also adds support for difference(g1, g2) where topological dimension of g1 is greater than of g2 so the result is always g1, e.g. Polygon \ MultiPoint.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2022-05-11 08:23:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/997#pullrequestreview-968881365  

📁 include/boost/geometry/algorithms/difference.hpp

```diff
  21 |- 
  18 | #include <boost/geometry/algorithms/detail/intersection/multi.hpp>
  19 |+ #include <boost/geometry/algorithms/detail/make_rtree.hpp>
```

> Username: barendgehrels  
> Created_at: 2022-05-11 08:23:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r870012596  

Did we have a dependency on `rtree` in the non-index part of the library before?  
  
Usually all the overlay algorithms use partition. Why do we need an rtree now for the same (didn't review all yet, but it's probably the same) purpose?

> Username: awulkiew  
> Created_at: 2022-05-12 13:06:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r871357210  

Yes, rtree is used in distance, relate and within.  
  
Here r-tree is used only at the level of GC elements. When difference of elements is calculated partition is still used internally (and calculated each time potentially for the same segments, an index created lazily and passed into get_turns would probably be better is this case).  
  
The code using rtree is simpler and more readable. Partition requires to write visitor and policies which makes the code several times longer than it could be. With partition parts of an algorithm are separated from the rest of the algorithm because they have to be implemented in a visitor somewhere else.  
  
With rtree calculating subsequent subtractions of geometries from one geometry is also more straightforward because with partition the order of processed overlapping geometries is undefined.  
  
Using partition makes sense if an algorithm allows to be interrupted. Here we have to analyze all of the overlapping geometries.  
  
AFAIU partition performs space partitioning WRT both geometries (in order to allow interruption) which is not needed here because there is no interruption. An index of only one needs to be calculated.

> Username: barendgehrels  
> Created_at: 2022-05-22 07:13:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r878808166  

Thanks for your explanation! I agree with you that code using partition is less readable.  
I'm fine.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2022-05-18 11:00:22 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/997#pullrequestreview-976737500  

Thanks Adam! Look OK to me.

📁 include/boost/geometry/algorithms/detail/make_rtree.hpp

```diff
  55 |+     using rtree_t = index::rtree<value_t, rtree_parameters_t>;
  56 |+ 
  57 |+     // TODO: get rid of the temporary vector
```

> Username: vissarion  
> Created_at: 2022-05-18 10:41:11 UTC  
> Updated_at: 2022-05-18 11:00:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r875742891  

what is the obstacle of doing it in this PR?

> Username: awulkiew  
> Created_at: 2022-05-18 22:03:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r876418546  

Short version: it would be a lot more complex than is required by this PR.  
  
Long version: this would require an iterator which would be passed into the rtree constructor directly instead of the temporary vector. This iterator would have to traverse geometry collection recursively as it's done in `visit_breadth_first_impl` and e.g. return iterator of the GC which we could then transform with e.g. `boost::transform_iterator` into envelope-GC-iterator pair on the fly.

> Username: vissarion  
> Created_at: 2022-05-19 09:31:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r876835689  

Cool. Thanks for both versions!


📁 include/boost/geometry/algorithms/difference.hpp

```diff
 423 |+ 
 424 |+ private:
 425 |+     // Implemented as separate function because msvc is unable to do nested lambda capture
```

> Username: vissarion  
> Created_at: 2022-05-18 10:44:46 UTC  
> Updated_at: 2022-05-18 11:00:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r875745707  

what version of MSVC does not support this?

> Username: awulkiew  
> Created_at: 2022-05-18 22:06:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r876420249  

I tested with msvc-14.1 i.e. VS2017.

> Username: vissarion  
> Created_at: 2022-05-19 09:30:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/997#discussion_r876834882  

OK thanks! Side note, it could have been fixed in VS2019   
https://developercommunity.visualstudio.com/t/this-captured-in-nested-lambda-when-used-in-captur/292128


---
