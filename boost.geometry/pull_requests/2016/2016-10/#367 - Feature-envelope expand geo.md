# #367 Feature/envelope expand geo [Merged]

> Username: vissarion  
> Created at: 2016-10-27 10:40:19 UTC  
> Updated at: 2017-03-09 11:03:26 UTC  
> Merged at: 2017-02-06 21:54:20 UTC  
> Closed at: 2017-02-06 21:54:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/367  

### Problem: compute the envelope and expand functions on segments on spheroid  
  
The core procedure when computing the envelope of a segment is to compute the segment's vertex latitude. That is, given a segment compute the point with the highest latitude, called vertex latitude. Note that on sphere/ellipsoid this could be higher than the maximum latitude of the segment's endpoints.   
  
### Solution  
  
This PR implements formulas proposed in   
  
`Wood - Vertex Latitudes on Ellipsoid Geodesics, SIAM Rev., 38(4), 637–644, 1996`   
  
It is implemented in formulas/vertex_latitude.hpp for both geographic and spherical cases. It also adds strategies for azimuth computation for all coordinate systems. Then the algorithm envelope (or expand) will call the right azimuth strategy and the right vertex latitude formula depending on the coordinate system. Several tests are also added.  
  
### Comments  
- The solution is expected to be much faster than computing the shortest distance between the segment and the North (or South) pole. Note that the later is a correct (but slower) solution for the problem.   
- Azimuth algorithm for spherical segments was altered in order to use an azimuth formula. This is more consistent to geographic azimuth, i.e. the algorithm calls a formula. Then the azimuth formula is directly used by the vertex latitude formula.   
- Rings that cover more than half of the earth are not supported which is the same as in area i.e. if we alter the orientation of an exterior ring then the area will be negative and not area(earth) - area(ring)  
  
*** EDIT / REVISION ***  
Instead of [Wood96] formula we now use a simpler formula (that utilize the auxiliary sphere) cf. commit 3fda39d

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2016-12-01 17:14:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-264233837  

I'm OK with merging

---

## Comment 2

> Username: awulkiew  
> Created_at: 2017-01-16 18:04:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-272928502  

So currently `envelope()` expects azimuth strategy. I'd rather expect it taking a strategy calculating an envelope of a Segment, plus specific/simple versions for Point and Box (which could be not implemented as it is now). From user's perspective passing azimuth strategy into the envelope() doesn't make sense. He/she should rather pass some envelope strategy, just like distance strategy is passed into the distance algorithm and area strategy into the area algorithm.  
  
Plus with the segment envelope strategy the interface would be the same if we decided to support bezier or arc segments at some point. Not that I'm planning to do this in the near future, this simply tells me that this is more correct level of abstraction.  
  
On the other hand azimuth strategy could also be used by `azimuth()` algorithm if we decided to add it (I remember users asking about it).  
  
What do you think?

---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2017-01-17 01:46:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/367#pullrequestreview-16854839  

📁 include/boost/geometry/algorithms/detail/envelope/interface.hpp

```diff
 130 |+     // TODO put this into a resolve_strategy stage
 131 |+     //      (and take the return type from resolve_variant)
 132 |+     typedef typename point_type<Geometry>::type point_type;
```

> Username: awulkiew  
> Created_at: 2017-01-16 17:38:31 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r96277217  

Yes, this should be done (TODO). So in 2 parameter envelope pass object of type `bg::default_strategy` into the `resolve_variant::envelope<>` and specialize for this type above.


📁 include/boost/geometry/algorithms/detail/expand/interface.hpp

```diff
 123 |-     resolve_variant::expand<Geometry>::apply(box, geometry);
 155 |+     // TODO put this into a resolve_strategy stage
 156 |+     //      (and take the return type from resolve_variant)
```

> Username: awulkiew  
> Created_at: 2017-01-16 17:39:19 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r96277327  

TODO


📁 include/boost/geometry/formulas/vertex_latitude.hpp

```diff
 102 |+     /*
 103 |+     template <typename T>
 104 |+     inline static void sign_adjastment(CT lat1, CT lat2, CT vertex_lat, T& vrt_result)
```

> Username: awulkiew  
> Created_at: 2017-01-16 17:39:39 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r96277372  

Is the word "adjastment" ok?


📁 include/boost/geometry/strategies/azimuth.hpp

```diff
  27 |+ template <typename Tag, typename CalculationType>
  28 |+ struct default_strategy
  29 |+ {};
```

> Username: awulkiew  
> Created_at: 2017-01-16 17:40:11 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r96277447  

Here there should be static assert (see other algorithms), e.g.:  
  
    BOOST_MPL_ASSERT_MSG  
    (  
        false, NOT_IMPLEMENTED_FOR_THIS_TAG  
        , (types<Tag>)  
    );


