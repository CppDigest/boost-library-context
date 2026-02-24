# #704 - Inconsistency in overlaps() vs. intersection()? [Open]

> Username: isohedral  
> Created at: 2020-04-26 21:09:01 UTC  
> Updated at: 2020-04-27 07:44:05 UTC  
> Url: https://github.com/boostorg/geometry/issues/704  

Hi,   
  
I'm confused about a particular case of checking for overlaps.  I've got two polygons, `p1` and `p2`, that meet along a line. I'm expecting `overlaps(p1,p2)` to return `false`, but I get back `true`. What's weird about that is that when I try to prise apart what's happening, I find that the intersection of `p1` and `p2` is empty, which is inconsistent with the claim that the shapes overlap.  Am I doing something obviously wrong, or am I misunderstanding the behaviour of `overlaps()`, or is it simple floating point imprecision and I'm out of luck?   
  
Full code appended below.  Thanks for the help!  
  
```  
#include <iostream>  
#include <deque>  
  
#include <boost/geometry.hpp>  
  
typedef boost::geometry::model::d2::point_xy<double> point;  
typedef boost::geometry::model::polygon<point> polygon;  
  
int main( void )  
{  
    polygon p1;  
    polygon p2;  
  
    boost::geometry::read_wkt( "POLYGON((0.6666666666685 -1.5396007178376,1.6666  
666666685 -1.5396007178376,1.16666666667 -0.673575314055,0.166666666667 1.058475  
49351,-0.833333333333 1.05847549351,0.6666666666685 -1.5396007178376))", p1 );  
    boost::geometry::read_wkt( "POLYGON((1.66666666666947 0.192450089732598,0.66  
6666666667 0.19245008973,1.16666666667 -0.673575314055,1.6666666666745 -1.539600  
7178426,1.66666666666947 0.192450089732598))", p2 );  
  
    if( boost::geometry::overlaps( p1, p2 ) ) {  
        std::cout << "overlaps" << std::endl;  
        std::deque<polygon> out;  
  
        boost::geometry::intersection( p1, p2, out );  
  
        double area = 0.0;  
  
        std::cout << "Number of intersection geometries: " << out.size()  
            << std::endl;  
        for( auto& p : out ) {  
            std::cout << "    " << boost::geometry::wkt( p ) << std::endl;  
            area += boost::geometry::area( p );  
        }  
  
        std::cout << "Intersection area: " << area << std::endl;  
    } else {  
        std::cout << "does not overlap" << std::endl;  
    }  
}  
```

---

## Comment 1

> Username: tinko92  
> Created at: 2020-04-26 22:58:08 UTC  
> Updated at: 2020-04-26 23:16:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/704#issuecomment-619637998  

Hi, I cannot tell you why you get the inconsistency without further analysis (I suspect numerical issues, more on that further below), but I have a strong suspicion why you get true for overlaps:  
  
