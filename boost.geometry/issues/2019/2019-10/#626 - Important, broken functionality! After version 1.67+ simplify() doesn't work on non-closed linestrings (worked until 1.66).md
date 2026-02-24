# #626 - Important, broken functionality! After version 1.67+ simplify() doesn't work on non-closed linestrings (worked until 1.66). [Closed]

> Username: romanshuvalov  
> Created at: 2019-10-12 00:24:08 UTC  
> Updated at: 2019-11-23 11:42:21 UTC  
> Closed at: 2019-11-23 11:42:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/626  

Up to version 1.66 geometry::simplify() worked with open (non-looped) linestrings. After 1.67 something happened and now simplify() works only with closed loops.   
  
Tested on 1.66 (simplify works), 1.67 (simplify doesn't work) and current 1.71 (simplify doesn't work).  
  
Demo attached, `c++ main.cpp -o demo && ./demo`  
  
[main.cpp.zip](https://github.com/boostorg/geometry/files/3719958/main.cpp.zip)

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-10-12 10:48:18 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-541313155  

Thanks for your report. Indeed something is changed there a few releases ago.  
It surprises me that this (main) case does not work and is not covered in unit tests, but I will have a look soon.

---

## Comment 2

> Username: benotn  
> Created at: 2019-11-11 15:11:28 UTC  
> Updated at: 2019-11-11 19:48:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-552483783  

I seem to have a similiar issue with geometry::simplify()  
  
In the following example, with boost>=1.67, Out does not contain points after running simplify,  
whereas with boost==1.66, the code works.  
  
Do you have any update on when you think this issue will be resolved?  
  
```c++  
typedef boost::geometry::model::d2::point_xy<double> point_type;  
typedef boost::geometry::model::polygon<point_type> polygon_type;  
  
polygon_type Vertices;  
Vertices.outer().reserve(4);  
Vertices.outer().emplace_back(point_type{-10.0, 0.0});  
Vertices.outer().emplace_back(point_type{-5.0, 0.0});  
Vertices.outer().emplace_back(point_type{-7.5, 3});  
  
polygon_type Out; // to hold new geometry  
boost::geometry::simplify(Vertices, Out, 0.01);  
```

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-11-13 11:40:07 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-553366457  

About the original ticket: you are not simplifying a linestring. You are simplifying a ring, which is expected to be closed.  
`BOOST_GEOMETRY_REGISTER_RING(test_boost_linestring_t)` means you register it as a ring, not as a linestring (which the name of the type seems to indicate). I changed it into linestring but that obviously does not compile because in the traits you assume it is a polygon...  
  
Boost.Geometry can simplify a ring (which is normally closed) and it does. If I uncomment the line to close the ring, it generates the output. Your ring type is expected to be closed. An open ring is invalid. You could call geometry::correct to close the ring.  
  
Indeed things change for rings/polygons in simplify: there were fixes and enhancements.  
  
About the additional example in the comment: also this is a polygon type, and also here the polygon is not closed.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2019-11-13 11:40:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-553366716  

Can I close this issue?

---

## Comment 5

> Username: benotn  
> Created at: 2019-11-20 13:02:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-555994089  

Yes, I just realized where I was wrong.  
From my end, you can close it.

---

## Comment 6

> Username: barendgehrels  
> Created at: 2019-11-23 11:42:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/626#issuecomment-557790874  

Thanks for confirming this. I'll close it now.