📁 include/boost/geometry/strategies/cartesian/azimuth_cartesian.hpp

```diff
  37 |+                       CalculationType& a2) const
  38 |+     {
  39 |+         // not implemented
```

> Username: awulkiew  
> Created_at: 2017-01-16 17:40:41 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r96277522  

If `azimuth::services::default_strategy` had static/mpl assert this whole file wouldn't be needed.  
  
As an alternative we could simply implement it. Wouldn't it be something like:  
  
    a1 = a2 = atan2(y2 - y1, x2 -x1);  
  
?


---

## Comment 4

> Username: vissarion  
> Created_at: 2017-01-17 15:27:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-273200942  

Thanks Adam for the comments. I agree about the azimuth strategy. What I did was to make the current BG code work with as little changes as possible. For a more convenient interface I propose a strategy e.g. segment_mbr(Point p1, Point p2, Box& mbr) that compute the envelope of segment (p1,p2) and stores it to mbr. This is not far from the interface of distance strategies that take two points and return the distance. Similarly, azimuth strategies could take points as parameters instead of coordinates (as it is in the current code of this PR).  
  
What do you think?

---

## Comment 5

> Username: awulkiew  
> Created_at: 2017-01-17 17:28:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-273238204  

I think this is the correct approach.  
  
It'd have to take the Spheroid and CalculationType as template parameters, correct?  
  
TLDR: I think that either 2 input points and 1 mbr or 2 input points and 2 output corners are ok. It's because if you have at least points (as opposed to plain coordinates) then the information about the CS units is there for you. In some cases it may be beneficial to know it (e.g. for normalization purposes). The former (the one you proposed) seems to be slightly better because we already have strategies taking Boxes (e.g. within Box/Box Pt/Box or distance Box/Box) and because in this case more compile-time information is preserved.

---

## Comment 6

> Username: vissarion  
> Created_at: 2017-01-18 10:48:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-273443774  

Yes, there are two template parameters: Spheroid and CalculationType. Then there is a choice of having one more, namely the inverse formula, e.g. geometry::formula::thomas_inverse or do it as in distance and create one strategy per inverse formula. The pos of the former is that it is more compact but exposes the user to use formulas for parametrization.

---

## Comment 7

> Username: vissarion  
> Created_at: 2017-01-23 13:27:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-274487484  

As discussed above I moved all the CS related computation from the algorithm level to the strategy level. Any comments?

---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2017-01-25 14:14:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/367#pullrequestreview-18402386  

📁 include/boost/geometry/formulas/envelope_segment.hpp

```diff
 209 |+                              CalculationType lat2,
 210 |+                              Box& mbr,
 211 |+                              Strategy const& strategy)
```

> Username: awulkiew  
> Created_at: 2017-01-25 13:58:41 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r97782373  

This code (probably content of the whole file) shouldn't be in `formulas`, it defines how the strategy is used so it should be on the algorithm side, so in the `algorithms/detail/envelope` directory and in `namespace bg::detail::envelope`.


📁 include/boost/geometry/strategies/cartesian/envelope_segment.hpp

```diff
  21 |+ 
  22 |+ template<typename CalculationType>
  23 |+ class segment_cartesian
```

> Username: awulkiew  
> Created_at: 2017-01-25 14:11:01 UTC  
> Updated_at: 2017-02-02 11:41:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#discussion_r97784746  

`CalculationType = void`  
  
Regarding the consistency of the names, we'd have `strategy::area::geographic` and `strategy::azimuth::geographic` so my guess as a user would be to expect the name of the CS right after the name of the strategy "category" or algorithm name. So I was thinking about renaming it to `envelope::cartesian_segment` and `envelope::geographic_segment` (we also have `intersection::relate_cartesian_segments` with the name of the geometry at the end). And this way it'd also be more pleasant for english speakers. What do you think?


---

## Comment 9

> Username: vissarion  
> Created_at: 2017-01-31 15:38:12 UTC  
> Updated_at: 2017-01-31 15:38:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-276397949  

I moved the envelope computation back to algorithm level and also changed the names `segment_cartesian` to `cartesian_segment` etc.

---

## Comment 10

> Username: awulkiew  
> Created_at: 2017-02-01 23:53:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-276822127  

@vissarion Unfortunately after merging area PR https://github.com/boostorg/geometry/pull/359 there are some conflicts.

---

## Comment 11

> Username: vissarion  
> Created_at: 2017-02-02 11:52:11 UTC  
> Url: https://github.com/boostorg/geometry/pull/367#issuecomment-276937707  

@awulkiew Thanks for the message, I resolved the conflicts.

---
