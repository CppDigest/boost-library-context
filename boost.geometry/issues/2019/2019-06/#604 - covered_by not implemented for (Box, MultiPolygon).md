# #604 - covered_by not implemented for (Box, MultiPolygon) [Closed]

> Username: vschoech  
> Created at: 2019-06-21 09:55:39 UTC  
> Updated at: 2023-09-02 23:02:38 UTC  
> Closed at: 2022-10-19 13:24:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/604  

Any chance this gets implemented any time soon?  
See our conversation here:  
https://lists.boost.org/geometry/2012/02/1838.php

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-06-26 10:18:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-505814413  

Right, thanks for the reminder, that is a long time ago indeed.  
From my side I still have to postpone this until rescaling removal is finished.  
But thanks for opening the issue, it has a greater visibility than the mailing list.

---

## Comment 2

> Username: digu-007  
> Created at: 2020-02-14 08:15:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-586147293  

[covered_by](https://www.boost.org/doc/libs/1_72_0/libs/geometry/doc/html/geometry/reference/algorithms/covered_by/covered_by_2.html) is implemented for (Polygon, Multipolygon), can't we just make use of this and convert Box into the rectangle (which is a Polygon) out of the given two corners of Box, and then solve this query for (Box, MultiPolygon).  
  
If that's the case I would like to contribute towards the issue. :)

---

## Comment 3

> Username: vissarion  
> Created at: 2020-02-14 09:13:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-586168587  

>covered_by is implemented for (Polygon, Multipolygon), can't we just make use of this and convert Box into the rectangle (which is a Polygon) out of the given two corners of Box, and then solve this query for (Box, MultiPolygon).  
  
`Box` is a `Polygon` only in `cartesian` for other coordinate systems it cannot represented as a polygon since polygon's segments are geodesics (e.g. great circles for spherical) but box's segments are not.

---

## Comment 4

> Username: digu-007  
> Created at: 2020-02-14 09:31:27 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-586176468  

> `Box` is a `Polygon` only in `cartesian` for other coordinate systems it cannot represented as a polygon since polygon's segments are geodesics (e.g. great circles for spherical) but box's segments are not.  
  
Oh ok, I just now realized that we have four different kinds of coordinate systems here, I'll study them.

---

## Comment 5

> Username: vschoech  
> Created at: 2020-03-09 10:29:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-596447463  

Still missing implementations for covered_by in boost 1.72.0.

---

## Comment 6

> Username: vschoech  
> Created at: 2020-06-17 10:41:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-645297335  

Still missing implementations for covered_by in boost 1.73.0.

---

## Comment 7

> Username: vschoech  
> Created at: 2021-03-26 15:51:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-808325995  

Still missing implementations for covered_by in boost 1.75.0.

---

## Comment 8

> Username: awulkiew  
> Created at: 2021-03-26 18:23:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-808427993  

@vschoech out of curiosity, why do you need this combination for? Wouldn't checking  
```  
covered_by(box, return_envelope<Box>(multi_polygon));  
```  
be enough?

---

## Comment 9

> Username: vschoech  
> Created at: 2021-03-29 07:14:42 UTC  
> Updated at: 2021-03-29 07:15:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-809132690  

@awulkiew   
  
> ```  
> covered_by(box, return_envelope<Box>(multi_polygon));  
> ```  
  
I don't think so, unless I'm missing something. In case of a non-convex polygon, as illustrated on the bottom of this [man page](https://www.boost.org/doc/libs/1_73_0/libs/geometry/doc/html/geometry/reference/algorithms/envelope/return_envelope_1.html), there are a lot of boxes that are covered by the envelope, but not covered by the polygon. The same for a multi-polygon with at least two disjunct polygons.  
  
Note the definition of `covered_by(geometry1, geometry2)`: [The free function covered_by checks if the first geometry is inside or on border the second geometry.](https://www.boost.org/doc/libs/1_75_0/libs/geometry/doc/html/geometry/reference/algorithms/covered_by/covered_by_2.html)  
  
Does this make sense? Otherwise, please explain in more detail.

---

## Comment 10

> Username: awulkiew  
> Created at: 2021-03-29 09:41:00 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-809235601  

@vschoech Sorry, may bad. I confused the covered with the covering geometry. Yes, I can see that this may be useful in particular with relatively big concave polygons.  
  
In general I tend to think that there is no need to mix bounding geometries with geometries which they represent. That relational operations should be done for Boxes vs Boxes and then corresponding geometries should be processed. It's because the space partitioning/indexing is designed around trivial geometries. But if you work on Boxes specifically and they do not correspond to any other geometry this is a different story I guess. How it is in your case?  
  
I can imagine that for a specific case one could design a specific algorithm that would be more efficient than e.g. simply calling `covered_by` for each Box and a MultiPolygon.

---

## Comment 11

> Username: vschoech  
> Created at: 2021-03-29 09:53:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-809244056  

@awulkiew Thank you for the clarification. In my case I have text labels that are approximated by boxes, and that I need to clip against the polygon shape of an area chart (basically a line chart with color between the lines):  
  
![Screenshot 2021-03-29 115107](https://user-images.githubusercontent.com/44493453/112819712-2920d600-9085-11eb-9e96-a99c95e3f604.png)

---

## Comment 12

> Username: awulkiew  
> Created at: 2021-03-29 11:55:15 UTC  
> Updated at: 2021-03-29 12:23:33 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-809317959  

@vschoech Ok, got it.  
  
Note that technically this is not a use case for `covered_by(box, multi_polygon)` since what you really need is to check on which side of a linestring a box is. It's more complex if points can be duplicated, lines going backwards, spikes, etc. But in the most simple case this could be checked by calling side strategy for each segment of a linestring and for each point of a box, e.g. something like this:  
```  
    box b{ {0, 0}, {1, 1} };  
    linestring ls{ {0, 2}, {1, 3}, {2, 4} };  
    point points[4];  
    bg::detail::assign_box_corners_oriented<false>(b, points);  
    bool result = bg::all_segments_of(ls, [&](auto const& s)  
    {  
        for (int i = 0; i < 4; ++i)  
        {  
            // assuming points are sorted by x (so linestring going left to right)  
            if (bg::get<0>(s.first) <= bg::get<bg::max_corner, 0>(b)  
                && bg::get<0>(s.second) >= bg::get<bg::min_corner, 0>(b))  
            {  
                int side_val = bg::strategy::side::side_by_triangle<>().apply(s.first, s.second, points[i]);  
                if (side_val > 0) // point is on the left side of the segment  
                    return false;  
            }  
        }  
        return true;  
    });  
```  
  
EDIT: Actually you'd have to do this only for segments that have overlapping x cordinates to box coordinates. It's because one of the next/previous segments of a linestring could have such a slope so the box would be on a different side. I updated the example above.  
  
And then if you'd like to make it even more efficient you could create the y intervals for linestrings (min and max y coordinate, 1-d bounding boxes of linestrings, or simply 2-d bounding boxes and take the y cooridnates of corners), create a 1-d r-tree or something like that and only check the linestrings for boxes that fall into these intervals because for coordinates outside of them you know the result.

---

## Comment 13

> Username: vschoech  
> Created at: 2022-07-08 15:06:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-1179092390  

We have just updated our code base to boost 1.79.0. Apparently, there still is no `covered_by(box, multi_polygon)` in 1.79.0.  
  
@awulkiew I carefully re-read your detailed reply above. I admittedly fail to map your suggested solution to my problem: The way text labels are displayed, wether or not they are boxed, which color is used for boxing, and which color is used for text -- it all depends on the box's relation to the colored areas, which have polygon shape. Comparing to an ordered linestring doesn't seem to do the trick.

---

## Comment 14

> Username: vissarion  
> Created at: 2022-10-19 13:24:25 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-1284013075  

@vschoech https://github.com/boostorg/geometry/pull/1046 fixes this issue.

---

## Comment 15

> Username: vschoech  
> Created at: 2023-08-28 13:56:48 UTC  
> Updated at: 2023-08-28 13:57:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-1695748596  

It is great to see that `covered_by(box, multi_polygon)` is now implemented (boost 1.83.0)! 👍   
  
However, for empty boxes it returns `false` which, according to my email conversation with @barendgehrels (2018-04-18), is questionable. What is the perspective on this issue?

---

## Comment 16

> Username: awulkiew  
> Created at: 2023-09-02 11:09:36 UTC  
> Updated at: 2023-09-02 11:09:49 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-1703803471  

@vschoech What do you mean by empty box? AFAIU in Boost.Geometry a box can be degenerated to a point (min == max) or invalid (max < min) but never empty. Could you give an example?

---

## Comment 17

> Username: vschoech  
> Created at: 2023-09-02 23:02:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/604#issuecomment-1703959385  

@awulkiew thank you for clarification. In our terminology, a box is "empty" when !(min < max).  
  
Does "invalid" imply that the result of `covered_by(box, multi_polygon)` is meaningless/unusable?
