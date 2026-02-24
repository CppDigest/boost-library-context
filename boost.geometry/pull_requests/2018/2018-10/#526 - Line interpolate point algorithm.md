# #526 Line interpolate point algorithm [Merged]

> Username: vissarion  
> Created at: 2018-10-30 09:18:11 UTC  
> Updated at: 2019-03-04 16:25:32 UTC  
> Merged at: 2019-02-27 13:14:58 UTC  
> Closed at: 2019-02-27 13:14:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/526  

This PR proposes an implementation for a new algorithm to interpolate points on a LineString.   
  
The behaviour is similar to the postgis function: https://postgis.net/docs/ST_LineInterpolatePoints.html   
  
The interface is a bit different though. The function `line_interpolate_point(l, fraction, p)` takes a linestring `l` and if `p` is type of `Point` then computes one point on `l` at a given `fraction` of total LineString length. Otherwise if `p` is of type `MultiPoint` it computes as many points as possible on `l` w.r.t. given `fraction`.   
  
Strategies for `line_interpolate_point` algorithm are also implemented, one for each coordinate system.    
  
The PR contains documentation and some examples.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-12-17 01:44:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-447698612  

Thanks. I'll review the PR tomorrow. I have a few questions/suggestions.  
  
Correct me if I'm wrong, this algorithm is PostGis-specific, not defined in the OGC standard. This means we can choose whatever name we like, right? Why not simply `interpolate_point` or `interpolate`? Do you think we could interpolate in a different way?  
  
I think if there are 2 possible outputs we should have 2 algorithms `interpolate_point` and `interpolate_points`. Our options are also limited by the name. We shouldn't return multiple points from algorithm named `xxx_point`. And if we choose a general name, without the part defining the output like `interpolate` only then the output argument has to define the behavior. We already have it in set operations so no problem with that. What do you think?  
  
This algorithm probably share many internals with `densify`. Do you think both could be merged somehow or are they too different or would add unnecessary complexity?

---

## Comment 2

> Username: vissarion  
> Created_at: 2018-12-17 12:08:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-447822886  

@awulkiew I like the idea of general name `interpolate`. I do not see any other way of interpolation apart from points on linestrings. An exception could be interpolation on rings given a fraction of the perimeter but there is a simple workaround to that representing the ring by a linestring.   
  
Regarding `densify` I proposed it this way because otherwise we will add unnecessary complexity, but I have no strong opinion and I am willing to discuss further on this if needed.

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2018-12-17 20:48:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-447993526  

Note, interpolate in a GIS context means, in general, interpolating a value of a measurement at a certain point, located somewhere between other points, or in a grid. See for example here https://en.wikipedia.org/wiki/Kriging or here http://planet.botany.uwc.ac.za/nisl/GIS/spatial/chap_1_11.htm  
  
So we should not use the name interpolate. So maybe prefer interpolate_points like PostGIS does (but only if it has exactly the same meaning).

---

## Comment 4

> Username: awulkiew  
> Created_at: 2018-12-20 16:45:05 UTC  
> Updated_at: 2018-12-20 17:02:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-449061445  

Thanks for the examples. It seems that unless we don't want to contextually define the behavior of `interpolate` (based on parameters) then this algorithm should indeed have a distinct name. `line_interpolate` seems to be ok if the output is defined by the type. Otherwise if we want to be specific about the output it should be a part of the function name, so `line_interpolate_point` and `line_interpolate_points`. I'll also throw some other names in case you liked something: `interpolate_linestring`, `interpolate_linear`, `interpolate_linestring_point`.  
  
Btw, if a function can return N points on a Linestring, would it have sense to return N points on MultiLinestring? AFAIU this would work exactly as Linestring, i.e. the algorithm would have to calculate the length of the whole geometry and then calculate the distance step from length and ratio and use this distance step to generate points.  
  
