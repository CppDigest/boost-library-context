# #884 - boost::geomotry::union_ result wrong in boost1.64.0 [Closed]

> Username: whu-lyh  
> Created at: 2021-07-05 13:56:10 UTC  
> Updated at: 2021-07-10 09:44:12 UTC  
> Closed at: 2021-07-10 06:29:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/884  

Hi everyone, recently i use boost geometry module to calculate the union of two bbox the bboxs are following:  
both bbox points are stored in counter-clock wise order  
bbox_a:  
```  
1,49559.42005, 7371.75  
2,50110.88005, 7371.75  
3,50110.88005, 7703.12  
4,49559.42005, 7703.12  
```  
bbox_b:  
```  
1,49559.42005, 7371.75  
2,50110.88005, 7371.75  
3,50110.88005, 7703.12  
4,49559.42005, 7703.12  
```  
i implement the boost::geometry::union_, but the union result is wrong and return the following result show as points:  
```  
1 , 50110.88005,7528.73281  
2 , 50035.300295,7528.73276  
3 , 50035.300295,7204.38347  
4 , 50564.832525,7204.38347  
5 , 50564.832525,7528.73276  
6 , 50110.88005,7528.73281  
```  
![image](https://user-images.githubusercontent.com/32642500/124482251-aaea7300-dddb-11eb-9201-3c6c10acb4ec.png)  
The union result is definitely wrong.  
besides the intersection result is slightly large than the union result which confuses me a lot too.  
Somebody who can help me? Thanks~

---

## Comment 1

> Username: awulkiew  
> Created at: 2021-07-05 16:20:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-874222835  

Hi! Could you please paste the code? Some minimal example showing what are you doing.

---

## Comment 2

> Username: whu-lyh  
> Created at: 2021-07-07 08:56:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-875422343  

Oops, recently i find the boost::geometry::difference return wrong results...  
here are some example codes:   
```  
typedef boost::geometry::model::polygon<boost::geometry::model::d2::point_xy<double>, false> polygon;  
polygon poly_extend, poly_equal_angle;  
std::vector<boost::geometry::model::d2::point_xy<double>> points;  
int vertex_size = pointsnumber;  
for(int i=0;i<vertex_size ;++i)  
     points.emplace_back(boost::geometry::model::d2::point_xy<double>(x, y));  
}  
boost::geometry::assign_points(poly_extend, points);			  
// another polygon points  
vertex_size = (int)pointsnumber.size();  
points.clear();  
for (int i = 0; i < vertex_size; ++i) {  
      points.emplace_back(boost::geometry::model::d2::point_xy<double>(x, y));  
}  
boost::geometry::assign_points(poly_equal_angle, points);  
std::deque<polygon> output;  
boost::geometry::difference(poly_equal_angle, poly_extend, output);  
// the union operation  
//boost::geometry::union_(poly_equal_angle, poly_extend, output);  
```  
some codes are not available, my apologies.

---

## Comment 3

> Username: whu-lyh  
> Created at: 2021-07-07 08:57:10 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-875423009  

@awulkiew

---

## Comment 4

> Username: awulkiew  
> Created at: 2021-07-07 21:23:54 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-875944001  

@whu-lyh I'm confused.  
So `bbox_a` is `poly_equal_angle` and `bbox_b` is `poly_extend`?  
Is this correct that the coordinates of `bbox_a` are exactly the same as `bbox_b`?  
Could you prepare full cpp file including creation of the geometries (with coordinates)?  
  
Btw, `bg::model::polygon<>` takes initializer list so you can create it like that:  
```  
polygon_t poly_cw{{{0.0, 0.0}, {0.0, 5.0}, {5.0, 5.0}, {5.0, 0.0}, {0.0, 0.0}},  
                  {{1.0, 1.0}, {4.0, 1.0}, {4.0, 4.0}, {1.0, 4.0}, {1.0, 1.0}}};  
```

---

## Comment 5

> Username: barendgehrels  
> Created at: 2021-07-08 16:24:02 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-876575843  

Are you sure you are testing 1.64? Can you test it with the latest version too?

---

## Comment 6

> Username: whu-lyh  
> Created at: 2021-07-09 12:37:43 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877156107  

> Are you sure you are testing 1.64? Can you test it with the latest version too?  
  
Sorry， due to the project environment, i have to do this on 1.64.0 version.

---

## Comment 7

> Username: whu-lyh  
> Created at: 2021-07-09 12:54:38 UTC  
> Updated at: 2021-07-09 12:54:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877165555  

@awulkiew @barendgehrels   
yes bbox_a is is poly_equal_angle and bbox_b is poly_extend  
and the polygon 's coordinate is absolutely corrected.  
here is a example cpp file for you all to test, thank you. i have debug this in a long time. BTW, i am drained...  
[boost_test.txt](https://github.com/boostorg/geometry/files/6791427/boost_test.txt)

---

## Comment 8

> Username: awulkiew  
> Created at: 2021-07-09 13:39:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877196594  

@whu-lyh I'll first write what I did and then I'll write my conclusion.  
  
First of all you defined polygon type which is CCW and Closed but you only pass 4 points, not closing it which makes it invalid. You can check that by calling `bg::is_valid(poly_a)`. This doesn't change the result in this case which I show below. But in general this is an error and you may get wrong results if your data doesn't match the type. You could use `boost::geometry::correct()` to close and reverse polygon rings if necessary, you could define polygon as `bg::model::polygon<point_t, false, false>` or you could add 5th point at the end that is equal to the first point.  
  
I modified your test by replacing `deque` with `multi_polygon` to display it with `bg::dsv` without manually writing a loop. I used `dsv` and not `wkt` because `wkt` adds closing point due to the WKT standard requirement even if this point is not in the data.  
  
I tested with msvc-14.1 and boost-1.64.0  
  
Here is my test:  
```  
#include <iostream>  
  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/geometries.hpp>  
  
int main()  
{  
    namespace bg = boost::geometry;  
    using point_t = bg::model::point<double, 2, bg::cs::cartesian>;  
    using polygon_t = bg::model::polygon<point_t, false>;  
  
    polygon_t poly_a, poly_b;  
    std::vector<point_t> points;  
    points.emplace_back(point_t(49559.42005, 7371.75));  
    points.emplace_back(point_t(50110.88005, 7371.75));  
    points.emplace_back(point_t(50110.88005, 7703.12));  
    points.emplace_back(point_t(49559.42005, 7703.12));  
    bg::assign_points(poly_a, points);  
    points.clear();  
    points.emplace_back(point_t(49559.42005, 7371.75));  
    points.emplace_back(point_t(50110.88005, 7371.75));  
    points.emplace_back(point_t(50110.88005, 7703.12));  
    points.emplace_back(point_t(49559.42005, 7703.12));  
    bg::assign_points(poly_b, points);  
      
    std::cout << "Polygons before correct():" << std::endl;  
    std::cout << bg::dsv(poly_a) << std::endl;  
    std::cout << bg::dsv(poly_b) << std::endl;  
  
    bg::model::multi_polygon<polygon_t> output_d, output_u;  
    bg::difference(poly_b, poly_a, output_d);  
    bg::union_(poly_b, poly_a, output_u);  
  
    std::cout << "Difference:" << std::endl;  
    std::cout << bg::dsv(output_d) << std::endl;  
  
    std::cout << "Union:" << std::endl;  
    std::cout << bg::dsv(output_u) << std::endl;  
  
    bg::correct(poly_a);  
    bg::correct(poly_b);  
  
    std::cout << "Polygons after correct():" << std::endl;  
    std::cout << bg::dsv(poly_a) << std::endl;  
    std::cout << bg::dsv(poly_b) << std::endl;  
          
    bg::clear(output_d);  
    bg::clear(output_u);  
    bg::difference(poly_b, poly_a, output_d);  
    bg::union_(poly_b, poly_a, output_u);  
  
    std::cout << "Difference:" << std::endl;  
    std::cout << bg::dsv(output_d) << std::endl;  
  
    std::cout << "Union:" << std::endl;  
    std::cout << bg::dsv(output_u) << std::endl;  
}  
```  
  
Here is the result:  
```  
Polygons before correct():  
(((49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12), (49559.4, 7703.12)))  
(((49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12), (49559.4, 7703.12)))  
Difference:  
()  
Union:  
((((50110.9, 7703.12), (49559.4, 7703.12), (49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12))))  
Polygons after correct():  
(((49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12), (49559.4, 7703.12), (49559.4, 7371.75)))  
(((49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12), (49559.4, 7703.12), (49559.4, 7371.75)))  
Difference:  
()  
Union:  
((((49559.4, 7703.12), (49559.4, 7371.75), (50110.9, 7371.75), (50110.9, 7703.12), (49559.4, 7703.12))))  
```  
  
Conclusion:  
The outputs are correct.  
Starting point is different but that's ok.  
  
Are your results the same?  
If not:  
Could you run this program and share the results?  
What compiler (and version) are you using?

---

## Comment 9

> Username: whu-lyh  
> Created at: 2021-07-10 02:54:22 UTC  
> Updated at: 2021-07-10 02:57:17 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877545081  

Oops, i believe i find the reason that the polygon in boost::geometry should be a closed ring to get the correct boolean operation result!!! Just follow your instruction, i get the correct result by calling `bg::correct` after pushing the point into polygon object making sure the ending point is exactly the start point.  
Thank you guys  @awulkiew @barendgehrels.

---

## Comment 10

> Username: whu-lyh  
> Created at: 2021-07-10 02:56:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877545319  

i will close this issue in a few hours waiting your replay, Thank you!!

---

## Comment 11

> Username: awulkiew  
> Created at: 2021-07-10 09:42:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/884#issuecomment-877607313  

Ok, thanks for the info. :)
