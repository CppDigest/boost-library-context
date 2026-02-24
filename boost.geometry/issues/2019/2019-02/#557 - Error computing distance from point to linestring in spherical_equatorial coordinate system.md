# #557 - Error computing distance from point to linestring in spherical_equatorial coordinate system [Closed]

> Username: yhenon  
> Created at: 2019-02-19 19:56:07 UTC  
> Updated at: 2019-11-08 18:30:08 UTC  
> Closed at: 2019-11-06 16:21:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/557  

In spherical coordinates, computing a `distance` between a `linestring` and a `point` type does not return the same result as computing a `distance` between each segment of the `linestring` and the `point` and returning the minimum  
  
Sample example:  
  
```  
    namespace bg = boost::geometry;  
  
    // use spherical coordinates  
    typedef bg::model::point<double, 2, boost::geometry::cs::spherical_equatorial<boost::geometry::degree>> point_se;  
    typedef bg::model::linestring<point_se> linestring_se;  
  
    constexpr double EARTH_RADIUS = 6371008.8;  
  
    // define a point to calculate distance from  
    point_se pt{51.99999790563572, 43.71656981636763};  
  
    // define a linestring  
    linestring_se ls;  
  
    bg::append(ls, point_se(52.0000243071011, 43.716569742012496));  
    bg::append(ls, point_se(52.0000121532845, 43.71656942616241));  
    bg::append(ls, point_se(52.0, 43.7165690998572));  
    bg::append(ls, point_se(51.999987847203, 43.7165687638793));  
  
    double minDist = std::numeric_limits<double>::max();  
  
    // iterate over each segment in the linestring, and compute the distance from the segment to the point  
    // keeping only the minimum distance  
    for (size_t i = 0; i < ls.size() - 1; ++i) {  
        linestring_se l11;  
  
        bg::append(l11, ls[i]);  
        bg::append(l11, ls[i+1]);  
  
        double dist =  bg::distance(pt, l11);  
  
        if (dist_latlong < minDist) {  
            minDist = dist;  
        }  
    }  
    std::cout << EARTH_RADIUS * minDist << "," << EARTH_RADIUS * bg::distance(ls, pt) << std::endl;  
```  
  
Output:  
  
```  
0.0860478,0.186224  
```  
  
The expected outcome (I believe) is for the 2 methods of computation to return the same thing.  
  
Boost version: 1.69, 1.58  
Compiler: g++ (Ubuntu 5.4.0-6ubuntu1~16.04.10) 5.4.0 20160609  
System: Ubuntu, 64bit

---

## Comment 1

> Username: vissarion  
> Created at: 2019-10-17 16:54:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/557#issuecomment-543266239  

This should be fixed by https://github.com/boostorg/geometry/pull/628

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-11-06 16:21:21 UTC  
> Url: https://github.com/boostorg/geometry/issues/557#issuecomment-550385860  

Thanks! We have a fix and a test case merged. I assume this issue is fixed.

---

## Comment 3

> Username: yhenon  
> Created at: 2019-11-08 18:30:08 UTC  
> Url: https://github.com/boostorg/geometry/issues/557#issuecomment-551939756  

Thanks for fixing this :+1:
