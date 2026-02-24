# #1075 - [Request] Provide an argument to `correct` giving a vector defining the interior or exterior of a spherical polygon. [Open]

> Username: rconde01  
> Created at: 2022-10-21 15:09:52 UTC  
> Updated at: 2023-03-27 20:30:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075  

### Discussed in https://github.com/boostorg/geometry/discussions/1061  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **rconde01** October  4, 2022</sup>  
I'm trying to find a way to specify the inside of a polygon so that the results are correct when calling `correct`. For example, you might have a "cone" where the half angle is greater than 90 degrees. Currently this is corrected such that the axis of the code is flipped to the other side. Specifying a vector in the interior of the polygon should be able to resolve the ambiguity...but so far I don't see a way to do this.</div>

---

## Comment 1

> Username: rconde01  
> Created at: 2022-10-25 21:41:39 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1291174480  

I guess one way to implement is do a "contains" query, and then reverse if the answer is the opposite of the expected answer. But maybe there are more efficient ways.

---

## Comment 2

> Username: vissarion  
> Created at: 2022-11-17 14:16:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1318703555  

Could you please provide a minimal code example and explain the issue in more details. I am afraid I cannot fully understand your problem with the current description.

---

## Comment 3

> Username: rconde01  
> Created at: 2022-11-17 14:21:34 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1318711333  

I think I can explain more without code - but if you still want code later I can provide it. First let's start with boolean operations on a 2d plane. For a polygon, there's a clear inside and outside. Therefore based on the template argument for whether rings are specified clockwise or counter clockwise, correct can easily fix outer and inner rings to be "correct". However, things are not so simple for a spherical polygon. For example, an outer ring could valid define the interior of the polygon no matter which direction it's vertices are specified in. Therefore, in order to properly perform a "correct" a point defining the intended interior is required.

---

## Comment 4

> Username: vissarion  
> Created at: 2022-11-17 15:18:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1318789744  

OK, I see your point now, thanks for clarifications. I think a simpler choice from what you propose is to change `correct` to do nothing for the ordering of non-cartesian polygons/rings since both orderings are correct. Now a user can easily change the ordering to contain a given point so I would not include this functionality in correct. What do you think?

---

## Comment 5

> Username: rconde01  
> Created at: 2022-11-17 15:36:22 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1318817343  

Here's a code example btw:  
https://godbolt.org/z/6Pxbco356  
  
A couple thoughts:  
- On the one hand it's reasonable...but it seems to break user expectation because correcting vertex order is one of the main things `correct` is used for  
- So yes, you could take your polygon, do a `contains` operation, and then optionally invert based on the expectation - but is that the most efficient way to achieve this?

---

## Comment 6

> Username: vissarion  
> Created at: 2022-11-18 09:49:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1319772115  

OK, my proposal above was not correct. The main issue here is that Boost.Geometry does not support polygons that cover more than half of the spheroid. So if one gives an order that is opposite from the one in polygon declaration (default: clockwise) then the polygon is invalid and `correct` reverse the ordering to make it valid. Note that for invalid geometries the results of operations (such as `within`) does not make sense and the output can be anything.   
  
@awulkiew @barendgehrels I cannot find a point in documentation that explicitly states that those polygons are nor supported. Am I missing something here?

---

## Comment 7

> Username: rconde01  
> Created at: 2022-11-18 13:17:48 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1319983892  

Ouch - I definitely don't like that outcome. I admit I'm not an expert in the internals, but I don't see any reason that should be the case. Already the operations work as expected if the vertex order matches the intended definition - it's just `correct` which doesn't have the information to work in a meaningful way. I guess you could say something like "correct will choose the direction of minimum area". And then users could do a `contains` query to solve the original problem I posed. Again, I don't know if that's the most efficient way. But generally I think either representation is valid, and the user has to provide more information to disambiguate.

---

## Comment 8

> Username: vissarion  
> Created at: 2022-11-18 13:47:14 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1320015133  

OK I think you have to face this feature (Boost.Geometry does not support polygons that cover more than half of the spheroid) as a limitation. I agree that one can use the opposite (to the one defined by the polygon) vertex orientation to represent the complement of a polygon but this is not supported in Boost.Geometry now. Indeed if this is implemented you proposal with the extra point could be used for correct. But I do not see this as a straightforward task since all operations and algorithms should be enhanced to work with that kind of polygons.

---

## Comment 9

> Username: rconde01  
> Created at: 2022-11-18 14:02:33 UTC  
> Updated at: 2022-11-18 14:03:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1320033108  

I guess I'm confused when you say it doesn't support it. Maybe `correct` doesn't support it, but otherwise everything appears to work as expected (although I admit I haven't thoroughly tested everything). Also, "does not support polygons that cover more than half of the spheroid"...I wonder what that means exactly. Is that a polygon whose area is greater than 50% of the total area...or a polygon whose vertices all lie on a hemisphere opposed from the center, or something else?

---

## Comment 10

> Username: vissarion  
> Created at: 2022-11-18 14:29:27 UTC  
> Updated at: 2022-11-18 14:29:46 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1320070408  