And since I'm at it. Why does the algorithm need the ratio instead of step distance? This way the algorithm has to always calculate the length of the Linear geometry at the beginning. I'm aware that the lengths of segments of a linestring has to be calculated anyway while interpolating and this could be done once and stored but for 1 point output it's a waste of time since lengths after the interpolated point are not needed. So currently if a user has the ratio the whole length is calculated internally and if he has the distance he has to calculate the whole length, ratio and then the whole length is calculated again internally. However if the algorithm took the distance and a user had ratio then he'd be forced to calculate the length and distance and the algorithm would calculate lengths of segments from the beginning to the interpolated point, and in case he knew the distance the algorithm would calculate only the lengths of segments to the interpolated point. So AFAIU in both cases it means less segments lengths calculations. Would it make sense to take distance in CS units instead of ratio or am I missing something?  
  
EDIT: I'm proabaly missing cartesian segments where we don't need to know the distance to interpolate with ratios. And indeed only because of this it's a better solution.

---

## Comment 5

> Username: vissarion  
> Created_at: 2019-01-02 10:59:17 UTC  
> Updated_at: 2019-01-02 14:10:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-450835860  

>Thanks for the examples. It seems that unless we don't want to contextually define the behavior of interpolate (based on parameters) then this algorithm should indeed have a distinct name. line_interpolate seems to be ok if the output is defined by the type. Otherwise if we want to be specific about the output it should be a part of the function name, so line_interpolate_point and line_interpolate_points. I'll also throw some other names in case you liked something: interpolate_linestring, interpolate_linear, interpolate_linestring_point.  
  
I am more towards a single name, i.e. `line_interpolate` and the output is defined by type, either a `Point` or a `MultiPoint`.   
  
>Btw, if a function can return N points on a Linestring, would it have sense to return N points on MultiLinestring? AFAIU this would work exactly as Linestring, i.e. the algorithm would have to calculate the length of the whole geometry and then calculate the distance step from length and ratio and use this distance step to generate points.  
  
It would not be difficult to add this but I do not know if this makes sense since `MultiLinestring` is a set as far as I understand, so there is no ordering. I would leave it to the user to implement such (easy) functionality while defining his/her own order.   
  
>And since I'm at it. Why does the algorithm need the ratio instead of step distance? This way the algorithm has to always calculate the length of the Linear geometry at the beginning. I'm aware that the lengths of segments of a linestring has to be calculated anyway while interpolating and this could be done once and stored but for 1 point output it's a waste of time since lengths after the interpolated point are not needed. So currently if a user has the ratio the whole length is calculated internally and if he has the distance he has to calculate the whole length, ratio and then the whole length is calculated again internally. However if the algorithm took the distance and a user had ratio then he'd be forced to calculate the length and distance and the algorithm would calculate lengths of segments from the beginning to the interpolated point, and in case he knew the distance the algorithm would calculate only the lengths of segments to the interpolated point. So AFAIU in both cases it means less segments lengths calculations. Would it make sense to take distance in CS units instead of ratio or am I missing something?  
  
>EDIT: I'm proabaly missing cartesian segments where we don't need to know the distance to interpolate with ratios. And indeed only because of this it's a better solution.  
  
I could propose an overload that takes a distance instead of ratio and be optimized for that case (i.e. do not compute the length twice).  
  
  
EDIT: Also note that for many interpolation points if the user knows the distance he/she should use the `densify` algorithm.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-01-03 03:48:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451050226  

> I am more towards a single name, i.e. line_interpolate and the output is defined by type, either a Point or a MultiPoint.  
  
I don't have a strong opinion about it and on one hand I like the short name. @barendgehrels @mloskot what do you think?

---

## Comment 7

> Username: mloskot  
> Created_at: 2019-01-03 09:55:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451097771  

I don't have strong opinion either, but if I was asked to decide, I'd lean towards the PostGIS-like `line_interpolate_point` or `interpolate_point`.  
  
-----  
  