If I didn't make a mistake, your problem, if drawn with the svg_mapper, looks like this:  
![polygons](https://user-images.githubusercontent.com/17624459/80321544-1f13a900-881e-11ea-8637-7689da218335.png)  
  
With:  
```  
point p1(0.6666666666685, -1.5396007178376),  
      p2(1.6666666666685, -1.5396007178376),  
      p3(1.16666666667, -0.673575314055),  
      p4(0.166666666667, 1.05847549351),  
      p5(-0.833333333333, 1.05847549351),  
      p6(1.66666666666947, 0.192450089732598),  
      p7(0.666666666667, 0.19245008973),  
      p8(1.6666666666745, -1.5396007178426);  
  
polygon poly1{{p1, p2, p3, p4, p5, p1}};  
polygon poly2{{p6, p7, p3, p8, p6}};  
```  
The first thing I notice is that your polygon is counter-clockwise but the default for the polygon model is clockwise, you can make the model counter-clockwise by declaring polygon as `typedef boost::geometry::model::polygon<point, false> polygon;`  
  
Next thing, I notice, is that if I test `boost::geometry::strategy::side::side_by_triangle<double>::apply(p3, p4, p7)` I get `1`, i.e. "left of segment (> 0)". So, if point 7 lies to the left of the segment from point 3 to point 4, then I think point 7 lies inside the first polygon, so in that case overlaps returning true is correct.  
  
The intersection is still empty, I suspect because of numerical issues, because if I declare BOOST_GEOMETRY_NO_ROBUSTNESS and replace side_by_triangle with the numerically robust side_robust strategy (currently in extensions, which are not in the release afaik), I actually get an intersection, namely:   
  
```  
Number of intersection geometries: 1  
    POLYGON((1.16553 -0.67161,0.666667 0.19245,0.666667 0.19245,1.16553 -0.67161))  
Intersection area: 2.44812e-14  
```  
  
which is, as expected, a very spiky triangle between point 3 and point 7.  
  
I suppose a quick fix to get no overlap, would be to just add point 7 to polygon 1, like this:  
`polygon poly1{{p1, p2, p3, p7, p4, p5, p1}}, poly2{{p6, p7, p3, p2, p6}};`  
  
If I do that (combined with the orientation fix described at the start of my comment), overlaps returns false.

---

## Comment 2

> Username: isohedral  
> Created at: 2020-04-26 23:19:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/704#issuecomment-619640476  

Regarding polygon orientation, my apologies—I had been experimenting with both CW and CCW orientation and left the example code in the wrong state.  In my main implementation I've got the polygon type correctly declared as CCW, and the bug still manifests.  
  
Sure, I would have chalked things up to pure numerical issues and not submitted an issue.  The part that struck me as odd was that `overlaps()` and `intersection()` effectively disagree on their answers.  I would have expected them at least to be consistent with each other.  
  
Interestingly, it was originally my intention that point 7 be included in `p1`.  That polygon was generated by taking the union of two others that also met along a shared edge, and the `union_` function seems to have deleted the (collinear) vertex along the way. Is there a way to prevent that from happening?  
  
Anyway, I have a relatively simple workaround for the time being. Instead of checking `overlaps()`, I compute the intersection and check that its area is less than an epsilon. I assume that I'll take a performance hit, but my polygons are small enough that I don't think it'll cause problems in practice.

---

## Comment 3

> Username: tinko92  
> Created at: 2020-04-26 23:34:48 UTC  
> Updated at: 2020-04-26 23:35:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/704#issuecomment-619642376  

I am not a maintainer, so I don't know if union_ deletes collinear vertices and if there is a way to stop it. But point 7 is not strictly collinear with point 3 and point 4, as this issues shows, so that may be be a failure in robustness as well.  
  
I would be interested in looking at it, if it would not be too much trouble, could you show me the data that you fed into union_? Maybe I can look at it and determine whether point 7 is not included because of a miscalculation and maybe that would be relevant to another discussion ( #699 ) in which I argue for the inclusion of side_robust.

---

## Comment 4

> Username: isohedral  
> Created at: 2020-04-27 02:55:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/704#issuecomment-619681950  

I think this example demonstrates the disappearing vertex. The two input polygons have 9 vertices (including the duplicates at the end).  You'd expect the union to have 15 vertices, but it has 14. Feedback welcome (including how to force collinear vertices to stick around, if that's possible).  Keep in mind that my polygons are CCW.  
  
```  
POLYGON((1.6666666666685 -1.5396007178376,1.16666666667 -0.673575314055,0.166666666667 -0.673575314055,-0.3333333333345 -1.5396007178376,-0.833333333339 -2.4056261216252,0.166666666667 -2.4056261216252,1.16666666667 -2.4056261216252,0.6666666666685 -1.5396007178376,1.6666666666685 -1.5396007178376))  
POLYGON((-1.33333333333 0.19245008973,-0.333333333333 0.19245008973,-0.833333333333 -0.673575314055,0.166666666667 -0.673575314055,1.16666666667 -0.673575314055,0.666666666667 0.19245008973,0.166666666667 1.05847549351,-0.833333333333 1.05847549351,-1.33333333333 0.19245008973))  
```

---

## Comment 5

> Username: tinko92  
> Created at: 2020-04-27 07:44:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/704#issuecomment-619796328  

Thanks. Unfortunately, my knowledge of the implementation of union_ is insufficient to tell how to force it to keep collinear vertices. I hope somebody else can help you with that.  
  
However, here is one observation, maybe it helps you work around this issue: If I build a program that computes the union of these two polygons and I pass "-DBOOST_GEOMETRY_NO_ROBUSTNESS" to g++ to define the no-robustness-flag, then the disappearing vertex shows up in the union. I think it was mentioned in another issue that this will ultimately become the default.
