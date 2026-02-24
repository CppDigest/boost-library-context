# #707 New algorithm closest_points [Closed]

> Username: vissarion  
> Created at: 2020-04-29 12:02:26 UTC  
> Updated at: 2022-11-16 14:11:51 UTC  
> Closed at: 2021-11-03 09:00:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/707  

## The problem   
This PR proposes an implementation of closest points algorithm. That is given two geometries G1, G2 compute the pair of points p1 \in G1, p2 \in G2 such that the distance between p1, p2 is minimized.   
In case that G1, G2 have an non empty intersection the function returns an (arbitrary) common point. See also issue  https://github.com/boostorg/geometry/issues/633  
A similar function in `PostGIS` is the `ST_ClosestPoint` https://postgis.net/docs/ST_ClosestPoint.html  
  
## Example  
### Input:   
POINT(4.3 1.9);  
POLYGON((2 1.3,2.4 1.7,2.8 1.8,3.4 1.2,3.7 1.6,3.4 2,4.1 3,5.3 2.6,5.4 1.2,4.9 0.8,2.9 0.7,2 1.3)(4.0 2.0, 4.2 1.4, 4.8 1.9, 4.4 2.2, 4.0 2.0))  
LINESTRING(5 0, 2 0));  
MULTIPOINT((2 0.5,2 3))  
### Output:   
Point-Poly: LINESTRING(4.3 1.9,4.2 2.1)  
Poly-Line: LINESTRING(2.9 0.7,2.9 0)  
Poly-MultiPoint: LINESTRING(2.36923 1.05385,2 0.5)  
  
  
![closest_points_example](https://user-images.githubusercontent.com/3660366/81163067-d636bf80-8f96-11ea-9b74-075a6d85f904.png)  
  
 ## Solution remarks  
  
* Largely reuse the code of `distance` and `disjoint` algorithms wherever possible.   
* Created 5 strategies for each coordinate system (15 in total) namely pt-pt, pt-seg, pt-box, box-box and seg-box similarly to distance strategies.  
* New formulas for computing the longitude of a point on a geodesic segment given starting point, azimuth and latitude of given point. In other words, compute where a given segment crosses a parallel. For geographic CS this is a variant of direct geodesic problem. For spherical this can be computed by a trigonometric formula.  
* For all CSs algorithms and formulas have moved from `strategies` to `formulas`.  
* All services of closest_point strategies are in `distance::service` namespace expect from default strategies that are in `closest_points::service`

---

## Comment 1

> Username: vissarion  
> Created_at: 2020-05-06 18:46:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-624823490  

There are a few points that need refactoring (e.g. cycling references where I used forward declaration just to be functional) but since this PR is becoming large I would like some review at this point.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 01:39:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443505968  

📁 doc/html/index.html

```diff
 144 |+         Tinko Bartels (Delaunay triangulation, Voronoi diagram, random point generation,
 145 |+         ...)
 146 |+       </li>
```

> Username: awulkiew  
> Created_at: 2020-07-07 01:38:59 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450564541  

This is unrelated to the PR, but that's ok.


---

## Review 3 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 01:42:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443507069  

📁 include/boost/geometry/algorithms/closest_points.hpp

```diff
  61 |+         >::type result = distance <
  62 |+                                       Geometry1, Geometry2, Strategy
  63 |+                                   >::apply(g1, g2, strategy);
```

> Username: awulkiew  
> Created_at: 2020-07-07 01:42:57 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450565498  

The indentation of <...> blocks is not correct in this section I think.

> Username: awulkiew  
> Created_at: 2020-07-07 01:47:55 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450566696  

So `closest_points` strategies are basically `distance` strategies but instead of the distance value 2 points are returned. And I'm saying 2 points because when we move to other dimensions then probably more coordinates will be returned here. Right?

> Username: vissarion  
> Created_at: 2020-07-07 11:16:33 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450788995  

Yes, in particular, `closest_points` strategies return the coordinates of the two points and the distance (actually only the comparable distance is required since it is used only for comparison of  two `closest_points` results). As it is now the code assumes 2 dimensions.


---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 01:51:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443509634  

📁 include/boost/geometry/algorithms/detail/check_iterator_range.hpp

```diff
  13 | #include <boost/core/ignore_unused.hpp>
  14 |- 
  14 |+ #include <boost/geometry/strategies/intersection_result.hpp>
```

> Username: awulkiew  
> Created_at: 2020-07-07 01:51:58 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450567764  

This file doesn't seem to be the correct place for this utility.

> Username: vissarion  
> Created_at: 2020-07-08 08:32:09 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451374668  

Maybe, but this include is need by `check_iterator_range_with_info` to define the return type   
`geometry::segment_intersection_points<point_type>`

> Username: awulkiew  
> Created_at: 2020-07-08 09:00:36 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451392496  

Yes, I know why it's here. My point was that I'm not sure whether or not this is the correct place.  
  
Another way is to implement it differently, e.g. either of these solution would allow to not use the specific result type:  
- store the state in a predicate and return this predicate, just like STL algorithms returns function objects in some cases  
- take result type as output parameter  
- define the return type as the first template parameter  
- define the return type in predicate

> Username: vissarion  
> Created_at: 2020-07-28 08:32:40 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r461411115  

Now, I see your point, sure, I defined the return type as template parameter and removed the include.


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 01:59:42 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443511896  

📁 include/boost/geometry/algorithms/detail/check_iterator_range.hpp

```diff
  91 |+         return -1;
  92 |+     }
  93 |+ };
```

> Username: awulkiew  
> Created_at: 2020-07-07 01:59:42 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450569838  

Actually I don't understand the need for the original `check_iterator_range`. AFAIU it could be replaced with `std::find_if` right? And then `check_iterator_range_with_index` would be:  
  
    it = std::find_if(begin, end, pred);  
    return (it == end) ? std::ptrdiff_t(-1) : (it - begin);  
  
Or am I missing something?

> Username: awulkiew  
> Created_at: 2020-07-07 11:49:30 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450805282  

Regarding my comment above, the fact that `check_iterator_range` could be replaced with `std::find_if` does not mean that it should be changed in this PR. It's only an observation. And since we already have it, it makes sense to also have `check_iterator_range_with_index`.

> Username: vissarion  
> Created_at: 2020-07-08 08:35:29 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451376733  

It is not exactly the same with `std::find_if` returns `bool` instead of an iterator, but the code can be simplified using `std::find_if`. I agree with your later comment, so I think it is better to leave it for a later PR possibly exploiting `C++14`

> Username: awulkiew  
> Created_at: 2020-07-08 08:55:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451389509  

It does return iterator: https://en.cppreference.com/w/cpp/algorithm/find  
I agree that this could be postponed.


---

## Review 6 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:01:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443512429  

📁 include/boost/geometry/algorithms/detail/check_iterator_range.hpp

```diff
 118 |+         {
 119 |+             intersection_return_type res = construction.apply(*it);
 120 |+             if (res.count != 0)
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:01:41 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450570378  

Why it has to be here instead of being checked inside `predicate` called in `check_iterator_range_with_index` or `std::find_if`?

> Username: vissarion  
> Created_at: 2020-07-08 08:54:27 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451388560  

`check_iterator_range_with_index` does not take a predicate but a construction (i.e.  return some non boolean result), I cannot see a much simpler way than the one I use here.

> Username: awulkiew  
> Created_at: 2020-07-08 09:13:40 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451399871  

Yes unless `check_iterator_range_with_index` took `predicate` as reference instead of const reference, internally called `construction.apply(*it)` and stored the last value inside, or if the predicate was taken by copy and returned from the function (like std::for_each but with stopping condition https://en.cppreference.com/w/cpp/algorithm/for_each), or the function returned `std::pair<index_type, predicate_type>`, etc.


---

## Review 7 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:03:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443512854  

📁 include/boost/geometry/algorithms/detail/closest_points/default_strategies.hpp

```diff
  45 |+     bool Reverse = geometry::reverse_dispatch<Geometry1, Geometry2>::type::value
  46 |+ >
  47 |+ struct default_strategy
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:03:03 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450570754  

Shouldn't this file be in directory `strategies`?

> Username: awulkiew  
> Created_at: 2020-07-07 15:35:44 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450957946  

Ok, I now see that it corresponds to `algorithms/detail/distance/default_strategies.hpp`. So ignore.

> Username: vissarion  
> Created_at: 2020-07-08 08:56:02 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451389606  

I followed the design of distance, see https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/detail/distance/default_strategies.hpp


---

## Review 8 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:05:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443513605  

📁 include/boost/geometry/algorithms/detail/closest_points/result.hpp

```diff
  67 |+     }
  68 |+ 
  69 |+     void swap()
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:05:46 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450571450  

Typically a member function called like that would allow to swap 2 objects of type `result`. How about calling it `swap_points` or something?

> Username: vissarion  
> Created_at: 2020-07-08 09:48:00 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451420306  

I agree, `swap_points` sounds fine


---

## Review 9 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:09:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443514780  

📁 include/boost/geometry/algorithms/detail/disjoint/areal_areal.hpp

```diff
 209 |+         {
 210 |+             res.count = 1;
 211 |+             res.intersections[0] = box.max_corner();
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:09:51 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450572498  

User-defined box may not define `max_corner()`.

> Username: vissarion  
> Created_at: 2020-07-08 10:15:20 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451435320  

Ok, I will do it with `get()` and `set()`.  
However it is not clear to me from the definition of the concept https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/concepts/concept_box.html that user-defined box may not define `max_corner()`.

> Username: awulkiew  
> Created_at: 2020-07-08 15:51:29 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451649030  

It may or may not define it. The coordinates of a user-defined box or segment are accessed with 2-argument get/set by the library. The library can access the points with `max_corner` but only if it's one of the types we provide. Btw, if you need a representation of one of the box/segment's points you can use `indexed_point_view` (see: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/views/detail/indexed_point_view.hpp).


---

## Review 10 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:15:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443516319  

📁 include/boost/geometry/algorithms/detail/disjoint/areal_areal.hpp

```diff
 220 |+     static inline intersection_return_type
 221 |+     for_each_segment(SegIter first,
 222 |+                      SegIter last,
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:15:07 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450573892  

More proper name would be something like `check_segments_range`. An algorithm `for_each_segment` would call a predicate for each segment of a geometry, like that: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/algorithms/for_each.hpp#L366

> Username: vissarion  
> Created_at: 2020-07-08 10:39:40 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451447541  

Again this is derived from original implementation of distance. But I agree, so I changed the name of both.


---

## Review 11 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:18:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443517319  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
  84 |+                              Geometry2 const& g2,
  85 |+                              Strategy const& strategy,
  86 |+                              point1_type& p)
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:18:31 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450574783  

Typically we put the output geometry before strategy.

> Username: awulkiew  
> Created_at: 2020-07-07 02:19:36 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450575036  

Btw, why is this type taken from Geometry1 and not passed as template parameter (passed by the user)?

> Username: vissarion  
> Created_at: 2020-07-08 11:54:04 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451484262  

This is a limitation of the current design, i.e. closest_points call distance algorithm passing only the types of two geometries then distance calls parts of disjoint algorithm and at that point there is no user-defined type for the output point. However, that point will be an intersection point of two geometries. Do you think there could be a problem here?

> Username: vissarion  
> Created_at: 2020-07-08 11:54:21 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451484403  

> Typically we put the output geometry before strategy.  
  
I will fix this.

> Username: awulkiew  
> Created_at: 2020-07-08 16:00:34 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451655235  

> Do you think there could be a problem here?  
  
Probably not. Technically when BG function returns a geometry the point type of this geometry should be used. But I think it's ok to convert to it at the very end. We should only use the more precise coordinate type of the two geometries though.


---

## Review 12 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:20:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443517978  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 123 |+                              Geometry2 const& g2,
 124 |+                              Strategy const& strategy,
 125 |+                              point1_type& p)
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:20:49 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450575325  

Same here.

> Username: vissarion  
> Created_at: 2020-07-08 11:54:38 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451484572  

Same answer as above.


---

## Review 13 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 02:26:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443519856  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 151 |+ 
 152 |+     template <typename Strategy>
 153 |+     static inline intersection_return_type
```

> Username: awulkiew  
> Created_at: 2020-07-07 02:26:39 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450576884  

Right, so the problem is that the function `closest_points` takes the segment type which would define the result, but here and also in strategies the return type of a strategy is "derived" from the input geometries. This is probably not a problem in general since the coordinates are stored using these types anyway. So I think it's enough to calculate the coordinates and then convert to segment coordinates as you did. But the coordinate type of `p_type` could be the most precise coordinate type of both `Geometry1` and `Geometry2`, not only `Geometry1`.

> Username: vissarion  
> Created_at: 2020-07-08 12:54:22 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451521527  

ok, I am getting the most precise


---

## Comment 14

> Username: awulkiew  
> Created_at: 2020-07-07 02:29:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-654561305  

I reviewed a part of the PR. I'll finish tomorrow.

---

## Review 15 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:39:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443807590  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 155 |+     {
 156 |+         intersection_return_type res =
 157 |+         disjoint_linear_with_info<Geometry1, Geometry2>::apply(g1, g2, strategy);
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:39:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450799971  

I'm not sure about it but shouldn't lines such as this be indented?

> Username: vissarion  
> Created_at: 2020-07-08 12:55:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451522479  

From the wiki  
```  
The preferred line length is 80 characters, with maximum length of 100.  
```

> Username: awulkiew  
> Created_at: 2020-07-08 15:56:24 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451652496  

I mean either:  
```  
intersection_return_type res =  
    disjoint_linear_with_info<Geometry1, Geometry2>::apply(g1, g2, strategy);  
```  
or if that's not possible then:  
```  
intersection_return_type res = disjoint_linear_with_info  
                                    <  
                                        Geometry1, Geometry2  
                                    >::apply(g1, g2, strategy);  
```

> Username: vissarion  
> Created_at: 2020-07-14 12:27:29 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r454318622  

converting to   
```  
intersection_return_type res = disjoint_linear_with_info  
                                           <  
                                               Geometry1,  
                                               Geometry2  
                                           >::apply(g1, g2, strategy);  
```  
since template parameters should be in different lines in this case.


---

## Review 16 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:39:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443807952  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 166 |+                 ::apply(g1, g2,
 167 |+                         strategy.template get_point_in_geometry_strategy
 168 |+                         <Geometry1, Geometry2>(),
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:39:48 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450800290  

Indent, probably, maybe?

> Username: vissarion  
> Created_at: 2020-07-08 12:56:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451523085  

done


---

## Review 17 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:50:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443814910  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 277 |+         <
 278 |+             ring, closure<Polygon>::value, Segment
 279 |+         >::apply(geometry::exterior_ring(polygon), segment, strategy);
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:50:34 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450805782  

This should be indented.

> Username: vissarion  
> Created_at: 2020-07-08 12:57:27 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451523439  

done


---

## Review 18 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:53:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443816784  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 333 |+ struct disjoint_segment_areal_with_info<Segment, MultiPolygon, multi_polygon_tag>
 334 |+ {
 335 |+     typedef typename point_type<Segment>::type p_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:53:19 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450807267  

Here again, point type taken from Segment (first geometry) and I'm not sure if that's correct.

> Username: vissarion  
> Created_at: 2020-07-08 13:02:18 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451526634  

changed to most precise


---

## Review 19 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:54:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443817305  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 367 |+ struct disjoint_segment_areal_with_info<Segment, Ring, ring_tag>
 368 | {
 369 |+     typedef typename point_type<Ring>::type p_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:54:11 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450807679  

But here it's taken from Ring?

> Username: vissarion  
> Created_at: 2020-07-08 13:51:41 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451560278  

changed to most precise


---

## Review 20 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 11:56:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443818757  

📁 include/boost/geometry/algorithms/detail/disjoint/linear_areal.hpp

```diff
 325 |+     {
 326 |+         return disjoint_segment_areal_with_info<Segment, Polygon>
 327 |+                 ::apply(segment, polygon, strategy).count == 0;
```

> Username: awulkiew  
> Created_at: 2020-07-07 11:56:33 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450808879  

Should we worry about performance loss caused by the fact that the regular disjoint without info internally calculates disjoint with info? Probably not. This is only called by `disjoint(segment, polygon)` and not `disjoint(linestring, polygon)`, right?

> Username: vissarion  
> Created_at: 2020-07-08 13:52:23 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451560845  

AFAIU yes.


---

## Review 21 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:13:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443991413  

📁 include/boost/geometry/algorithms/detail/disjoint/multirange_geometry.hpp

```diff
 139 |+                     ConstantSizeGeometry
 140 |+                 >
 141 |+          > unary_predicate_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:13:19 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450942113  

Should be indented.

> Username: vissarion  
> Created_at: 2020-07-08 13:54:36 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451562595  

ok


---

## Review 22 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:18:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443996531  

📁 include/boost/geometry/algorithms/detail/disjoint/multirange_geometry.hpp

```diff
 103 |+                 ConstantSizeGeometry, unary_predicate_type
 104 |             >::apply(boost::begin(multirange), boost::end(multirange),
 105 |                      unary_predicate_type(constant_size_geometry, strategy));
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:18:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450945940  

Ok, so here is an example where `check_iterator_range_with_info` is used. I think I'd be ok with leaving it as it is for now. Later when we move to C++14 it would be possible to replace it with `std::find_if` with generic lambda. Now it'd still require to implement a predicate elsewhere but instead of `apply` it'd have `operator()`. So it wouldn't be much better.

> Username: vissarion  
> Created_at: 2020-07-08 13:54:59 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451562925  

Ok, I agree.


---

## Review 23 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:21:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443998998  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 259 |+                              Box const& box,
 260 |+                              Strategy const& strategy,
 261 |+                              Result& result)
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:21:25 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450947777  

Interesting, so here the `Result` is taken as an output argument instead of returned from function. Is there a reason why isn't one pattern consistently applied in all places?

> Username: awulkiew  
> Created_at: 2020-07-07 15:23:49 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450949436  

Btw, if it was implemented like this in all places then the point type could be taken from the Segment type passed by the user at the highest level. But then I guess it wouldn't be possible to use existing `distance` algorithm to return the info and you'd have to implement yet another `_with_info` algorithm. Correct?

> Username: vissarion  
> Created_at: 2020-07-08 18:55:26 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451759337  

The main function here is apply of `disjoint_segment_box_with_info`. In the `_compute` helper I used the output argument just because I found it simpler to derive the type of the result and since it is only called from `disjoint_segment_box_with_info`


---

## Review 24 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:22:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-443999611  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 343 |+ 
 344 |+     template <typename Strategy>
 345 |+     static inline intersection_return_type
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:22:04 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450948218  

And here it is returned.


---

## Review 25 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444005275  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 296 |+                 .apply(s, bottom_left, top_left,
 297 |+                        strategy.get_relate_segment_segment_strategy(),
 298 |+                        result);
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:13 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450952699  

This is probably not the way to go. A strategy taking another strategy? If needed `disjoint_segment_box_with_info_strategy` should simply contain the correct `relate_segment_segment_strategy`. Or is there a problem with cyclic dependency? But then I wonder why would `relate_segment_segment_strategy` need any `_with_info` strategy?

> Username: vissarion  
> Created_at: 2020-07-16 10:38:31 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455691935  

Indeed there is a problem with cyclic dependency. Here, `strategy` can be either a closest_point, distance, disjoint or intersection strategy. All of them provide the `disjoint_segment_box_with_info_strategy` and `relate_segment_segment_strategy`.


---

## Review 26 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444005459  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 304 |+         strategy.get_disjoint_segment_box_with_info_strategy()
 305 |+                 .apply(s, bottom_right, top_right,
 306 |+                        strategy.get_relate_segment_segment_strategy(),
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:26 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450952843  

Here again.


---

## Review 27 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444005645  

📁 include/boost/geometry/algorithms/detail/disjoint/segment_box.hpp

```diff
 315 |+                 .apply(segment,
 316 |+                        box,
 317 |+                        strategy.get_relate_segment_segment_strategy(),
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:28:38 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450952995  

And here.


---

## Review 28 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:37:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444013605  

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 185 |+             return geometry::detail::disjoint::disjoint_with_info
 186 |+                        <Geometry, SegOrBox>::apply(
 187 |+                           g1, g2, s.get_relate_segment_segment_strategy());
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:37:30 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450959131  

AFAIR the correct indentation is:  
```  
return geometry::detail::disjoint::disjoint_with_info  
    <  
        Geometry, SegOrBox  
    >::apply(g1, g2, s.get_relate_segment_segment_strategy());  
```

> Username: vissarion  
> Created_at: 2020-07-16 10:41:32 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455693607  

or   
```  
return geometry::detail::disjoint::disjoint_with_info  
    <  
        Geometry,  
        SegOrBox  
    >::apply(g1, g2, s.get_relate_segment_segment_strategy());  
```  
since typename parameters should be on different lines


---

## Review 29 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:40:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444016431  

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 268 |         // segment or box
 242 |-         comparable_return_type cd_min1(0);
 269 |+         comparable_return_type cd_min1;
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:40:41 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450961167  

Do I understand correctly that this is because `comparable_return_type` is no longer a number? If that's the case maybe it would be wise to rename it because in the future we may get an impression that it is comparable distance. What do you think?

> Username: vissarion  
> Created_at: 2020-07-16 10:55:42 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455700964  

Since the name is comparable return type and not comparable distance it does not yield a problem for me. But I changed the name of the variables containing `cd` to `cr`.


---

## Review 30 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:40:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444016606  

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 300 |         // segments of the geometry
 274 |-         comparable_return_type cd_min2(0);
 301 |+         comparable_return_type cd_min2;
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:40:52 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450961303  

And here as well.


---

## Review 31 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:43:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444018927  

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 381 |+         if ((is_polygon || is_mpolygon) && is_cartesian && is_box)
 382 |+         {
 383 |+             strategy::distance::services::swap<Strategy>::apply(res);
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:43:26 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450962938  

What does it do? What does it swap with what? Typically a function like that would swap two things with each other. Furthermore, is `services` namespace really a good place for it? Isn't it a detail?

> Username: awulkiew  
> Created_at: 2020-07-07 15:45:37 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450964464  

Is this function here because `res` may either be a number of an "info" (distance + 2 points)? If that's the case then it doesn't swap anything if it is a number right?

> Username: vissarion  
> Created_at: 2020-07-16 11:56:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455731538  

yes, and it actually swaps the two points in the case of closest_points and nothing in case of distance. I renamed it to `swap_result_points`. I placed it into `services` namespace since it is a result manipulation function such as `result_from_distance`


---

## Review 32 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:44:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444019506  

📁 include/boost/geometry/algorithms/detail/distance/interface.hpp

```diff
  88 |             >::apply(g2, g1, strategy);
  89 |+ 
  90 |+         strategy::distance::services::swap<Strategy>::apply(res);
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:44:03 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450963380  

Swap what?

> Username: vissarion  
> Created_at: 2020-07-16 11:57:02 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455731686  

renamed it to swap_result_points


---

## Review 33 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:47:45 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444022847  

📁 include/boost/geometry/algorithms/detail/distance/geometry_to_segment_or_box.hpp

```diff
 379 |+                 >::type::value;
 380 |+ 
 381 |+         if ((is_polygon || is_mpolygon) && is_cartesian && is_box)
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:47:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450965904  

Should `is_cartesian` be checked here? It should rather depend on strategy, right?

> Username: vissarion  
> Created_at: 2020-07-16 12:25:47 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455746825  

Is seems that for only this case the swap of results points is needed only for cartesian geometries and I didn't want to create a more complex swap_result_points function.

> Username: vissarion  
> Created_at: 2020-07-17 07:54:39 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456280492  

UPDATE: it seems that in the current version `is_cartesian` is not needed, so I am removing it.


---

## Review 34 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:52:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444026942  

📁 include/boost/geometry/algorithms/detail/distance/linear_or_areal_to_areal.hpp

```diff
  82 |+                     typename tag<Linear>::type,
  83 |+                     multi_linestring_tag
  84 |+                 >::type::value;
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:52:19 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450969018  

Maybe it would be wise to implement them as utilities?  
Then instead of:  
```  
boost::is_same  
    <  
        typename tag<Areal>::type,  
        multi_polygon_tag  
    >::type::value  
```  
you could write:  
```  
geometry::is_multi_polygon<Areal>::value  
```

> Username: vissarion  
> Created_at: 2020-07-29 12:52:18 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r462273690  

good idea, done.


---

## Review 35 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:53:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444028185  

📁 include/boost/geometry/algorithms/detail/distance/multipoint_to_geometry.hpp

```diff
 107 |+                     typename tag<Linear>::type,
 108 |+                     multi_linestring_tag
 109 |+                 >::type::value;
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:53:48 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450970018  

It's rather `is_multi_linestring` not `is_multi` which could be interpreted also as `multi_point` and `multi_polygon`.

> Username: vissarion  
> Created_at: 2020-07-16 12:08:33 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455737424  

right, thanks


---

## Review 36 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:56:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444030585  

📁 include/boost/geometry/algorithms/detail/distance/multipoint_to_geometry.hpp

```diff
 171 |-         return 0;
 237 |+         return_type res;
 238 |+         strategy::distance::services::result_set_unique_point<Strategy>
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:56:26 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450971843  

Isn't it rather a `detail` instead of `service`? Or have you mimicked already existing designs? I don't remember exactly but AFAIR there are many convoluted implementation details in `distance`/`comparable_distance`. So I'd understand if that was simply using the existing way of doing things around this algorithm.

> Username: vissarion  
> Created_at: 2020-07-16 12:06:36 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455736446  

Maybe I missing something on this topic whether sth is `detail` or `service`. The `detail` namespace is for algorithms right? The `services` is for strategies. As commented above I followed already existing designs for manipulations of the result.

> Username: vissarion  
> Created_at: 2020-07-17 11:15:01 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456378989  

Ok I see there is detail namespace for strategies too but the main argument holds.


---

## Review 37 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:57:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444031559  

📁 include/boost/geometry/algorithms/detail/distance/point_to_geometry.hpp

```diff
 105 |-         return_type const zero = return_type(0);
 107 |+ 
 108 |+         return_type const zero = return_type();
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:57:32 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450972598  

Ok, so now user-defined `return_type` is required to have a default ctor initializing it to 0. Is that correct?

> Username: vissarion  
> Created_at: 2020-07-16 12:43:25 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455756521  

Yes, I changed it by adding a constructor to the return_type of `closest_points`


---

## Review 38 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 15:58:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444032654  

📁 include/boost/geometry/algorithms/detail/distance/range_to_geometry_rtree.hpp

```diff
 115 |                         >::value_type,
 116 |-                     Strategy
 116 |+                 Strategy
```

> Username: awulkiew  
> Created_at: 2020-07-07 15:58:40 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450973360  

This should probably stay indented, right?

> Username: vissarion  
> Created_at: 2020-07-16 12:44:27 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455757101  

sure, IDE problem, thanks


---

## Review 39 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:00:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444034728  

📁 include/boost/geometry/algorithms/detail/distance/point_to_geometry.hpp

```diff
 170 |+             strategy::distance::services::result_set_unique_point<Strategy>
 171 |+                     ::apply(result, point);
 172 |+             return result;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:00:57 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450974946  

Btw, the naming is not intuitive. Do I understand correctly that the `return_type` may either be a number or "info"/segment? Setting a unique point to a distance/number doesn't make sense.

> Username: vissarion  
> Created_at: 2020-07-16 12:48:41 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r455759636  

Sure, none of those methods make sense for distance, also e.g. `swap_result_points`. They are only for closest_points.


---

## Review 40 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:02:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444036024  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 383 |+                                             > geometry::get<1>(p0) ? p1 : p0;
 384 |+                         if (less_equal(geometry::get<1>(high),
 385 |+                                        geometry::get<1>(top_right)))
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:02:29 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450975992  

I know that this is probably because of the max line lengths but IMO the original version was more readable. Are we very strict about line lengths?

> Username: vissarion  
> Created_at: 2020-07-17 08:06:31 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456286481  

It was boarder-line w.r.t. max line length, so I revert the change since I also think is more readable this way.


---

## Review 41 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:03:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444037059  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 386 |-                             return cast::apply(ps_strategy.apply(high, bottom_right, top_right));
 387 |+                             return cast_to_result<ReturnType>::apply(ps_strategy
 388 |+                                          .apply(high, bottom_right, top_right));
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:03:42 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450976743  

I propose to put `ps_strategy` in the next line:  
```  
return cast_to_result<ReturnType>::apply(  
    ps_strategy.apply(high, bottom_right, top_right));  
```

> Username: vissarion  
> Created_at: 2020-07-17 08:08:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456287278  

done


---

## Review 42 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:04:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444037542  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 388 |-                         return cast::apply(ps_strategy.apply(top_right, p0, p1));
 390 |+                         ReturnType res = cast_to_result<ReturnType>::apply
 391 |+                                          (ps_strategy.apply(top_right, p0, p1));
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:04:16 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450977110  

And here the formatting is different.

> Username: vissarion  
> Created_at: 2020-07-17 08:08:35 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456287431  

changed as above


---

## Review 43 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:04:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444038101  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 394 |-                 return cast::apply(ps_strategy.apply(top_right, p0, p1));
 400 |+                 ReturnType res = cast_to_result<ReturnType>
 401 |+                                   ::apply(ps_strategy.apply(top_right, p0, p1));
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:04:56 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450977525  

Here the code is formatted in yet another way. :)

> Username: vissarion  
> Created_at: 2020-07-17 08:09:16 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456287821  

changed to match the above


---

## Review 44 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:06:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444039720  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 451 |+                         geometry::get_as_radian<1>(top_left),
 452 |+                         geometry::get_as_radian<0>(p0)
 453 |+                     );
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:06:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450978775  

The formatting looks unusual. Do we have guidelines regarding the parentheses?

> Username: vissarion  
> Created_at: 2020-07-17 08:14:58 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456290643  

no only this   
```  
Parentheses around expressions should not be pre/post-fixed with spaces.  
```  
I followed the bracket case.

> Username: vissarion  
> Created_at: 2020-07-17 08:19:46 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456293232  

is this better?  
```  
ReturnType diff =  
    sb_strategy.get_distance_ps_strategy().vertical_or_meridian(  
        geometry::get_as_radian<1>(p_max),  
        geometry::get_as_radian<1>(top_left),  
        geometry::get_as_radian<0>(p0));  
```


---

## Review 45 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:08:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444041492  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 464 |+             ReturnType res = cast_to_result<ReturnType>
 465 |+                     ::apply(sb_strategy.get_distance_ps_strategy()
 466 |+                     .apply(top_left, p0, p1));
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:08:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450980084  

How about:  
```  
ReturnType res = cast_to_result  
    <  
        ReturnType  
    >::apply(sb_strategy.get_distance_ps_strategy().apply(top_left, p0, p1));  
```

> Username: vissarion  
> Created_at: 2020-07-17 08:20:08 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456293413  

ok, also changed to similar code parts


---

## Review 46 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:11:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444043740  

📁 include/boost/geometry/algorithms/detail/distance/segment_to_box.hpp

```diff
 882 |+         if (mirror)
 883 |+         {
 884 |+             strategy::distance::services::mirror<SBStrategy>::apply(result);
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:11:26 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450981879  

The same story as with `swap`. What is mirrored here?

> Username: vissarion  
> Created_at: 2020-07-17 08:41:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456304892  

ok, renamed to `mirror_points`


---

## Review 47 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:15:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444047133  

📁 include/boost/geometry/algorithms/detail/equals/collect_vectors.hpp

```diff
  59 |+ #include <boost/geometry/strategies/spherical/closest_points_cross_track_point_box.hpp>
  60 |+ #include <boost/geometry/strategies/spherical/closest_points_cross_track_box_box.hpp>
  61 |+ #include <boost/geometry/strategies/spherical/closest_points_segment_box.hpp>
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:15:24 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450984397  

Right, this utility should probably be converted to strategies but not in this PR.

> Username: vissarion  
> Created_at: 2020-07-17 08:58:02 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456313886  

OK


---

## Review 48 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:18:20 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444049587  

📁 include/boost/geometry/formulas/cartesian.hpp

```diff
  63 |+           PointOfSegment const& p1,
  64 |+           PointOfSegment const& p2,
  65 |+           Strategy const& comparable_distance_strategy)
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:18:20 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450986233  

If this uses a strategy I'm not sure if `formulas` is the correct place for it. It looks more like this should be somewhere in `strategies`, since this is also the result type which some of the strategies can return, right? And in the worst case it should probably be a `detail` of an algorithm.

> Username: vissarion  
> Created_at: 2020-07-17 09:41:09 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456336010  

I moved to strategies, since detail is not CS-specific.


---

## Review 49 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:21:39 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444052207  

📁 include/boost/geometry/formulas/point_segment_distance.hpp

```diff
  45 |+     bool Bisection = false
  46 |+ >
  47 |+ class point_segment_distance
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:21:39 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450988306  

You mean `distance_geographic`?  
  
Also the order of template parameters is not consistent with the geographic strategies so may be confusing.

> Username: vissarion  
> Created_at: 2020-07-17 10:06:42 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456349283  

renamed to `geographic_point_segment_distance` and changed template parameters' order.


---

## Review 50 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:34:07 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444062701  

📁 include/boost/geometry/formulas/spherical.hpp

```diff
 585 |+     bool EnableClosestPoint = false
 586 |+ >
 587 |+ class comparable_spherical_point_segment_distance
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:34:07 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450995970  

Here the name is ok.  
  
Though this looks like a strategy or an algorithm. It takes the `CalculationType`, it takes `comparable_distance_strategy`, it defines `result_type`.  
  
On the other hand it looks like at least parts of it could be a formula. Would it be possible to extract some kind of "formula core" from it? So a part only taking and returning coordinates, distances, etc. As other existing formulas do.

> Username: vissarion  
> Created_at: 2020-07-17 10:39:57 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456364476  

I would like to leave it into strategies for now (removed it from formulas) and leave splitting of the "formula" parts for a new PR.


---

## Review 51 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:40:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444067618  

📁 include/boost/geometry/strategies/cartesian/closest_points_projected_point.hpp

```diff
  33 |+     typename Strategy = distance::pythagoras<CalculationType>
  34 |+ >
  35 |+ class projected_point
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:40:04 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r450999609  

This strategy is probably not needed here. I know you mimicked the `distance::projected_point` but I think the `Strategy` argument is not needed. Especially because other strategies are defined internally anyway. Plus, `projected_point` name could be `cartesian_point_segment` for consistency with other strategies you added in this PR.

> Username: vissarion  
> Created_at: 2020-07-17 10:54:01 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456370277  

I changed the name but I am not sure I understand the issue with the Strategy, it is used internally to define some comparable strategies etc.


---

## Review 52 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:42:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444069363  

📁 include/boost/geometry/strategies/cartesian/closest_points_projected_point.hpp

```diff
  91 |+ 
  92 |+         typename closest_point_result<Point, PointOfSegment>::type
  93 |+                  closest_point_result;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:42:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451000930  

Can using the same identifier cause problems with some compilers?

> Username: vissarion  
> Created_at: 2020-07-17 10:56:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456371558  

I do not know! I change it though.


---

## Review 53 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:43:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444070732  

📁 include/boost/geometry/strategies/cartesian/closest_points_projected_point.hpp

```diff
 113 |+     template <typename ResultType>
 114 |+     ResultType
 115 |+     apply(ResultType comparable_result) const
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:43:51 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451002008  

Here the line wouldn't be too long. Would it?

> Username: vissarion  
> Created_at: 2020-07-17 10:58:08 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456372183  

no, changed.


---

## Review 54 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:45:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444072128  

📁 include/boost/geometry/strategies/cartesian/closest_points_projected_point.hpp

```diff
 228 |+                                                 Strategy
 229 |+                                             >,
 230 |+                             P, PS>
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:45:34 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451003092  

AFAIR the guidelines says:  
```  
struct result_from_distance  
<  
    closest_points::projected_point  
        <  
            CalculationType,  
            Strategy  
        >,  
    P, PS  
>  
```

> Username: vissarion  
> Created_at: 2020-07-17 11:00:21 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456373135  

done


---

## Review 55 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:47:04 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444073298  

📁 include/boost/geometry/strategies/cartesian/closest_points_projected_point.hpp

```diff
 236 |+                                                     Strategy
 237 |+                                                  >,
 238 |+                                  P, PS>::type return_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:47:04 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451004009  

```  
    typedef typename return_type  
        <  
            closest_points::projected_point  
                <  
                    CalculationType,  
                    Strategy  
                >,  
            P, PS  
        >::type return_type;  
```

> Username: vissarion  
> Created_at: 2020-07-17 11:01:52 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456373774  

ok


---

## Review 56 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:49:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444075047  

📁 include/boost/geometry/strategies/cartesian/closest_points_segment_box.hpp

```diff
 275 |+             PS,
 276 |+             PB
 277 |+        >
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:49:18 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451005245  

Almost correct. :)

> Username: vissarion  
> Created_at: 2020-07-17 11:02:03 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456373871  

fixed


---

## Review 57 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:49:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444075283  

📁 include/boost/geometry/strategies/cartesian/closest_points_segment_box.hpp

```diff
 286 |+                                     PS,
 287 |+                                     PB
 288 |+                                  >::type return_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:49:35 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451005444  

Less correct. ;)

