# #485 - boost::geometry::buffer gives a void polygon [Closed]

> Username: marcobeninca71  
> Created at: 2018-05-31 13:03:38 UTC  
> Updated at: 2018-09-24 16:16:29 UTC  
> Closed at: 2018-09-24 16:16:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/485  

Dear all  
the function boost::geometry::buffer for the polygon described by the attached file gives a void (no points inside) polygon as a result.  
  
My code is as follow  
  
			  
			boost::geometry::strategy::buffer::join_round join_strategy;  
			boost::geometry::strategy::buffer::point_square circle_strategy;  
			boost::geometry::strategy::buffer::end_flat end_strategy;  
			boost::geometry::strategy::buffer::side_straight side_strategy;  
  
			MultiPolygon2D offset;  
  
			boost::geometry::strategy::buffer::distance_symmetric<double> distance_strategy(-offsetDistance);  
  
			Polygon2D::ExteriorRing ring(_curve.begin(), _curve.end());  
			Polygon2D poly(ring);  
  
			boost::geometry::buffer(poly, offset, distance_strategy, side_strategy, join_strategy, end_strategy, circle_strategy);  
  
  
  
Where Polygon2D is a custom class specializing boost::geometry::model::polygon and MultiPolygon2D is a vector of Polygon2D; _curve is a vector of points. The parameter offsetDistance is positive, so I'm buffering with negative distance.  
  
I'm using this implementation from a while and I never had any issue.  
Is this a bug or should I use the function in some different way?  
  
[points.txt](https://github.com/boostorg/geometry/files/2058248/points.txt)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2018-09-20 10:58:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/485#issuecomment-423140894  

Hi @marcobeninca71   
Sorry for the (very) late reaction. We cannot investigate this issue as long as the distance is unknown. Because sure, buffering with a negative distance can result in a void polygon. It just depends on the distance.  
  
Also, for investigation by us it is way more convenient to include a complete minimal program (now we have to rewrite your program...)

---

## Comment 2

> Username: adrien-berchet  
> Created at: 2018-09-21 09:54:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/485#issuecomment-423479494  

Maybe it is related to my (very old) issue #362 ?

---

## Comment 3

> Username: marcobeninca71  
> Created at: 2018-09-24 06:13:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/485#issuecomment-423884005  

Hi @barendgehrels   
Making more tests I found out that the points describing the border are not in the rigth order. Calling boost::geometry::correct on them everything works properly  
Thanks  
Marco

---

## Comment 4

> Username: barendgehrels  
> Created at: 2018-09-24 16:16:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/485#issuecomment-424033930  

Good to hear, thank you!
