# #1226 - union_ returns invalid polygon [Closed]

> Username: andershol  
> Created at: 2023-12-27 19:31:05 UTC  
> Updated at: 2024-11-16 09:18:00 UTC  
> Closed at: 2024-11-16 07:56:20 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226  

This code simply takes the union of two simple polygons. But with these specific polygons the result is a "double wound" polygon, i.e. the path goes around the rim twice. Fails with 1.83 and 1.84, but works with 1.73, but that might just be due to that version not running with BOOST_GEOMETRY_NO_ROBUSTNESS.  
```  
#include <boost/geometry.hpp>  
#include <boost/geometry/geometries/polygon.hpp>  
#include <boost/geometry/geometries/adapted/boost_tuple.hpp>  
BOOST_GEOMETRY_REGISTER_BOOST_TUPLE_CS(cs::cartesian)  
#include <iostream>  
  
typedef boost::tuple<double, double> point;  
typedef boost::geometry::model::polygon<point> polygon;  
  
int main() {  
    polygon a{{  
        {-0.90478776881879274807, .51756843862589896332},  
        {-0.91, .48},  
        {-1.2, .4},  
        {-1.4, 1.9},  
        {-0.90478776881879274807, .51756843862589896332},  
    }};  
    polygon b{{  
        {-0.91943242964602156508, .55292377741135378955},  
        {-0.90478776881879174887, .51756843862590162786},  
        {-0.91, .48},  
    }};  
    std::vector<polygon> output;  
    boost::geometry::union_(a, b, output);  
  
    std::cout << "a: " << boost::geometry::to_wkt(a) << "\n";  
    std::cout << "b: " << boost::geometry::to_wkt(b) << "\n";  
    for (const auto& pg : output)  
        std::cout << "output: " << boost::geometry::to_wkt(pg) << "\n";  
}  
```  
Can be compiled on windows with:  
````  
cl test.cpp /EHsc /O2 -std:c++17 /Fe:test.exe -I ..\boost_1.84\boost\include  
````  
And running the executable result in:  
```  
a: POLYGON((-0.904788 0.517568,-0.91 0.48,-1.2 0.4,-1.4 1.9,-0.904788 0.517568))  
b: POLYGON((-0.919432 0.552924,-0.904788 0.517568,-0.91 0.48,-0.919432 0.552924))  
output: POLYGON((-0.91 0.48,-1.2 0.4,-1.4 1.9,-0.904788 0.517568,-0.904788 0.517568,  
                 -0.91 0.48,-1.2 0.4,-1.4 1.9,-0.904788 0.517568,-0.91 0.48))  
```  
Note that polygon a has 5 points (including closing duplicate), polygon b has 4 points, but output has 10 and I don't think the output should ever have more points than the sum of the input polygons. And going around twice seems to me to be invalid.

---

## Comment 1

> Username: the-code-samurai-97  
> Created at: 2023-12-31 11:42:14 UTC  
> Updated at: 2023-12-31 11:42:57 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226#issuecomment-1872928252  

@andershol check this link   
https://compiler-explorer.com/z/rzE1oab7j  
you can check this python script , you can plot in your system and try it out  
https://compiler-explorer.com/z/Gc3P1oex1  
![Figure_1](https://github.com/boostorg/geometry/assets/77912970/9390b067-fdf5-4c48-a387-5a149c97664f)  
![Figure_2](https://github.com/boostorg/geometry/assets/77912970/ccd50743-4440-40c4-a38c-7a396a2f0ff9)  
  
 it is giving the correct points

---

## Comment 2

> Username: andershol  
> Created at: 2023-12-31 14:42:56 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226#issuecomment-1872965531  

> check this link https://compiler-explorer.com/z/rzE1oab7j  
  
I think you misunderstood, but perhaps I was imprecise. Except from making the output a bit more verbose (I tried to keep it short), your code seem identical to mine, and your result also seems to be identical. That is, there are 10 points in the output, but only 4 distinct points, so the quadrilateral is traversed twice.  
  
> you can check this python script , you can plot in your system and try it out https://compiler-explorer.com/z/Gc3P1oex1 it is giving the correct points  
  
I was not saying that the points in the output is placed incorrectly. I was saying that the path goes around the rim twice. You don't see that, when you just draw the polygon as you don't see that every line segment is painted twice.

---

## Comment 3

> Username: vissarion  
> Created at: 2024-01-10 09:59:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226#issuecomment-1884533180  

I can reproduce the issue with 1.84. This is a bug.   
  
On my platform (linux, gcc9) the issue is "resolved" when using `long double` instead of `double` yielding the following output:  
`POLYGON((-0.91 0.48,-1.2 0.4,-1.4 1.9,-0.90478776881880620085 0.51756843862593651808,-0.90478776881879174887 0.51756843862590162786,-0.91 0.48)) `  
  
>Note that polygon a has 5 points (including closing duplicate), polygon b has 4 points, but output has 10 and I don't think the output should ever have more points than the sum of the input polygons. And going around twice seems to me to be invalid.  
  
A side remark here, the output is indeed invalid but the statement about the number of vertices of the union is incorrect. Please consider the creation of new vertices in the union as the intersection of edges of the input polygons. Note, that the output computed with `long double` has also 5 vertices.

---

## Comment 4

> Username: barendgehrels  
> Created at: 2024-11-14 21:38:03 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226#issuecomment-2477460310  

@andershol Seems fixed now, and sorry for the delay, I hadn't focused on this issue earlier.

---

## Comment 5

> Username: barendgehrels  
> Created at: 2024-11-16 09:17:59 UTC  
> Url: https://github.com/boostorg/geometry/issues/1226#issuecomment-2480490244  

✅ merged. It might (probably) make it to 1.87