> Username: vissarion  
> Created_at: 2020-07-17 11:02:56 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456374219  

fixed


---

## Review 58 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:51:35 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444076867  

📁 include/boost/geometry/strategies/cartesian/disjoint_segment_box_with_info.hpp

```diff
  42 |+                              BoxPoint const& box_point2,
  43 |+                              Strategy const& strategy,
  44 |+                              T& result)
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:51:35 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451006607  

This looks more like an algorithm than a strategy.

> Username: vissarion  
> Created_at: 2020-07-17 11:05:49 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456375377  

for cartesian maybe, but not for non-cartesian


---

## Review 59 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:53:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444078220  

📁 include/boost/geometry/strategies/cartesian/distance_projected_point.hpp

```diff
 183 |- 
 184 |-         return strategy.apply(p, projected);
 152 |+         return std::sqrt(comparable_result);
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:53:19 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451007653  

For now it should be `bg::math::sqrt()`;

> Username: vissarion  
> Created_at: 2020-07-17 11:06:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456375738  

ok


---

## Review 60 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:55:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444079738  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 185 |+         CalculationType,
 186 |+         distance::pythagoras<CalculationType>
 187 |+     > disjoint_segment_box_strategy_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:55:11 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451008770  

Hmm, I probably missed it, but why does `disjoint::segment_box` require distance strategy?

