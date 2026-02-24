# #515 - Invalid result of sym_difference(). [Open]

> Username: awulkiew  
> Created at: 2018-10-12 18:35:23 UTC  
> Updated at: 2019-01-25 00:05:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/515  

Detected for several cases of `sym_difference`, e.g. *difference.cpp* test case `intersect_holes_intersect_and_disjoint` used in the example below:  
  
    #include <boost/geometry.hpp>  
    #include <iostream>  
  
    int main()  
    {  
        namespace bg = boost::geometry;  
        typedef bg::model::point<double, 2, bg::cs::cartesian> point;  
        typedef bg::model::polygon<point> polygon;  
        typedef bg::model::multi_polygon<polygon> multi_polygon;  
  
        polygon g1, g2;  
        multi_polygon result;  
  
        bg::read_wkt("POLYGON((0 0,0 7,5 7,5 0,0 0),(2 2,3 2,3 3,2 3,2 2),(2 4,3 4,3 5,2 5,2 4))", g1);  
        bg::read_wkt("POLYGON((1 1,1 6,6 6,6 1,1 1),(2.5 2.5,3.5 2.5,3.5 3.5,2.5 3.5,2.5 2.5))", g2);  
  
        bg::sym_difference(g1, g2, result);  
  
        std::cout << std::setprecision(10);  
        std::cout << bg::wkt(result[3].outer()[4]) << std::endl;  
        std::cout << bg::wkt(result[4].outer()[2]) << std::endl;  
  
        std::cout << bg::relation(result[3], result[4]).str() << std::endl;  
  
        return 0;  
    }  
  
With develop branch and msvc-14.0 (Visual Studio 2015) x86 Debug mode the output is:  
  
    POINT(3 2.49999965)  
    POINT(3.00000035 2.5)  
    212101212  
  
![path885](https://user-images.githubusercontent.com/1226951/46887920-4df64400-ce5f-11e8-82ef-b04b5db4a4b7.png)  
  
which means that the interiors of polygons 3 and 4 in the result intersect (internally in `relate` `get_turns` correctly returns 2 crossing turns). Two equal points should be generated as a result of intersection of the holes of the input polygons. Two different points are generated instead.  
  
Side note: ~this is not~ previously this was not detected by `is_valid()` due to a bug in this algorithm incorrectly using `point_on_border()` to check if interiors intersect.

---

## Comment 1

> Username: awulkiew  
> Created at: 2018-11-15 02:14:51 UTC  
> Url: https://github.com/boostorg/geometry/issues/515#issuecomment-438890833  

@barendgehrels

---

## Comment 2

> Username: awulkiew  
> Created at: 2019-01-25 00:05:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/515#issuecomment-457405522  

`difference()` cases currently failing validity test are:  
- intersect_holes_intersect_and_disjoint  
- intersect_holes_intersect_and_touch  
- intersect_holes_intersect  
The problematic fragment is the same in all of them.