However, since I'm having my morning coffee and I may let myself dive into some art of naming brainstorm :-)  
  
AFAIK, there is no function overloading in PostgreSQL, so `ST_LineInterpolatePoints` has to be verbose with object (`Line`), operation (`Interpolate`) and output (`Points`) encoded.  
  
OTOH, Python's library [Shapely](https://shapely.readthedocs.io/en/stable/manual.html#linear-referencing-methods) which is close to/influenced by PostGIS, defines `LineString.interpolate()` in object-oriented fashion, which returns single point, so `Points` part is missing from name and `Line` part is replaced by the `LineString` instance itself. Hence, `interpolate` is obvious and unambiguous choice.  
  
Since Boost.Geometry provides free function(s) and despite overloading is available, a good name should be balanced between the two approaches mentioned above.  
  
Although all names suggested seems to be good names, if we focus on properties of self-descriptive and discoverable interface, they are slightly different:  
  
- `interpolate_points` or singular `interpolate_point` - may suggest referencing points on objects other than line is also provided;   
- `line_interpolate` - only referencing on line is supported and the `points` part is implicit (what else could be interpolated); for some it may be slightly English-ambiguous suggesting the product of interpolation is actually a line (like in `interpolate_line`);  
- `line_interpolate_point` - PostGIS-like name may be clearest for those familiar with PostGIS; singular `point` seems fine and output type clarifies it is 1 or N points; this name may be easier to search source code for, unambiguously.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2019-01-03 20:36:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451269371  

What is exactly the difference with the densify algorithm? As far as I can see it is quite the same, or maybe nearly exactly the same. Can we not harmonize them? I like the name Densify, it is a clear name.  
I can assume that densify has a linestring-in ->linestring-out, but we can them easily implement it with a linestring-in -> multipoint-out

---

## Comment 9

> Username: vissarion  
> Created_at: 2019-01-04 10:15:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451404807  

> What is exactly the difference with the densify algorithm? As far as I can see it is quite the same, or maybe nearly exactly the same. Can we not harmonize them? I like the name Densify, it is a clear name.  
I can assume that densify has a linestring-in ->linestring-out, but we can them easily implement it with a linestring-in -> multipoint-out  
  
Indeed, this is very similar to `densify`. However, there are (minor) differences. First, the input: `densify` takes distance while `line_interpolate` takes ratio. Of course, one can calculate the former from the latter and vice versa. Second, `line_interpolate` can compute only one point on linestring and then avoid all other points while `densify` not. All those functionalities can be merged to one algorithm `densify` with 3 modes: densify a geometry (as it is now), interpolate a point one a linestring, interpolate a multipoint on a linestring. The question is: do we like such (or similar) interface ?

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2019-01-04 18:35:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451529298  

Thanks. So, then I'm in favour of harmonizing it with densify.  
  
It is no problem if densify results a multipoint i/o a linestring (this we will get for free, it is just the output type). And if you specify a point as output, then it is logical that there is only one point generated.  
  
So basically the output type specifies everything. Which is how our library works. And that we use a ratio i/o a distance, either we can just take a distance (people wanting ratio should recalculate before), or we could make a strategy or policy for this behaviour.  
  
Are there other objections for densify?

---

## Comment 11

> Username: awulkiew  
> Created_at: 2019-01-04 23:48:11 UTC  
> Updated_at: 2019-01-04 23:49:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451602835  

@barendgehrels I'd say that semantically the operation of returning a midpoint of a segment or linestring is not densification. So we'd need a name describing what's happening at higher level of abstraction. What operation can be both densification and linear interpolation at the same time? IMO this would be too confusing. Based on output type the behavior could be any combination of the following:  
- use distance vs ratio  
- return endpoints of segments or not together with midpoints  
- return 1 point or multiple points (or possibly N points passed by the user)  
- support certain input type (should linear interpolation work for multipolygons?)  
  