> Username: vissarion  
> Created_at: 2020-07-17 11:28:58 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456384475  

ooops, it doesn't. It was there before this PR but I am removing it now.


---

## Review 61 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 16:58:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444082154  

📁 include/boost/geometry/strategies/cartesian/side_by_triangle.hpp

```diff
  86 |+         CalculationType,
  87 |+         distance::pythagoras<CalculationType>
  88 |+     > disjoint_segment_box_strategy_type;
```

> Username: awulkiew  
> Created_at: 2020-07-07 16:58:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451010637  

So side strategy now provides `disjoint::segment_box` which requires distance strategy? Why is this the case?

> Username: vissarion  
> Created_at: 2020-07-17 11:29:30 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456384695  

disjoint::segment_box does not require distance strategy


---

## Review 62 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:00:44 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444084138  

📁 include/boost/geometry/strategies/geographic/closest_points.hpp

```diff
  63 |+     {}
  64 |+ 
  65 |+     Spheroid get_spheroid() const
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:00:43 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451012164  

In other strategies there is member function `model()` for this. We could of course change it the other way around but we should be consistent. And then also the name wouldn't cover spheres returned by spherical strategies.

> Username: vissarion  
> Created_at: 2020-07-17 11:32:10 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456385747  

sure, renamed to model


---

## Review 63 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:01:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444084870  

📁 include/boost/geometry/strategies/geographic/closest_points.hpp

```diff
 208 |+ >
 209 |+ struct comparable_type<closest_points::geographic
 210 |+                          <FormulaPolicy, Spheroid, CalculationType> >
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:01:34 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451012677  

