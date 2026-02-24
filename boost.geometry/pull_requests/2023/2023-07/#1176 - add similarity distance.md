# #1176 [similarity] add similarity distance [Open]

> Username: barendgehrels  
> Created at: 2023-07-12 18:01:31 UTC  
> Updated at: 2024-01-23 15:19:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176  

This is a new similarity distance, comparable to Hausdorff or Frechet.  
  
It calculates the area between two lines and divides them by the line length. This is an average distance, measuring quite well how similar two lines are. It is less sensible to outliers (such as Hausdorff/Frechet) and not discrete.  
  
For example this case (west1/west2):  
  
![image](https://github.com/boostorg/geometry/assets/334849/997498fb-2b4a-40a7-ae6f-2985a00b29f9)  
  
They are quite similar. Distance impressions are:  
`Frechet: 53.4426 Hausdorff: 53.4426 Average: 5.61308`  
  
How it works in detail:   
 * it projects all points on the other line  
 * and vice versa  
 * it adds their own points too. Then both collections have an equal amount of points, and because of the projections, their locations are comparable (especially if they are already similar)  
 * it checks if one of the lines should be reversed  
 * then it calculates areas of all four-corner rings and sums them  
 * and it divides by the shortest length  
  
**(See the comment below for an enhanced description)**  
  
Zoomed in:  
![image](https://github.com/boostorg/geometry/assets/334849/9767757e-4101-4baf-8066-a2f3e9c6dab4)  
  
Apart from this, this PR also adds an experimental `geojson_writer`  
  
If you like it, I'll also add a version for rings. For polygons/multi it is a bit more complex (especially if the number of objects are unequal), to be worked out.  
  
The `geojson_writer` is really useful for visualizing results (or debug steps). But it is not completely finished either.  
  
Complete output of the test program:  
```  
Simplex cases:   
Frechet: 0.1 Hausdorff: 0.1 Average: 0.1  
Frechet: 0.509902 Hausdorff: 0.509902 Average: 0.1  
Frechet: 1.58114 Hausdorff: 1.58114 Average: 0.114  
  
Real cases:   
Frechet: 53.4426 Hausdorff: 53.4426 Average: 5.61308  
Frechet: 81.0897 Hausdorff: 81.0897 Average: 3.98684  
  
Reversed:  
Frechet: 990.622 Hausdorff: 53.4426 Average: 5.61308  
Frechet: 1199.04 Hausdorff: 81.0897 Average: 3.98684  
  
Simplified (west):   
Frechet: 494.575 Hausdorff: 494.575 Average: 61.4674  
Frechet: 475.107 Hausdorff: 475.107 Average: 24.5124  
Frechet: 54.7065 Hausdorff: 54.7065 Average: 0.733346  
Frechet: 23.9904 Hausdorff: 23.9904 Average: 0  
  
Simplified (east):   
Frechet: 520.245 Hausdorff: 520.245 Average: 124.301  
Frechet: 74.5731 Hausdorff: 74.5731 Average: 7.53191  
Frechet: 74.5731 Hausdorff: 74.5731 Average: 0.511786  
Frechet: 0 Hausdorff: 0 Average: 0  
  
Not similar:   
Frechet: 8760.18 Hausdorff: 7737.14 Average: 4925.39  
Frechet: 8771.21 Hausdorff: 7746.79 Average: 4848.01  
  
Parts (TODO):  
Frechet: 831.744 Hausdorff: 831.744 Average: 7.25765  
Frechet: 1094.28 Hausdorff: 1094.28 Average: 199.363  
  
Identical:   
Frechet: 0 Hausdorff: 0 Average: 0  
Frechet: 0 Hausdorff: 0 Average: 0  
  
Performance:  
Frechet: 105 ms  
Hausdorff: 78 ms  
Average: 498 ms  
```  
  
Showing that:  
* hausdorff was not symmetric (I created an issue for that)  
* frechet gives different results for reversed cases  
* frechet and hausdorff are often, but not always, the same  
* the new method is much slower (we can use an index to enhance this)  
* because frechet/hausdorff are discrete, its behavior is not good when there are points in between (see simplex 2, or the simplified cases)  
* because frechet/hausdorff use a max distance, a spike large influence (simplex 3)  
  
_I also tried to give a "partial" result, which happens if one line is partly identical to the other line. This is possible, but not yet finished. It uses the projections and then recursively finds the best division point. But this is not part of the PR._

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2023-07-13 17:52:35 UTC  
> Updated_at: 2023-07-13 17:53:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1634660955  

A rephrased description, with a bit of AI help:  
  
To compute the Average Similarity Distance (ASD) between two linestrings, let's consider linestring `P` and linestring `Q`. The ASD is derived by comparing the geometric characteristics of the two linestrings.  
  
1. Projection: Each point of linestring `Q` is projected onto linestring `P`, identifying the shortest distance for projection. This projection process creates a modified version of linestring `P`, denoted as `P'`. Similarly, each point of linestring `P` is projected onto linestring `Q`, resulting in a modified version of linestring `Q` called `Q'`.  
2. Augmentation: All points of linestring `P` are added to `P'`, maintaining their original position, with an offset value of `0` and increasing segment distance. Similarly, all points of linestring `Q` are added to `Q'`.  
3. Alignment: `P'` and `Q'` are sorted based on their segment index and offset values in increasing order. This sorting ensures that both collections are comparable.  
4. Reversal: If necessary, one of the linestrings is reversed to achieve better alignment between corresponding points in `P'` and `Q'`. This step ensures consistency in the comparison.  
5. Area Calculation: The areas of all quadrilaterals, formed by connecting each point with its subsequent point in both `P'` and `Q'`, are calculated. The sum of these areas is computed.  
6. Normalization: The total sum of areas is divided by the shortest length of the two linestrings `P` and `Q` to normalize the ASD value.  
  
The resulting ASD value provides an average distance measure that captures the geometric similarity between the linestrings `P` and `Q`. By incorporating projection, augmentation, alignment, and area calculation steps, the ASD offers an enhanced understanding of the similarity between the two linestrings.

---

## Review 2 [Commented]

> Username: vissarion  
> Created_at: 2023-07-21 16:28:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1176#pullrequestreview-1536889618  

Great!! That's a new algorithm right? There is a similar work where they compute the area between two linestrings but in a slightly different way https://link.springer.com/article/10.1007/s12289-018-1421-8 (ends up in a different area).  
  
A did a first parse and put some comments in the code.  
  
Is there cases where other measures (Fréchet or Hausdorff) is better than the new one? How the new method perform when the inputs differ a lot between them?

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
  68 |+ {
  69 |+     using coor_t = typename coordinate_type<Point>::type;
  70 |+     using closest_strategy = geometry::strategy::closest_points::detail::compute_closest_point_to_segment<coor_t>;
```

> Username: vissarion  
> Created_at: 2023-07-19 11:33:13 UTC  
> Updated_at: 2023-07-21 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1267944841  

I think this should passed as a function parameter following a similar mechanism as in all other algorithms.

---

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
  76 |+         auto const& next = target[i + 1];
  77 |+         projection<Point> other;
  78 |+         auto const pp = closest_strategy::apply(point, current, next);
```

> Username: vissarion  
> Created_at: 2023-07-19 11:42:19 UTC  
> Updated_at: 2023-07-21 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1267953761  

This is very similar to how closest points are computed for a linestring. I guess you are not using that algorithm because you want to keep track also the index. Maybe the closest_points procedure can be enhanced with this extra information (e.g. providing the index) and reused here?   
  
From performance point of view there is some duplication in the computation of the closest point here with the distance  computed in two lines below. Also, you may consider using the comparable distance instead of the real distance.

> Username: barendgehrels  
> Created_at: 2023-07-27 11:37:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1276154683  

I looked at it. It seems that some code duplication is also there in the `detail::closest_feature::point_to_point_range` function, where the closest segment is determined based on (comparable) distance. Which, internally, already finds the projection - but it is discarded (in the strategy itself).  
Then the closest point is calculated.  
  
Here I do it the other way round, calling first closest point strategy (which already does some calculations to find it - but does not calculate the distance yet) and then calculate its distance (I could use comparable instead - changed it right now).  
  
I'm not sure if it is worth the effort to harmonize this code for this reason only, it will make it more complex.  
  
What could be useful is a strategy which gives all the internal information to avoid code duplication in other places. Something like that is already in extensions (`calculate_distance_info`) but never made it. So maybe also that is not really worth the effort...

---

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
 314 |+         boost::begin(q_enriched), boost::end(q_enriched), ring, visitor);
 315 |+ 
 316 |+     auto const length = (std::min)(geometry::length(p), geometry::length(q));
```

> Username: vissarion  
> Created_at: 2023-07-19 13:24:07 UTC  
> Updated_at: 2023-07-21 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1268070629  

at least for the reversed linestring the length is already computed (maybe this can be reused here)

> Username: barendgehrels  
> Created_at: 2023-07-27 11:43:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1276160468  

I don't see that, unless you mean the distance calculation done for reversal.

---

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
 193 |+     int const side_p1_q = side_strategy::apply(q0.point, q1.point, p1.point);
 194 |+ 
 195 |+     if (side_q0_p == 0 && side_q1_p == 0 && side_p0_q == 0 && side_p1_q == 0)
```

> Username: vissarion  
> Created_at: 2023-07-21 16:22:43 UTC  
> Updated_at: 2023-07-21 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1270863208  

shouldn't `side_q0_p == 0 && side_q1_p == 0` be enough?

> Username: barendgehrels  
> Created_at: 2023-07-27 11:42:26 UTC  
> Updated_at: 2023-07-27 11:42:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1276159530  

That should indeed be enough, at least for the cases I tested. However, I'm not sure about FP-precision where there are sometimes weird results (such as 3 times 0 and one non-zero). And the sides have to be calculated later (used later) anyway.  
But with those FP-precision errors the area is probably minimal - so it probably won't make a real difference.  
  
I will think about what my final preference is here.

---

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
 180 |+ 
 181 |+ template <typename Projection, typename Ring>
 182 |+ bool fill_quadrilateral(Projection const& p0, Projection const& p1, Projection const& q0, Projection const& q1, Ring& ring)
```

> Username: vissarion  
> Created_at: 2023-07-21 16:23:17 UTC  
> Updated_at: 2023-07-21 16:28:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1270863718  

is it always a quadrileteral? In some cases could be a triangle or even degenerated to a segment.

> Username: barendgehrels  
> Created_at: 2023-07-22 13:25:59 UTC  
> Updated_at: 2023-07-22 13:26:00 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1271296303  

Indeed, it can. But sorting that out is probably not worth it, because the area will be the same.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2023-07-21 22:03:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1646280090  

Thanks, that's interesting!  
  
AFAIU the CS-specific operations are closest points, side and area so technically this distance should work in all coordinate systems correct?  
  
Do I understand correctly that you wanted to show a prototype to start a discussion and you plan to implement it with Boost.Geometry structure (strategy, dispatch, etc.) if we agree that this is a good addition to the library?  
  
Is ASD an official name for this measure?  
Would giving the function a name more specific than `similarity_distance` be a good idea?  
Or do you think that this should be baseline, default similarity measure in the library?

---

## Review 4 [Commented]

> Username: awulkiew  
> Created_at: 2023-07-21 22:08:29 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1176#pullrequestreview-1541894479  

📁 include/boost/geometry/algorithms/discrete_hausdorff_distance.hpp

```diff
  32 |+ #include <iterator>
  33 |+ #include <utility>
  34 |+ #include <limits>
```

> Username: awulkiew  
> Created_at: 2023-07-21 22:08:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1271141220  

I recall that this order of headers is prefered in general. In our case this would be Boost.Geometry headers, then other Boost headers, then standard headers. Do we want to make a guideline about this?  
  
I'd add one thing. The headers are not sorted alphabetically. They probably should.

> Username: awulkiew  
> Created_at: 2023-07-21 22:10:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1271142297  

I see that in other file the order is different so I'm curious why were they moved here?


---

## Review 5 [Commented]

> Username: awulkiew  
> Created_at: 2023-07-21 22:09:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1176#pullrequestreview-1541895099  

📁 include/boost/geometry/algorithms/similarity.hpp

```diff
  16 |+ #include <boost/geometry/core/point_type.hpp>
  17 |+ #include <boost/geometry/algorithms/distance.hpp>
  18 |+ #include <boost/geometry/algorithms/length.hpp>
```

> Username: awulkiew  
> Created_at: 2023-07-21 22:09:39 UTC  
> Updated_at: 2023-07-21 22:09:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#discussion_r1271141683  

Here standard headers are before Boost.Geometry headers though.


---

## Comment 6

> Username: barendgehrels  
> Created_at: 2023-07-22 13:27:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1646584384  

Thank you both for your comments! Cool!  
Yes, I think `similarity_distance` would be a good name, it also crossed my mind.  
I will come back to your comments in more detail next week on Thursday, probably.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2023-07-22 14:45:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1646599373  

To clarify, I'm considering whether or not giving it such general name is a good idea. There are various measures of similarity after all. And AFAIU they are not interchangeable. Unless it would be possible to define some kind of standard of similarity allowing to generate comparable results between various algorithms? So the question is which one if any should be considered as the default one in the library? If it's not possible to answer this question then maybe this algorithm should have a specific name like the other ones.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2023-07-27 12:09:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1653488579  

> AFAIU the CS-specific operations are closest points, side and area so technically this distance should work in all coordinate systems correct?  
  
Yes, correct, I added a call for geographic coordinate system as well.  
  
>   
> Do I understand correctly that you wanted to show a prototype to start a discussion and you plan to implement it with Boost.Geometry structure (strategy, dispatch, etc.) if we agree that this is a good addition to the library?  
  
Yes, indeed  
  
> Is ASD an official name for this measure? Would giving the function a name more specific than `similarity_distance` be a good idea? Or do you think that this should be baseline, default similarity measure in the library?  
  
Okay, I now have `average_distance` - which is, in fact, what it is. And I think it's a better measure for similarity than hausdorff or frechet, because that is more a "max distance" which might have a large value while all other points are close or equal, apart from the problems because it is discrete. But this might be biased and have to be researched a bit more.

---

## Comment 9

> Username: awulkiew  
> Created_at: 2023-09-25 12:01:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1176#issuecomment-1733527307  

> Okay, I now have average_distance - which is, in fact, what it is.  
  
Would it be possible to calculate the similarity of areal geometries (rings or polygons) this way, i.e. pass them into this algorithm and internally calculate the similarity of their boundaries? If it is then this name would not work for them because the average distance of two intersecting polygons would always be 0.

---
