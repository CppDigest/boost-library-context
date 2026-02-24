# #643 - boost::intersection produces wrong results by tolerance [Closed]

> Username: Legotckoi  
> Created at: 2019-11-18 12:41:02 UTC  
> Updated at: 2019-11-26 18:48:31 UTC  
> Closed at: 2019-11-26 18:48:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/643  

This is related to [#630](https://github.com/boostorg/geometry/issues/630)   
Consider the following:  
  
```  
#include <iostream>  
#include <boost/geometry.hpp>  
  
using namespace std;  
using namespace boost::geometry;  
using point2d = model::d2::point_xy<double>;  
using polygon = model::polygon<point2d>;  
using multiPolygon = model::multi_polygon<polygon>;  
  
void checkIntersection(const std::string& polyString, const std::string& multiPolyString)  
{  
    polygon poly;  
    multiPolygon multiPoly;  
    multiPolygon result;  
  
    read_wkt(polyString, poly);  
    read_wkt(multiPolyString, multiPoly);  
  
    intersection(multiPoly, poly, result);  
    correct(result);  
  
    cout << area(poly) << " " << area(result) << endl;  
    cout << result.empty() << " " << intersects(multiPoly, poly) << endl << endl;  
}  
  
int main()  
{  
    polygon poly;  
    multiPolygon multiPoly;  
    multiPolygon result;  
  
    // this works  
    cout << "works" << endl;  
    checkIntersection("POLYGON((-0.420826 0.168095,5.29161 5.76881,12.2925 -1.37173,6.58007 -6.97245,-0.420826 0.168095))",  
                      "MULTIPOLYGON(((4.75986e-16 -0.261125,0 0,0.723376 0.600649,1.22339 1.19163,1.7234 2.13422,1.7234 -2.0189,4.75986e-16 -0.261125)))");  
  
    // this doesn`t works  
    cout << "doesn`t work" << endl;  
    checkIntersection("POLYGON((-0.420825839394717959862646239344 0.168094877926996288941552393226,"  
                      "5.29161113734201116187705338234 5.76881481261494233336861725547,"  
                      "12.2925110557019436896553088445 -1.37173140830596795858298264648,"  
                      "6.58007407896521545609402892296 -6.97245134299391278176472042105,"  
                      "-0.420825839394717959862646239344 0.168094877926996288941552393226))",  
                      "MULTIPOLYGON(((4.75985680877701196159479187986e-16 -0.26112514220036736611874061964,"  
                      "0 0,"  
                      "0.72337592336357892097709054724 0.600648602980154100450249643472,"  
                      "1.223386680467822174023240222 1.19163154396839887638748223253,"  
                      "1.72339743757206620422550713556 2.1342216197599452875977021904,"  
                      "1.72339742847305732453833115869 -2.01889900623749607433410346857,"  
                      "4.75985680877701196159479187986e-16 -0.26112514220036736611874061964)))");  
  
    return 0;  
}  
  
```  
  
Output  
  
```  
works  
79.9999 3.46151  
0 1  
  
doesn`t work  
80 0  
1 1  
```  
  
Details: MSVC 2015, boost 1.68

---

## Comment 1

> Username: barendgehrels  
> Created at: 2019-11-23 13:15:24 UTC  
> Url: https://github.com/boostorg/geometry/issues/643#issuecomment-557797136  

Thanks for the report. I can reproduce it.  
Note, if you use the library with the define `BOOST_GEOMETRY_NO_ROBUSTNESS`, the issue is not present. This will be the default in (hopefully) 1.73 or a later version. Because I'm currently removing rescaling and taking care all set operations and buffer succeed without it.  
  
I added your case to the testsuite, to be sure it succeeds in future versions. Can I close the issue?

---

## Comment 2

> Username: Legotckoi  
> Created at: 2019-11-25 14:16:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/643#issuecomment-558174549  

I  checked `BOOST_GEOMETRY_NO_ROBUSTNESS` in our project. But unfortunately, we have now this issue exists in 1.68.  
We will update boost library in our project after release 1.73.  
 I think, you can close this issue. I made tolerance reducing for this case. We will reopen issue, if it will not fixed for us project.  
  
Thank you.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2019-11-26 18:48:31 UTC  
> Url: https://github.com/boostorg/geometry/issues/643#issuecomment-558767317  

All right, thanks, and yes please reopen it if it not fixed.