```  
struct comparable_type  
<  
    closest_points::geographic  
        <  
            FormulaPolicy, Spheroid, CalculationType  
        >  
>  
```  
This, or the whole <> block indented once more, because this is not the template arguments list but the specialization arguments and our guidelines don't cover that.

> Username: vissarion  
> Created_at: 2020-07-17 11:33:55 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456386458  

ok


---

## Review 64 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:04:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444087304  

📁 include/boost/geometry/strategies/geographic/closest_points.hpp

```diff
 230 |+                                 CalculationType
 231 |+                             >
 232 |+         >
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:04:36 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451014424  

Something like that. But here it's indented too much. ;)


---

## Review 65 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:06:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444088951  

📁 include/boost/geometry/strategies/geographic/closest_points_cross_track.hpp

```diff
  29 |+     typename CalculationType = void
  30 |+ >
  31 |+ class geographic_cross_track
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:06:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451015674  

So this could be `geographic_point_segment` right?

> Username: vissarion  
> Created_at: 2020-07-17 11:37:03 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456387679  

yes, but I wanted to be consistent with distance naming


---

## Review 66 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:09:57 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444091187  

📁 include/boost/geometry/strategies/geographic/closest_points_cross_track_box_box.hpp

```diff
 103 |+                                          Result& result)
 104 |+     {
 105 |+         if (geometry::covered_by(box_corner, other_box))
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:09:56 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451017383  

Here a geographic strategy should be called instead.

> Username: vissarion  
> Created_at: 2020-07-27 12:25:16 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r460852224  

both spherical and geographic strategies are using now `within::spherical_point_box`


---

## Review 67 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:12:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444093369  

📁 include/boost/geometry/strategies/geographic/closest_points_cross_track_box_box.hpp

```diff
  49 |+     typename CalculationType = void
  50 |+ >
  51 |+ class geographic_cross_track_box_box
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:12:54 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451019142  