So merged `densify`/`line_interpolate` could return the original geometry with midpoints (densified) or only some midpoints from the beginning of a geometry (interpolated). "The beginning of a geometry" is also not well-defined for Areal and Multi Geometries.  
  
But all of the above is not the worst thing. Both algorithms have more fundamental difference. Densification works at segment-level, i.e. it densifies segments one by one leaving the endpoints. For the next segment it starts measuring the distance again. Line-interpolate measures the distance from the beginning of a geometry and the measured distances/ratios are preserved between segments. So e.g. if a user passed ratio greater than the length of a segment this algorithm would do the opposite of densification. Yet another difference, semantically densification has to preserve the original shape. In this context interpolation doesn't have sense because the output is different geometry but assuming it has sense the shape may be not preserved during interpolation.  
  
An example of similar case would be to merge `simplify` with `centroid` or `point_on_border` because any geometry could be simplified to its center of mass or any point on boundary. I know these algorithms internally are more different than densify and line_interpolate but semantically it would be similar. So if the main argument is based on the internal similarity of algorithms then maybe we should rather think about merging the internals rather than the interface?  
  
> So basically the output type specifies everything. Which is how our library works.  
  
I thought that the input types define the behavior. There are rare cases like `intersection` where AFAIR various geometries can be returned like e.g. in L/L case the result can be L or P. But AFAIU this is only because of limitation of Boost.Geometry, i.e. lack of GeometryCollections. Also the behavior is well defined, i.e. the output is partial result of intersection for given topoligical dimension (so PointLike part or Linear part). So the output type does not define the input types but the other way around, the input types requires certain output types because of the limitations of the library. Or am I missing something or do you have something else in mind?  
  
Btw, this algorithm could be replaced by iterator (probably would have to be InputIterator) iterating through interpolated points. Then the output would not be a problem because a user could increment the iterator how many times as he wants. What do you think about this?

---

## Comment 12

> Username: barendgehrels  
> Created_at: 2019-01-06 21:16:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451775651  

@awulkiew thanks for your explanations. I'm OK with `line_interpolate` too - no problem.  
  
About iterators, I think the interface should be similar to other algorithms as densify, centroid and the other ones you are mentioning - so let's not create one with an iterator only.

---

## Comment 13

> Username: awulkiew  
> Created_at: 2019-01-07 14:12:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-451946755  

> About iterators, I think the interface should be similar to other algorithms (...)  
  
Right.  
  
I'd like to go back to the ratio vs distance. I'm not sure if it's a good idea to have `line_interpolate` taking ratio. On one hand ratios will be faster for cartesian segment assuming the user has ratio precalculated (and probably only for this case?). On the other hand other algorithms (`densify`, `simplify`, `buffer`) take distance so newly added algorithm should probably be consistent because otherwise the user could be surprised. I'm lightly leaning towards consistency because writing interpolation for cartesian segment at user side would be trivial.

---

