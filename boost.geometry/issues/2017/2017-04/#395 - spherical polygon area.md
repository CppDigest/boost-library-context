# #395 - spherical polygon area [Closed]

> Username: penghao94  
> Created at: 2017-04-19 15:14:32 UTC  
> Updated at: 2017-12-14 22:56:51 UTC  
> Closed at: 2017-12-14 22:56:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/395  

Hi，  
there may be a bug in calculating the area of spherical polygon. when the polygon 's verticals contain north polar,the result is right ,but when containing south polar, it returns a wrong result.  
I have tested POLYGON((0 0,90 0,0 -90,0 0)), we expect to 1.57249 but returning 2.02365.  
swanny

---

## Comment 1

> Username: awulkiew  
> Created at: 2017-07-28 02:04:04 UTC  
> Url: https://github.com/boostorg/geometry/issues/395#issuecomment-318535890  

What version of Boost are you using? develop and 1.64 seems to be fine.  
  
My test:  
  
    typedef bg::model::point<double, 2, bg::cs::spherical_equatorial<bg::degree> > point;  
    typedef bg::model::polygon<point> polygon;  
  
    polygon poly1, poly2;  
    bg::read_wkt("POLYGON((0 0,90 0,0 -90,0 0))", poly1);  
    bg::read_wkt("POLYGON((0 0,0 90,90 0,0 0))", poly2);  
  
    double a1 = bg::area(poly1);  
    double a2 = bg::area(poly2);  
  
I'm getting `1.5707963267948963` in both cases. The same for `float` coordinate type.

---

## Comment 2

> Username: awulkiew  
> Created at: 2017-12-14 22:56:02 UTC  
> Updated at: 2017-12-14 22:56:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/395#issuecomment-351861473  

I couldn't reproduce it so I'm going to close this issue. You didn't confirm or deny that the code above works for you so reopen this issue if needed. For now I'll assume that in recent versions of Boost the problem is fixed.