This could be `geographic_box_box`.

> Username: vissarion  
> Created_at: 2020-07-17 11:38:10 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456388104  

yes, but I wanted to be consistent with distance naming


---

## Review 68 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:13:30 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444093835  

📁 include/boost/geometry/strategies/geographic/closest_points_cross_track_point_box.hpp

```diff
  48 |+     typename CalculationType = void
  49 |+ >
  50 |+ class geographic_cross_track_point_box
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:13:30 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451019512  

Here `geographic_point_box` would be enough.

> Username: vissarion  
> Created_at: 2020-07-17 11:38:46 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456388316  

again yes, but I wanted to be consistent with distance naming. Maybe it make sense to change all of them but in a new PR


---

## Review 69 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:19:46 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444098320  

📁 include/boost/geometry/strategies/geographic/disjoint_segment_box_with_info.hpp

```diff
  53 |+                              BoxPoint const& box_point2,
  54 |+                              Strategy const& strategy,
  55 |+                              T& result)
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:19:46 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451023206  

Ok, so here the result is returned as output parameter. Probably because you implemented another version of the algorithm. However in case of distance() you probably wanted to use as much of the existing implementation as possible. Is that correct?

> Username: vissarion  
> Created_at: 2020-07-17 11:42:43 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456389895  

I am not sure I understand the question here.

> Username: awulkiew  
> Created_at: 2020-11-02 00:51:06 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r515697046  

I mean the interface of this strategy looks wierd to me.  
  
Many `apply()` methods taking different parameters and the result returned by output argument instead of returned from the function (the return type is `void`).  
  
Furthermore a different strategy is taken here which is something we never do in any other strategy.  
  
Also taking min and max points of a box is something we don't do in any strategy AFAIR.  
  
So I wanted to understand why it was done this way.

> Username: vissarion  
> Created_at: 2020-11-02 15:51:37 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r516068068  

>Furthermore a different strategy is taken here which is something we never do in any other strategy.  
  
this is because of cycle dependencies, the disjoint_segment_box_with_info strategy cannot define a getter to a relate_segment_segment strategy so the algorithm passes that strategy.   
  
The rest, return types and max box points can be easily fixed to match the rest of library's design.

> Username: awulkiew  
> Created_at: 2020-11-02 16:39:45 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r516102705  

This interface suggests that it's this strategy that needs the other strategy, not the algorithm. So I don't understand your point about defining a getter. If my understanding is correct and another strategy is needed by this one then it should simply be internally created and kept as a member.  
  