## Review 14 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:27:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192643413  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
  52 |+ {
  53 |+     template <typename Point1, typename Point2>
  54 |+     inline void apply(Point1& p1, Point2& p2)
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:27:33 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247890560  

Why do this function take non-const Point while the above function take const?


---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:41:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192649736  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
  70 |+     static inline void apply(Range const& range,
  71 |+                              double const& fraction,
  72 |+                              PointType & p_or_mp,
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:41:30 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247895309  

It can be point or multi-point, correct? Then it is not `PointType`. The name *pointlike* is used for these geometries, e.g. `pointlike_tag`. So you could use `PointLike & p_or_mp` or `PointLike & pointlike`.


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:44:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192650875  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 111 |+         iterator_t prev = it++;
 112 |+         do {
 113 |+             result_type len = strategy.compute(*prev, *it);
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:44:11 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247896208  

I suggest to be more precise with the name. Compute what? `strategy.distance()`, `strategy.comparable_distance()`. Another approach could be to return the distance strategy from this strategy and use it directly.

> Username: awulkiew  
> Created_at: 2019-01-15 14:32:23 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247914112  

Right, so this is not *length* but is an object of a type holding distance or distance and azimuth. Then I'd suggest to change the name. Even some generic `result` or `res` would be better IMO because they don't lead the reader to *length*.


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:47:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192652373  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 110 |+         vector_t lengths;
 111 |+         iterator_t prev = it++;
 112 |+         do {
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:47:34 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247897372  

If there is only one point in the range at this point `it` will point outside range. Why not use for-loop?


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:48:22 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192652766  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 100 |+             return;
 101 |+         }
 102 |+         if (fraction == 1)
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:48:22 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247897644  

What if the fraction is > 1?


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:48:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192652822  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
  95 |+             return;
  96 |+         }
  97 |+         if (fraction == 0)
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:48:30 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247897694  

What if the fraction is < 0?


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 13:50:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192653812  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 127 |+         bool single_point = false;
 128 |+ 
 129 |+         do {
```

> Username: awulkiew  
> Created_at: 2019-01-15 13:50:37 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247898497  

If there is only 1 point in range it will be equal to end here and possibly point to garbage. Why not for-loop?


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:07:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192661848  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 139 |+                                (repeated_fraction - prev_fraction) / seg_fraction,
 140 |+                                p, *vit);
 141 |+                 single_point = boost::is_same<Policy, convert_and_assign>::value;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:07:43 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247904535  

It is known at compile-time. Why not handle point and multipoint in separate functions or even have `if`-condition before?

> Username: vissarion  
> Created_at: 2019-02-12 10:41:16 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r255895890  

I removed the `bool` variable and changed the condition to be more understandable here: https://github.com/boostorg/geometry/pull/526/commits/a2e355a683d7aeb64ada41c50e604342ffe69209   
I do not use separate functions for compactness, but I do not know what you have in mind.

> Username: awulkiew  
> Created_at: 2019-02-12 11:39:36 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r255915681  

I will check the new version but to clarify what I had in mind. One function/struct for point calling the strategy once and a separate function for multipoint with a loop. This way it would be clear what's happening in which case.  
  
There is also one additional issue here, i.e. the behavior is based on the Policy type. Why not on the geometry tag? This would be more clear too. Or am I missing something?  
  
But anyway, I'll check the new version.


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:12:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192664120  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
 121 |+         calc_t prev_fraction = 0;
 122 |+         calc_t cur_fraction = 0;
 123 |+         unsigned int i = 1;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:12:38 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247906415  

I'm not sure if `unsigned int` is a proper type here since on x64 it will still be 32b type. What do you think about changing it to `std::size_t`? It's for counting fractions, not elements of range but since fraction is typically in range [0, 1] and theoretically there may be more points in the output than it is in the input then using at least as big type to count fractions as we use to count elements has sense.


---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:13:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192664587  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
  82 |+                 typename default_length_result<Range>::type,
  83 |+                 double
  84 |+             >::type calc_t;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:13:37 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247906807  

Doesn't `default_length_result` already promote to `double`?


---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:23:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192669474  

📁 include/boost/geometry/algorithms/line_interpolate_point.hpp

```diff
  85 |+ 
  86 |+         typedef typename Strategy::template result_type<point_t> result_type;
  87 |+         typedef boost::container::vector<result_type> vector_t;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:23:50 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247910693  

Is there a reason why `std::vector` is not used here? It'd mean less dependencies.

> Username: awulkiew  
> Created_at: 2019-01-15 14:25:26 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247911316  

In case we decided that distance should be passed by the user instead of fraction this vector wouldn't be needed.


---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:33:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192674497  

📁 include/boost/geometry/strategies/cartesian/line_interpolate_point.hpp

```diff
  58 |+ 
  59 |+         result_type() :
  60 |+             distance(0)
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:33:50 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247914739  

result_type()  
        : distance(0)

> Username: awulkiew  
> Created_at: 2019-01-15 14:48:57 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247920972  

See: https://github.com/boostorg/geometry/wiki/Guidelines-for-Developers#code-formatting-and-indentation


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:34:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192674791  

📁 include/boost/geometry/strategies/cartesian/line_interpolate_point.hpp

```diff
  62 |+ 
  63 |+         result_type(calc_t d) :
  64 |+             distance(d)
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:34:27 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247914992  

explicit result_type(calc_t d)  
        : distance(d)


---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:36:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192676148  

📁 include/boost/geometry/strategies/geographic/line_interpolate_point.hpp

```diff
  70 |+         result_type() :
  71 |+             distance(0),
  72 |+             azimuth(0)
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:36:58 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247916025  

result_type()  
        : distance(0)  
        , azimuth(0)


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:40:01 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192677732  

📁 include/boost/geometry/strategies/cartesian/line_interpolate_point.hpp

```diff
  55 |+                 typename coordinate_type<Point>::type,
  56 |+                 CalculationType
  57 |+             >::type calc_t;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:40:01 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247917248  

`CalculationType` can be `void`. You should rather use `select_calculation_type_alt`. See: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/select_calculation_type.hpp#L64  
  
EDIT: Another problem with that is if the user passes `CalculationType` the strategy should use that even if the type is less precise than `coordinate_type`.


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:43:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192679697  

📁 include/boost/geometry/strategies/cartesian/line_interpolate_point.hpp

```diff
  86 |+                 typename coordinate_type<Point>::type,
  87 |+                 CalculationType
  88 |+             >::type calc_t;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:43:41 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247918812  

To be consistent: `select_calculation_type_alt<CalculationType, Point>::type`.

> Username: awulkiew  
> Created_at: 2019-01-15 14:46:46 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247920074  

Actually you should probably do the same as in other strategies, so: `result_type<Point>::calc_t`.


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:44:55 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192680301  

📁 include/boost/geometry/strategies/geographic/line_interpolate_point.hpp

```diff
  66 |+                 typename coordinate_type<Point>::type,
  67 |+                 CalculationType
  68 |+             >::type calc_t;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:44:55 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247919284  

`select_calculation_type_alt`


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:47:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192681838  

📁 include/boost/geometry/strategies/cartesian/line_interpolate_point.hpp

```diff
  80 |+                       T1 const& fraction,
  81 |+                       Point & p,
  82 |+                       T2 const&) const
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:47:42 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247920441  

`T2` is actually `result_type<Point>` isn't it?


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:48:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192682068  

📁 include/boost/geometry/strategies/geographic/line_interpolate_point.hpp

```diff
  75 |+         result_type(calc_t d, calc_t a) :
  76 |+             distance(d),
  77 |+             azimuth(a)
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:48:12 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247920643  

result_type(calc_t d, calc_t a)  
        : distance(d)  
        , azimuth(a)


---

## Review 33 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:50:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192683083  

📁 include/boost/geometry/strategies/spherical/line_interpolate_point.hpp

```diff
  53 |+     struct result_type
  54 |+     {
  55 |+         typedef typename select_most_precise
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:50:07 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247921430  

different util.


---

## Review 34 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:50:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192683347  

📁 include/boost/geometry/strategies/spherical/line_interpolate_point.hpp

```diff
  80 |+                       T2 const& dist_az) const
  81 |+     {
  82 |+         typedef typename select_most_precise
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:50:34 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247921629  

take `calc_t` from `result_type`.


---

## Review 35 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:51:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192683843  

📁 include/boost/geometry/strategies/spherical/line_interpolate_point.hpp

```diff
  70 |+                                       Point const& p1) const
  71 |+     {        
  72 |+         return result_type<Point>(DistanceStrategy().apply(p0,p1));
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:51:25 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247922016  

If we decided to use distance instead of fraction, radius would be important.


---

## Review 36 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:52:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192684261  

📁 include/boost/geometry/strategies/spherical/line_interpolate_point.hpp

```diff
  78 |+                       T1 const& fraction,
  79 |+                       Point & p,
  80 |+                       T2 const& dist_az) const
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:52:10 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247922329  

`result_type<Point> const&`


---

## Review 37 [Commented]

> Username: awulkiew  
> Created_at: 2019-01-15 14:55:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-192685950  

📁 include/boost/geometry/strategies/spherical/line_interpolate_point.hpp

```diff
  97 |+         calc_t const angle01 = dist_az.distance;
  98 |+ 
  99 |+         point3d_t axis;
```

> Username: awulkiew  
> Created_at: 2019-01-15 14:55:20 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r247923631  

This code is from `densify` strategy, isn't it?  
From here: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/strategies/spherical/densify.hpp#L99  
We should probably move it to formulas.


---

## Comment 38

> Username: awulkiew  
> Created_at: 2019-01-15 14:59:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-454421731  

There are some places to fix before merging.  
Plus, the decisions to be made regarding the name and the distance vs fraction.

---

## Comment 39

> Username: vissarion  
> Created_at: 2019-01-24 13:10:21 UTC  
> Updated_at: 2019-01-24 13:11:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-457190789  

@awulkiew thanks for the review! I would like to agree on distance vs fraction before doing any changes. I am ok with using distance and I understand the arguments. Just to note that in the case we provide an interface with distance and the user has the ratio only then she has to compute the length of the line to compute the distance but then the algorithm cannot use that computation hence it will recompute distances of segments leading to at most twice distance computations. I do not know whether we are interested to support more efficient interfaces for fraction. That may lead to a new interface or maybe new algorithm or we can just avoid that for now. What do you think?

---

## Review 40 [Approved]

> Username: awulkiew  
> Created_at: 2019-02-27 12:36:55 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-208496653  

Thanks. I'm ok with merging.

📁 include/boost/geometry/algorithms/line_interpolate.hpp

```diff
 358 |+ >
 359 |+ inline void line_interpolate(Geometry const& geometry,
 360 |+                                    Distance const& max_distance,
```

> Username: awulkiew  
> Created_at: 2019-02-27 12:32:16 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r260727017  

Indentation could be improved here.

> Username: vissarion  
> Created_at: 2019-02-27 13:14:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r260741759  

Thanks, I fixed this along with few others

---

📁 include/boost/geometry/algorithms/line_interpolate.hpp

```diff
 395 |+ template<typename Geometry, typename Distance, typename Pointlike>
 396 |+ inline void line_interpolate(Geometry const& geometry,
 397 |+                                    Distance const& max_distance,
```

> Username: awulkiew  
> Created_at: 2019-02-27 12:33:30 UTC  
> Updated_at: 2019-02-27 13:13:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r260727321  

And here.


---

## Comment 41

> Username: awulkiew  
> Created_at: 2019-02-27 12:37:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-467846351  

There seems to be some conflicts.

---

## Comment 42

> Username: barendgehrels  
> Created_at: 2019-02-27 22:23:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#issuecomment-468054959  

Thanks!

---

## Review 43 [Commented]

> Username: awulkiew  
> Created_at: 2019-03-04 16:24:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/526#pullrequestreview-210235405  

📁 include/boost/geometry/algorithms/line_interpolate.hpp

```diff
  45 |+         typename boost::range_value<Range>::type p2;
  46 |+         geometry::detail::conversion::convert_point_to_point(p, p2);
  47 |+         range::push_back(range, p2);
```

> Username: awulkiew  
> Created_at: 2019-03-04 16:24:23 UTC  
> Updated_at: 2019-03-04 16:25:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/526#discussion_r262135876  

This line causes error with msvc-14.0:  
  
    error C2039: 'push_back': is not a member of 'boost::geometry::detail::interpolate_point::range<Policy>'


---