```cpp  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/point_xy.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <cmath>  
#include <iostream>  
#include <vector>  
  
namespace bg = boost::geometry;  
  
using SphericalPoint =  
    bg::model::d2::point_xy<double, bg::cs::spherical_equatorial<bg::degree>>;  
  
using SphericalPolygon =  
    boost::geometry::model::polygon<SphericalPoint,  
                                    false,  // clockwise if true  
                                    true,   // closed  
                                    std::vector, std::vector, std::allocator,  
                                    std::allocator>;  
  
int main()   
{  
    SphericalPolygon quad  {{{0,0}, {0,1}, {1,1}, {1,0}, {0,0}}};  
    SphericalPoint p {0.5,0.5};  
  
    std::cout << "within=" << bg::within(p,quad) << std::endl;  
    std::cout << bg::wkt(quad) << std::endl;  
    std::cout << "is valid=" << bg::is_valid(quad) << std::endl;  
    std::cout << bg::area(quad) << std::endl;  
  
    bg::correct(quad);  
  
    std::cout << "within=" << bg::within(p,quad) << std::endl;  
    std::cout << bg::wkt(quad) << std::endl;  
    std::cout << "is valid=" << bg::is_valid(quad) << std::endl;  
    std::cout << bg::area(quad) << std::endl;  
  
    return 0;  
}  
```  
ΟΚ let me share a simple example based on your code where I use CW ordering while the definition of the polygon was CCW. Then `quad` is invalid and the area result is wrong (is the negative of the correct polygon). Also if the quad is the complement of  `POLYGON((0 0,1 0,1 1,0 1,0 0))` then within with `POINT(0.5,0.5)` should return false but this is not the case. After applying `correct` the results are as expected.

---

## Comment 11

> Username: rconde01  
> Created at: 2022-11-18 15:20:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1320167396  

sigh - ok...that's a good example. Though, this is a very clear case - I'm still not sure for a much more complex case how the determination is made. Maybe "if all points of a polygon fall within a (freely oriented) hemisphere, then the all of the interior of that polygon must also fall within this hemisphere." It would be nice if this could be fixed, but you don't seem very amenable to that priority.

---

## Comment 12

> Username: rconde01  
> Created at: 2022-11-18 19:23:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1320437511  

Say you have a bunch of polygons which all seem valid by your definition - if you union them they might create a polygon which violates the definition of valid. It seems like this issue has to be fixed for general usage.

---

## Comment 13

> Username: vissarion  
> Created at: 2022-11-21 09:13:38 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1321736960  

I agree this is a interesting non-trivial feature. I would be interested to work on it but I cannot promise timelines. Also I do not know if other libraries support this feature (AFAIK Microsoft's SQL Server does; at least for certain operations).

---

## Comment 14

> Username: vissarion  
> Created at: 2022-11-21 09:17:01 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1321741017  

> Say you have a bunch of polygons which all seem valid by your definition - if you union them they might create a polygon which violates the definition of valid. It seems like this issue has to be fixed for general usage.  
  
In Boost Geometry union (as any other) function should not generate invalid geometries from valid ones, if you have an example that this happens please file an (different) issue.

---

## Comment 15

> Username: rconde01  
> Created at: 2022-11-21 13:30:00 UTC  
> Updated at: 2022-11-21 13:49:55 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1322068368  

SQL Server supports boolean operations on spherical polygons?  
  
TIL: https://learn.microsoft.com/en-us/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-ver16

---

## Comment 16

> Username: rconde01  
> Created at: 2022-11-21 15:27:52 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1322235155  

> In Boost Geometry union (as any other) function should not generate invalid geometries from valid ones, if you have an example that this happens please file an (different) issue.  
  
https://github.com/boostorg/geometry/issues/1097  
  
This case just follows from the fact that the "greater than half" polygons are not handled correctly.

---

## Comment 17

> Username: rconde01  
> Created at: 2022-11-30 00:19:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1331486117  

btw if you can provide guidance, I would be interested in collaborating on fixing this

---

## Comment 18

> Username: vissarion  
> Created at: 2022-12-08 08:30:58 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1342264979  

sorry for the late reply, sure, I can help!

---

## Comment 19

> Username: rconde01  
> Created at: 2022-12-08 14:09:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1342794013  

great - if you can give me an outline of the tasks that need to be completed i can start digging in...would that be possible?

---

## Comment 20

> Username: rconde01  
> Created at: 2023-01-21 17:48:16 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1399298129  

@vissarion just pinging to remind you of this

---

## Comment 21

> Username: vissarion  
> Created at: 2023-02-13 14:49:23 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1428065924  

sorry for the slow replies here... I think it make sense to start with some simple algorithm like area since the whole project seems complicated (i.e. to provide full support for polygons larger than half of the globe). Then there is some issue of consistency since we do not want to change the behaviour of `correct()` before providing full support.   
One choice would be to let some functions return right results and declare in the documentation about the status. That is, area is treating "large spherical" polygons correctly even if  applying correct will change the input polygon. @barendgehrels what do you think?

---

## Comment 22

> Username: rconde01  
> Created at: 2023-02-13 18:09:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1428423377  

That seems like a good place to start to me.

---

## Comment 23

> Username: awulkiew  
> Created at: 2023-02-14 16:58:40 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1430074200  

@vissarion AFAIR polygons that cover less than half of the globe are there because either in setops or buffer the algorithm has to detect the order of points in rings (I don't remember why) and based on that do something with them. @barendgehrels should know more about it.

---

## Comment 24

> Username: rconde01  
> Created at: 2023-03-27 01:29:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1484335233  

@vissarion So one approach to area is pretty simple. If you assume the area should be positive, then you check for a negative result and add 4*pi. This does seem to break stuff downstream though (for example, correct).

---

## Comment 25

> Username: rconde01  
> Created at: 2023-03-27 20:30:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1075#issuecomment-1485823331  

Just wanted to note that I think potentially this enhancement can apply to 2d polygons as well. You could imagine defining a "pure" hole in the 2d plane such that everything outside the exterior ring is "inside the polygon"...although i guess you could do the same by just allowing an empty exterior ring.