Furthermore if there are some cyclic dependencies of headers because some parts of the code used here internally are defined in headers also defining some higher level code that is using this strategy then you could simply extract the common part to a different header and include it here and there.  
  
If I'm missing something could you please describe what exactly is this cyclic dependency?


---

## Review 70 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:32:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444107150  

📁 include/boost/geometry/strategies/geographic/intersection.hpp

```diff
  82 |+     {
  83 |+         return geographic_segments<FormulaPolicy, Order, Spheroid, CalculationType>(m_spheroid);
  84 |+     }
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:32:09 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451030411  

Why does this strategy has to define a getter to itself?

> Username: vissarion  
> Created_at: 2020-07-17 11:40:18 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456388932  

it is the issue with cyclic dependences described above.


---

## Review 71 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:33:41 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444108236  

📁 include/boost/geometry/strategies/spherical/closest_points_cross_track.hpp

```diff
  32 |+         typename Strategy = distance::haversine<double, CalculationType>
  33 |+ >
  34 |+ class cross_track
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:33:41 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451031285  

So this could be `spherical_point_segment` right? Plus, Strategy is probably not needed as an argument.

> Username: vissarion  
> Created_at: 2020-07-20 10:00:14 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r457241531  

I named it like this to be consistent with distance. I prefer to changed them both at once in a new PR.

> Username: vissarion  
> Created_at: 2020-07-20 10:40:24 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r457268082  

`Strategy` is needed to define to comparable strategy used to compute the comparable distance.


---

## Review 72 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:35:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444109397  

📁 include/boost/geometry/strategies/spherical/closest_points_cross_track_box_box.hpp

```diff
 101 |+     typename Strategy = distance::haversine<double, CalculationType>
 102 |+ >
 103 |+ class cross_track_box_box
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:35:23 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451032227  

Same here, could be `spherical_box_box` without `Strategy`.

> Username: vissarion  
> Created_at: 2020-07-20 10:40:53 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r457268387  

I named it like this to be consistent with distance. I prefer to changed them both at once in a new PR.


---

## Review 73 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:37:12 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444110630  

📁 include/boost/geometry/strategies/spherical/closest_points_cross_track_point_box.hpp

```diff
  46 |+     typename Strategy = distance::haversine<double, CalculationType>
  47 |+ >
  48 |+ class cross_track_point_box
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:37:12 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451033328  

`spherical_point_box`

> Username: vissarion  
> Created_at: 2020-07-20 10:40:57 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r457268422  

I named it like this to be consistent with distance. I prefer to changed them both at once in a new PR.


---

## Review 74 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-07 17:38:50 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444111795  

📁 include/boost/geometry/strategies/spherical/disjoint_segment_box.hpp

```diff
  73 | 
  74 | 
  75 |+ struct segment_box_spherical_with_info
```

> Username: awulkiew  
> Created_at: 2020-07-07 17:38:50 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451034254  

For consistency: `spherical_segment_box_with_info`.

> Username: vissarion  
> Created_at: 2020-07-20 10:29:01 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r457260773  

ok


---

## Comment 75

> Username: awulkiew  
> Created_at: 2020-07-07 17:49:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-655023008  

Damn, that's a lot of code. Thanks!  
  
In general it looks good.  
  
Do I understand correctly that you used as much of the implementation of distance algorithm as you could and you implemented `_with_info` versions of all other algorithms that were needed (e.g. disjoint)? If that's the case then I'm wondering whether or not the choice to utilize distance was correct. It's implementation is very complex with all of the regular to comparable conversions and back, with all of the traits for strategies, etc. I wonder if it was a separate, new algorithm would it be shorter, less complex, more clear, etc.? Without the traits/conversions/millions of specializations, etc. What do you think?

---

## Comment 76

> Username: awulkiew  
> Created_at: 2020-07-08 07:35:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-655344842  

Another thing. Would it be possible to separate the implementation details of all `_with_info` algorithms from corresponding non-`_with_info` algorithms? E.g. areal_areal_with_info. These are new detail algorithms, they are not for the users, variant support is not needed, only dispatches for these geometries that are required. They could be put in separate files ideally leaving the old algorithms untouched.  
  
And by extension this could be done for distance too as I mentioned above. Then you wouldn't be bound by all of the limitations of the distance like return type, strategies traits, etc.

---

## Review 77 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-08 09:15:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/707#pullrequestreview-444559900  

📁 include/boost/geometry/algorithms/detail/check_iterator_range.hpp

```diff
  73 |+     // version where we can pass a predicate object
  74 |+     template <typename InputIterator>
  75 |+     static inline int apply(InputIterator first,
```

> Username: awulkiew  
> Created_at: 2020-07-08 09:15:34 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r451401027  

The return type should be: `std::iterator_traits<InputIterator>::difference_type`

> Username: vissarion  
> Created_at: 2020-07-17 11:45:25 UTC  
> Updated_at: 2021-03-10 10:03:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#discussion_r456391041  

indeed, changed.


---

## Comment 78

> Username: vissarion  
> Created_at: 2020-07-20 10:48:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-660953549  

> Damn, that's a lot of code. Thanks!  
>   
> In general it looks good.  
>   
> Do I understand correctly that you used as much of the implementation of distance algorithm as you could and you implemented `_with_info` versions of all other algorithms that were needed (e.g. disjoint)? If that's the case then I'm wondering whether or not the choice to utilize distance was correct. It's implementation is very complex with all of the regular to comparable conversions and back, with all of the traits for strategies, etc. I wonder if it was a separate, new algorithm would it be shorter, less complex, more clear, etc.? Without the traits/conversions/millions of specializations, etc. What do you think?  
  
First of all thanks for the detailed review! I tried to address most of the comments, please see them and mark as resolved the conversations that are ok with you.   
  
I tried to reuse as much of the implementation of distance and disjoint and only in special cases introduce `_with_info` versions. For example in some cases, such as points and multi-points such implementations are not needed. Since there are many cases (all the possible pairs of geometries) on those algorithms such an approach does not introduce a lot of redundant code. Maybe, revisiting all those algorithms that have tight connections (distance, disjoint, closest points) could lead to a more elegant unified design but I would like to do this as a separate PR if this is OK with the rest of BG developers.

---

## Comment 79

> Username: vissarion  
> Created_at: 2020-07-20 10:53:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-660955567  

> Another thing. Would it be possible to separate the implementation details of all `_with_info` algorithms from corresponding non-`_with_info` algorithms? E.g. areal_areal_with_info. These are new detail algorithms, they are not for the users, variant support is not needed, only dispatches for these geometries that are required. They could be put in separate files ideally leaving the old algorithms untouched.  
>   
> And by extension this could be done for distance too as I mentioned above. Then you wouldn't be bound by all of the limitations of the distance like return type, strategies traits, etc.  
  
AFAIU this is close to what I comment above for a different design of distance, disjoint, (with or without) info and closest_points. Would it be a problem to be done in a new PR? This PR is already large to handle. Also, we could utilize the C++14 standard coming on 1.75 to make the new code simpler.

---

## Comment 80

> Username: addy90  
> Created_at: 2021-02-26 13:34:03 UTC  
> Updated_at: 2021-03-01 16:45:56 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-786650747  

EDIT2: Please find a suggestion for a fix in https://github.com/boostorg/geometry/pull/707#issuecomment-788012237.  
  
EDIT: Please review my next comment here, it looks like a deeper problem: https://github.com/boostorg/geometry/pull/707#issuecomment-786770158  
I just let this example stay as this one works in Debug but not Release and the other works in no case.  
  
I have a strange problem with closest point in "CMake Release" mode when using this pull request.  
  
See the following code:  
```  
#define BOOST_TEST_DYN_LINK  
  
#include <boost/test/unit_test.hpp>  
  
#include <boost/geometry/geometries/point.hpp>  
#include <boost/geometry/geometries/segment.hpp>  
#include <boost/geometry/algorithms/closest_points.hpp>  
#include <boost/geometry/algorithms/length.hpp>  
#include <boost/geometry/strategies/geographic/closest_points_cross_track.hpp>  
#include <boost/geometry/strategies/geographic/distance_cross_track.hpp>  
#include <boost/geometry/strategies/geographic/distance.hpp>  
#include <boost/geometry/io/wkt/write.hpp>  
  
BOOST_AUTO_TEST_SUITE(library_tests)  
  
    BOOST_AUTO_TEST_CASE(closest_path_test) {  
        using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
        using segment_type = boost::geometry::model::segment<point_type>;  
  
        boost::geometry::strategy::closest_points::geographic_cross_track<> closest_point_strategy;  
        boost::geometry::strategy::distance::geographic_cross_track<> cross_track_strategy;  
        boost::geometry::strategy::distance::geographic<> distance_strategy;  
  
        point_type point{11.845747600604916272,  
                         50.303247769986953131};  
        segment_type segment{{11.8449176, 50.3030635},  
                             {11.8458063, 50.3032608}};  
  
        const auto distance = boost::geometry::distance(point, segment, cross_track_strategy);  
  
        segment_type projection;  
        boost::geometry::closest_points(point, segment, projection, closest_point_strategy);  
        const auto length = boost::geometry::length(projection, distance_strategy);  
  
        std::cout << std::setprecision(20)  
                  << "Point: " << boost::geometry::wkt(point) << "\n"  
                  << "Segment: " << boost::geometry::wkt(segment) << "\n"  
                  << "Distance: " << distance << "\n"  
                  << "Projection: " << boost::geometry::wkt(projection) << "\n"  
                  << "Length: " << length << std::endl;  
  
        BOOST_CHECK_CLOSE_FRACTION(distance, length, 1e-6);  
    }  
  
BOOST_AUTO_TEST_SUITE_END()  
```  
  
