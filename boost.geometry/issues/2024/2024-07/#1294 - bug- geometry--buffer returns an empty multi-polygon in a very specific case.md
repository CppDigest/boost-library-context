# #1294 - bug: geometry::buffer returns an empty multi-polygon in a very specific case [Closed]

> Username: DJ-caddev  
> Created at: 2024-07-25 09:40:15 UTC  
> Updated at: 2024-11-15 21:24:22 UTC  
> Closed at: 2024-11-15 21:24:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1294  

BOOST v1.84 / v1.85  
  
Using the following code, lOuts is empty in the end:  
```c++  
boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>> lPolygon;  
lPolygon.outer().push_back({730.35, 750.00});  
lPolygon.outer().push_back({730.35, 740.00});  
lPolygon.outer().push_back({726.02, 740.00});  
lPolygon.outer().push_back({726.02, 735.00});  
lPolygon.outer().push_back({730.35, 735.00});  
lPolygon.outer().push_back({730.35, 0.00});  
lPolygon.outer().push_back({0.00, 0.00});  
lPolygon.outer().push_back({0.00, 750.00});  
lPolygon.outer().push_back({730.35, 750.00});  
  
static const boost::geometry::strategy::buffer::side_straight side_strategy;  
const boost::geometry::strategy::buffer::distance_symmetric<double> distance_strategy(5);  
boost::geometry::model::multi_polygon<boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>>> lOuts;  
  
static const boost::geometry::strategy::buffer::join_miter join_strategy;  
static const boost::geometry::strategy::buffer::end_flat end_strategy;  
static const boost::geometry::strategy::buffer::point_circle point_strategy;  
boost::geometry::buffer(lPolygon, lOuts, distance_strategy, side_strategy, join_strategy, end_strategy, point_strategy);  
```  
  
The shape looks like this:  
![image](https://github.com/user-attachments/assets/3bd5ed72-1187-4742-bb79-c5907a54cc6c)  
  
If the small hole is moved a bit to the bottom, the boost function seems to work well.  
This is a very specific case that crashes in our code.  
Moreover, we need to keep "rectangular/cartesian" shapes (we cannot have arcs in the final shapes).  
  
Do you guys have any clue of how to bypass this particular problem? Is this a wrong usage from on our side or something that Boost can fix directly?  
  
If you need any detail, feel free to ask :)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2024-07-27 11:32:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1294#issuecomment-2254122879  

Indeed, I can reproduce it. The code is right.

---

## Comment 2

> Username: barendgehrels  
> Created at: 2024-07-27 12:15:25 UTC  
> Updated at: 2024-07-27 12:17:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1294#issuecomment-2254133265  

Ugly workaround: add 1.0e-12 to the distance.  
  
Smaller reproduction wkt: `POLYGON((130.35 150,130.35 140,126.02 140,126.02 135,130.35 135,130.35 0,0 0,0 150,130.35 150))`  
  
It generates colocated turns. The green ones on the right side are all detected OK. But the top two are colocated (therefore you see only 2) and that causes the problem.  
![image](https://github.com/user-attachments/assets/331341db-c0cb-4734-aa8b-9a367885f4c0)  
  
To be continued.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2024-07-29 07:42:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/1294#issuecomment-2255227593  

I've a working fix and will make a PR on Wednesday

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-15 21:24:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/1294#issuecomment-2479952384  

This was fixed in July 30 (but the issue was not closed automatically, somehow - and not manually). PR #1300  
Closing.