When I run this code in "CMake Debug" mode on Ubuntu 20.04 LTS, I get the following output:  
  
> Point: POINT(11.845747600604916272 50.303247769986953131)  
Segment: LINESTRING(11.84491760000000049 50.303063500000000374,11.845806299999999567 50.303260799999996777)  
Distance: 0  
Projection: LINESTRING(11.845747600604916272 50.303247769986953131,11.845747599899924651 50.303247769830448988)  
Length: 0  
test.cpp(46): info: difference{} between distance{0} and length{0} doesn't exceed 9.9999999999999995e-07  
  
But when I run this code in "CMake Release" mode on Ubuntu 20.04 LTS, I get the following output:  
> Point: POINT(11.845747600604916272 50.303247769986953131)  
Segment: LINESTRING(11.84491760000000049 50.303063500000000374,11.845806299999999567 50.303260799999996777)  
Distance: 0.095230302293127844226  
Projection: LINESTRING(11.845747600604916272 50.303247769986953131,20.187677220056457372 51.800819195292461927)  
Length: 607801.1508758389391  
test.cpp(46): error: in "library_tests/closest_path_test": difference{6.38243e+06} between distance{0.095230302293127844} and length{607801.150875838939101} exceeds 9.9999999999999995e-07  
  
The projection is suddenly over 607801 meters away though the point should lie on top of the segment, as we can see in "Debug" mode.  
  
I think this happens because of floating point optimization in Release mode, so I compared with `distance` function with `cross_track_strategy` and there we only have a minimal difference from the rounding errors in Release mode. But the `closest_point` is completely away from the right position. This should not happen, right? I mean, the `distance` function gives a fine answer though some rounding errors, but the `closest_point` does something strange.  
  
Any ideas how to solve this problem? Still, thank you for the great pull request!

---

## Comment 81

> Username: addy90  
> Created_at: 2021-02-26 17:01:23 UTC  
> Updated_at: 2021-03-01 16:45:46 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-786770158  

EDIT: Please find a suggestion for a fix in https://github.com/boostorg/geometry/pull/707#issuecomment-788012237.  
  
I found another test case that does not work neither with "Debug" nor with "Release" mode. I start to believe that the floating point optimization is not the problem.  
  
Please see the following test:  
```  
    BOOST_AUTO_TEST_CASE(clostest_path_test_2) {  
        using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
        using segment_type = boost::geometry::model::segment<point_type>;  
  
        boost::geometry::strategy::closest_points::geographic_cross_track<> closest_point_strategy;  
        boost::geometry::strategy::distance::geographic_cross_track<> cross_track_strategy;  
        boost::geometry::strategy::distance::geographic<> distance_strategy;  
  
        point_type point{11.921418190002441406,  
                         50.316425323486328125};  
        segment_type segment{{11.9214920, 50.3161678},  
                             {11.9212341, 50.3161381}};  
  
        const auto distance = boost::geometry::distance(point, segment, cross_track_strategy);  
  
        segment_type projection;  
        boost::geometry::closest_points(point, segment, projection, closest_point_strategy);  
        const auto length = boost::geometry::length(projection, distance_strategy);  
  
        std::cout << std::setprecision(20)  
                  << "Point: " << boost::geometry::wkt(point) << "\n"  
                  << "Segment: " << boost::geometry::wkt(segment) << "\n"  
                  << "Distance: " << distance << "\n"  
                  << "Projection: " << boost::geometry::wkt(projection) << "\n"  
                  << "Length: " << length << std::endl;  
  
        BOOST_CHECK_CLOSE_FRACTION(distance, length, 1e-6);  
    }  
```  
  
When running in "Release" mode, I get the following:  
  
> Point: POINT(11.921418190002441406 50.316425323486328125)  
Segment: LINESTRING(11.921492000000000644 50.31616780000000233,11.921234099999999501 50.316138100000003419)  
Distance: 29.124063251608422576  
Projection: LINESTRING(11.921418190002441406 50.316425323486328125,11.924663903206226934 50.316533032574753292)  
Length: 231.48238061273059429  
tests.cpp(75): error: in "library_tests/clostest_path_test_2": difference{6.94815} between distance{29.124063251608423} and length{231.482380612730594294} exceeds 9.9999999999999995e-07  
  
Running in "Debug" mode gives the following:  
> Point: POINT(11.921418190002441406 50.316425323486328125)  
Segment: LINESTRING(11.921492000000000644 50.31616780000000233,11.921234099999999501 50.316138100000003419)  
Distance: 29.124063251912851058  
Projection: LINESTRING(11.921418190002441406 50.316425323486328125,11.924663902887006728 50.3165330325379756)  
Length: 231.48236102530717062  
tests.cpp(75): error: in "library_tests/clostest_path_test_2": difference{6.94815} between distance{29.124063251912851} and length{231.482361025307170621} exceeds 9.9999999999999995e-07  
  
The floating points are not similar in "Debug" and "Release" but here in both cases the closest point is totally off the limits. Sounds like a deeper problem for me than "floating point optimization". I reviewed the LineStrings with QGIS and QuickWKT plugin, the clostest point projection goes into a totally wrong direction here.  
  
Are you able to reproduce my examples?

---

## Comment 82

> Username: addy90  
> Created_at: 2021-03-01 14:57:06 UTC  
> Updated_at: 2021-03-01 16:45:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-788012237  

EDIT: Please find a suggestion for a fix at the end of this post. Hopefully this was it!  
  
I have to post a third test, because I read about https://github.com/boostorg/geometry/issues/541 and https://github.com/vissarion/geometry/wiki/Geographic-algorithms  
I learned that "long double" makes a difference to "double" in the calculations. This is why I tested both above tests with "long double" instead of "double" for coordinate type in the points. Both then worked in "Debug" and "Release" mode.  
  
So I searched my data for a third test that does not work even with "long double", see here:  
```  
    BOOST_AUTO_TEST_CASE(clostest_path_test_3) {  
        using point_type = boost::geometry::model::point<long double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
        using segment_type = boost::geometry::model::segment<point_type>;  
  
        boost::geometry::strategy::closest_points::geographic_cross_track<> closest_point_strategy;  
        boost::geometry::strategy::distance::geographic_cross_track<> cross_track_strategy;  
        boost::geometry::strategy::distance::geographic<> distance_strategy;  
  
        point_type point{11.904624124918561169L,  
                         50.317349861000025692L};  
        segment_type segment{{11.9046808, 50.3173523},  
                             {11.9045925, 50.3173485}};  
  
        const auto distance = boost::geometry::distance(point, segment, cross_track_strategy);  
  
        segment_type projection;  
        boost::geometry::closest_points(point, segment, projection, closest_point_strategy);  
        const auto length = boost::geometry::length(projection, distance_strategy);  
  
        std::cout << std::setprecision(20)  
                  << "Point: " << boost::geometry::wkt(point) << "\n"  
                  << "Segment: " << boost::geometry::wkt(segment) << "\n"  
                  << "Distance: " << distance << "\n"  
                  << "Projection: " << boost::geometry::wkt(projection) << "\n"  
                  << "Length: " << length << std::endl;  
  
        BOOST_CHECK_CLOSE_FRACTION(distance, length, 1e-6);  
    }  
```  
  
Result is the same in "Debug" and "Release":  
> Point: POINT(11.904624124918561169 50.317349861000025692)  
Segment: LINESTRING(11.904680799999999508 50.31735230000000314,11.904592499999999688 50.317348500000001366)  
Distance: 0.0021043139698268269131  
Projection: LINESTRING(11.904624124918561169 50.317349861000025692,7.7920873632969789527 50.066502375228474256)  
Length: 294959.48255105902601  
tests.cpp(104): error: in "library_tests/clostest_path_test_3": difference{1.40169e+08} between distance{0.00210431396982682691314} and length{294959.482551059026008} exceeds 9.9999999999999995e-07  
  
Does not work as intended, as we can see.  
  
I found out that the calculated point is returned from here: https://github.com/vissarion/geometry/blob/b7f71635d2e9f07411185b848cb712a0de5a2d1e/include/boost/geometry/formulas/geographic_point_segment_distance.hpp#L288  
It is calculated above at `res14 = direct_distance_type::apply(lon1, lat1, s14, a12, spheroid);`  
  
The problem is that when the `res34.distance` starts to go crazy, for the `distance` function, the `prev_distance` is re-set so that the best value stays. But for `closest_path`, the new `res14` is set.  
  
I suggest the following:  
At line [246](https://github.com/vissarion/geometry/blob/b7f71635d2e9f07411185b848cb712a0de5a2d1e/include/boost/geometry/formulas/geographic_point_segment_distance.hpp#L246), introduce variable `prev_res14`.  
Below line [257](https://github.com/vissarion/geometry/blob/b7f71635d2e9f07411185b848cb712a0de5a2d1e/include/boost/geometry/formulas/geographic_point_segment_distance.hpp#L257) set `prev_res14 = res14;`  
In line [288](https://github.com/vissarion/geometry/blob/b7f71635d2e9f07411185b848cb712a0de5a2d1e/include/boost/geometry/formulas/geographic_point_segment_distance.hpp#L288) set the previous value as with the distance, so `set_result(prev_distance, prev_res14, result);`.  
  
This should fix the main problem, the difference between length and distance methods sometimes is still a little bit different, but no extreme problems remain. Would this fix be feasible as suggested from me?  
  
See this patch file for suggested fixes:  
[shortest-paths.patch.txt](https://github.com/boostorg/geometry/files/6062875/shortest-paths.patch.txt)

---

## Comment 83

> Username: vissarion  
> Created_at: 2021-03-02 09:28:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-788762402  

@addy90 thanks for using the code and finding this bug. I pushed a fix (similar to yours) and now your tests are passing.   
  
> This should fix the main problem, the difference between length and distance methods sometimes is still a little bit different, but no extreme problems remain.  
  
Do you have a case where distance and length are a bit different because in your current cases the difference is less than `1e-20` on my setup.

---

## Comment 84

> Username: addy90  
> Created_at: 2021-03-02 10:22:15 UTC  
> Updated_at: 2021-03-02 10:47:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-788798354  

Dear @vissarion, thank you for using my suggestions for deploying a fix! I am currently very depending on your closest_points code on my current project (in the hope that it will come to stable Boost 1.76 or later) so this really helps me out! I applied your version of the fix before continuing with my tests.  
  
Concerning the tests I have the following four test cases at the moment with "long double":  
[library_tests.cpp.txt](https://github.com/boostorg/geometry/files/6067614/library_tests.cpp.txt)  
  
The result on my setup in CMake Release mode is the following (Debug mode is very similar right now with the fix applied):  
[library_tests_result.txt](https://github.com/boostorg/geometry/files/6067702/library_tests_result.txt)  
The fourth test is similar to the first test but the length differences are exchanged in my case, as you can see with the results. In the first test case the distance is zero but the closest path length is not, in the fourth test it is reversed. I am not sure why this is happening but I have to use large error margins for my tests with "long double" due to this as you can see. I have Ubuntu 20.04 LTS with GCC 9.3.0 running.  
  
What I tried now is to use "double" instead of "long double" again for the point type (also I removed the L from the doubles that set the point locations). This is how you applied my tests in your commit. When I do this, the result is the following:  
[library_tests_result_double.txt](https://github.com/boostorg/geometry/files/6067734/library_tests_result_double.txt)  
No differences at all. So with "double" there are no differences in these four tests, but with "long double" there are.  
  
I searched through my data again with "double" type and found a test case that still has differences between "length" and "distance":  
```  
    BOOST_AUTO_TEST_CASE(clostest_path_test_5) {  
        using point_type = boost::geometry::model::point<double, 2, boost::geometry::cs::geographic<boost::geometry::degree>>;  
        using segment_type = boost::geometry::model::segment<point_type>;  
  
        boost::geometry::strategy::closest_points::geographic_cross_track<> closest_point_strategy;  
        boost::geometry::strategy::distance::geographic_cross_track<> cross_track_strategy;  
        boost::geometry::strategy::distance::geographic<> distance_strategy;  
  
        point_type point{11.894846916198730469,  
                         50.316379547119140625};  
        segment_type segment{{11.8958402, 50.3155918},  
                             {11.8953426, 50.3155504}};  
  
        const auto distance = boost::geometry::distance(point, segment, cross_track_strategy);  
  
        segment_type projection;  
        boost::geometry::closest_points(point, segment, projection, closest_point_strategy);  
        const auto length = boost::geometry::length(projection, distance_strategy);  
  
        std::cout << std::setprecision(20)  
                  << "Point: " << boost::geometry::wkt(point) << "\n"  
                  << "Segment: " << boost::geometry::wkt(segment) << "\n"  
                  << "Distance: " << distance << "\n"  
                  << "Projection: " << boost::geometry::wkt(projection) << "\n"  
                  << "Length: " << length << std::endl;  
  
        BOOST_CHECK_LE(std::fabs(distance - length), 1e-6);  
    }  
```  
  
My result is the following (same with Debug and Release mode):  
> Point: POINT(11.894846916198730469 50.316379547119140625)  
Segment: LINESTRING(11.895840200000000308 50.315591799999999978,11.895342599999999322 50.315550399999999343)  
Distance: 98.757331318280364485  
Projection: LINESTRING(11.894846916198732245 50.31637954711914773,11.895342599999999322 50.315550400000006448)  
Length: 98.757285403844150551  
library_tests.cpp(162): error: in "library_tests/clostest_path_test_5": check std::fabs(distance - length) <= 1e-6 has failed [4.59144362139340955764e-05 > 9.9999999999999995e-07]  
  
So my current tests file is now the following (with a larger error margin for test 5 so that it works for the moment):  
[library_tests.cpp.txt](https://github.com/boostorg/geometry/files/6067869/library_tests.cpp.txt)  
  
For me, the error is sufficiently small in practice. I have to deal with floating point errors throughout my whole project, so I have to work and deal with error margins in many places. Still it is strange because after all this is not expected when the length of the segment is different to the cross_track_distance, right?  
  
What I do to find similar examples is to import some road network from OSM (with libosmium) and calculate closest_points to road segments from random point clouds in that area. I use Boost Graph for organizing the nodes and edges of Boost Geometry points and linestrings. With RTree from Boost Geometry I am able to find the nearest segments of a road (with k-nearest for example) before applying closest_points. Then I compare distance and length of projected segment for finding such examples. If you need more examples please let me know.

---

## Comment 85

> Username: addy90  
> Created_at: 2021-03-02 13:40:18 UTC  
> Updated_at: 2021-03-02 13:45:10 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-788916452  

Sorry to bother again, here I found a very strange result from the following test:  
[test_6.txt](https://github.com/boostorg/geometry/files/6068973/test_6.txt)  
  
Output is here:  
> Point: POINT(11.914519782008024862 50.319138765234583843)  
Segment: LINESTRING(11.91451570000000082 50.319101699999997379,11.914525700000000441 50.319192499999999768)  
Distance: 0.095230389158085126944  
Projection: LINESTRING(11.914519782008024862 50.319138765234583843,11.914519782002392034 50.319138765183417661)  
Length: 0.095230389158085126944  
library_tests.cpp(191): info: check std::fabs(distance - length) <= 1e-4 has passed  
  
The distance is the same but this is not the problem. The result looks like this when viewed in QGIS (QuickWKT plugin for pasting the WKT strings from above output):  
![Screenshot 2021-03-02 143413](https://user-images.githubusercontent.com/6824664/109656347-9f084f00-7b64-11eb-8555-99647c7969c5.png)  
In green is the point, in red is the segment, in blue is the projection.  
  
That is a very strange result. Shouldn't the projection have a right angle to the segment? Can you verify this? Might come frome accumulated rounding errors...

---

## Comment 86

> Username: vissarion  
> Created_at: 2021-03-10 10:05:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-795187212  

@addy90 About the first issue above it seems like floating point issue and seems minor to me at this point. The second issue I think it is an inaccuracy of the graphical interface since the distances are very small. Please have a look at my last commit and let me know what you think.

---

## Comment 87

> Username: addy90  
> Created_at: 2021-03-10 12:18:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-795334905  

@vissarion Thank you for looking again! For me this looks fine, I am currently examining the remaining floating point issues in my codes and already did similar templating of the floating point types. I already noticed that lengths are by default calculated as "long double" [see here](https://github.com/boostorg/geometry/blob/efb1dcd5ddecae2c83638804aa1a2b056c14cf57/include/boost/geometry/strategies/default_length_result.hpp#L67) compared to distances in "double" (depends on the coordinate type, [see this file](https://github.com/boostorg/geometry/blob/master/include/boost/geometry/strategies/distance_result.hpp)). I need to be careful mixing these resolutions for reducing floating point accumulation errors in my further calculations. For the rest I just need to find out the right tolerances for my use cases. I agree that minor differences are no issue of the algorithm.  
I also adapted your test pattern. If I find a new problem I can send you the tests in the pattern that you proposed. Thanks again!

---

## Comment 88

> Username: vissarion  
> Created_at: 2021-11-03 09:00:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/707#issuecomment-958758212  

I am closing this PR and create new ones that use the new umbrella strategies; starting from https://github.com/boostorg/geometry/pull/923

---
